---
title: 'Azure Stack Hub-Speicherinfrastruktur: Übersicht'
titleSuffix: Azure Stack
description: Erfahren Sie, wie Sie die Speicherinfrastruktur für Azure Stack Hub verwalten können.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperfq4
ms.openlocfilehash: 0712caec89d3a6e2203ca780b4877b330953c61c
ms.sourcegitcommit: 4a8d7203fd06aeb2c3026d31ffec9d4fbd403613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202490"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack Hub-Speicherinfrastruktur: Übersicht

Dieser Artikel bietet eine Übersicht über die Azure Stack Hub-Speicherinfrastruktur.

## <a name="understand-drives-and-volumes"></a>Grundlegendes zu Laufwerken und Volumes

### <a name="drives"></a>Laufwerke

Azure Stack Hub basiert auf Windows Server-Software und definiert Speicherfunktionen mit einer Kombination aus „Direkte Speicherplätze“ (Storage Spaces Direct, S2D) und Windows Server-Failoverclustering. Diese Kombination bietet einen leistungsstarken, skalierbaren und resilienten Speicherdienst.

Partner für integrierte Azure Stack Hub-Systeme bieten viele verschiedene Lösungsvarianten an, z. B. umfassende Flexibilität in Bezug auf Speicher. Derzeit können bis zu zwei Laufwerkstypen aus den drei unterstützten Laufwerkstypen ausgewählt werden: Nicht flüchtiger Speicher (Non-Volatile Memory Express, NVMe), SSD-Datenträger (SATA/SAS SSD) und Festplattenlaufwerk (Hard Disk Drive, HDD). 

„Direkte Speicherplätze“ verfügt über einen Cache zum Maximieren der Speicherleistung. In einer Azure Stack Hub-Appliance mit einem Laufwerkstyp (d. h. NVMe oder SSD) werden alle Laufwerke für die Kapazität verwendet. Bei zwei Laufwerkstypen werden für „Direkte Speicherplätze“ automatisch alle Laufwerke des „schnellsten“ Typs (NVMe &gt; SSD &gt; HDD) für die Zwischenspeicherung verwendet. Alle weiteren Datenträger werden zur Bereitstellung der Kapazität verwendet. Diese Laufwerke können entweder zu einer Bereitstellung vom Typ „Nur Flash“ oder „Hybrid“ gruppiert werden:

![Azure Stack Hub-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image1.png)

Bei Nur-Flash-Bereitstellungen wird die Speicherleistung maximiert, und es sind keine rotierenden HDDs vorhanden.

![Azure Stack Hub-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image2.png)

Bei Hybridbereitstellungen soll eine Balance zwischen Leistung und Kapazität erzielt oder die Kapazität maximiert werden, und es werden rotierende HDDs verwendet.

Das Verhalten des Caches wird automatisch anhand des Typs von Laufwerken ermittelt, für die die Zwischenspeicherung durchgeführt wird. Bei der Zwischenspeicherung für SSDs (z. B. NVMe-Zwischenspeicherung für SSDs) werden nur Schreibvorgänge zwischengespeichert. Auf diese Weise wird die Belastung der Kapazitätslaufwerke verringert und der gesamte Datenverkehr reduziert, sodass sich ihre Lebensdauer erhöht. In der Zwischenzeit werden Lesevorgänge nicht zwischengespeichert. Lesevorgänge wirken sich nicht stark auf die Lebensdauer von Flashspeicher aus, und SSD-Datenträger weisen bei diesen Vorgängen generell eine geringe Latenz auf. Beim Zwischenspeichern für HDDs (z. B. SSD-Zwischenspeicherung für HDDs) werden Lese- und Schreibvorgänge zwischengespeichert, um in beiden Fällen Latenzwerte wie bei Flashspeicher zu erzielen (häufig ca. um den Faktor 10 verbessert).

![Azure Stack Hub-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image3.png)

Informationen zu den verfügbaren Speicherkonfigurationen und eine ausführliche Spezifikation erhalten Sie beim Azure Stack Hub-OEM-Partner (https://azure.microsoft.com/overview/azure-stack/partners/).

> [!Note]  
> Die Azure Stack Hub-Appliance kann im Rahmen einer Hybridbereitstellung, die sowohl über HDD- als auch über SSD-Laufwerke (oder NVMe) verfügt, verfügbar gemacht werden. Die Laufwerke des schnelleren Typs werden dann aber als Cachelaufwerke verwendet, und alle verbleibenden Laufwerke werden als Kapazitätslaufwerke in einem Pool genutzt. Die Mandantendaten (Blobs, Tabellen, Warteschlangen und Datenträger) werden auf Kapazitätslaufwerken angeordnet. Die Bereitstellung von Premium-Datenträgern oder die Auswahl des Kontotyps „Storage Premium“ bedeutet also nicht, dass die Objekte auf jeden Fall SSD- oder NVMe-Laufwerken zugeordnet werden.

### <a name="volumes"></a>Volumes

Der *Speicherdienst* partitioniert den verfügbaren Speicher in separate Volumes, die zum Speichern von System- und Mandantendaten zugeordnet werden. Für Volumes werden die Laufwerke im Speicherpool kombiniert, um die Fehlertoleranz, Skalierbarkeit und Leistungsvorteile von „Direkte Speicherplätze“ zu bieten.

![Azure Stack Hub-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image4.png)

Es gibt drei Arten von Volumes, die im Azure Stack Hub-Speicherpool erstellt werden:

- Infrastruktur: Hier werden Dateien gehostet, die von VMs und Kerndiensten der Azure Stack Hub-Infrastruktur verwendet werden.

- VM temporär: Hier werden die temporären Datenträger gehostet, die an Mandanten-VMs angefügt sind, und diese Daten werden auf diesen Datenträgern gespeichert.

- Objektspeicher: Hier werden Mandantendaten für Blobs, Tabellen, Warteschlangen und VM-Datenträger gehostet.

In einer Bereitstellung mit mehreren Knoten sind drei Infrastrukturvolumes vorhanden, und die Anzahl von temporären VM-Volumes und Objektspeichervolumes entspricht der Anzahl von Knoten in der Azure Stack Hub-Bereitstellung:

- Bei einer Bereitstellung mit vier Knoten sind vier gleiche temporäre VM-Volumes und vier gleiche Objektspeichervolumes vorhanden.

- Wenn Sie dem Cluster einen neuen Knoten hinzufügen, wird für beide Typen ein neues Volume erstellt.

- Die Anzahl von Volumes bleibt auch dann gleich, wenn ein Knoten fehlerhaft ist oder entfernt wurde.

- Bei Verwendung des Azure Stack Development Kit ist ein einzelnes Volume mit mehreren Freigaben vorhanden.

Volumes in „Direkte Speicherplätze“ bieten Resilienz zum Schutz vor Hardwareproblemen wie Laufwerk- oder Serverfehlern. Außerdem ermöglichen sie die fortlaufende Verfügbarkeit während der Serverwartung, wie etwa Software Updates. Für die Azure Stack Hub-Bereitstellung wird die Drei-Wege-Spiegelung verwendet, um für Datenresilienz zu sorgen. Drei Kopien von Mandantendaten werden auf unterschiedliche Server geschrieben, wo sie im Cache gespeichert werden:

![Azure Stack Hub-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image5.png)

Die Spiegelung ermöglicht Fehlertoleranz, indem mehrere Kopien aller Daten aufbewahrt werden. Das Striping und die Anordnung dieser Daten sind nicht trivial, aber alle per Spiegelung gespeicherten Daten werden vollständig mehrfach geschrieben. Jede Kopie wird auf andere physische Hardware (unterschiedliche Laufwerke von nicht identischen Servern) geschrieben, für die angenommen wird, dass sie nicht gemeinsam ausfallen. Bei der Drei-Wege-Spiegelung können mindestens zwei gleichzeitige Hardwareprobleme (Laufwerk oder Server) problemlos toleriert werden. Wenn Sie beispielsweise einen Server neu starten und plötzlich ein anderes Laufwerk bzw. ein Server ausfällt, bleiben alle Daten geschützt und sind ohne Unterbrechung zugänglich.

## <a name="next-step"></a>Nächster Schritt

[Verwalten der Speicherkapazität](azure-stack-manage-storage-shares.md) 
