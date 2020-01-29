---
title: Durchführen einer Bereitstellung mit Visual Studio Code für Azure Stack Hub | Microsoft-Dokumentation
description: Als Benutzer möchte ich eine Azure Resource Manager-Vorlage in Visual Studio Code erstellen und das Bereitstellungsschema verwenden, um eine Vorlage zu erhalten, die mit meiner Version von Azure Stack Hub kompatibel ist.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 12f7b1f0dad8e1d7b452fcfe0e1785fcd851b04b
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536452"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack-hub"></a>Durchführen einer Bereitstellung mit Visual Studio Code für Azure Stack Hub

Sie können Visual Studio Code und die Erweiterung „Azure Resource Manager-Tools“ verwenden, um Azure Resource Manager-Vorlagen zu erstellen und zu bearbeiten, die für Ihre Version von Azure Stack Hub funktionieren. Sie können Resource Manager-Vorlagen in Visual Studio Code auch ohne die Erweiterung erstellen, aber die Erweiterung verfügt über Optionen für die automatische Vervollständigung, die Ihnen die Entwicklung von Vorlagen vereinfachen. Darüber hinaus können Sie ein Bereitstellungsschema angeben, mit dessen Hilfe Sie sich mit den Ressourcen vertraut machen können, die in Azure Stack Hub verfügbar sind.

In diesem Artikel stellen Sie einen virtuellen Windows-Computer bereit.

## <a name="concepts-for-azure-stack-hub-resource-manager"></a>Konzepte für Azure Stack Hub Resource Manager

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub: Resource Manager

Informationen zu den Konzepten für die Bereitstellung und Verwaltung Ihrer Azure-Lösungen in Azure Stack Hub finden Sie unter [Verwenden von Azure Resource Manager-Vorlagen in Azure Stack Hub](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>API-Profile
Informationen zu den Konzepten für die Koordination von Ressourcenanbietern in Azure Stack Hub finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack Hub](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>Bereitstellungsschema

Das Azure Stack Hub-Bereitstellungsschema unterstützt Hybridprofile mit Azure Resource Manager-Vorlagen in Visual Studio Code. Sie können eine Zeile in der JSON-Vorlage ändern, um auf das Schema zu verweisen, und anschließend können Sie IntelliSense verwenden, um die mit Azure kompatible Ressource zu überprüfen. Überprüfen Sie mit dem Schema die Ressourcenanbieter, Typen und API-Versionen, die in Ihrer Version von Azure Stack Hub unterstützt werden. Für das Schema wird das API-Profil benötigt, um die spezifischen Versionen der API-Endpunkte in den Ressourcenanbietern abzurufen, die für Ihre Version von Azure Stack Hub unterstützt werden. Sie können die Wortvervollständigung für „type“ und „apiVersion“ verwenden und sind dann auf die „apiVersion“ und Ressourcentypen beschränkt, die für das API-Profil verfügbar sind.

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio Code](https://code.visualstudio.com/)
- Zugriff auf Azure Stack Hub
- [Installation von Azure Stack Hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json) auf einem Computer, mit dem auf die Verwaltungsendpunkte zugegriffen werden kann

## <a name="install-resource-manager-tools-extension"></a>Installieren der Azure Resource Manager-Tools-Erweiterung

Führen Sie diese Schritte aus, um die Erweiterung „Resource Manager-Tools“ zu installieren:

1. Öffnen Sie Visual Studio Code.
2. Drücken Sie STRG+UMSCHALT+X, um den Bereich „Erweiterungen“ zu öffnen.
3. Suchen Sie nach `Azure Resource Manager Tools`, und wählen Sie dann **Installieren** aus.
4. Wählen Sie die Option **Erneut laden**, um die Installation der Erweiterung abzuschließen.

## <a name="get-a-template"></a>Abrufen einer Vorlage

Anstatt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Vorlage auch über „AzureStack-QuickStart-Templates“ (https://github.com/Azure/AzureStack-QuickStart-Templates) öffnen. „AzureStack-QuickStart-Templates“ ist ein Repository für Resource Manager-Vorlagen, mit denen Ressourcen für Azure Stack Hub bereitgestellt werden. 

Die Vorlage in diesem Artikel hat den Namen `101-vm-windows-create`. Mit der Vorlage wird eine grundlegende Bereitstellung einer Windows-VM für Azure Stack Hub definiert.  Diese Vorlage stellt auch ein virtuelles Netzwerk (mit DNS), eine Netzwerksicherheitsgruppe und eine Netzwerkschnittstelle bereit.

1. Öffnen Sie Visual Studio Code, und navigieren Sie auf Ihrem Computer zu einem Arbeitsordner.
2. Öffnen Sie das Git Bash-Terminal in Visual Studio Code.
3. Führen Sie den folgenden Befehl aus, um das Azure Stack Hub-Schnellstartrepository abzurufen.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Öffnen Sie das Verzeichnis mit dem Repository.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. Wählen Sie **Öffnen** aus, um die Datei im Repository unter `/101-vm-windows-create/azuredeploy.json` zu öffnen.
6. Speichern Sie die Datei in Ihrem eigenen Arbeitsbereich. Falls Sie eine Verzweigung des Repositorys erstellt haben, können Sie vor Ort arbeiten.
7. Ändern Sie bei geöffneter Datei das Feld `$Schema` in `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`.
8. Sie können überprüfen, ob das Bereitstellungsschema funktioniert, indem Sie den Wert des Felds „apiProfile“ löschen.
    ```JSON  
    "apiProfile": ""
    ```
9. Platzieren Sie den Cursor zwischen den leeren Anführungszeichen, und drücken Sie STRG+LEERTASTE. Sie können im Bereitstellungsschema für Azure Stack Hub eine Auswahl aus den gültigen API-Profilen treffen. Sie können diesen Vorgang mit jedem Ressourcenanbieter in der Vorlage durchführen.

    ![Azure Stack Hub Resource Manager-Bereitstellungsschema](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Wenn Sie bereit sind, können Sie Ihre Vorlage mit PowerShell bereitstellen. Befolgen Sie die Anleitung unter [Bereitstellen mit PowerShell](azure-stack-deploy-template-powershell.md). Geben Sie den Speicherort der Vorlage im Skript an.
11. Navigieren Sie nach dem Bereitstellen Ihrer Windows-VM zum Azure Stack Hub-Portal, und suchen Sie nach der Ressourcengruppe. Falls Sie das Ergebnis dieser Übung aus Ihrer Azure Stack Hub-Instanz löschen möchten, müssen Sie die Ressourcengruppe löschen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich weiter über die [Azure Stack Hub Resource Manager-Vorlagen](azure-stack-arm-templates.md).  
- Informieren Sie sich über [API-Profile in Azure Stack Hub](azure-stack-version-profiles.md).