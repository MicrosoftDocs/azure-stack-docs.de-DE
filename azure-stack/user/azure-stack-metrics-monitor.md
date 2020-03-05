---
title: Nutzen der Überwachungsdaten von Azure Stack Hub
description: Lernen Sie Optionen zum Nutzen von Überwachungsdaten aus Azure Stack Hub kennen.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 9bbe39f63a4b59446f5c8d6444381afc9ea89cc8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703926"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Nutzen der Überwachungsdaten von Azure Stack Hub

Überwachungsdaten befinden sich an einem Ort in der Azure Monitor-Pipeline, genau wie Azure Monitor im globalen Azure. Aber nicht alle globalen Azure-Überwachungsdaten stehen in Azure Stack Hub zur Verfügung. In diesem Artikel erhalten Sie eine Zusammenfassung der verschiedenen Möglichkeiten zur Nutzung von Überwachungsdaten in Azure Stack Hub.
 
## <a name="options-for-data-consumption"></a>Optionen für die Datennutzung

| Datentyp | Category | Unterstützte Dienste | Zugriffsmethoden |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor-Metriken auf Plattformebene | Metriken | [Unterstützte Metriken von Azure Monitor in Azure Stack Hub](azure-stack-metrics-supported.md) | REST-API |
| Computemetriken zum Gastbetriebssystem (z.B. Leistungsindikator) | Metriken | Virtuelle Windows- und Linux-Computer | Speichertabelle oder -blob:<br>Azure-Diagnose unter Windows oder Linux <br>Event Hub:<br>Azure-Diagnose unter Windows |
| Speichermetrik | Metriken | Azure Storage | Speichertabelle:<br>Speicheranalyse |
| Aktivitätsprotokoll | Events | Alle Azure-Dienste | REST-API:<br>Azure Monitor-Ereignis-API |
| Computeprotokolle für Gastbetriebssysteme (z.B. IIS, ETW, Syslog-Protokolle) | Events | Virtuelle Windows- und Linux-Computer | Speichertabelle oder -blob:<br>Azure-Diagnose unter Windows oder Linux <br>Event Hub:<br>Azure-Diagnose unter Windows |
| Speicherprotokolle | Events | Azure Storage | Speichertabelle:<br>Speicheranalyse |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Azure Monitor in Azure Stack Hub](azure-stack-metrics-azure-data.md).
