---
title: Ressourcennutzungs-APIs für Mandanten | Microsoft-Dokumentation
description: Referenz für die Ressourcennutzungs-APIs, die Azure Stack-Nutzungsinformationen abrufen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 85bb518335c473a70ff97473d1b8b61654372cb8
ms.sourcegitcommit: 95f30e32e5441599790d39542ff02ba90e70f9d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71070097"
---
# <a name="tenant-resource-usage-api"></a>Ressourcennutzungs-API für Mandanten

Ein Mandant kann die Mandanten-APIs verwenden, um seine eigenen Ressourcennutzungsdaten anzuzeigen. Diese APIs sind mit den Azure-Nutzungs-APIs konsistent.

Sie können das PowerShell-Cmdlet [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) von Windows verwenden, um Nutzungsdaten wie in Azure abzurufen.

## <a name="api-call"></a>API-Aufruf

### <a name="request"></a>Anforderung

Die Anforderung ruft detaillierte Nutzungsinformationen für das angeforderte Abonnement und den angeforderten Zeitraum ab. Es gibt keinen Anforderungstext.

| **Methode** | **Anforderungs-URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="parameters"></a>Parameter

| **Parameter** | **Beschreibung** |
| --- | --- |
| Armendpoint |Azure Resource Management-Endpunkt Ihrer Azure Stack-Umgebung. Nach Azure Stack-Konvention folgt der Name des Azure Resource Manager-Endpunkts dem Format `https://management.{domain-name}`. Wenn der Domänenname für das Development Kit beispielsweise „local.azurestack.external“ lautet, ist der Resource Manager-Endpunkt `https://management.local.azurestack.external`. |
| subId |Abonnement-ID des Benutzers, von dem der Aufruf ausgeht Sie können diese API nur verwenden, um die Nutzung eines einzelnen Abonnements abzufragen. Anbieter können die Ressourcennutzungs-API für Anbieter verwenden, um die Nutzung aller Mandanten abzufragen. |
| reportedStartTime |Die Startzeit der Abfrage. Der Wert für *DateTime* sollte in UTC angegeben werden und in vollen Stunden, z. B. 13:00. Legen Sie den Wert für eine tägliche Aggregation auf Mitternacht (UTC) fest. Das Format ist ISO 8601 mit Escapezeichen, z. B. **2015-06-16T18%3a53%3a11%2b00%3a00Z**, wobei ein Semikolon zu „%3a“ und ein Pluszeichen zu „%2b“ wird, um auch für URIs geeignet zu sein. |
| reportedEndTime |Die Endzeit der Abfrage. Für diesen Parameter gelten die gleichen Einschränkungen wie für **reportedStartTime**. Der Wert für **gemeldeteEndzeit** darf nicht in der Zukunft liegen. |
| aggregationGranularity |Optionaler Parameter, der zwei mögliche diskrete Werte hat: **täglich** und **stündlich**. Wie die Namen schon sagen, gibt der eine Wert Daten in Abständen von einem Tag zurück und der andere in Abständen von einer Stunde. Der Standard ist die Option **täglich**. |
| api-version |Die Version des Protokolls, dass für diese Anforderung verwendet wird. Sie müssen **2015-06-01-preview** verwenden. |
| continuationToken |Das Token, das durch den letzten Aufruf des Anbieters der Nutzungs-API abgerufen wurde. Dieses Token ist erforderlich, wenn eine Antwort länger als 1.000 Zeilen ist. Es fungiert als Lesezeichen für den Status. Falls nicht vorhanden, werden die Daten auf Grundlage der übergebenen Granularität vom Anfang des Tages oder der Stunde abgerufen. |

### <a name="response"></a>response

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

...
```

### <a name="response-details"></a>Genaue Angaben zu Antwort

| **Parameter** | **Beschreibung** |
| --- | --- |
| id |Eindeutige ID des Nutzungsaggregats. |
| name |Name des Nutzungsaggregats. |
| type |Ressourcendefinition. |
| subscriptionId |Bezeichner des Abonnements des Azure-Benutzers. |
| usageStartTime |Startzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört. |
| usageEndTime |Endzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört. |
| instanceData |Schlüssel-Wert-Paare der genaueren Angaben zu der Instanz (in neuem Format):<br>  *resourceUri*: Vollqualifizierte Ressourcen-ID, die die Ressourcengruppen und den Instanznamen enthält. <br>  *location*: Region, in der der Dienst ausgeführt wurde. <br>  *tags*: Ressourcentags, die vom Benutzer angegeben werden. <br>  *additionalInfo*: Weitere Angaben zur genutzten Ressource, z. B. die Betriebssystemversion oder der Imagetyp. |
| quantity |Menge der Ressourcennutzung, die in diesem Zeitraum aufgetreten ist. |
| meterId |Eindeutige ID der Verbrauchseinheit: Eine eindeutige ID für die verwendete Ressource (auch als **ResourceID** bezeichnet). |

## <a name="next-steps"></a>Nächste Schritte

- [Ressourcennutzungs-API für Anbieter](azure-stack-provider-resource-api.md)
- [Häufig gestellte Fragen zur Nutzung](azure-stack-usage-related-faq.md)
