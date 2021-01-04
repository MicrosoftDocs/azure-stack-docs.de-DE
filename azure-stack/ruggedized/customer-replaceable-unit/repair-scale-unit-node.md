---
title: Einschalten und Reparieren eines Skalierungseinheitknotens
description: Hier erfahren Sie, wie Sie einen Skalierungseinheitknoten einschalten und reparieren.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 255a40bc2439ae1a6995d4ddf89df192d5e57551
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97393019"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Einschalten und Reparieren eines Skalierungseinheitknotens

**Schritte**

Sie müssen den Azure Stack Hub-Reparaturvorgang ausführen, um einen Skalierungseinheitknoten zu reparieren und wieder in die Produktionsumgebung zu verschieben.

> [!NOTE]
> Der Reparaturvorgang dauert ungefähr drei Stunden.

1.  Wählen Sie im **Verwaltungsportal** den Knoten aus, und klicken Sie auf **Reparieren**.

    ![](media/image-52.png)

1.  Geben Sie die **BMC-IP-Adresse** an, die dem zu reparierenden Knoten entspricht, und klicken Sie auf **Reparieren**.

    ![](media/image-53.png)

1.  Überwachen Sie den Fortschritt im Benachrichtigungsbereich:

    ![](media/image-54.png)
    
    
    > [!NOTE]
    > Wenn der Reparaturvorgang nicht innerhalb von drei Stunden abgeschlossen wird, öffnen Sie einen Fall beim Dell Technologies-Support, der für die weitere Problembehandlung den Microsoft-Support einbindet.
    
    Wenn der Reparaturvorgang abgeschlossen ist, weist der Knoten wieder den **Status „Wird ausgeführt“** auf.
    
