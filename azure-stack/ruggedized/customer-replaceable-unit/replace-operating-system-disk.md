---
title: Ersetzen eines Betriebssystemdatenträgers
description: Hier erfahren Sie mehr über das Ersetzen eines Betriebssystemdatenträgers.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 529a81a8c63e2fe72da4618e7f359832b2267ea3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392719"
---
# <a name="replacing-an-operating-system-disk"></a>Ersetzen eines Betriebssystemdatenträgers

Mithilfe der folgenden Aktionen können Sie einen fehlerhaften Betriebssystemdatenträger in einem Skalierungseinheitknoten ersetzen.

## <a name="prerequisites"></a>Voraussetzungen

1.  Lesen Sie die *Hinweise und Warnungen* am Anfang dieses Leitfadens.

2.  Sehen Sie sich die Vorsichtsmaßnahmen bei der Handhabung an.

3.  Sie müssen über die erforderlichen Kenntnisse für das Arbeiten mit Skalierungseinheitknoten in einer Tactical Cloud Appliance bei Verwendung eines Skalierungseinheitknotens verfügen.

4.  Schließen Sie den Schritt „Überprüfen des Zugriffs und der Integrität des Skalierungseinheitknotens“ ab.

5.  Schließen Sie den Schritt „Ausschalten von Skalierungseinheitknoten“ ab.

    Bei den Azure Stack Hub-Skalierungseinheitknoten wird das Betriebssystem von einem gespiegelten Paar von M.2-SSD-Modulen auf der Dell PowerEdge-BOSS-Karte (Boot Optimized Storage Solution) ausgeführt. Das System muss ausgeschaltet werden, um einen Betriebssystemdatenträger ersetzen zu können.
    
## <a name="steps"></a>Schritte

1.  Suchen Sie nach dem physischen Knoten im Rack.

2.  Überprüfen Sie, ob der Knoten ausgeschaltet ist. Die Stromversorgungs-LED sollte orange leuchten.

    > [!CAUTION]
    > Stellen Sie vor dem Trennen der Kabel für den Server, auf dem Sie arbeiten, sicher, dass jedes Kabel ordnungsgemäß beschriftet ist. Die Kabel MÜSSEN wieder mit den ursprünglichen Anschlüssen verbunden werden.
    
3.  Ersetzen Sie die M.2-SSD-Module, bei denen ein Fehler aufgetreten ist.

    Befolgen Sie die Schritte zum Austauschen des M.2-SSD-Moduls im [Dell EMC PowerEdge R640 Installations- und Service-Handbuch](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) für Skalierungseinheitknoten.
    
4.  Schalten Sie den Knoten ein.

    Wenn der Server nach dem Wiedereinstecken des Stromkabels nicht automatisch neu gestartet wird, drücken Sie auf den Netzschalter, um den Knoten wieder einzuschalten.
    
## <a name="next-steps"></a>Nächste Schritte

1.  Schließen Sie den Schritt „Einschalten und Reparieren eines Skalierungseinheitknotens“ ab.

2.  Schließen Sie den Schritt „Überprüfen der Integrität des Skalierungseinheitknotens“ ab.

