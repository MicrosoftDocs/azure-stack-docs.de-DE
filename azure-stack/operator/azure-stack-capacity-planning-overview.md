---
title: Übersicht über die Kapazitätsplanung
titleSuffix: Azure Stack Hub
description: Informationen zur Kapazitätsplanung für Azure Stack Hub-Bereitstellungen.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 0ef537db34ef882257ec5d35eb7d6c91ac1b03d1
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625236"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Übersicht über die Kapazitätsplanung für Azure Stack Hub

Berücksichtigen Sie beim Auswerten einer Azure Stack Hub-Lösung die Entscheidungen in Bezug auf die Hardwarekonfiguration, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack Hub-Cloud haben.

Sie müssen Entscheidungen zur CPU, Arbeitsspeicherdichte, Speicherkonfiguration und zum Gesamtumfang der Lösung oder der Anzahl von Servern treffen. Die Bestimmung der nutzbaren Kapazität unterscheidet sich jedoch von einer herkömmlichen Virtualisierungslösung, da ein Teil der Kapazität bereits verwendet wird. Azure Stack Hub ist so aufgebaut, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Außerdem ist ein Teil der Kapazität der Lösung für die Unterstützung der Resilienz reserviert. Resilienz ist als Aktualisierung der Lösungssoftware auf eine Weise definiert, bei der die Beeinträchtigung von Mandantenworkloads verringert wird.

> [!IMPORTANT]
> Diese Informationen zur Kapazitätsplanung und der [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) sind ein guter Ausgangspunkt für Planungs- und Konfigurationsentscheidungen für Azure Stack Hub. Diese Informationen sollen aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.

## <a name="hyperconvergence-and-the-scale-unit"></a>Hyperkonvergenz und Skalierungseinheit
Eine Azure Stack Hub-Lösung ist als hyperkonvergenter Cluster mit Compute- und Speicherkomponenten aufgebaut. Die Konvergenz ermöglicht die gemeinsame Nutzung der Hardwarekapazität im Cluster. Dies wird als *Skalierungseinheit* bezeichnet. In Azure Stack Hub wird mit einer Skalierungseinheit für die Verfügbarkeit und Skalierbarkeit Ihrer Ressourcen gesorgt. Eine Skalierungseinheit besteht aus einer Reihe von Azure Stack Hub-Servern, die als *Hosts* bezeichnet werden. Die Infrastruktursoftware wird in einer Gruppe von virtuellen Computern (VMs) gehostet und verwendet die gleichen physischen Server wie die Mandanten-VMs. Alle Azure Stack Hub-VMs werden dann von den Windows Server-Clusteringtechnologien der Skalierungseinheit und einzelnen Hyper-V-Instanzen verwaltet.

Die Skalierungseinheit vereinfacht den Erwerb und die Verwaltung von Azure Stack Hub. Darüber hinaus ermöglicht die Skalierungseinheit das Verschieben und Skalieren aller Dienste (Mandant und Infrastruktur) unter Azure Stack Hub.

Die folgenden Themen enthalten weitere Informationen zu den einzelnen Komponenten:

- [Azure Stack Hub-Compute](azure-stack-capacity-planning-compute.md)
- [Azure Stack Hub-Speicher](azure-stack-capacity-planning-storage.md)
- [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)
