---
title: Austauschen eines Lüfters
description: Hier erfahren Sie, wie Sie einen Lüfter austauschen.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 418501e5ee6e2f14536ace5ea61f172d2736a31a
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874505"
---
# <a name="replacing-a-cooling-fan"></a>Austauschen eines Lüfters

Verwenden Sie das folgende Verfahren zum Ersetzen von einem oder mehreren Lüftern.

## <a name="prerequisites"></a>Voraussetzungen

1.  Lesen Sie die *Hinweise und Warnungen* am Anfang dieses Leitfadens.

2.  Sehen Sie sich die Vorsichtsmaßnahmen bei der Handhabung an.

3.  Überprüfung

    -   Sie müssen über die erforderlichen Kenntnisse zum Arbeiten mit Skalierungseinheitknoten in einer Tactical Cloud Appliance verfügen.

    -   Sie müssen über die erforderlichen Kenntnisse auf Seite 5 zum Arbeiten mit dem Hardwarelebenszyklushost bei Verwendung des Hardwarelebenszyklushosts verfügen.

4.  Abgeschlossen

    -   Sie müssen die Überprüfung des Zugriffs und der Integrität des Skalierungseinheitknotens bei Verwendung eines Skalierungseinheitknotens abgeschlossen haben.

    -   Sie müssen die Überprüfung des Zugriffs und der Integrität für den Hardwarelebenszyklushost bei Verwendung des Hardwarelebenszyklushosts abgeschlossen haben.

5.  Abgeschlossen

    -   Ausschalten der Skalierungseinheitknoten bei Verwendung eines Skalierungseinheitknotens

    -   Ausschalten des Hardwarelebenszyklushosts bei Verwendung des Hardwarelebenszyklushosts

## <a name="steps"></a>Schritte

1.  Suchen Sie nach dem physischen Knoten im Rack.

2.  Überprüfen Sie, ob der Knoten ausgeschaltet ist. Die Stromversorgungs-LED sollte orange leuchten.

    > [!CAUTION]
    > Stellen Sie vor dem Trennen der Kabel für den Server, auf dem Sie arbeiten, sicher, dass jedes Kabel ordnungsgemäß beschriftet ist. Die Kabel MÜSSEN wieder mit den ursprünglichen Anschlüssen verbunden werden.
    
3.  Tauschen Sie den oder die Lüfter aus.

    Befolgen Sie die Schritte zum Austauschen des Lüfters im [Dell EMC PowerEdge R640 Installations- und Service-Handbuch](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) für Skalierungseinheitknoten oder Hardwarelebenszyklushosts.
    
4.  Schalten Sie den Knoten ein.

    Wenn der Server nach dem Wiedereinstecken des Stromkabels nicht automatisch neu gestartet wird, drücken Sie auf den Netzschalter, um den Knoten wieder einzuschalten.
    
## <a name="next-steps"></a>Nächste Schritte

Gehen Sie bei der Arbeit mit einem Skalierungseinheitknoten wie folgt vor:

1.  Schließen Sie den Schritt „Einschalten eines Skalierungseinheitknotens“ ab.

2.  Schließen Sie den Schritt „Überprüfen der Integrität des Skalierungseinheitknotens“ ab. Gehen Sie bei der Arbeit mit dem Hardwarelebenszyklushost wie folgt vor:

    -   Schließen Sie den Schritt „Überprüfung der Integrität des Hardwarelebenszyklushosts“ ab.
    
