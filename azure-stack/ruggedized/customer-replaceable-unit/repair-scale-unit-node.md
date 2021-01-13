---
title: Einschalten und Reparieren eines Skalierungseinheitknotens
description: Hier erfahren Sie, wie Sie einen Skalierungseinheitknoten einschalten und reparieren.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d05c696f62aad4d9305137edab777d69b6442814
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909921"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Einschalten und Reparieren eines Skalierungseinheitknotens

**Schritte**

Sie müssen den Azure Stack Hub-Reparaturvorgang ausführen, um einen Skalierungseinheitknoten zu reparieren und wieder in die Produktionsumgebung zu verschieben.

> [!NOTE]
> Der Reparaturvorgang dauert ungefähr drei Stunden.

1.  Wählen Sie im **Verwaltungsportal** den Knoten aus, und klicken Sie auf **Reparieren**.

    ![Screenshot der Seite „Verwaltung – Knoten“, auf der ein Knoten und die Aktion „Reparieren“ ausgewählt sind](media/image-52.png)

1.  Geben Sie die **BMC-IP-Adresse** an, die dem zu reparierenden Knoten entspricht, und klicken Sie auf **Reparieren**.

    ![Screenshot der Seite „Verwaltung – Knoten“, auf der ein Knoten ausgewählt ist, die IP-Adresse hervorgehoben ist und das Dialogfeld „Repair node“ (Knoten reparieren) angezeigt wird](media/image-53.png)

1.  Überwachen Sie den Fortschritt im Benachrichtigungsbereich:

    ![Screenshot des Bereichs „Benachrichtigungen“, in dem angezeigt wird, dass das Reparieren des Knotens derzeit ausgeführt wird](media/image-54.png)
    
    
    > [!NOTE]
    > Wenn der Reparaturvorgang nicht innerhalb von drei Stunden abgeschlossen wird, öffnen Sie einen Fall beim Dell Technologies-Support, der für die weitere Problembehandlung den Microsoft-Support einbindet.
    
    Wenn der Reparaturvorgang abgeschlossen ist, weist der Knoten wieder den **Status „Wird ausgeführt“** auf.
    
