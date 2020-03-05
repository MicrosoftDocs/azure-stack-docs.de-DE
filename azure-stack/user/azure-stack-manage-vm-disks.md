---
title: Erstellen von VM-Datenträgerspeicher in Azure Stack Hub
description: Erstellen Sie Datenträger für VMs in Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 5bcfcb8a5e2af29b0d7b60774853fa35392d9f23
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702481"
---
# <a name="create-vm-disk-storage-in-azure-stack-hub"></a>Erstellen von VM-Datenträgerspeicher in Azure Stack Hub

In diesem Artikel wird beschrieben, wie Sie VM-Datenträgerspeicher im Azure Stack Hub-Portal oder mit PowerShell erstellen.

## <a name="overview"></a>Übersicht

Ab Version 1808 können in Azure Stack Hub verwaltete und nicht verwaltete Datenträger auf VMs sowohl als Betriebssystemdatenträger als auch als Datenträger für Daten verwendet werden. Vor Version 1808 werden nur nicht verwaltete Datenträger unterstützt.

[Verwaltete Datenträger](/azure/virtual-machines/windows/managed-disks-overview) vereinfachen die Datenträgerverwaltung für Azure-IaaS-VMs durch die Verwaltung der Speicherkonten, die den VM-Datenträgern zugeordnet sind. Sie müssen nur die Größe des von Ihnen benötigten Datenträgers angeben. Azure Stack Hub erstellt und verwaltet den Datenträger dann für Sie.

Für nicht verwaltete Datenträger müssen Sie ein Speicherkonto erstellen, unter dem die Datenträger gespeichert werden können. Die von Ihnen erstellten Datenträger werden als VM-Datenträger bezeichnet und in Containern im Speicherkonto gespeichert.

## <a name="best-practice-guidelines"></a>Richtlinien zu bewährten Methoden

Es wird empfohlen, Managed Disks für virtuelle Computer zu verwenden, um die Verwaltung und den Kapazitätsausgleich zu erleichtern. Sie müssen vor der Verwendung von Managed Disks kein Speicherkonto und keine Container vorbereiten. Beim Erstellen mehrerer verwalteter Datenträger werden die Datenträger auf mehrere Volumes verteilt, wodurch die Kapazität der Volumes ausgeglichen werden kann.  

Zur Verbesserung der Leistung und Reduzierung der Gesamtkosten empfehlen wir Ihnen bei der Verwendung von nicht verwalteten Datenträgern, jeden nicht verwalteten Datenträger in einem separaten Container zu platzieren. Sie können Betriebssystem-Datenträger und Datenträger zwar im gleichen Container ablegen, es empfiehlt sich aber, in einem Container entweder einen Betriebssystem-Datenträger oder einen Datenträger zu platzieren und nicht beide gleichzeitig.

Wenn Sie einem virtuellen Computer Datenträger für Daten hinzufügen, sollten Sie als Speicherort für diese Datenträger zusätzliche Container verwenden. Der Betriebssystemdatenträger für zusätzliche virtuelle Computer sollte sich ebenfalls in einem eigenen Container befinden.

Wenn Sie virtuelle Computer erstellen, können Sie für jeden neuen virtuellen Computer das gleiche Speicherkonto wiederverwenden. Lediglich die erstellten Container sollten getrennt sein.

## <a name="adding-new-disks"></a>Hinzufügen von neuen Datenträgern

In der folgenden Tabelle ist zusammengefasst, wie Sie Datenträger über das Portal und mit PowerShell hinzufügen:

| Methode | Tastatur
|-|-|
|Benutzerportal|- Fügen Sie einer vorhandenen VM neue Datenträger für Daten hinzu. Von Azure Stack Hub werden neue Datenträger erstellt. </br> </br> - Fügen Sie eine vorhandene Datenträgerdatei (.vhd) einem vorab erstellten virtuellen Computer hinzu. Hierzu müssen Sie die VHD-Datei vorbereiten und dann in Azure Stack Hub hochladen. |
|[PowerShell](#use-powershell-to-add-multiple-disks-to-a-vm) | - Erstellen Sie einen neuen virtuellen Computer mit einem Betriebssystem-Datenträger, und fügen Sie dem virtuellen Computer gleichzeitig einen oder mehrere Datenträger für Daten hinzu. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Verwenden des Portals zum Hinzufügen von Datenträgern zu einer VM

Wenn Sie über das Portal einen virtuellen Computer erstellen, wird bei den meisten Marketplace-Elementen standardmäßig nur der Betriebssystemdatenträger erstellt.

Nach der Erstellung einer VM können Sie das Portal für folgende Zwecke verwenden:

* Erstellen Sie einen neuen Datenträger für Daten, und fügen Sie ihn an den virtuellen Computer an.
* Laden Sie einen vorhandenen Datenträger für Daten hoch, und fügen Sie ihn an die VM an.

Jeder nicht verwaltete Datenträger, den Sie hinzufügen, sollte in einem separaten Container angeordnet werden.

>[!NOTE]  
>Von Azure erstellte und verwaltete Datenträger werden als [verwaltete Datenträger](/azure/virtual-machines/windows/managed-disks-overview) bezeichnet.

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Verwenden des Portals zum Erstellen und Anfügen eines neuen Datenträgers für Daten

1. Wählen Sie im Portal **Alle Dienste** und dann **Virtuelle Computer** aus.
   ![Beispiel: VM-Dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Wählen Sie einen virtuellen Computer aus, der zuvor erstellt wurde.
   ![Beispiel: Auswählen eines virtuellen Computers auf dem Dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. Wählen Sie für die VM die Option **Datenträger** und dann **Datenträger hinzufügen** aus.
   ![Beispiel: Anfügen eines neuen Datenträgers an den virtuellen Computer](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Datenträger:
   * Geben Sie die **LUN** ein. Die LUN muss eine gültige Zahl sein.
   * Wählen Sie **Datenträger erstellen**.
   ![Beispiel: Anfügen eines neuen Datenträgers an den virtuellen Computer](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. Führen Sie auf dem Blatt **Verwalteten Datenträger erstellen** die folgenden Schritte aus:
   * Geben Sie unter **Name** den Namen des Datenträgers ein.
   * Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue.
   * Wählen Sie den **Speicherort** aus. Der Speicherort ist standardmäßig auf den Container festgelegt, der auch den Betriebssystem-Datenträger enthält.
   * Wählen Sie den **Kontotyp** aus.
      ![Beispiel: Anfügen eines neuen Datenträgers an den virtuellen Computer](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **SSD Premium**  
      Premium-Datenträger (SSD) basieren auf Solid State Drives und bieten konsistente Leistung mit geringen Wartezeiten. Sie bieten das beste Preis-Leistungs-Verhältnis und eignen sich ideal für E/A-intensive Apps und Produktionsworkloads.

      **HDD Standard**  
      Standard-Datenträgern (HDD) basieren auf magnetischen Laufwerken und werden für Apps bevorzugt, in denen nur selten auf Daten zugegriffen wird. Zonenredundante Datenträger basieren auf zonenredundantem Speicher, der Ihre Daten in mehrere Zonen repliziert und so sicherstellt, dass Ihre Daten auch dann verfügbar sind, wenn eine einzelne Zone ausfällt.

   * Wählen Sie den **Quelltyp** aus.

     Erstellen Sie einen Datenträger auf der Grundlage einer Momentaufnahme eines anderen Datenträgers oder eines Blobs in einem Speicherkonto. Sie können auch einen leeren Datenträger erstellen.

      **Momentaufnahme**: Wählen Sie eine Momentaufnahme aus, falls eine verfügbar ist. Die Momentaufnahme muss im Abonnement und am Speicherort des virtuellen Computers verfügbar sein.

      **Speicherblob**
     * Fügen Sie den URI des Speicherblobs hinzu, das das Datenträgerimage enthält.  
     * Wählen Sie **Durchsuchen**, um das Blatt „Speicherkonten“ zu öffnen. Anweisungen hierzu finden Sie unter [Hinzufügen eines Datenträgers in einem Speicherkonto](#add-a-data-disk-from-a-storage-account).
     * Wählen Sie den Betriebssystemtyp des Images aus: **Windows**, **Linux** oder **Ohne (Datenträger)** .

   * Wählen Sie unter **Größe (GiB)** die Größe aus.

     Die Kosten für Standard-Datenträger sind von der Größe des Datenträgers abhängig. Die Kosten und die Leistung von Premium-Datenträgern hängen von der Größe des Datenträgers ab. Weitere Informationen finden Sie unter [Verwaltete Datenträger – Preise ](https://go.microsoft.com/fwlink/?linkid=843142).

   * Klicken Sie auf **Erstellen**. Azure Stack Hub erstellt und überprüft den verwalteten Datenträger.

6. Nachdem der Datenträger von Azure Stack Hub erstellt und an die VM angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen der VM unter **DATENTRÄGER** aufgeführt.

   ![Beispiel: Anzeigen des Datenträgers](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Hinzufügen eines Datenträgers in einem Speicherkonto

Weitere Informationen zum Arbeiten mit Speicherkonten in Azure Stack Hub finden Sie unter [Einführung zu Azure Stack Hub-Speicher](azure-stack-storage-overview.md).

1. Wählen Sie das zu verwendende **Speicherkonto** aus.
2. Wählen Sie den **Container** aus, in dem der Datenträger für Daten platziert werden soll. Auf dem Blatt **Container** können Sie bei Bedarf auch einen neuen Container erstellen. Anschließend können Sie den Speicherort für den neuen Datenträger auf den eigenen Container festlegen. Wenn Sie für jeden Datenträger einen separaten Container verwenden, verteilen Sie die Platzierung des Datenträgers für Daten, was zur Verbesserung der Leistung beitragen kann.
3. Wählen Sie zum Speichern der Auswahl die Option **Auswählen**.

    ![Beispiel: Auswählen eines Containers](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Hinzufügen eines vorhandenen Datenträgers zu einem virtuellen Computer

1. [Bereiten Sie eine VHD-Datei vor](/azure/virtual-machines/windows/classic/createupload-vhd), um sie als Datenträger für einen virtuellen Computer zu verwenden. Laden Sie die VHD-Datei in ein Speicherkonto hoch, das Sie mit der VM verwenden, an die Sie die VHD-Datei anfügen möchten.

    - Verwenden Sie für die VHD-Datei nicht den Container, der den Betriebssystem-Datenträger enthält.  
    - Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
    - Lesen Sie [Planen der Migration zu Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks) vor dem Starten der Migration zu [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    
    ![Beispiel: Hochladen einer VHD-Datei](media/azure-stack-manage-vm-disks/upload-vhd.png)



2. Nach dem Hochladen der VHD-Datei können Sie die virtuelle Festplatte an einen virtuellen Computer anfügen. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer**.  
 ![Beispiel: Auswählen eines virtuellen Computers auf dem Dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Wählen Sie die VM aus der Liste aus.

    ![Beispiel: Auswählen eines virtuellen Computers auf dem Dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. Wählen Sie auf der Seite für die VM die Option **Datenträger** und dann **Vorhandenen anfügen** aus.

    ![Beispiel: Anfügen eines vorhandenen Datenträgers](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. Wählen Sie auf der Seite **Vorhandenen Datenträger anfügen** die Option **VHD-Datei**. Die Seite **Speicherkonten** wird geöffnet.

    ![Beispiel: Auswählen einer VHD-Datei](media/azure-stack-manage-vm-disks/select-vhd.png)

6. Wählen Sie unter **Speicherkonten** das zu verwendende Konto und anschließend einen Container aus, der die zuvor hochgeladene VHD-Datei enthält. Wählen Sie die VHD-Datei aus, und klicken Sie auf **Auswählen**, um die Auswahl zu speichern.

    ![Beispiel: Auswählen eines Containers](media/azure-stack-manage-vm-disks/select-container2.png)

7. Die ausgewählte Datei wird unter **Vorhandenen Datenträger anfügen** unter **VHD-Datei** angezeigt. Aktualisieren Sie die Einstellung **Hostzwischenspeicherung** des Datenträgers, und wählen Sie dann **OK**, um die neue Datenträgerkonfiguration für den virtuellen Computer zu speichern.

    ![Beispiel: Anfügen der VHD-Datei](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Nachdem der Datenträger von Azure Stack Hub erstellt und an die VM angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen der VM unter **Datenträger** aufgeführt.

    ![Beispiel: Abschließen der Datenträgeranfügung](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-disks-to-a-vm"></a>Hinzufügen mehrerer Datenträger zu einem virtuellen Computer mithilfe von PowerShell

Mit PowerShell können Sie einen virtuellen Computer bereitstellen und neue Datenträger hinzufügen oder einen bereits vorhandenen verwalteten Datenträger bzw. eine VHD-Datei als Datenträger anfügen.

Das Cmdlet **Add-AzureRmVMDataDisk** fügt einem virtuellen Computer einen Datenträger hinzu. Ein Datenträger kann beim Erstellen eines virtuellen Computers oder einem bereits vorhandenen virtuellen Computer hinzugefügt werden. Geben Sie für einen nicht verwalteten Datenträger den Parameter **VhdUri** an, um die Datenträger auf unterschiedliche Container zu verteilen.

### <a name="add-data-disks-to-a-new-vm"></a>Hinzufügen von Datenträgern zu einem **neuen** virtuellen Computer

In den folgenden Beispielen wird mithilfe von PowerShell-Befehlen ein virtueller Computer mit drei Datenträgern erstellt. Die Befehle werden aufgrund der geringfügigen Unterschiede bei der Verwendung verwalteter Datenträger oder nicht verwalteter Datenträger in mehreren Abschnitten bereitgestellt. 

#### <a name="create-virtual-machine-configuration-and-network-resources"></a>Erstellen der VM-Konfiguration und -Netzwerkressourcen

Das folgende Skript erstellt ein VM-Objekt und speichert es in der Variablen `$VirtualMachine`. Die Befehle weisen dem virtuellen Computer einen Namen und eine Größe zu und erstellen anschließend die Netzwerkressourcen (virtuelles Netzwerk, Subnetz, virtueller Netzwerkadapter, NSG und öffentliche IP-Adresse) für den virtuellen Computer:

```powershell
# Create new virtual machine configuration
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"

# Set variables
$rgName = "myResourceGroup"
$location = "local"

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
                                  -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
                                  -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
                                                -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
                                                -SourceAddressPrefix Internet -SourcePortRange * `
                                                -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
                                       -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
                                   -Location $location -SubnetId $vnet.Subnets[0].Id `
                                   -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

```

#### <a name="add-managed-disk"></a>Hinzufügen eines verwalteten Datenträgers
>[!NOTE]  
>Dieser Abschnitt bezieht sich nur auf das Hinzufügen verwalteter Datenträger. 

Die folgenden drei Befehle fügen dem in `$VirtualMachine` gespeicherten virtuellen Computer verwaltete Datenträger hinzu. Die einzelnen Befehle geben jeweils den Namen und zusätzliche Eigenschaften des Datenträgers an:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -CreateOption Empty
```

Mit dem folgenden Befehl wird dem in `$VirtualMachine` gespeicherten virtuellen Computer ein Betriebssystemdatenträger als verwalteter Datenträger hinzugefügt.

```powershell
# Set OS Disk
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName  `
                                      -CreateOption FromImage -Windows
```

#### <a name="add-unmanaged-disk"></a>Hinzufügen nicht verwalteter Datenträger

>[!NOTE]  
>Dieser Abschnitt bezieht sich nur auf das Hinzufügen von nicht verwalteten Datenträgern. 

Die nächsten drei Befehle weisen den Variablen `$DataDiskVhdUri01`, `$DataDiskVhdUri02` und `$DataDiskVhdUri03` die Pfade von drei nicht verwalteten Datenträgern zu. Definieren Sie in der URL einen anderen Pfadnamen, um die Datenträger auf unterschiedliche Container zu verteilen:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Die folgenden drei Befehle fügen dem in `$VirtualMachine` gespeicherten virtuellen Computer Datenträger hinzu. Die einzelnen Befehle geben jeweils den Namen und zusätzliche Eigenschaften des Datenträgers an. Die URIs der Datenträger werden in `$DataDiskVhdUri01`, `$DataDiskVhdUri02` und `$DataDiskVhdUri03` gespeichert:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Die folgenden Befehle fügen dem in `$VirtualMachine` gespeicherten virtuellen Computer einen nicht verwalteten Betriebssystemdatenträger hinzu.

```powershell
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
                                      -CreateOption FromImage -Windows
```


#### <a name="create-new-virtual-machine"></a>Erstellen eines neuen virtuellen Computers
Verwenden Sie die folgenden PowerShell-Befehle, um das Betriebssystemimage festzulegen und dem virtuellen Computer eine Netzwerkkonfiguration hinzuzufügen. Starten Sie dann den neuen virtuellen Computer.

```powershell
#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine -ProvisionVMAgent | `
                  Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
                  -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```


### <a name="add-data-disks-to-an-existing-vm"></a>Hinzufügen von Datenträgern zu einem **vorhandenen** virtuellen Computer
In den folgenden Beispielen werden einem vorhandenen virtuellen Computer mithilfe von PowerShell-Befehlen drei Datenträger hinzugefügt.

#### <a name="get-virtual-machine"></a>Abrufen des virtuellen Computers

 Der erste Befehl ruft mithilfe des Cmdlets **Get-AzureRmVM** die VM mit dem Namen **VirtualMachine** ab. Der Befehl speichert die VM in der Variablen `$VirtualMachine`:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

#### <a name="add-managed-disk"></a>Hinzufügen eines verwalteten Datenträgers

>[!NOTE]  
>Dieser Abschnitt bezieht sich nur auf das Hinzufügen verwalteter Datenträger.

Die nächsten drei Befehle fügen dem in der Variablen `$VirtualMachine` gespeicherten virtuellen Computer die verwalteten Datenträger hinzu. Die einzelnen Befehle geben jeweils den Namen und zusätzliche Eigenschaften des Datenträgers an:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" -Lun 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" -Lun 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" -Lun 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="add-unmanaged-disk"></a>Hinzufügen nicht verwalteter Datenträger

>[!NOTE]  
>Dieser Abschnitt bezieht sich nur auf das Hinzufügen von nicht verwalteten Datenträgern. 

Die nächsten drei Befehle weisen den Variablen `$DataDiskVhdUri01`, `$DataDiskVhdUri02` und `$DataDiskVhdUri03` die Pfade für drei Datenträger zu. Die verschiedenen Pfadnamen in den VHD-URIs geben unterschiedliche Container für die Platzierung der Datenträger an:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Die nächsten drei Befehle fügen die Datenträger der in der Variablen `$VirtualMachine` gespeicherten VM hinzu. Die einzelnen Befehle geben jeweils den Namen, den Speicherort und zusätzliche Eigenschaften des Datenträgers an. Die URIs der Datenträger werden in `$DataDiskVhdUri01`, `$DataDiskVhdUri02` und `$DataDiskVhdUri03` gespeichert:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="update-virtual-machine-state"></a>Aktualisieren des Zustands virtueller Computer

Dieser Befehl aktualisiert den Zustand des in `$VirtualMachine` gespeicherten virtuellen Computers (in `-ResourceGroupName`):

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Stack Hub-VMs finden Sie unter [Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md).
