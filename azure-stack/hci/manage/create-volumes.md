---
title: Erstellen von Volumes in Azure Stack HCI
description: Hier wird beschrieben, wie Sie Volumes in Azure Stack HCI mithilfe von Windows Admin Center und PowerShell erstellen.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: b195a55314935282f43f99bf02bda1d2bc298b54
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370439"
---
# <a name="creating-volumes-in-azure-stack-hci"></a>Erstellen von Volumes in Azure Stack HCI

> Gilt für: Windows Server 2019

In diesem Thema wird beschrieben, wie Sie mit Windows Admin Center und Windows PowerShell Volumes in einem „Direkte Speicherplätze“-Cluster erstellen, auf den Volumes mit Dateien arbeiten und dafür die Datendeduplizierung und -komprimierung aktivieren.

## <a name="create-a-three-way-mirror-volume"></a>Erstellen eines Volumes für Drei-Wege-Spiegelung

Erstellen Sie wie folgt mit Windows Admin Center ein Volume für Drei-Wege-Spiegelung:

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem „Direkte Speicherplätze“-Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** und dann **Volume erstellen** aus.
3. Geben Sie im Bereich **Volume erstellen** einen Namen für das Volume ein, und behalten Sie für **Resilienz** die Option **Drei-Wege-Spiegelung** bei.
4. Geben Sie unter **Size on HDD** (Größe auf HDD) die Größe des Volumes an. Beispiel: 5 TB (Terabyte).
5. Klicken Sie auf **Erstellen**.

Je nach Größe kann die Erstellung des Volumes einige Minuten dauern. Nachdem das Volume erstellt wurde, wird oben rechts eine entsprechende Benachrichtigung angezeigt. Das neue Volume wird in der Liste „Bestand“ angezeigt.

Sehen Sie sich ein kurzes Video zur Erstellung eines Volumes für Drei-Wege-Spiegelung an.

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>Erstellen eines Volumes für Parität mit Beschleunigung per Spiegelung

Bei der Parität mit Beschleunigung per Spiegelung wird der Speicherbedarf des Volumes auf der HDD reduziert. Bei einem Volume für Drei-Wege-Spiegelung bedeutet dies, dass Sie für eine Größe von 10 TB jeweils einen Speicherbedarf von 30 TB benötigen. Erstellen Sie ein Volume für Parität mit Beschleunigung per Spiegelung, um den Speicherbedarf zu reduzieren. Hierdurch wird der Speicherbedarf von 30 TB auf nur 22 TB verringert (auch bei nur vier Servern), indem die aktivsten 20 % der Daten gespiegelt werden. Zum Speichern der restlichen Daten wird Parität genutzt, weil diese Vorgehensweise in Bezug auf den Speicherbedarf effizienter ist. Sie können dieses Verhältnis von Parität und Spiegelung anpassen, um Leistung und Kapazität so abzustimmen, dass für Ihre Workload das bestmögliche Ergebnis erzielt wird. Bei einem Paritätsanteil von 90 % und einem Spiegelungsanteil von 10 % ist die Leistung beispielsweise geringer, aber der Speicherbedarf wird noch weiter optimiert.

Erstellen Sie in Windows Admin Center wie folgt ein Volume für Parität mit Beschleunigung per Spiegelung:

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem „Direkte Speicherplätze“-Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite „Volumes“ die Registerkarte **Bestand** und dann **Volume erstellen** aus.
3. Geben Sie im Bereich **Volume erstellen** einen Namen für das Volume ein.
4. Wählen Sie unter **Resilienz** die Option **Mirror-accelerated parity** (Parität mit Beschleunigung per Spiegelung) aus.
5. Wählen Sie unter **Parity percentage** (Paritätsprozentsatz) den Prozentsatz für die Parität aus.
6. Klicken Sie auf **Erstellen**.

Sehen Sie sich ein kurzes Video zur Erstellung eines Volumes für Parität mit Beschleunigung per Spiegelung an.

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>Öffnen eines Volumes und Hinzufügen von Dateien

Gehen Sie wie folgt vor, um in Windows Admin Center ein Volume zu öffnen und Dateien hinzuzufügen:

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem „Direkte Speicherplätze“-Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** aus.
2. Wählen Sie in der Liste mit den Volumes den Namen des Volumes aus, das Sie öffnen möchten.

    Auf der Seite mit den Volumedetails wird der Pfad zum Volume angezeigt.

4. Wählen Sie oben auf der Seite die Option **Öffnen** aus. Das Tool **Dateien** wird in Windows Admin Center geöffnet.
5. Navigieren Sie zum Pfad des Volumes. Hier können Sie die Dateien des Volumes durchsuchen.
6. Wählen Sie  **Hochladen** und dann eine Datei für den Upload aus.
7. Verwenden Sie die Browserschaltfläche **Zurück**, um zurück zum Bereich **Tools** in Windows Admin Center zu wechseln.

Sehen Sie sich ein kurzes Video zum Öffnen eines Volumes und Hinzufügen von Dateien an.

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>Aktivieren der Deduplizierung und Komprimierung

Die Deduplizierung und Komprimierung wird pro Volume verwaltet. Bei der Deduplizierung und Komprimierung wird ein Nachbearbeitungsmodell verwendet. Dies bedeutet, dass Einsparungen für Sie erst nach der Ausführung erkennbar sind. Bei der Ausführung werden alle Dateien verarbeitet – auch die Dateien, die bereits vorhanden waren.

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem „Direkte Speicherplätze“-Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** aus.
3. Wählen Sie in der Liste mit den Volumes den Namen des Volumes aus, das Sie verwalten möchten.
4. Klicken Sie auf der Seite mit den Volumedetails auf den Switch **Deduplizierung und Komprimierung**.
5. Wählen Sie im Bereich **Enable deduplication** (Deduplizierung aktivieren) den Deduplizierungsmodus aus.

    Anstatt komplizierte Einstellungen vornehmen zu müssen, können Sie mit Windows Admin Center zwischen fertigen Profilen für unterschiedliche Workloads wählen. Verwenden Sie die Standardeinstellung, falls Sie unsicher sind.

6. Wählen Sie **Aktivieren** aus.

Sehen Sie sich ein kurzes Video dazu an, wie Sie die Deduplizierung und Komprimierung aktivieren.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

## <a name="create-volumes-using-windows-powershell"></a>Erstellen von Volumes mit Windows PowerShell

Starten Sie zunächst Windows PowerShell über das Windows-Menü „Start“. Wir empfehlen Ihnen, zum Erstellen von Volumes für Azure Stack HCI das Cmdlet **New-Volume** zu verwenden. Dies ist die schnellste und einfachste Möglichkeit. Mit diesem Cmdlet wird mit nur einem einfachen Schritt Folgendes durchgeführt: Der virtuelle Datenträger wird automatisch erstellt und anschließend partitioniert und formatiert, und das Volume wird mit dem entsprechenden Namen erstellt und den freigegebenen Clustervolumes hinzugefügt.

Das Cmdlet **New-Volume** verfügt über vier Parameter, die Sie immer angeben müssen:

- **FriendlyName:** Eine beliebige Zeichenfolge, z. B. *„Volume1“* .
- **FileSystem:** Entweder **CSVFS_ReFS** (empfohlen) oder **CSVFS_NTFS**.
- **StoragePoolFriendlyName:** Der Name Ihres Speicherpools, z. B. *„S2D auf Clustername“* .
- **Size:** Die Größe des Volumes, z. B. *„10TB“* .

   > [!NOTE]
   > Unter Windows, einschließlich PowerShell, wird basierend auf Binärzahlen (Basis 2) gezählt, während für Laufwerke häufig Dezimalzahlen (Basis 10) verwendet werden. Dies ist der Grund, warum für ein Laufwerk mit „einem Terabyte“ (als 1.000.000.000.000 Byte definiert) in Windows die Größe als „909 GB“ angezeigt wird. Dies entspricht dem erwarteten Verhalten. Beim Erstellen von Volumes mit **New-Volume** sollten Sie den Parameter **Size** als Binärzahl (Basis 2) angeben. Wenn Sie beispielsweise „909GB“ oder „0,909495TB“ angeben, wird ein Volume mit ca. 1.000.000.000.000 Byte erstellt.

### <a name="example-with-2-or-3-servers"></a>Beispiel: Mit zwei oder drei Servern

Falls Ihre Bereitstellung nur über zwei Server verfügt, nutzt „Direkte Speicherplätze“der Einfachheit halber automatisch die Zwei-Wege-Spiegelung zur Erzielung von Resilienz. Falls Ihre Bereitstellung nur drei Server umfasst, wird automatisch die Drei-Wege-Spiegelung genutzt.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>Beispiel: Mit vier oder mehr Servern

Wenn Sie über vier oder mehr Server verfügen, können Sie den optionalen Parameter **ResiliencySettingName** verwenden, um Ihren Resilienztyp zu wählen.

-   **ResiliencySettingName:** Entweder **Mirror** (Spiegelung) oder **Parity** (Parität).

Im folgenden Beispiel wird für *„Volume2“* die Drei-Wege-Spiegelung und für *„Volume3“* die duale Parität (häufig als „Erasure Coding“ bezeichnet) verwendet.

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

### <a name="example-using-storage-tiers"></a>Beispiel: Verwenden von Speicherebenen

Bei Bereitstellungen mit drei Arten von Laufwerken kann ein Volume für die SSD- und HDD-Ebene übergreifend verwendet werden und jeweils teilweise auf diesen Laufwerken angeordnet werden. Bei Bereitstellungen mit vier oder mehr Servern können für ein Volume Spiegelung und duale Parität so gemischt werden, dass sie darauf jeweils teilweise angeordnet sind.

Als Hilfe bei der Erstellung dieser Volumes werden von „Direkte Speicherplätze“ Standardvorlagen für Ebenen bereitgestellt, die die Bezeichnungen *Leistung* und *Kapazität* haben. Sie enthalten Definitionen für die Drei-Wege-Spiegelung auf den schnelleren Kapazitätslaufwerken (falls zutreffend) und für die duale Parität auf den langsameren Kapazitätslaufwerken (falls zutreffend).

Sie können dies anzeigen, indem Sie das Cmdlet **Get-StorageTier** ausführen.

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

![Screenshot: Speicherebenen in PowerShell](media/creating-volumes/storage-tiers-screenshot.png)

Verweisen Sie zum Erstellen von mehrstufigen Volumes auf diese Ebenenvorlagen, indem Sie die Parameter **StorageTierFriendlyNames** und **StorageTierSizes** des Cmdlets **New-Volume** nutzen. Beispielsweise wird mit dem folgenden Cmdlet ein Volume erstellt, für das die Drei-Wege-Spiegelung und die duale Parität im Verhältnis 30:70 gemischt werden.

```PowerShell
New-Volume -FriendlyName "Volume4" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames Performance, Capacity -StorageTierSizes 300GB, 700GB
```

Sie haben es geschafft! Wiederholen Sie diese Schritte bei Bedarf, um mehr als ein Volume zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Durchführen anderer Aufgaben der Speicherverwaltung in „Direkte Speicherplätze“ finden Sie unter:

- [Direkte Speicherplätze – Übersicht](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Planen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/plan-volumes)
- [Erweitern von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/resize-volumes)
- [Löschen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/delete-volumes)