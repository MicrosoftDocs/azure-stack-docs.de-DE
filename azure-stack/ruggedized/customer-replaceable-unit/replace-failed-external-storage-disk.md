---
title: Ersetzen eines fehlerhaften Datenlaufwerks
description: Hier erfahren Sie, wie Sie ein fehlerhaftes Datenlaufwerk ersetzen.
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: a5b7796794a0444a434fdb9a628ff04b93adcc03
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488104"
---
# <a name="replacing-a-failed-data-drive"></a>Ersetzen eines fehlerhaften Datenlaufwerks

Verwenden Sie die folgende Anleitung, um ein fehlerhaftes externes Datenlaufwerk auszutauschen.

## <a name="prerequisites"></a>Voraussetzungen

1.  Lesen Sie die *Hinweise und Warnungen* am Anfang dieses Leitfadens.

2.  Sehen Sie sich die Vorsichtsmaßnahmen bei der Handhabung an.

3.  Überprüfung

    -   Sie müssen über die erforderlichen Kenntnisse für das Arbeiten mit Skalierungseinheitknoten in einer Ruggedized-Cloudappliance verfügen, wenn Sie einen Skalierungseinheitknoten verwenden.

    -   Sie müssen über die erforderlichen Kenntnisse zum Arbeiten mit dem Hardwarelebenszyklushost bei Verwendung des Hardwarelebenszyklushosts verfügen.

4.  Abgeschlossen

    -   Sie müssen den Schritt „Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten“ abgeschlossen haben, wenn Sie einen Skalierungseinheitknoten verwenden.

    -   Sie müssen die Überprüfung des Zugriffs und der Integrität für den Hardwarelebenszyklushost bei Verwendung des Hardwarelebenszyklushosts abgeschlossen haben.

5.  Abgeschlossen

    -   Sie müssen den Schritt „Ausschalten von Skalierungseinheitknoten“ abgeschlossen haben, wenn Sie einen Skalierungseinheitknoten verwenden.

    -   Ausschalten des Hardwarelebenszyklushosts bei Verwendung des Hardwarelebenszyklushosts

## <a name="steps"></a>Schritte

1.  Suchen Sie nach dem physischen Knoten im Rack.

2.  Überprüfen Sie das Datenträgermodell.

    Vergewissern Sie sich, dass das neue Datenträgermodell in der [Supportmatrix](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D) als unterstützt aufgeführt ist.
    Wenn sich der Datenträger nicht in der Unterstützungsmatrix befindet, müssen Sie einen anderen Austausch anfordern.
    
    > [!CAUTION]
    > Wenn Sie einen Datenträger einlegen, der sich nicht in der Unterstützungsmatrix befindet, führt dies dazu, dass der neue Datenträger unter Quarantäne gestellt wird.
        
    Suchen Sie zur Überprüfung des Modells die Bezeichnung, und überprüfen Sie, ob sich die Komponente in der Unterstützungsmatrix befindet.
    
3.  Tauschen Sie das fehlerhafte Datenlaufwerk aus.

    Befolgen Sie die Anleitung zum [Austauschen des Datenlaufwerkträgers](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) für Skalierungseinheitknoten oder Hardwarelebenszyklushosts.
    
    Sehen Sie sich auch die relevanten Abschnitte zum Entfernen eines Laufwerks aus dem Träger und das Installieren eines Laufwerks im Träger oder für einen Laufwerkadapter an.
    
4.  Überprüfen Sie nach dem Austausch des Datenträgers anhand der Unterstützungsmatrix, ob die Firmware unterstützt wird. Führen Sie Folgendes aus, um die Firmwareversion des Datenträgers abzurufen:

    1.  Melden Sie sich entweder mithilfe der routingfähigen IP-Adresse oder direkt durch Zugriff auf die iDRAC-Schnittstelle über eine direkte USB-Verbindung bei iDRAC an.

    1.  Klicken Sie im oberen Menü auf **Storage** (Speicher):

        ![Screenshot der iDRAC-Seite mit ausgewählter Option „Storage“ (Speicher)](media/image-30.png)
    
    1.  Suchen Sie nach dem ersetzten Datenträger, und erweitern Sie das Element. Vergleichen Sie die **Revision** des Datenträgers mit der Revision in der Unterstützungsmatrix. Wenn sich der Datenträger nicht in der Unterstützungsmatrix befindet, fordern Sie Unterstützung an, um das Problem zu lösen.

        ![Screenshot der iDRAC-Seite „Advanced Properties“ (Erweiterte Eigenschaften) mit hervorgehobenen Optionen „Manufacturer“ (Hersteller), „Product ID“ (Produkt-ID) und „Revision“](media/image-31.png)
        
## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit einem Skalierungseinheitknoten arbeiten:

1.  Schließen Sie den Schritt „Überprüfen der Integrität des Skalierungseinheitknotens“ ab.

2.  Schließen Sie den Schritt „Überprüfen der Datenträgerintegrität des Skalierungseinheitknotens“ ab. Wenn Sie mit dem Hardwarelebenszyklushost arbeiten:

    -   Schließen Sie den Schritt „Überprüfung der Integrität des Hardwarelebenszyklushosts“ ab.
    
