---
title: Ressourcennutzungs-API für Azure Stack Hub-Anbieter | Microsoft-Dokumentation
description: Referenz für die Ressourcennutzungs-API, die Azure Stack Hub-Nutzungsinformationen abruft
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: e1d2e350079ab3d58802c8490da30a4c9555885d
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023262"
---
# <a name="provider-resource-usage-api"></a>Ressourcennutzungs-API für Anbieter

Der Begriff *Anbieter* bezieht sich auf den Dienstadministrator und alle delegierten Anbieter. Azure Stack Hub-Betreibers und delegierte Anbieter können die Nutzungs-API für Anbieter verwenden, um einen Einblick in die Nutzung ihrer direkten Mandanten zu erhalten. Wie im folgenden Diagramm dargestellt, kann P0 beispielsweise die Anbieter-API aufrufen, um Informationen zur direkten Nutzung von P1 und P2 abzurufen, und P1 kann die Nutzungsinformationen von P3 und P4 abrufen.

![Konzeptuelles Modell der Anbieterhierarchie](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API-Aufrufreferenz

### <a name="request"></a>Anforderung

Die Anforderung ruft detaillierte Nutzungsinformationen für das angeforderte Abonnement und den angeforderten Zeitraum ab. Es gibt keinen Anforderungstext.

Die Nutzungs-API ist eine Anbieter-API. Aus diesem Grund muss dem Aufrufer im Abonnement des Anbieters die Rolle des **Besitzers**, **Mitwirkenden** oder **Lesers** zugewiesen sein.

| Methode | Anforderungs-URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
| --- | --- |
| `armendpoint` |Azure Resource Manager-Endpunkt Ihrer Azure Stack Hub-Umgebung. Nach Azure Stack Hub-Konvention folgt der Name des Azure Resource Manager-Endpunkts dem Format `https://adminmanagement.{domain-name}`. Wenn der Domänenname für das Azure Stack Development Kit (ASDK) beispielsweise *local.azurestack.external* lautet, ist der Resource Manager-Endpunkt `https://adminmanagement.local.azurestack.external`. |
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

| Argument | Beschreibung |
| --- | --- |
|`id` |Eindeutige ID des Nutzungsaggregats. |
|`name` |Name des Nutzungsaggregats. |
|`type` |Ressourcendefinition. |
|`subscriptionId` |Bezeichner des Abonnements des Azure Stack Hub-Benutzers. |
|`usageStartTime`|Startzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört.|
|`usageEndTime`|Endzeit (UTC) des Nutzungsbuckets, zu dem dieses Nutzungsaggregat gehört. |
|`instanceData` |Schlüssel-Wert-Paare der genaueren Angaben zu der Instanz (in neuem Format):<br> `resourceUri`: Vollqualifizierte Ressourcen-ID, die die Ressourcengruppen und den Instanznamen enthält. <br> `location`: Region, in der der Dienst ausgeführt wurde. <br> `tags`: Ressourcentags, die vom Benutzer angegeben werden. <br> `additionalInfo`: Weitere Angaben zur genutzten Ressource, z.B. die Betriebssystemversion oder der Imagetyp. |
|`quantity`|Menge der Ressourcennutzung, die in diesem Zeitraum aufgetreten ist. |
|`meterId` |Eindeutige ID der Verbrauchseinheit: Eine eindeutige ID für die verwendete Ressource (auch als `ResourceID` bezeichnet). |

## <a name="retrieve-usage-information"></a>Abrufen von Nutzungsinformationen

### <a name="powershell"></a>PowerShell

Zum Generieren der Nutzungsdaten benötigen Sie Ressourcen, die ausgeführt werden und das System aktiv verwenden, beispielsweise einen aktiven virtuellen Computer oder ein Speicherkonto mit Daten. Wenn Sie nicht sicher sind, ob Sie über Ressourcen verfügen, die im Azure Stack Hub-Marketplace ausgeführt werden, stellen Sie eine VM bereit. Stellen Sie dann mithilfe des Überwachungsblatt für diese VM sicher, dass die VM ausgeführt wird. Verwenden Sie die folgenden PowerShell-Cmdlets, um die Nutzungsdaten anzuzeigen:

1. [Installieren Sie PowerShell für Azure Stack Hub](azure-stack-powershell-install.md).
2. Konfigurieren Sie die PowerShell-Umgebung des [Azure Stack Hub-Benutzers](../user/azure-stack-powershell-configure-user.md) oder des [Azure Stack Hub-Betreibers](azure-stack-powershell-configure-admin.md).
3. Rufen Sie zum Abrufen der Nutzungsdaten das PowerShell-Cmdlet [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) ab:

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST-API

Sie können Nutzungsinformationen für gelöschte Abonnements sammeln, indem Sie den **Microsoft.Commerce.Admin**-Dienst aufrufen.

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Zurückgeben der gesamte Mandantennutzung für gelöschte Abonnements für aktive Benutzer

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Zurückgeben der Nutzung für gelöschte oder aktive Mandanten

| Methode | Anforderungs-URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>Nächste Schritte

- [Referenz zur Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md)
- [Häufig gestellte Fragen zur Nutzung](azure-stack-usage-related-faq.md)
