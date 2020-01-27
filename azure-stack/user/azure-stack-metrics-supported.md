---
title: Unterstützte Metriken für Azure Monitor in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie mehr über die unterstützten Metriken von Azure Monitor in Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 321b0c6137b29fd9edea2a6ae05237e0210a8ad2
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878661"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Unterstützte Metriken für Azure Monitor in Azure Stack Hub

Metriken aus Azure Monitor in Azure Stack Hub können genauso wie in der allgemeinen Azure-Umgebung abgerufen werden. Sie können Ihre Measures im Portal erstellen, über die REST-API abrufen oder per PowerShell oder Befehlszeilenschnittstelle abfragen.

Die folgenden Tabellen enthalten die Metriken, die über die Metrikpipeline von Azure Monitor in Azure Stack Hub verfügbar sind. Wenn Sie diese Metriken abfragen oder darauf zugreifen möchten, müssen Sie die API-Version **2018-01-01** des API-Profils verwenden. Weitere Informationen zu API-Profilen und Azure Stack Hub finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack Hub](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrik | Metrikanzeigename | Einheit | Aggregationstyp | Beschreibung | Dimensionen |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| CPU in Prozent | CPU in Prozent | Percent | Average | Der Prozentsatz der zugewiesenen Compute-Einheiten, die derzeit von den VMs verwendet werden | Keine Dimensionen |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrik | Metrikanzeigename | Einheit | Aggregationstyp | Beschreibung | Dimensionen |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Verwendete Kapazität | Byte | Average | Vom Konto verwendete Kapazität | Keine Dimensionen |
| Transaktionen | Transaktionen | Anzahl | Gesamt | Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen. | ResponseType, GeoType, ApiName |
| Eingehende Daten | Eingehende Daten | Byte | Gesamt | Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst in Azure Storage eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. | GeoType, ApiName |
| Ausgehende Daten | Ausgehende Daten | Byte | Gesamt | Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst in Azure Storage ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. | GeoType, ApiName |
| SuccessServerLatency | Serverlatenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz. | GeoType, ApiName |
| SuccessE2ELatency | E2E-Latenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung. | GeoType, ApiName |
| Verfügbarkeit | Verfügbarkeit | Percent | Average | Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Berechnen Sie die Verfügbarkeit, indem Sie den Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen teilen, einschließlich derer, die unerwartete Fehler erzeugt haben. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrik | Metrikanzeigename | Einheit | Aggregationstyp | Beschreibung | Dimensionen |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Blob-Kapazität | Byte | Gesamt | Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte. | BlobType |
| BlobCount | Anzahl von Blobs | Anzahl | Gesamt | Die Anzahl von Blobs im Blob-Dienst des Speicherkontos. | BlobType |
| ContainerCount | Anzahl von Blob-Containern | Anzahl | Average | Die Anzahl von Containern im Blob-Dienst des Speicherkontos. | Keine Dimensionen |
| Transaktionen | Transaktionen | Anzahl | Gesamt | Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen. | ResponseType, GeoType, ApiName |
| Eingehende Daten | Eingehende Daten | Byte | Gesamt | Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst in Azure Storage eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. | GeoType, ApiName |
| Ausgehende Daten | Ausgehende Daten | Byte | Gesamt | Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst in Azure Storage ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. | GeoType, ApiName |
| SuccessServerLatency | Serverlatenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz. | GeoType, ApiName |
| SuccessE2ELatency | E2E-Latenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung. | GeoType, ApiName |
| Verfügbarkeit | Verfügbarkeit | Percent | Average | Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Berechnen Sie die Verfügbarkeit, indem Sie den Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen teilen, einschließlich derer, die unerwartete Fehler erzeugt haben. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrik | Metrikanzeigename | Einheit | Aggregationstyp | Beschreibung | Dimensionen |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Tabellenkapazität | Byte | Average | Die Größe des vom Tabellendienst des Speicherkontos genutzten Speichers in Byte. | Keine Dimensionen |
| TableCount | Anzahl von Tabellen | Anzahl | Average | Die Anzahl von Tabellen im Tabellenspeicherdienst des Speicherkontos. | Keine Dimensionen |
| TableEntityCount | Anzahl von Tabellenentitäten | Anzahl | Average | Die Anzahl von Tabellenentitäten im Tabellendienst des Speicherkontos. | Keine Dimensionen |
| Transaktionen | Transaktionen | Anzahl | Gesamt | Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen. | ResponseType, GeoType, ApiName |
| Eingehende Daten | Eingehende Daten | Byte | Gesamt | Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst in Azure Storage eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. | GeoType, ApiName |
| Ausgehende Daten | Ausgehende Daten | Byte | Gesamt | Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst in Azure Storage ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. | GeoType, ApiName |
| SuccessServerLatency | Serverlatenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz. | GeoType, ApiName |
| SuccessE2ELatency | E2E-Latenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung. | GeoType, ApiName |
| Verfügbarkeit | Verfügbarkeit | Percent | Average | Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Berechnen Sie die Verfügbarkeit, indem Sie den Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen teilen, einschließlich derer, die unerwartete Fehler erzeugt haben. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrik | Metrikanzeigename | Einheit | Aggregationstyp | Beschreibung | Dimensionen |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Warteschlangenkapazität | Byte | Average | Die Größe des vom Warteschlangendienst des Speicherkontos genutzten Speichers in Byte. | Keine Dimensionen |
| QueueCount | Anzahl von Warteschlangen | Anzahl | Average | Die Anzahl von Warteschlangen im Warteschlangendienst des Speicherkontos. | Keine Dimensionen |
| QueueMessageCount | Anzahl von Warteschlangennachrichten | Anzahl | Average | Die ungefähre Anzahl von Warteschlangennachrichten im Warteschlangendienst des Speicherkontos. | Keine Dimensionen |
| Transaktionen | Transaktionen | Anzahl | Gesamt | Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen. | ResponseType, GeoType, ApiName |
| Eingehende Daten | Eingehende Daten | Byte | Gesamt | Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst in Azure Storage eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. | GeoType, ApiName |
| Ausgehende Daten | Ausgehende Daten | Byte | Gesamt | Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst in Azure Storage ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. | GeoType, ApiName |
| SuccessServerLatency | Serverlatenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz. | GeoType, ApiName |
| SuccessE2ELatency | E2E-Latenz (erfolgreich) | Millisekunden | Average | Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung. | GeoType, ApiName |
| Verfügbarkeit | Verfügbarkeit | Percent | Average | Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Berechnen Sie die Verfügbarkeit, indem Sie den Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen teilen, einschließlich derer, die unerwartete Fehler erzeugt haben. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang. | GeoType, ApiName |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Azure Monitor in Azure Stack Hub](azure-stack-metrics-azure-data.md).
