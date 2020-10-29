---
title: Verwenden des CSV-In-Memory-Lesecaches mit Azure Stack HCI
description: In diesem Thema wird beschrieben, wie Sie den Arbeitsspeicher des Systems zum Steigern der Leistung einsetzen.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/16/2020
ms.openlocfilehash: 47bfa8c656a2581c9dc125b1bd99379b9012e448
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157630"
---
# <a name="use-the-csv-in-memory-read-cache-with-azure-stack-hci"></a>Verwenden des CSV-In-Memory-Lesecaches mit Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

In diesem Thema wird beschrieben, wie Sie den Arbeitsspeicher des Systems einsetzen, um die Leistung von Azure Stack HCI durch das Zwischenspeichern häufiger Lesevorgänge zu steigern. Schreibvorgänge können nicht im Arbeitsspeicher zwischengespeichert werden.

Azure Stack HCI ist mit dem CSV-In-Memory-Lesecache (Cluster Shared Volume, Freigegebenes Clustervolume) kompatibel. Der Einsatz des Arbeitsspeichers des Systems zum Zwischenspeichern von Lesevorgängen kann die Leistung von Anwendungen wie Hyper-V verbessern, die ungepufferte E/A-Vorgänge für den Zugriff auf VHD- oder VHDX-Dateien verwenden. (Nicht gepufferte E/A-Vorgänge sind Vorgänge, die nicht vom Windows-Cache-Manager zwischengespeichert werden.)

Da der In-Memory-Cache serverbasiert ist, verbessert er die Datenlokalität. Neuere Lesevorgänge werden im Arbeitsspeicher desselben Hosts zwischengespeichert, auf dem der virtuelle Computer läuft, wodurch die Anzahl der Lesevorgänge über das Netzwerk reduziert wird. Dies führt zu einer geringeren Latenz und einer besseren Arbeitsspeicherleistung.

Beachten Sie, dass sich der CSV-In-Memory-Lesecache im Arbeitsspeicher vom [Speicherpoolcache](../concepts/cache.md) in Azure Stack HCI unterscheidet.

## <a name="planning-considerations"></a>Überlegungen zur Planung

Der In-Memory-Lesecache ist am effektivsten für Workloads mit vielen Lesevorgängen, wie z. B. Virtual Desktop Infrastructure (VDI). Bei Workloads mit extrem vielen Schreibvorgängen kann der Cache hingegen mehr Aufwand als Nutzen bringen und sollte deshalb deaktiviert werden.

Sie können bis zu 80 % des gesamten physischen Arbeitsspeichers für den CSV-In-Memory-Lesecache verwenden. Achten Sie darauf, ausreichend Arbeitsspeicher für Ihre virtuellen Computer zur Verfügung zu stellen!

  > [!NOTE]
  > Bestimmte Tools für das Microbenchmarking wie DISKSPD und [VM Fleet](https://github.com/Microsoft/diskspd/tree/master/Frameworks/VMFleet) erzielen mit aktiviertem CSV-In-Memory-Read-Cache ggf. schlechtere Ergebnisse als ohne. VM Fleet erstellt standardmäßig eine 10 GiB große VHDX pro virtueller Computer (insgesamt etwa 1 TiB für 100 VMs) und führt dann *gleichmäßig zufällige* Lese- und Schreibvorgänge auf diesen VMs durch. Im Gegensatz zu realen Workloads folgen die Lesevorgänge keinem vorhersehbaren oder sich wiederholenden Muster. Daher ist der In-Memory-Cache nicht effektiv, sondern verursacht nur zusätzlichen Verarbeitungsaufwand.

## <a name="configuring-the-in-memory-read-cache"></a>Konfigurieren des In-Memory-Lesecaches

Der CSV-In-Memory-Lesecache ist in Azure Stack HCI, Windows Server 2019 und Windows Server 2016 mit gleicher Funktionalität verfügbar. Unter Azure Stack HCI und Windows Server 2019 ist dieser Cache mit zugeteilter Größe von 1 GiB (Gibibyte) standardmäßig aktiviert. Unter Windows Server 2016 ist er standardmäßig deaktiviert.

| Betriebssystemversion          | Standardgröße des CSV-Caches |
|---------------------|------------------------|
| Azure Stack HCI     | 1 GiB                  |
| Windows Server 2019 | 1 GiB                  |
| Windows Server 2016 | 0 (deaktiviert)           |

Um den Cache mithilfe des Windows Admin Centers zu konfigurieren, wählen Sie ganz unten im Menü **Extras** auf der linken Seite **Einstellungen** aus. Wechseln Sie dann zu **Speicher > In-Memory-Cache** . Sie aktivieren bzw. deaktivieren den Cache über ein Kontrollkästchen. Außerdem können Sie den maximalen Arbeitsspeicher pro Server angeben, der dem Cache zugeordnet werden soll. Klicken Sie unbedingt nach Abschluss Ihrer Änderungen unten auf der Seite auf **Speichern** .

Um mit PowerShell festzustellen, wie viel Arbeitsspeicher zugeteilt ist, führen Sie Folgendes aus:

```PowerShell
(Get-Cluster).BlockCacheSize
```

Der zurückgegebene Wert entspricht Mebibytes (MiB) pro Server. `1024` stellt z. B. 1 GiB dar.

Um die Größe des zugeteilten Arbeitsspeichers zu ändern, bearbeiten Sie diesen Wert mit PowerShell. Um z. B. 2 GiB pro Server zuzuteilen, führen Sie Folgendes aus:

```PowerShell
(Get-Cluster).BlockCacheSize = 2048
```

Damit Änderungen sofort wirksam werden, müssen Sie Ihre CSV-Volumes anhalten und fortsetzen oder auf andere Server verschieben. Verschieben Sie z. B. mit diesem PowerShell-Fragment jedes CSV auf einen anderen Serverknoten und wieder zurück:

```PowerShell
Get-ClusterSharedVolume | ForEach {
    $Owner = $_.OwnerNode
    $_ | Move-ClusterSharedVolume
    $_ | Move-ClusterSharedVolume -Node $Owner
}
```

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen:

- [Grundlegendes zum Speicherpoolcache](../concepts/cache.md)
- [Verwenden freigegebener Clustervolumes in einem Failovercluster](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)
