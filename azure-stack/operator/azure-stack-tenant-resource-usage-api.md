---
title: Ressourcennutzungs-API für Mandanten | Microsoft-Dokumentation
description: Referenz für die Ressourcennutzungs-API, die Azure Stack-Nutzungsinformationen abruft
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2378bef2457b29271f048ec213ccf4b79454dd95
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782206"
---
# <a name="tenant-resource-usage-api"></a>Ressourcennutzungs-API für Mandanten

Ein Mandant kann die Mandanten-API verwenden, um seine eigenen Ressourcennutzungsdaten anzuzeigen. Diese API stimmt mit der Azure-Nutzungs-API überein, die sich aktuell in der öffentlichen Vorschau befindet.

Sie können das PowerShell-Cmdlet **Get-UsageAggregates** von Windows verwenden, um Nutzungsdaten wie in Azure abzurufen.

## <a name="api-call"></a>API-Aufruf
### <a name="request"></a>Anforderung
Die Anforderung ruft detaillierte Nutzungsinformationen für das angeforderte Abonnement und den angeforderten Zeitraum ab. Es gibt keinen Anforderungstext.

| **Methode** | **Anforderungs-URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumente
| **Argument** | **Beschreibung** |
| --- | --- |
| *armendpoint* |Azure Resource Management-Endpunkt Ihrer Azure Stack-Umgebung. Nach Azure Stack-Konvention folgt der Name des Azure Resource Manager-Endpunkts dem Format `https://management.{domain-name}`. Wenn der Domänenname für das Development Kit beispielsweise „local.azurestack.external“ lautet, ist der Resource Manager-Endpunkt `https://management.local.azurestack.external`. |
| *aboId* |Abonnement-ID des Benutzers, von dem der Aufruf ausgeht Sie können diese API nur verwenden, um die Nutzung eines einzelnen Abonnements abzufragen. Anbieter können die Ressourcennutzungs-API für Anbieter verwenden, um die Nutzung aller Mandanten abzufragen. |
| *gemeldeteStartzeit* |Die Startzeit der Abfrage. Der Wert für *DatumUhrzeit* sollte in UTC angegeben werden und in vollen Stunden, z.B. 13:00. Legen Sie den Wert für eine tägliche Aggregation auf Mitternacht (UTC) fest. Das Format ist ISO 8601 *mit Escapezeichen*, z.B. 2015-06-16T18%3a53%3a11%2b00%3a00Z, wobei ein Semikolon zu „%3a“ und ein Pluszeichen zu „%2b“ wird, um auch für URIs geeignet zu sein. |
| *gemeldeteEndzeit* |Die Endzeit der Abfrage. Für dieses Argument gelten die gleichen Einschränkungen wie für die *gemeldeteStartzeit*. Der Wert für *gemeldeteEndzeit* darf nicht in der Zukunft liegen. |
| *aggregationsgranularität* |Optionaler Parameter, der zwei mögliche diskrete Werte hat: täglich und stündlich. Wie die Namen schon sagen, gibt der eine Wert Daten in Abständen von einem Tag zurück und der andere in Abständen von einer Stunde. Standardmäßig ist die tägliche Option aktiviert. |
| *api-version* |Die Version des Protokolls, dass für diese Anforderung verwendet wird. Sie müssen 2015-06-01-preview verwenden. |
| *fortsetzungsToken* |Das Token, das durch den letzten Aufruf des Anbieters der Nutzungs-API abgerufen wurde. Dieses Token ist erforderlich, wenn eine Antwort länger als 1000 Zeilen ist und als Textmarke für den Fortschritt fungiert. Falls nicht vorhanden, werden die Daten auf Grundlage der übergebenen Granularität vom Anfang des Tages oder der Stunde abgerufen. |

### <a name="response"></a>response
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

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

. . .
```

### <a name="response-details"></a>Genaue Angaben zu Antwort
| **Argument** | **Beschreibung** |
| --- | --- |
| *id* |Eindeutige ID des Nutzungsaggregats |
| *name* |Name des Nutzungsaggregats |
| *type* |Ressourcendefinition |
| *subscriptionId* |Bezeichner des Abonnements des Azure-Benutzers |
| *usageStartTime* |Startzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört |
| *usageEndTime* |Endzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört |
| *instanceData* |Schlüssel-Wert-Paare der genaueren Angaben zu der Instanz (in neuem Format):<br>  *resourceUri*: Vollqualifizierte Ressourcen-ID, die die Ressourcengruppen und den Instanznamen enthält <br>  *location*: Region, in der der Dienst ausgeführt wurde <br>  *tags*: Ressourcentags, die vom Benutzer angegeben werden <br>  *additionalInfo*: Weitere Angaben zur genutzten Ressource, z.B. die Betriebssystemversion oder der Imagetyp |
| *quantity* |Menge der Ressourcennutzung, die in diesem Zeitraum aufgetreten ist |
| *meterId* |Eindeutige ID der Verbrauchseinheit: Eine eindeutige ID für die verwendete Ressource (auch als *ResourceID* bezeichnet) |


## <a name="next-steps"></a>Nächste Schritte
[Ressourcennutzungs-API für Anbieter](azure-stack-provider-resource-api.md)

[Häufig gestellte Fragen zur Nutzung](azure-stack-usage-related-faq.md)

