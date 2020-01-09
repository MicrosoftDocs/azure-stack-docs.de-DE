---
title: Erstellen eines virtuellen Windows Server-Computers in Azure Stack mit PowerShell | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie einen virtuellen Windows Server-Computer in Azure Stack mit PowerShell erstellen.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: e31edd266c16fa51855efa3259c86ecf9b53660b
ms.sourcegitcommit: bbe1048682c7dccc6cebde542462c14ee1f3d0d1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75677687"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack"></a>Schnellstart: Erstellen eines virtuellen Windows Server-Computers in Azure Stack mit PowerShell

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Windows Server 2016-Computer mit Azure Stack PowerShell erstellen. Befolgen Sie die Schritte in diesem Artikel zum Erstellen und Verwenden eines virtuellen Computers. In diesem Artikel führen Sie auch die folgenden Schritte aus:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren des IIS-Webservers und Anzeigen der Standardstartseite
* Bereinigen Ihrer Ressourcen

> [!NOTE]
>  Führen Sie die in diesem Artikel beschriebenen Schritte entweder mithilfe des Azure Stack Development Kits oder eines Windows-basierten externen Clients aus, wenn Sie über ein VPN verbunden sind.

## <a name="prerequisites-for-windows-server-vm"></a>Voraussetzungen für einen virtuellen Windows Server-Computer

* Stellen Sie sicher, dass Ihr Azure Stack-Bediener das Image **Windows Server 2016** dem Azure Stack-Marketplace hinzugefügt hat.

* Azure Stack erfordert eine spezifische Version von Azure PowerShell, um die Ressourcen zu erstellen und zu verwalten. Wenn Sie PowerShell nicht für Azure Stack konfiguriert haben, führen Sie die Schritte zum [Installieren](../operator/azure-stack-powershell-install.md) von PowerShell aus.

* Bei der Einrichtung von Azure Stack PowerShell müssen Sie eine Verbindung mit Ihrer Azure Stack-Umgebung herstellen. Anleitungen dazu finden Sie unter [Herstellen einer Verbindung mit Azure Stack über PowerShell als Benutzer](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure Stack-Ressourcen bereitgestellt und verwaltet werden. Führen Sie im Development Kit oder im integrierten Azure Stack-System den folgenden Codeblock aus, um eine Ressourcengruppe zu erstellen. 

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

Erstellen Sie ein Speicherkonto, um die Ausgabe der Startdiagnose zu speichern.

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

Erstellen Sie ein virtuelles Netzwerk, ein Subnetz und eine öffentliche IP-Adresse. Diese Ressourcen dienen dazu, Netzwerkkonnektivität für die VM bereitzustellen.

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

Die Netzwerksicherheitsgruppe schützt die VM mithilfe von Eingangs- und Ausgangsregeln für Datenverkehr. Wir erstellen nun eine Eingangsregel für Port 3389, um eingehende Remotedesktopverbindungen zuzulassen, und eine Eingangsregel für Port 80, um eingehenden Webdatenverkehr zuzulassen.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-vm"></a>Erstellen einer Netzwerkkarte für die VM

Mit der Netzwerkkarte wird die VM mit einem Subnetz, einer Netzwerksicherheitsgruppe und einer öffentlichen IP-Adresse verbunden.

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

## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie eine Konfiguration für den virtuellen Computer. Diese Konfiguration umfasst die Einstellungen, die beim Bereitstellen der VM verwendet werden. Beispiel: Anmeldeinformationen, Größe und VM-Image.

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential -ProvisionVMAgent

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Um eine Remoteverbindung mit der VM herzustellen, die Sie im vorherigen Schritt erstellt haben, benötigen Sie ihre öffentliche IP-Adresse. Führen Sie den folgenden Befehl aus, um die öffentliche IP-Adresse der VM abzurufen:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Erstellen Sie mit dem folgenden Befehl eine Remotedesktopsitzung mit der VM. Ersetzen Sie die IP-Adresse durch die öffentliche IP-Adresse (*publicIPAddress*) Ihres virtuellen Computers. Geben Sie bei entsprechender Aufforderung den Benutzernamen, den Sie beim Erstellen der VM verwendet haben, und das zugehörige Kennwort ein.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Installieren von IIS mithilfe von PowerShell

Nach der Anmeldung am virtuellen Computer können Sie mit nur einer PowerShell-Codezeile IIS installieren und die lokale Firewallregel aktivieren, um Webdatenverkehr zuzulassen. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Anzeigen der IIS-Willkommensseite

Nachdem Sie IIS installiert und Port 80 auf Ihrem virtuellen Computer geöffnet haben, können Sie die IIS-Standardwillkommensseite in einem beliebigen Browser anzeigen. Verwenden Sie die öffentliche IP-Adresse (*publicIpAddress*), die Sie sich im vorherigen Abschnitt notiert haben, um die Standardseite zu besuchen.

![IIS-Standardwebsite](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Entfernen Sie die Ressourcengruppe, die die VM und die zugehörigen Ressourcen enthält, mithilfe des folgenden Befehls, wenn Sie sie nicht mehr benötigen:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Windows-Computer bereitgestellt. Weitere Informationen zu Azure Stack-VMs finden Sie unter [Überlegungen zur Verwendung von virtuellen Computern in Azure Stack](azure-stack-vm-considerations.md).
