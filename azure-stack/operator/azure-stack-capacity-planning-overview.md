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
ms.openlocfilehash: 335e92fcdb2eadd8bebfbfd3fb2e3b31edd00734
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131336"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Übersicht über die Azure Stack-Kapazitätsplanung

Berücksichtigen Sie beim Auswerten einer Azure Stack-Lösung die Entscheidungen in Bezug auf die Hardwarekonfiguration, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack-Cloud haben. 

Beispielsweise müssen Sie Entscheidungen treffen zur CPU, Arbeitsspeicherdichte, Speicherkonfiguration und zum Gesamtumfang der Lösung oder der Anzahl von Servern. Im Gegensatz zu einer herkömmlichen Virtualisierungslösung gilt die einfache Arithmetik dieser Komponenten zur Ermittlung der nutzbaren Kapazität nicht. Azure Stack ist so aufgebaut, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Außerdem ist ein Teil der Lösungskapazität zur Unterstützung der Resilienz reserviert. Es geht um die Aktualisierung der Lösungssoftware auf eine Weise, bei der die Beeinträchtigung von Mandantenworkloads verringert wird. 

> [!IMPORTANT]
> Diese Informationen zur Kapazitätsplanung und der [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) sind ein guter Ausgangspunkt für Planungs- und Konfigurationsentscheidungen für Azure Stack. Diese Informationen sollen aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.
 
Eine Azure Stack-Lösung ist als hyperkonvergenter Cluster mit Compute- und Speicherkomponenten aufgebaut. Die Konvergenz ermöglicht die gemeinsame Nutzung der Hardwarekapazität im Cluster. Dies wird als *Skalierungseinheit* bezeichnet. In Azure Stack wird mit einer Skalierungseinheit für die Verfügbarkeit und Skalierbarkeit Ihrer Ressourcen gesorgt. Eine Skalierungseinheit besteht aus einer Reihe von Azure Stack-Servern, die als *Hosts* bezeichnet werden. Die Infrastruktursoftware wird in einer Gruppe von virtuellen Computern (VMs) gehostet und verwendet die gleichen physischen Server wie die Mandanten-VMs. Alle Azure Stack-VMs werden dann von den Windows Server-Clusteringtechnologien der Skalierungseinheit und einzelnen Hyper-V-Instanzen verwaltet. 

Die Skalierungseinheit vereinfacht den Erwerb und die Verwaltung von Azure Stack. Darüber hinaus ermöglicht die Skalierungseinheit das Verschieben und Skalieren aller Dienste (Mandant und Infrastruktur) unter Azure Stack. 

Die folgenden Themen enthalten weitere Informationen zu den einzelnen Komponenten:

- [Azure Stack-Compute](azure-stack-capacity-planning-compute.md)
- [Azure Stack-Speicher](azure-stack-capacity-planning-storage.md)
- [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
