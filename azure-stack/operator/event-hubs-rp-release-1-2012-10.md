---
title: Versionshinweise zu Event Hubs in Azure Stack Hub 1.2012.1.0
description: Hier erfahren Sie mehr über das 1.2012.1.0-Release für Event Hubs in Azure Stack Hub, einschließlich Fehlerbehebungen, Features und Informationen zur Installation des Updates.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2021
ms.reviewer: kalkeea
ms.lastreviewed: 02/15/2021
ms.openlocfilehash: ef28447b87c0647b65c996112c5739bf7c6caccf
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563528"
---
# <a name="event-hubs-on-azure-stack-hub-1201210-release-notes"></a>Versionshinweise zu Event Hubs in Azure Stack Hub 1.2012.1.0

In diesen Versionshinweisen werden Verbesserungen und Korrekturen in Bezug auf Event Hubs in Azure Stack Hub für die Version 1.2012.1.0 sowie alle bekannten Probleme beschrieben. 

[!INCLUDE [Azure Stack Hub update reminder](../includes/event-hubs-hub-update-banner.md)]

## <a name="updates-in-this-release"></a>Updates in diesem Release

Diese Version enthält die folgenden Updates:

- Für SDK-Entwickler, die das Azure-Portal nutzen, wird die Portalversion 5.0.303.7361 jetzt unterstützt.
- Die internen Protokollierung für Event Hubs-Cluster wurde verbessert.

## <a name="issues-fixed-in-this-release"></a>In diesem Release behobene Probleme

Diese Version enthält die folgenden Korrekturen:

- Eine Korrektur der Upgradereihenfolge für Event Hubs-Cluster wurde hinzugefügt, um ein Upgradeproblem zu beheben.
- Die Integritätsprüfung für Cluster und Sicherung für Event Hubs-Cluster wurde nicht ausgeführt, wenn Cluster einen der Status „Upgrade wird durchgeführt“ oder „Fehler beim Update“ aufwiesen. Dieses Problem wurde in diesem Release behoben.
- Es wurde ein Fehler behoben, der dazu führt, dass Nutzungsdaten eine falsche Menge enthalten. Anstelle von Kernen wurden Kapazitätseinheiten (CUs) ausgegeben. Bisher wurde für einen Cluster mit einer CU eine stündliche Nutzung von einem Kern ausgegeben. Für Benutzer wird nun die korrekte Menge von zehn Kernen für einen Cluster mit einer CU als stündliche Nutzung angezeigt.

## <a name="known-issues"></a>Bekannte Probleme 

Es sind keine Probleme für dieses Release bekannt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Übersicht über den Operator für Event Hubs in Azure Stack Hub](event-hubs-rp-overview.md).

