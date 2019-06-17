---
title: Übersicht über die Kapazitätsplanung für Azure Stack | Microsoft-Dokumentation
description: Informationen zur Kapazitätsplanung für Azure Stack-Bereitstellungen.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 0d2610d2082973f4ab255027cc299b5858660c58
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461077"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Übersicht über die Azure Stack-Kapazitätsplanung

Beim Auswerten einer Azure Stack-Lösung müssen Entscheidungen in Bezug auf die Hardwarekonfiguration getroffen werden, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack-Cloud haben. Hierzu gehört die übliche Auswahl von CPU, Arbeitsspeicherdichte, Speicherkonfiguration und Gesamtumfang der Lösung (z.B. Anzahl von Servern). Im Gegensatz zu einer herkömmlichen Virtualisierungslösung gilt die einfache Arithmetik dieser Komponenten zur Ermittlung der nutzbaren Kapazität nicht. Der erste Grund dafür ist, dass Azure Stack so aufgebaut ist, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Der zweite Grund ist, dass ein Teil der Lösungskapazität zur Unterstützung der Resilienz reserviert ist. Es geht um die Aktualisierung der Lösungssoftware auf eine Weise, bei der die Beeinträchtigung von Mandantenworkloads verringert wird. 

> [!IMPORTANT]
> Diese Informationen zur Kapazitätsplanung und der [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) sind ein guter Ausgangspunkt für Planungs- und Konfigurationsentscheidungen für Azure Stack. Dies soll aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.
 
Eine Azure Stack-Lösung ist als hyperkonvergenter Cluster mit Compute- und Speicherkomponenten aufgebaut. Die Konvergenz ermöglicht die gemeinsame Nutzung der Hardwarekapazität im Cluster. Dies wird als *Skalierungseinheit* bezeichnet. In Azure Stack wird mit einer Skalierungseinheit für die Verfügbarkeit und Skalierbarkeit Ihrer Ressourcen gesorgt. Eine Skalierungseinheit besteht aus einer Reihe von Azure Stack-Servern, die als *Hosts* bezeichnet werden. Die Infrastruktursoftware wird in einer Gruppe von VMs gehostet und verwendet die gleichen physischen Server wie die Mandanten-VMs. Alle Azure Stack-VMs werden dann von den Windows Server-Clusteringtechnologien der Skalierungseinheit und einzelnen Hyper-V-Instanzen verwaltet. Die Skalierungseinheit vereinfacht den Erwerb und die Verwaltung von Azure Stack. Darüber hinaus ermöglicht die Skalierungseinheit das Verschieben und Skalieren aller Dienste (Mandant und Infrastruktur) unter Azure Stack. 

Die folgenden Themen enthalten weitere Informationen zu den einzelnen Komponenten:

- [Azure Stack-Compute](azure-stack-capacity-planning-compute.md)
- [Azure Stack-Speicher](azure-stack-capacity-planning-storage.md)
- [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
