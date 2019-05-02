---
title: Bereitstellen von Vorlagen mithilfe der Befehlszeile in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit der plattformübergreifenden Befehlszeilenschnittstelle (Command Line Interface, CLI) Vorlagen in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: fcf84ee394c917e896bc50d5d6a97f42191451e9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985595"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Bereitstellen von Vorlagen in Azure Stack mithilfe der Befehlszeile

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Verwenden Sie die Befehlszeile zum Bereitstellen von Azure Resource Manager-Vorlagen in der Azure Stack Development Kit-Umgebung. Azure Resource Manager-Vorlagen stellen alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereit.

## <a name="before-you-begin"></a>Voraussetzungen

- Verwenden Sie die Azure CLI, um Azure Stack zu [installieren und eine Verbindung herzustellen](azure-stack-version-profiles-azurecli2.md).
- Laden Sie die Dateien *azuredeploy.json* und *azuredeploy.parameters.json* aus der [Beispielvorlage zum Erstellen von Speicherkonten](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account) herunter.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Navigieren Sie zu dem Ordner, in den diese Dateien heruntergeladen wurden, und führen Sie den folgenden Befehl zum Bereitstellen der Vorlage aus:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Dieser Befehl stellt die Vorlage für die Ressourcengruppe **cliRG** am Standardspeicherort für Azure Stack POC bereit.

## <a name="validate-template-deployment"></a>Überprüfen der Vorlagenbereitstellung

Um diese Ressourcengruppe und das Speicherkonto anzuzeigen, verwenden Sie die folgenden CLI-Befehle:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Bereitstellen von Vorlagen finden Sie unter:

[Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
