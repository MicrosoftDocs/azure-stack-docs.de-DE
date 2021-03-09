---
title: Erstellen von Volumes in Azure Stack HCI- und Windows Server-Clustern
description: Erstellen von Volumes in Azure Stack HCI- und Windows Server-Clustern mithilfe von Windows Admin Center und PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: f5c585bd612cb25b32df22c342988bbad17d08ee
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839781"
---
# <a name="create-volumes-in-azure-stack-hci-and-windows-server-clusters"></a>Erstellen von Volumes in Azure Stack HCI- und Windows Server-Clustern

> Gilt für: Azure Stack HCI (Version 20H2), Windows Server 2019, Windows Server 2016

In diesem Thema wird beschrieben, wie Sie Volumes auf einem Cluster mithilfe von Windows Admin Center und Windows PowerShell erstellen, wie Sie mit Dateien auf den Volumes arbeiten und wie Sie Deduplizierung und Komprimierung, Integritätsprüfsummen oder BitLocker-Verschlüsselung für Volumes aktivieren. Informationen zum Erstellen von Volumes und zum Einrichten der Replikation für Stretchingcluster finden Sie unter [Erstellen von Stretchingvolumes](create-stretched-volumes.md).

> [!TIP]
> Wenn dies noch nicht geschehen ist, lesen Sie zunächst [Planen von Volumes](../concepts/plan-volumes.md).

## <a name="create-a-two-way-or-three-way-mirror-volume"></a>Erstellen eines Volumes für Zwei- oder Drei-Wege-Spiegelung

Erstellen Sie wie folgt mit Windows Admin Center ein Volume für Zwei- oder Drei-Wege-Spiegelung:

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
1. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** und dann **Erstellen** aus.
1. Geben Sie im Bereich **Volume erstellen** einen Namen für das Volume ein.
1. Wählen Sie unter **Resilienz** abhängig von der Anzahl der Server in Ihrem Cluster die Option **Zwei-Wege-Spiegelung** oder **Drei-Wege-Spiegelung** aus.
1. Geben Sie unter **Size on HDD** (Größe auf HDD) die Größe des Volumes an. Beispiel: 5 TB (Terabyte).
1. Unter **Weitere Optionen** können Sie mit den Kontrollkästchen Deduplizierung und Komprimierung, Integritätsprüfsummen oder BitLocker-Verschlüsselung aktivieren.
1. Klicken Sie auf **Erstellen**.

   :::image type="content" source="media/create-volumes/create-mirror-volume.png" alt-text="Sie können Windows Admin Center verwenden, um ein Volume für Zwei- oder Drei-Wege-Spiegelung zu erstellen" lightbox="media/create-volumes/create-mirror-volume.png":::

Je nach Größe kann die Erstellung des Volumes einige Minuten dauern. Nachdem das Volume erstellt wurde, wird oben rechts eine entsprechende Benachrichtigung angezeigt. Das neue Volume wird dann in der Bestandsliste angezeigt.

## <a name="create-a-mirror-accelerated-parity-volume"></a>Erstellen eines Volumes für Parität mit Beschleunigung per Spiegelung

Bei der Parität mit Beschleunigung per Spiegelung (Mirror-Accelerated Parity, MAP) wird der Speicherbedarf des Volumes auf der HDD reduziert. Bei einem Volume für Drei-Wege-Spiegelung bedeutet dies, dass Sie für eine Größe von 10 TB jeweils einen Speicherbedarf von 30 TB benötigen. Erstellen Sie ein Volume für Parität mit Beschleunigung per Spiegelung, um den Speicherbedarf zu reduzieren. Hierdurch wird der Speicherbedarf von 30 TB auf nur 22 TB verringert (auch bei nur vier Servern), indem die aktivsten 20 % der Daten gespiegelt werden. Zum Speichern der restlichen Daten wird Parität genutzt, weil diese Vorgehensweise in Bezug auf den Speicherbedarf effizienter ist. Sie können dieses Verhältnis von Parität und Spiegelung anpassen, um Leistung und Kapazität so abzustimmen, dass für Ihre Workload das bestmögliche Ergebnis erzielt wird. Bei einem Paritätsanteil von 90 % und einem Spiegelungsanteil von 10 % ist die Leistung beispielsweise geringer, aber der Speicherbedarf wird noch weiter optimiert.

  >[!NOTE]
  >Für Volumes mit Parität mit Beschleunigung per Spiegelung wird ein resilientes Dateisystem (Resilient File System, ReFS) benötigt.

Erstellen Sie in Windows Admin Center wie folgt ein Volume für Parität mit Beschleunigung per Spiegelung:

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
1. Wählen Sie auf der Seite „Volumes“ die Registerkarte **Bestand** und dann **Erstellen** aus.
1. Geben Sie im Bereich **Volume erstellen** einen Namen für das Volume ein.
1. Wählen Sie unter **Resilienz** die Option **Mirror-accelerated parity** (Parität mit Beschleunigung per Spiegelung) aus.
1. Wählen Sie unter **Parity percentage** (Paritätsprozentsatz) den Prozentsatz für die Parität aus.
1. Unter **Weitere Optionen** können Sie mit den Kontrollkästchen Deduplizierung und Komprimierung, Integritätsprüfsummen oder BitLocker-Verschlüsselung aktivieren.
1. Klicken Sie auf **Erstellen**.

## <a name="open-volume-and-add-files"></a>Öffnen eines Volumes und Hinzufügen von Dateien

Gehen Sie wie folgt vor, um in Windows Admin Center ein Volume zu öffnen und Dateien hinzuzufügen:

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem Cluster her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** aus.
2. Wählen Sie in der Liste mit den Volumes den Namen des Volumes aus, das Sie öffnen möchten.

    Auf der Seite mit den Volumedetails wird der Pfad zum Volume angezeigt.

4. Wählen Sie oben auf der Seite die Option **Öffnen** aus. Das Tool **Dateien** wird in Windows Admin Center geöffnet.
5. Navigieren Sie zum Pfad des Volumes. Hier können Sie die Dateien des Volumes durchsuchen.
6. Wählen Sie  **Hochladen** und dann eine Datei für den Upload aus.
7. Verwenden Sie die Browserschaltfläche **Zurück**, um zurück zum Bereich **Tools** in Windows Admin Center zu wechseln.

## <a name="turn-on-deduplication-and-compression"></a>Aktivieren der Deduplizierung und Komprimierung

Die Deduplizierung und Komprimierung wird pro Volume verwaltet. Bei der Deduplizierung und Komprimierung wird ein Nachbearbeitungsmodell verwendet. Dies bedeutet, dass Einsparungen für Sie erst nach der Ausführung erkennbar sind. Bei der Ausführung werden alle Dateien verarbeitet – auch die Dateien, die bereits vorhanden waren.

Weitere Informationen finden Sie unter [Aktivieren der Volumeverschlüsselung, Deduplizierung und Komprimierung](volume-encryption-deduplication.md).

## <a name="create-volumes-using-windows-powershell"></a>Erstellen von Volumes mit Windows PowerShell

Starten Sie zunächst Windows PowerShell über das Windows-Menü „Start“. Wir empfehlen Ihnen, zum Erstellen von Volumes für Azure Stack HCI das Cmdlet **New-Volume** zu verwenden. Dies ist die schnellste und einfachste Möglichkeit. Mit diesem Cmdlet wird mit nur einem einfachen Schritt Folgendes durchgeführt: Der virtuelle Datenträger wird automatisch erstellt und anschließend partitioniert und formatiert, und das Volume wird mit dem entsprechenden Namen erstellt und den freigegebenen Clustervolumes hinzugefügt.

Das Cmdlet **New-Volume** verfügt über vier Parameter, die Sie immer angeben müssen:

- **FriendlyName:** Eine beliebige Zeichenfolge, z. B. *„Volume1“* .
- **FileSystem:** Entweder **CSVFS_ReFS** (empfohlen für alle Volumes, obligatorisch für Volumes mit Parität mit Beschleunigung per Spiegelung) oder **CSVFS_NTFS**.
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

## <a name="using-storage-tiers"></a>Verwenden von Speicherebenen

Bei Bereitstellungen mit drei Arten von Laufwerken kann ein Volume für die SSD- und HDD-Ebene übergreifend verwendet werden und jeweils teilweise auf diesen Laufwerken angeordnet werden. Bei Bereitstellungen mit vier oder mehr Servern können für ein Volume Spiegelung und duale Parität so gemischt werden, dass sie darauf jeweils teilweise angeordnet sind.

Als Hilfe für die Erstellung von Volumes dieser Art verfügen Azure Stack HCI und Windows Server 2019 über die Standardebenenvorlagen **MirrorOn*MediaType*** und **NestedMirrorOn*MediaType*** (für Leistung) sowie **ParityOn*MediaType*** und **NestedParityOn*MediaType*** (für Kapazität). Hierbei wird als *MediaType* entweder HDD oder SSD verwendet. Die Vorlagen repräsentieren Speicherebenen, die auf Medientypen basieren, und enthalten Definitionen für die Drei-Wege-Spiegelung auf den schnelleren Kapazitätslaufwerken (falls zutreffend) und für die duale Parität auf den langsameren Kapazitätslaufwerken (falls zutreffend).

   > [!NOTE]
   > In Windows Server 2016-Clustern, in denen direkte Speicherplätze ausgeführt werden, wurden die Standardebenenvorlagen einfach als **Leistung** und **Kapazität** bezeichnet.

Sie können die Speicherebenen anzeigen, indem Sie auf einem Server im Cluster das Cmdlet **Get-StorageTier** ausführen.

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

Wenn Sie beispielsweise über einen Cluster mit zwei Knoten und nur mit HDD verfügen, sieht Ihre Ausgabe in etwa wie folgt aus:

```PowerShell
FriendlyName      ResiliencySettingName PhysicalDiskRedundancy
------------      --------------------- ----------------------
NestedParityOnHDD Parity                                     1
Capacity          Mirror                                     1
NestedMirrorOnHDD Mirror                                     3
MirrorOnHDD       Mirror                                     1
```

Verweisen Sie zum Erstellen von mehrstufigen Volumes auf diese Ebenenvorlagen, indem Sie die Parameter **StorageTierFriendlyNames** und **StorageTierSizes** des Cmdlets **New-Volume** nutzen. Beispielsweise wird mit dem folgenden Cmdlet ein Volume erstellt, für das die Drei-Wege-Spiegelung und die duale Parität im Verhältnis 30:70 gemischt werden.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames MirrorOnHDD, Capacity -StorageTierSizes 300GB, 700GB
```

Wiederholen Sie diese Schritte bei Bedarf, um mehr als ein Volume zu erstellen.

### <a name="nested-resiliency-volumes"></a>Volumes mit geschachtelter Resilienz

Geschachtelte Resilienz gilt nur für Cluster mit zwei Servern, auf denen Azure Stack HCI oder Windows Server 2019 ausgeführt wird. Sie können geschachtelte Resilienz nicht verwenden, wenn Ihr Cluster drei oder mehr Server umfasst oder wenn Ihr Cluster Windows Server 2016 ausführt. Bei der geschachtelten Resilienz kann ein Cluster mit zwei Servern mehrere gleichzeitige Hardwarefehler überstehen, ohne dass es zu einem Verlust der Speicherverfügbarkeit kommt. Dies bedeutet, dass es für Benutzer, Apps und virtuelle Computer nicht zu einer Störung kommt. Weitere Informationen finden Sie unter [Planen von Volumes in Azure Stack HCI: Auswählen des Resilienztyps](../concepts/plan-volumes.md#choosing-the-resiliency-type).

#### <a name="create-nested-storage-tiers"></a>Erstellen von geschachtelten Speicherebenen

Erstellen Sie wie folgt eine NestedMirror-Ebene:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedMirror -ResiliencySettingName Mirror -NumberOfDataCopies 4 -MediaType HDD -CimSession 2nodecluster
```

Erstellen Sie wie folgt eine NestedParity-Ebene:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedParity -ResiliencySettingName Parity -NumberOfDataCopies 2 -PhysicalDiskRedundancy 1 -NumberOfGroups 1 -FaultDomainAwareness StorageScaleUnit -ColumnIsolation PhysicalDisk -MediaType HDD -CimSession 2nodecluster
```

#### <a name="create-nested-volumes"></a>Erstellen von geschachtelten Volumes

Erstellen Sie wie folgt ein NestedMirror-Volume:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyMirrorNestedVolume -StorageTierFriendlyNames NestedMirror -StorageTierSizes 500GB -CimSession 2nodecluster
```

Erstellen Sie wie folgt ein NestedParity-Volume:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyParityNestedVolume -StorageTierFriendlyNames NestedMirror,NestedParity -StorageTierSizes 200GB, 1TB -CimSession 2nodecluster
```

### <a name="storage-tier-summary-table"></a>Tabelle mit Speicherebenen-Zusammenfassung

Die folgenden Tabellen enthalten eine Zusammenfassung der Speicherebenen, die in Azure Stack HCI und Windows Server 2019 erstellt werden bzw. erstellt werden können.

**NumberOfNodes: 2**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Hinweis         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | Festplattenlaufwerk       | Spiegel                | 2                  | 1                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| MirrorOnSSD       | SSD       | Spiegel                | 2                  | 1                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| MirrorOnSCM       | SCM       | Spiegel                | 2                  | 1                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| NestedMirrorOnHDD | Festplattenlaufwerk       | Spiegel                | 4                  | 3                      | 1              | StorageScaleUnit     | Physischer Datenträger    | manual       |
| NestedMirrorOnSSD | SSD       | Spiegel                | 4                  | 3                      | 1              | StorageScaleUnit     | Physischer Datenträger    | manual       |
| NestedMirrorOnSCM | SCM       | Spiegel                | 4                  | 3                      | 1              | StorageScaleUnit     | Physischer Datenträger    | manual       |
| NestedParityOnHDD | Festplattenlaufwerk       | Parität                | 2                  | 1                      | 1              | StorageScaleUnit     | Physischer Datenträger    | manual       |
| NestedParityOnSSD | SSD       | Parität                | 2                  | 1                      | 1              | StorageScaleUnit     | Physischer Datenträger    | manual       |
| NestedParityOnSCM | SCM       | Parität                | 2                  | 1                      | 1              | StorageScaleUnit     | Physischer Datenträger    | manual       |

**NumberOfNodes: 3**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Hinweis         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | Festplattenlaufwerk       | Spiegel                | 3                  | 2                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| MirrorOnSSD       | SSD       | Spiegel                | 3                  | 2                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| MirrorOnSCM       | SCM       | Spiegel                | 3                  | 2                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |

**NumberOfNodes: 4+**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Hinweis         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | Festplattenlaufwerk       | Spiegel                | 3                  | 2                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| MirrorOnSSD       | SSD       | Spiegel                | 3                  | 2                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| MirrorOnSCM       | SCM       | Spiegel                | 3                  | 2                      | 1              | StorageScaleUnit     | Physischer Datenträger    | Automatische Erstellung |
| ParityOnHDD       | Festplattenlaufwerk       | Parität                | 1                  | 2                      | Automatisch           | StorageScaleUnit     | StorageScaleUnit| Automatische Erstellung |
| ParityOnSSD       | SSD       | Parität                | 1                  | 2                      | Automatisch           | StorageScaleUnit     | StorageScaleUnit| Automatische Erstellung |
| ParityOnSCM       | SCM       | Parität                | 1                  | 2                      | Automatisch           | StorageScaleUnit     | StorageScaleUnit| Automatische Erstellung |

## <a name="next-steps"></a>Nächste Schritte

Verwandte Themen und andere Speicherverwaltungsaufgaben finden Sie unter:

- [Direkte Speicherplätze – Übersicht](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Erweitern von Volumes](extend-volumes.md)
- [Löschen von Volumes](delete-volumes.md)