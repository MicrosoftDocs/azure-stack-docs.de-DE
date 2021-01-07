---
title: 'Azure Stack Hub-Speicherinfrastruktur: Übersicht'
titleSuffix: Azure Stack
description: Erfahren Sie, wie Sie die Speicherinfrastruktur für Azure Stack Hub verwalten können.
author: PatAltimore
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: patricka
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperf-fy20q4
ms.openlocfilehash: 04a4e2314fb91a2a8a8eaedf0ca5d5849c8bee10
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869167"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack Hub-Speicherinfrastruktur: Übersicht

In diesem Artikel werden die Konzepte der Azure Stack Hub-Speicherinfrastruktur erläutert. Er enthält Informationen zu Laufwerken und Volumes sowie zu ihrer Verwendung in Azure Stack Hub.

## <a name="drives"></a>Laufwerke

### <a name="drive-types"></a>Laufwerkstypen

Partner für integrierte Azure Stack Hub-Systeme bieten viele verschiedene Lösungsvarianten an, z. B. umfassende Flexibilität in Bezug auf Speicher. Sie können bis zu **zwei** Laufwerkstypen aus den drei unterstützten Laufwerkstypen auswählen:

1. NVMe (Non-Volatile Memory Express)
1. SATA-/SAS-SSD (Solid State Drive)
1. HDD (Hard Disk Drive)

### <a name="performance-vs-capacity"></a>Leistung und Kapazität

Azure Stack Hub verwendet S2D (Storage Spaces Direct, Direkte Speicherplätze) mit Windows Server-Failoverclustering. Diese Kombination bietet einen leistungsstarken, skalierbaren und resilienten Speicherdienst.

Azure Stack-Bereitstellungen können die Speicherleistung maximieren oder eine Balance zwischen Leistung und Kapazität erzielen.

„Direkte Speicherplätze“ verwendet einen Cache zum Maximieren der Speicherleistung.

### <a name="how-drive-types-are-used"></a>Verwendung von Laufwerkstypen

In einer Azure Stack Hub-Appliance mit einem Laufwerkstyp werden alle Laufwerke für die Kapazität verwendet.

Bei zwei Laufwerkstypen werden für „Direkte Speicherplätze“ automatisch alle Laufwerke des „schnellsten“ Typs (NVMe &gt; SSD &gt; HDD) für die Zwischenspeicherung verwendet. Alle weiteren Datenträger werden zur Bereitstellung der Kapazität verwendet.

### <a name="all-flash-or-hybrid"></a>Nur Flash oder Hybrid

Die Laufwerke können entweder zu einer Bereitstellung vom Typ „Nur Flash“ oder „Hybrid“ gruppiert werden.

Bei Nur-Flash-Bereitstellungen wird die Speicherleistung maximiert, und es sind keine rotierenden HDDs vorhanden.

![Diagramm, das eine Nur-Flash-Bereitstellungsgruppierung zeigt.](media/azure-stack-storage-infrastructure-overview/image1.png)


Bei Hybridbereitstellungen soll eine Balance zwischen Leistung und Kapazität erzielt oder die Kapazität maximiert werden, und es werden rotierende HDDs verwendet.

![Diagramm, das eine Hybridbereitstellungsgruppierung zeigt.](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>Verhalten beim Zwischenspeichern

Das Verhalten des Cache wird automatisch anhand des Laufwerkstyps ermittelt. Bei der Zwischenspeicherung für SSDs (z. B. NVMe-Zwischenspeicherung für SSDs) werden nur Schreibvorgänge zwischengespeichert. Auf diese Weise wird die Belastung der Kapazitätslaufwerke verringert und der gesamte Datenverkehr reduziert, sodass sich ihre Lebensdauer erhöht.

Lesevorgänge werden nicht zwischengespeichert. Lesevorgänge wirken sich nicht stark auf die Lebensdauer von Flashspeicher aus, und SSD-Datenträger weisen bei diesen Vorgängen generell eine geringe Latenz auf.

Beim Zwischenspeichern für HDDs (z. B. SSD-Zwischenspeicherung für HDDs) werden Lese- und Schreibvorgänge zwischengespeichert, um in beiden Fällen Latenzwerte wie bei Flashspeicher zu erzielen (häufig ca. um den Faktor 10 verbessert).

![Diagramm, das das Verhalten beim Zwischenspeichern des Azure Stack Hub-Speichers zeigt.](media/azure-stack-storage-infrastructure-overview/image3.svg)

Informationen zu den verfügbaren Speicherkonfigurationen und eine ausführliche Spezifikation erhalten Sie beim Azure Stack Hub-OEM-Partner (https://azure.microsoft.com/overview/azure-stack/partners/).

> [!NOTE]
> Die Azure Stack Hub-Appliance kann im Rahmen einer Hybridbereitstellung, die sowohl über HDD- als auch über SSD-Laufwerke (oder NVMe) verfügt, verfügbar gemacht werden. Die Laufwerke des schnelleren Typs werden dann aber als Cachelaufwerke verwendet, und alle verbleibenden Laufwerke werden als Kapazitätslaufwerke in einem Pool genutzt. Die Mandantendaten (Blobs, Tabellen, Warteschlangen und Datenträger) werden auf Kapazitätslaufwerken angeordnet. Die Bereitstellung von Premium-Datenträgern oder die Auswahl des Kontotyps „Storage Premium“ bedeutet also nicht, dass die Objekte auf jeden Fall SSD- oder NVMe-Laufwerken zugeordnet werden.

## <a name="volumes"></a>Volumes

Der *Speicherdienst* partitioniert den verfügbaren Speicher in separate Volumes, die zum Speichern von System- und Mandantendaten zugeordnet werden. Für Volumes werden die Laufwerke im Speicherpool kombiniert, um die Fehlertoleranz, Skalierbarkeit und Leistungsvorteile von „Direkte Speicherplätze“ zu bieten.

![Diagramm, das die Partitionen des Azure Stack Hub-Speicherdiensts zeigt.](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>Volumetypen

Es gibt drei Arten von Volumes, die im Azure Stack Hub-Speicherpool erstellt werden:

1. **Infrastrukturvolumes** hosten Dateien, die von VMs und Kerndiensten der Azure Stack Hub-Infrastruktur verwendet werden.
1. Auf Volumes vom Typ **VM temporär** werden die temporären Datenträger gehostet, die an Mandanten-VMs angefügt sind, und diese Daten werden auf diesen Datenträgern gespeichert.
1. Auf **Objektspeichervolumes** werden Mandantendaten für Blobs, Tabellen, Warteschlangen und VM-Datenträger gehostet.

### <a name="volumes-in-a-multi-node-deployment"></a>Volumes in einer Bereitstellung mit mehreren Knoten

In einer Bereitstellung mit mehreren Knoten gibt es drei Infrastrukturvolumes.

Die Anzahl von temporären VM-Volumes und Objektspeichervolumes entspricht der Anzahl von Knoten in der Azure Stack Hub-Bereitstellung:

- Bei einer Bereitstellung mit vier Knoten sind vier gleiche temporäre VM-Volumes und vier gleiche Objektspeichervolumes vorhanden.

- Wenn Sie dem Cluster einen neuen Knoten hinzufügen, wird für beide Typen ein neues Volume erstellt.

- Die Anzahl von Volumes bleibt auch dann gleich, wenn ein Knoten fehlerhaft ist oder entfernt wurde.

> [!NOTE]
> Bei Verwendung des [Azure Stack Development Kit (ASDK)](../asdk/index.yml) ist ein einzelnes Volume mit mehreren [Freigaben](azure-stack-manage-storage-shares.md) vorhanden.

### <a name="fault-tolerance-and-mirroring"></a>Fehlertoleranz und Spiegelung

Volumes in „Direkte Speicherplätze“ bieten Resilienz zum Schutz vor Hardwareproblemen wie Laufwerk- oder Serverfehlern. Sie ermöglichen die fortlaufende Verfügbarkeit während der Serverwartung (etwa bei Softwareupdates).

Die Spiegelung ermöglicht Fehlertoleranz, indem mehrere Kopien aller Daten aufbewahrt werden. Das Striping und die Anordnung dieser Daten sind nicht trivial, aber alle per Spiegelung gespeicherten Daten werden vollständig mehrfach geschrieben. Jede Kopie wird auf andere physische Hardware (unterschiedliche Laufwerke von nicht identischen Servern) geschrieben, für die angenommen wird, dass sie nicht gemeinsam ausfallen. 

Für die Azure Stack Hub-Bereitstellung wird die Drei-Wege-Spiegelung verwendet, um für Datenresilienz zu sorgen. Bei der Drei-Wege-Spiegelung können mindestens zwei gleichzeitige Hardwareprobleme (Laufwerk oder Server) problemlos toleriert werden. Wenn Sie beispielsweise einen Server neu starten und plötzlich ein anderes Laufwerk bzw. ein Server ausfällt, bleiben alle Daten geschützt und sind ohne Unterbrechung zugänglich.

Drei Kopien von Mandantendaten werden auf unterschiedliche Server geschrieben, wo sie im Cache gespeichert werden:

![Diagramm, das zeigt, wie drei Kopien der Mandantendaten auf unterschiedliche Server geschrieben werden.](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>Nächster Schritt

[Verwalten der Speicherkapazität](azure-stack-manage-storage-shares.md) 
