---
title: Einschalten und Reparieren eines Skalierungseinheitknotens
description: Hier erfahren Sie, wie Sie einen Skalierungseinheitknoten einschalten und reparieren.
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 0a2aa54234b5dccc8f1ce3425906425064463910
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488121"
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
    > Wenn der Reparaturvorgang nicht innerhalb von drei Stunden abgeschlossen wird, öffnen Sie einen Fall beim Microsoft-Support für die weitere Problembehandlung.
    
    Wenn der Reparaturvorgang abgeschlossen ist, weist der Knoten wieder den **Status „Wird ausgeführt“** auf.
    
