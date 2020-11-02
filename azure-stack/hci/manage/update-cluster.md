---
title: Aktualisieren von Azure Stack HCI-Clustern
description: Hier erfahren Sie, wie Sie Betriebssystem- und Firmwareupdates über Windows Admin Center und PowerShell auf Azure Stack HCI anwenden.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: acb3b9c8c0db738d04bba44ccec799a5f9c0939b
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688306"
---
# <a name="update-azure-stack-hci-clusters"></a>Aktualisieren von Azure Stack HCI-Clustern

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Beim Aktualisieren von Azure Stack HCI-Clustern besteht das Ziel darin, die Verfügbarkeit aufrechtzuerhalten, indem jeweils nur ein Server im Cluster aktualisiert wird. Bei vielen Betriebssystemupdates ist es erforderlich, den Server offline zu schalten, um z. B. einen Neustart auszuführen oder Software wie den Netzwerkstapel zu aktualisieren. Es wird empfohlen, das Feature „Clusterfähiges Aktualisieren“ zu nutzen, das die Installation von Updates auf jedem Server in Ihrem Cluster vereinfacht, ohne die Ausführung Ihrer Anwendungen zu unterbrechen. Beim clusterfähigen Aktualisieren wird der Server beim Installieren von Updates automatisch in den Wartungsmodus versetzt sowie dieser wieder aufgehoben, und der Server wird ggf. automatisch neu gestartet. Clusterfähiges Aktualisieren ist die Standardmethode für die Aktualisierung, die vom Windows Admin Center verwendet wird, und kann auch mithilfe der PowerShell initiiert werden.

   > [!IMPORTANT]
   > Das Vorschauupdate (KB4580388) vom 20.10.2020 für Azure Stack HCI kann zu einem Fehler bei einer clusterfähigen Aktualisierung führen, wenn einer der virtuellen Computer während der clusterfähigen Aktualisierung eine Livemigration ausführen soll. Eine Problemumgehung finden Sie in den [Versionshinweisen](../release-notes.md#october-20-2020-preview-update-kb4580388).

Dieses Thema konzentriert sich auf Betriebssystem- und Softwareupdates. Informationen dazu, wenn Sie einen Server offline schalten müssen, um Wartungsarbeiten an der Hardware durchzuführen, finden Sie unter [Offlineschalten eines Servers zur Wartung](maintain-servers.md).

## <a name="update-a-cluster-using-windows-admin-center"></a>Aktualisieren eines Clusters über Windows Admin Center

Windows Admin Center bietet eine einfache Benutzeroberfläche, über die Sie problemlos einen Cluster aktualisieren und Betriebssystem- und Lösungsupdates anwenden können. Wenn Sie ein integriertes System bei einem Microsoft-Hardwarepartner erworben haben, können Sie ganz einfach die neuesten Treiber-, Firmware- und anderweitigen Updates direkt vom Windows Admin Center abrufen, indem Sie die entsprechenden Updateerweiterung des Partners installieren. Wenn Ihre Hardware nicht als integriertes System erworben wurde, müssen Firmware- und Treiberupdates möglicherweise separat ausgeführt werden. Folgen Sie dazu den Empfehlungen des Hardwareherstellers.

Windows Admin Center prüft, ob der Cluster richtig konfiguriert ist, um clusterfähiges Aktualisieren auszuführen, und fragt bei Bedarf, ob das Windows Admin Center die CAU für Sie konfigurieren soll, einschließlich der Installation der CAU-Clusterrolle und der Aktivierung der erforderlichen Firewallregeln.

1. Wenn Sie eine Verbindung mit einem Cluster herstellen, informiert das Windows Admin Center-Dashboard Sie, falls für einen Ihrer Server ein Update zu installieren ist, und stellt einen Link bereit, über den Sie das Update direkt ausführen können. Alternativ dazu können Sie im Menü **Tools** auf der linken Seite die Option **Updates** auswählen.
1. Um die clusterfähige Aktualisierung in Windows Admin Center zu verwenden, müssen Sie den Credential Security Service Provider (CredSSP) aktivieren und explizite Anmeldeinformationen angeben. Wenn Sie gefragt werden, ob der CredSSP aktiviert werden soll, klicken Sie auf **Ja** .
1. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, und klicken Sie auf **Weiter** .
1. Alle verfügbaren Updates werden angezeigt. Klicken Sie auf **Verfügbare Updates überprüfen** , um die Liste zu aktualisieren.
1. Wählen Sie die Updates aus, die Sie installieren möchten, und klicken Sie auf **Alle Updates anwenden** . Damit werden die Updates auf jedem Server im Cluster installiert. Wenn ein Neustart erforderlich ist, werden Clusterrollen wie z. B. virtuelle Computer zuerst auf einen anderen Server verschoben, um Unterbrechungen zu vermeiden.
1. Um die Sicherheit zu verbessern, deaktivieren Sie CredSSP, sobald Sie mit dem Installieren der Updates fertig sind:
    - Wählen Sie in Windows Admin Center unter **Alle Verbindungen** den ersten Server im Cluster aus, und klicken Sie auf **Verbinden** .
    - Wählen Sie auf der Seite **Übersicht** die Option **CredSSP deaktivieren** aus, und klicken Sie dann im Popupfenster **CredSSP deaktivieren** auf **Ja** .

## <a name="update-a-cluster-using-powershell"></a>Aktualisieren eines Clusters mithilfe von PowerShell

Bevor Sie das clusterfähige Aktualisieren verwenden können, um einen Cluster zu aktualisieren, müssen Sie zunächst die **Failoverclusteringtools** installieren, die zu den **Remoteserver-Verwaltungstools** (Remote Server Administration Tools, RSAT) gehören und die Software für das clusterfähige Aktualisieren enthalten. Wenn Sie einen vorhandenen Cluster aktualisieren, sind die Tools möglicherweise bereits installiert.

Um zu überprüfen, ob ein Failovercluster ordnungsgemäß eingerichtet ist, sodass Softwareupdates mithilfe des clusterfähigen Aktualisierens angewendet werden können, führen Sie das PowerShell-Cmdlet **Test-CauSetup** aus. Dieses führt eine BPA-Überprüfung (Best Practices Analyzer) des Failoverclusters und der Netzwerkumgebung aus und informiert Sie über etwaige Warnungen oder Fehler:

```PowerShell
Test-CauSetup -ClusterName Cluster1
```

Wenn Sie Features, Tools oder Rollen installieren müssen, lesen Sie die nächsten Abschnitte. Andernfalls können Sie direkt mit dem [Überprüfen auf Updates mit PowerShell](#check-for-updates-with-powershell) fortfahren.

### <a name="install-failover-clustering-and-failover-clustering-tools-using-powershell"></a>Installieren von Failoverclustering und Failoverclusteringtools mithilfe von PowerShell

Um zu überprüfen, ob das Failoverclustering und die Failoverclusteringtools bereits auf einem Server oder in einem Cluster installiert sind, führen Sie das PowerShell-Cmdlet **`Get-WindowsFeature`** von Ihrem Verwaltungs-PC aus (Sie können das Cmdlet auch direkt im Cluster oder auf dem Server ausführen, indem Sie den Parameter „-ComputerName“ weglassen):

```PowerShell
Get-WindowsFeature -Name Failover*, RSAT-Clustering* -ComputerName Server1
```

Stellen Sie sicher, dass der Installationsstatus als „Installiert“ angegeben ist und sowohl Failoverclustering als auch Failoverclustermodul für Windows PowerShell mit einem X markiert sind:

```
Display Name                                            Name                       Install State
------------                                            ----                       -------------
[X] Failover Clustering                                 Failover-Clustering            Installed
        [X] Failover Clustering Tools                   RSAT-Clustering                Installed
            [X] Failover Cluster Module for Windows ... RSAT-Clustering-Powe...        Installed
            [ ] Failover Cluster Automation Server      RSAT-Clustering-Auto...        Available
            [ ] Failover Cluster Command Interface      RSAT-Clustering-CmdI...        Available
```

Wenn das Failoverclustering nicht installiert ist, installieren Sie dieses Feature mit dem Cmdlet **`Install-WindowsFeature`** auf jedem Server im Cluster. Verwenden Sie dabei die Parameter „-IncludeAllSubFeature“ und „-IncludeManagementTools“:

```PowerShell
Install-WindowsFeature –Name Failover-Clustering -IncludeAllSubFeature –IncludeManagementTools -ComputerName Server1
```

Dieser Befehl installiert auch das Failoverclustermodul für PowerShell, das die PowerShell-Cmdlets für die Verwaltung von Failoverclustern enthält. Installiert wird ebenfalls das Modul für clusterfähiges Aktualisieren für PowerShell, mit dem Softwareupdates auf Failoverclustern installiert werden.

Wenn das Failoverclustering bereits installiert ist, das Failoverclustermodul für Windows PowerShell aber nicht, installieren Sie das Modul einfach mit dem Cmdlet **Install-WindowsFeature** auf jedem Server im Cluster:

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>Auswählen eines Aktualisierungsmodus

Das Feature des clusterfähigen Aktualisierens kann die gesamte Clusteraktualisierung in zwei Modi koordinieren:  
  
-   **Selbstaktualisierungsmodus** : In diesem Modus ist die Clusterrolle für das clusterfähige Aktualisieren als Workload auf dem zu aktualisierenden Failovercluster konfiguriert, und ein entsprechender Aktualisierungszeitplan ist definiert. Der Cluster aktualisiert sich zu den geplanten Zeiten mithilfe eines standardmäßigen oder benutzerdefinierten Ausführungsprofils selbst. Während der Updateausführung startet der Updatekoordinatorprozess für das clusterfähige Aktualisieren auf dem Knoten, der aktuell der Besitzer der Clusterrolle für das clusterfähige Aktualisieren ist, und aktualisiert nacheinander alle Clusterknoten. Um den aktuellen Clusterknoten zu aktualisieren, führt die Clusterrolle für das clusterfähige Aktualisieren ein Failover auf einen anderen Clusterknoten aus, und ein neuer Updatekoordinatorprozess auf diesem Knoten übernimmt die Steuerung für die Updateausführung. Im Selbstaktualisierungsmodus kann das Feature des clusterfähigen Aktualisierens den Failovercluster in einem vollständig automatisierten, End-to-End-Updateprozess aktualisieren. In diesem Modus kann ein Administrator Updates auch bei Bedarf auslösen oder einfach remote anstoßen.
  
-   **Remoteaktualisierungsmodus** : Für diesen Modus ist ein Remoteverwaltungscomputer (in der Regel ein Windows 10-PC), der über eine Netzwerkverbindung mit dem Failovercluster verfügt, aber kein Mitglied des Failoverclusters ist, mit den Failoverclustertools konfiguriert. Auf dem Remoteverwaltungscomputer, der auch als Updatekoordinator bezeichnet wird, verwendet der Administrator ein standardmäßiges oder benutzerdefiniertes Profil für die Updateausführung, um bei Bedarf eine Updateausführung auszulösen. Der Remoteaktualisierungsmodus ist nützlich für die Überwachung des Echtzeitfortschritts während der Updateausführung sowie für Cluster in Server Core-Installationen.  

   > [!NOTE]
   > Ab dem Windows 10-Update vom Oktober 2018 ist RSAT als Gruppe von „Features on Demand“ direkt in Windows 10 eingeschlossen. Wechseln Sie einfach zu **Einstellungen > Apps > Apps und Features > Optionale Features > Feature hinzufügen > RSAT: Failoverclustertools** , und wählen Sie **Installieren** aus. Um den Installationsfortschritt anzuzeigen, klicken Sie auf die Schaltfläche „Zurück“, und überprüfen Sie den Status auf der Seite „Optionale Features verwalten“. Das installierte Feature wird über Upgrades von Windows 10-Versionen hinweg beibehalten. Um die Remoteserver-Verwaltungstools für Windows 10 vor dem Update vom Oktober 2018 zu installieren [laden Sie ein RSAT-Paket herunter](https://www.microsoft.com/download/details.aspx?id=45520).

### <a name="add-cau-cluster-role-to-the-cluster"></a>Hinzufügen einer Rolle für das clusterfähige Aktualisieren zum Cluster

Die Clusterrolle für das clusterfähige Aktualisieren ist für den Selbstaktualisierungsmodus erforderlich. Wenn Sie Windows Admin Center zum Ausführen der Updates verwenden, wird die Clusterrolle automatisch hinzugefügt.

Das Cmdlet **`Get-CauClusterRole`** zeigt die Konfigurationseigenschaften der Clusterrolle für das clusterfähige Aktualisieren im angegebenen Cluster an.

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

Wenn die Rolle noch nicht im Cluster konfiguriert, wird die folgende Fehlermeldung angezeigt:

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

Um die Clusterrolle für das clusterfähige Aktualisieren für den Selbstaktualisierungsmodus über PowerShell hinzuzufügen, verwenden Sie das Cmdlet **`Add-CauClusterRole`** , und geben Sie geeignete [Parameter](/powershell/module/clusterawareupdating/add-cauclusterrole#parameters) an, wie im folgenden Beispiel gezeigt:

```PowerShell
Add-CauClusterRole -ClusterName Cluster1 -MaxFailedNodes 0 -RequireAllNodesOnline -EnableFirewallRules -VirtualComputerObjectName Cluster1-CAU -Force -CauPluginName Microsoft.WindowsUpdatePlugin -MaxRetriesPerNode 3 -CauPluginArguments @{ 'IncludeRecommendedUpdates' = 'False' } -StartDate "3/2/2020 3:00:00 AM" -DaysOfWeek 4 -WeeksOfMonth @(3) -verbose
```

   > [!NOTE]
   > Der obige Befehl muss auf einem Verwaltungs-PC oder Domänencontroller ausgeführt werden.

### <a name="enable-firewall-rules-to-allow-remote-restarts"></a>Aktivieren von Firewallregeln zum Zulassen von Remoteneustarts

Sie müssen zulassen, dass Server während des Updateprozesses remote neu gestartet werden. Wenn Sie die Updates über Windows Admin Center ausführen, werden die Regeln der Windows Firewall auf jedem Server automatisch aktualisiert, um Remoteneustarts zuzulassen. Wenn Sie PowerShell verwenden, aktivieren Sie entweder die Firewallregelgruppe für Remoteshutdowns in der Windows Firewall, oder übergeben Sie den Parameter „-EnableFirewallRules“ an das Cmdlet, wie im obigen Beispiel gezeigt.

## <a name="check-for-updates-with-powershell"></a>Überprüfen auf Updates mit PowerShell

Sie können das Cmdlet **`Invoke-CAUScan`** verwenden, um Server auf anwendbare Updates zu überprüfen und eine Liste der anfänglichen Updates abzurufen, die auf jeden Server in einem bestimmten Cluster angewendet werden sollen:

```PowerShell
Invoke-CauScan -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -Verbose
```

Das Generieren der Liste kann einige Minuten dauern. Die Vorschauliste enthält nur die anfänglichen Updates. Sie enthält keine Updates, die möglicherweise angewendet werden müssen, nachdem die anfänglichen Updates installiert wurden.

## <a name="install-updates-with-powershell"></a>Installieren von Updates mit PowerShell

Um Server auf anwendbare Updates zu überprüfen und eine vollständige Aktualisierung im angegebenen Cluster auszuführen, verwenden Sie das Cmdlet **`Invoke-CAURun`** :

```PowerShell
Invoke-CauRun -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -MaxFailedNodes 1 -MaxRetriesPerNode 3 -RequireAllNodesOnline -EnableFirewallRules -Force
```

Dieser Befehl führt eine Überprüfung und eine vollständige Updateausführung im Cluster namens „Cluster1“ aus. Dieses Cmdlet verwendet das Microsoft.WindowsUpdatePlugin. Alle Clusterknoten müssen online sein, bevor dieses Cmdlet ausgeführt wird. Darüber hinaus lässt dieses Cmdlet maximal drei Wiederholungen pro Knoten zu, bevor es den Knoten als fehlerhaft markiert. Außerdem darf maximal ein Knoten fehlerhaft sein, sonst wird die gesamte Updateausführung als fehlerhaft markiert. Das Cmdlet aktiviert zudem Firewallregeln, um einen Remoteneustart der Server zu ermöglichen. Da der Befehl den Parameter „Force“ angibt, wird das Cmdlet ausgeführt, ohne Eingabeaufforderungen zur Bestätigung anzuzeigen.

Der Updateausführungsprozess umfasst Folgendes: 
- Überprüfen auf und Herunterladen von anwendbaren Updates auf jedem Server im Cluster
- Verschieben aktuell ausgeführter Clusterrollen von jedem Server
- Installieren der Updates auf jedem Server
- Neustarten des Servers, sofern dies aufgrund der installierten Updates erforderlich ist
- Verschieben der Clusterrollen zurück zum ursprünglichen Server

Der Updateausführungsprozess stellt außerdem die Aufrechterhaltung des Quorums sicher, überprüft, ob weitere Updates vorliegen, die erst nach den anfänglichen Updates installiert werden können, und speichert einen Bericht der ausgeführten Aktionen.

## <a name="check-on-the-status-of-an-updating-run"></a>Überprüfen des Status einer Updateausführung

Mit dem Cmdlet **`Get-CauRun`** kann ein Administrator Übersichtsinformationen über eine Updateausführung abrufen:

```PowerShell
Get-CauRun -ClusterName Cluster1
```

Hier finden Sie eine Beispielausgabe:

```
RunId                   : 834dd11e-584b-41f2-8d22-4c9c0471dbad 
RunStartTime            : 10/13/2019 1:35:39 PM 
CurrentOrchestrator     : NODE1 
NodeStatusNotifications : { 
Node      : NODE1 
Status    : Waiting 
Timestamp : 10/13/2019 1:35:49 PM 
} 
NodeResults             : { 
Node                     : NODE2 
Status                   : Succeeded 
ErrorRecordData          : 
NumberOfSucceededUpdates : 0 
NumberOfFailedUpdates    : 0 
InstallResults           : Microsoft.ClusterAwareUpdating.UpdateInstallResult[] 
}
```

## <a name="perform-a-fast-offline-update-of-all-servers-in-a-cluster"></a>Ausführen eines schnellen Offlineupdates aller Server in einem Cluster

Diese Methode gestattet es Ihnen, alle Server in einem Cluster gleichzeitig offline zu schalten und alle gleichzeitig zu aktualisieren. Dies spart Zeit während des Aktualisierungsvorgangs, doch der Nachteil besteht in Ausfallzeiten bei den gehosteten Ressourcen.

Wenn ein kritisches Sicherheitsupdate vorliegt, das Sie schnell anwenden müssen, oder wenn Sie sicherstellen müssen, dass Updates innerhalb Ihres Wartungsfensters abgeschlossen werden, ist diese Methode möglicherweise für Sie geeignet. Durch diesen Prozess wird der Azure Stack HCI-Cluster heruntergefahren, die Server werden aktualisiert, und alle werden wieder gestartet.

1. Planen Sie Ihr Wartungsfenster.
2. Schalten Sie die virtuellen Datenträger offline.
3. Beenden Sie den Cluster, um den Speicherpool offline zu schalten. Führen Sie das Cmdlet **Stop-Cluster** aus, oder verwenden Sie das Windows Admin Center, um den Cluster zu beenden.
4. Legen Sie den Clusterdienst in der Services.msc auf jedem Server auf **Deaktiviert** fest. Dadurch wird verhindert, dass der Clusterdienst während der Aktualisierung gestartet wird.
5. Wenden Sie das kumulative Update für Windows Server und alle erforderlichen Wartungsstapelaktualisierungen auf alle Server an. Sie können alle Server gleichzeitig aktualisieren. Es ist nicht erforderlich, zu warten, da der Cluster nicht verfügbar ist.
6. Starten Sie die Server neu, und stellen Sie sicher, dass alles gut aussieht.
7. Legen Sie den Clusterdienst auf jedem Server wiecer auf **Automatisch** fest.
8. Starten Sie den Cluster. Führen Sie das Cmdlet **Start-Cluster** aus, oder verwenden Sie das Windows Admin Center.

   Geben Sie dem Vorgang fünf Minuten.  Stellen Sie sicher, dass der Speicherpool fehlerfrei ist.

9. Schalten Sie die virtuellen Datenträger wieder online.
10. Überwachen Sie den Status der virtuellen Datenträger, indem Sie die Cmdlets **Get-Volume** und **Get-VirtualDisk** ausführen.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Clusterfähiges Aktualisieren (CAU)](/windows-server/failover-clustering/cluster-aware-updating)
- [Aktualisieren der Laufwerksfirmware in Direkte Speicherplätze](/windows-server/storage/update-firmware)
- [Überprüfen eines Azure Stack HCI-Clusters](../deploy/validate.md)