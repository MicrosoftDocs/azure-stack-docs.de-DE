---
title: Übersicht über die Kapazitätsplanung für Azure Stack Hub | Microsoft-Dokumentation
description: Informationen zur Kapazitätsplanung für Azure Stack Hub-Bereitstellungen. Hier finden Sie die Spezifikationen für die hohen und niedrigen robusten Azure Stack Hub-Modelle.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 1231b69574466cadcebff56f23cbb85d4dbaa9fd
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867977"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Übersicht über die Azure Stack Hub-Kapazitätsplanung

Berücksichtigen Sie beim Auswerten einer Azure Stack Hub-Lösung die Entscheidungen in Bezug auf die Hardwarekonfiguration, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack Hub-Cloud haben. 

Beispielsweise müssen Sie Entscheidungen treffen zur CPU, Arbeitsspeicherdichte, Speicherkonfiguration und zum Gesamtumfang der Lösung oder der Anzahl von Servern. Im Gegensatz zu einer herkömmlichen Virtualisierungslösung gilt die einfache Arithmetik dieser Komponenten zur Ermittlung der nutzbaren Kapazität nicht. Azure Stack Hub ist so aufgebaut, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Außerdem ist ein Teil der Lösungskapazität zur Unterstützung der Resilienz reserviert. Es geht um die Aktualisierung der Lösungssoftware auf eine Weise, bei der die Beeinträchtigung von Mandantenworkloads verringert wird. 

> [!IMPORTANT]
> Diese Informationen zur Kapazitätsplanung und der [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) sind ein guter Ausgangspunkt für Planungs- und Konfigurationsentscheidungen für Azure Stack Hub. Diese Informationen sollen aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.
 
Eine Azure Stack Hub-Lösung ist als hyperkonvergenter Cluster mit Compute- und Speicherkomponenten aufgebaut. Die Konvergenz ermöglicht die gemeinsame Nutzung der Hardwarekapazität im Cluster. Dies wird als *Skalierungseinheit* bezeichnet. In Azure Stack Hub wird mit einer Skalierungseinheit für die Verfügbarkeit und Skalierbarkeit Ihrer Ressourcen gesorgt. Eine Skalierungseinheit besteht aus einer Reihe von Azure Stack Hub-Servern, die als *Hosts* bezeichnet werden. Die Infrastruktursoftware wird in einer Gruppe von virtuellen Computern (VMs) gehostet und verwendet die gleichen physischen Server wie die Mandanten-VMs. Alle Azure Stack Hub-VMs werden dann von den Windows Server-Clusteringtechnologien der Skalierungseinheit und einzelnen Hyper-V-Instanzen verwaltet. 

Die Skalierungseinheit vereinfacht den Erwerb und die Verwaltung von Azure Stack Hub. Darüber hinaus ermöglicht die Skalierungseinheit das Verschieben und Skalieren aller Dienste (Mandant und Infrastruktur) unter Azure Stack Hub. 

Die folgenden Themen enthalten weitere Informationen zu den einzelnen Komponenten:

- [Azure Stack Hub-Compute](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack Hub-Speicher](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack Hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)

## <a name="azure-stack-hub-ruggedized-high-and-low-models"></a>Hohe und niedrige Azure Stack Hub Ruggedized-Modelle

In der folgenden Tabelle sind die Spezifikationen für die hohen und niedrigen Azure Stack Hub Ruggedized-Modelle aufgeführt.

| Komponente               | Spezifikation |
|-------------------------|---------------|
| CPU                     |Hoch: 284 vCPU-Kerne<br>Niedrig: 184 vCPU-Kerne  |
| Arbeitsspeicher                  |Hoch: 1.037 GB<br>Niedrig: 547 GB                |
| Storage                 |Hoch: 34,2 TB<br>Niedrig: 15,4 TB                |
| Acceleration            |–                                          |
| Größe/Gewicht             |Compute: 80 cm L x 60,45 cm B x 38,94 cm H<br>Gewicht 54,43 kg (2 Stück)<br>Netzwerk: 80 cm L x 60,45 cm B x 43,48 cm H<br>Gewicht 65,77 kg (1 Stück)              |
| Optionale Konfigurationen |Vorwärmer<br>Hohe oder niedrige Konfiguration     |

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Compute](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
[Azure Stack Hub-Speicher](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
[Azure Stack Hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
