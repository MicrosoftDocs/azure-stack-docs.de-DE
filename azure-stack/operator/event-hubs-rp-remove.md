---
title: Entfernen von Event Hubs in Azure Stack Hub
description: Erfahren Sie, wie Sie den Event Hubs-Ressourcenanbieter in Azure Stack Hub entfernen.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a1525f4b91998479d368714aa3a88df6ecfcef9
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "80424596"
---
# <a name="how-to-remove-event-hubs-on-azure-stack-hub"></a>Entfernen von Event Hubs in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

> [!WARNING]
> Wenn Sie Event Hubs deinstallieren, werden der Ressourcenanbieter und alle von Benutzern erstellten Event Hubs-Cluster, -Namespaces und Event Hubs-Ressourcen entfernt (gelöscht). Außerdem werden deren zugehörige Ereignisdaten entfernt.  
> Gehen Sie mit äußerster Vorsicht vor, bevor Sie sich entscheiden, Event Hubs zu deinstallieren. Bei einem Deinstallieren von Event Hubs werden die Pakete, die zum Installieren von Event Hubs verwendet werden, **nicht** gelöscht. Informationen, wie auch diese Pakete gelöscht werden, finden Sie unter [Löschen von Event Hubs-Paketen](#delete-event-hubs-packages).

## <a name="uninstall-event-hubs"></a>Deinstallieren von Event Hubs

Mit diesen Schritten werden alle Event Hubs-Ressourcen, einschließlich Cluster, Namespaces, Event Hubs und des Ressourcenanbieters, entfernt.

Um Event Hubs und alle zugehörigen Ressourcen zu entfernen, die von Benutzern erstellt wurden, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
2. Wählen Sie auf der linken Seite die Option **Marketplace-Verwaltung** aus.
3. Wählen Sie **Ressourcenanbieter** aus.
4. Wählen Sie **Event Hubs** in der Liste der Ressourcenanbieter aus. Sie können die Liste filtern, indem Sie „Event Hubs“ in das bereitgestellte Suchtextfeld eingeben.

   [![Event Hubs entfernen 1](media/event-hubs-rp-remove/1-uninstall.png)](media/event-hubs-rp-remove/1-uninstall.png#lightbox)

5. Wählen Sie **Deinstallieren** in den Optionen aus, die oben auf der Seite angezeigt werden.

   [![Event Hubs entfernen 2](media/event-hubs-rp-remove/2-uninstall.png)](media/event-hubs-rp-remove/2-uninstall.png#lightbox)

6. Geben Sie den Namen des Ressourcenanbieters ein, und wählen Sie anschließend **Deinstallieren** aus. Mit dieser Aktion bestätigen Sie, dass Sie Folgendes deinstallieren möchten:
   - Den Event Hubs-Ressourcenanbieter
   - Alle vom Benutzer erstellten Cluster, Namespaces, Event Hubs und Ereignisdaten

   [![Event Hubs entfernen 3](media/event-hubs-rp-remove/3-uninstall.png)](media/event-hubs-rp-remove/3-uninstall.png#lightbox)

   [![Event Hubs entfernen 4](media/event-hubs-rp-remove/4-uninstall.png)](media/event-hubs-rp-remove/4-uninstall.png#lightbox)

   > [!IMPORTANT]
   > Nachdem Event Hubs erfolgreich entfernt wurde, müssen Sie mindestens 10 Minuten warten, bevor Sie Event Hubs erneut installieren. Dies liegt darin begründet, dass die Bereinigungsaktivitäten möglicherweise weiterhin ausgeführt werden, was zu einem Konflikt mit jeder neuen Installation führen kann.

## <a name="delete-event-hubs-packages"></a>Löschen von Event Hubs-Paketen

Verwenden Sie diese Option, wenn Sie nach dem Deinstallieren von Event Hubs auch alle Pakete entfernen möchten, die zum Installieren von Event Hubs verwendet wurden. 

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Event Hubs erneut installieren möchten, wechseln Sie wieder zum Artikel [Installieren von Event Hubs in Azure Stack Hub](event-hubs-rp-install.md).