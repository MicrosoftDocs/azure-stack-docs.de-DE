---
title: Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit der plattformübergreifenden Azure-Befehlszeilenschnittstelle (Command Line Interface, CLI) Vorlagen in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 7b3daaefd8fa7e7bce9c6d5708e664911fc906fe
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304085"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) zum Bereitstellen von Azure Resource Manager-Vorlagen in Azure Stack verwenden. Azure Resource Manager-Vorlagen stellen Ressourcen für Ihre App in einer einzigen, koordinierten Aktion bereit und richten sie ein.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

1. Suchen Sie im Repository [AzureStack-QuickStart-Templates repo](https://aka.ms/AzureStackGitHub) nach der Vorlage **101-create-storage-account**. Speichern Sie die Vorlage (`azuredeploy.json`) und die Parameterdateien `(azuredeploy.parameters.json`) an einem Speicherort auf dem lokalen Laufwerk, etwa `C:\templates\`.
2. Navigieren Sie zu dem Ordner, in den Sie die Dateien heruntergeladen haben. 
3. Verwenden Sie die Azure CLI, um Azure Stack zu [installieren und eine Verbindung herzustellen](azure-stack-version-profiles-azurecli2.md).
4. Aktualisieren Sie die Region und den Speicherort im folgenden Befehl. Geben Sie bei Verwendung des ASDK `local` als location-Parameter an. So stellen Sie die Vorlage bereit:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Dieser Befehl stellt die Vorlage für die Ressourcengruppe **testDeploy** in Ihrer Azure Stack-Instanz bereit.

## <a name="validate-template-deployment"></a>Überprüfen der Vorlagenbereitstellung

Wenn Sie die Ressourcengruppe und das Speicherkonto überprüfen möchten, führen Sie die folgenden CLI-Befehle aus:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Bereitstellen von Vorlagen mit Azure PowerShell](azure-stack-deploy-template-powershell.md).
