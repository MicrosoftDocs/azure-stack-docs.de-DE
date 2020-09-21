---
title: Aktivieren der Volumeverschlüsselung, Deduplizierung und Komprimierung – Azure Stack HCI
description: In diesem Thema wird beschrieben, wie Sie die Volumeverschlüsselung, Deduplizierung und Komprimierung in Azure Stack HCI mithilfe von Windows Admin Center einsetzen.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 09/03/2020
ms.openlocfilehash: 9599a4d24d93e545c964de91ad10b905c79b42a1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573495"
---
# <a name="enable-volume-encryption-deduplication-and-compression-in-azure-stack-hci"></a>Aktivieren der Volumeverschlüsselung, Deduplizierung und Komprimierung in Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

In diesem Thema wird behandelt, wie die Verschlüsselung mit BitLocker auf Volumes in Azure Stack HCI über Windows Admin Center aktiviert werden kann. Außerdem wird erläutert, wie Deduplizierung und Komprimierung auf Volumes aktiviert werden. Informationen zum Erstellen von Volumes finden Sie unter [Erstellen von Volumes](create-volumes.md).

## <a name="turn-on-bitlocker-to-protect-volumes"></a>Aktivieren von BitLocker zum Schützen von Volumes
So schalten Sie BitLocker im Windows Admin Center ein

1. Stellen Sie eine Verbindung mit einem Cluster direkter Speicherplätze her, und wählen Sie dann im Bereich **Extras** **Volumes** aus.
1. Wählen Sie auf der Seite **Volumes** die Registerkarte **Inventar** aus. Schalten Sie dann unter **Optionale Features** die Option **Verschlüsselung (BitLocker)** ein.

    :::image type="content" source="media/volume-encryption-deduplication/bitlocker-toggle-switch.png" alt-text="Der Umschalter zur Aktivierung von BitLocker":::

1. Wählen Sie im Popupmenü **Verschlüsselung (BitLocker)** **Start** aus, und geben Sie dann auf der Seite **Turn on Encryption** (Verschlüsselung aktivieren) Ihre Anmeldeinformationen ein, um den Workflow abzuschließen.

>[!NOTE]
   > Wenn die Popupmeldung **Install BitLocker feature first** (Installieren Sie zunächst das BitLocker-Feature) angezeigt wird, befolgen Sie die Anweisungen zum Installieren des Features auf jedem Server im Cluster, und starten Sie Ihre Server dann erneut.

## <a name="turn-on-deduplication-and-compression"></a>Aktivieren der Deduplizierung und Komprimierung
Deduplizierung und Komprimierung werden volumebezogen verwaltet. Für Deduplizierung und Komprimierung wird ein Nachbearbeitungsmodell verwendet. Dies bedeutet, dass Einsparungen erst nach Ausführung erkennbar sind. Bei der Ausführung werden alle Dateien verarbeitet, also auch bereits vorhandene Dateien.

So aktivieren Sie in Windows Admin Center Deduplizierung und Komprimierung auf einem Volume

1. Stellen Sie eine Verbindung mit einem Cluster direkter Speicherplätze her, und wählen Sie dann im Bereich **Extras** **Volumes** aus.
1. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** aus.
1. Wählen Sie in der Liste mit den Volumes den Namen des Volumes aus, das Sie verwalten möchten.
1. Schaten Sie auf der Seite „Volumedetails“ **Deduplizierung und Komprimierung** ein.
1. Wählen Sie im Bereich **Deduplizierung aktivieren** den Deduplizierungsmodus aus.

    Anstatt komplizierte Einstellungen vornehmen zu müssen, können Sie mit Windows Admin Center zwischen fertigen Profilen für unterschiedliche Workloads wählen. Verwenden Sie die Standardeinstellung, falls Sie unsicher sind.

1. Wählen Sie **Deduplizierung aktivieren** aus.

Sehen Sie sich ein kurzes Video dazu an, wie Sie die Deduplizierung und Komprimierung aktivieren. Die Verschlüsselung wird im Video nicht gezeigt.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

Das Aktivieren der Verschlüsselung von Volumes hat eine geringe Auswirkung auf deren Leistung (typischerweise unter 10 %), wobei die Auswirkungen je nach Hardware und Workload variieren. Datendeduplizierung und -komprimierung wirken sich auch auf die Leistung aus. Weitere Informationen finden Sie unter [Bestimmen, welche Workloads für die Deduplizierung in Frage kommen](/windows-server/storage/data-deduplication/install-enable#enable-dedup-candidate-workloads).

<!---Add info on greyed out ReFS option? --->

Sie haben es geschafft! Wiederholen Sie diese Schritte, um die Daten in Ihren Volumes zu schützen.

## <a name="next-steps"></a>Nächste Schritte
Verwandte Themen und andere Speicherverwaltungsaufgaben finden Sie unter:

- [Direkte Speicherplätze – Übersicht](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Planen von Volumes](../concepts/plan-volumes.md)
- [Erweitern von Volumes](extend-volumes.md)
- [Löschen von Volumes](delete-volumes.md)