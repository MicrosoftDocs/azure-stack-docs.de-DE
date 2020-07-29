---
title: Verwalten von Azure Stack HCI-Clustern mit PowerShell
description: Hier erfahren Sie, wie Sie Cluster in Azure Stack HCI mit PowerShell verwalten.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 88f6ace707d14466de481f60133875968b8edce0
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947434"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Verwalten von Azure Stack HCI-Clustern mit PowerShell

> Gilt für die Azure Stack HCI, Version 20H2; Windows Server 2019

Mit Windows PowerShell können Sie in Ihren Azure Stack HCI-Clustern Ressourcen verwalten und Funktionen konfigurieren.

Cluster werden von einem Remotecomputer aus und nicht auf einem Hostserver in einem Cluster verwaltet. Dieser Remotecomputer wird als Verwaltungscomputer bezeichnet.

> [!NOTE]
> Beim Ausführen von PowerShell-Befehlen von einem Verwaltungscomputer schließen Sie den Parameter `-Name` oder `-Cluster` mit dem Namen des verwalteten Clusters ein. Außerdem müssen Sie den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) angeben, wenn Sie den Parameter `-ComputerName` für einen Serverknoten verwenden.

Die komplette Referenzdokumentation zum Verwalten von Clustern mithilfe von PowerShell finden Sie in der [FailoverCluster-V-Referenz](/powershell/module/failoverclusters/?view=win10-ps).

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

Verwenden Sie die Cmdlets `Start-Cluster` und `Stop-Cluster`, um einen Serverknoten für Ihren Cluster hinzuzufügen oder zu entfernen. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Start-Cluster](/powershell/module/failoverclusters/start-cluster?view=win10-ps) und [Stop-Cluster](/powershell/module/failoverclusters/stop-cluster?view=win10-ps).

Startet den Clusterdienst auf allen Serverknoten des Clusters, auf dem er noch nicht gestartet wurde:

```powershell
Start-Cluster -Name Cluster1
```

In diesem Beispiel wird der Clusterdienst auf allen Knoten im Cluster namens Cluster1 beendet, wodurch alle im Cluster konfigurierten Dienste und Anwendungen beendet werden:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Hinzufügen oder Entfernen eines Servers

Verwenden Sie die Cmdlets `Add-ClusterNode` und `Remove-ClusterNode`, um einen Serverknoten für Ihren Cluster hinzuzufügen oder zu entfernen. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Add-ClusterNode](/powershell/module/failoverclusters/add-clusternode?view=win10-ps) und [Remove-ClusterNode](/powershell/module/failoverclusters/remove-clusternode?view=win10-ps).

In diesem Beispiel wird einem Cluster namens Cluster1 der Server Node4 hinzugefügt. Vergewissern Sie sich, dass der Server ausgeführt wird und mit dem Clusternetzwerk verbunden ist.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

In diesem Beispiel wird der Knoten Vode4 aus dem Cluster namens Cluster1 entfernt:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="setup-the-cluster-witness"></a>Einrichten des Clusterzeugen

Legen Sie Quorumzeugenoptionen für den Cluster mit dem Cmdlet `Set-ClusterQuorum` fest. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Set-ClusterQuorum](/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps).

In diesem Beispiel wird die Quorumkonfiguration geändert, sodass ein Cloudzeuge auf Cluster1 verwendet wird:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

In diesem Beispiel wird die Quorumkonfiguration in „Knoten- und Dateifreigabemehrheit“ auf Cluster1 geändert; dabei wird die Datenträgerressource unter \\fileserver\fsw als Dateifreigabezeuge verwendet.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Aktivieren von Direkte Speicherplätze

Verwenden Sie das Cmdlet `Enable-ClusterStorageSpacesDirect`, um Direkte Speicherplätze auf dem Cluster zu aktivieren. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Enable-ClusterStorageSpacesDirect](/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps).

In diesem Beispiel wird Direkte Speicherplätze auf Server1 aktiviert:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Konfigurieren eines Hyper-V-Hosts

Verwenden Sie das Cmdlet `Set-VMHost`, um verschiedene Einstellungen für Hyper-V-Hosts wie VHD- und VM-Pfade, Livemigrationen, Speichermigrationen, Authentifizierung, Aufteilung auf NUMA und andere zu konfigurieren. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Enable-ClusterStorageSpacesDirect](/powershell/module/hyper-v/set-vmhost?view=win10-ps).

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

## <a name="remove-cluster-and-resources"></a>Entfernen des Clusters und von Ressourcen

Verwenden Sie das Cmdlet `Remove-ClusterResource`, um eine oder alle Ressourcen in einem Cluster zu entfernen. Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Remove-ClusterResource](/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps).

> [!NOTE]
> Zum Entfernen eines Clusters müssen Sie vorübergehend die CredSSP-Authentifizierung (Credential Security Service Provider) aktivieren. Weitere Informationen finden Sie unter [Enable-WSManCredSSP](/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

Im folgenden Beispiel werden Clusterressourcen nach Name von Cluster1 entfernt:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

In diesem Beispiel wird Cluster1 mithilfe des Cmdlets `Remove-Cluster` vollständig entfernt:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Nächste Schritte

- Sie sollten den Cluster im Anschluss an das Vornehmen von Änderungen überprüfen. Mehr dazu finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](../deploy/validate.md).
- Erfahren Sie, wie Sie mithilfe von Windows Admin Center Ihre Cluster verwalten. Weitere Informationen erhalten Sie unter [Verwalten von Clustern in Azure Stack HCI mithilfe von Windows Admin Center](cluster.md).
