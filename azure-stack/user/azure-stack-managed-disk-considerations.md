---
title: Unterschiede und Überlegungen für Managed Disks und verwaltete Images in Azure Stack | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Unterschiede und zu berücksichtigende Überlegungen bei der Verwendung von Managed Disks und verwaltete Images in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: d32d7d8d484d6e9819440fc9261bae52d6395ae2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985913"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: Unterschiede und Überlegungen

Dieser Artikel beschreibt die bekannten Unterschiede zwischen [Azure Stack Managed Disks](azure-stack-manage-vm-disks.md) und [Managed Disks für Azure](/azure/virtual-machines/windows/managed-disks-overview). Informationen zu allgemeinen Unterschieden zwischen Azure Stack und Azure finden Sie im Artikel [Key considerations](azure-stack-considerations.md) (Wichtige Aspekte).

Managed Disks vereinfacht die Datenträgerverwaltung für IaaS-VMs durch die Verwaltung der [Speicherkonten](../operator/azure-stack-manage-storage-accounts.md), die den VM-Datenträgern zugeordnet sind.

> [!Note]  
> Managed Disks steht in Azure Stack ab 1808 Update zur Verfügung. Es ist jetzt standardmäßig aktiviert, wenn virtuelle Computer über das Azure Stack-Portal ab 1811 Update erstellt werden.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Spickzettel: Unterschiede zwischen Managed Disks

| Feature | Azure (global) | Azure Stack |
| --- | --- | --- |
|Verschlüsselung für ruhende Daten |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |BitLocker-128-Bit-AES-Verschlüsselung      |
|Image          | Unterstützung von verwalteten benutzerdefinierten Images |Unterstützt|
|Sicherungsoptionen |Unterstützung für Azure Backup-Dienst |Noch nicht unterstützt |
|Optionen für die Notfallwiederherstellung |Unterstützung von Azure Site Recovery |Noch nicht unterstützt|
|Datenträgertypen     |SSD Premium, SSD Standard und HDD Standard |SSD Premium, HDD Standard |
|Premium-Datenträger  |Vollständig unterstützt |Kann bereitgestellt werden, jedoch ohne Leistungsgrenzwerte oder Garantien  |
|Premium-Datenträger-IOPs  |Abhängig von der Größe des Datenträgers  |2.300 IOPS pro Datenträger |
|Durchsatz Premium-Datenträger |Abhängig von der Größe des Datenträgers |145 MB/Sekunde pro Datenträger |
|Datenträgergröße  |Azure Premium Disk: P4 (32 GiB) bis P80 (32 TiB)<br>Azure SSD Standard-Datenträger: E10 (128 GiB) bis E80 (32 TiB)<br>Azure HDD Standard-Datenträger: S4 (32 GiB) bis S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GB<br>M15: 256 GiB<br>M20: 512 GB<br>M30: 1024 GiB |
|Datenträger-Momentaufnahme Kopie|Momentaufnahme: Verwaltete Azure-Datenträger, die an einen ausgeführten virtuellen Computer angefügt sind, werden unterstützt|Noch nicht unterstützt |
|Analyse Datenträgerleistung |Unterstützung für aggregierte Metriken und Metriken pro Datenträger |Noch nicht unterstützt |
|Migration      |Bereitstellung eines Tools für die Migration von vorhandenen, nicht verwalteten Azure Resource Manager VMs, ohne dass der virtuelle Computer neu erstellt werden muss  |Noch nicht unterstützt |

> [!NOTE]  
> Managed Disks-IOPs und Durchsatz in Azure Stack ist eine Cap-Zahl statt einer bereitgestellten Zahl, die durch Hardware und in Azure Stack ausgeführte Workloads beeinflusst werden kann.

## <a name="metrics"></a>Metriken

Es gibt auch Unterschiede zu Speichermetriken:

- Mit Azure Stack unterscheiden die Transaktionsdaten in Speichermetriken nicht zwischen interner und externer Netzwerkbandbreite.
- In Azure Stack beinhalten die Transaktionsdaten in Speichermetriken keinen Zugriff auf die bereitgestellten Datenträger über virtuelle Computer.

## <a name="api-versions"></a>API-Versionen

Azure Stack Managed Disks unterstützt die folgenden API-Versionen:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Konvertieren in verwaltete Datenträger

Mit dem folgenden Skript können Sie einen aktuell bereitgestellten virtuellen Computer von nicht verwalteten Datenträgern auf verwaltete Datenträger umstellen. Ersetzen Sie die Platzhalter durch Ihre eigenen Werte:

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Verwaltete Images

Azure Stack unterstütz *verwaltete Images*, was es Ihnen ermöglicht, ein verwaltetes Image-Objekt auf einem generalisierten virtuellen Computer (sowohl nicht verwaltet als auch verwaltet) zu erstellen, der fortan nur noch virtuelle Computer auf verwalteten Datenträgern erstellen kann. Verwaltete Images ermöglichen die folgenden zwei Szenarien:

- Sie haben generalisierte, nicht verwaltete virtuelle Computer und möchten im weiteren Verlauf verwaltete Datenträger verwenden.
- Sie haben eine generalisierte, verwaltete VM und möchten mehrere, ähnliche verwaltete VMs erstellen.

### <a name="step-1-generalize-the-vm"></a>Schritt 1: Generalisieren des virtuellen Computers

Folgen Sie unter Windows dem Abschnitt [Generalisieren des virtuellen Windows-Computers mithilfe von Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep). Für Linux folgen Sie Schritt 1 [hier](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Stellen Sie sicher, dass Sie Ihre VM generalisieren. Das Erstellen einer VM aus einem nicht ordnungsgemäß generalisierten Image führt zu einem **VMProvisioningTimeout**-Fehler.

### <a name="step-2-create-the-managed-image"></a>Schritt 2: Erstellen des verwalteten Images

Zum Erstellen des verwalteten Images können Sie das Portal, PowerShell oder die Befehlszeilenschnittstelle verwenden. Folgen Sie den Schritten in [diesem](/azure/virtual-machines/windows/capture-image-resource) Azure-Artikel.

### <a name="step-3-choose-the-use-case"></a>Schritt 3: Auswählen des Anwendungsfalls

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Fall 1: Migrieren nicht verwalteter VMs zu verwalteten Datenträgern

Stellen Sie sicher, dass Sie Ihre VM richtig generalisieren, bevor Sie diesen Schritt ausführen. Nach der Generalisierung können Sie diese VM nicht mehr verwenden. Das Erstellen einer VM aus einem nicht ordnungsgemäß generalisierten Image führt zu einem **VMProvisioningTimeout**-Fehler.

Befolgen Sie die [hier](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account) dargelegten Anleitungen, um ein verwaltetes Image aus einer generalisierten VHD in einem Speicherkonto zu erstellen. Dieses Image kann verwendet werden, um im weiteren Verlauf Zukunft verwaltete VMs zu erstellen.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Fall 2: Erstellen einer verwalteten VM aus einem verwalteten Image mit PowerShell

Nachdem Sie ein Image aus einer vorhandenen VM mit verwaltetem Datenträger mithilfe des [hier zu findenden](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell) Skripts erstellt haben, erstellt das folgende Beispielskript eine ähnlich Linux-VM aus einem vorhandenen Image-Objekt:

Bei Azure Stack-PowerShell-Modul 1.7.0 oder höher befolgen Sie die Anweisungen [hier](/azure/virtual-machines/windows/create-vm-generalized-managed).

Bei Azure Stack-PowerShell-Modul 1.6.0 oder früher:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Sie können das Portal auch verwenden, um eine VM aus einem verwalteten Image zu erstellen. Weitere Informationen finden Sie in den Azure-Artikeln zu verwalteten Images [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](/azure/virtual-machines/windows/capture-image-resource) und [Erstellen eines virtuellen Computers aus einem verwalteten Image](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfiguration

Nach der Anwendung des Updates 1808 oder höher müssen Sie die folgende Konfiguration ausführen, bevor Sie Managed Disks verwenden:

- Wenn ein Abonnement vor dem Update 1808 erstellt wurde, führen Sie die folgenden Schritte aus, um das Abonnement zu aktualisieren. Andernfalls kann die Bereitstellung von VMs in diesem Abonnement mit einer Fehlermeldung „Interner Fehler im Datenträger-Manager“ fehlschlagen.
   1. Navigieren Sie im Mandantenportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Klicken Sie auf **Ressourcenanbieter**, klicken Sie dann auf **Microsoft.Compute**, und klicken Sie anschließend auf **Erneut registrieren**.
   2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)**, und überprüfen Sie, ob **Azure Stack – Verwalteter Datenträger** aufgeführt wird.
- Wenn Sie eine Umgebung mit mehreren Mandanten verwenden, bitten Sie Ihren Cloudoperator (der sich in Ihrem Unternehmen oder beim Dienstanbieter befinden kann), jedes Ihrer Gastverzeichnisse gemäß den folgenden Schritten in [diesem Artikel](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) neu zu konfigurieren. Andernfalls kann die Bereitstellung von VMs in einem Abonnement, das diesem Gastverzeichnis zugeordnet ist, mit einer Fehlermeldung „Interner Fehler im Datenträger-Manager.“ fehlschlagen.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über virtuelle Azure Stack-Computer](azure-stack-compute-overview.md)
