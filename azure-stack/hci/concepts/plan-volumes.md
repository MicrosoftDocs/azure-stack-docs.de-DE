---
title: Planen von Volumes in Azure Stack HCI
description: Es wird beschrieben, wie Sie Speichervolumes in Azure Stack HCI planen.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: c40d1ca54bfe71088b18413371b90bd26f8b7386
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250451"
---
# <a name="plan-volumes-in-azure-stack-hci"></a>Planen von Volumes in Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Dieses Thema enthält eine Anleitung zum Planen von Volumes in Azure Stack HCI, um die Leistungs- und Kapazitätsanforderungen für Ihre Workloads zu erfüllen, einschließlich Auswahl von Dateisystem, Resilienztyp und Größe.

## <a name="review-what-are-volumes"></a>Überprüfung: Was sind Volumes?

In Volumes ordnen Sie die Dateien an, die von Ihren Workloads benötigt werden, z. B. VHD- oder VHDX-Dateien für virtuelle Hyper-V-Computer. Für Volumes werden die Laufwerke im Speicherpool kombiniert, um die Fehlertoleranz, Skalierbarkeit und Leistungsvorteile von [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) zu erhalten, der softwaredefinierten Speichertechnologie hinter Azure Stack HCI.

   >[!NOTE]
   > In der gesamten Dokumentation für „Direkte Speicherplätze“ verwenden wir den Begriff „Volume“, um sowohl auf das Volume als auch auf den zugrunde liegenden virtuellen Datenträger zu verweisen – einschließlich der Funktionalität, die von anderen integrierten Windows-Features, z. B. freigegebenen Clustervolumes (Cluster Shared Volumes, CSV) und ReFS, bereitgestellt wird. Eine genaue Kenntnis dieser Unterschiede auf Implementierungsebene ist nicht erforderlich, um „Direkte Speicherplätze“ erfolgreich planen und bereitstellen zu können.

![what-are-volumes](media/plan-volumes/what-are-volumes.png)

Auf alle Volumes kann von allen Servern des Clusters gleichzeitig zugegriffen werden. Nach der Erstellung werden sie auf allen Servern unter **C:\ClusterStorage\\** angezeigt.

![csv-folder-screenshot](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>Auswählen der Anzahl zu erstellender Volumes

Wir empfehlen Ihnen, als Anzahl von Volumes ein Vielfaches der Anzahl von Servern Ihres Clusters zu wählen. Wenn Sie beispielsweise vier Server verwenden, erzielen Sie mit vier Volumes eine einheitlichere Leistung als mit drei oder fünf. Der Cluster kann den „Volumebesitz“ (ein Server führt die Metadatenorchestrierung für jedes Volume durch) dann gleichmäßig auf die Server verteilen.

Wir empfehlen, die Gesamtzahl von Volumes auf 64 pro Cluster zu begrenzen.

## <a name="choosing-the-filesystem"></a>Auswählen des Dateisystems

Wir empfehlen Ihnen die Verwendung des neuen [resilienten Dateisystems (Resilient File System, ReFS)](/en-us/windows-server/storage/refs/refs-overview) für „Direkte Speicherplätze“. ReFS ist ein Dateisystem, das speziell für die Virtualisierung entwickelt wurde. Es bietet viele Vorteile, z. B. erhebliche Leistungsverbesserungen und integrierter Schutz vor Datenbeschädigung. Nahezu alle wichtigen NTFS-Features werden unterstützt, z. B. die Datendeduplizierung unter Windows Server-Version 1709 und höher. Ausführliche Informationen finden Sie in der [Tabelle mit dem Featurevergleich](/windows-server/storage/refs/refs-overview#feature-comparison) für ReFS.

Falls Sie für Ihre Workload ein Feature benötigen, das von ReFS noch nicht unterstützt wird, können Sie stattdessen NTFS verwenden.

   > [!TIP]
   > Volumes mit unterschiedlichen Dateisystemen können in demselben Cluster parallel vorhanden sein.

## <a name="choosing-the-resiliency-type"></a>Auswählen des Resilienztyps

Volumes unter „Direkte Speicherplätze“ bieten Resilienz als Schutz vor Hardwareproblemen, z. B. Laufwerks- oder Serverfehler, und ermöglichen eine ununterbrochene Verfügbarkeit bei der Serverwartung, z. B. Softwareupdates.

   > [!NOTE]
   > Welche Resilienztypen Sie auswählen können, ist unabhängig davon, welche Laufwerkstypen Sie verwenden.

### <a name="with-two-servers"></a>Mit zwei Servern

Bei zwei Servern im Cluster können Sie die Zwei-Wege-Spiegelung oder die geschachtelte Resilienz verwenden.

Bei der Zwei-Wege-Spiegelung werden zwei Kopien aller Daten beibehalten (eine Kopie auf dem Laufwerk jedes Servers). Die Speichereffizienz beträgt hierbei 50 %. Zum Schreiben von 1 TB an Daten benötigen Sie mindestens 2 TB an physischer Speicherkapazität im Speicherpool. Bei der Zwei-Wege-Spiegelung kann jeweils problemlos ein Hardwarefehler toleriert werden (ein Server oder Laufwerk).

![Zwei-Wege-Spiegelung](media/plan-volumes/two-way-mirror.png)

Die geschachtelte Resilienz sorgt für Datenresilienz zwischen Servern mit Zwei-Wege-Spiegelung und fügt dann Resilienz auf einem Server mit Zwei-Wege-Spiegelung oder Parität mit Beschleunigung per Spiegelung hinzu. Die Schachtelung ermöglicht auch dann Datenresilienz, wenn ein Server neu gestartet wird oder nicht verfügbar ist. Hierbei beträgt die Speichereffizienz für die geschachtelte Zwei-Wege-Spiegelung 25 % und ca. 35 - 40 % für die geschachtelte Parität mit Beschleunigung per Spiegelung. Bei der geschachtelten Resilienz können jeweils zwei Hardwarefehler toleriert werden (zwei Laufwerke oder ein Server und ein Laufwerk auf dem verbleibenden Server). Aufgrund dieser zusätzlichen Datenresilienz empfiehlt sich die Verwendung der geschachtelten Resilienz in Produktionsbereitstellungen mit Clustern aus zwei Servern. Weitere Informationen finden Sie unter [Geschachtelte Resilienz](/windows-server/storage/storage-spaces/nested-resiliency).

![Geschachtelte Parität mit Beschleunigung per Spiegelung](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>Mit drei Servern

Bei drei Servern sollten Sie die Drei-Wege-Spiegelung verwenden, um eine bessere Fehlertoleranz und Leistung zu erzielen. Bei der Drei-Wege-Spiegelung werden drei Kopien aller Daten beibehalten (eine Kopie auf dem Laufwerk jedes Servers). Die Speichereffizienz beträgt hierbei 33,3 %. Zum Schreiben von 1 TB an Daten benötigen Sie mindestens 3 TB an physischer Speicherkapazität im Speicherpool. Bei der Drei-Wege-Spiegelung können [mindestens zwei gleichzeitige Hardwareprobleme (Laufwerk oder Server)](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples) problemlos toleriert werden. Wenn zwei Knoten ausfallen, geht das Quorum für den Speicherpool verloren, weil zwei Drittel der Datenträger nicht verfügbar sind und der Zugriff auf die virtuellen Datenträger nicht möglich ist. Die virtuellen Datenträger bleiben aber online, wenn ein Knoten ausfällt und ein oder mehrere Datenträger auf einem anderen Knoten ausfallen. Wenn Sie beispielsweise einen Server neu starten und plötzlich ein anderes Laufwerk bzw. ein Server ausfällt, bleiben alle Daten geschützt und sind ohne Unterbrechung zugänglich.

![Drei-Wege-Spiegelung](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>Mit vier oder mehr Servern

Bei vier oder mehr Servern können Sie für jedes Volume auswählen, ob die Drei-Wege-Spiegelung, die duale Parität (häufig als „Erasure Coding“ bezeichnet) oder eine Mischung dieser beiden Optionen mit Parität mit Beschleunigung per Spiegelung verwendet werden soll.

Die duale Parität bietet die gleiche Fehlertoleranz wie die Drei-Wege-Spiegelung, aber eine höhere Speichereffizienz. Bei vier Servern beträgt die Speichereffizienz 50,0 %. Zum Schreiben von 2 TB an Daten benötigen Sie 4 TB an physischer Speicherkapazität im Speicherpool. Bei sieben Servern erhöht sich die Speichereffizienz auf 66,7 %, und dieser Wert kann mit weiteren Servern auf bis zu 80,0 % gesteigert werden. Der Nachteil ist, dass es zu Leistungseinbußen kommen kann, weil die Paritätscodierung rechenintensiver ist.

![Duale Parität](media/plan-volumes/dual-parity.png)

Welcher Resilienztyp verwendet werden sollte, hängt von den Anforderungen Ihrer Workload ab. In der Tabelle unten ist zusammengefasst, welche Workloads für einen Resilienztyp jeweils gut geeignet sind. Außerdem sind die Leistung und die Speichereffizienz für jeden Resilienztyp angegeben.

| Resilienztyp | Kapazitätseffizienz | Geschwindigkeit | Arbeitsauslastungen |
| ------------------- | ----------------------  | --------- | ------------- |
| **Spiegel**         | ![Speichereffizienz von 33 %](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>Drei-Wege-Spiegelung: 33 % <br>Zwei-Wege-Spiegelung: 50%     |![Leistung von 100 %](media/plan-volumes/three-way-mirror-perf.png)<br> Höchste Leistung  | Virtualisierte Workloads<br> Datenbanken<br>Weitere Hochleistungsworkloads |
| **Parität mit Beschleunigung per Spiegelung** |![Speichereffizienz von ca. 50 %](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> Abhängig von Spiegelungs- und Paritätsanteil | ![Leistung von ca. 20 %](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>Deutlich langsamer als die Spiegelung, aber bis zu doppelt so schnell wie die duale Parität<br> Am besten für umfangreiche sequenzielle Schreib- und Lesevorgänge geeignet | Archivierung und Sicherung<br> Virtualisierte Desktopinfrastruktur     |
| **Duale Parität**               | ![Speichereffizienz von ca. 80 %](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4 Server: 50% <br>16 Server: bis zu 80 % | ![Leistung von ca. 10 %](media/plan-volumes/dual-parity-perf.png)<br>Höchste E/A-Latenz und CPU-Auslastung bei Schreibvorgängen<br> Am besten für umfangreiche sequenzielle Schreib- und Lesevorgänge geeignet | Archivierung und Sicherung<br> Virtualisierte Desktopinfrastruktur  |

#### <a name="when-performance-matters-most"></a>Bei hohen Leistungsanforderungen

Workloads, für die hohe Latenzanforderungen gelten oder für die viele verschiedene zufällige IOPS-Vorgänge durchgeführt werden müssen, z. B. SQL Server-Datenbanken oder leistungsempfindliche virtuelle Hyper-V-Computer, sollten auf Volumes ausgeführt werden, für die die Spiegelung zum Maximieren der Leistung genutzt wird.

   >[!TIP]
   > Die Spiegelung ist schneller als alle anderen Resilienztypen. Wir verwenden die Spiegelung für nahezu alle Beispiele zur Leistung.

#### <a name="when-capacity-matters-most"></a>Bei hohen Kapazitätsanforderungen

Workloads mit seltenen Schreibvorgängen, z. B. Data Warehouses oder „Cold Storage“, sollten auf Volumes ausgeführt werden, für die die duale Parität zum Maximieren der Speichereffizienz genutzt wird. Für bestimmte andere Workloads, z. B. herkömmliche Dateiserver, virtuelle Desktopinfrastruktur (VDI) oder andere, bei denen keine großen Mengen von sich schnell änderndem, zufälligem E/A-Datenverkehr anfallen oder für die nicht die höchstmögliche Leistung erforderlich ist, kann je nach Ihren Anforderungen auch die duale Parität genutzt werden. Im Vergleich zur Spiegelung kommt es bei Parität unweigerlich zu einer Erhöhung der CPU-Auslastung und E/A-Latenz, vor allem für Schreibvorgänge.

#### <a name="when-data-is-written-in-bulk"></a>Bei Massenschreibvorgängen für Daten

Für Workloads, bei denen es zu großen sequenziellen Schreibvorgängen kommt, z. B. Archivierungs- oder Sicherungsziele, gibt es eine andere Option: auf einem Volume können Spiegelung und duale Parität gemischt werden. Schreibvorgänge landen zuerst im gespiegelten Teil und werden später dann allmählich in den Paritätsteil verschoben. Hierdurch wird die Erfassung beschleunigt und die Ressourcenverwendung reduziert, wenn große Schreibvorgänge auftreten, indem es ermöglicht wird, dass die rechenintensive Paritätscodierung über einen längeren Zeitraum erfolgt. Beachten Sie bei der Größenanpassung für die Teile, dass die Menge an gleichzeitig durchgeführten Schreibvorgängen (z. B. die tägliche Sicherung) gut in den Spiegelungsteil passt. Wenn Sie beispielsweise einmal täglich 100 GB erfassen, sollten Sie erwägen, die Spiegelung für 150 bis 200 GB und die duale Parität für den Rest zu verwenden.

Die sich ergebende Speichereffizienz richtet sich nach den von Ihnen gewählten Anteilen. Einige Beispiele sind in [dieser Demo](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) enthalten.

   > [!TIP]
   > Falls Sie während der Datenerfassung eine abrupte Abnahme der Schreibleistung bemerken, kann dies ein Hinweis darauf sein, dass der Spiegelungsteil nicht groß genug ist oder die Parität mit Beschleunigung per Spiegelung für Ihren Anwendungsfall nicht gut geeignet ist. Beispiel: Wenn die Schreibleistung von 400 MB/s auf 40 MB/s abnimmt, sollten Sie erwägen, den Spiegelungsteil zu erweitern oder zur Drei-Wege-Spiegelung zu wechseln.

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>Informationen zu Bereitstellungen mit NVMe, SSDs und HDDs

Bei Bereitstellungen mit zwei Arten von Laufwerken übernehmen die schnelleren Laufwerke die Zwischenspeicherung und die langsameren die Kapazitätsaufgaben. Dies erfolgt automatisch. Weitere Informationen finden Sie unter [Grundlegendes zum Cache in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/understand-the-cache). Bei Bereitstellungen dieser Art befinden sich alle Volumes letztendlich auf dem gleichen Laufwerkstyp, nämlich den Kapazitätslaufwerken.

Bei Bereitstellungen mit allen drei Laufwerkstypen übernehmen nur die schnellsten Laufwerke (NVMe) die Zwischenspeicherung, und zwei Laufwerkstypen (SSD und HDD) sind für die Kapazität zuständig. Sie können für jedes Volume auswählen, ob es sich vollständig auf der SSD-Ebene oder der HDD-Ebene befindet oder ob es übergreifend angeordnet ist.

   > [!IMPORTANT]
   > Wir empfehlen Ihnen, die SSD-Ebene zu verwenden, um Ihre leistungsempfindlichsten Workloads als reine Flash-Bereitstellung anzuordnen.

## <a name="choosing-the-size-of-volumes"></a>Auswählen der Größe von Volumes

Wir empfehlen Ihnen, die Größe pro Volume unter Windows Server 2019 auf 64 TB zu beschränken.

   > [!TIP]
   > Bei Verwendung einer Sicherungslösung, die auf dem Volumeschattenkopie-Dienst (VSS) und dem Softwareanbieter Volsnap basiert – wie bei Dateiserverworkloads üblich –, können Leistung und Zuverlässigkeit durch die Begrenzung der Volumegröße auf 10 TB verbessert werden. Sicherungslösungen, für die die neuere Hyper-V RCT-API, das Klonen von ReFS-Blöcken oder die nativen SQL-Sicherungs-APIs genutzt werden, weisen bei bis zu 32 TB und darüber hinaus eine gute Leistung auf.

### <a name="footprint"></a>Speicherbedarf

Die Größe eines Volumes bezieht sich auf die nutzbare Kapazität – also die Daten, die gespeichert werden können. Hierfür wird der Parameter **-Size** des Cmdlets **New-Volume** verwendet. Die Größe wird dann in der **Size**-Eigenschaft angezeigt, wenn Sie das Cmdlet **Get-Volume** ausführen.

Die Größe ist etwas anderes als der *Speicherbedarf* des Volumes. Der Speicherbedarf ist die gesamte physische Speicherkapazität, die im Speicherpool dafür genutzt wird. Der Speicherbedarf richtet sich jeweils nach dem Resilienztyp. Für Volumes mit Drei-Wege-Spiegelung wird beispielsweise ein Speicherbedarf in Höhe ihrer dreifachen Größe benötigt.

Der Speicherpool muss ausreichend groß sein, um den Speicherbedarf Ihrer Volumes abdecken zu können.

![size-versus-footprint](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>Reservekapazität

Wenn ein Teil der Kapazität im Speicherpool nicht belegt wird, ist für Volumes nach Laufwerksausfällen Platz für direkte Reparaturen, und dies führt zu einer Verbesserung der Datensicherheit und Leistung. Wenn ausreichende Kapazität vorhanden ist, kann für Volumes durch eine sofortige, direkte, parallele Reparatur die Resilienz vollständig wiederhergestellt werden, noch bevor die ausgefallenen Laufwerke ausgetauscht werden. Dies erfolgt automatisch.

Wir empfehlen Ihnen, pro Server ein Kapazitätslaufwerk zu reservieren (bis zu vier Laufwerke). Sie können bei Bedarf auch mehr Laufwerke reservieren. Mit dieser Empfehlung zur Mindestkonfiguration wird aber sichergestellt, dass die sofortige, direkte, parallele Reparatur nach dem Ausfall eines Laufwerks erfolgreich durchgeführt werden kann.

![Reserve](media/plan-volumes/reserve.png)

Wenn Sie beispielsweise zwei Server und Kapazitätslaufwerke mit 1 TB verwenden, sollten Sie zwei Terabyte (2 x 1 = 2 TB) des Pools als Reserve bereitstellen. Bei drei Servern und Kapazitätslaufwerken mit 1 TB sind drei Terabyte (3 x 1 = 3 TB) als Reserve ratsam. Bei vier oder mehr Servern und Kapazitätslaufwerken mit 1 TB sind vier Terabyte (4 x 1 = 4 TB) als Reserve zu empfehlen.

   >[!NOTE]
   > Bei Clustern mit allen drei Laufwerkstypen (NVMe, SSDs und HDDs) lautet die Empfehlung, pro Server eine SSD und eine HDD zu reservieren (jeweils bis zu vier Laufwerke).

## <a name="example-capacity-planning"></a>Beispiel: Kapazitätsplanung

Stellen Sie sich einen Cluster mit vier Servern vor. Jeder Server verfügt über einige Cachelaufwerke sowie 16 Kapazitätslaufwerke mit jeweils 2 TB.

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

Von diesen 128 TB im Speicherpool reservieren wir vier Laufwerke (also 8 TB), damit direkte Reparaturen durchgeführt werden können, ohne dass Zeitnot beim Austauschen von Laufwerken nach einem Ausfall besteht. Es verbleiben 120 TB an physischer Speicherkapazität im Pool, die für die Erstellung von Volumes genutzt werden kann.

```
128 TB – (4 x 2 TB) = 120 TB
```

Angenommen, wir benötigen unsere Bereitstellung zum Hosten von einigen sehr aktiven virtuellen Hyper-V-Computern, verfügen aber auch über eine große Menge von „Cold Storage“ (alte Dateien und Sicherungen, die beibehalten werden müssen). Da wir vier Server haben, erstellen wir vier Volumes.

Wir ordnen die virtuellen Computer auf den ersten beiden Volumes *Volume1* und *Volume2* an. Wir verwenden ReFS als Dateisystem (aufgrund der schnelleren Erstellung und der Prüfpunkte) und die Drei-Wege-Spiegelung zur Erzielung von Resilienz, um die Leistung zu maximieren. „Cold Storage“ ordnen wir auf den anderen beiden Volumes *Volume3* und *Volume4* an. Wir wählen NTFS als Dateisystem (für die Datendeduplizierung) und die duale Parität zur Erzielung von Resilienz, um die Kapazität zu maximieren.

Es müssen nicht unbedingt alle Volumes die gleiche Größe haben, aber der Einfachheit halber wählen wir hier diese Vorgehensweise, indem wir beispielsweise für alle eine Größe von 12 TB festlegen.

*Volume1* und *Volume2* belegen jeweils 36 TB an physischer Speicherkapazität (12 TB x 33,3 % Effizienz).

*Volume3* und *Volume4* belegen jeweils 24 TB an physischer Speicherkapazität (12 TB x 50,0 % Effizienz).

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

Die vier Volumes füllen genau die physische Speicherkapazität aus, die in unserem Pool verfügbar ist. Perfekt!

![Beispiel](media/plan-volumes/example.png)

   >[!TIP]
   > Sie müssen nicht alle Volumes sofort erstellen. Es ist später jederzeit möglich, Volumes zu erweitern oder neue Volumes zu erstellen.

Der Einfachheit halber werden in diesem Beispiel durchgängig dezimale Einheiten (Basis 10) verwendet. Dies bedeutet dass 1 TB 1.000.000.000.000 Byte entspricht. Unter Windows werden Speichermengen dagegen als Binäreinheiten (Basis 2) angezeigt. Beispielsweise wird für 2-TB-Laufwerke unter Windows eine Größe von 1,82 TiB angegeben. Analog wird für den Speicherpool mit 128 TB eine Größe von 116,41 TiB angegeben. Dies entspricht dem erwarteten Verhalten.

## <a name="usage"></a>Verwendung

Weitere Informationen finden Sie unter [Erstellen von Volumes in Azure Stack HCI](../manage/create-volumes.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie auch unter:

- [Auswählen von Laufwerken für „Direkte Speicherplätze“](choose-drives.md)
- [Fehlertoleranz und Speichereffizienz](fault-tolerance.md)