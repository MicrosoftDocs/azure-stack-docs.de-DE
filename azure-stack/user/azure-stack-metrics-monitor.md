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
ms.openlocfilehash: a068576cb907dfc2f7fcc79b1beea08899a2cb2c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310494"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Nutzen der Überwachungsdaten von Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme*

Sie finden sämtliche Überwachungsdaten an einem Ort in der Azure Monitor-Pipeline – wie auch für die allgemeine Azure-Umgebung in Azure Monitor. Aber nicht alle globalen Azure-Überwachungsdaten stehen in Azure Stack zur Verfügung. In diesem Artikel finden Sie eine Zusammenfassung der verschiedenen Möglichkeiten zum programmgesteuerten Erfassen von Überwachungsdaten aus dem Dienst.
 
## <a name="options-for-data-consumption"></a>Optionen für die Datennutzung

| Datentyp | Category (Kategorie) | Unterstützte Dienste | Zugriffsmethoden |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor-Metriken auf Plattformebene | Metriken | [Unterstützte Metriken von Azure Monitor in Azure Stack](azure-stack-metrics-supported.md) | REST-API |
| Computemetriken zum Gastbetriebssystem (z.B. Leistungsindikator) | Metriken | Virtuelle Windows- und Linux-Computer | Speichertabelle oder -blob:<br>Azure-Diagnose unter Windows oder Linux <br>Event Hub:<br>Azure-Diagnose unter Windows |
| Speichermetrik | Metriken | Azure Storage | Speichertabelle:<br>Speicheranalyse |
| Aktivitätsprotokoll | Ereignisse | Alle Azure-Dienste | REST-API:<br>Azure Monitor-Ereignis-API |
| Computeprotokolle für Gastbetriebssysteme (z.B. IIS, ETW, Syslog-Protokolle) | Ereignisse | Virtuelle Windows- und Linux-Computer | Speichertabelle oder -blob:<br>Azure-Diagnose unter Windows oder Linux <br>Event Hub:<br>Azure-Diagnose unter Windows |
| Speicherprotokolle | Ereignisse | Azure Storage | Speichertabelle:<br>Speicheranalyse |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Azure Monitor in Azure Stack](azure-stack-metrics-azure-data.md).
