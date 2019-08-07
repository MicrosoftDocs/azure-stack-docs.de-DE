---
title: Entwickeln von Vorlagen für Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Vorlagen für den Azure Resource Manager entwickelt werden, um Portabilität von Apps zwischen Azure und Azure Stack zu erreichen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: bedc4c3971c5d4a177f4d8ac804878babebaa9b6
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658643"
---
# <a name="develop-templates-for-azure-stack-with-azure-resource-manager"></a>Entwickeln von Vorlagen für Azure Stack mit Azure Resource Manager

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Bei der Entwicklung Ihrer App ist die Portabilität von Vorlagen zwischen Azure und Azure Stack wichtig. Dieser Artikel enthält Überlegungen zum Entwickeln von [Azure Resource Manager-Vorlagen](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf). Mit diesen Vorlagen können Sie einen Prototyp Ihrer App erstellen und die Bereitstellung in Azure ohne Zugriff auf eine Azure Stack-Umgebung testen.

## <a name="resource-provider-availability"></a>Verfügbarkeit des Ressourcenanbieters

Die Vorlage, die Sie bereitstellen möchten, darf nur Microsoft Azure-Dienste verwenden, die in Azure Stack bereits verfügbar sind oder sich in der Vorschauphase befinden.

## <a name="public-namespaces"></a>Öffentliche Namespaces

Da Azure Stack in Ihrem Rechenzentrum gehostet wird, weist die Lösung andere Dienstendpunkt-Namespaces als die öffentliche Azure-Cloud auf. Daher verursachen hartcodierte öffentliche Endpunkte in Azure Resource Manager-Vorlagen Fehler, wenn Sie versuchen, sie in Azure Stack bereitzustellen. Sie können Dienstendpunkte mithilfe der Funktionen `reference` und `concatenate` dynamisch erstellen, um Werte während der Bereitstellung vom Ressourcenanbieter abzurufen. Anstatt beispielsweise `blob.core.windows.net` in Ihrer Vorlage hartzucodieren, rufen Sie [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) ab, um den Endpunkt *osDisk.URI* dynamisch festzulegen:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API-Versionsverwaltung

Azure Service-Versionen können zwischen Azure und Azure Stack unterschiedlich sein. Jede Ressource erfordert das Attribut **apiVersion**, das die gebotenen Funktionen definiert. Um API-Versionskompatibilität in Azure Stack sicherzustellen, sind die folgenden API-Versionen für jeden Ressourcenanbieter gültig:

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

Diese Funktionen sind in Azure Stack nicht verfügbar:

* Überspringen
* Take

## <a name="resource-location"></a>Speicherort von Ressourcen

Azure Resource Manager-Vorlagen nutzen ein `location`-Attribut zum Platzieren von Ressourcen während der Bereitstellung. In Azure verweisen Standorte auf eine Region wie „USA, Westen“ oder „Südamerika“. In Azure Stack sind Speicherorte anders, weil Azure Stack sich in Ihrem Rechenzentrum befindet. Um sicherzustellen, dass Vorlagen zwischen Azure und Azure Stack übertragbar sind, müssen Sie auf den Speicherort der Ressourcengruppe verweisen, während Sie einzelne Ressourcen bereitstellen. Nutzen Sie hierfür `[resourceGroup().Location]`, um sicherzustellen, dass alle Ressourcen den Speicherort der Ressourcengruppe erben. Der folgende Code ist ein Beispiel für die Verwendung dieser Funktion beim Bereitstellen eines Speicherkontos:

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
