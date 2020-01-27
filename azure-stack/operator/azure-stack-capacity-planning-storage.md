---
title: 'Azure Stack Hub-Kapazitätsplanung: Storage | Microsoft-Dokumentation'
description: Informationen zur Kapazitätsplanung für Azure Stack Hub-Bereitstellungen.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 79cb1de207d4d2722000273814d0cd39a3ea8820
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817498"
---
# <a name="azure-stack-hub-storage"></a>Azure Stack Hub-Speicher

Die folgenden Abschnitte enthalten Informationen zur Planung der Speicherkapazität von Azure Stack Hub, die bei der Planung des Speicherbedarfs der Lösung helfen.

## <a name="uses-and-organization-of-storage-capacity"></a>Verwendungszwecke und Organisation der Speicherkapazität
Die hyperkonvergente Konfiguration von Azure Stack Hub ermöglicht die gemeinsame Nutzung physischer Speichergeräte. Es gibt drei Hauptbereiche verfügbaren Speichers, die gemeinsam genutzt werden können: die Infrastruktur, die temporäre Speicherung der virtuellen Mandantencomputer und den Speicher, der die Blobs, Tabellen und Warteschlangen der ACS-Dienste (Azure Consistent Storage) sichert.

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Cache von „Direkte Speicherplätze“ und Kapazitätsebenen
Es gibt Speicherkapazität, die für das Betriebssystem, lokale Protokollierung, Speicherabbilder und andere temporäre Infrastrukturspeicheranforderungen verwendet wird. Diese lokale Speicherkapazität ist getrennt (Geräte und Kapazität) von den Speichergeräten, die unter der Verwaltung der Konfiguration „Direkte Speicherplätze“ stehen. Der Rest der Speichergeräte wird in einem einzigen Pool von Speicherkapazität platziert, und zwar unabhängig von der Anzahl der Server in der Skalierungseinheit.

Für diese Geräte werden zwei Typen unterschieden: Cache und Kapazität. „Direkte Speicherplätze“ nutzt diese Cachegeräte zum Zwischenspeichern von Rückschreib- und Lesevorgängen. Die Kapazitäten dieser Cachegeräte sind während ihrer Verwendung nicht auf die formatierte, „sichtbare“ Kapazität der formatierten virtuellen Datenträger festgelegt. Im Gegensatz dazu verwendet „Direkte Speicherplätze“ Kapazitätsgeräte zu diesem Zweck und stellt so den „Hauptstandort“ der verwalteten Daten bereit.

Die Azure Stack Hub-Infrastruktur ordnet und verwaltet die gesamte Speicherkapazität direkt. Der Operator muss keine Entscheidungen über Konfiguration, Zuordnung oder Kapazitätserweiterungen treffen. Azure Stack Hub automatisiert diese Entwurfsentscheidungen in Übereinstimmung mit den Lösungsanforderungen entweder während der Erstinstallation und -bereitstellung oder bei der Kapazitätserweiterung. Azure Stack Hub berücksichtigt dabei Details zur Resilienz, zur reservierten Kapazität für Neuerstellungen und andere Details als Teil des Entwurfs. 

Operatoren können zwischen einer *Nur-Flash*- und einer *Hybrid*speicherkonfiguration wählen:

![Diagramm der Azure-Speicherkapazitätsplanung](media/azure-stack-capacity-planning/storage.png)

Bei der Nur-Flash-Konfiguration kann eine Konfiguration mit zwei Ebenen oder eine Konfiguration mit einer Ebene verwendet werden. Wird eine Konfiguration mit einer Ebene verwendet, weisen alle Kapazitätsgeräte den gleichen Typ (z. B. NVMe, SATA-SSD oder SAS-SSD) auf, und es werden keine Cachegeräte verwendet. In einer Nur-Flash-Konfiguration mit zwei Ebenen werden in der Regel als Cachegeräte NVMe und als Kapazitätsgeräte SATA- oder SAS-SSDs verwendet.

In der Hybridkonfiguration mit zwei Ebenen ist der Cache wahlweise NVMe oder eine SATA- bzw. SAS-SSD, und für die Kapazität wird HDD verwendet. 

Die Speicherkonfiguration von „Direkte Speicherplätze“ und Azure Stack Hub lässt sich wie folgt kurz zusammenfassen:
- Ein „Direkte Speicherplätze“-Pool pro Skalierungseinheit (alle Speichergeräte werden innerhalb eines einzigen Pools konfiguriert).
- Virtuelle Datenträger werden als Spiegel mit drei Kopien für die beste Leistung und Resilienz erstellt.
- Jeder virtuelle Datenträger wird als ein ReFS-Dateisystem formatiert.
- Die Kapazität des virtuellen Datenträgers wird so berechnet und zugeordnet, dass die Datenkapazität eines Kapazitätsgeräts im Pool nicht zugeordnet wird. Dies entspricht einem Kapazitätslaufwerk pro Server.
- Für jedes ReFS-Dateisystem ist BitLocker für die Verschlüsselung ruhender Daten aktiviert. 

Die automatisch erstellten virtuellen Datenträger und die zugehörigen Kapazitäten stellen sich folgendermaßen dar:

|Name|Kapazitätsberechnung|Beschreibung|
|-----|-----|-----|
|Lokales/Startgerät|Mindestens 340 GB<sup>1</sup>|Einzelner Serverspeicher für Betriebssystemimages und „lokale“ Infrastruktur-VMs.|
|Infrastruktur|3,5 TB|Gesamte Azure Stack Hub-Infrastrukturnutzung.|
|VmTemp|Siehe unten<sup>2</sup>|An Mandanten-VMs ist ein temporärer Datenträger angefügt, und diese Daten werden auf diesen virtuellen Datenträgern gespeichert.|
|ACS|Siehe unten <sup>3</sup>|Azure Consistent Storage-Kapazität für Blobs, Tabellen und Warteschlangen.|

<sup>1</sup> Minimale Speicherkapazität, die für Azure Stack Hub-Lösungspartner erforderlich ist.

<sup>2</sup> Die für temporäre Datenträger von Tenant Virtual Machine verwendete Größe des virtuellen Datenträgers wird als Verhältnis des physischen Speichers des Servers berechnet. Der temporäre Datenträger ist ein Verhältnis aus dem physischen Speicher, der dem virtuellen Computer zugewiesen ist. Die Zuordnung für den Speicher „temporärer Datenträger“ in Azure Stack Hub erfasst die meisten Anwendungsfälle, kann aber möglicherweise nicht alle Speicheranforderungen an den temporären Datenträgerspeicher erfüllen. Das Verhältnis ist ein Kompromiss zwischen der Bereitstellung von temporärem Speicher, ohne einen Großteil der Speicherkapazität der Lösung nur für temporäre Datenträgerkapazität zu nutzen. Pro Server wird in der Skalierungseinheit ein temporärer Speicherdatenträger erstellt. Die Kapazität des temporären Speichers steigt nicht über 10 % der gesamten verfügbaren Speicherkapazität im Speicherpool der Skalierungseinheit an. Die Berechnung erfolgt ähnlich wie im folgenden Beispiel:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> Die virtuellen Datenträger, die für die Verwendung durch ACS erstellt wurden, stellen eine einfache Division der verbleibenden Kapazität dar. Wie bereits erwähnt, sind alle virtuellen Datenträger ein Drei-Wege-Spiegel, und die Kapazität eines Kapazitätslaufwerks für jeden Server ist nicht zugeordnet. Die verschiedenen, zuvor aufgelisteten virtuellen Datenträger werden zunächst zugeordnet, und die verbleibende Kapazität wird dann für die virtuellen ACS-Datenträger verwendet.


## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über den [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md).
