---
title: 'Stretchingcluster: Übersicht'
description: Erfahren Sie mehr über Stretchingcluster
author: v-dasis
ms.topic: how-to
ms.date: 11/05/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e9651111e82b3449823ec7d13135154e7f0a447e
ms.sourcegitcommit: 96bc36a203954622be411fdb038d601e49f97d4e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441139"
---
# <a name="stretched-clusters-overview"></a>Stretchingcluster: Übersicht

> Gilt für Azure Stack HCI, Version v20H2

Eine Azure Stack HCI-Stretchingclusterlösung für die Notfallwiederherstellung bietet automatisches Failover, um die Produktion schnell und ohne die Notwendigkeit manueller Eingriffe wiederherzustellen. Speicherreplikation bietet die standortübergreifende Replikation von Volumes zur Notfallwiederherstellung, wobei alle Server synchron bleiben.

Speicherreplikation unterstützt sowohl synchrone als auch asynchrone Replikation:

- Bei der synchronen Replikation werden Daten von absturzkonsistenten Volumes standortübergreifend in einem Netzwerk mit geringer Latenz gespiegelt, um während eines Ausfalls Datenverlust auf der Dateisystemebene auszuschließen.
- Die asynchrone Replikation spiegelt Daten zwischen Standorten über regionale Bereiche über Netzwerklinks mit höherer Latenzen wider, jedoch ohne die Garantie, dass beide Standorte über identische Kopien der Daten zur Zeit eines Ausfalls verfügen.

Es gibt zwei Arten von Stretchingclustern, aktiv-passiv und aktiv-aktiv. Sie können eine aktiv-passive Standortreplikation einrichten, bei der es einen bevorzugten Standort und eine Replikationsrichtung gibt. Die aktiv-aktive Replikation kann bidirektional von beiden Standorten aus erfolgen. In diesem Artikel wird nur die aktiv/passive Konfiguration behandelt.

Einfach ausgedrückt ist ein *aktiver* Standort einer, der über Ressourcen und Workloads verfügt, mit denen Clients Verbindungen herstellen können. Ein *passiver* Standort stellt keine Rollen oder Workloads für Clients bereit und wartet für die Notfallwiederherstellung auf ein Failover vom aktiven Standort.

Die Standorte können in zwei verschiedenen Staaten, verschiedenen Ländern, auf verschiedenen Etagen oder in verschiedenen Räumen liegen. Stretchingcluster mit zwei Standorten bieten Notfallwiederherstellung und Geschäftskontinuität, sollte bei einem Standort ein Ausfall oder Fehler auftreten.

Nehmen Sie sich einige Minuten Zeit, um sich das Video zum Verwenden von Stretched Clustern mit Azure Stack HCI anzusehen:
> [!VIDEO https://www.youtube.com/embed/rYnZL1wMiqU]

## <a name="active-passive-stretched-cluster"></a>Aktiv-passiver Stretchingcluster

Im folgenden Diagramm ist Standort 1 als der aktive Standort mit Replikation zu Standort 2 dargestellt, also einer unidirektionalen Replikation.

:::image type="content" source="media/stretched-cluster/active-passive-stretched-cluster.png" alt-text="Szenario mit einem aktiv-passiven Stretchingcluster"  lightbox="media/stretched-cluster/active-passive-stretched-cluster.png":::

## <a name="active-active-stretched-cluster"></a>Aktiv-aktiver Stretchingcluster

Im folgenden Diagramm sind sowohl Standort 1 als auch Standort 2 als aktive Standorte mit bidirektionaler Replikation zum anderen Standort dargestellt.

:::image type="content" source="media/stretched-cluster/active-active-stretched-cluster.png" alt-text="Szenario mit einem aktiv-aktiven Stretchingcluster" lightbox="media/stretched-cluster/active-active-stretched-cluster.png":::

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Hardware- und sonstigen Anforderungen für Stretchingcluster. Weitere Informationen finden Sie unter [Systemanforderungen](system-requirements.md).
- Erfahren Sie, wie ein Stretchingcluster mithilfe von Windows Admin Center bereitgestellt wird. Mehr dazu finden Sie unter [Erstellen eines Clusters mithilfe von Windows Admin Center](../deploy/create-cluster.md).
- Erfahren Sie, wie Sie einen Stretchingcluster mithilfe von PowerShell bereitstellen. Mehr dazu finden Sie unter [Erstellen eines Clusters mithilfe von PowerShell](../deploy/create-cluster-powershell.md).
- Erfahren Sie, wie Volumes erstellt werden und Replikation für Stretchingcluster eingerichtet wird. Mehr dazu finden Sie unter [Erstellen von Volumes und Einrichten von Replikation für Stretchingcluster](../manage/create-stretched-volumes.md).
