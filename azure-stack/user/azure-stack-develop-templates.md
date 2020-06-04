---
title: Entwickeln von Vorlagen für Azure Stack Hub
description: Erfahren Sie, wie Vorlagen für den Azure Resource Manager entwickelt werden, um Portabilität von Apps zwischen Azure und Azure Stack Hub zu erreichen.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: dd43abbf1194aa4aaa3ca1cc75a3e2ff6262bbbc
ms.sourcegitcommit: db3c9179916a36be78b43a8a47e1fd414aed3c2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84146749"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>Entwickeln von Vorlagen für Azure Stack Hub mit Azure Resource Manager

Bei der Entwicklung Ihrer App ist die Portabilität von Vorlagen zwischen Azure und Azure Stack Hub wichtig. Dieser Artikel enthält Überlegungen zum Entwickeln von [Azure Resource Manager-Vorlagen](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf). Mit diesen Vorlagen können Sie einen Prototyp Ihrer App erstellen und die Bereitstellung in Azure ohne Zugriff auf eine Azure Stack Hub-Umgebung testen.

## <a name="resource-provider-availability"></a>Verfügbarkeit des Ressourcenanbieters

Die Vorlage, die Sie bereitstellen möchten, darf nur Microsoft Azure-Dienste verwenden, die in Azure Stack Hub bereits verfügbar sind oder sich in der Vorschauphase befinden.

## <a name="public-namespaces"></a>Öffentliche Namespaces

Da Azure Stack Hub in Ihrem Rechenzentrum gehostet wird, weist die Lösung andere Dienstendpunkt-Namespaces als die öffentliche Azure-Cloud auf. Daher verursachen hartcodierte öffentliche Endpunkte in Azure Resource Manager-Vorlagen Fehler, wenn Sie versuchen, sie in Azure Stack Hub bereitzustellen. Sie können Dienstendpunkte mithilfe der Funktionen `reference` und `concatenate` dynamisch erstellen, um Werte während der Bereitstellung vom Ressourcenanbieter abzurufen. Anstatt beispielsweise `blob.core.windows.net` in Ihrer Vorlage hartzucodieren, rufen Sie [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) ab, um den Endpunkt *osDisk.URI* dynamisch festzulegen:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API-Versionsverwaltung

Die Versionen von Azure-Diensten können zwischen Azure und Azure Stack Hub variieren. Jede Ressource erfordert das Attribut **apiVersion**, das die gebotenen Funktionen definiert. Um API-Versionskompatibilität in Azure Stack Hub sicherzustellen, sind die folgenden API-Versionen für jeden Ressourcenanbieter gültig:

| Ressourcenanbieter | apiVersion |
| --- | --- |
| Compute |**2015-06-15** |
| Netzwerk |**2015-06-15**, **2015-05-01-preview** |
| Storage |**2016-01-01**, **2015-06-15**, **2015-05-01-preview** |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Funktionen von Azure-Ressourcen-Manager-Vorlagen

Azure Resource Manager-[Funktionen](/azure/azure-resource-manager/resource-group-template-functions) ermöglichen das Erstellen dynamischer Vorlagen. Beispielsweise können Sie Funktionen für Aufgaben wie die folgenden verwenden:

* Verketten oder Kürzen von Zeichenfolgen
* Verweisen auf Werte aus anderen Ressourcen
* Durchlaufen von Ressourcen zur Bereitstellung mehrerer Instanzen

Diese Funktionen sind in Azure Stack Hub nicht verfügbar:

* Skip
* Take

## <a name="resource-location"></a>Ressourcenspeicherort

Azure Resource Manager-Vorlagen nutzen ein `location`-Attribut zum Platzieren von Ressourcen während der Bereitstellung. In Azure verweisen Standorte auf eine Region wie „USA, Westen“ oder „Südamerika“. Die Speicherorte sind in Azure Stack Hub anders, weil Azure Stack Hub sich in Ihrem Rechenzentrum befindet. Um sicherzustellen, dass Vorlagen zwischen Azure und Azure Stack Hub übertragbar sind, müssen Sie auf den Speicherort der Ressourcengruppe verweisen, während Sie einzelne Ressourcen bereitstellen. Nutzen Sie hierfür `[resourceGroup().Location]`, um sicherzustellen, dass alle Ressourcen den Speicherort der Ressourcengruppe erben. Der folgende Code ist ein Beispiel für die Verwendung dieser Funktion beim Bereitstellen eines Speicherkontos:

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
* [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
* [Bereitstellen von Vorlagen mithilfe von Visual Studio](azure-stack-deploy-template-visual-studio.md)
