---
title: Bereitstellen einer SDN-Infrastruktur mit SDN Express
description: Es wird beschrieben, wie Sie eine SDN-Infrastruktur mit SDN Express bereitstellen.
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e367602252207a673316caf3482d7805bff02ba8
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647809"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Bereitstellen einer SDN-Infrastruktur mit SDN Express

> Gilt für Azure Stack HCI, Version 20H2

Unter diesem Thema wird beschrieben, wie Sie eine End-to-End-SDN-Infrastruktur (Softwaredefiniertes Netzwerk) bereitstellen, indem Sie SDN Express-PowerShell-Skripts verwenden. Die Infrastruktur kann als Komponenten einen Netzwerkcontroller (NC), einen Software Load Balancer (SLB) und ein Gateway umfassen, die allesamt hoch verfügbar sind.  Die Skripts unterstützen eine Bereitstellung in Phasen. Sie können beispielsweise zunächst nur die Netzwerkcontrollerkomponente bereitstellen, um einen Teil der Funktionen bei sehr niedrigen Netzwerkanforderungen zu erhalten.

Darüber hinaus können Sie mit System Center Virtual Machine Manager (VMM) auch eine SDN-Infrastruktur bereitstellen. Weitere Informationen finden Sie unter [Verwalten von SDN-Ressourcen im VMM-Fabric](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit einer SDN-Bereitstellung beginnen, sollten Sie Ihre Infrastruktur für physische Netzwerke und Hostnetzwerke planen und konfigurieren. Lesen Sie die folgenden Artikel:

- [Anforderungen für physische Netzwerke](../concepts/physical-network-requirements.md)
- [Anforderungen für Hostnetzwerke](../concepts/host-network-requirements.md)
- [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](../deploy/create-cluster.md)
- [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](../deploy/create-cluster-powershell.md)
- [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md)

Sie müssen nicht alle SDN-Komponenten bereitstellen. Anhand der Informationen im Abschnitt [Stufenweise Bereitstellung](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) unter [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md) können Sie ermitteln, welche Infrastrukturkomponenten Sie benötigen. Anschließend können Sie die entsprechenden Skripts ausführen.

Stellen Sie sicher, dass auf allen Hostservern das Azure Stack HCI-Betriebssystem installiert ist. Informationen hierzu finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](../deploy/operating-system.md).

## <a name="requirements"></a>Anforderungen

Damit die SDN-Bereitstellung erfolgreich ist, müssen die folgenden Anforderungen erfüllt sein:

- Für alle Hostserver muss Hyper-V aktiviert sein.
- Alle Hostserver müssen in Active Directory eingebunden sein.
- Ein virtueller Switch muss erstellt werden.
- Das physische Netzwerk muss für die Subnetze und VLANs konfiguriert werden, die in der Konfigurationsdatei definiert sind.
- Die in der Konfigurationsdatei angegebene VHDX-Datei muss von dem Bereitstellungscomputer aus erreichbar sein, auf dem das SDN Express-Skript ausgeführt wird

## <a name="create-the-vdx-file"></a>Erstellen der VDX-Datei

Für SDN wird eine VHDX-Datei mit dem Azure Stack HCI-Betriebssystem als Quelle für die Erstellung der SDN-VMs verwendet. Die Version des Betriebssystems in Ihrer VHDX-Datei muss mit der Version übereinstimmen, die von den Hyper-V-Hosts für die Azure Stack HCI verwendet wird. Diese VHDX-Datei wird von allen Komponenten der SDN-Infrastruktur verwendet.

Wenn Sie das Azure Stack HCI-Betriebssystem über ein ISO-Image heruntergeladen und installiert haben, können Sie die VHDX-Datei mithilfe des Hilfsprogramms [Convert-WindowsImage](https://www.powershellgallery.com/packages/Convert-WindowsImage/10.0) erstellen.

Hier ist ein Beispiel für die Verwendung von `Convert-WindowsImage` angegeben:

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>Herunterladen des GitHub-Repositorys

Die SDN Express-Skriptdateien befinden sich auf GitHub. Im ersten Schritt werden die benötigten Dateien und Ordner auf Ihren Bereitstellungscomputer heruntergeladen.

1. Rufen Sie das Repository [Microsoft SDN GitHub](https://github.com/microsoft/SDN) auf.

1. Erweitern Sie im Repository die Dropdownliste **Code**, und klicken Sie dann entweder auf **Klonen** oder auf **ZIP herunterladen**, um die SDN-Dateien auf dem vorgesehenen Bereitstellungscomputer herunterzuladen.

    > [!NOTE]
    > Auf dem Bereitstellungscomputer muss Windows Server 2016 oder höher ausgeführt werden.

1. Extrahieren Sie die ZIP-Datei, und kopieren Sie den Ordner `SDNExpress` in den Ordner `C:\` Ihres Bereitstellungscomputers.

## <a name="edit-the-configuration-file"></a>Bearbeiten der Konfigurationsdatei

Die PowerShell-Datei `MultiNodeSampleConfig.psd1` mit den Konfigurationsdaten enthält alle Parameter und Einstellungen, die für das SDN Express-Skript als Eingabe für die verschiedenen Parameter und Konfigurationseinstellungen benötigt werden. Diese Datei enthält spezifische Informationen dazu, was Sie angeben müssen. Dies hängt davon ab, ob Sie nur die Netzwerkcontroller-Komponente oder auch die Software Load Balancer- und die Gateway-Komponente bereitstellen. Ausführliche Informationen finden Sie im Thema [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md).

Navigieren Sie in Ihrem Projekt zum Ordner `C:\SDNExpress\scripts`, und öffnen Sie die Datei `MultiNodeSampleConfig.psd1` in einem Text-Editor Ihrer Wahl. Ändern Sie die spezifischen Parameterwerte, um sie an Ihre Infrastruktur und Ihre Bereitstellung anzupassen:

### <a name="general-settings-and-parameters"></a>Allgemeine Einstellungen und Parameter

Die Einstellungen und Parameter werden von SDN generell für alle Bereitstellungen verwendet:

- **VHDPath:** VHD-Dateipfad, der von allen VMs der SDN-Infrastruktur verwendet wird (NC, SLB, Gateway)
- **VHDFile:** VHD-Dateiname, der von allen VMs der SDN-Infrastruktur verwendet wird
- **VMLocation:** Dateiname zu den VMs der SDN-Infrastruktur
- **JoinDomain:** Domäne, mit der die VMs der SDN-Infrastruktur verknüpft werden
- **SDNMacPoolStart:** MAC-Pooladresse für die VMs der Clientworkload zu Beginn
- **SDNMacPoolEnd:** MAC-Pooladresse für die VMs der Clientworkload am Ende
- **ManagementSubnet:** Subnetz des Verwaltungsnetzwerks, das vom NC verwendet wird, um Hyper-V-Hosts, SLB- und Gatewaykomponenten zu verwalten
- **ManagementGateway:** Gatewayadresse für das Verwaltungsnetzwerk
- **ManagementDNS:** DNS-Server für das Verwaltungsnetzwerk
- **ManagementVLANID:** VLAN-ID für das Verwaltungsnetzwerk
- **DomainJoinUsername:** Benutzername des Administrators
- **LocalAdminDomainUser:** Administratorkennwort
- **RestName:** DNS-Name, der von Verwaltungsclients (wie Windows Admin Center) für die Kommunikation mit dem NC verwendet wird
- **HyperVHosts:** Hostserver, die vom NC verwaltet werden sollen
- **NCUsername:** Benutzername zum NC-Konto
- **ProductKey:** Product Key für die VMs der SDN-Infrastruktur
- **SwitchName:** nur erforderlich, wenn es mehr als einen virtuellen Switch auf den Hyper-V-Hosts gibt
- **VMMemory:** Speicher (in GB), der den Infrastruktur-VMs zugewiesen ist. Der Standardwert lautet 4 GB.
- **VMProcessorCount:** Anzahl der Prozessoren, die den Infrastruktur-VMs zugewiesen sind. Der Standardwert lautet 8.
- **Locale:** Falls kein Wert angegeben ist, wird das Gebietsschema des Bereitstellungscomputers verwendet.
- **TimeZone:** Falls kein Wert angegeben ist, wird die Zeitzone verwendet, in der sich der Bereitstellungscomputer befindet.

Kennwörter können optional hinzugefügt werden, wenn sie verschlüsselt als textcodierte sichere Zeichenfolgen gespeichert werden.  Kennwörter werden nur verwendet, wenn SDN Express-Skripts auf demselben Computer ausgeführt werden, auf dem auch die Kennwörter verschlüsselt wurden. Andernfalls werden Sie zur Eingabe der folgenden Kennwörter aufgefordert:

- **DomainJoinSecurePassword:** für das Domänenkonto
- **LocalAdminSecurePassword:** für das lokale Administratorkonto
- **NCSecurePassword:** für das NC-Konto

> [!NOTE]
> Das SDN Express-Skript unterstützt die DHCP-Adressierung nicht. Stellen Sie sicher, dass die MAC-Adressen für alle VMs der SDN-Infrastruktur außerhalb des Parameterbereichs von `SDNMACPool` liegen.

### <a name="network-controller-vm-section"></a>Abschnitt zu NC-VMs

Der Abschnitt `NCs = @()` wird für die NC-VMs verwendet. Vergewissern Sie sich, dass sich die MAC-Adressen der einzelnen NC-VMs außerhalb des Bereichs `SDNMACPool` befinden, der unter den allgemeinen Einstellungen aufgeführt ist:

- **ComputerName:** Name der NC-VM
- **HostName:** Hostname des Servers, in dem sich die NC-VM befindet
- **ManagementIP:** IP-Adresse des Verwaltungsnetzwerks für die NC-VM
- **MACAddress:** MAC-Adresse für die NC-VM

### <a name="software-load-balancer-vm-section"></a>Abschnitt zu SLB-VMs

Der Abschnitt `Muxes = @()` wird für die SLB-VMs verwendet. Vergewissern Sie sich, dass sich die MAC-Adressen der einzelnen SLB-VMs außerhalb des Bereichs `SDNMACPool` befinden, der unter den allgemeinen Einstellungen aufgeführt ist. Lassen Sie den Abschnitt `Muxes = @()` leer, wenn keine SLB-Komponente bereitgestellt wird:

- **ComputerName:** Name der SLB-VM
- **HostName:** Hostname des Servers, in dem sich die SLB-VM befindet
- **ManagementIP:** IP-Adresse des Verwaltungsnetzwerks für die SLB-VM
- **MACAddress:** MAC-Adresse für die SLB-VM
- **PAIPAddress:** IP-Adresse des Anbieternetzwerks für die SLB-VM
- **PAMACAddress:** IP-Adresse des Anbieternetzwerks für die SLB-VM

### <a name="gateway-vm-section"></a>Abschnitt zu Gateway-VMs

Der Abschnitt `Gateways = @()` wird für die Gateway-VMs verwendet. Vergewissern Sie sich, dass sich die MAC-Adressen der einzelnen Gateway-VMs außerhalb des Bereichs `SDNMACPool` befinden, der unter den allgemeinen Einstellungen aufgeführt ist. Lassen Sie den Abschnitt `Gateways = @()` leer, wenn keine Gatewaykomponente bereitgestellt wird:

- **ComputerName:** Name der Gateway-VM
- **HostName:** Hostname des Servers, in dem sich die Gateway-VM befindet
- **ManagementIP:** IP-Adresse des Verwaltungsnetzwerks für die Gateway-VM
- **MACAddress:** MAC-Adresse für die Gateway-VM
- **FrontEndIp:** Front-End-IP-Adresse des Anbieternetzwerks für die Gateway-VM
- **FrontEndMac:** Front-End-MAC-Adresse des Anbieternetzwerks für die Gateway-VM
- **BackEndMac:** Back-End-MAC-Adresse des Anbieternetzwerks für die Gateway-VM

### <a name="additional-settings-for-slb-and-gateway"></a>Zusätzliche Einstellungen für SLB und das Gateway

Die folgenden zusätzlichen Parameter werden von SLB- und Gateway-VMs verwendet. Lassen Sie diese Werte leer, wenn Sie keine SLB- oder Gateway-VMs bereitstellen:

- **SDNASN:** autonome Systemnummer (ASN), die vom SDN zum Peering von Netzwerkswitches verwendet wird
- **RouterASN:** ASN des Gatewayrouters
- **RouterIPAddress:** IP-Adresse des Gatewayrouters
- **PrivateVIPSubnet:** virtuelle IP-Adresse (VIP) für das private Subnetz
- **PublicVIPSubnet:** VIP für das öffentliche Subnetz

Die folgenden zusätzlichen Parameter werden nur von Gateway-VMs verwendet. Lassen Sie diese Werte leer, wenn keine Gateway-VMs bereitgestellt werden:

- **PoolName:** Poolname, der von allen Gateway-VMs verwendet wird
- **GRESubne:t** VIP-Subnetz für GRE (wenn GRE-Verbindungen verwendet werden)
- **Capacity:** Kapazität für jede Gateway-VM im Pool in kbps

### <a name="settings-for-tenant-overlay-networks"></a>Einstellungen für Mandantenüberlagerungsnetzwerke

Verwenden Sie die folgenden Parameter, wenn Sie virtuelle Überlagerungsnetzwerke für Mandanten bereitstellen und verwalten. Wenn Sie stattdessen den NC zum Verwalten von traditionellen VLAN-Netzwerken verwenden, können Sie diese Werte leer lassen.

- **PASubnet:** Subnetz für das Netzwerk der Anbieteradresse (Provider Address, PA)
- **PAVLANID:** VLAN-ID für das PA-Netzwerk
- **PAGateway:** IP-Adresse für das PA-Netzwerkgateway
- **PAPoolStart:** IP-Adresse für den PA-Netzwerkpool zu Beginn
- **PAPoolEnd:** IP-Adresse für den PA-Netzwerkpool am Ende

## <a name="run-the-deployment-script"></a>Ausführen des Bereitstellungsskripts

Mit dem SDN Express-Skript wird die von Ihnen angegebene SDN-Infrastruktur bereitgestellt. Nach Abschluss der Skriptausführung können Sie Ihre SDN-Infrastruktur für Bereitstellungen von VM-Workloads nutzen.

1. Die Datei `README.md` enthält die neuesten Informationen zur Ausführung des Bereitstellungsskripts.  

1. Führen Sie den folgenden Befehl über ein Benutzerkonto mit Administratoranmeldeinformationen für die Clusterhostserver aus:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Konfigurieren Sie nach dem Erstellen der NC-VMs dynamische DNS-Updates für den Namen des NC-Clusters auf dem DNS-Server. Weitere Informationen finden Sie unter [Konfigurieren dynamischer DNS-Updates in Windows Server 2003](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-dns-dynamic-updates-windows-server-2003).

## <a name="next-steps"></a>Nächste Schritte

Verwalten Sie Ihre VMs. Weitere Informationen finden Sie unter [Verwalten von VMs mit Azure AD-basierter Authentifizierung und Autorisierung sowie regionsspezifischen Azure-Abonnements](../manage/vm.md).
