---
title: Austauschen fehlerhafter externer Speicherdatenträger
description: Hier erhalten Sie Informationen zum Austauschen fehlerhafter externer Speicherdatenträger.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 46320af2b4eeb6eddbca92a6749550eed05f6627
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392722"
---
# <a name="replacing-a-failed-external-storage-disk"></a>Austauschen fehlerhafter externer Speicherdatenträger

Verwenden Sie die folgende Prozedur, um einen fehlerhaften externen Datenträger auszutauschen.

## <a name="prerequisites"></a>Voraussetzungen

1.  Lesen Sie die *Hinweise und Warnungen* am Anfang dieses Leitfadens.

2.  Sehen Sie sich die Vorsichtsmaßnahmen bei der Handhabung an.

3.  Überprüfung

    -   Sie müssen über die erforderlichen Kenntnisse für das Arbeiten mit Skalierungseinheitknoten in einer Tactical Cloud Appliance bei Verwendung eines Skalierungseinheitknotens verfügen.

    -   Sie müssen über die erforderlichen Kenntnisse zum Arbeiten mit dem Hardwarelebenszyklushost bei Verwendung des Hardwarelebenszyklushosts verfügen.

4.  Abgeschlossen

    -   Sie müssen die Überprüfung des Zugriffs und der Integrität des Skalierungseinheitknotens bei Verwendung eines Skalierungseinheitknotens abgeschlossen haben.

    -   Sie müssen die Überprüfung des Zugriffs und der Integrität für den Hardwarelebenszyklushost bei Verwendung des Hardwarelebenszyklushosts abgeschlossen haben.

5.  Abgeschlossen

    -   Ausschalten der Skalierungseinheitknoten bei Verwendung eines Skalierungseinheitknotens

    -   Ausschalten des Hardwarelebenszyklushosts bei Verwendung des Hardwarelebenszyklushosts

## <a name="steps"></a>Schritte

1.  Suchen Sie nach dem physischen Knoten im Rack.

2.  Überprüfen Sie das Datenträgermodell.

    Überprüfen Sie, ob das Datenträgermodell als unterstütztes Modell in der
    [Unterstützungsmatrix](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D)* [Integrierte System für Microsoft Azure Stack Hub 14G](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D) aufgeführt ist.
    Wenn sich der Datenträger nicht in der Unterstützungsmatrix befindet, müssen Sie einen anderen Austausch anfordern.
    
    > [!CAUTION]
    > Wenn Sie einen Datenträger einlegen, der sich nicht in der Unterstützungsmatrix befindet, führt dies dazu, dass der neue Datenträger unter Quarantäne gestellt wird.
        
    Suchen Sie zur Überprüfung des Modells die Bezeichnung, und überprüfen Sie, ob sich die Komponente in der Unterstützungsmatrix befindet.
    
3.  Tauschen Sie den fehlerhaften externen Speicherdatenträger aus.

    Befolgen Sie den Austauschprozess für Datenträger im [Dell EMC PowerEdge R640 Installations- und Service-Handbuch](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) für Skalierungseinheitknoten oder Hardwarelebenszyklushosts.
    
    Sehen Sie sich auch die relevanten Abschnitte zum Entfernen eines Laufwerks aus dem Träger und das Installieren eines Laufwerks im Träger oder für einen Laufwerkadapter an.
    
4.  Überprüfen Sie nach dem Austausch des Datenträgers anhand der Unterstützungsmatrix, ob die Firmware unterstützt wird. Führen Sie Folgendes aus, um die Firmwareversion des Datenträgers abzurufen:

    1.  Melden Sie sich entweder mithilfe der routingfähigen IP-Adresse oder direkt durch Zugriff auf die iDRAC-Schnittstelle über eine direkte USB-Verbindung bei iDRAC an.

    1.  Klicken Sie im oberen Menü auf **Storage** (Speicher):

        ![](media/image-30.png)
    
    1.  Suchen Sie nach dem ersetzten Datenträger, und erweitern Sie das Element. Vergleichen Sie die **Revision** des Datenträgers mit der Revision in der Unterstützungsmatrix. Wenn sich der Datenträger nicht in der Unterstützungsmatrix befindet, fordern Sie Unterstützung an, um das Problem zu lösen.

        ![](media/image-31.png)
        
## <a name="next-steps"></a>Nächste Schritte

Gehen Sie bei der Arbeit mit einem Skalierungseinheitknoten wie folgt vor:

1.  Schließen Sie die Überprüfung der Integrität des Skalierungseinheitknotens ab.

2.  Schließen Sie die Überprüfung der Integrität des Datenträgers des Skalierungseinheitknotens ab. Wenn Sie mit dem Hardwarelebenszyklushost arbeiten, tun Sie Folgendes:

    -   Schließen Sie den Schritt „Überprüfung der Integrität des Hardwarelebenszyklushosts“ ab.
    
