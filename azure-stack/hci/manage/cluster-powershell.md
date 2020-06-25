---
title: Verwalten von Azure Stack HCI-Clustern mit PowerShell
description: Hier erfahren Sie, wie Sie Cluster in Azure Stack HCI mit PowerShell verwalten.
author: v-dasis
ms.topic: how-to
ms.date: 05/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7b87efc11518a982591340f9a8b5b8b72532eedc
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196390"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Verwalten von Azure Stack HCI-Clustern mit PowerShell

> Gilt für Windows Server 2019.

Mit Windows PowerShell können Sie in Ihren Azure Stack HCI-Clustern Ressourcen verwalten und Funktionen konfigurieren.

In der Regel verwalten Sie Cluster von einem Remotecomputer, auf dem Windows 10 ausgeführt wird, und nicht auf einem Hostserver in einem Cluster. Dieser Remotecomputer wird als Verwaltungscomputer bezeichnet.

> [!NOTE]
> Beim Ausführen von PowerShell-Befehlen von einem Verwaltungscomputer schließen Sie den Parameter `-Name` oder `-Cluster` mit dem Namen des verwalteten Clusters ein. Außerdem müssen Sie den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) angeben, wenn Sie den Parameter `-ComputerName` für einen Serverknoten verwenden.

Die komplette Referenzdokumentation zum Verwalten von Clustern mithilfe von PowerShell finden Sie in der [FailoverCluster-V-Referenz](https://docs.microsoft.com/powershell/module/failoverclusters/?view=win10-ps).

## <a name="using-windows-powershell"></a>Verwenden von Windows PowerShell

Sämtliche Aufgaben in diesem Artikel werden mit Windows PowerShell ausgeführt. Aus Gründen der Benutzerfreundlichkeit empfiehlt es sich, die App an die Taskleiste anzuheften.

Wenn die folgenden Cmdlets in der PowerShell-Sitzung nicht verfügbar sind, müssen Sie unter Umständen das `Failover Cluster`-Modul für Windows PowerShell mit dem folgenden PowerShell-Befehl hinzufügen: `Add-WindowsFeature RSAT-Clustering-PowerShell`.

> [!NOTE]
> Ab dem Windows 10-Update vom Oktober 2018 ist RSAT als Gruppe von „Features on Demand“ direkt in Windows 10 eingeschlossen. Wechseln Sie einfach zu **Einstellungen > Apps > Apps und Features > Optionale Features > Feature hinzufügen > RSAT: Failoverclustertools**, und wählen Sie **Installieren** aus. Um den Installationsfortschritt anzuzeigen, klicken Sie auf die Schaltfläche „Zurück“, und überprüfen Sie den Status auf der Seite „Optionale Features verwalten“. Das installierte Feature wird über Upgrades von Windows 10-Versionen hinweg beibehalten.

## <a name="view-cluster-settings-and-resources"></a>Anzeigen von Clustereinstellungen und -ressourcen

Ruft Informationen zu einem Cluster namens Cluster1 ab:

```powershell
Get-Cluster -Name Cluster1
```
Ruft Informationen zu einem oder mehreren Knoten bzw. Servern in Cluster1 ab:

```powershell
Get-ClusterNode -Cluster Cluster1
```

Überprüfen Sie mit dem Cmdlet `Get-WindowsFeature`, welche Windows-Features auf einem Clusterknoten installiert sind. Beispiel:

```powershell
Get-WindowsFeature -ComputerName Server1
```

So zeigen Sie Netzwerkadapter und deren Eigenschaften an, z. B. Name, IPv4-Adressen und VLAN-ID:

```powershell
Get-NetAdapter -CimSession Server1 | Where Status -Eq "Up" | Sort InterfaceAlias | Format-Table Name, InterfaceDescription, Status, LinkSpeed, VLANID, MacAddress
```

So zeigen Sie virtuelle Hyper-V-Switches und die Kombination von physischen Netzwerkadaptern an:

```powershell
Get-VMSwitch -ComputerName Server1
```

So zeigen Sie virtuelle Netzwerkadapter für den Host an:

```powershell
Get-VMNetworkAdapter -ComputerName Server1
```

So überprüfen Sie, ob Direkte Speicherplätze aktiviert ist:

```powershell
Get-CimSession -ComputerName Server1 | Get-ClusterStorageSpacesDirect
```

## <a name="start-or-stop-a-cluster"></a>Starten oder Beenden eines Clusters

Verwenden Sie die Cmdlets `Start-Cluster` und `Stop-Cluster`, um einen Serverknoten für Ihren Cluster hinzuzufügen oder zu entfernen. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Start-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/start-cluster?view=win10-ps) und [Stop-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/stop-cluster?view=win10-ps).

Startet den Clusterdienst auf allen Serverknoten des Clusters, auf dem er noch nicht gestartet wurde:

```powershell
Start-Cluster -Name Cluster1
```

In diesem Beispiel wird der Clusterdienst auf allen Knoten im Cluster namens Cluster1 beendet, wodurch alle im Cluster konfigurierten Dienste und Anwendungen beendet werden:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Hinzufügen oder Entfernen eines Servers

Verwenden Sie die Cmdlets `Add-ClusterNode` und `Remove-ClusterNode`, um einen Serverknoten für Ihren Cluster hinzuzufügen oder zu entfernen. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode?view=win10-ps) und [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode?view=win10-ps).

In diesem Beispiel wird einem Cluster namens Cluster1 der Server Node4 hinzugefügt. Stellen Sie zuerst sicher, dass der Server eingeschaltet und mit dem Clusternetzwerk verbunden ist.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

In diesem Beispiel wird der Knoten Vode4 aus dem Cluster namens Cluster1 entfernt:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="set-quorum-options"></a>Festlegen von Quorumoptionen

Legen Sie Quorumoptionen für den Cluster mit dem Cmdlet `Set-ClusterQuorum` fest. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Set-ClusterQuorum](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps).

In diesem Beispiel wird die Quorumkonfiguration geändert, sodass ein Cloudzeuge auf Cluster1 verwendet wird:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

In diesem Beispiel wird die Quorumkonfiguration in „Knoten- und Dateifreigabemehrheit“ auf Cluster1 geändert; dabei wird die Datenträgerressource unter \\fileserver\fsw als Dateifreigabezeuge verwendet.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Aktivieren von Direkte Speicherplätze

Verwenden Sie das Cmdlet `Enable-ClusterStorageSpacesDirect`, um Direkte Speicherplätze auf dem Cluster zu aktivieren. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps).

In diesem Beispiel wird Direkte Speicherplätze auf Server1 aktiviert:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Konfigurieren eines Hyper-V-Hosts

Verwenden Sie das Cmdlet `Set-VMHost`, um verschiedene Einstellungen für Hyper-V-Hosts wie VHD- und VM-Pfade, Livemigrationen, Speichermigrationen, Authentifizierung, Aufteilung auf NUMA und andere zu konfigurieren. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/hyper-v/Set-VMHost?view=win10-ps).

In diesem Beispiel werden neue Standardspeicherorte für virtuelle Festplatten und VMs auf dem Hostserver Server1 angegeben:

```powershell
Set-VMHost -ComputerName Server1 -VirtualHardDiskPath "C:\Hyper-V\Virtual Hard Disks" -VirtualMachinePath "C:\Hyper-V\Configuration Files"
```

In diesem Beispiel wird Hostserver Server1 so konfiguriert, dass 10 simultane Livemigrationen und Speichermigrationen zugelassen werden:

```powershell
Set-VMHost -ComputerName Server1 -MaximumVirtualMachineMigrations 10 -MaximumStorageMigrations 10
```

In diesem Beispiel wird der Hostserver Server1 so konfiguriert, dass Kerberos zum Authentifizieren eingehender Livemigrationen verwendet wird:

```powershell
Set-VMHost -ComputerName Server1 -VirtualMachineMigrationAuthenticationType Kerberos
```

## <a name="validate-a-cluster"></a>Überprüfen eines Clusters

Führen Sie mit dem Cmdlet `Test-Cluster` Validierungstests für einen Cluster aus. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Test-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/test-cluster?view=win10-ps).

In diesem Beispiel werden alle anwendbaren Clustervalidierungstests für einen Cluster namens Cluster1 ausgeführt:

```powershell
Test-Cluster -Cluster Cluster1
```

In diesem Beispiel werden die Namen aller Tests und Kategorien in der Clusterüberprüfung aufgelistet. Geben Sie diese Testnamen mit dem Parameter *Ignore* oder *Include* an, um bestimmte Tests auszuführen:


```powershell
Test-Cluster -Cluster Cluster1 -List
Category                                DisplayName                             Description
--------                                -----------                             -----------
Cluster Configuration                   List Cluster Core Groups                List information about the available...
Cluster Configuration                   List Cluster Network Information        List cluster-specific network settin...
Cluster Configuration                   List Cluster Resources                  List the resources that are configur...
Cluster Configuration                   List Cluster Volumes                    List information for the volumes in ...
Cluster Configuration                   List Clustered Roles                    List information about clustered roles.
Cluster Configuration                   Validate Quorum Configuration           Validate that the current quorum con...
Cluster Configuration                   Validate Resource Status                Validate that cluster resources are ...
Cluster Configuration                   Validate Service Principal Name         Validate that a Service Principal Na...
Cluster Configuration                   Validate Volume Consistency             If any volumes are flagged as incons...
Hyper-V Configuration                   List Information About Servers Runni... List Hyper-V related information on ...
Hyper-V Configuration                   Validate Compatibility of Virtual Fi... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Memory Resource Poo... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Network Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Processor Resource ... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Role Installed         Validate that all the nodes have the...
Hyper-V Configuration                   Validate Hyper-V Storage Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Matching Processor Manufact... Validate that all specified nodes sh...
Hyper-V Configuration                   List Hyper-V Virtual Machine Informa... List Hyper-V virtual machine informa...
Hyper-V Configuration                   Validate Hyper-V Integration Service... Validate that the Hyper-V integratio...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Net... Validate that all virtual machines o...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Sto... Validate that all virtual machines o...
Inventory                               List Fibre Channel Host Bus Adapters    List Fibre Channel host bus adapters...
Inventory                               List iSCSI Host Bus Adapters            List iSCSI host bus adapters on each...
Inventory                               List SAS Host Bus Adapters              List Serial Attached SCSI (SAS) host...
Inventory                               List BIOS Information                   List BIOS information from each node...
Inventory                               List Environment Variables              List environment variables set on ea...
Inventory                               List Memory Information                 List memory information for each node.
Inventory                               List Operating System Information       List information about the operating...
Inventory                               List Plug and Play Devices              List Plug and Play devices on each n...
Inventory                               List Running Processes                  List the running processes on each n...
Inventory                               List Services Information               List information about the services ...
Inventory                               List Software Updates                   List software updates that have been...
Inventory                               List System Drivers                     List the system drivers on each node.
Inventory                               List System Information                 List system information such as comp...
Inventory                               List Unsigned Drivers                   List the unsigned drivers on each node.
Network                                 List Network Binding Order              List the order in which networks are...
Network                                 Validate Cluster Network Configuration  Validate the cluster networks that w...
Network                                 Validate IP Configuration               Validate that IP addresses are uniqu...
Network                                 Validate Multiple Subnet Properties     For clusters using multiple subnets,...
Network                                 Validate Network Communication          Validate that servers can communicat...
Network                                 Validate Windows Firewall Configuration Validate that the Windows Firewall i...
Storage                                 List Disks                              List all disks visible to one or mor...
Storage                                 List Potential Cluster Disks            List disks that will be validated fo...
Storage                                 Validate CSV Network Bindings           Validate that network bindings requi...
Storage                                 Validate CSV Settings                   Validate that settings and configura...
Storage                                 Validate Disk Access Latency            Validate acceptable latency for disk...
Storage                                 Validate Disk Arbitration               Validate that a node that owns a dis...
Storage                                 Validate Disk Failover                  Validate that a disk can fail over s...
Storage                                 Validate File System                    Validate that the file system on dis...
Storage                                 Validate Microsoft MPIO-based disks     Validate that disks that use Microso...
Storage                                 Validate Multiple Arbitration           Validate that in a multiple-node arb...
Storage                                 Validate SCSI device Vital Product D... Validate uniqueness of inquiry data ...
Storage                                 Validate SCSI-3 Persistent Reservation  Validate that storage supports the S...
Storage                                 Validate Simultaneous Failover          Validate that disks can fail over si...
System Configuration                    Validate Active Directory Configuration Validate that all the nodes have the...
System Configuration                    Validate All Drivers Signed             Validate that tested servers contain...
System Configuration                    Validate Cluster Service and Driver ... Validate startup settings used by se...
System Configuration                    Validate Memory Dump Settings           Validate that none of the nodes curr...
System Configuration                    Validate Operating System Edition       Validate that all tested servers are...
System Configuration                    Validate Operating System Installati... Validate that the operating systems ...
System Configuration                    Validate Operating System Version       Validate that the operating systems ...
System Configuration                    Validate Required Services              Validate that services required for ...
System Configuration                    Validate Same Processor Architecture    Validate that all servers run as 64-...
System Configuration                    Validate Service Pack Levels            Validate that all servers with same ...
System Configuration                    Validate Software Update Levels         Validate that all tested servers hav...
System Configuration                    Validate System Drive Variable          Validate that all nodes have the sam...
```

## <a name="remove-cluster-and-resources"></a>Entfernen des Clusters und von Ressourcen

Verwenden Sie das Cmdlet `Remove-ClusterResource`, um eine oder alle Ressourcen in einem Cluster zu entfernen. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Remove-ClusterResource](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps).

> [!NOTE]
> Zum Entfernen eines Clusters müssen Sie vorübergehend die CredSSP-Authentifizierung (Credential Security Service Provider) aktivieren. Weitere Informationen finden Sie unter [Enable-WSManCredSSP](https://docs.microsoft.com/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

Im folgenden Beispiel werden Clusterressourcen nach Name von Cluster1 entfernt:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

In diesem Beispiel wird Cluster1 mithilfe des Cmdlets `Remove-Cluster` vollständig entfernt:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie mithilfe von Windows Admin Center Ihre Cluster verwalten. Weitere Informationen erhalten Sie unter [Verwalten von Clustern in Azure Stack HCI mithilfe von Windows Admin Center](cluster.md).