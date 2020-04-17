---
title: Erweitern von Volumes in Azure Stack HCI
description: Hier erfahren Sie, wie Sie die Größe von Volumes in Azure Stack HCI mithilfe von Windows Admin Center und PowerShell ändern.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 703931b0dccb533b2b924847eb3302f0efa46d1a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79089302"
---
# <a name="extending-volumes-in-storage-spaces-direct"></a>Erweitern von Volumes in „Direkte Speicherplätze“
> Gilt für: Windows Server 2019

In diesem Thema erfahren Sie, wie Sie die Größe von Volumes in einem Cluster mit [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) mithilfe von Windows Admin Center ändern.

> [!WARNING]
> **Das Ändern der Größe des zugrunde liegenden Speichers, der von „Direkte Speicherplätze“ verwendet wird, wird nicht unterstützt.** Wenn Sie „Direkte Speicherplätze“ in einer virtualisierten Speicherumgebung ausführen (etwa in Azure), wird das Ändern der Größe oder Merkmale der Speichergeräte, die von den virtuellen Computern verwendet werden, nicht unterstützt und führt dazu, dass nicht mehr auf die Daten zugegriffen werden kann. Gehen Sie stattdessen wie unter [Hinzufügen von Servern oder Laufwerken zu „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/add-nodes) beschrieben vor, um vor der Erweiterung von Volumes zusätzliche Kapazität hinzuzufügen.

Sehen Sie sich ein kurzes Anleitungsvideo zum Ändern der Größe eines Volumes an:

> [!VIDEO https://www.youtube-nocookie.com/embed/hqyBzipBoTI]

## <a name="extending-volumes-using-windows-admin-center"></a>Erweitern von Volumes mithilfe von Windows Admin Center

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem Cluster mit „Direkte Speicherplätze“ her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** und anschließend das Volume aus, dessen Größe Sie ändern möchten.

    Auf der Seite mit den Volumedetails wird die Speicherkapazität für das Volume angezeigt. Die Seite mit den Volumedetails kann auch direkt über das Dashboard geöffnet werden. Wählen auf dem Dashboard im Bereich „Warnungen“ die Warnung aus, die angezeigt wird, wenn die Speicherkapazität eines Volumes nahezu erschöpft ist, und wählen Sie dann **Go To Volume** (Zum Volume) aus.

4. Wählen Sie im oberen Bereich der Seite mit den Volumedetails die Option **Größe ändern** aus.
5. Geben Sie einen neuen (größeren) Wert für die Größe ein, und wählen Sie **Größe ändern** aus.

    Auf der Seite mit den Volumedetails wird die größere Speicherkapazität für das Volume angezeigt, und die Warnung auf dem Dashboard wird entfernt.

## <a name="extending-volumes-using-powershell"></a>Erweitern von Volumes mithilfe von PowerShell

### <a name="capacity-in-the-storage-pool"></a>Kapazität im Speicherpool

Vergewissern Sie sich vor dem Ändern der Größe eines Volumes, dass im Speicherpool genügend Kapazität für den neuen (höheren) Speicherbedarf vorhanden ist. Wenn Sie beispielsweise die Größe eines Volumes mit Drei-Wege-Spiegelung von 1 TB auf 2 TB erhöhen, erhöht sich der Speicherbedarf von 3 TB auf 6 TB. Für eine erfolgreiche Größenänderung muss im Speicherpool eine Kapazität von mindestens 3 TB (6 TB minus 3 TB) verfügbar sein.

### <a name="familiarity-with-volumes-in-storage-spaces"></a>Informationen zu Volumes in Speicherplätzen

In „Direkte Speicherplätze“ besteht jedes Volume aus mehreren gestapelten Objekten: dem freigegebenen Clustervolume (Cluster Shared Volume, CSV), der Partition, dem (virtuellen) Datenträger und mindestens einer Speicherebene (sofern zutreffend). Wenn Sie die Größe eines Volumes ändern möchten, muss die Größe von mehreren dieser Objekte geändert werden.

![volumes-in-smapi](media/extend-volumes/volumes-in-smapi.png)

Führen Sie **Get-** mit dem entsprechenden Substantiv in PowerShell aus, um sich mit den einzelnen Objekten vertraut zu machen.

Beispiel:

```PowerShell
Get-VirtualDisk
```

Um Zuordnungen zwischen Objekten im Stapel zu folgen, verknüpfen Sie Cmdlets vom Typ **Get-** jeweils durch einen senkrechten Strich.

So gelangen Sie beispielsweise von einem virtuellen Datenträger zum zugehörigen Volume:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume 
```

### <a name="step-1--resize-the-virtual-disk"></a>Schritt 1: Ändern der Größe des virtuellen Datenträgers

Der virtuelle Datenträger verwendet unter Umständen Speicherebenen. Dies ist abhängig davon, wie er erstellt wurde.

Führen Sie zur Überprüfung das folgende Cmdlet aus:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier 
```

Wenn das Cmdlet nichts zurückgibt, verwendet der virtuelle Datenträger keine Speicherebenen.

#### <a name="no-storage-tiers"></a>Ohne Speicherebenen

Wenn der virtuelle Datenträger über keine Speicherebenen verfügt, kann die Größe direkt mithilfe des Cmdlets **Resize-VirtualDisk** geändert werden.

Geben Sie die neue Größe im Parameter **-Size** an.

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

Wenn Sie die Größe des virtuellen Datenträgers (**VirtualDisk**) ändern, wird automatisch auch die Größe des Datenträgers (**Disk**) geändert.

![Resize-VirtualDisk](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>Mit Speicherebenen

Wenn der virtuelle Datenträger Speicherebenen verwendet, kann die Größe der einzelnen Ebenen separat mithilfe des Cmdlets **Resize-StorageTier** geändert werden.

Ermitteln Sie die Namen der Speicherebenen, indem Sie vom virtuellen Datenträger aus den Zuordnungen folgen.

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

Geben Sie dann die neue Größe für die jeweilige Ebene im Parameter **-Size** an.

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> Falls es sich bei Ihren Ebenen um verschiedene physische Medientypen handelt (beispielsweise **MediaType = SSD** und **MediaType = HDD**), müssen Sie sich vergewissern, dass im Speicherpool für jeden Medientyp genügend Kapazität für den neuen (höheren) Speicherbedarf der jeweiligen Ebene zur Verfügung steht.

Wenn Sie die Größe einer Speicherebene (**StorageTier**) ändern, wird automatisch auch die Größe des virtuellen Datenträgers (**VirtualDisk**) und des Datenträgers (**Disk**) geändert.

![Resize-StorageTier](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--resize-the-partition"></a>Schritt 2: Ändern der Größe der Partition

Ändern Sie als Nächstes die Größe der Partition mithilfe des Cmdlets **Resize-Partition**. Es wird erwartet, dass der virtuelle Datenträger über zwei Partitionen verfügt: Die erste Partition ist reserviert und darf nicht geändert werden. Die Partition, deren Größe geändert werden muss, besitzt folgende Merkmale: **PartitionNumber = 2** und **Type = Basic**.

Geben Sie die neue Größe im Parameter **-Size** an. Es wird empfohlen, die maximal unterstützte Größe zu verwenden, wie weiter unten gezeigt.

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size 
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

Wenn Sie die Größe der **Partition** ändern, wird automatisch auch die Größe des Volumes (**Volume**) und des freigegebenen Clustervolumes (**ClusterSharedVolume**) geändert.

![Resize-Partition](media/extend-volumes/Resize-Partition.gif)

Das ist alles!

> [!TIP]
> Durch Ausführen von **Get-Volume** können Sie sich vergewissern, dass das Volume eine neue Größe hat.

## <a name="next-steps"></a>Nächste Schritte

Schrittanleitungen für weitere wichtige Speicherverwaltungsaufgaben finden Sie unter den folgenden Links:

- [Planen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/plan-volumes)
- [Erstellen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/create-volumes)
- [Löschen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/delete-volumes)