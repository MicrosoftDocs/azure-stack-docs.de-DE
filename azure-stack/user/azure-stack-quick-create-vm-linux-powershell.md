---
title: Erstellen eines virtuellen Linux-Computers mithilfe von PowerShell in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie einen virtuellen Linux-Computer mithilfe von PowerShell in Azure Stack erstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 55f1395d66262b268b9107f196528270c1546bba
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712282"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-powershell-in-azure-stack"></a>Schnellstart: Erstellen eines virtuellen Linux-Servercomputers mithilfe von PowerShell in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Computer mit Ubuntu Server 16.04 LTS mit Azure Stack PowerShell erstellen. Befolgen Sie die Schritte in diesem Artikel zum Erstellen und Verwenden eines virtuellen Computers.  In diesem Artikel führen Sie auch die folgenden Schritte aus:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren des NGINX-Webservers und Anzeigen der Standardstartseite
* Bereinigen nicht mehr benötigter Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Linux-Image im Azure Stack-Marketplace**

   Ein Linux-Image ist standardmäßig nicht im Azure Stack-Marketplace enthalten. Bitten Sie den Azure Stack-Operator, das benötigte Image **Ubuntu Server 16.04 LTS** bereitzustellen. Hierzu kann der Operator die Schritte des Artikels [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) ausführen.

* Azure Stack erfordert eine spezifische Version von Azure PowerShell, um die Ressourcen zu erstellen und zu verwalten. Wenn Sie PowerShell nicht für Azure Stack konfiguriert haben, führen Sie die Schritte zum [Installieren](../operator/azure-stack-powershell-install.md) von PowerShell aus.

* Bei der Einrichtung von Azure Stack PowerShell müssen Sie eine Verbindung mit Ihrer Azure Stack-Umgebung herstellen. Anleitungen dazu finden Sie unter [Herstellen einer Verbindung mit Azure Stack über PowerShell als Benutzer](azure-stack-powershell-configure-user.md).

* Ein öffentlicher SSH-Schlüssel mit dem Namen „id_rsa.pub“ im SSH-Verzeichnis Ihres Windows-Benutzerprofils. Ausführliche Informationen zum Erstellen von SSH-Schlüsseln finden Sie unter [Verwenden eines öffentlichen SSH-Schlüssels](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Sie Azure Stack-Ressourcen bereitstellen und verwalten können. Führen Sie im Development Kit oder im integrierten Azure Stack-System den folgenden Codeblock aus, um eine Ressourcengruppe zu erstellen. 

> [!NOTE]
> Allen Variablen in den Codebeispielen sind Werte zugewiesen. Sie können bei Bedarf aber auch neue Werte zuweisen.

```powershell  
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Erstellen von Speicherressourcen

Erstellen Sie ein Speicherkonto und dann einen Speichercontainer zum Speichern des Ubuntu Server 16.04 LTS-Images.

```powershell  
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>Erstellen von Netzwerkressourcen

Erstellen Sie ein virtuelles Netzwerk, ein Subnetz und eine öffentliche IP-Adresse. Diese Ressourcen dienen dazu, Netzwerkkonnektivität für den virtuellen Computer bereitzustellen.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Erstellen Sie eine Netzwerksicherheitsgruppe und eine Netzwerksicherheitsgruppen-Regel.

Die Netzwerksicherheitsgruppe sichert den virtuellen Computer mithilfe von Regeln für eingehenden und ausgehenden Datenverkehr. Erstellen Sie eine Eingangsregel für Port 3389, um eingehende Remotedesktopverbindungen zuzulassen, und eine Eingangsregel für Port 80, um eingehenden Webdatenverkehr zuzulassen.

```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Erstellen einer Netzwerkkarte für den virtuellen Computer

Die Netzwerkkarte verbindet die VM mit einem Subnetz, einer Netzwerksicherheitsgruppe und einer öffentlichen IP-Adresse.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie eine VM-Konfiguration. Diese Konfiguration umfasst die Einstellungen, die beim Bereitstellen des virtuellen Computers verwendet werden. Hierzu zählen etwa Benutzeranmeldeinformationen, Größe und VM-Image.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="quick-create-virtual-machine---full-script"></a>Schnelles Erstellen virtueller Computer: vollständiges Skript

> [!NOTE]
> Das Skript besteht im Wesentlichen aus dem zusammengeführten obigen Code. Allerdings enthält es anstelle eines SSH-Schlüssels ein Kennwort für die Authentifizierung.

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack resources. From your development kit or the Azure Stack integrated system, run the following code block to create a resource group. Values are assigned for all the variables in this document, you can use these values or assign new values.
#>

# Edit your variables here if required

# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for virtual machine password
$VMPassword = 'Password123!'

# End of Variables - no need to edit anything past that point to deploy a single VM

# Create Resource Group
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account and then create a storage container for the Ubuntu Server 16.04 LTS image.

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, subnet, and a public IP address. These resources are used to provide network connectivity to the virtual machine.

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"


### Create a network security group and a network security group rule

<#
The network security group secures the virtual machine by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the virtual machine

# The network card connects the virtual machine to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a virtual machine
<#
Create a virtual machine configuration. This configuration includes the settings used when deploying the virtual machine. For example: user credentials, size, and the virtual machine image.
#>

# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Herstellen einer Verbindung mit dem virtuellen Computer

Nachdem der virtuelle Computer bereitgestellt wurde, konfigurieren Sie eine SSH-Verbindung für den virtuellen Computer. Verwenden Sie den Befehl [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), um die öffentliche IP-Adresse des virtuellen Computers abzurufen.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Verwenden Sie von einem Clientsystem aus, auf dem SSH installiert ist, den folgenden Befehl, um die Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie unter Windows arbeiten, können Sie [PuTTY](https://www.putty.org/) zum Herstellen der Verbindung verwenden.

```
ssh <Public IP Address>
```

Melden Sie sich bei entsprechender Aufforderung als **azureuser** an. Wenn Sie eine Passphrase beim Erstellen der SSH-Schlüssel verwendet haben, müssen Sie die Passphrase eingeben.

## <a name="install-the-nginx-web-server"></a>Installieren des NGINX-Webservers

Führen Sie das folgende Skript aus, um Paketquellen zu aktualisieren und das neueste NGINX-Paket zu installieren:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Anzeigen der NGINX-Willkommensseite

Nachdem NGINX installiert und der Port 80 auf dem virtuellen Computer geöffnet wurde, können Sie über die öffentliche IP-Adresse des virtuellen Computers auf den Webserver zugreifen. Öffnen Sie einen Webbrowser, und navigieren Sie zu ```http://<public IP address>```.

![Willkommensseite des NGINX-Webservers](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, die Sie nicht mehr benötigen. Sie können den Befehl [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) verwenden, um diese Ressourcen zu entfernen. Um die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen, führen Sie den folgenden Befehl aus:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen einfachen virtuellen Linux-Servercomputer bereitgestellt. Weitere Informationen zu virtuellen Azure Stack-Computern finden Sie unter [Aspekte von virtuellen Computern in Azure Stack](azure-stack-vm-considerations.md).
