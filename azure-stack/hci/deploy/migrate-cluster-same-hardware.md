---
title: Migrieren zu Azure Stack HCI auf derselben Hardware
description: Hier erfahren Sie, wie Sie einen Cluster auf derselben Hardware zu Azure Stack HCI migrieren.
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 35c1de7da10fbecbf6b861a23cdebb752502ca44
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772277"
---
# <a name="migrate-to-azure-stack-hci-on-same-hardware"></a>Migrieren zu Azure Stack HCI auf derselben Hardware

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016; Windows Server 2012 R2; Windows Server 2008 R2.

In diesem Thema wird beschrieben, wie ein Windows Server 2016- oder Windows Server 2019-Cluster unter Verwendung Ihrer vorhandenen Serverhardware zu Azure Stack HCI migriert wird. Bei diesem Vorgang wird das neue Azure Stack HCI-Betriebssystem installiert. Die vorhandenen Clustereinstellungen und der Speicher werden beibehalten. VMs werden importiert.

Auf der folgenden Abbildung wird die direkte Migration Ihres Windows Server-Clusters mithilfe derselben Serverhardware dargestellt. Nach dem Herunterfahren des Clusters wird die Azure Stack HCI installiert, der Speicher wird erneut angefügt, und Ihre VMs werden importiert und hochverfügbar gemacht (HA).

:::image type="content" source="media/migrate/migrate-cluster-same-hardware.png" alt-text="Migrieren eines Clusters zu Azure Stack HCI auf derselben Hardware" lightbox="media/migrate/migrate-cluster-same-hardware.png":::

Informationen zum Migrieren Ihrer VMs auf neue Azure Stack HCI-Hardware finden Sie unter [Migrieren zu Azure Stack HCI auf neuer Hardware](migrate-cluster-new-hardware.md).

> [!NOTE]
> Das Migrieren von Stretched Cluster wird in diesem Artikel nicht behandelt.

## <a name="before-you-begin"></a>Vorbereitung

Es gibt verschiedene Anforderungen und Aspekte, die Sie berücksichtigen sollten, bevor Sie mit der Migration beginnen:

- Alle Windows PowerShell-Befehle müssen als Administrator ausgeführt werden.

- Sie müssen über Domänenanmeldeinformationen mit Administratorberechtigungen für Azure Stack HCI verfügen.

- Sichern Sie alle VMs im Quellcluster. Führen Sie eine absturzkonsistente Sicherung aller Anwendungen und Daten sowie eine anwendungskonsistente Sicherung aller Datenbanken aus.  Informationen zum Sichern in Azure finden Sie unter [Verwenden von Azure Backup](../manage/use-azure-backup.md).

- Sammeln Sie Inventur- und Konfigurationsinformationen für alle Clusterknoten und Clusternamen, die Netzwerkkonfiguration, Resilienz und Kapazität von CSV (Cluster Shared Volume, freigegebenes Clustervolume) sowie einen Quorumzeugen.

- Fahren Sie Ihre Cluster-VMs, Offline-CSVs, Offlinespeicherpools und den Clusterdienst herunter.
- Deaktivieren Sie das Clusternamenobjekt (CNO) (es wird später wieder verwendet) und:
    - Überprüfen Sie, ob das CNO über die Berechtigung zum Erstellen von Objekten für seine eigene Organisationseinheit verfügt.
    - Überprüfen Sie, ob die Richtlinie „Vererbung deaktivieren“ für die Organisationseinheit festgelegt wurde.
    - Legen Sie die erforderliche Richtlinie für Azure Stack HCI in dieser Organisationseinheit fest.

## <a name="vm-version-support-and-update"></a>Unterstützung und Aktualisieren von VM-Versionen

In der folgenden Tabelle sind die unterstützten Windows Server-Betriebssystemversionen und deren VM-Versionen für die direkte Migration auf derselben Hardware aufgeführt.

Unabhängig von der Betriebssystemversion, auf der eine VM möglicherweise ausgeführt wird, ist die VM-Mindestversion, die für die Migration zu Azure Stack HCI unterstützt wird, die Version 5.0. Das heißt, alle VMs, die auf dem Windows Server 2016- oder Windows Server 2019-Cluster unter Version 2.0, 3.0 oder 4.0 ausgeführt werden, müssen vor der Migration auf Version 5.0 aktualisiert werden.

|Betriebssystemversion|VM-Version|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4,0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

Bei VMs unter Windows Server 2008 SP1-, Windows Server 2008 R2 SP1- und Windows 2012-Clustern wird die direkte Migration zu Azure Stack HCI nicht unterstützt. In diesen Fällen haben Sie zwei Möglichkeiten:

- Migrieren Sie diese VMs zuerst zu Windows Server 2012 R2, Windows Server 2016 oder Windows Server 2019, aktualisieren Sie die VM-Version, und beginnen Sie dann mit dem Migrationsvorgang.

- Verwenden Sie Robocopy, um alle VM-VHDs in Azure Stack HCI zu kopieren. Erstellen Sie dann neue VMs, und fügen Sie die kopierten VHDs an die entsprechenden VMs in Azure Stack HCI an. Dadurch wird die Einschränkung hinsichtlich der VM-Version für diese älteren VMs umgangen.

## <a name="updating-the-vm-version"></a>Aktualisieren der VM-Version

Verwenden Sie den folgenden Befehl, um alle VM-Versionen auf einem einzelnen Server anzuzeigen:

```powershell
Get-VM * | Format-Table Name,Version
```

So zeigen Sie alle VM-Versionen für alle Knoten in Ihrem Windows Server-Cluster an

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

So aktualisieren Sie alle VMs auf allen Windows Server-Knoten auf die neueste Version

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="updating-the-servers-and-cluster"></a>Aktualisieren von Servern und Clustern

Die Migration umfasst die Ausführung des Azure Stack HCI-Setups in Ihrer Windows Server-Bereitstellung für eine frische Betriebssysteminstallation, bei der Ihre VMs und der Speicher beibehalten werden. Dabei wird das aktuelle Betriebssystem durch Azure Stack HCI ersetzt. Ausführliche Informationen finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md). Anschließend erstellen Sie einen neuen Azure Stack HCI-Cluster, fügen den Speicher erneut an und importieren die VMs.

1. Fahren Sie Ihre vorhandenen Cluster-VMs, Offline-CSVs, Offlinespeicherpools und den Clusterdienst herunter.

1. Wechseln Sie zum Speicherort, an den Sie die Azure Stack HCI-Bits heruntergeladen haben, und führen Sie dann Azure Stack HCI-Setup auf jedem einzelnen Windows Server-Knoten aus.

1. Wählen Sie während des Setups **Custom: Install the newer version of Azure Stack HCI only (Advanced)** (Benutzerdefiniert: Nur die neuere Version von Azure Stack HCI installieren (erweitert)) aus. Wiederholen Sie dies für jeden Server.

1. Erstellen Sie den neuen Azure Stack HCI-Cluster. Hierfür können Sie das Windows Admin Center oder Windows PowerShell verwenden, wie unten beschrieben.  

> [!IMPORTANT]
> Der Name des virtuellen Hyper-V-Switchs (`VMSwitch`) muss mit dem Namen übereinstimmen, der in der Clusterkonfigurationsinventur notiert wurde. Stellen Sie sicher, dass der Name des virtuellen Switchs, der für den Azure Stack HCI-Cluster verwendet wird, mit dem Namen des ursprünglichen virtuellen Quellswitchs übereinstimmt, bevor Sie die VMs importieren.

> [!NOTE]
> Sie müssen den Azure Stack HCI-Cluster bei Azure registrieren, bevor Sie neue VMs im Cluster erstellen können. Weitere Informationen finden Sie unter [Registrieren bei Azure](register-with-azure.md).

### <a name="using-windows-admin-center"></a>Verwenden von Windows Admin Center

Wenn Sie das Windows Admin Center verwenden, um den Azure Stack HCI-Cluster zu erstellen, werden alle erforderlichen Rollen und Features automatisch vom Clustererstellungs-Assistenten auf den einzelnen Serverknoten installiert.

Ausführliche Informationen zum Erstellen des Clusters finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](create-cluster.md).

> [!IMPORTANT]
> Überspringen Sie im Clustererstellungs-Assistenten den Schritt **4.1 Clean drives** (Laufwerke bereinigen). Andernfalls werden die vorhandenen VMs und der Speicher gelöscht.

1. Starten Sie den Clustererstellungs-Assistenten. Wenn Sie bei **Schritt 4: Speicher** angelangt sind:

1. Überspringen Sie **4.1 Clean drives** (Laufwerke bereinigen). Führen Sie diesen Schritt nicht aus.

1. Nehmen Sie für den Moment keine weiteren Aktionen im Assistenten vor.

1. Öffnen Sie PowerShell, und führen Sie das folgende Cmdlet aus, um die neue ID `Storagesubsystem Object` zu erstellen, alle Speichergehäuse wieder zu erfassen und die SES-Laufwerknummern zuzuweisen:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

    Bei einer Migration von Windows Server 2016 wird dadurch auch das neue ReFS-Volume `ClusterperformanceHistory` erstellt und der SDDC-Clusterressourcengruppe zugewiesen.

    Bei einer Migration von Windows Server 2019 wird dadurch auch das neue ReFS-Volume `ClusterperformanceHistory` erstellt und der SDDC-Clusterressourcengruppe zugewiesen.

1. Kehren Sie zum Assistenten zurück. Überprüfen Sie in Schritt **4.2 Verify drives** (Laufwerke überprüfen), dass alle Laufwerke ohne Warnungen und Fehler aufgeführt werden.

1. Schließen Sie den Assistenten ab.

### <a name="using-windows-powershell"></a>Verwenden von Windows PowerShell

Wenn Sie PowerShell verwenden, um den Azure Stack HCI-Cluster zu erstellen, müssen die folgenden Rollen und Features mit dem folgenden Cmdlet auf jedem Azure Stack HCI-Clusterknoten installiert werden:

```powershell
Install-WindowsFeature -Name Hyper-V, Failover-Clustering, FS-Data-Deduplication, Bitlocker, Data-Center-Bridging, RSAT-AD-PowerShell -IncludeAllSubFeature -IncludeManagementTools -Verbose
```

Ausführliche Informationen zum Erstellen des Clusters mit PowerShell finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](create-cluster-powershell.md).

> [!NOTE]
> Verwenden Sie den gleichen Namen wie für das zuvor deaktivierte Clusternamenobjekt.

1. Verwenden Sie das folgende Cmdlet, um den Cluster zu erstellen:

    ```powershell
    New-cluster –name "clustername" –node Server01,Server02 –staticaddress xx.xx.xx.xx –nostorage
    ```

1. Führen Sie das folgende Cmdlet aus, um die neue ID `Storagesubsystem Object` zu erstellen, alle Speichergehäuse wieder zu erfassen und die SES-Laufwerknummern zuzuweisen:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

1. Bei einer Migration von Windows Server 2016 wird dadurch auch das neue ReFS-Volume `ClusterperformanceHistory` erstellt und der SDDC-Clusterressourcengruppe zugewiesen.

    > [!NOTE]
    > Wenn bei einem Speicherpool angezeigt wird, dass eine Minderheit der Datenträger fehlerhaft ist (im Cluster-Manager sichtbar), führen Sie das Cmdlet `Enable-ClusterS2D -verbose` erneut aus.

1. Aktivieren Sie mithilfe des Cluster-Managers alle CSV-Instanzen mit Ausnahme des Volumes `ClusterperformanceHistory`, bei dem es sich um ein ReFS-Volume handelt. (Stellen Sie sicher, dass es kein ReFS-CSV ist.)

1. Wenn Sie von Windows Server 2019 migrieren, führen Sie das Cmdlet `Enable-ClusterS2D -verbose` erneut aus. Dadurch wird das ReFS-Volume `ClusterperformanceHistory` mit der SDDC-Clusterressourcengruppe verknüpft.

1. Bestimmen Sie den aktuellen Namen und die Version des Speicherpools, indem Sie Folgendes ausführen:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Bestimmen Sie nun den Namen und die Version des neuen Speicherpools:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Erstellen Sie den Quorumzeugen. Weitere Informationen zur Vorgehensweise finden Sie unter [Einrichten eines Clusterzeugen](../manage/witness.md).

1. Überprüfen Sie mithilfe des folgenden Befehls, ob Speicherreparaturaufträge abgeschlossen wurden:

    ```powershell
    Get-StorageJob
    ```

    > [!NOTE]
    >Dies kann je nach Anzahl der VMs, die während des Upgrades ausgeführt werden, einige Zeit in Anspruch nehmen.

1. Überprüfen Sie, ob alle Datenträger fehlerfrei sind:

    ```powershell
    Get-VirtualDisk
    ```

1. Bestimmen Sie die Clusterknotenversion, die unter `ClusterFunctionalLevel` und `ClusterUpgradeVersion` angezeigt wird. Führen Sie zum Abrufen Folgendes aus:

    ```powershell
    Get-ClusterNodeSupportedVersion
    ```

    > [!NOTE]
    > `ClusterFunctionalLevel` wird automatisch auf `10` festgelegt und muss aufgrund des neuen Betriebssystems und der Clustererstellung nicht aktualisiert werden.

1. Aktualisieren Sie den Speicherpool wie folgt:

    ```powershell
    Get-StoragePool | Update-StoragePool
    ```

## <a name="refs-volumes"></a>ReFS-Volumes

Wenn Sie von Windows Server 2016 migrieren, werden ReFS-Volumes (Resilient File System, robustes Dateisystem) unterstützt, bei solchen Volumes profitieren Sie jedoch nicht von den folgenden Leistungsverbesserungen in Azure Stack HCI:

- Parität mit Beschleunigung per Spiegelung
- MAP-Protokollumgehung

Diese Verbesserungen erfordern, dass ein neues ReFS-Volume mit dem Cmdlet `New-Volume` erstellt wird.

> [!NOTE]
> Bei Windows Server 2016-Volumes mit Parität mit Beschleunigung per Spiegelung war die ReFS-Komprimierung nicht verfügbar. Daher können diese Volumes problemlos wieder angefügt werden, sie sind jedoch weniger leistungsfähig als bei Erstellung eines neuen MAP-Volumes auf einem Azure Stack HCI-Cluster.

## <a name="import-the-vms"></a>Importieren der VMs

Eine bewährte Vorgehensweise besteht darin, mindestens ein CSV (Cluster Shared Volume) pro Clusterknoten zu erstellen, um ausgeglichene VMs für jeden CSV-Besitzer zu ermöglichen und so die Resilienz, die Leistung und die Skalierbarkeit von VM-Workloads zu erhöhen. Standardmäßig erfolgt dieser Ausgleich automatisch alle fünf Minuten. Dies muss berücksichtigt werden, wenn Robocopy zwischen einem Quellclusterknoten und dem Zielclusterknoten verwendet wird, um sicherzustellen, dass Quell- und Ziel-CSV-Besitzer übereinstimmen, damit Übertragungspfad und Geschwindigkeit optimiert werden.

Führen Sie die folgenden Schritte auf Ihrem Azure Stack HCI-Cluster aus, um die VMs zu importieren, Hochverfügbarkeit sicherzustellen und sie zu starten:

1. Führen Sie das folgende Cmdlet aus, um alle CSV-Besitzerknoten anzuzeigen:

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Wechseln Sie auf jedem Serverknoten zu `C:\Clusterstorage\Volume`, und legen Sie den Pfad für alle VMs fest, z. B. `C:\Clusterstorage\volume01`.

1. Führen Sie das folgende Cmdlet auf jedem CSV-Besitzerknoten aus, um vor dem VM-Import den Pfad zu allen VM-VMCX-Dateien pro Volume anzuzeigen. Passen Sie den Pfad Ihrer Umgebung entsprechend an:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

1. Führen Sie das folgende Cmdlet auf jedem Serverknoten aus, um alle VMs zu importieren und zu registrieren und die Hochverfügbarkeit auf jedem CSV-Besitzerknoten sicherzustellen. Dadurch wird eine gleichmäßige Verteilung der VMs für eine optimale Prozessor- und Speicherzuordnung sichergestellt:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Starten Sie die jeweiligen VMs auf den einzelnen Knoten:

    ```powershell
    Start-VM -Name
    ```

1. Melden Sie sich an, und überprüfen Sie, ob alle VMs ausgeführt werden und alle Ihre Apps und Daten vorhanden sind:

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Aktualisieren Sie zum Schluss Ihre VMs auf die neueste Azure Stack HCI-Version, um von allen Verbesserungen zu profitieren:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie den Cluster nach der Migration. Mehr dazu finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](validate.md).
- Informationen zum Migrieren von Windows Server-VMs auf neue Azure Stack HCI-Hardware finden Sie unter [Migrieren zu Azure Stack HCI auf neuer Hardware](migrate-cluster-new-hardware.md).