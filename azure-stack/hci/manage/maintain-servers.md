---
title: Offlineschalten eines Azure Stack HCI-Servers zur Wartung
description: Dieses Thema enthält Anleitungen, wie Sie Server, auf denen das Azure Stack HCI-Betriebssystem ausgeführt wird, mithilfe von Windows Admin Center und PowerShell ordnungsgemäß anhalten, entladen und fortsetzen.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/23/2020
ms.openlocfilehash: 3b2c462ad8e6db96be5968074ce5478f1ca9c870
ms.sourcegitcommit: 6a51687a98c417a004cd4295ad06ae813e1978cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92641043"
---
# <a name="taking-an-azure-stack-hci-server-offline-for-maintenance"></a>Offlineschalten eines Azure Stack HCI-Servers zur Wartung

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Wenn Sie einen Server unter Azure Stack HCI offline nehmen, müssen hierfür Teile des Speichers offline geschaltet werden, die von allen Servern im Cluster gemeinsam verwendet werden. Hierfür muss der Server angehalten werden, der offline geschaltet werden soll, es müssen Rollen und virtuelle Computer (VMs) auf andere Server im Cluster verschoben werden, und es muss überprüft werden, ob alle Daten auf den anderen Servern im Cluster verfügbar sind. Durch diesen Vorgang wird sichergestellt, dass die Daten während des gesamten Wartungszeitraums sicher und zugänglich bleiben.

Sie können entweder Windows Admin Center oder PowerShell verwenden, um einen Server zur Wartung offline zu schalten. In diesem Thema werden beide Methoden behandelt.

   > [!IMPORTANT]
   > In diesem Thema wird vorausgesetzt, dass Sie einen physischen Server ausschalten müssen, um Wartungsarbeiten auszuführen, oder ihn aus einem anderen Grund neu starten müssen. Informationen zum Installieren von Updates auf einem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](update-cluster.md), worin erläutert wird, wie Sie clusterfähiges Aktualisieren (Cluster-Aware Updating, CAU) verwenden, um alle Schritte in diesem Thema automatisch auszuführen, während gleichzeitig die Server aktualisiert und ggf. neu gestartet werden.

## <a name="take-a-server-offline-using-windows-admin-center"></a>Offlineschalten eines Servers über Windows Admin Center

Die einfachste Möglichkeit, um einen Server in einem Azure Stack HCI-Cluster offline zu schalten, ist die Verwendung des Windows Admin Centers.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Vergewissern Sie sich, dass es sicher ist, den Server offline zu schalten.

1. Stellen Sie mithilfe des Windows Admin Centers eine Verbindung mit dem Server her, den Sie offline schalten möchten. Wählen Sie im Menü **Tools** die Option **Speicher > Datenträger** aus, und überprüfen Sie, ob in der Spalte **Status** für jeden virtuellen Datenträger **Online** angezeigt wird.

2. Wählen Sie dann **Speicher > Volumes** aus, und vergewissern Sie sich, dass in der Spalte **Integrität** für jedes Volume **Fehlerfrei** angezeigt wird, und dass in der Spalte **Status** für jedes Volume **OK** angezeigt wird.

### <a name="pause-and-drain-the-server"></a>Anhalten und Entladen des Servers

Vor dem Herunterfahren oder Neustarten eines Servers sollten Sie den Server anhalten und alle gruppierten Rollen wie virtuelle Computer, die auf dem Server ausgeführt werden, entladen (wegverschieben), um sicherzustellen, dass das Herunterfahren des Servers den Anwendungsstatus nicht beeinträchtigt. Halten Sie gruppierte Server vor dem Offlineschalten zur Wartung immer an, und entladen Sie sie zuerst.

1. Stellen Sie mit Windows Admin Center eine Verbindung mit dem Cluster her, und wählen Sie dann im Cluster-Manager im Menü **Tools** die Option **Compute > Knoten** aus.

2. Klicken Sie auf den Namen des Servers, der angehalten und entladen werden soll, und wählen Sie **Anhalten** aus. Daraufhin sollte folgende Aufforderung angezeigt werden:

   *Wenn Sie diesen Knoten anhalten, werden alle gruppierten Rollen auf andere Knoten verschoben, und diesem Knoten können keine Rollen mehr hinzugefügt werden, bis er fortgesetzt wird. Möchten Sie den Clusterknoten anhalten?*

3. Wählen Sie **Ja** aus, um den Server anzuhalten und den Entladungsprozess zu initiieren. Der Status des Clusterknotens wird als **Wird entladen** angezeigt, und Rollen wie Hyper-V und virtuelle Computer beginnen sofort mit der Livemigration zu anderen Servern im Cluster. Dies kann einige Minuten dauern.

   > [!NOTE]
   > Wenn Sie den Server ordnungsgemäß anhalten und entladen, führt Azure Stack HCI eine automatische Sicherheitsüberprüfung durch, um sicherzustellen, dass der Vorgang sicher fortgesetzt werden kann. Wenn fehlerhafte Volumes vorhanden sind, hält er an und warnt Sie, dass der Vorgang nicht sicher fortgesetzt werden kann.

### <a name="shut-down-the-server"></a>Herunterfahren des Servers

Nachdem der Server das Entladen abgeschlossen hat, wird sein Status im Windows Admin Center als **Angehalten** angezeigt. Sie können den Server jetzt sicher zur Wartung herunterfahren oder neu starten.

### <a name="resume-the-server"></a>Fortsetzen des Servers

Wenn Sie bereit dafür sind, dass der Server mit dem Hosting gruppierter Rollen und VMs wieder beginnen kann, schalten Sie den Server einfach wieder ein, warten Sie, bis er gestartet wurde, und setzen Sie den Server mit den folgenden Schritten fort.

1. Wählen Sie im Cluster-Manager im Menü **Tools** auf der linken Seite die Option **Compute > Knoten** aus.

2. Wählen Sie den Namen des Servers aus, der fortgesetzt werden soll, und klicken Sie dann auf **Fortsetzen** . Daraufhin sollte folgende Aufforderung angezeigt werden:

   *Möchten Sie den Clusterknoten fortsetzen?*

3. In den meisten Fällen sollten Sie das Kontrollkästchen *Gruppierte Rollen zurück auf diesen Knoten übertragen* aktivieren. Wählen Sie **Ja** aus, um den Server fortzusetzen.

Wenn Sie oben in Schritt 3 das Kontrollkästchen aktiviert haben, beginnen gruppierte Rollen und VMs sofort die Livemigration zurück auf den Server. Dies kann einige Minuten dauern.

### <a name="wait-for-storage-to-resync"></a>Abwarten der Neusynchronisierung des Speichers

Beim Fortsetzen des Servers müssen alle neuen Schreibvorgänge, die während der Nichtverfügbarkeit erfolgt sind, neu synchronisiert werden. Dies erfolgt automatisch mithilfe intelligenter Änderungsnachverfolgung. Es ist nicht erforderlich, dass *alle* Daten überprüft oder synchronisiert werden, lediglich die Änderungen. Dieser Prozess wird gedrosselt, um die Auswirkungen auf Produktionsworkloads zu mindern. Je nachdem, wie lange der Server angehalten wurde und wie viele neue Daten geschrieben wurden, kann es mehrere Minuten dauern, bis der Vorgang abgeschlossen ist.

   > [!IMPORTANT]
   > Sie müssen warten, bis die Neusynchronisierung beendet ist, bevor Sie andere Server im Cluster offline schalten.

Um zu überprüfen, ob die Neusynchronisierung abgeschlossen wurde, stellen Sie über das Windows Admin Center eine Verbindung mit dem Server her, und wählen Sie **Speicher > Volumes** im Menü **Tools** auf der linken Seite aus und dann **Volumes** am oberen Rand der Seite. Wenn in der Spalte **Integrität** für jedes Volume **Fehlerfrei** und in der Spalte **Status** für jedes Volume **OK** angezeigt wird, ist die Neusynchronisierung abgeschlossen, und Sie können nun weitere Server im Cluster sicher offline schalten.

## <a name="take-a-server-offline-using-powershell"></a>Offlineschalten eines Servers mit PowerShell

Verwenden Sie die folgenden Verfahren, um einen Server in einem Azure Stack HCI-Cluster mithilfe von PowerShell ordnungsgemäß anzuhalten, zu entladen und fortzusetzen.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Vergewissern Sie sich, dass es sicher ist, den Server offline zu schalten.

Um zu überprüfen, ob alle Ihre Volumes fehlerfrei sind, führen Sie das folgende Cmdlet als Administrator aus:

```PowerShell
Get-VirtualDisk
```

Hier sehen Sie ein Beispiel für eine mögliche Ausgabe:

```
FriendlyName              ResiliencySettingName FaultDomainRedundancy OperationalStatus HealthStatus    Size FootprintOnPool StorageEfficiency
------------              --------------------- --------------------- ----------------- ------------    ---- --------------- -----------------
Mirror II                 Mirror                1                     OK                Healthy         4 TB         8.01 TB            49.99%
Mirror-accelerated parity                                             OK                Healthy      1002 GB         1.96 TB            49.98%
Mirror                    Mirror                1                     OK                Healthy         1 TB            2 TB            49.98%
ClusterPerformanceHistory Mirror                1                     OK                Healthy        24 GB           49 GB            48.98%
```

Vergewissern Sie sich, dass die Eigenschaft **HealthStatus** für jedes Volume **Fehlerfrei** ist, und die Eigenschaft **OperationalStatus** den Wert „OK“ hat.

### <a name="pause-and-drain-the-server"></a>Anhalten und Entladen des Servers

Führen Sie folgendes Cmdlet als Administrator aus, um den Server anzuhalten und zu entladen:

```PowerShell
Suspend-ClusterNode -Drain
```

### <a name="shut-down-the-server"></a>Herunterfahren des Servers

Nachdem der Server das Entladen abgeschlossen hat, wird er in PowerShell als **Angehalten** angezeigt.

Sie können den Server nun sicher herunterfahren oder neu starten, indem Sie die Cmdlets `Stop-Computer` oder `Restart-Computer` der PowerShell verwenden.

   > [!NOTE]
   > Wenn Sie einen `Get-VirtualDisk`-Befehl auf Servern ausführen, die den Clusterdienst herunterfahren oder starten/beenden, wird der Betriebsstatus des Servers möglicherweise als „unvollständig“ oder „verschlechtert“ gemeldet, und in der Spalte „Integritätsstatus“ wird eventuell eine Warnung angezeigt. Dies ist normal und sollte keine Bedenken verursachen. Alle Ihre Volumes bleiben online und zugänglich.

### <a name="resume-the-server"></a>Fortsetzen des Servers

Führen Sie folgendes Cmdlet als Administrator aus, um den Server im Cluster fortzusetzen. Um die gruppierten Rollen und VMs, die zuvor auf dem Server ausgeführt wurden, wieder zurückzuverschieben, verwenden Sie das optionale Flag **-Failback** :

```PowerShell
Resume-ClusterNode –Failback Immediate
```

Nachdem der Server fortgesetzt wurde, wird er in PowerShell als **Aktiv** angezeigt.

### <a name="wait-for-storage-to-resync"></a>Abwarten der Neusynchronisierung des Speichers

Wenn der Server fortgesetzt wird, müssen Sie warten, bis die Neusynchronisierung beendet ist, bevor Sie andere Server im Cluster offline schalten.

Führen Sie folgendes Cmdlet als Administrator aus, um den Status zu überwachen:

```PowerShell
Get-StorageJob
```

Wenn die Neusynchronisierung bereits abgeschlossen wurde, erhalten Sie keine Ausgabe.

Hier sehen Sie eine Beispielausgabe, die anzeigt, dass Neusynchronisierungs(Reparatur)aufträge noch ausgeführt werden:

```
Name   IsBackgroundTask ElapsedTime JobState  PercentComplete BytesProcessed BytesTotal
----   ---------------- ----------- --------  --------------- -------------- ----------
Repair True             00:06:23    Running   65              11477975040    17448304640
Repair True             00:06:40    Running   66              15987900416    23890755584
Repair True             00:06:52    Running   68              20104802841    22104819713
```

In der Spalte **BytesTotal** wird angezeigt, wie viel Speicher neu synchronisiert werden muss. In der Spalte **PercentComplete** wird der Status angezeigt.

   > [!WARNING]
   > Es ist nicht sicher, einen anderen Server offline zu schalten, bevor diese Reparaturaufträge abgeschlossen sind.

Während dieser Zeit werden Ihre Volumes unter **HealthStatus** weiterhin als **Warnung** angezeigt, was normal ist.

Wenn Sie beispielsweise das Cmdlet `Get-VirtualDisk` verwenden, während der Speicher neu synchronisiert wird, wird möglicherweise die folgende Ausgabe angezeigt:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                InService         Warning      True           1 TB
MyVolume2    Mirror                InService         Warning      True           1 TB
MyVolume3    Mirror                InService         Warning      True           1 TB
```

Sobald die Aufträge abgeschlossen sind, vergewissern Sie sich, dass mit dem Cmdlet `Get-VirtualDisk` erneut, dass die Volumes als **Fehlerfrei** angezeigt werden. Hier ist eine Beispielausgabe angegeben:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                OK                Healthy      True           1 TB
MyVolume2    Mirror                OK                Healthy      True           1 TB
MyVolume3    Mirror                OK                Healthy      True           1 TB
```

Nun können andere Server im Cluster sicher angehalten und neu gestartet werden.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Direkte Speicherplätze – Übersicht](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Aktualisieren von Azure Stack HCI-Clustern](update-cluster.md)