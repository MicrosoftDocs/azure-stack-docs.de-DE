---
title: Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack Hub
description: Hier erfahren Sie, wie Sie mit der plattformübergreifenden Azure-Befehlszeilenschnittstelle (Command Line Interface, CLI) Vorlagen in Azure Stack Hub bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 38327d2e23fd74c1a5e0a197ccc2a32b65b3fa7a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247811"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Bereitstellen einer Vorlage mithilfe der Befehlszeile in Azure Stack Hub

Sie können die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) zum Bereitstellen von Azure Resource Manager-Vorlagen in Azure Stack Hub verwenden. Azure Resource Manager-Vorlagen stellen Ressourcen für Ihre App in einer einzigen, koordinierten Aktion bereit und richten sie ein.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

1. Suchen Sie im Repository [AzureStack-QuickStart-Templates repo](https://aka.ms/AzureStackGitHub) nach der Vorlage **101-create-storage-account**. Speichern Sie die Vorlage (`azuredeploy.json`) und die Parameterdateien `(azuredeploy.parameters.json`) an einem Speicherort auf dem lokalen Laufwerk, etwa `C:\templates\`.
2. Navigieren Sie zu dem Ordner, in den Sie die Dateien heruntergeladen haben. 
3. Verwenden Sie die Azure CLI, um Azure Stack Hub zu [installieren und eine Verbindung herzustellen](azure-stack-version-profiles-azurecli2.md).
4. Aktualisieren Sie die Region und den Speicherort im folgenden Befehl. Geben Sie bei Verwendung des ASDK `local` als location-Parameter an. So stellen Sie die Vorlage bereit:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Dieser Befehl stellt die Vorlage für die Ressourcengruppe **testDeploy** in Ihrer Azure Stack Hub-Instanz bereit.

## <a name="validate-template-deployment"></a>Überprüfen der Vorlagenbereitstellung

Wenn Sie die Ressourcengruppe und das Speicherkonto überprüfen möchten, führen Sie die folgenden CLI-Befehle aus:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Bereitstellen von Vorlagen mit Azure PowerShell](azure-stack-deploy-template-powershell.md).
