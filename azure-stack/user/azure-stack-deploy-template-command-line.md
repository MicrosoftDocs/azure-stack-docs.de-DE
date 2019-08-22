---
title: Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit der plattformübergreifenden Azure-Befehlszeilenschnittstelle (Command Line Interface, CLI) Vorlagen in Azure Stack bereitstellen.
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
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: da17e80c802e210d53effbad8f264b1a4019e6e0
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991849"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) zum Bereitstellen von Azure Resource Manager-Vorlagen in Azure Stack verwenden. Azure Resource Manager-Vorlagen stellen Ressourcen für Ihre App in einem einzigen, koordinierten Vorgang bereit.

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

Informieren Sie sich über das [Bereitstellen von Vorlagen mit Azure PowerShell](azure-stack-deploy-template-powershell.md).
