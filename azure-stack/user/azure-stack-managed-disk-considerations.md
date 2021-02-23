---
title: 'Verwaltete Azure Stack Hub-Datenträger: Unterschiede und Überlegungen'
description: Dieser Artikel beschreibt die Unterschiede und zu berücksichtigende Überlegungen bei der Verwendung von verwalteten Datenträgern und verwalteten Images in Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: fd1f827ec9108d1412df544c94044aabf600224a
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975876"
---
# <a name="azure-stack-hub-managed-disks-differences-and-considerations"></a>Verwaltete Azure Stack Hub-Datenträger: Unterschiede und Überlegungen

Dieser Artikel beschreibt die Unterschiede zwischen [*verwalteten Datenträgern* in Azure Stack Hub](azure-stack-manage-vm-disks.md) und [verwalteten Datenträgern in Azure](/azure/virtual-machines/windows/managed-disks-overview). Informationen zu allgemeinen Unterschieden zwischen Azure Stack Hub und Azure finden Sie im Artikel [Wichtige Aspekte](azure-stack-considerations.md).

Verwaltete Datenträger vereinfachen die Datenträgerverwaltung für IaaS-VMs durch Verwaltung der [Speicherkonten](../operator/azure-stack-manage-storage-accounts.md), die den VM-Datenträgern zugeordnet sind.

Verwaltete Datenträger sind standardmäßig aktiviert, wenn virtuelle Computer über das Azure Stack Hub-Portal erstellt werden.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Spickzettel: Unterschiede zwischen Managed Disks

| Funktion | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
|Verschlüsselung für ruhende Daten |Azure-Speicherdienstverschlüsselung (Storage Service Encryption, SSE), Azure Disk Encryption (ADE).     |BitLocker-128-Bit-AES-Verschlüsselung      |
|Image          | Verwaltetes benutzerdefiniertes Image |Unterstützt|
|Sicherungsoptionen | Azure Backup-Dienst |Noch nicht unterstützt |
|Optionen für die Notfallwiederherstellung | Azure Site Recovery |Noch nicht unterstützt|
|Datenträgertypen     |SSD Premium, SSD Standard und HDD Standard. |SSD Premium, HDD Standard |
|Premium-Datenträger  |Vollständig unterstützt. |Kann bereitgestellt werden, jedoch ohne Leistungsgrenzwerte oder Garantien  |
|Premium-Datenträger-IOPs  |Abhängig von der Größe des Datenträgers.  |2\.300 IOPS pro Datenträger |
|Durchsatz Premium-Datenträger |Abhängig von der Größe des Datenträgers. |145 MB/Sekunde pro Datenträger |
|Datenträgergröße  |Azure Premium Disk: P4 (32 GiB) bis P80 (32 TiB)<br>Azure SSD Standard-Datenträger: E10 (128 GiB) bis E80 (32 TiB)<br>Azure HDD Standard-Datenträger: S4 (32 GiB) bis S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GB<br>M15: 256 GiB<br>M20: 512 GB<br>M30: 1.023 GiB |
|Datenträger-Momentaufnahme Kopie|Momentaufnahmen verwalteter Azure-Datenträger, die an eine ausgeführte VM angefügt sind, werden unterstützt.|Noch nicht unterstützt |
|Analyse Datenträgerleistung |Unterstützung für aggregierte Metriken und Metriken pro Datenträger. |Noch nicht unterstützt |
|Migration      |Bereitstellung eines Tools für die Migration von vorhandenen, nicht verwalteten Azure Resource Manager-VMs, ohne dass der virtuelle Computer neu erstellt werden muss.  |Noch nicht unterstützt |

> [!NOTE]  
> Managed Disks-IOPs und Durchsatz in Azure Stack Hub ist eine Cap-Zahl statt einer bereitgestellten Zahl, die durch Hardware und in Azure Stack Hub ausgeführte Workloads beeinflusst werden kann.

## <a name="metrics"></a>Metriken

Es gibt auch Unterschiede zu Speichermetriken:

- Mit Azure Stack Hub unterscheiden die Transaktionsdaten in Speichermetriken nicht zwischen interner und externer Netzwerkbandbreite.
- In Azure Stack Hub beinhalten die Transaktionsdaten in Speichermetriken keinen Zugriff auf die bereitgestellten Datenträger über virtuelle Computer.

## <a name="api-versions"></a>API-Versionen

Verwaltete Azure Stack Hub-Datenträger unterstützen die folgenden API-Versionen:

::: moniker range=">=azs-2008"
- 2019-03-01
- 2018-09-30
- 2018-06-01
- 2018-04-01
- 2017-03-30
::: moniker-end

::: moniker range="<=azs-2005"

- 2017-03-30
- 2017-12-01 (nur verwaltete Images, keine Datenträger, keine Momentaufnahmen)
::: moniker-end

## <a name="convert-to-managed-disks"></a>Konvertieren in verwaltete Datenträger

> [!NOTE]  
> Das Azure PowerShell-Cmdlet **ConvertTo-AzVMManagedDisk** kann nicht dazu verwendet werden, einen nicht verwalteten Datenträger in einen verwalteten Datenträger in Azure Stack Hub zu konvertieren. Dieses Cmdlet wird von Azure Stack Hub derzeit nicht unterstützt.

Mit dem folgenden Skript können Sie einen aktuell bereitgestellten virtuellen Computer von nicht verwalteten Datenträgern auf verwaltete Datenträger umstellen. Ersetzen Sie die Platzhalter durch Ihre eigenen Werte.

### <a name="az-modules"></a>[Az-Module](#tab/az1)

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack Hub location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack Hub location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzureRMSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzureRMVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzureRMDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzureRMDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzureRMDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzureRMVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzureRMVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzureRMPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzureRMVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzureRMNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzureRMVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzureRMVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

---




## <a name="managed-images"></a>Verwaltete Images

Azure Stack Hub unterstützt *verwaltete Images*. Somit können Sie ein verwaltetes Image-Objekt auf einem generalisierten virtuellen Computer (sowohl nicht verwaltet als auch verwaltet) erstellen, der fortan nur noch virtuelle Computer auf verwalteten Datenträgern erstellen kann. Verwaltete Images ermöglichen die folgenden zwei Szenarien:

- Sie haben generalisierte, nicht verwaltete virtuelle Computer und möchten im weiteren Verlauf verwaltete Datenträger verwenden.
- Sie haben eine generalisierte, verwaltete VM und möchten mehrere, ähnliche verwaltete VMs erstellen.

### <a name="step-1-generalize-the-vm"></a>Schritt 1: Generalisieren des virtuellen Computers

Folgen Sie unter Windows dem Abschnitt [Generalisieren des virtuellen Windows-Computers mithilfe von Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep). Für Linux führen Sie [Schritt 1](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm) aus.

> [!NOTE]
> Stellen Sie sicher, dass Sie Ihre VM generalisieren. Das Erstellen einer VM aus einem nicht ordnungsgemäß generalisierten Image kann zu einem **VMProvisioningTimeout**-Fehler führen.

### <a name="step-2-create-the-managed-image"></a>Schritt 2: Erstellen des verwalteten Images

Zum Erstellen des verwalteten Images können Sie das Portal, PowerShell oder die Befehlszeilenschnittstelle verwenden. Führen Sie die Schritte unter [Erstellen eines verwalteten Images](/azure/virtual-machines/windows/capture-image-resource) aus.

### <a name="step-3-choose-the-use-case"></a>Schritt 3: Auswählen des Anwendungsfalls

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Fall 1: Migrieren nicht verwalteter VMs zu verwalteten Datenträgern

Stellen Sie sicher, dass Sie Ihre VM richtig generalisieren, bevor Sie diesen Schritt ausführen. Nach der Generalisierung können Sie diese VM nicht mehr verwenden. Das Erstellen einer VM aus einem nicht ordnungsgemäß generalisierten Image führt zu einem **VMProvisioningTimeout**-Fehler.

Befolgen Sie die Anweisungen unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account), um ein verwaltetes Image aus einer generalisierten VHD in einem Speicherkonto zu erstellen. Dieses Image können Sie künftig dazu verwenden, verwaltete VMs zu erstellen.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Fall 2: Erstellen einer verwalteten VM auf der Grundlage eines verwalteten Image mithilfe von PowerShell

Nachdem Sie ein Image aus einer vorhandenen VM mit verwaltetem Datenträger mithilfe des Skripts unter [Erstellen eines Image von einem verwalteten Datenträger mithilfe von PowerShell](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell) erstellt haben, verwenden Sie das folgende Beispielskript zum Erstellen einer ähnlichen Linux-VM aus einem vorhandenen Image-Objekt.

Azure Stack Hub-PowerShell-Modul 1.7.0 oder höher: Befolgen Sie die Anweisungen unter [Erstellen eines virtuellen Computers aus einem verwalteten Image](/azure/virtual-machines/windows/create-vm-generalized-managed).

Azure Stack Hub-PowerShell-Modul 1.6.0 oder höher:

### <a name="az-modules"></a>[Az-Module](#tab/az2)

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleSSH = New-AzNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleSSH"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 22 -Access Allow

# Create a network security group
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleSSH

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzVMSourceImage -Id $Image.Id | `
Set-AzVMOSDisk -VM $VmConfig -CreateOption FromImage -Linux | `
Add-AzVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm2)

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRMResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzureRMVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzureRMVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzureRMPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleSSH = New-AzureRMNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleSSH"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 22 -Access Allow

# Create a network security group
$Nsg = New-AzureRMNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleSSH

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzureRMNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzureRMImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Set-AzureRmVMOSDisk -VM $VmConfig -CreateOption FromImage -Linux | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzureRMVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

---



Sie können das Portal auch verwenden, um eine VM aus einem verwalteten Image zu erstellen. Weitere Informationen finden Sie in den Azure-Artikeln zu verwalteten Images [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](/azure/virtual-machines/windows/capture-image-resource) und [Erstellen eines virtuellen Computers aus einem verwalteten Image](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfiguration

Nach Anwendung des Updates 1808 oder höher müssen Sie folgende Änderung an der Konfiguration vornehmen, bevor Sie verwaltete Datenträger verwenden:

- Wenn ein Abonnement vor dem Update 1808 erstellt wurde, führen Sie die folgenden Schritte aus, um das Abonnement zu aktualisieren. Andernfalls kann die Bereitstellung von VMs in diesem Abonnement mit einer Fehlermeldung „Interner Fehler im Datenträger-Manager“ fehlschlagen.
   1. Navigieren Sie im Azure Stack Hub-Benutzerportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Klicken Sie auf **Ressourcenanbieter**, klicken Sie dann auf **Microsoft.Compute**, und klicken Sie anschließend auf **Erneut registrieren**.
   2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)** , und überprüfen Sie, ob **Azure Stack Hub – Verwalteter Datenträger** aufgeführt wird.
- Wenn Sie eine Umgebung mit mehreren Mandanten verwenden, bitten Sie Ihren Cloudoperator (in Ihrer eigenen Organisation oder beim Dienstanbieter), all Ihre Gastverzeichnisse gemäß den Schritten unter [Konfigurieren der Mehrinstanzenfähigkeit in Azure Stack Hub](../operator/azure-stack-enable-multitenancy.md#configure-guest-directory) neu zu konfigurieren. Andernfalls kann die Bereitstellung von VMs in einem Abonnement, das diesem Gastverzeichnis zugeordnet ist, mit der Fehlermeldung „Interner Fehler im Datenträger-Manager“ fehlschlagen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [virtuelle Azure Stack Hub-Computer](azure-stack-compute-overview.md).
