---
title: Ressourcennutzungs-API für Anbieter | Microsoft-Dokumentation
description: Referenz für die Ressourcennutzungs-API, die Azure Stack-Nutzungsinformationen abruft
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
ms.date: 03/11/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: 8b9a8f403fbc4ca80bb7ce179547d5b7f8954525
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268279"
---
# <a name="provider-resource-usage-api"></a>Ressourcennutzungs-API für Anbieter

Der Begriff *Anbieter* bezieht sich auf den Dienstadministrator und alle delegierten Anbieter. Azure Stack-Operators und delegierte Anbieter können die Nutzungs-API für Anbieter verwenden, um einen Einblick in die Nutzung ihrer direkten Mandanten zu erhalten. Wie im Diagramm dargestellt, kann P0 beispielsweise die Anbieter-API aufrufen, um die Nutzungsinformationen zur direkten Nutzung von P1 und P2 abzurufen, und P1 kann die Nutzungsinformationen von P3 und P4 abrufen.

![Konzeptuelles Modell der Anbieterhierarchie](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API-Aufrufreferenz
### <a name="request"></a>Anforderung
Die Anforderung ruft detaillierte Nutzungsinformationen für das angeforderte Abonnement und den angeforderten Zeitraum ab. Es gibt keinen Anforderungstext.

Die Nutzungs-API ist eine Anbieter-API. Aus diesem Grund muss dem Aufrufer im Abonnement des Anbieters die Rolle des Besitzers, Mitwirkenden oder Lesers zugewiesen sein.

| **Methode** | **Anforderungs-URI** |
| --- | --- |
| GET |https://{ARM-Endpunkt}/subscriptions/{Abo-ID}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={gemeldeteStartzeit}&reportedEndTime={gemeldeteAbschlusszeit}&aggregationGranularity={Granularität}&subscriberId={Abo1.1}&api-version=2015-06-01-preview&continuationToken={Tokenwert} |

### <a name="arguments"></a>Argumente

| **Argument** | **Beschreibung** |
| --- | --- |
| *armendpunkt* |Azure Resource Management-Endpunkt Ihrer Azure Stack-Umgebung. Nach Azure Stack-Konvention folgt der Name des Azure Resource Manager-Endpunkts dem Format `https://adminmanagement.{domain-name}`. Wenn der Domänenname für das Development Kit beispielsweise *local.azurestack.external* lautet, ist der Resource Manager-Endpunkt `https://adminmanagement.local.azurestack.external`. |
| *aboId* |Abonnement-ID des Benutzers, von dem der Aufruf ausgeht. |
| *gemeldeteStartzeit* |Die Startzeit der Abfrage. Der Wert für *DateTime* sollte in koordinierter Weltzeit (UTC) und vollen Stunden angegeben werden, z.B. 13:00. Legen Sie den Wert für eine tägliche Aggregation auf Mitternacht (UTC) fest. Das Format ist gemäß ISO 8601 *mit Escapezeichen versehen*. Beispiel: *2015-06-16T18%3a53%3a11%2b00%3a00Z*, wobei der Doppelpunkt zu *%3a* und das Pluszeichen zu *%2b* wird, um auch für URIs geeignet zu sein. |
| *gemeldeteEndzeit* |Die Endzeit der Abfrage. Für dieses Argument gelten die gleichen Einschränkungen wie für die *gemeldeteStartzeit*. Der Wert für *reportedEndTime* darf nicht in der Zukunft oder im aktuellen Datum liegen. Wenn dies der Fall ist, wird als Ergebnis „processing not complete“ (nicht abgeschlossen) angezeigt. |
| *aggregationsgranularität* |Optionaler Parameter, der zwei mögliche diskrete Werte hat: täglich und stündlich. Wie die Namen schon sagen, gibt der eine Wert Daten in Abständen von einem Tag zurück und der andere in Abständen von einer Stunde. Standardmäßig ist die tägliche Option aktiviert. |
| *abonnentenId* |Abonnement-ID Um gefilterte Daten zu erhalten, ist die Abonnement-ID eines direkten Mandanten des Anbieters erforderlich. Wenn kein Parameter für die Abonnement-ID angegeben ist, gibt der Aufruf Nutzungsdaten für alle direkten Mandanten des Anbieters zurück. |
| *api-version* |Die Version des Protokolls, das für diese Anforderung verwendet wird. Dieser Wert ist auf *2015-06-01-preview* festgelegt. |
| *fortsetzungsToken* |Das Token, das durch den letzten Aufruf des Anbieters der Nutzungs-API abgerufen wurde. Dieses Token ist erforderlich, wenn eine Antwort länger als 1000 Zeilen ist und als Textmarke für den Fortschritt fungiert. Falls das Token nicht vorhanden ist, werden die Daten auf Grundlage der übergebenen Granularität vom Anfang des Tages oder der Stunde abgerufen. |

### <a name="response"></a>response
GET /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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
| *id* |Eindeutige ID des Nutzungsaggregats. |
| *name* |Name des Nutzungsaggregats. |
| *type* |Ressourcendefinition. |
| *subscriptionId* |Bezeichner des Abonnements des Azure Stack-Benutzers. |
| *usageStartTime* |Startzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört.|
| *usageEndTime* |Endzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört. |
| *instanceData* |Schlüssel-Wert-Paare der genaueren Angaben zu der Instanz (in neuem Format):<br> *resourceUri*: Vollqualifizierte Ressourcen-ID, die die Ressourcengruppen und den Instanznamen enthält. <br> *location*: Region, in der der Dienst ausgeführt wurde. <br> *tags*: Ressourcentags, die vom Benutzer angegeben werden. <br> *additionalInfo*: Weitere Angaben zur genutzten Ressource, z.B. die Betriebssystemversion oder der Imagetyp. |
| *quantity* |Menge der Ressourcennutzung, die in diesem Zeitraum aufgetreten ist. |
| *meterId* |Eindeutige ID der Verbrauchseinheit: Eine eindeutige ID für die verwendete Ressource (auch als *ResourceID* bezeichnet). |


## <a name="retrieve-usage-information"></a>Abrufen von Nutzungsinformationen

### <a name="powershell"></a>PowerShell

Um Nutzungsdaten zu generieren, benötigen Sie Ressourcen, die ausgeführt werden und das System aktiv verwenden, beispielsweise einen aktiven virtuellen Computer oder ein Speicherkonto mit Daten. Wenn Sie nicht sicher sind, ob Sie über Ressourcen verfügen, die im Azure Stack Marketplace ausgeführt werden, stellen Sie einen virtuellen Computer bereit, und überprüfen Sie das Überwachungsblatt für diesen Computer, um sicherzustellen, dass er ausgeführt wird. Verwenden Sie die folgenden PowerShell-Cmdlets, um die Nutzungsdaten anzuzeigen:

1. [Installieren Sie PowerShell für Azure Stack-](azure-stack-powershell-install.md)
2. Konfigurieren Sie die PowerShell-Umgebung des [Azure Stack-Benutzers](../user/azure-stack-powershell-configure-user.md) oder des [Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md). 
3. Um die Nutzungsdaten abzurufen, verwenden Sie das PowerShell-Cmdlet [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates):
   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

### <a name="rest-api"></a>REST-API

Sie können Nutzungsinformationen für gelöschte Abonnements sammeln, indem Sie den Microsoft.Commerce.Admin-Dienst aufrufen. 

**So geben Sie die gesamte Mandantennutzung für gelöschte Abonnements für aktive Benutzer zurück:**

| **Methode** | **Anforderungs-URI** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview |

**So geben Sie die Nutzung für gelöschte oder aktive Mandanten zurück:**

| **Methode** | **Anforderungs-URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview |


## <a name="next-steps"></a>Nächste Schritte
[Referenz zur Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md)

[Häufig gestellte Fragen zur Nutzung](azure-stack-usage-related-faq.md)
