---
title: Auswählen von Laufwerken für Azure Stack HCI
description: Es wird beschrieben, wie Sie Laufwerke für „Direkte Speicherplätze“ in Azure Stack HCI auswählen.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: c16907c9fab70bd185e0174b79ce746770ff646a
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196850"
---
# <a name="choosing-drives-for-azure-stack-hci"></a>Auswählen von Laufwerken für Azure Stack HCI

>Gilt für: Windows Server 2019

Dieses Thema enthält eine Anleitung zur Auswahl von Laufwerken für [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview), um Ihre Leistungs- und Kapazitätsanforderungen für Azure Stack HCI zu erfüllen.

## <a name="drive-types"></a>Laufwerkstypen

Für „Direkte Speicherplätze“ können derzeit drei Arten von Laufwerken verwendet werden:

|||
|----------------------|--------------------------|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe**-Laufwerke (Non-Volatile Memory Express) sind Solid State Drives, die direkt auf dem PCIe-Bus angeordnet sind. Häufig verwendete Formfaktoren sind 2,5" U.2, PCIe Add-In-Card (AIC) und M.2. NVMe ermöglicht einen höheren IOPS- und E/A-Durchsatz mit geringerer Latenz als alle anderen Laufwerkstypen, die derzeit unterstützt werden.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** steht für „Solid State Drives“, die über herkömmliche SATA- oder SAS-Verbindungen verbunden sind.|
|![Festplattenlaufwerk](media/choose-drives/HDD-100-px.png)|**HDD** steht für rotierende, magnetische Festplattenlaufwerke (Hard Disk Drives) mit hoher Speicherkapazität.|

## <a name="built-in-cache"></a>Integrierter Cache

„Direkte Speicherplätze“ verfügt über einen integrierten serverseitigen Cache. Es handelt sich um einen großen, persistenten Echtzeitcache für Lese- und Schreibvorgänge. Bei Bereitstellungen mit mehreren Arten von Laufwerken wird er automatisch so konfiguriert, dass alle „schnellsten“ Laufwerke verwendet werden. Alle weiteren Datenträger werden zur Bereitstellung der Kapazität verwendet.

Weitere Informationen finden Sie unter [Grundlegendes zum Cache in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/understand-the-cache).

## <a name="option-1--maximizing-performance"></a>Option 1: Maximieren der Leistung

Wir empfehlen Ihnen die Nutzung von reinen Flash-Bereitstellungen, um eine vorhersagbare und einheitliche Latenz unterhalb des Millisekundenbereichs für zufällige Lese- und Schreibvorgänge aller Daten oder einen sehr hohen IOPS- (bei uns [über sechs Millionen](https://www.youtube.com/watch?v=0LviCzsudGY&t=28m)!) bzw. E/A-Durchsatz (bei uns [mehr als 1 TB/s](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=16m50s)!) zu erzielen.

Hierfür gibt es derzeit drei Möglichkeiten:

![Optionen bei Nur-Flash-Bereitstellungen](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **Nur NVMe:** Die Verwendung einer reinen NVMe-Bereitstellung ermöglicht eine unvergleichlich hohe Leistung und eine geringe Latenz mit bestmöglicher Vorhersagbarkeit. Wenn Sie für Ihre gesamten Laufwerke das gleiche Modell nutzen, ist kein Cache vorhanden. Sie können auch NVMe-Modelle mit längerer und kürzerer Lebensdauer mischen und festlegen, dass Erstere die Schreibvorgänge für Letztere zwischenspeichern ([Einrichtung erforderlich](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

2. **NVMe und SSD:** Wenn NVMe-Laufwerke zusammen mit SSDs verwendet werden, werden vom NVMe-Laufwerk automatisch Schreibvorgänge für die SSDs zwischengespeichert. Auf diese Weise können Schreibvorgänge im Cache zusammengefasst werden. Das De-Staging wird hierfür dann nur bei Bedarf durchgeführt, um die Belastung für die SSDs zu verringern. Dies ermöglicht NVMe-ähnliche Schreibeigenschaften, während Lesevorgänge direkt über die ebenfalls schnellen SSDs bereitgestellt werden.

3. **Nur SSDs:** Wie auch bei einer reinen Bereitstellung mit NVMe-Laufwerken ist kein Cache vorhanden, wenn für alle Laufwerke das gleiche Modell verwendet wird. Wenn Sie Modelle mit längerer und kürzerer Lebensdauer mischen, können Sie festlegen, dass Erstere die Schreibvorgänge für Letztere zwischenspeichern ([Einrichtung erforderlich](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

   >[!NOTE]
   > Ein Vorteil bei der Verwendung von reinen NVMe- oder SSD-Bereitstellungen ohne Cache ist, dass Sie für jedes Laufwerk über nutzbare Speicherkapazität verfügen. Es geht keine Kapazität für die Zwischenspeicherung verloren. Dies kann bei kleineren Bereitstellungen ratsam sein.

## <a name="option-2--balancing-performance-and-capacity"></a>Option 2: Abstimmen von Leistung und Kapazität

Für Umgebungen mit einer Vielzahl von Anwendungen und Workloads, für die teilweise strenge Leistungsanforderungen gelten und teilweise beträchtliche Speicherkapazität benötigt wird, sollten Sie einen Hybridansatz wählen, bei dem entweder mit NVMe-Laufwerken oder SSDs die Zwischenspeicherung für größere HDDs durchgeführt wird.

![Optionen bei Hybridbereitstellungen](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe und HDDs:** Mit den NVMe-Laufwerken werden Lese- und Schreibvorgänge beschleunigt, indem beide zwischengespeichert werden. Die Zwischenspeicherung von Lesevorgängen ermöglicht den HDDs die Konzentration auf Schreibvorgänge. Mit der Zwischenspeicherung von Schreibvorgängen werden Bursts absorbiert, und Schreibvorgänge können zusammengefasst werden, bis bei Bedarf das De-Staging durchgeführt wird. Dies erfolgt auf künstlich serialisierte Weise, um den IOPS- und E/A-Durchsatz für HDDs zu maximieren. So können NVMe-ähnliche Schreibeigenschaften und – für häufig oder kürzlich gelesene Daten – auch NVMe-ähnliche Leseeigenschaften erzielt werden.

2. **SSDs und HDDs:** Ähnlich wie im obigen Fall werden Lese- und Schreibvorgänge mit SSDs beschleunigt, indem beide zwischengespeichert werden. Dies ermöglicht SSD-ähnliche Schreibeigenschaften und für häufig oder kürzlich gelesene Daten auch SSD-ähnliche Leseeigenschaften.

    Es gibt noch eine weitere Option, die eher ungewöhnlich ist: die Nutzung von Laufwerken *aller drei* Typen.

3. **NVMe, SSDs und HDDs:** Bei Verwendung von Laufwerken aller drei Typen übernehmen die NVMe-Laufwerke die Zwischenspeicherung für die SSDs und HDDs. Der Vorteil besteht darin, dass Sie Volumes auf den SSDs und den HDDs parallel in demselben Cluster erstellen können, die alle per NVMe beschleunigt werden. Erstere verhalten sich genauso wie bei einer reinen Flash-Bereitstellung, und Letztere verhalten sich genauso wie bei den oben beschriebenen Hybridbereitstellungen. Vom Konzept her entspricht dies der Nutzung von zwei Pools mit größtenteils unabhängiger Kapazitätsverwaltung, Ausfall- und Reparaturzyklen usw.

   >[!IMPORTANT]
   > Wir empfehlen Ihnen, die SSD-Ebene zu verwenden, um Ihre leistungsempfindlichsten Workloads als reine Flash-Bereitstellung anzuordnen.

## <a name="option-3--maximizing-capacity"></a>Option 3: Maximieren der Kapazität

Bei Workloads, für die nur selten eine sehr hohe Kapazität und Schreibvorgänge benötigt werden, z. B. Archivierung, Sicherungsziele, Data Warehouses oder „Cold Storage“, sollten Sie einige SSDs für die Zwischenspeicherung mit vielen großen HDDs zur Abdeckung der Kapazität kombinieren.

![Bereitstellungsoptionen zur Maximierung der Kapazität](media/choose-drives/maximizing-capacity.png)

1. **SSDs und HDDs:** Die SSDs übernehmen die Zwischenspeicherung der Lese- und Schreibvorgänge, um Bursts zu absorbieren und eine SSD-ähnliche Schreibleistung zu erzielen. Später wird dann das optimierte De-Staging für die HDDs durchgeführt.

>[!IMPORTANT]
>Konfigurationen nur mit HDDs werden nicht unterstützt. Es ist nicht ratsam, dass SSDs mit längerer Lebensdauer die Zwischenspeicherung für SSDs mit kürzerer Lebensdauer übernehmen.

## <a name="sizing-considerations"></a>Überlegungen zur Größenanpassung

### <a name="cache"></a>Cache

Jeder Server muss mindestens über zwei Cachelaufwerke verfügen (Mindestanforderung zur Erzielung von Redundanz). Wir empfehlen Ihnen, als Anzahl von Kapazitätslaufwerken ein Vielfaches der Anzahl von Cachelaufwerken zu wählen. Wenn Sie beispielsweise vier Cachelaufwerke verwenden, erhalten Sie eine einheitlichere Leistung, wenn Sie acht Kapazitätslaufwerke (Verhältnis 1:2) nutzen, und nicht sieben oder neun.

Die Cachegröße sollte so gewählt werden, dass der Arbeitssatz Ihrer Anwendungen und Workloads abgedeckt werden kann (also alle Daten, die jeweils aktiv gelesen und geschrieben werden). Darüber hinaus besteht für die Cachegröße keine weitere Anforderung. Für Bereitstellungen mit HDDs sind 10 % Kapazität ein guter Ausgangspunkt. Wenn beispielsweise jeder Server über vier 4-TB-HDDs mit insgesamt 16 TB Kapazität verfügt, ergibt sich für zwei 800-GB-SSDs ein Gesamtcache von 1,6 TB pro Server. Bei reinen Flash-Bereitstellungen – vor allem mit SSDs mit sehr [langer Lebensdauer](https://blogs.technet.microsoft.com/filecab/2017/08/11/understanding-dwpd-tbw/) – kann es ratsam sein, eher mit fünf Prozent Kapazität zu beginnen. Beispiel: Wenn jeder Server über eine Kapazität von 24 x 1,2-TB-SSD = 28,8 TB verfügt, ergeben sich 2 x 750-GB-NVMe = 1,5 TB an Cache pro Server. Sie können später jederzeit Cachelaufwerke hinzufügen oder entfernen, um dies anzupassen.

### <a name="general"></a>Allgemein

Wir empfehlen Ihnen, die Gesamtspeicherkapazität pro Server auf ca. 400 TB (Terabyte) zu begrenzen. Je mehr Speicherkapazität pro Server vorhanden ist, desto mehr Zeit wird nach einem Ausfall oder Neustart für die erneute Synchronisierung der Daten benötigt, z. B. beim Anwenden von Softwareupdates. Für Windows Server 2019 beträgt die maximale Größe pro Speicherpool derzeit 4 PB (Petabyte = 4.000 TB).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie auch unter:

- [Azure Stack HCI: Übersicht](../overview.md)
- [Grundlegendes zum Cache in Azure Stack HCI](cache.md)
- [Hardwareanforderungen für „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
- [Planen von Volumes in Azure Stack HCI](plan-volumes.md)
- [Fehlertoleranz und Speichereffizienz](fault-tolerance.md)