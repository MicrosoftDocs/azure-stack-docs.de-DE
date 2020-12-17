---
title: Regionsverwaltung in Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Übersicht über die Regionsverwaltung in Azure Stack Hub
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 86e449c9a825c80fa230d94948281c67e4756a25
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577022"
---
# <a name="region-management-in-azure-stack-hub"></a>Regionsverwaltung in Azure Stack Hub

Azure Stack Hub verwendet *Regionen*. Dabei handelt es sich um logische Entitäten, die aus den Hardwareressourcen bestehen, aus denen sich wiederum die Azure Stack Hub-Infrastruktur zusammensetzt. In der Regionsverwaltung finden Sie alle Ressourcen, die für den erfolgreichen Betrieb der Azure Stack Hub-Infrastruktur erforderlich sind.

Eine integrierte (als *Azure Stack Hub-Cloud* bezeichnete) Systembereitstellung bildet eine einzelne Region. Jedes Azure Stack Development Kit (ASDK) hat eine einzelne als **lokal** benannte Region. Wenn Sie ein zweites integriertes Azure Stack Hub-System bereitstellen oder eine andere Instanz des ASDK auf separater Hardware einrichten, ist diese Azure Stack Hub-Cloud eine andere Region.

## <a name="information-available-through-the-region-management-tile"></a>Informationen, die über die Kachel „Regionsverwaltung“ verfügbar sind

Azure Stack Hub bietet einige Funktionen der Regionsverwaltung, die auf der Kachel **Regionsverwaltung** verfügbar sind. Diese Kachel steht Azure Stack Hub-Operatoren im Administratorportal im Standarddashboard zur Verfügung. Auf diesem Bildschirm können Sie Ihre Azure Stack Hub-Region und ihre regionsspezifischen Komponenten überwachen und aktualisieren.

![Die Kachel „Regionsverwaltung“ im Azure Stack Hub-Administratorportal](media/azure-stack-region-management/image1.png)

Wenn Sie auf der Kachel **Regionsverwaltung** eine Region auswählen, können Sie auf folgende Informationen zugreifen:

[![Beschreibung der Bereiche auf dem Blatt „Regionsverwaltung“ im Azure Stack Hub-Administratorportal](media/azure-stack-region-management/regionssm.png "Blatt „Regionsverwaltung“ im Azure Stack Hub-Administratorportal")](media/azure-stack-region-management/regions.png#lightbox)

1. **Das Ressourcenmenü**: Hier können Sie auf verschiedene Infrastrukturverwaltungsbereiche zugreifen und Benutzerressourcen anzeigen und verwalten. Zu solchen Ressourcen zählen etwa Speicherkonten und virtuelle Netzwerke.

2. **Warnungen**: Hier werden systemweite Warnungen sowie ausführlichere Informationen zu diesen Warnungen aufgelistet.

3. **Updates**: Hier werden die aktuelle Version Ihrer Azure Stack Hub-Infrastruktur, verfügbare Updates und der Updateverlauf angezeigt. Sie können auch Ihr integriertes System aktualisieren.

4. **Ressourcenanbieter**: Hier können Sie die Benutzerfunktionen der Komponenten verwalten, die zur Ausführung von Azure Stack Hub erforderlich sind. Jeder Ressourcenanbieter hat eine eigene Administratorerfahrung. Diese Erfahrung beinhaltet Warnungen für spezifische Anbieter, Metriken und andere Verwaltungsfunktionen, die ressourcenbetreiberspezifisch sind.

5. **Infrastrukturrollen**: Die Komponenten, die zur Ausführung von Azure Stack Hub erforderlich sind. Es werden nur die Infrastrukturrollen aufgelistet, die Warnungen ausgeben. Wenn Sie eine Rolle auswählen, können Sie die Warnungen dieser Rolle anzeigen sowie die Rolleninstanzen, auf denen die Rolle ausgeführt wird.

6. **Properties:** Der Registrierungsstatus und die Details Ihrer Umgebung auf dem Blatt „Regionsverwaltung“. Der Status kann **Registriert**, **Nicht registriert** oder **Abgelaufen** sein. Beim Status „registriert“ wird außerdem die ID des Azure-Abonnements angezeigt, mit dem Sie Ihre Azure Stack Hub-Instanz registriert haben, sowie die Gruppe und der Name der Registrierungsressource.

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen von Integrität und Warnungen in Azure Stack Hub](azure-stack-monitor-health.md)
- [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md)
