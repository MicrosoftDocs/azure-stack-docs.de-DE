---
title: 'Übersicht: Kapazitätsplanung für Azure Stack Hub | Microsoft-Dokumentation'
description: Hier erfahren Sie mehr über die Kapazitätsplanung für Azure Stack Hub-Bereitstellungen einschließlich Compute- und Speicherinformationen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 12862a71146606bfba81ea0de630a93181951c06
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598588"
---
# <a name="overview-of-azure-stack-hub-capacity-planning---modular-data-center-mdc"></a>Übersicht über die Azure Stack Hub-Kapazitätsplanung – Modular Data Center (MDC)

Berücksichtigen Sie beim Auswerten einer Azure Stack Hub-Lösung die Entscheidungen in Bezug auf die Hardwarekonfiguration, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack Hub-Cloud haben. 

Beispielsweise müssen Sie Entscheidungen treffen zur CPU, Arbeitsspeicherdichte, Speicherkonfiguration und zum Gesamtumfang der Lösung oder der Anzahl von Servern. Im Gegensatz zu einer herkömmlichen Virtualisierungslösung gilt die einfache Arithmetik dieser Komponenten zur Ermittlung der nutzbaren Kapazität nicht. Azure Stack Hub ist so aufgebaut, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Außerdem ist ein Teil der Lösungskapazität zur Unterstützung der Resilienz reserviert. Es geht um die Aktualisierung der Lösungssoftware auf eine Weise, bei der die Beeinträchtigung von Mandantenworkloads verringert wird. 

> [!IMPORTANT]
> Diese Informationen zur Kapazitätsplanung und der [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) sind ein guter Ausgangspunkt für Planungs- und Konfigurationsentscheidungen für Azure Stack Hub. Diese Informationen sollen aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.
 
Eine Azure Stack Hub-Lösung ist als hyperkonvergenter Cluster mit Compute- und Speicherkomponenten aufgebaut. Die Konvergenz ermöglicht die gemeinsame Nutzung der Hardwarekapazität im Cluster. Dies wird als *Skalierungseinheit* bezeichnet. In Azure Stack Hub wird mit einer Skalierungseinheit für die Verfügbarkeit und Skalierbarkeit Ihrer Ressourcen gesorgt. Eine Skalierungseinheit besteht aus einer Reihe von Azure Stack Hub-Servern, die als *Hosts* bezeichnet werden. Die Infrastruktursoftware wird in einer Gruppe von virtuellen Computern (VMs) gehostet und verwendet die gleichen physischen Server wie die Mandanten-VMs. Alle Azure Stack Hub-VMs werden dann von den Windows Server-Clusteringtechnologien der Skalierungseinheit und einzelnen Hyper-V-Instanzen verwaltet. 

Die Skalierungseinheit vereinfacht den Erwerb und die Verwaltung von Azure Stack Hub. Darüber hinaus ermöglicht die Skalierungseinheit das Verschieben und Skalieren aller Dienste (Mandant und Infrastruktur) unter Azure Stack Hub. 

Die folgenden Themen enthalten weitere Informationen zu den einzelnen Komponenten:

- [Azure Stack Hub-Compute](../operator/azure-stack-capacity-planning-compute.md)
- [Azure Stack Hub-Speicher](../operator/azure-stack-capacity-planning-storage.md)
- [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)

