---
title: Verwalten von SMB Multichannel
description: In diesem Thema erfahren Sie, wie Sie SMB Multichannel verwenden, um Durchsatz und Netzwerkfehlertoleranz zu verbessern.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: b7dc0c8050bc9d813115d2c3ebd7716b87bd89fa
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585966"
---
# <a name="manage-smb-multichannel"></a>Verwalten von SMB Multichannel

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows 10

SMB Multichannel gehört zum SMB 3.0-Protokoll (Server Message Block), das die Netzwerkleistung sowie die Verfügbarkeit von Dateiservern erhöht.

Mit SMB Multichannel können Dateiserver mehrere Netzwerkverbindungen gleichzeitig nutzen. Das Feature ermöglicht die Aggregation von Netzwerkbandbreite und Netzwerkfehlertoleranz, wenn zwischen dem SMB 3.0-Client und dem SMB 3.0-Server mehrere Pfade verfügbar sind. So können Serveranwendungen die gesamte verfügbare Bandbreite vollständig nutzen und sind widerstandsfähiger gegen Netzwerkausfälle.

SMB Multichannel bietet die folgenden Funktionen:

- **Erhöhter Durchsatz**: Durch Verwendung von mehreren Verbindungen für Hochgeschwindigkeits-Netzwerkadapter oder mehrere Netzwerkadapter kann der Dateiserver gleichzeitig zusätzliche Daten übertragen.

- **Netzwerkfehlertoleranz**: Wenn Clients mehrere Netzwerkverbindungen gleichzeitig nutzen, können sie auch bei Ausfall einer Netzwerkverbindung ohne Unterbrechung weiter ausgeführt werden.

- **Automatische Konfiguration**: SMB Multichannel entdeckt automatisch mehrere verfügbare Netzwerkpfade und fügt bei Bedarf dynamisch Verbindungen hinzu.

## <a name="requirements-for-smb-multichannel"></a>Anforderungen für SMB Multichannel

SMB Multichannel ist standardmäßig aktiviert, und Sie müssen keine zusätzlichen Rollen, Rollendienste oder Features installieren. Der SMB-Client erkennt und verwendet automatisch mehrere Netzwerkverbindungen, wenn diese Konfiguration identifiziert wird. Für SMB Multichannel gelten allerdings folgende Voraussetzungen:

- Mindestens zwei Computer, auf denen Azure Stack HCI, Windows Server oder Windows 10 ausgeführt wird

- Mindestens eine der folgenden Konfigurationen:

    - Mehrere Netzwerkadapter

    - Mindestens ein Netzwerkadapter mit Unterstützung für RSS (Receive Side Scaling)

    - Mehrere Netzwerkadapter, die per Teaming zusammengeschlossen sind (siehe [NIC-Teaming](#nic-teaming))

    - Mindestens ein Netzwerkadapter mit Unterstützung für RDMA (Remote Direct Memory Access)

## <a name="configure-smb-multichannel"></a>Konfigurieren von SMB Multichannel

In diesem Abschnitt werden einige Konfigurationsoptionen für die Bereitstellung von SMB Multichannel unter Verwendung eines Arrays aus Netzwerkadaptern beschrieben. Hierbei handelt es sich nur um Beispielkonfigurationen. Es gibt eine Vielzahl weiterer möglicher Konfigurationen, die in diesem Thema nicht behandelt werden.

### <a name="single-rss-capable-network-adapter"></a>Einzelner RSS-fähiger Netzwerkadapter

Diese typische Konfiguration umfasst einen SMB-Client und einen SMB-Server mit einem einzelnen 10-GbE-Netzwerkadapter (Gigabit Ethernet). Wenn SMB ohne das Multichannelfeature bereitgestellt und nur eine SMB-Sitzung eingerichtet wurde, erstellt SMB eine einzelne TCP/IP-Verbindung. Da nur ein CPU-Kern vorhanden ist, führt diese Konfiguration zwangsläufig zu einer Überlastung, insbesondere dann, wenn viele kleine E/A-Vorgänge ausgeführt werden. Hier besteht also ein erhebliches Potenzial für einen Leistungsengpass.

Die meisten aktuellen Netzwerkadapter bieten eine Funktion namens RSS (Receive Side Scaling), mit der mehrere Verbindungen automatisch auf mehrere CPU-Kerne verteilt werden. Wenn Sie jedoch nur eine einzige Verbindung verwenden, ist RSS auch keine Hilfe. Wenn Sie SMB Multichannel mit einem RSS-fähigen Netzwerkadapter verwenden, erstellt SMB mehrere TCP/IP-Verbindungen für die jeweilige Sitzung. Mit einer solchen Konfiguration lassen sich potenzielle Leistungsengpässe in einem einzelnen CPU-Kern vermeiden, wenn viele kleine E/A-Vorgänge erforderlich sind.

### <a name="multiple-network-adapters"></a>Mehrere Netzwerkadapter

In diesem Szenario sind ein SMB-Client und ein SMB-Server mit mehreren 10-GbE-Netzwerkadaptern konfiguriert. Wenn SMB ohne das Multichannelfeature bereitgestellt und nur eine SMB-Sitzung eingerichtet wurde, verwendet SMB einen der verfügbaren Netzwerkadapter, um eine einzelne TCP/IP-Verbindung zu erstellen. In dieser Konfiguration ist es nicht möglich, die Bandbreite mehrerer Netzwerkadapter zu aggregieren: Sie können beispielsweise nicht einfach zwei 1-GbE-Netzwerkadapter verwenden, um 2 GBit/s zu erzielen. Außerdem besteht die Gefahr eines Ausfalls, wenn der ausgewählte Netzwerkadapter getrennt oder deaktiviert wird.

Bei Bereitstellung mit dem Multichannelfeature erstellt SMB mehrere TCP/IP-Verbindungen für eine einzelne Sitzung mit mindestens einer Verbindung pro Schnittstelle. Dies setzt voraus, dass die Netzwerkadapter RSS-fähig sind. In dieser Konfiguration kann SMB die kombinierte verfügbare Bandbreite der Netzwerkadapter nutzen, sodass der SMB-Client auch bei Ausfall eines Netzwerkadapters ohne Unterbrechung ausgeführt werden kann.

### <a name="nic-teaming"></a>NIC-Teaming

Über ein Feature namens NIC-Teaming unterstützen Azure Stack HCI und Windows Server die Fähigkeit, mehrere Netzwerkadapter zu einem einzigen Netzwerkadapter zu kombinieren. Ein Team bietet zwar immer Fehlertoleranz, aber wenn SMB ohne das Multichannelfeature bereitgestellt wird, wird nur eine TCP/IP-Verbindung pro Team erstellt. Diese Konfiguration führt zu Einschränkungen sowohl hinsichtlich der Anzahl von verwendeten CPU-Kernen als auch im Hinblick auf die maximale Nutzbarkeit der Teambandbreite.

Bei Bereitstellung mit dem Multichannelfeature erstellt SMB mehrere TCP/IP-Verbindungen für eine einzelne Sitzung, um eine bessere Balance zwischen den CPU-Kernen und eine bessere Nutzung der verfügbaren Bandbreite zu erzielen. NIC-Teaming bietet außerdem eine Failoverfunktion, die schneller ist als die alleinige Verwendung von SMB Multichannel. NIC-Teaming wird noch aus einem weiteren Grund empfohlen: Dieses Feature bietet Funktionen für ein Failover für andere Workloads, die nicht auf SMB basieren, weil sie von den Failoverfunktionen von SMB Multichannel nicht profitieren.

Wenn Sie eine dedizierte Gruppe von Netzwerkadaptern für den Datenverkehr des Features „Direkte Speicherplätze“ verwenden (dies ist bei Azure Stack HCI zuweilen der Fall), ist ein Teaming dieser Speichernetzwerkadapter rein optional – es bietet weder wesentliche Vorteile noch erhebliche Nachteile.

>[!IMPORTANT]
>Verwenden Sie NIC-Teaming nicht unter Windows Server 2012 R2 und früher, wenn Sie die RDMA-Funktionen der Netzwerkadapter nutzen möchten. In diesen Betriebssystemen wird ein Team aus RDMA-fähigen Netzwerkadaptern immer als nicht RDMA-fähig gemeldet, weil das Teaming die RDMA-Funktion des Netzwerkadapters deaktiviert.

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>Einzelne oder mehrere RDMA-fähige Netzwerkadapter

SMB Multichannel erkennt die RDMA-Funktionen von Netzwerkadaptern, die das SMB Direct-Feature namens SMB Direct über RDMA aktivieren. Ohne SMB Multichannel verwendet SMB reguläre TCP/IP-Verbindungen mit den RDMA-fähigen Netzwerkadaptern. Dabei stellen alle Netzwerkadapter einen TCP/IP-Stapel bereit, der parallel neben dem RDMA-Stapel existiert.

Bei Bereitstellung mit SMB Multichannel erkennt SMB die RDMA-Fähigkeit eines Netzwerkadapters und erstellt mehrere RDMA-Verbindungen für die jeweilige Einzelsitzung. Pro Schnittstelle werden zwei RDMA-Verbindungen erstellt. Mit dieser Konfiguration kann SMB von dem hohen Durchsatz, der niedrigen Latenz und der geringen CPU-Auslastung der RDMA-fähigen Netzwerkadapter profitieren. Bei Verwendung mehrerer RDMA-Schnittstellen bietet diese Konfiguration zudem Fehlertoleranz.

>[!IMPORTANT]
>Nach dem Erstellen einer RDMA-Verbindung wird die TCP/IP-Verbindung für die ursprüngliche Protokollaushandlung nicht mehr verwendet. Diese Verbindung wird jedoch beibehalten, falls eine andere RDMA-Verbindung ausfallen sollte.

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>SMB Multichannel, RDMA-fähige Netzwerkadapter und Kompatibilität mit NIC-Teaming

In der folgenden Tabelle sind die verschiedenen Funktionen zusammengefasst, die bei Kombination von SMB Multichannel, RDMA (SMB Direct) und NIC-Teaming verfügbar sind.

| Konfiguration                                           | Throughput | Fehlertoleranz mit SMB | Fehlertoleranz ohne SMB | Geringere CPU-Auslastung |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| Einzelner Netzwerkadapter (kein RSS)                         | *          |                         |                             |                       |
| Mehrere Netzwerkadapter (kein RSS)                      | **         | *                       |                             |                       |
| Mehrere Netzwerkadapter (kein RSS) mit NIC-Teaming     | **         | **                      | *                           |                       |
| Einzelner Netzwerkadapter mit RSS                         | *          |                         |                             |                       |
| Mehrere Netzwerkadapter mit RSS                      | **         | *                       |                             |                       |
| Mehrere Netzwerkadapter mit RSS und NIC-Teaming      | **         | **                      | *                           |                       |
| Einzelner RDMA-fähiger Netzwerkadapter                     | *          |                         |                             | *                     |
| Mehrere RDMA-fähige Netzwerkadapter                  | ***        | *                       |                             | *                     |
| Mehrere RDMA-fähige Netzwerkadapter mit NIC-Teaming | ***        | **                      | *                           | *                     |

Bei Ausführung von Windows Server 2016 oder höher besteht die ideale Lösung darin, mehrere RDMA-fähige Netzwerkadapter zu verwenden und NIC-Teaming mit SMB Multichannel zu kombinieren. Diese Kombination bietet den besten Durchsatz sowie Fehlertoleranz für Anwendungen, die SMB oder andere Protokolle verwenden. Zudem zeigt sie die geringsten Auswirkungen auf die CPU-Nutzung.

Wie bereits oben erwähnt, ist NIC-Teaming bei Verwendung von RDMA-fähigen Netzwerkadaptern unter Windows Server 2012 R2 oder früher keine gute Idee, weil die RDMA-Fähigkeit des Netzwerkadapters deaktiviert wird.

### <a name="example-configurations-without-smb-multichannel"></a>Beispielkonfigurationen ohne SMB Multichannel

Wenn Sie planen, einen einzelnen Netzwerkadapter ohne RSS zu verwenden, können Sie nicht von mehreren Netzwerkverbindungen profitieren. Daher wird SMB Multichannel nicht verwendet. Wenn Sie Netzwerkadapter mit unterschiedlichen Geschwindigkeiten einsetzen, wählt SMB Multichannel automatisch den schnellsten Netzwerkadapter aus. Dies liegt daran, dass Netzwerkadapter vom selben Typ (z. B. RDMA, RSS oder keinen dieser Typen) und mit derselben Geschwindigkeit von SMB Multichannel simultan verwendet werden. Die langsameren Netzwerkadapter befinden sich dann im Leerlauf.

## <a name="disable-smb-multichannel"></a>Deaktivieren von SMB Multichannel
In der Regel müssen Sie SMB Multichannel nicht deaktivieren. Wenn Sie das Feature jedoch deaktivieren möchten – beispielsweise in einer Testumgebung –, nutzen Sie die folgenden Windows PowerShell-Verfahren.

Stellen Sie zunächst eine Verbindung mit einem der Server her, indem Sie eine PowerShell-Sitzung öffnen:

```PowerShell
Enter-PSSession <server-name>
```

Zum Deaktivieren von SMB Multichannel auf Serverseite verwenden Sie das folgende Cmdlet:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

Zum Deaktivieren von SMB Multichannel auf Clientseite verwenden Sie das folgende Cmdlet:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>Durch Deaktivieren von SMB Multichannel auf dem Client oder dem Server werden beide Systeme an der Verwendung dieses Features gehindert.

## <a name="re-enable-smb-multichannel"></a>Erneutes Aktivieren von SMB Multichannel

Wenn Sie SMB Multichannel deaktiviert haben und es erneut aktivieren möchten, nutzen Sie die folgenden Vorgehensweisen.

Zum erneuten Aktivieren von SMB Multichannel auf Serverseite verwenden Sie das folgende Cmdlet:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

Zum erneuten Aktivieren von SMB Multichannel auf Clientseite verwenden Sie das folgende Cmdlet:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>Um SMB Multichannel wieder verwenden zu können, müssen Sie das Feature sowohl auf dem Client als auch auf dem Server erneut aktivieren.

## <a name="test-smb-multichannel"></a>Testen von SMB Multichannel

In diesem Abschnitt werden einige Szenarien zum Testen von SMB Multichannel beschrieben: In einem Szenario vergleichen Sie Dateikopien mit und ohne SMB Multichannel, in einem anderen verursachen Sie absichtlich den Ausfall eines Netzwerkadapters während einer Dateikopieraktion.

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>Vergleichen von Dateikopien mit und ohne SMB Multichannel

Um den von SMB Multichannel bereitgestellten höheren Durchsatz zu messen, führen Sie die folgenden Tests aus: Starten Sie vor dem Test sowohl den Server als auch den Client neu, um sicherzustellen, dass beide unter gleichen Bedingungen betrieben werden.

1. Richten Sie eine der oben beschriebenen Konfigurationen für SMB Multichannel ein.

1. Messen Sie die Dauer eines Dateikopiervorgangs mit langer Ausführungszeit über SMB Multichannel.

1. Deaktivieren Sie SMB Multichannel. Anweisungen dazu finden Sie unter [Deaktivieren von SMB Multichannel](#disable-smb-multichannel).

1. Messen Sie, wie lange es dauert, denselben Dateikopiervorgang mit langer Ausführungszeit ohne SMB Multichannel auszuführen.

1. Aktivieren Sie SMB Multichannel erneut. Anweisungen dazu finden Sie unter [Erneutes Aktivieren von SMB Multichannel](#re-enable-smb-multichannel).

1. Vergleichen Sie die Ergebnisse.

>[!IMPORTANT]
>Um Leistungsbeeinträchtigungen durch Zwischenspeicherung zu vermeiden, kopieren Sie zunächst eine große Datenmenge, die die Größe des verfügbaren Arbeitsspeichers überschreitet. Führen Sie den Kopiervorgang dann erneut aus. Der erste Kopiervorgang dient dabei als Aufwärmphase. Messen Sie nur die Dauer des zweiten Kopiervorgangs.

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>Verursachen des Ausfalls eines der Netzwerkadapter während eines Dateikopiervorgangs mit SMB Multichannel

So bestätigen Sie die Failoverfunktion von SMB Multichannel:

1. Stellen Sie sicher, dass SMB Multichannel in einer Konfiguration mit mehreren Netzwerkadaptern betrieben wird.

1. Starten Sie einen Dateikopiervorgang mit langer Ausführungszeit.

1. Während der Dateikopiervorgang ausgeführt wird, simulieren Sie einen Ausfall eines der Netzwerkpfade, indem Sie eins der Kabel trennen oder einen der Netzwerkadapter deaktivieren.

1. Vergewissern Sie sich, dass der Kopiervorgang den verbleibenden Netzwerkadapter verwendet und weiter ohne Dateikopierfehler ausgeführt wird.

Stellen Sie sicher, dass der getrennte Netzwerkverbindungspfad von keiner anderen Workload verwendet wird. Diese Vorsichtsmaßnahme verhindert mögliche Fehler bei Workloads, die SMB Multichannel gar nicht nutzen.

## <a name="verify-that-smb-multichannel-works"></a>Überprüfen, ob SMB Multichannel funktioniert

Gehen Sie folgendermaßen vor, um zu überprüfen, ob SMB Multichannel funktioniert.

1. Um die korrekte Konfiguration der Netzwerkadapter zu überprüfen, geben Sie sowohl auf dem SMB-Server als auch auf dem SMB-Client Folgendes in Windows PowerShell ein:

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. Um zu überprüfen, ob SMB Multichannel aktiviert ist, vergewissern Sie sich, dass SMB die Netzwerkadapter sowie die RSS- und RDMA-Funktionen der Adapter ordnungsgemäß identifiziert. Gehen Sie folgendermaßen vor:

   Geben Sie auf dem SMB-Client Folgendes in Windows PowerShell ein:

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   Geben Sie auf dem SMB-Server Folgendes in Windows PowerShell ein:

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. Starten Sie auf dem SMB-Client einen Dateikopiervorgang mit langer Ausführungszeit, der eine laufende Sitzung mit dem SMB-Server erstellt. Während der Kopiervorgang ausgeführt wird, geben Sie Folgendes in Windows PowerShell ein, um zu überprüfen, ob die Verbindung die richtige Version von SMB verwendet und ob SMB Multichannel funktioniert:

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Übersicht über die Dateifreigabe mithilfe des SMB 3-Protokolls in Windows Server](/windows-server/storage/file-server/file-server-smb-overview)
- [Vereinfachtes SMB Multichannel und Multi-NIC-Clusternetzwerke](/windows-server/failover-clustering/smb-multichannel)
- [SMB Direct](/windows-server/storage/file-server/smb-direct)
