---
title: Computekapazitätsplanung für Azure Stack | Microsoft Docs
description: Informationen zur Computekapazitätsplanung für Azure Stack-Bereitstellungen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: 907a10caa2a23b58d0c63ee1abb7f4e4a074d1a1
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618913"
---
# <a name="azure-stack-compute-capacity-planning"></a>Planen der Azure Stack-Computekapazität
Die [für Azure Stack unterstützten VM-Größen](../user/azure-stack-vm-sizes.md) sind eine Teilmenge der in Azure unterstützten VM-Größen. Azure erzwingt Ressourcengrenzwerte auf verschiedene Arten, um einen übermäßigen Ressourcenverbrauch (auf dem lokalen Server und auf der Dienstebene) zu vermeiden. Wenn keine Einschränkungen für die Nutzung durch Mandanten gelten würden, würde die Mandantenerfahrung beeinträchtigt, wenn andere Mandanten Ressourcen übermäßig nutzen. Für ausgehenden Netzwerkdatenverkehr des virtuellen Computers gelten Bandbreitenobergrenzen für Azure Stack, die mit den Azure-Einschränkungen übereinstimmen. Für Speicherressourcen wurden für Azure Stack Speicher-IOPs-Grenzwerte implementiert, um den allgemeinen übermäßigen Ressourcenverbrauch durch Mandanten für Speicherzugriff zu vermeiden.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>VM-Platzierung und Überbereitstellung von physischen im Vergleich zu virtuellen Kernen
In Azure Stack gibt es keine Möglichkeit für einen Mandanten, einen bestimmten Server anzugeben, der für die VM-Platzierung verwendet wird. Die einzige Überlegung bei der Platzierung von virtuellen Computern bezieht sich darauf, ob genügend Arbeitsspeicher auf dem Host für diesen VM-Typ vorhanden ist. Azure Stack führt keine Mehrfachvergabe von Speicher aus, jedoch ist die Mehrfachvergabe der Anzahl der Kerne zulässig. Da Platzierungsalgorithmen das vorhandene Verhältnis der Überbereitstellung von virtuellen im Vergleich zu physischen Kernen nicht als Faktor berücksichtigen, kann jeder Host ein anderes Verhältnis aufweisen. 

Zur Erreichung von Hochverfügbarkeit für ein Produktionssystem mit mehreren virtuellen Computern in Azure werden die virtuellen Computer in einer Verfügbarkeitsgruppe angeordnet, um sie auf mehrere Fehlerdomänen zu verteilen. In Azure Stack ist eine Fehlerdomäne in einer Verfügbarkeitsgruppe als einzelner Knoten in der Skalierungseinheit definiert.

Die Infrastruktur von Azure Stack verfügt zwar über Resilienz gegenüber Fehlern, aber bei einem Hardwarefehler kommt es bei der zugrunde liegenden Technologie (Failoverclustering) für virtuelle Computer auf einem betroffenen physischen Server trotzdem noch zu Ausfallzeiten. Azure Stack unterstützt zurzeit die Verwendung einer Verfügbarkeitsgruppe mit maximal drei Fehlerdomänen, um Konsistenz mit Azure zu erzielen. In einer Verfügbarkeitsgruppe angeordnete VMs werden physisch voneinander isoliert, indem sie so gleichmäßig wie möglich auf mehrere Fehlerdomänen (Azure Stack-Knoten) verteilt werden. Bei einem Hardwarefehler werden virtuelle Computer aus der betroffenen Fehlerdomäne in anderen Knoten neu gestartet. Dies sind nach Möglichkeit aber Fehlerdomänen, die von den anderen virtuellen Computern in derselben Verfügbarkeitsgruppe getrennt sind. Nachdem die Hardware wieder in den Onlinezustand versetzt wurde, wird für die VMs ein neuer Ausgleichsvorgang durchgeführt, um die Hochverfügbarkeit sicherzustellen.

Ein weiteres Konzept, das von Azure verwendet wird, um Hochverfügbarkeit bereitzustellen, sind Updatedomänen in Verfügbarkeitsgruppen. Eine Updatedomäne ist eine logische Gruppe von zugrunde liegender Hardware, die zur gleichen Zeit gewartet oder neu gestartet werden kann. In Azure Stack wird für VMs eine Livemigration über die anderen Onlinehosts im Cluster durchgeführt, bevor der zugrunde liegende Host aktualisiert wird. Da es während eines Hostupdates nicht zu Mandantenausfallzeiten kommt, ist das Updatedomänenfeature in Azure Stack nur für die Vorlagenkompatibilität mit Azure vorhanden.

## <a name="azure-stack-resiliency-resources"></a>Azure Stack-Resilienzressourcen
Damit Patch und Update für in Azure Stack integrierte Systeme möglich ist und Resilienz vor physischen Hardwareausfällen erreicht wird, wird ein Teil des Gesamtserverspeichers reserviert und steht für die Platzierung virtueller Mandantencomputer (VMs) nicht zur Verfügung.

Wenn ein Server ausfällt, werden auf dem fehlerhaften Server gehostete virtuelle Computer auf den verbleibenden verfügbaren Servern neu gestartet, um VM-Verfügbarkeit bereitzustellen. Auf ähnliche Weise werden während des Patch- und Updatevorgangs alle virtuellen Computer, die auf einem Server ausgeführt werden, live auf einen anderen verfügbaren Server migriert. Diese VM-Verwaltung oder -Verschiebung kann nur erreicht werden, wenn reservierte Kapazität vorhanden ist, damit der Neustart bzw. die Migration auftreten kann.

Die folgende Berechnung ergibt den insgesamt verfügbaren Arbeitsspeicher, der für Mandanten-VM-Platzierung verwendet werden kann. Diese Speicherkapazität gilt für die Gesamtheit der Azure Stack-Skalierungseinheit.

  Verfügbarer Speicher für VM-Platzierung = Serverspeicher gesamt – Resilienzreserve – von ausgeführten VMs verwendeter Arbeitsspeicher – Azure Stack-Infrastrukturmehraufwand <sup>1</sup>

  Resilienzreserve = H + R * ((N-1) * H) + V * (N-2)

> Hinweis:
> - H = Größe des einzelnen Serverspeichers
> - N = Größe der Skalierungseinheit (Anzahl der Server)
> - R = Betriebssystemreserve für Betriebssystemmehraufwand<sup>2</sup>
> - V = Größter virtueller Computer in der Skalierungseinheit

  <sup>1</sup> Azure Stack-Infrastrukturmehraufwand = 230 GB

  <sup>2</sup> Betriebssystemreserve für Mehraufwand = 15 % des Knotenspeichers. Der Wert der Betriebssystemreserve ist eine Schätzung und variiert je nach der physischen Speicherkapazität des Servers und dem allgemeinen Betriebssystemmehraufwand.

Der Wert V (größter virtueller Computer in der Skalierungseinheit) basiert dynamisch auf der VM-Speichergröße des größten Mandanten. Der größte Wert für den virtuellen Computer kann beispielsweise 7 GB oder 112 GB oder jede andere unterstützte VM-Speichergröße in der Azure Stack-Lösung sein.

Die obige Berechnung ist eine Schätzung und kann sich auf der Grundlage der aktuellen Version von Azure Stack ändern. Die Möglichkeit, Mandanten-VMs und -Dienste bereitzustellen, basiert auf den Besonderheiten der bereitgestellten Lösung. Diese Beispielberechnung stellt nur einen Leitfaden dar und ist nicht die endgültige Antworten auf die Möglichkeit, virtuelle Computer bereitzustellen.



## <a name="next-steps"></a>Nächste Schritte
[Planen der Speicherkapazität](capacity-planning-storage.md)
