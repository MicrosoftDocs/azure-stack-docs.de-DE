---
title: Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell
description: Erfahren Sie, wie Sie mithilfe von Windows PowerShell einen hyperkonvergenten Cluster für Azure Stack HCI erstellen.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ab2edb1dc5650a313ba84af89d0ff386e60f8264
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868071"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-powershell"></a>Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell

> Gilt für: Azure Stack HCI, Version v20H2

In diesem Artikel erfahren Sie, wie Sie Windows PowerShell verwenden, um einen hyperkonvergenten Azure Stack HCI-Cluster erstellen, der Direkte Speicherplätze verwendet. Wenn Sie es vorziehen, zum Erstellen des Clusters den Assistenten zur Clustererstellung in Windows Admin Center zu verwenden, lesen Sie [Erstellen des Clusters mit Windows Admin Center](create-cluster.md).

Sie haben die Wahl zwischen zwei Clustertypen:

- Standardcluster mit mindestens zwei Serverknoten, die alle an einem einzelnen Standort betrieben werden.
- Stretchingcluster mit mindestens vier Serverknoten, die sich über zwei Standorte erstrecken und pro Standort zwei Knoten aufweisen.

In diesem Artikel erstellen wir einen Beispielcluster mit dem Namen Cluster1, der sich aus vier Serverknoten mit den Namen Server1, Server2, Server3 und Server4 zusammensetzt.

Für das Stretchingcluster-Szenario verwenden wir ClusterS1 als Namen und ansonsten die gleichen vier Serverknoten, die sich über die Standorte Site1 und Site2 erstrecken.

Weitere Informationen zu Stretchingclustern finden Sie unter [Stretchingcluster: Übersicht](../concepts/stretched-clusters.md).

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Lesen Sie die Hardware- und sonstigen Anforderungen in [Vor dem Bereitstellen von Azure Stack HCI](before-you-start.md).
- Installieren Sie das Azure Stack HCI-Betriebssystem auf jedem Server im Cluster. Mehr dazu finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md).
- Auf jedem Server ist ein Konto vorhanden, das Mitglied der lokalen Administratorgruppe ist.
- Sie verfügen über Berechtigungen zum Erstellen von Objekten in Active Directory.

## <a name="using-windows-powershell"></a>Verwenden von Windows PowerShell

Sie können PowerShell entweder lokal in einer RDP-Sitzung auf einem Hostserver oder remote auf einem Verwaltungscomputer ausführen. In diesem Artikel wird die Remoteoption behandelt.

Beim Ausführen von PowerShell von einem Verwaltungscomputer schließen Sie den Parameter `-Name` oder `-Cluster` mit dem Namen des verwalteten Servers oder Clusters ein. Außerdem müssen Sie möglicherweise den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) angeben, wenn Sie den Parameter `-ComputerName` für einen Serverknoten verwenden.

Sie benötigen ferner die RSAT-Cmdlets (Remote Server Administration Tools) und PowerShell-Module für Hyper-V und Failoverclustering. Wenn diese in der PowerShell-Sitzung auf Ihrem Verwaltungscomputer noch nicht verfügbar sind, können Sie sie mit dem folgenden Befehl aktivieren: `Add-WindowsFeature RSAT-Clustering-PowerShell`.

## <a name="step-1-provision-the-servers"></a>Schritt 1: Bereitstellen der Server

Zunächst stellen wir eine Verbindung zu jedem der Server her, verknüpfen ihn mit einer Domäne (derselben Domäne, in der sich der Verwaltungscomputer befindet) und installieren die erforderlichen Rollen und Features.

### <a name="step-11-connect-to-the-servers"></a>Schritt 1.1: Herstellen von Verbindungen mit den Servern

Zum Herstellen von Verbindungen mit den Servern müssen Sie zunächst über Netzwerkkonnektivität verfügen, Mitglied der gleichen Domäne oder einer Domäne mit voller Vertrauensstellung sein und über lokale Verwaltungsberechtigungen für die Server verfügen.

Öffnen Sie PowerShell, und verwenden Sie entweder den vollqualifizierten Domänennamen oder die IP-Adresse des Servers, mit dem Sie eine Verbindung herstellen möchten. Nachdem Sie den folgenden Befehl auf jedem Server (Server1, Server2, Server3, Server4) ausgeführt haben, werden Sie zur Eingabe eines Kennworts aufgefordert:

   ```powershell
   Enter-PSSession -ComputerName Server1 -Credential Server1\Administrator
   ```

Hier sehen Sie ein weiteres Beispiel für den gleichen Vorgang:

   ```powershell
   $myServer1 = "Server1"
   $user = "$myServer1\Administrator"

   Enter-PSSession -ComputerName $myServer1 -Credential $user
   ```

> [!TIP]
> Wenn Sie PowerShell-Befehle an Ihrem Verwaltungs-PC ausführen, wird möglicherweise ein Fehler wie *WinRM kann die Anforderung nicht verarbeiten* angezeigt. Um dieses Problem zu beheben, fügen Sie jeden Server mithilfe von PowerShell der Liste vertrauenswürdiger Hosts auf Ihrem Verwaltungscomputer hinzu. Die Liste unterstützt Platzhalterzeichen, wie etwa `Server*`.
>
> `Set-Item WSMAN:\Localhost\Client\TrustedHosts -Value Server1 -Force`
>  
> Geben Sie `Get-Item WSMAN:\Localhost\Client\TrustedHosts` ein, um die Liste vertrauenswürdiger Server anzuzeigen.  
>
> Um die Liste zu leeren, geben Sie `Clear-Item WSMAN:\Localhost\Client\TrustedHost` ein.  

### <a name="step-12-join-the-domain-and-add-domain-accounts"></a>Schritt 1.2: Beitreten zur Domäne und Hinzufügen von Domänenkonten

Bisher haben Sie eine Verbindung mit jedem Serverknoten mithilfe des lokalen Administratorkontos `<ServerName>\Administrator` hergestellt.

Um fortzufahren, müssen Sie die Server einer Domäne hinzufügen und das Domänenkonto verwenden, das sich in der lokalen Gruppe „Administratoren“ auf jedem Server befindet.

Verwenden Sie das Cmdlet `Enter-PSSession`, um eine Verbindung mit jedem Server herzustellen, und führen Sie das folgende Cmdlet aus, wobei Sie den Servernamen, den Domänennamen und die Domänenanmeldeinformationen ersetzen:

```powershell  
Add-Computer -NewName "Server1" -DomainName "contoso.com" -Credential "Contoso\User" -Restart -Force  
```

Wenn Ihr Administratorkonto kein Mitglied der Gruppe „Domänenadministratoren“ ist, fügen Sie Ihr Administratorkonto der lokalen Gruppe „Administratoren“ auf jedem Server hinzu – oder fügen Sie besser noch die Gruppe hinzu, die Sie für Administratoren verwenden. Dazu können Sie den folgenden Befehl verwenden:

```powershell
Add-LocalGroupMember -Group "Administrators" -Member "king@contoso.local"
```

### <a name="step-13-install-roles-and-features"></a>Schritt 1.3: Installieren von Rollen und Features

Der nächste Schritt besteht darin, auf jedem Server für den Cluster die erforderlichen Rollen und Features zu installieren. Diese Rollen müssen installiert werden:

- BitLocker
- Data Center Bridging (für RoCEv2-Netzwerkadapter)
- Failoverclustering
- Dateiserver (für Dateifreigabezeugen oder das Hosten von Dateifreigaben)
- FS-Datendeduplizierungsmodul
- Hyper-V
- RSAT-AD-PowerShell-Modul
- Speicherreplikation (nur für Stretchingcluster)

Verwenden Sie für jeden Server den folgenden Befehl:

```powershell
Install-WindowsFeature -ComputerName Server1 -Name "BitLocker", "Data-Center-Bridging", "Failover-Clustering  -IncludeAllSubFeature -IncludeManagementTools", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica"
```

Um den Befehl auf allen Servern im Cluster zugleich auszuführen, verwenden Sie das folgende Skript, und ändern Sie die Liste der Variablen am Anfang für Ihre Umgebung entsprechend.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"
$FeatureList = "BitLocker", "Data-Center-Bridging", "Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica"

# This part runs the Install-WindowsFeature cmdlet on all servers in $ServerList, passing the list of features in $FeatureList.
Invoke-Command ($ServerList) {
    Install-WindowsFeature -Name $Using:Featurelist
}
```
Starten Sie anschließend alle Server neu:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Restart-Computer -ComputerName $ServerList
```

## <a name="step-2-configure-networking"></a>Schritt 2: Konfigurieren der Netzwerkeinstellungen

Bei diesem Schritt wird davon ausgegangen, dass Sie RDMA und andere Netzwerke für Ihre Umgebung bereits eingerichtet haben.

### <a name="disable-unused-networks"></a>Deaktivieren nicht verwendeter Netzwerke

Sie müssen alle getrennten oder nicht für die Verwaltung, die Speicherung oder den Workload-Datenverkehr (etwa mit VMs) verwendeten Netzwerke deaktivieren. So identifizieren Sie nicht verwendete Netzwerke:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected
```
Und auf diese Weise deaktivieren Sie sie:

```powershell
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected | Disable-NetAdapter -Confirm:$False
```

### <a name="assign-virtual-network-adapters"></a>Zuweisen von virtuellen Netzwerkadaptern

Als Nächstes weisen Sie zur Verwaltung und für Ihren restlichen Datenverkehr virtuelle Netzwerkadapter (vNICs) zu, wie hier im Beispiel zu sehen:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
Rename-VMNetworkAdapter -ManagementOS -Name $vSwitchName -NewName Management -ComputerName $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB01 -SwitchName $vSwitchName -CimSession $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB02 -SwitchName $vSwitchName -Cimsession $Servers
```

Dann überprüfen Sie, ob sie erfolgreich hinzugefügt und zugewiesen wurden:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMNetworkAdapter -CimSession $Servers -ManagementOS
```

### <a name="create-virtual-switches"></a>Erstellen virtueller Switches

Für jeden Serverknoten in Ihrem Cluster ist ein virtueller Switch erforderlich. Im folgenden Beispiel wird ein virtueller Switch mit SR-IOV-Funktion mithilfe von verbundenen Netzwerkadaptern erstellt (der Status ist UP). Die aktivierte SR-IOV-Funktion kann nützlich sein, da sie für RDMA-fähige vmNICs (vNICs für VMs) erforderlich ist.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
```

Und so erstellen Sie den virtuellen Switch:

```powershell
Invoke-Command -ComputerName $Servers -ScriptBlock {New-VMSwitch -Name $using:vSwitchName -EnableEmbeddedTeaming $TRUE -EnableIov $true -NetAdapterName (Get-NetAdapter | Where-Object Status -eq Up ).InterfaceAlias}
```

Überprüfen Sie jetzt, ob der Switch erfolgreich erstellt wurde:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMSwitch -CimSession $Servers | Select-Object Name, IOVEnabled, IOVS*
Get-VMSwitchTeam -CimSession $Servers
```

### <a name="configure-ip-addresses-and-vlans"></a>Konfigurieren von IP-Adressen und VLANs

Sie können wahlweise ein oder zwei Subnetze konfigurieren. Zwei Subnetze sind vorzuziehen, wenn Sie eine Überlastung der Verbindung zwischen den Switches verhindern möchten. Beispielsweise bleibt der Datenverkehr für den SMB-Speicher in einem Subnetz, das dediziert einem physischen Switch zugewiesen ist.

### <a name="obtain-network-interface-information"></a>Abrufen von Informationen zur Netzwerkschnittstelle

Bevor Sie IP-Adressen für die Netzwerkschnittstellenkarten festlegen können, benötigen Sie zunächst einige Informationen, wie etwa den Schnittstellenindex (`ifIndex`), `Interface Alias` und `Address Family`. Notieren Sie diese für jeden Serverknoten, da Sie sie später benötigen.

Führen Sie Folgendes aus:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetIPInterface -ComputerName $ServerList
```

#### <a name="configure-one-subnet"></a>Konfigurieren eines Subnetzes

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet="172.16.1."
$StorVLAN=1
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    $IP++
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="configure-two-subnets"></a>Konfigurieren von zwei Subnetzen

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet1="172.16.1."
$StorNet2="172.16.2."
$StorVLAN1=1
$StorVLAN2=2
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet1+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    New-NetIPAddress -IPAddress ($StorNet2+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN1 -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN2 -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="verify-vlan-ids-and-subnets"></a>Überprüfen von VLAN-IDs und-Subnetzen

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
#verify ip config
Get-NetIPAddress -CimSession $servers -InterfaceAlias vEthernet* -AddressFamily IPv4 | Sort-Object -Property PSComputername | ft pscomputername,interfacealias,ipaddress -AutoSize -GroupBy PSComputerName

#Verify that the VlanID is set
Get-VMNetworkAdapterVlan -ManagementOS -CimSession $servers | Sort-Object -Property Computername | Format-Table ComputerName,AccessVlanID,ParentAdapter -AutoSize -GroupBy ComputerName
```

## <a name="step-3-prep-for-cluster-setup"></a>Schritt 3: Vorbereitung der Clustereinrichtung

Überprüfen Sie als Nächstes, ob Ihre Server für das Clustering bereit sind. Als einleitende Integritätsprüfung können Sie die folgenden Befehle ausführen, um sicherzustellen, dass die Server noch nicht zu einem Cluster gehören:

Verwenden Sie `Get-ClusterNode`, um alle Knoten anzuzeigen:

```powershell
Get-ClusterNode
```

Verwenden Sie `Get-ClusterResource`, um alle Clusterknoten anzuzeigen:

```powershell
Get-ClusterResource
```

Verwenden Sie `Get-ClusterNetwork`, um alle Clusternetzwerke anzuzeigen:

```powershell
Get-ClusterNetwork
```

### <a name="step-31-prepare-drives"></a>Schritt 3.1: Vorbereiten von Laufwerken

Vergewissern Sie sich vor dem Aktivieren von Direkte Speicherplätze, dass Ihre Laufwerke leer sind. Führen Sie das folgende Skript aus, um alle alten Partitionen oder sonstigen Daten zu entfernen.

> [!WARNING]
> Dieses Skript entfernt endgültig alle Daten auf allen Laufwerken mit Ausnahme des Azure Stack HCI-System-Startlaufwerks.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"

Invoke-Command ($ServerList) {
    Update-StorageProviderCache
    Get-StoragePool | ? IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -ne RAW | % {
        $_ | Set-Disk -isoffline:$false
        $_ | Set-Disk -isreadonly:$false
        $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
        $_ | Set-Disk -isreadonly:$true
        $_ | Set-Disk -isoffline:$true
    }
    Get-Disk | Where Number -Ne $Null | Where IsBoot -Ne $True | Where IsSystem -Ne $True | Where PartitionStyle -Eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

### <a name="step-32-test-cluster-configuration"></a>Schritt 3.2: Testen der Clusterkonfiguration

In diesem Schritt stellen Sie sicher, dass die Serverknoten ordnungsgemäß für die Erstellung eines Clusters konfiguriert sind. Das Cmdlet `Test-Cluster` wird für Tests verwendet, mit denen überprüft wird, ob Ihre Konfiguration passend ist, um als hyperkonvergenter Cluster zu fungieren. Im Beispiel unten wird der Parameter `-Include` mit Angabe der spezifischen Testkategorien verwendet. Dadurch wird sichergestellt, dass die richtigen Tests in die Überprüfung eingeschlossen werden.

```powershell
Test-Cluster -Cluster –Node Server1, Server2, Server3, Server4 –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

## <a name="step-4-create-the-cluster"></a>Schritt 4: Erstellen des Clusters

Sie sind jetzt bereit, einen Cluster mit den Serverknoten zu erstellen, die Sie in den vorhergehenden Schritten überprüft haben.

Beim Erstellen des Clusters wird eine Warnung dieses Inhalts angezeigt: `"There were issues while creating the clustered role that may prevent it from starting. For more information, view the report file below."`. Sie können diese Warnung gefahrlos ignorieren. Sie beruht darauf, dass für den Clusterzeugen, den Sie später erstellen, keine Datenträger verfügbar sind. 

> [!NOTE]
> Wenn die Server statische IP-Adressen verwenden, ändern Sie den folgenden Befehl so, dass er die statischen IP-Adressen berücksichtigt, indem Sie den folgenden Parameter hinzufügen und die IP-Adresse angeben: `–StaticAddress <X.X.X.X>;`.

```powershell
 New-Cluster –Name Cluster1 –Node Server1, Server2, Server3, Server4 –NoStorage
```

Herzlichen Glückwunsch, Ihr Cluster wurde soeben erstellt.

> [!NOTE]
> Nachdem der Cluster erstellt wurde, kann es eine Weile dauern, bis der Clustername repliziert wird. Wenn das Auflösen des Clusters nicht erfolgreich ist, können Sie in den meisten Fällen den Computernamen eines Serverknotens im Cluster anstelle des Clusternamens angeben.

## <a name="step-5-set-up-sites-stretched-cluster"></a>Schritt 5: Einrichten von Standorten (Stretchingcluster)

Diese Aufgabe wird nur ausgeführt, wenn Sie einen Stretchingcluster zwischen zwei Standorten erstellen.

### <a name="step-51-create-sites"></a>Schritt 5.1: Erstellen von Websites

Im Cmdlet unten stellt *FaultDomain* lediglich einen Namen für einen Standort dar. Im Beispiel wird `ClusterS1` als Name des Stretchingclusters verwendet.

```powershell
New-ClusterFaultDomain -CimSession ClusterS1 -Type Site -Name Site1
```

```powershell
New-ClusterFaultDomain -CimSession ClusterS1 -Type Site -Name Site2
```

Verwenden Sie das Cmdlet `Get-ClusterFaultDomain`, um zu überprüfen, ob beide Standorte für den Cluster erstellt wurden.

```powershell
Get-ClusterFaultDomain
```

### <a name="step-52-assign-server-nodes"></a>Schritt 5.2: Zuweisen von Serverknoten

Als Nächstes weisen wir die vier Serverknoten ihren jeweiligen Standorten zu. Im Beispiel unten werden Server1 und Server2 Site1 (Standort1), Server3 und Server4 dagegen Site2 (Standort2) zugewiesen.

```powershell
Set-ClusterFaultDomain -CimSession ClusterS1 -Name Server1, Server2 -Parent Site1
```

```powershell
Set-ClusterFaultDomain -CimSession ClusterS1 -Name Server3, Server4 -Parent Site2
```

Überprüfen Sie mithilfe des Cmdlets `Get-ClusterFaultDomain`, ob sich die Knoten an den richtigen Standorten befinden.

```powershell
Get-ClusterFaultDomain -CimSession ClusterS1
```

### <a name="step-53-set-a-preferred-site"></a>Schritt 5.3: Festlegen eines bevorzugten Standorts

Sie können auch einen global *bevorzugten* Standort definieren, was bedeutet, dass die angegebenen Ressourcen und Gruppen an dem bevorzugten Standort ausgeführt werden müssen.  Diese Einstellung kann mit dem folgenden Befehl auf Standortebene festgelegt werden:  

```powershell
(Get-Cluster).PreferredSite = Site1
```

Die Angabe eines bevorzugten Standorts für Stretchingcluster bietet die folgenden Vorteile:

- **Kaltstart**: Bei einem Kaltstart werden die virtuellen Computer am bevorzugten Standort platziert

- **Quorumabstimmung**
  - Mithilfe eines dynamischen Quorums wird die Gewichtung des passiven (replizierten) Standorts zuerst verringert, um sicherzustellen, dass der bevorzugte Standort überlebt, wenn alle anderen Umstände gleich sind. Darüber hinaus werden Serverknoten nach Ereignissen wie einem asymmetrischen Ausfall der Netzwerkkonnektivität bei der Neugruppierung am passiven Standort zuerst reduziert.

  - Während einer Quorumaufteilung zweier Standorte kann keine Verbindung mit dem Clusterzeugen hergestellt werden, der bevorzugte Standort wird dann automatisch zum Sieger bestimmt. Die Serverknoten am passiven Standort verlieren dann ihre Clustermitgliedschaft. Auf diese Weise kann der Cluster einen gleichzeitigen 50 %-Verlust der Stimmen überstehen.

Der bevorzugte Standort kann darüber hinaus auf der Ebene der Clusterrolle oder der Gruppe definiert werden. In diesem Fall kann für jede virtuelle Computergruppe ein anderer bevorzugter Standort konfiguriert werden. Dadurch kann ein Standort aktiv und für bestimmte virtuelle Computer bevorzugt sein.

## <a name="step-6-enable-storage-spaces-direct"></a>Schritt 6: Aktivieren von Direkte Speicherplätze

Verwenden Sie nach dem Erstellen des Clusters das Cmdlet `Enable-ClusterStorageSpacesDirect`, das Direkte Speicherplätze aktiviert und die folgenden Aktionen automatisch ausführt:

- **Erstellen eines Speicherpools:** Erstellt einen Speicherpool für den Cluster und gibt ihm einen Namen wie „Cluster1 Storage Pool“.

- **Erstellen eines Datenträgers für den Leistungsverlauf des Clusters:** Erstellt einen virtuellen Datenträger für den Leistungsverlauf des Clusters im Speicherpool.

- **Erstellen von Daten- und Protokollvolumes:** Erstellt ein Datenvolume und ein Protokollvolume im Speicherpool.

- **Konfigurieren von Caches für Direkte Speicherplätze:** Wenn für Direkte Speicherplätze mehr als ein Medientyp (Laufwerk) verfügbar ist, werden die schnellsten als Cachegeräte aktiviert (In den meisten Fällen Lese- und Schreibcache).

- **Erstellen von Ebenen:** Erstellt zwei Ebenen als Standardebenen. Eine trägt den Namen „Capacity“ (Kapazität), die andere den Namen „Performance“ (Leistung). Das Cmdlet analysiert die Geräte und konfiguriert jede Ebene mit der Mischung aus Gerätetypen und Resilienz.

Bei Stretchingclustern führt das Cmdlet `Enable-ClusterStorageSpacesDirect` außerdem Folgendes aus:

- Überprüfen, ob die Standorte eingerichtet wurden
- Bestimmen, welche Knoten sich an welchen Standorten befinden
- Bestimmen, welcher Speicher für die einzelnen Knoten zur Verfügung steht
- Überprüfen, ob die Funktion Speicherreplikation auf jedem Knoten installiert ist
- Erstellt einen Speicherpool für jeden Standort und identifiziert ihn mit dem gleichen des Standorts
- Erstellt Daten- und Protokollvolumes in jedem Speicherpool, eins pro Standort

Mit dem folgenden Befehl wird Direkte Speicherplätze aktiviert. Sie können außerdem einen Anzeigenamen für einen Speicherpool angeben, wie hier gezeigt:

```powershell
New-CimSession -Cluster Cluster1 | Enable-ClusterStorageSpacesDirect -PoolFriendlyName 'Cluster1 Storage Pool'
```

Verwenden Sie diesen Befehl, um die Speicherpools anzuzeigen:

```powershell
Get-StoragePool -Cluster Cluster1
```

Herzlichen Glückwunsch, Sie haben nun einen Barebonescluster erstellt.

## <a name="after-you-create-the-cluster"></a>Nach dem Erstellen des Clusters

Jetzt, da Sie die Clustererstellung abgeschlossen haben, gibt es noch einige wichtige Aufgaben, die Sie ausführen müssen, um einen voll funktionsfähigen Cluster zu erhalten:

- Einrichten eines Clusterzeugen. Mehr dazu finden Sie unter [Einrichten eines Clusterzeugen](witness.md).
- Erstellen Ihrer Volumes. Mehr dazu finden Sie unter [Erstellen von Volumes](../manage/create-volumes.md).
- Erstellen Sie für Stretchingcluster Volumes und die Setupreplikation mithilfe von Speicherreplikation. Mehr dazu finden Sie unter [Erstellen von Volumes und Einrichten von Replikation für Stretchingcluster](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Nächste Schritte

- Registrieren Sie Ihren Cluster bei Azure. Mehr dazu finden Sie unter [Verwalten der Azure-Registrierung](../manage/manage-azure-registration.md).
- Führen Sie eine abschließende Überprüfung des Clusters durch. Mehr dazu finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](validate.md).
- Stellen Sie Ihre virtuellen Computer bereit. Lesen Sie dazu [Verwalten von VMs unter Azure Stack HCI mit PowerShell](../manage/vm-powershell.md).
- Sie können einen Cluster auch mit Windows Admin Center erstellen. Mehr dazu finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](create-cluster.md).
