---
title: Ausschalten von Skalierungseinheitknoten
description: Hier erfahren Sie, wie Sie Skalierungseinheitknoten ausschalten.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 2eb2c85bcdb350173923b3d8b5a3b463ce6a16ff
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910719"
---
# <a name="powering-off-scale-unit-nodes"></a>Ausschalten von Skalierungseinheitknoten

Bevor Sie einen Skalierungseinheitknoten ausschalten können, müssen Sie die Integrität der Skalierungseinheit überprüfen und den Knoten ermitteln, der repariert werden muss.

Wenn der **Energiestatus** nicht **Beendet** lautet, verwenden Sie die folgenden Schritte, um den Knoten sicher herunterzufahren.

**Schritte**

1.  Entladen Sie den Skalierungseinheitknoten.

    1.  Wählen Sie im Verwaltungsportal den Knoten aus, der repariert werden muss, und klicken Sie dann auf **Drain** (Entladen).

        ![Screenshot: Seite „Verwaltung“ mit ausgewählter Aktion „Drain“ (Entladen) und einem hervorgehobenen Knoten](media/image-23.png)
        
    1.  Wenn Sie dazu aufgefordert werden, geben Sie den Namen des zu entladenden Knotens ein, und klicken Sie auf **Ja**.

        ![Screenshot der Seite „Verwaltung – Knoten“](media/image-24.png)
    
    1.  Es wird eine Benachrichtigung angezeigt, die besagt, dass der Entladevorgang läuft.
    
        ![Screenshot der Seite „Verwaltung – Knoten“, auf der die Benachrichtigung über das Entladen angezeigt wird](media/image-25.png)
        
    1.  Melden Sie sich bei der iDRAC-Schnittstelle an, und überprüfen Sie das Diensttag des Knotens im Bereich **System Information** (Systeminformationen).
    

2.  Fahren Sie den Skalierungseinheitknoten herunter.

    1.  Wenn der Entladevorgang abgeschlossen ist, wählen Sie noch mal den Knoten aus, stellen Sie sicher, dass der **Energiestatus** **Wartung** lautet, und klicken Sie auf **Herunterfahren**.

        ![Screenshot der Seite „Verwaltung – Knoten“, auf der ein Knoten und die Aktion „Herunterfahren“ ausgewählt sind](media/image-26.png)
        
    1.  Wählen Sie bei entsprechender Aufforderung **Ja** aus, um das Herunterfahren zu bestätigen.
    
        ![Screenshot der Seite „Verwaltung – Knoten“, auf der das Dialogfeld „Shutdown node“ (Knoten herunterfahren) angezeigt wird](media/image-27.png)
        
    1.  Es wird eine Benachrichtigung angezeigt, die besagt, dass der Schritt zum Herunterfahren ausgeführt wird.

        ![Screenshot der Seite „Verwaltung – Knoten“ mit dem Dialogfeld, das angibt, dass der Knoten heruntergefahren wird](media/image-28.png)
    
    1.  Nach dem Herunterfahren lautet der **Energiestatus** **Beendet**.
    
        ![Screenshot der Seite „Verwaltung – Knoten“, auf der ein Knoten ausgewählt ist und für „Energiestatus“ der Status „Beendet“ hervorgehoben ist](media/image-29.png)
        