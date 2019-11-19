---
title: Regionsverwaltung in Azure Stack | Microsoft-Dokumentation
titleSuffix: Azure Stack
description: Übersicht über die Regionsverwaltung in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 38004b88f43ef59448ca99c3eb2762e5ca63e89c
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802282"
---
# <a name="region-management-in-azure-stack"></a>Regionsverwaltung in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack verwendet *Regionen*. Dabei handelt es sich um logische Entitäten, die Hardwareressourcen umfassen, aus denen wiederum die Azure Stack-Infrastruktur besteht. In der Regionsverwaltung finden Sie alle Ressourcen, die für den erfolgreichen Betrieb der Azure Stack-Infrastruktur erforderlich sind.

Eine integrierte (als *Azure Stack-Cloud* bezeichnete) Systembereitstellung bildet eine einzelne Region. Jedes Azure Stack Development Kit (ASDK) hat eine einzelne als **lokal** benannte Region. Wenn Sie ein zweites integriertes Azure Stack-System bereitstellen oder eine andere Instanz des ASDK auf separater Hardware einrichten, ist diese Azure Stack-Cloud eine andere Region.

## <a name="information-available-through-the-region-management-tile"></a>Informationen, die über die Kachel „Regionsverwaltung“ verfügbar sind

Azure Stack verfügt über einige Funktionen der Regionsverwaltung, die in der Kachel **Regionsverwaltung** verfügbar sind. Diese Kachel steht Azure Stack-Operatoren im Administratorportal im Standarddashboard zur Verfügung. Über diese Kachel können Sie Ihre Azure Stack-Region und deren regionsspezifische Komponenten überwachen und aktualisieren.

![Die Kachel „Regionsverwaltung“ im Azure Stack-Administratorportal](media/azure-stack-region-management/image1.png)

Wenn Sie auf der Kachel **Regionsverwaltung** auf eine Region klicken, können Sie auf folgende Informationen zugreifen:

[![Beschreibung der Bereiche auf dem Blatt „Regionsverwaltung“ im Azure Stack-Administratorportal](media/azure-stack-region-management/regionssm.png "Blatt „Regionsverwaltung“ im Azure Stack-Administratorportal")](media/azure-stack-region-management/regions.png#lightbox)

1. **Das Ressourcenmenü**: Hier können Sie auf verschiedene Infrastrukturverwaltungsbereiche zugreifen und Benutzerressourcen anzeigen und verwalten. Zu solchen Ressourcen zählen etwa Speicherkonten und virtuelle Netzwerke.

2. **Warnungen**: Hier werden systemweite Warnungen sowie ausführlichere Informationen zu diesen Warnungen aufgelistet.

3. **Updates**: Hier werden die aktuelle Version Ihrer Azure Stack-Infrastruktur, verfügbare Updates und der Updateverlauf angezeigt. Sie können auch Ihr integriertes System aktualisieren.

4. **Ressourcenanbieter**: Hier können Sie die Benutzerfunktionen der Komponenten verwalten, die zur Ausführung von Azure Stack erforderlich sind. Jeder Ressourcenanbieter hat eine eigene Administratorerfahrung. Diese Erfahrung beinhaltet Warnungen für spezifische Anbieter, Metriken und andere Verwaltungsfunktionen, die ressourcenbetreiberspezifisch sind.

5. **Infrastrukturrollen**: Die Komponenten, die zur Ausführung von Azure Stack erforderlich sind. Es werden nur die Infrastrukturrollen aufgelistet, die Warnungen ausgeben. Wenn Sie eine Rolle auswählen, können Sie die Warnungen dieser Rolle anzeigen sowie die Rolleninstanzen, auf denen die Rolle ausgeführt wird.

6. **Properties:** Der Registrierungsstatus und die Details Ihrer Umgebung auf dem Blatt „Regionsverwaltung“. Der Status kann **Registriert**, **Nicht registriert** oder **Abgelaufen** sein. Beim Status „registriert“ wird außerdem die ID des Azure-Abonnements angezeigt, mit dem Sie Ihren Azure Stack registriert haben, sowie die Gruppe und der Name der Registrierungsressource.

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen von Integrität und Warnungen in Azure Stack](azure-stack-monitor-health.md)
- [Verwalten von Updates in Azure Stack](azure-stack-updates.md)
