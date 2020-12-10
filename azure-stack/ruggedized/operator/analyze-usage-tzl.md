---
title: Analysieren der Azure Stack-Nutzung und der Ressourcennutzungs-API | Microsoft-Dokumentation
description: Analysieren der Azure Stack-Nutzung und Referenz für die Ressourcennutzungs-API, die Azure Stack-Nutzungsinformationen abruft
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
ms.date: 07/29/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 07/29/2020
ms.openlocfilehash: f546c1ad783ded0aa686ebdcb7e08f1427bea91e
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941172"
---
# <a name="analyze-azure-stack-usage-with-local-usage-meters"></a>Analysieren der Azure Stack-Nutzung mit lokalen Verbrauchseinheiten

Informationen darüber, welche Abonnements welche Ressourcen nutzen, sind in einer lokalen Verwendungsdatenbank gespeichert. Administratoren können diese Daten abrufen, um zu analysieren, welche Benutzer Ressourcen verwenden.

## <a name="api-call-reference"></a>API-Aufrufreferenz

### <a name="request"></a>Anforderung

Die Anforderung ruft detaillierte Nutzungsinformationen für das angeforderte Abonnement und den angeforderten Zeitraum ab. Es gibt keinen Anforderungstext.

Die Nutzungs-API ist eine Anbieter-API. Aus diesem Grund muss dem Aufrufer im Abonnement des Anbieters die Rolle des **Besitzers**, **Mitwirkenden** oder **Lesers** zugewiesen sein.

| Methode | Anforderungs-URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Argumente

| Argument | BESCHREIBUNG |
| --- | --- |
| `armendpoint` |Azure Resource Management-Endpunkt Ihrer Azure Stack-Umgebung. Nach Azure Stack-Konvention folgt der Name des Azure Resource Manager-Endpunkts dem Format `https://adminmanagement.{domain-name}`. <!-- TZLASDKFIX For example, for the Azure Stack Development Kit (ASDK), if the domain name is *local.azurestack.external*, then the Resource Manager endpoint is `https://adminmanagement.local.azurestack.external`. --> |
| `subId` |Abonnement-ID des Benutzers, von dem der Aufruf ausgeht. |
| `reportedStartTime` |Die Startzeit der Abfrage. Der Wert für `DateTime` sollte in koordinierter Weltzeit (UTC) und vollen Stunden angegeben werden, z. B. 13:00. Legen Sie den Wert für eine tägliche Aggregation auf Mitternacht (UTC) fest. Das Format ist ISO 8601 mit Escapezeichen, z. B. `2015-06-16T18%3a53%3a11%2b00%3a00Z`. Dabei wird der Doppelpunkt mit Escapezeichen zu `%3a` und das Pluszeichen zu `%2b`, um auch für URIs geeignet zu sein. |
| `reportedEndTime` |Die Endzeit der Abfrage. Die Einschränkungen, die für `reportedStartTime` gelten, gelten auch für dieses Argument. Der Wert von `reportedEndTime` darf nicht in der Zukunft liegen oder das aktuelle Datum sein. Wenn dies der Fall ist, wird als Ergebnis „processing not complete“ (nicht abgeschlossen) angezeigt. |
| `aggregationGranularity` |Optionaler Parameter, der zwei mögliche diskrete Werte hat: **täglich** und **stündlich**. Wie die Namen schon sagen, gibt der eine Wert Daten in Abständen von einem Tag zurück und der andere in Abständen von einer Stunde. Der Standard ist die Option **täglich**. |
| `subscriberId` |Abonnement-ID Um gefilterte Daten zu erhalten, ist die Abonnement-ID eines direkten Mandanten des Anbieters erforderlich. Wenn kein Parameter für die Abonnement-ID angegeben ist, gibt der Aufruf Nutzungsdaten für alle direkten Mandanten des Anbieters zurück. |
| `api-version` |Die Version des Protokolls, das für diese Anforderung verwendet wird. Dieser Wert ist auf `2015-06-01-preview` festgelegt. |
| `continuationToken` |Das Token, das durch den letzten Aufruf des Anbieters der Nutzungs-API abgerufen wurde. Dieses Token ist erforderlich, wenn eine Antwort länger als 1.000 Zeilen ist. Es fungiert als Lesezeichen für den Status. Falls das Token nicht vorhanden ist, werden die Daten auf Grundlage der übergebenen Granularität vom Anfang des Tages oder der Stunde abgerufen. |

### <a name="response"></a>Antwort

```http
GET
/subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0
```

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

| Argument | BESCHREIBUNG |
| --- | --- |
|`id` |Eindeutige ID des Nutzungsaggregats. |
|`name` |Name des Nutzungsaggregats. |
|`type` |Ressourcendefinition. |
|`subscriptionId` |Bezeichner des Abonnements des Azure Stack-Benutzers. |
|`usageStartTime`|Startzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört.|
|`usageEndTime`|Endzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört. |
|`instanceData` |Schlüssel-Wert-Paare der genaueren Angaben zu der Instanz (in neuem Format):<br> `resourceUri`: Vollqualifizierte Ressourcen-ID, die die Ressourcengruppen und den Instanznamen enthält. <br> `location`: Region, in der der Dienst ausgeführt wurde. <br> `tags`: Ressourcentags, die vom Benutzer angegeben werden. <br> `additionalInfo`: Weitere Angaben zur genutzten Ressource, z.B. die Betriebssystemversion oder der Imagetyp. |
|`quantity`|Menge der Ressourcennutzung, die in diesem Zeitraum aufgetreten ist. |
|`meterId` |Eindeutige ID der Verbrauchseinheit: Eine eindeutige ID für die verwendete Ressource (auch als `ResourceID` bezeichnet). |

## <a name="retrieve-usage-information"></a>Abrufen von Nutzungsinformationen

### <a name="powershell"></a>PowerShell

Zum Generieren der Nutzungsdaten benötigen Sie Ressourcen, die ausgeführt werden und das System aktiv verwenden, beispielsweise einen aktiven virtuellen Computer oder ein Speicherkonto mit Daten. Wenn Sie nicht sicher sind, ob Sie über Ressourcen verfügen, die im Azure Stack Marketplace ausgeführt werden, stellen Sie einen virtuellen Computer bereit, und überprüfen Sie das Überwachungsblatt für diesen virtuellen Computer, um sicherzustellen, dass er ausgeführt wird. Verwenden Sie die folgenden PowerShell-Cmdlets, um die Nutzungsdaten anzuzeigen:

1. [Installieren Sie PowerShell für Azure Stack](../../operator/azure-stack-powershell-install.md).
2. Konfigurieren Sie die PowerShell-Umgebung des [Azure Stack-Benutzers](../../user/azure-stack-powershell-configure-user.md) oder des [Azure Stack-Betreibers](../../operator/azure-stack-powershell-configure-admin.md).
3. Rufen Sie zum Abrufen der Nutzungsdaten das PowerShell-Cmdlet [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) ab:

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST-API

Sie können Nutzungsinformationen für gelöschte Abonnements sammeln, indem Sie den Microsoft.Commerce.Admin-Dienst aufrufen.

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Zurückgeben der gesamte Mandantennutzung für gelöschte Abonnements für aktive Benutzer

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Zurückgeben der Nutzung für gelöschte oder aktive Mandanten

| Methode | Anforderungs-URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |
