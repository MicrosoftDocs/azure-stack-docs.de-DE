---
title: Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit der plattformübergreifenden Befehlszeilenschnittstelle (Command Line Interface, CLI) Vorlagen in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 92c9189f8144804f36e551ab89d8b4fc4c1f8598
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691366"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Verwenden Sie die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) zum Bereitstellen von Azure Resource Manager-Vorlagen in Azure Stack. Azure Resource Manager-Vorlagen stellen Ressourcen für Ihre App in einem einzigen, koordinierten Vorgang bereit.

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

Erfahren Sie mehr über das [Bereitstellen von Vorlagen mithilfe von PowerShell](azure-stack-deploy-template-powershell.md).
