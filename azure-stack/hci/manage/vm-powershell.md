---
title: Verwalten von VMs in Azure Stack HCI mithilfe von Windows PowerShell
description: Hier erfahren Sie, wie Sie mithilfe von Windows PowerShell VMs in Azure Stack HCI verwalten.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: bbb77e9d96daa3f7f6eefd88812a11656e2acc5d
ms.sourcegitcommit: ad6bbb611ac671b295568d3f00a193b783470c68
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87397429"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>Verwalten von VMs in Azure Stack HCI mithilfe von Windows PowerShell

> Gilt für die Azure Stack HCI, Version 20H2; Windows Server 2019

Mit Windows PowerShell können Sie um virtuelle Computer (Virtual Machines, VMs) in Azure Stack HCI erstellen und verwalten.

In der Regel verwalten Sie VMs von einem Remotecomputer und nicht auf einem Hostserver in einem Cluster. Dieser Remotecomputer wird als Verwaltungscomputer bezeichnet.

> [!NOTE]
> Beim Ausführen von PowerShell-Befehlen von einem Verwaltungscomputer schließen Sie den -ComputerName-Parameter mit dem Namen des verwalteten Hostservers ein. NetBIOS-Namen, IP-Adressen und vollqualifizierte Domänennamen sind zulässig.

Die komplette Referenzdokumentation zum Verwalten von VMs mithilfe von PowerShell finden Sie unter [Hyper-V-Referenz](/powershell/module/hyper-v/?view=win10-ps).

## <a name="create-a-vm"></a>Erstellen einer VM  

Mit dem Cmdlet `New-VM` wird eine neue VM erstellt. Ausführliche Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [New-VM](/powershell/module/hyper-v/new-vm?view=win10-ps).

Dies sind die Einstellungen, die Sie beim Erstellen einer neuen VM mit einer vorhandenen virtuellen Festplatte angeben können. Dabei gilt Folgendes:

- **-Name** ist der Name, den Sie für den erstellten virtuellen Computer angeben.

- **-MemoryStartupBytes** ist die Menge an Arbeitsspeicher, die für den virtuellen Computer beim Starten verfügbar ist.

- **-BootDevice** ist das Gerät, auf dem der virtuelle Computer beim Hochfahren gestartet wird.
 Im Allgemeinen handelt es sich um eine virtuelle Festplatte (VHD), eine ISO-Datei für einen Startvorgang von DVD oder einen Netzwerkadapter (NetworkAdapter) für einen Netzwerkstart.

- **-VHDPath** ist der Pfad zum VM-Datenträger, der verwendet werden soll.

- **-Path** ist der Pfad, unter dem die VM-Konfigurationsdateien gespeichert werden sollen.

- **-Generation** ist die VM-Generation. Verwenden Sie Generation 1 für VHD und Generation 2 für VHDX.

- **-Switch** ist der Name des virtuellen Switches, über den der virtuelle Computer eine Verbindung mit anderen virtuellen Computern oder dem Netzwerk herstellen soll. Rufen Sie den Namen des virtuellen Switches mit [Get-VMSwitch](/powershell/module/hyper-v/get-vmswitch?view=win10-ps) ab. Beispiel:  

Der vollständige Befehl zum Erstellen einer VM namens VM1 lautet wie folgt:

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

Im nächsten Beispiel wird ein virtueller Computer der Generation 2 mit 4 GB Arbeitsspeicher erstellt. Er startet aus dem Ordner „VMs\Win10.vhdx“ im aktuellen Verzeichnis und verwendet den virtuellen Switch „ExternalSwitch“. Die VM-Konfigurationsdateien werden im Ordner „VMData“ gespeichert.  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

Mit den folgenden Parametern werden virtuelle Festplatten angegeben.

Ersetzen Sie zum Erstellen eines virtuellen Computers mit einer neuen virtuellen Festplatte den Parameter **-VHDPath** aus dem obigen Beispiel durch **-NewVHDPath**, und fügen Sie den Parameter **-NewVHDSizeBytes** wie hier gezeigt hinzu:  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

Wenn Sie einen virtuellen Computer mit einer neuen virtuellen Festplatte erstellen möchten, der mit einem Betriebssystemimage startet, informieren Sie sich im PowerShell-Beispiel unter [Erstellen eines virtuellen Computers auf Windows 10 mit Hyper-V](/virtualization/hyper-v-on-windows/quick-start/create-virtual-machine).  

## <a name="get-a-list-of-vms"></a>Abrufen einer Liste von VMs

Im folgenden Beispiel wird eine Liste aller VMs auf Server1 zurückgegeben.

```powershell
Get-VM -ComputerName Server1
```

Im folgenden Beispiel wird eine Liste aller ausgeführten VMs auf einem Server zurückgegeben, indem mit dem `Where-Object`-Befehl ein Filter hinzugefügt wird. Weitere Informationen finden Sie in der Dokumentation zum [Verwenden von „Where-Object“](/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)).

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

Im nächsten Beispiel wird eine Liste aller heruntergefahrenen VMs auf dem Server zurückgegeben.

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>Starten und Beenden einer VM

Starten und beenden Sie eine VM mit den Befehlen `Start-VM` und `Stop-VM`. Ausführliche Informationen finden Sie in der Referenzdokumentation zu [Start-VM](/powershell/module/hyper-v/start-vm?view=win10-ps) und [Stop-VM](/powershell/module/hyper-v/stop-vm?view=win10-ps).

Im folgenden Beispiel wird veranschaulicht, wie Sie eine VM namens VM1 starten:

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

Im folgenden Beispiel wird veranschaulicht, wie Sie eine VM namens TestVM herunterfahren:

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>Verschieben eines virtuellen Computers

Das Cmdlet `Move-VM` verschiebt eine VM auf einen anderen Server. Weitere Informationen finden Sie in der Referenzdokumentation zu [Move-VM](/powershell/module/hyper-v/move-vm?view=win10-ps).

 Im folgenden Beispiel wird veranschaulicht, wie Sie eine VM auf Server2 verschieben, wenn die VM auf einer SMB-Freigabe auf Server1 gespeichert ist:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

Das folgende Beispiel zeigt, wie Sie eine VM von Server1 auf Server2 verschieben und alle der VM zugeordneten Dateien auf dem Remotecomputer auf „D:\VM_name“ verschieben:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>Importieren oder Exportieren einer VM

Mit den Cmdlets `Import-VM` und `Export-VM` importieren und exportieren Sie eine VM. Im Folgenden finden Sie einige Beispiele. Weitere Informationen finden Sie in der Referenzdokumentation zu [Import-VM](/powershell/module/hyper-v/import-vm?view=win10-ps) und [Export-VM](/powershell/module/hyper-v/export-vm?view=win10-ps).

Im folgenden Beispiel wird gezeigt, wie Sie eine VM aus ihrer Konfigurationsdatei importieren. Die VM wird direkt registriert, daher werden ihre Dateien nicht kopiert:

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

Im folgenden Beispiel wird eine VM in den Stamm des Laufwerks „D“ exportiert:

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>Umbenennen einer VM

Mit dem Cmdlet `Rename-VM` können Sie eine VM umbenennen. Ausführliche Informationen finden Sie in der Referenzdokumentation zu [Rename-VM](/powershell/module/hyper-v/rename-vm?view=win10-ps).

Im folgenden Beispiel wird VM1 in VM2 umbenannt, und der umbenannte virtuelle Computer wird angezeigt:

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>Erstellen eines VM-Prüfpunkts

Mit dem Cmdlet `Checkpoint-VM` erstellen Sie einen Prüfpunkt für eine VM. Ausführliche Informationen finden Sie in der Referenzdokumentation zu [Checkpoint-VM](/powershell/module/hyper-v/checkpoint-vm?view=win10-ps).

Im folgenden Beispiel wird der Prüfpunkt „BeforeInstallingUpdates“ für die VM „Test“ erstellt.

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>Erstellen einer VHD für eine VM

Mit dem Cmdlet `New-VHD` wird eine neue virtuelle Festplatte (VHD) für eine VM erstellt. Ausführliche Informationen zu seiner Verwendung finden Sie in der Referenzdokumentation zu [New-VHD](/powershell/module/hyper-v/new-vhd?view=win10-ps).

Im folgenden Beispiel wird eine dynamische virtuelle Festplatte im VHDX-Format mit einer Größe von 10 GB erstellt. Die Dateinamenerweiterung bestimmt das Format, und der Standardtyp „dynamisch“ wird verwendet, weil kein Typ angegeben ist.

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>Hinzufügen eines Netzwerkadapters zu einer VM

Mit dem Cmdlet `Add-VMNetworkAdapter` fügen Sie einer VM einen virtuellen Netzwerkadapter hinzu. Im Folgenden finden Sie einige Beispiele. Ausführliche Informationen zu seiner Verwendung finden Sie in der Referenzdokumentation zu [Add-VMNetworkAdapter](/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps).

Im folgenden Beispiel wird dem virtuellen Computer VM1 der virtuelle Netzwerkadapter „Redmond NIC1“ hinzugefügt:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

In diesem Beispiel wird dem virtuellen Computer VM1 ein virtueller Netzwerkadapter hinzugefügt, und er wird mit einem virtuellen Switch namens „Network“ verbunden:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>Erstellen eines virtuellen Switches für eine VM

Mit dem Cmdlet `New-VMSwitch` wird ein neuer virtueller Switch für einen VM-Host erstellt. Ausführliche Informationen zu seiner Verwendung finden Sie in der Referenzdokumentation zu [New-VMSwitch](/powershell/module/hyper-v/new-vmswitch?view=win10-ps).

Im folgenden Beispiel wird der neue Switch „QoS switch“ erstellt. Dieser wird an einen Netzwerkadapter namens „Wired Ethernet Connection 3“ gebunden und unterstützt eine auf Gewichtung beruhende Mindestbandbreite.

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>Festlegen des Arbeitsspeichers für eine VM

Mit dem Cmdlet `Set-VMMemory` konfigurieren Sie den Arbeitsspeicher für eine VM. Ausführliche Informationen zu seiner Verwendung finden Sie in der Referenzdokumentation zu [Set-VMMemory](/powershell/module/hyper-v/set-vmmemory?view=win10-ps).

Im folgenden Beispiel wird dynamischer Arbeitsspeicher für eine VM namens VM1 aktiviert. Außerdem werden der minimale, Start- und maximale Arbeitsspeicher sowie die Arbeitsspeicherpriorität und der Puffer festgelegt.

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>Festlegen virtueller Prozessoren für eine VM

Mit dem Cmdlet `Set-VMProcessor` werden virtuelle Prozessoren für eine VM konfiguriert. Ausführliche Informationen zu seiner Verwendung finden Sie in der Referenzdokumentation zu [Set-VMProcessor](/powershell/module/hyper-v/set-vmprocessor?view=win10-ps).

Im folgenden Beispiel wird eine VM namens VM1 mit zwei virtuellen Prozessoren, einer Reserve von 10 %, einem Limit von 75 % und einer relativen Gewichtung von 200 konfiguriert.

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="next-steps"></a>Nächste Schritte  

Sie können VMs auch mit dem Windows Admin Center erstellen und verwalten. Weitere Informationen finden Sie unter [Windows Admin Center](vm.md).
