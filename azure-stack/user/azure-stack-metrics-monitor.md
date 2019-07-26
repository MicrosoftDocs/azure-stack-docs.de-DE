---
title: Nutzen der Überwachungsdaten von Azure Stack | Microsoft-Dokumentation
description: Lernen Sie Optionen zum Nutzen von Überwachungsdaten aus Azure Stack kennen.
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
ms.date: 04/15/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 0b04a7834218d07706e6314a9c41e0370ff6f6fd
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816186"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Nutzen der Überwachungsdaten von Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme*

Überwachungsdaten befinden sich an einem Ort in der Azure Monitor-Pipeline, genau wie Azure Monitor im globalen Azure. Aber nicht alle globalen Azure-Überwachungsdaten stehen in Azure Stack zur Verfügung. In diesem Artikel erhalten Sie eine Zusammenfassung der verschiedenen Möglichkeiten zur Nutzung von Überwachungsdaten in Azure Stack.
 
## <a name="options-for-data-consumption"></a>Optionen für die Datennutzung

| Datentyp | Category (Kategorie) | Unterstützte Dienste | Zugriffsmethoden |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor-Metriken auf Plattformebene | metrics | [Unterstützte Metriken von Azure Monitor in Azure Stack](azure-stack-metrics-supported.md) | REST-API |
| Computemetriken zum Gastbetriebssystem (z.B. Leistungsindikator) | metrics | Virtuelle Windows- und Linux-Computer | Speichertabelle oder -blob:<br>Azure-Diagnose unter Windows oder Linux <br>Event Hub:<br>Azure-Diagnose unter Windows |
| Speichermetrik | metrics | Azure Storage | Speichertabelle:<br>Speicheranalyse |
| Aktivitätsprotokoll | Events | Alle Azure-Dienste | REST-API:<br>Azure Monitor-Ereignis-API |
| Computeprotokolle für Gastbetriebssysteme (z.B. IIS, ETW, Syslog-Protokolle) | Events | Virtuelle Windows- und Linux-Computer | Speichertabelle oder -blob:<br>Azure-Diagnose unter Windows oder Linux <br>Event Hub:<br>Azure-Diagnose unter Windows |
| Speicherprotokolle | Events | Azure Storage | Speichertabelle:<br>Speicheranalyse |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Azure Monitor in Azure Stack](azure-stack-metrics-azure-data.md).
