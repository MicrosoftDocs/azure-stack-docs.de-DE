---
title: Migrieren zu Azure Stack HCI auf neuer Hardware
description: Hier erfahren Sie, wie Sie auf neuer Hardware zu Azure Stack HCI migrieren.
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7a0c0ca7a99b3554b74cc80911acbee92793aa52
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254822"
---
# <a name="migrate-to-azure-stack-hci-on-new-hardware"></a>Migrieren zu Azure Stack HCI auf neuer Hardware

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016; Windows Server 2012 R2; Windows Server 2008 R2.

In diesem Thema wird beschrieben, wie Sie mithilfe von Windows PowerShell und Robocopy Dateien für virtuelle Computer (VM) unter Windows Server 2012 R2, Windows Server 2016 oder Windows Server 2019 auf neue Azure Stack HCI-Serverhardware migrieren. Robocopy ist eine robuste Methode zum Kopieren von Dateien von einem Server zu einem anderen. Der Vorgang wird vom letzten bekannten Zustand aus fortgesetzt, falls die Verbindung getrennt wird. Robocopy unterstützt auch Multithread-Dateikopiervorgänge über SMB (Server Message Block). Weitere Informationen finden Sie unter [Robocopy](/windows-server/administration/windows-commands/robocopy).

> [!NOTE]
> Hyper-V-Livemigration und Hyper-V-Replikat von Windows Server zu Azure Stack HCI werden nicht unterstützt.

Wenn Sie über VMs unter Windows 2012 R2 oder früher verfügen, die Sie migrieren möchten, finden Sie weitere Informationen unter [Migrieren älterer virtueller Computer](#migrating-older-vms).

Informationen zum Migrieren Ihrer VMs auf der gleichen Hardware finden Sie unter [Migrieren zu Azure Stack HCI auf derselben Hardware](migrate-cluster-same-hardware.md).

Im folgenden Diagramm sehen Sie ein Beispiel für einen Windows Server-Quellcluster und einen Azure Stack HCI-Zielcluster. Sie können VMs auch auf eigenständige Server migrieren.

:::image type="content" source="media/migrate/migrate-cluster.png" alt-text="Migrieren eines Clusters zu Azure Stack HCI" lightbox="media/migrate/migrate-cluster.png":::

Im Hinblick auf die zu erwartende Ausfallzeit können Sie mit einer Übertragungsgeschwindigkeit von 1,9 TB pro Stunde rechnen, wenn Sie einen einzelnen NIC mit dualem 40 GB-RDMA-Ost-West-Netzwerk zwischen Clustern verwenden und Robocopy für 32 Multithreads konfigurieren.

> [!NOTE]
> Das Migrieren von VMs für Stretched Cluster wird in diesem Artikel nicht behandelt.

## <a name="before-you-begin"></a>Vorbereitung

Es gibt verschiedene Anforderungen und Aspekte, die Sie berücksichtigen sollten, bevor Sie mit der Migration beginnen:

- Alle Windows PowerShell-Befehle müssen als Administrator ausgeführt werden.

- Sie benötigen Domänenanmeldeinformationen mit Administratorberechtigungen für Quell- und Zielcluster mit Vollzugriff auf die Quell- und Zielorganisationseinheit, die beide Cluster enthält.

- Beide Cluster müssen sich in derselben Active Directory-Gesamtstruktur und -Domäne befinden, um für die Migration von VMs eine Kerberos-Authentifizierung zwischen Clustern zu ermöglichen.

- Beide Cluster müssen sich in einer Active Directory-Organisationseinheit befinden, wobei das Gruppenrichtlinienobjekt (GPO) „Vererbung deaktivieren“ für diese Organisationseinheit festgelegt ist. Dadurch wird sichergestellt, dass die Migration nicht durch Gruppenrichtlinienobjekten auf Domänenebene und Sicherheitsrichtlinien beeinträchtigt wird.

- Beide Cluster müssen mit derselben Zeitquelle verbunden sein, um eine konsistente Kerberos-Authentifizierung zwischen Clustern zu unterstützen.

- Notieren Sie sich den Namen des virtuellen Hyper-V-Switchs, der von den VMs im Quellcluster verwendet wird. Sie müssen für den Azure Stack HCI-Zielcluster „Netzwerk für virtuelle Computer“ den gleichen Namen des virtuellen Switchs verwenden, bevor Sie virtuelle Computer importieren.

- Entfernen Sie alle ISO-Imagedateien für Ihre Quell-VMs. Diesen Schritt führen Sie im Hyper-V-Manager im Abschnitt **Hardware** unter **Eigenschaften virtueller Computer** aus. Wählen Sie bei allen virtuellen CD/DVD-Laufwerken **Entfernen** aus.

- Fahren Sie alle VMs im Quellcluster herunter. Dies ist erforderlich, um sicherzustellen, dass die Versionskontrolle und der Status während des gesamten Migrationsprozesses beibehalten werden.

- Überprüfen Sie, ob Azure Stack HCI Ihre Version der VMs unterstützt, damit Ihre VMs nach Bedarf importiert und zu aktualisiert werden können. Weitere Informationen hierzu finden Sie im Abschnitt [Unterstützung und Aktualisieren von VM-Versionen](#vm-version-support-and-update).

- Sichern Sie alle VMs im Quellcluster. Führen Sie eine absturzkonsistente Sicherung aller Anwendungen und Daten sowie eine anwendungskonsistente Sicherung aller Datenbanken aus. Informationen zum Sichern in Azure finden Sie unter [Verwenden von Azure Backup](../manage/use-azure-backup.md).

- Erstellen Sie einen Prüfpunkt für die Quellcluster-VMs und den Domänencontroller für den Fall, dass ein Rollback auf einen vorherigen Zustand ausgeführt werden muss. Dies gilt nicht für physische Server.

- Stellen Sie sicher, dass die Höchstgröße von Großrahmen zwischen Quell- und Zielcluster-Speichernetzwerken identisch ist. Dies gilt insbesondere für RDMA-Netzwerkadapter und ihre entsprechenden Switchnetzwerkports, damit die effizienteste End-to-End-Übertragungspaketgröße verwendet wird.

- Notieren Sie sich den Namen des virtuellen Hyper-V-Switchs im Quellcluster. Dieser wird später im Zielcluster verwendet.

- Die Azure Stack HCI-Hardware sollte mindestens über die gleiche Kapazität und Konfiguration wie die Quellhardware verfügen.

- Minimieren Sie die Anzahl der Netzwerkhops oder die physische Entfernung zwischen Quell- und Zielcluster, damit die Dateiübertragung möglichst schnell erfolgt.

## <a name="vm-version-support-and-update"></a>Unterstützung und Aktualisieren von VM-Versionen

In dieser Tabelle sind die Versionen der Windows Server-Betriebssysteme und die entsprechenden VM-Versionen aufgelistet.

Unabhängig von der Betriebssystemversion, auf der eine VM möglicherweise ausgeführt wird, ist die VM-Mindestversion, die für die direkte Migration zu Azure Stack HCI unterstützt wird, die Version 5.0. Dies ist die Standardversion für VMs unter Windows Server 2012 R2. Daher müssen alle VMs, die unter Version 2.0, 3.0 oder 4.0 ausgeführt werden, vor der Migration auf Version 5.0 aktualisiert werden.

|Betriebssystemversion|VM-Version|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4,0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

Aktualisieren Sie bei VMs unter Windows Server 2012 R2, Windows Server 2016 und Windows Server 2019 alle VMs zuerst auf die neueste VM-Version, die auf der Quellhardware unterstützt wird, bevor Sie das Robocopy-Migrationsskript ausführen. Dadurch wird sichergestellt, dass alle VMs mindestens Version 5.0 aufweisen und der VM-Import erfolgreich ausgeführt werden kann.

Bei VMs unter Windows Server 2008 SP1, Windows Server 2008 R2 SP1 und Windows 2012 ist die VM-Version kleiner als Version 5.0. Diese VMs verwenden für die Konfiguration außerdem eine XML-Datei anstelle einer VMCX-Datei. Daher wird ein direkter Import der VM in Azure Stack HCI nicht unterstützt. In diesen Fällen haben Sie zwei Möglichkeiten, die in [Migrieren älterer virtueller Computer](#migrating-older-vms) beschrieben sind.

### <a name="updating-the-vm-version"></a>Aktualisieren der VM-Version

Die folgenden Befehle gelten für Windows Server 2012 R2 und höher. Verwenden Sie den folgenden Befehl, um alle VM-Versionen auf einem einzelnen Server anzuzeigen:

```powershell
Get-VM * | Format-Table Name,Version
```

So zeigen Sie alle VM-Versionen auf allen Servern in einem Cluster an

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

So aktualisieren Sie alle VMs auf allen Servern auf die neueste unterstützte Version

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="rdma-recommendations"></a>RDMA-Empfehlungen

Wenn Sie RDMA (Remote Direct Memory Access, Remotezugriff auf den direkten Speicher) verwenden, kann dieser von Robocopy beim Kopieren der VMs zwischen den Clustern genutzt werden. Nachfolgend finden Sie einige Empfehlungen für die Verwendung von RDMA:

- Verbinden Sie beide Cluster mit dem gleichen ToR-Switch (Top of Rack), um den schnellsten Netzwerkpfad zwischen Quell- und Zielcluster zu verwenden. Beim Speichernetzwerkpfad werden dabei normalerweise 10 GbE/25 GbE oder höhere Geschwindigkeiten und RDMA unterstützt.

- Wenn sich der RDMA-Adapter oder -Standard zwischen Quell- und Zielcluster (RoCE bzw. iWARP) unterscheidet, wird von Robocopy stattdessen SMB über TCP/IP über das schnellste verfügbare Netzwerk verwendet. Dabei handelt es sich in der Regel um eine duale Verbindung mit 10 GbE/25 GbE oder einer höheren Geschwindigkeit für das Ost-West-Netzwerk. Dies stellt das Optimum für das Kopieren von VM-VHDX-Dateien zwischen Clustern dar.

- Um sicherzustellen, dass Robocopy RDMA zwischen Clustern (Ost-West-Netzwerk) nutzen kann, konfigurieren Sie RDMA-Speichernetzwerke so, dass sie ein Routing zwischen dem Quell- und dem Zielcluster unterstützen.

## <a name="create-the-new-cluster"></a>Erstellen des neuen Clusters

Bevor Sie den Azure Stack HCI-Cluster erstellen können, müssen Sie das Azure Stack HCI-Betriebssystem auf jedem neuen Server im Cluster installieren. Entsprechende Informationen finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md).

Verwenden Sie das Windows Admin Center oder Windows PowerShell, um den neuen Cluster zu erstellen. Ausführliche Informationen hierzu finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](create-cluster.md) und [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](create-cluster-powershell.md).

> [!IMPORTANT]
> Die Namen virtueller Hyper-V-Switches (`VMSwitch`) zwischen Clustern müssen identisch sein. Stellen Sie sicher, dass die Namen der virtuellen Switches, die auf dem Zielcluster erstellt wurden, auf allen Servern den im Quellcluster verwendeten Namen entsprechen. Überprüfen Sie vor dem Importieren der VMs die Namen der Switchnamen entsprechend.

> [!NOTE]
> Sie müssen den Azure Stack HCI-Cluster bei Azure registrieren, bevor Sie neue VMs im Cluster erstellen können. Weitere Informationen finden Sie unter [Registrieren bei Azure](register-with-azure.md).

## <a name="run-the-migration-script"></a>Führen Sie das Migrationsskript aus.

Im folgenden PowerShell-Skript `Robocopy_Remote_Server_.ps1` wird Robocopy zum Kopieren der VM-Dateien und ihrer abhängigen Verzeichnisse und Metadaten aus dem Quell- in den Zielcluster verwendet. Für dieses Skript wurde das ursprüngliche Skript abgeändert, das sich auf TechNet unter folgendem Link befindet: [Robocopy Files to Remote Server Using PowerShell and RoboCopy](https://gallery.technet.microsoft.com/scriptcenter/Robocoy-Files-to-Remote-bdfc5154) (Kopieren von Dateien mit PowerShell und Robocopy auf Remoteserver).

Das Skript kopiert alle VM-VHD-, VHDX- und VMCX-Dateien für ein bestimmtes CSV (Cluster Shared Volume) in Ihren Zielcluster. Es wird immer nur ein CSV gleichzeitig migriert.

Das Migrationsskript wird lokal auf jedem Quellserver ausgeführt, um die Vorteile von RDMA und der schnellen Netzwerkübertragung zu nutzen. Gehen Sie dazu wie folgt vor:

1. Stellen Sie sicher, dass jeder Zielclusterknoten für das Ziel-CSV auf den CSV-Besitzer festgelegt ist.

1. Verwenden Sie das folgende Cmdlet, um den Speicherort aller VM-VHD- und VHDX-Dateien zu ermitteln, die kopiert werden sollen. In der Datei `C:\vmpaths.txt` finden Sie den Quelldateipfad auf höchster Ebene, bei dem Robocopy für Schritt 4 beginnen soll:

    ```powershell  
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

    > [!NOTE]
    > Wenn sich Ihre VHD- und VHDX-Dateien unter unterschiedlichen Pfaden auf demselben Volume befinden, müssen Sie das Migrationsskript für jeden Pfad separat ausführen, um alles zu kopieren.

1. Ändern Sie die folgenden drei Variablen so, dass sie dem Quellcluster-VM-Pfad bzw. dem Zielcluster-VM-Pfad entsprechen:

    - `$Dest_Server = "Node01"`
    - `$source  = "C:\Clusterstorage\Volume01"`
    - `$dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"`

1. Führen Sie auf jedem Windows Server-Quellserver das folgende Skript aus:

```powershell
<#
#===========================================================================  
# Script: Robocopy_Remote_Server_.ps1
#===========================================================================  
.DESCRIPTION:
Change the following variables to match your source cluster VM path with the destination cluster VM path. Then run this script on each source Cluster Node CSV owner and make sure the destination cluster node is set to the CSV owner for the destination CSV.

        Change $Dest_Server = "Node01"
        Change $source  = "C:\Clusterstorage\Volume01"
        Change $dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"
#>

$Space       = Write-host ""
$Dest_Server = "Node01"
$source      = "C:\Clusterstorage\Volume01"
$dest        = "\\$Dest_Server\C$\Clusterstorage\Volume01"
$Logfile     = "c:\temp\Robocopy1-$date.txt"
$date        = Get-Date -UFormat "%Y%m%d"
$cmdArgs     = @("$source","$dest",$what,$options)  
$what        = @("/COPYALL")
$options     = @("/E","/MT:32","/R:0","/W:1","/NFL","/NDL","/LOG:$logfile","/xf")
 
## Get Start Time
$startDTM = (Get-Date)
 
$Dest_Server     = "Node01"
$TARGETDIR   = \\$Dest_Server\C$\Clusterstorage\Volume01
$Space
Clear
## Provide Information
Write-host ".....Copying Virtual Machines FROM $Source to $TARGETDIR ....................." -fore Green -back black
Write-Host "........................................." -Fore Green

## Kick off the copy with options defined  
robocopy @cmdArgs
 
## Get End Time
$endDTM = (Get-Date)
 
## Echo Time elapsed
$Time = "Elapsed Time: = $(($endDTM-$startDTM).totalminutes) minutes"  
## Provide time it took
Write-host ""
Write-host " Copy Virtual Machines to $Dest_Server has been completed......" -fore Green -back black
Write-host " Copy Virtual Machines to $Dest_Server took $Time        ......" -fore Cyan
```

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

    > [!NOTE]
    > Bei Windows Server 2012 R2- und älteren VMs wird eine XML-Datei anstelle einer VMCX-Datei verwendet. Weitere Informationen finden Sie im Abschnitt **Migrieren älterer virtueller Computer**.

1. Führen Sie das folgende Cmdlet auf jedem Serverknoten aus, um die VMs zu importieren und zu registrieren und die Hochverfügbarkeit der VMs auf jedem CSV-Besitzerknoten sicherzustellen. Dadurch wird eine gleichmäßige Verteilung der VMs für eine optimale Prozessor- und Speicherzuordnung sichergestellt:

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

1. Aktualisieren Sie Ihre VMs auf die neueste Version für Azure Stack HCI, um von allen Verbesserungen zu profitieren:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. Überprüfen Sie nach Abschluss des Skripts die Robocopy-Protokolldatei auf aufgeführte Fehler, und vergewissern Sie sich, dass alle VMs erfolgreich kopiert wurden.

## <a name="migrating-older-vms"></a>Migrieren älterer virtueller Computer

Wenn Sie über Windows Server 2008 SP1-, Windows Server 2008 R2 SP1-, Windows Server 2012- oder Windows Server 2012 R2-VMs verfügen, ist dieser Abschnitt für Sie relevant. Sie haben zwei Möglichkeiten, solche VMs zu verarbeiten:

- Migrieren Sie diese VMs zuerst zu Windows Server 2012 R2, Windows Server 2016 oder Windows Server 2019, aktualisieren Sie die VM-Version, und beginnen Sie dann mit dem Migrationsvorgang.

- Verwenden Sie Robocopy, um alle VM-VHDs in Azure Stack HCI zu kopieren. Erstellen Sie dann neue VMs, und fügen Sie die kopierten VHDs an die VMs in Azure Stack HCI an. Dadurch wird die Einschränkung hinsichtlich der VM-Version für diese älteren VMs umgangen.

Hyper-V-Hosts unter Windows Server 2012 R2 und früher verwenden das XML-Dateiformat für die VM-Konfiguration. Dieses unterscheidet sich von dem für Hyper-V-Hosts unter Windows Server 2016 und höher verwendeten VMCX-Dateiformat. Um diese VMs in Azure Stack HCI zu kopieren, ist ein anderer Robocopy-Befehl erforderlich.

### <a name="option-1-staged-migration"></a>Option 1: Gestaffelte Migration

Hierbei handelt es sich um eine zweistufige Migration für VMs, die unter Windows Server 2008 SP1, Windows Server 2008 R2 SP1 und Windows Server 2012 gehostet werden. Sie verwenden folgenden Prozess:

1. Ermitteln Sie den Speicherort aller zu kopierenden VM-VHD- und VHDX-Dateien, und ermitteln Sie dann in der Datei `vmpaths.txt` den Quelldateipfad auf höchster Ebene, ab dem Robocopy ausgeführt werden soll. Verwenden Sie das folgende Cmdlet:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

1. Verwenden Sie den folgenden Robocopy-Beispielbefehl zum Kopieren von VMs auf Windows Server 2012 R2, indem Sie den Pfad auf höchster Ebene verwenden, den Sie in Schritt 1 ermittelt haben:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Vergewissern Sie sich, dass der Name des virtuellen Switchs (`VMSwitch`), der im Windows Server 2012 R2-Cluster verwendet wird, mit dem Namen des Switchs identisch ist, der in der Windows 2008 R2- oder Windows Server 2008 R2 SP1-Quelle verwendet wird. Verwenden Sie folgenden Befehl, um die Switchnamen anzuzeigen, die auf allen Servern in einem Cluster verwendet werden:

     ```powershell
    Get-VMSwitch -CimSession $Servers | Select-Object Name
    ```

    Benennen Sie den Switchnamen unter Windows Server 20212 R2 bei Bedarf um. Verwenden Sie folgenden Befehl, um die Switchnamen auf allen Servern im Cluster umzubenennen:

    ```powershell
    Invoke-Command -ComputerName $Servers -ScriptBlock {rename-VMSwitch -Name $using:vSwitcholdName -NewName $using:vSwitchnewname}
    ```

1. Kopieren Sie die VMs, und importieren Sie sie in Windows Server 2012 R2:

     ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Aktualisieren Sie unter Windows Server 2012 R2 die VM-Version für alle VMs auf 5.0:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. [Führen Sie das Migrationsskript aus](#run-the-migration-script), um VMs in Azure Stack HCI zu kopieren.

1. Befolgen Sie das Verfahren unter [Importieren der VMs](#import-the-vms), und ersetzen Sie Schritt 3 und 4 durch Folgendes, um die XML-Dateien zu verarbeiten und die VMs in Azure Stack HCI zu importieren:

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Führen Sie die verbleibenden Schritte unter [Importieren der VMs](#import-the-vms) aus.

### <a name="option-2-direct-vhd-copy"></a>Option 2: Direktes Kopieren der VHDs

Bei diesem Verfahren wird Robocopy zum Kopieren von VM-VHDs, die unter Windows 2008 SP1, Windows 2008 R2 SP1 und Windows 2012 gehostet werden, in Azure Stack HCI verwendet. Dadurch wird die Einschränkung hinsichtlich der niedrigsten unterstützten VM-Version für diese älteren VMs umgangen. Diese Option wird für VMs empfohlen, die unter Windows Server 2008 SP1 und Windows Server 2008 R2 SP1 gehostet werden.

VMs, die unter Windows 2008 SP1 und Windows 2008 R2 SP1 gehostet werden, unterstützen nur VMs der Generation 1 mit VHDs der Generation 1. Daher müssen die entsprechenden VMs der Generation 1 auf Azure Stack HCI erstellt werden, damit die kopierten VHDs an die neuen VMs angefügt werden können. Beachten Sie, dass für diese VHDs kein Upgrade auf VHDs der Generation 2 möglich ist.

> [!NOTE]
> Windows Server 2012 unterstützt VMs der Generation 1 und der Generation 2.

Sie verwenden folgenden Prozess:

1. Verwenden Sie das Robocopy-Beispiel, um VMs mit VHDs direkt in Azure Stack HCI zu kopieren:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Erstellen Sie neue VMs der Generation 1. Ausführliche Informationen hierzu finden Sie unter [Verwalten von VMs](../manage/vm.md).

1. Fügen Sie die kopierten VHD-Dateien an die neuen VMs an. Ausführliche Informationen finden Sie unter [Verwalten virtueller Festplatten (VHD)](/windows-server/storage/disk-management/manage-virtual-hard-disks).

Zur Information: Die folgenden Windows Server-Gastbetriebssysteme unterstützen VMs der Generation 2:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 10
- 64-Bit-Versionen von Windows Server 8.1 (64 Bit)
- 64-Bit-Versionen von Windows Server 8 (64 Bit)
- Linux (siehe [Unterstützte virtuelle Linux- und FreeBSD-Computer für Hyper-V unter Windows](/windows-server/virtualization/hyper-v/Supported-Linux-and-FreeBSD-virtual-machines-for-Hyper-V-on-Windows))

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie den Cluster nach der Migration. Mehr dazu finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](validate.md).

- Informationen zum direkten Migrieren Ihrer VMs auf der gleichen Hardware finden Sie unter [Migrieren zu Azure Stack HCI auf derselben Hardware](migrate-cluster-same-hardware.md).