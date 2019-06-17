---
title: 'Azure Stack-Kapazitätsplanung: Storage | Microsoft-Dokumentation'
description: Informationen zur Kapazitätsplanung für Azure Stack-Bereitstellungen.
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
ms.openlocfilehash: 30ce69f96747ab8dbdafd9e20e8cea07026074d5
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461089"
---
# <a name="azure-stack-storage"></a>Azure Stack-Speicher

Die folgenden Abschnitte enthalten Informationen zur Planung der Speicherkapazität von Azure Stack, die bei der Planung des Speicherbedarfs der Lösung helfen.

## <a name="uses-and-organization-of-storage-capacity"></a>Verwendungszwecke und Organisation der Speicherkapazität
Die hyperkonvergierte Konfiguration von Azure Stack ermöglicht die gemeinsame Nutzung physischer Speichergeräte. Die drei Hauptbereiche des verfügbaren Speichers beziehen sich auf die Infrastruktur, die temporäre Speicherung der virtuellen Mandantencomputer und den Speicher, der die Blobs, Tabellen und Warteschlangen der ACS-Dienste (Azure Consistent Storage) sichert.

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Cache von „Direkte Speicherplätze“ und Kapazitätsstufen
Es gibt Speicherkapazität, die für das Betriebssystem, lokale Protokollierung, Speicherabbilder und andere temporäre Infrastrukturspeicheranforderungen verwendet wird. Diese lokale Speicherkapazität ist getrennt (Geräte und Kapazität) von den Speichergeräten, die unter der Verwaltung der Konfiguration „Direkte Speicherplätze“ stehen. Der Rest der Speichergeräte wird in einem einzigen Pool von Speicherkapazität platziert, und zwar unabhängig von der Anzahl der Server in der Skalierungseinheit. Für diese Geräte werden zwei Typen unterschieden: Cache und Kapazität.  Die Cachegeräte sind einfach nur Caches. „Direkte Speicherplätze“ nutzt diese Geräte zum Zwischenspeichern von Rückschreib- und Lesevorgängen. Die Kapazitäten dieser Cachegeräte sind während ihrer Verwendung nicht auf die formatierte, „sichtbare“ Kapazität der formatierten virtuellen Datenträger festgelegt. Die Kapazitätsgeräte werden zu diesem Zweck verwendet und stellen den „Heimatspeicherort“ der von „Direkte Speicherplätze“ verwalteten Daten dar.

Die gesamte Speicherkapazität wird direkt von der Azure Stack-Infrastruktur zugeordnet und verwaltet. Der Operator muss Entscheidungen über Konfiguration und Zuordnung treffen bzw. sich mit Optionen auseinandersetzen, wenn es um Kapazitätserweiterungen geht. Diese Entwurfsentscheidungen wurden in Übereinstimmung mit den Lösungsanforderungen getroffen und werden entweder bei der Erstinstallation bzw. -bereitstellung oder bei der Kapazitätserweiterung automatisiert. Details zur Resilienz, zur reservierten Kapazität für Neuerstellungen und andere Details wurden als Teil des Entwurfs berücksichtigt. 

Operatoren können zwischen einer Nur-Flash- und einer Hybridspeicherkonfiguration wählen:

![Planen der Azure-Speicherkapazität](media/azure-stack-capacity-planning/storage.png)

In der Nur-Flash-Konfiguration kann eine Konfiguration mit zwei Ebenen oder eine Konfiguration mit einer Ebene verwendet werden.  Wird eine Konfiguration mit einer Ebene verwendet, weisen alle Kapazitätsgeräte den gleichen Typ (z. B. NVMe, SATA-SSD oder SAS-SSD) auf, und es werden keine Cachegeräte verwendet. In einer Nur-Flash-Konfiguration mit zwei Ebenen werden in der Regel als Cachegeräte NVMe und als Kapazitätsgeräte SATA- oder SAS-SSDs verwendet.

In der Hybridkonfiguration mit zwei Ebenen ist der Cache wahlweise NVMe oder eine SATA- bzw. SAS-SSD, und für die Kapazität wird HDD verwendet. 

Die Speicherkonfiguration von „Direkte Speicherplätze“ und Azure Stack lässt sich wie folgt kurz zusammenfassen:
- Ein Speicherplatzpool pro Skalierungseinheit (alle Speichergeräte werden innerhalb eines einzigen Pools konfiguriert).
- Virtuelle Datenträger werden als Spiegel mit drei Kopien für die beste Leistung und Resilienz erstellt.
- Jeder virtuelle Datenträger wird als ein ReFS-Dateisystem formatiert.
- Die Kapazität des virtuellen Datenträgers wird so berechnet und zugeordnet, dass die Datenkapazität eines Kapazitätsgeräts im Pool nicht zugeordnet wird. Dies entspricht einem Kapazitätslaufwerk pro Server.
- Für jedes ReFS-Dateisystem wird BitLocker für die Verschlüsselung ruhender Daten aktiviert. 

Die automatisch erstellten virtuellen Datenträger und die zugehörigen Kapazitäten stellen sich folgendermaßen dar:

|NAME|Kapazitätsberechnung|BESCHREIBUNG|
|-----|-----|-----|
|Lokales/Startgerät|Mindestens 340 GB<sup>1</sup>|Einzelner Serverspeicher für Betriebssystemimages und „lokale“ Infrastruktur-VMs|
|Infrastruktur|3,5 TB|Gesamte Azure Stack-Infrastrukturnutzung|
|VmTemp|Siehe unten<sup>2</sup>|An Mandanten-VMs ist ein temporärer Datenträger angefügt, und diese Daten werden auf diesen virtuellen Datenträgern gespeichert.|
|ACS|Siehe unten <sup>3</sup>|Azure Consistent Storage-Kapazität für Blobs, Tabellen und Warteschlangen|

<sup>1</sup> Minimale Speicherkapazität, die für Azure Stack-Lösungspartner erforderlich ist.

<sup>2</sup> Die für temporäre Datenträger von Tenant Virtual Machine verwendete Größe des virtuellen Datenträgers wird als Verhältnis des physischen Speichers des Servers berechnet. Wie in den folgenden Tabellen für die Größen von Azure-IaaS-VMs angegeben, ist der temporäre Datenträger ein Verhältnis des dem virtuellen Computer zugewiesenen physischen Speichers. Die Zuordnung für den „temporären Datenträger“speicher in Azure Stack erfolgt so, dass die meisten Anwendungsfälle erfasst werden, aber möglicherweise nicht alle Speicheranforderungen an den temporären Datenträgerspeicher erfüllt werden können. Das gewählte Verhältnis ist ein Kompromiss zwischen der Bereitstellung von temporärem Speicher, ohne einen Großteil der Speicherkapazität der Lösung nur für temporäre Datenträgerkapazität zu nutzen. Pro Server wird in der Skalierungseinheit ein temporärer Speicherdatenträger erstellt. Die Kapazität des temporären Speichers wird nicht über 10 % der gesamten verfügbaren Speicherkapazität im Speicherpool der Skalierungseinheit ansteigen. Die Berechnung erfolgt ähnlich wie im folgenden Beispiel:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> Die virtuellen Datenträger, die für die Verwendung durch ACS erstellt wurden, stellen eine einfache Division der verbleibenden Kapazität dar. Wie bereits erwähnt, sind alle virtuellen Datenträger ein Drei-Wege-Spiegel, und die Kapazität eines Kapazitätslaufwerks für jeden Server ist nicht zugeordnet. Die verschiedenen oben aufgelisteten virtuellen Datenträger werden zunächst zugeordnet, und die verbleibende Kapazität wird dann für die virtuellen ACS-Datenträger verwendet.


## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über den [Azure Stack Capacity Planner](azure-stack-capacity-planner.md).
