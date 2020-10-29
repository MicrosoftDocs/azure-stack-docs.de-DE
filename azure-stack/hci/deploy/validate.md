---
title: Überprüfen eines Azure Stack HCI-Clusters
description: Grundlegendes zur Wichtigkeit der Validierung von Clustern und dem Zeitpunkt, wann diese für einen vorhandenen Azure Stack HCI-Cluster ausgeführt werden sollte. Erkunden Sie Szenarien zur Problembehandlung eines aktualisierten Serverclusters.
author: JohnCobb1
ms.author: v-johcob
ms.topic: article
ms.date: 10/16/2020
ms.openlocfilehash: fe49df76ccb2a90849587acd5d4df7a41e329efb
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157698"
---
# <a name="validate-an-azure-stack-hci-cluster"></a>Überprüfen eines Azure Stack HCI-Clusters

>Gilt für: Azure Stack HCI, Version v20H2; Windows Server 2019

In diesem Artikel wird erläutert, warum die Validierung von Clustern so wichtig ist und wann diese für einen vorhandenen Azure Stack HCI-Cluster ausgeführt werden sollte. Die Clustervalidierung wird für die folgenden primären Szenarien empfohlen:
- Führen Sie nach der Bereitstellung eines Serverclusters das Validate-DCB-Tool aus, um das Netzwerk zu testen.
- Führen Sie je nach Szenario nach dem Aktualisieren eines Serverclusters beide Validierungen aus, um mögliche Probleme im Cluster zu beheben.
- Überprüfen Sie nach dem Einrichten der Replikation mit Speicherreplikaten, ob die Replikation normal fortgesetzt wird. Überprüfen Sie dazu bestimmte Ereignisse, und führen Sie einige Befehle aus.
- Führen Sie nach der Erstellung eines Serverclusters das Validate-DCB-Tool aus, bevor Sie ihn in der Produktionsumgebung platzieren.

    Weitere Informationen zum Bereitstellen eines Azure Stack HCI-Clusters finden Sie in der [Bereitstellungsübersicht](deployment-overview.md).

## <a name="what-is-cluster-validation"></a>Was ist die Clustervalidierung?
Die Clustervalidierung dient dazu, Hardware- oder Konfigurationsprobleme zu erfassen, bevor ein Cluster in der Produktion eingesetzt wird. Mit der Clustervalidierung können Sie sicherstellen, dass die Azure Stack HCI-Lösung, die Sie bereitstellen möchten, wirklich zuverlässig ist. Sie können die Clustervalidierung auch als Diagnosetool für konfigurierte Failovercluster verwenden.

## <a name="specific-validation-scenarios"></a>Spezifische Validierungsszenarien
In diesem Abschnitt werden Szenarien beschrieben, in denen eine Validierung ebenfalls erforderlich oder nützlich ist.

- **Validierung vor der Konfiguration des Clusters:**
  - **Eine Gruppe von Servern, die als Failovercluster eingesetzt werden sollen** : Dies ist das einfachste Validierungsszenario. Die Hardwarekomponenten (Systeme, Netzwerke und Speicher) sind verbunden, aber die Systeme funktionieren noch nicht als Cluster. Die Ausführung von Tests in dieser Situation wirkt sich nicht auf die Verfügbarkeit aus.
 
  - **Server-VMs** : Bei virtualisierten Servern in einem Cluster führen Sie die Clustervalidierung auf dieselbe Weise aus wie für einen neuen Cluster. Dieses Feature sollte immer ausgeführt werden, unabhängig davon, welche der folgenden Konfigurationen Sie verwenden:
    - Ein Hostcluster, bei dem das Failover zwischen zwei physischen Computern erfolgt.
    - Ein Gastcluster, bei dem das Failover zwischen Gastbetriebssystemen auf demselben physischen Computer erfolgt.
 
- **Validierung nach der Konfiguration und während der Verwendung des Clusters:**

  - **Vor dem Hinzufügen eines Servers zum Cluster** : Wenn Sie einen Server zu einem Cluster hinzufügen, wird dringend empfohlen, den Cluster zu validieren. Geben Sie beim Ausführen der Clustervalidierung sowohl die vorhandenen Clustermitglieder als auch den neuen Server an.
  
  - **Beim Hinzufügen von Laufwerken** : Wenn Sie dem Cluster zusätzliche Laufwerke hinzufügen – dieser Vorgang unterscheidet sich vom Ersetzen fehlerhafter Laufwerke oder Erstellen von virtuellen Datenträgern oder Volumes, die auf den vorhandenen Laufwerken basieren –, führen Sie die Clustervalidierung aus, um zu bestätigen, dass der neue Speicher ordnungsgemäß funktioniert.

  - **Beim Durchführen von Änderungen, die sich auf Firmware oder Treiber auswirken** : Wenn Sie ein Upgrade des Clusters ausführen oder Änderungen vornehmen, die sich auf die Firmware oder die Treiber auswirken, müssen Sie die Clustervalidierung ausführen, um zu bestätigen, dass die neue Kombination aus Hardware, Firmware, Treibern und Software die Failoverclusterfunktionalität unterstützt.

  - **Nach dem Wiederherstellen eines Systems aus einer Sicherung** : Nachdem Sie ein System aus einer Sicherung wiederhergestellt haben, führen Sie die Clustervalidierung aus, um zu bestätigen, dass das System als Teil des Clusters ordnungsgemäß funktioniert.

## <a name="validate-networking"></a>Validieren der Netzwerkfunktionen
Das Validate-DCB-Tool von Microsoft ist für die Validierung der DCB-Konfiguration (Data Center Bridging) im Cluster konzipiert. Zu diesem Zweck verwendet das Tool eine erwartete Konfiguration als Eingabe und testet dann jeden Server im Cluster. In diesem Abschnitt wird beschrieben, wie Sie das Validate-DCB-Tool installieren und ausführen, die Ergebnisse überprüfen und vom Tool identifizierte Netzwerkfehler beheben.

Im Netzwerk erfordert der Remotezugriff auf den direkten Speicher (Remote Direct Memory Access, RDMA) über konvergentes Ethernet (RDMA over Converged Ethernet, RoCE) DCB-Technologien, um das Netzwerkfabric verlustfrei zu machen. Bei iWARP ist DCB optional. Die Konfiguration von DCB kann jedoch sehr komplex sein und erfordert eine exakte Konfiguration in folgenden Komponenten:
- Jeder Server im Cluster
- Jeder Netzwerkport, den der RDMA-Datenverkehr im Fabric durchläuft

### <a name="prerequisites"></a>Voraussetzungen
- Informationen zum Netzwerksetup in dem Servercluster, den Sie validieren möchten:
    - Name des Hosts oder Serverclusters
    - Name des virtuellen Switchs
    - Namen der Netzwerkadapter
    - Einstellungen für Prioritätsflusssteuerung (Priority Flow Control, PFC) und erweiterte Übertragungsauswahl (Enhanced Transmission Selection, ETS)
- Eine Internetverbindung zum Herunterladen des Toolmoduls von Microsoft in Windows PowerShell.

### <a name="install-and-run-the-validate-dcb-tool"></a>Installieren und Ausführen des Validate-DCB-Tools
So installieren Sie das Validate-DCB-Tool und führen es aus:
1. Öffnen Sie auf dem Verwaltungscomputer eine Windows PowerShell-Sitzung als Administrator, und geben Sie dann folgenden Befehl ein, um das Tool zu installieren.

    ```powershell
    Install-Module Validate-DCB
    ```

1. Akzeptieren Sie die Anforderung zur Verwendung des NuGet-Anbieters, und greifen Sie auf das Repository zu, um das Tool zu installieren.
1. Nachdem PowerShell zum Herunterladen des Tools eine Verbindung mit dem Microsoft-Netzwerk hergestellt hat, geben Sie `Validate-DCB` ein und drücken die **EINGABETASTE** , um den Tool-Assistenten zu starten.

    > [!NOTE]
    > Wenn Sie das Validate-DCB-Toolskript nicht ausführen können, müssen Sie möglicherweise die Ausführungsrichtlinien von PowerShell anpassen. Verwenden Sie das Get-ExecutionPolicy-Cmdlet, um die aktuellen Richtlinieneinstellungen für die Skriptausführung anzuzeigen. Weitere Informationen zum Einrichten von Ausführungsrichtlinien in PowerShell finden Sie unter [Informationen zu Ausführungsrichtlinien](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7).

1. Klicken Sie auf der Willkommensseite des Assistenten für die Validate-DCB-Konfiguration auf **Next** (Weiter).
1. Geben Sie auf der Seite „Cluster and Nodes“ (Cluster und Knoten) den Namen des Serverclusters ein, den Sie validieren möchten, wählen Sie **Resolve** (Auflösen) aus, um ihn auf der Seite aufzulisten, und klicken Sie dann auf **Next** (Weiter).

    :::image type="content" source="../media/validate/clusters-and-nodes.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

1. Gehen Sie auf der Seite „Adapters“ folgendermaßen vor:
   1. Aktivieren Sie das Kontrollkästchen **vSwitch attached** (Angefügter virtueller Switch), und geben Sie den Namen des virtuellen Switchs ein.
   1. Geben Sie unter **Adapter Name** (Name des Adapters) den Namen jedes physischen Netzwerkadapters, unter **Host vNIC Name** (Name des virtuellen Hostnetzwerkadapters) den Namen jedes virtuellen Netzwerkadapters und unter **VLAN** die für jeden Adapter verwendete VLAN-ID ein.
   1. Erweitern Sie das Dropdown-Listenfeld **RDMA Type** (RDMA-Typ), und wählen Sie das entsprechende Protokoll aus: **RoCE** oder **iWARP** . Legen Sie auch die **Jumbo Frames** auf den für Ihr Netzwerk geeigneten Wert fest, und klicken Sie dann auf **Next** (Weiter).

    :::image type="content" source="../media/validate/adapters.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration" lightbox="../media/validate/adapters.png":::

    > [!NOTE]
    > - Weitere Informationen dazu, wie SR-IOV die Netzwerkleistung verbessert, finden Sie unter [Übersicht über die Single Root I/O Virtualization (SR-IOV)](/windows-hardware/drivers/network/overview-of-single-root-i-o-virtualization--sr-iov-).

1. Ändern Sie auf der Seite „Data Center Bridging“ die Werte so, dass sie den Einstellungen Ihrer Organisation für **Priority** (Priorität), **Policy Name** (Richtlinienname) und **Bandwidth Reservation** (Bandbreitenreservierung) entsprechen, und klicken Sie dann auf **Next** (Weiter).

    :::image type="content" source="../media/validate/data-center-bridging.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration" lightbox="../media/validate/data-center-bridging.png":::

    > [!NOTE]
    > Die Auswahl von RDMA über RoCE auf der vorherigen Seite des Assistenten erfordert DCB für die Netzwerkzuverlässigkeit in allen Netzwerkadaptern und Switchports.

1. Speichern Sie die Konfigurationsdatei auf der Seite „Save and Deploy“ (Speichern und bereitstellen) im Feld **Configuration File Path** (Pfad zur Konfigurationsdatei) mit der Erweiterung „.ps1“ in einem Speicherort, von dem aus Sie die Datei bei Bedarf später erneut verwenden können. Klicken Sie dann auf **Export** , um das Validate-DCB-Tool zu starten.

   - Sie können Ihre Konfigurationsdatei optional bereitstellen, indem Sie den Abschnitt **Deploy Configuration to Nodes** (Konfiguration auf Knoten bereitstellen) auf der Seite ausfüllen. Hier können Sie ein Azure Automation-Konto angeben, um die Konfiguration bereitzustellen und dann zu validieren. Informationen zu den ersten Schritten mit Azure Automation finden Sie unter [Erstellen eines Azure Automation-Kontos](/azure/automation/automation-quickstart-create-account).

    :::image type="content" source="../media/validate/save-and-deploy.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

### <a name="review-results-and-fix-errors"></a>Überprüfen der Ergebnisse und Beheben von Fehlern
Das Validate-DCB-Tools zeigt die Ergebnisse in zwei Einheiten an:
1. [Global Unit]-Ergebnisse listen Voraussetzungen und Anforderungen für die Ausführung von modalen Tests auf.
1. [Modal Unit]-Ergebnisse bieten Feedback zu jeder Clusterhostkonfiguration und schlagen Best Practices vor.

Dieses Beispiel zeigt mit dem FailedCount-Wert 0 erfolgreiche Überprüfungsergebnisse eines einzelnen Servers für alle Voraussetzungen und Modal Unit-Tests an.

:::image type="content" source="../media/validate/global-unit-and-modal-unit-results.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

Die folgenden Schritte zeigen, wie Sie einen Jumbo-Paketfehler in vNIC SMB02 identifizieren und beheben:
1. Die Überprüfungsergebnisse des Validate-DCB-Tools zeigen den FailedCount-Wert 1.

    :::image type="content" source="../media/validate/failed-count-error-1.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

1. Wenn Sie in den Ergebnissen nach oben scrollen, sehen Sie einen Fehler in roter Schrift, der darauf hinweist, dass das Jumbo-Paket für den vNIC SMB02 auf dem Host S046036 die Standardgröße 1514 aufweist, aber auf 9014 festgelegt sein sollte.

    :::image type="content" source="../media/validate/jumbo-packet-setting-error.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

1. Eine Überprüfung der Werte auf der Registerkarte **Advanced** (Erweitert) der Eigenschaften des vNIC SMB02 auf dem Host S046036 zeigt, dass das Jumbo-Paket auf den Standardwert **Disabled** (Deaktiviert) festgelegt ist.

    :::image type="content" source="../media/validate/hyper-v-advanced-properties-jumbo-packet-setting.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

1. Um diesen Fehler zu beheben, müssen Sie das Jumbo-Paketfeature aktivieren und die Größe in 9014 Bytes ändern. Eine erneute Ausführung der Überprüfung auf dem Host S046036 bestätigt diese Änderung durch Rückgabe des FailedCount-Werts 0.

    :::image type="content" source="../media/validate/jumbo-packet-error-fix-confirmation.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

Weitere Informationen zum Beheben von Fehlern, die vom Validate-DCB-Tool identifiziert wurden, finden Sie im folgenden Video.

> [!VIDEO https://www.youtube.com/embed/cC1uACvhPBs]

## <a name="validate-the-cluster"></a>Überprüfen des Clusters
Führen Sie die folgenden Schritte aus, um die Server in einem vorhandenen Cluster in Windows Admin Center zu validieren.

1. Wählen Sie in Windows Admin Center unter **Alle Verbindungen** den Azure Stack HCI-Cluster aus, den Sie validieren möchten, und klicken Sie dann auf **Verbinden** .

    Das **Cluster-Manager-Dashboard** zeigt Übersichtsinformationen zum Cluster an.

1. Wählen Sie auf dem **Cluster-Manager-Dashboard** unter **Tools** die Option **Server** aus.
1. Wählen Sie auf der Seite **Bestand** die Server im Cluster aus, erweitern Sie dann das Untermenü **Mehr** , und klicken Sie auf **Cluster überprüfen** .
1. Klicken Sie im Popupfenster **Cluster überprüfen** auf **Ja** .

    :::image type="content" source="../media/validate/validate-cluster-pop-up.png" alt-text="Die Seite „Clusters and Nodes“ (Cluster und Knoten) im Assistenten für die Validate-DCB-Konfiguration":::

1. Klicken Sie im Popupfenster **Credential Security Service Provider (CredSSP)** auf **Ja** .
1. Geben Sie Ihre Anmeldeinformationen ein, um **CredSSP** zu aktivieren, und klicken Sie dann auf **Weiter** .<br> Die Clustervalidierung wird im Hintergrund ausgeführt, und Sie erhalten eine Benachrichtigung, wenn sie abgeschlossen ist. Danach können Sie den Validierungsbericht überprüfen, wie im nächsten Abschnitt beschrieben.

> [!NOTE]
> Nachdem Ihre Clusterserver validiert wurden, müssen Sie CredSSP aus Sicherheitsgründen deaktivieren.

### <a name="disable-credssp"></a>Deaktivieren von CredSSP
Nachdem Ihr Servercluster erfolgreich validiert wurde, müssen Sie das CredSSP-Protokoll (Credential Security Support Provider) aus Sicherheitsgründen auf jedem Server deaktivieren. Weitere Informationen finden Sie unter [CVE-2018-0886](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-0886).

1. Wählen Sie in Windows Admin Center unter **Alle Verbindungen** den ersten Server im Cluster aus, und klicken Sie auf **Verbinden** .
1. Wählen Sie auf der Seite **Übersicht** die Option **CredSSP deaktivieren** aus, und klicken Sie dann im Popupfenster **CredSSP deaktivieren** auf **Ja** .

    Schritt 2 entfernt das rote Banner **CredSSP AKTIVIERT** oben auf der Seite **Übersicht** des Servers und deaktiviert CredSSP auf den anderen Servern.

### <a name="view-validation-reports"></a>Anzeigen von Validierungsberichten
Jetzt können Sie den Validierungsbericht für Ihren Cluster anzeigen.

Es gibt verschiedene Möglichkeiten, auf die Validierungsberichte zuzugreifen:
- Erweitern Sie auf der Seite **Bestand** das Untermenü **Mehr** , und wählen Sie **Validierungsberichte anzeigen** aus.


- Wählen Sie in **Windows Admin Center** oben rechts das Glockensymbol **Benachrichtigungen** aus, um den Bereich **Benachrichtigungen** anzuzeigen.
Wählen Sie den Hinweis **Erfolgreich validierte Cluster** aus, und klicken Sie dann auf **Zum Validierungsbericht für Failovercluster wechseln** .

> [!NOTE]
> Der Validierungsprozess für den Servercluster kann einige Zeit in Anspruch nehmen. Wechseln Sie während der Ausführung des Prozesses nicht zu einem anderen Tool in Windows Admin Center. Im Bereich **Benachrichtigungen** zeigt eine Statusleiste unter dem Hinweis **Cluster validieren** an, wenn der Prozess beendet ist.

## <a name="validate-the-cluster-using-powershell"></a>Validieren des Clusters mithilfe von PowerShell
Sie können auch Windows PowerShell verwenden, um Validierungstests in Ihrem Servercluster auszuführen und die Ergebnisse anzuzeigen. Sie können Tests sowohl vor als auch nach der Einrichtung eines Clusters ausführen.

Um einen Validierungstest in einem Servercluster auszuführen, verwenden Sie die PowerShell-Cmdlets **Get-Cluster** und **Test-Cluster** für <server clustername> auf Ihrem Verwaltungscomputer, oder führen Sie nur das Cmdlet **Test-Cluster** direkt im Cluster aus:

```PowerShell
$Cluster = Get-Cluster -Name 'server-cluster1'
Test-Cluster -InputObject $Cluster -Verbose
```

Weitere Beispiele und Informationen zur Verwendung finden Sie in der Referenzdokumentation zu [Test-Cluster](/powershell/module/failoverclusters/test-cluster?view=win10-ps).

## <a name="validate-replication-for-storage-replica"></a>Validieren der Replikation für Speicherreplikate
Wenn Sie Speicherreplikate verwenden, um Volumes in einem Stretchingcluster oder zwischen zwei Clustern zu replizieren, stehen verschiedene Ereignisse und Cmdlets zur Verfügung, mit denen Sie den Status der Replikation abrufen können. 

Im folgenden Szenario wurde das Speicherreplikat konfiguriert, indem Replikationsgruppen (RGs) für zwei Standorte erstellt wurden. Danach wurden die Daten- und Protokollvolumes für die Quellserverknoten in Site1 (Server1, Server2) und die replizierten Zielserverknoten in Site2 (Server3, Server4) angegeben.

Um den Replikationsfortschritt für Server1 in Site1 zu ermitteln, führen Sie den Get-WinEvent-Befehl aus und untersuchen die Ereignisse 5015, 5002, 5004, 1237, 5001 und 2200:

```powershell
Get-WinEvent -ComputerName Server1 -ProviderName Microsoft-Windows-StorageReplica -max 20
```

Für Server3 in Site2 führen Sie den folgenden `Get-WinEvent`-Befehl aus, um die Speicherreplikatereignisse für die Erstellung der Partnerschaft anzuzeigen. Dieses Ereignis gibt die Anzahl von kopierten Bytes und die dafür benötigte Zeit an. Beispiel:

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | Where-Object {$_.ID -eq "1215"} | FL
```

Für Server3 in Site2 führen Sie den `Get-WinEvent`-Befehl aus und untersuchen die Ereignisse 5009, 1237, 5001, 5015, 5005 und 2200, um sich über den Verarbeitungsfortschritt zu informieren. In dieser Sequenz sollten keine Warnungen oder Fehler vorliegen. Sie werden viele 1237-Ereignisse bemerken: Diese geben den Fortschritt an.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Alternativ dazu gibt die Zielservergruppe für das Replikat die Anzahl von Bytes an, die noch kopiert werden müssen. Diese Servergruppe kann in PowerShell mit `Get-SRGroup` abgefragt werden. Beispiel:

```powershell
(Get-SRGroup).Replicas | Select-Object numofbytesremaining
```

Führen Sie für den Knoten Server3 in Site2 den folgenden Befehl aus, und untersuchen Sie die Ereignisse 5009, 1237, 5001, 5015, 5005 und 2200, um sich über den Replikationsfortschritt zu informieren. Es sollten keine Warnungen oder Fehler vorliegen. Sie werden jedoch viele 1237-Ereignisse bemerken: Diese geben einfach den Fortschritt an.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Ein Fortschrittsskript, das nicht beendet wird:

```powershell
while($true) {
$v = (Get-SRGroup -Name "Replication2").replicas | Select-Object numofbytesremaining
[System.Console]::Write("Number of bytes remaining: {0}`r", $v.numofbytesremaining)
Start-Sleep -s 5
}
```

Um den Replikationsstatus innerhalb des Stretchingclusters abzurufen, verwenden Sie `Get-SRGroup` und `Get-SRPartnership`:

```powershell
Get-SRGroup -Cluster ClusterS1
```

```powershell
Get-SRPartnership -Cluster ClusterS1
```

```powershell
(Get-SRGroup).replicas -Cluster ClusterS1
```

Sobald die erfolgreiche Datenreplikation zwischen den Standorten bestätigt wurde, können Sie Ihre VMs und weitere Workloads erstellen.

## <a name="see-also"></a>Weitere Informationen
- Leistungstests für synthetische Workloads in einem neu erstellten Speicherplatz mithilfe von DiskSpd.exe. Weitere Informationen finden Sie unter [Testen der Leistung von Speicherplätzen mithilfe synthetischer Workloads in Windows Server](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn894707(v=ws.11)).
- Die Windows Server-Bewertung ist ein Premier-Dienst für Kunden, die ihre Installationen von Windows Server 2019 durch Microsoft überprüfen lassen möchten. Wenden Sie sich an den Microsoft Premier Support, um weitere Informationen zu erhalten. Mehr dazu erfahren Sie unter [Erste Schritte mit der Windows Server On-Demand-Bewertung (Server, Sicherheit, Hyper-V, Failover-Cluster, IIS)](/services-hub/health/getting-started-windows-server).
