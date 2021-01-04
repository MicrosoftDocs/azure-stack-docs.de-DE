---
title: Ausschalten von Skalierungseinheitknoten
description: Hier erfahren Sie, wie Sie Skalierungseinheitknoten ausschalten.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: fde7496a54f0c2d3b28d98584295a761c683e17c
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392723"
---
# <a name="powering-off-scale-unit-nodes"></a>Ausschalten von Skalierungseinheitknoten

Bevor Sie einen Skalierungseinheitknoten ausschalten können, müssen Sie die Integrität der Skalierungseinheit überprüfen und den Knoten ermitteln, der repariert werden muss.

Wenn der **Energiestatus** nicht **Beendet** lautet, verwenden Sie die folgenden Schritte, um den Knoten sicher herunterzufahren.

**Schritte**

1.  Entladen Sie den Skalierungseinheitknoten.

    1.  Wählen Sie im Verwaltungsportal den Knoten aus, der repariert werden muss, und klicken Sie dann auf **Drain** (Entladen).

        ![](media/image-23.png)
        
    1.  Wenn Sie dazu aufgefordert werden, geben Sie den Namen des zu entladenden Knotens ein, und klicken Sie auf **Ja**.

        ![](media/image-24.png)
    
    1.  Es wird eine Benachrichtigung angezeigt, die besagt, dass der Entladevorgang läuft.
    
        ![](media/image-25.png)
        
    1.  Melden Sie sich bei der iDRAC-Schnittstelle an, und überprüfen Sie das Diensttag des Knotens im Bereich **System Information** (Systeminformationen).
    

2.  Fahren Sie den Skalierungseinheitknoten herunter.

    1.  Wenn der Entladevorgang abgeschlossen ist, wählen Sie noch mal den Knoten aus, stellen Sie sicher, dass der **Energiestatus** **Wartung** lautet, und klicken Sie auf **Herunterfahren**.

        ![](media/image-26.png)
        
    1.  Wählen Sie bei entsprechender Aufforderung **Ja** aus, um das Herunterfahren zu bestätigen.
    
        ![](media/image-27.png)
        
    1.  Es wird eine Benachrichtigung angezeigt, die besagt, dass der Schritt zum Herunterfahren ausgeführt wird.

        ![](media/image-28.png)
    
    1.  Nach dem Herunterfahren lautet der **Energiestatus** **Beendet**.
    
        ![](media/image-29.png)
        