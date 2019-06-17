---
title: 'Azure Stack-Kapazitätsplanung: Compute | Microsoft-Dokumentation'
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
ms.openlocfilehash: e549413798ffc3c06c95bfbcf50ab4929ffeaf63
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461095"
---
# <a name="azure-stack-compute"></a>Azure Stack-Compute

Die für Azure Stack unterstützten [VM-Größen](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) stellen eine Teilmenge der in Azure unterstützten VM-Größen dar. Azure erzwingt Ressourcengrenzwerte auf verschiedene Arten, um einen übermäßigen Ressourcenverbrauch (auf dem lokalen Server und auf der Dienstebene) zu vermeiden. Wenn keine Einschränkungen für die Nutzung durch Mandanten gelten würden, würde die Mandantenerfahrung beeinträchtigt, wenn andere Mandanten Ressourcen übermäßig nutzen. Für ausgehenden Netzwerkdatenverkehr des virtuellen Computers gelten Bandbreitenobergrenzen für Azure Stack, die mit den Azure-Einschränkungen übereinstimmen. Für Speicherressourcen wurden für Azure Stack Speicher-IOPS-Grenzwerte implementiert, um den allgemeinen übermäßigen Ressourcenverbrauch durch Mandanten für Speicherzugriff zu vermeiden.

>[!IMPORTANT]
>Mit dem [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) wird die IOPS-Leistung nicht berücksichtigt oder garantiert.

## <a name="vm-placement"></a>VM-Platzierung

In Azure Stack wird die Platzierung von virtuellen Mandantencomputern für die verfügbaren Hosts automatisch vom Platzierungsmodul durchgeführt. Die einzigen beiden Aspekte, die beim Platzieren von VMs berücksichtigt werden müssen, sind: Ist auf dem Host für diesen VM-Typ genügend Arbeitsspeicher vorhanden, und sind die VMs Teil einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability), oder handelt es sich um [VM-Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)?  

Zur Erreichung von Hochverfügbarkeit für ein Produktionssystem mit mehreren VMs in Azure Stack werden die VMs in einer Verfügbarkeitsgruppe angeordnet, um sie auf mehrere Fehlerdomänen zu verteilen. Die Definition einer Fehlerdomäne in einer Verfügbarkeitsgruppe ist ein einzelner Knoten in der Skalierungseinheit. Azure Stack unterstützt die Verwendung einer Verfügbarkeitsgruppe mit maximal drei Fehlerdomänen, um Konsistenz mit Azure zu erzielen. In einer Verfügbarkeitsgruppe angeordnete VMs werden physisch voneinander isoliert, indem sie so gleichmäßig wie möglich auf mehrere Fehlerdomänen, also Azure Stack-Hosts, verteilt werden. Bei einem Hardwarefehler werden VMs aus der betroffenen Fehlerdomäne in anderen Fehlerdomänen neu gestartet. Dies sind nach Möglichkeit aber Fehlerdomänen, die von den anderen VMs in derselben Verfügbarkeitsgruppe getrennt sind. Nachdem der Host wieder in den Onlinezustand versetzt wurde, wird für die VMs ein neuer Ausgleichsvorgang durchgeführt, um die Hochverfügbarkeit sicherzustellen.  

Für VM-Skalierungsgruppen werden Verfügbarkeitsgruppen auf dem Back-End verwendet, und es wird sichergestellt, dass jede Instanz der VM-Skalierungsgruppe in einer anderen Fehlerdomäne platziert wird. Dies bedeutet, dass hierfür separate Azure Stack-Infrastrukturknoten verwendet werden. Bei einem Azure Stack-System mit vier Knoten kann es beispielsweise zu einer Situation kommen, in der für eine VM-Skalierungsgruppe mit drei Instanzen bei der Erstellung ein Fehler auftritt, weil bei einer Kapazität von vier Knoten nicht drei VM-Skalierungsgruppen-Instanzen auf drei separaten Azure Stack-Knoten platziert werden können. Außerdem können Azure Stack-Knoten vor der Durchführung der Platzierung auf verschiedenen Ebenen gefüllt werden. 

In Azure Stack wird nicht mehr Arbeitsspeicher als nötig zugewiesen. Bei der Anzahl von physischen Kernen ist dies aber zulässig. Da Platzierungsalgorithmen das vorhandene Verhältnis der Überbereitstellung von virtuellen Kernen im Vergleich zu physischen Kernen nicht als Faktor berücksichtigen, kann jeder Host ein anderes Verhältnis aufweisen. Microsoft gibt aufgrund der unterschiedlichen Anforderungen in Bezug auf Workloads und Servicelevels keine Empfehlung zum Verhältnis von physischen und virtuellen Kernen ab. 

## <a name="azure-stack-memory"></a>Azure Stack-Arbeitsspeicher 

Azure Stack ist so konzipiert, dass erfolgreich bereitgestellte VMs weiter ausgeführt werden. Wenn ein Host beispielsweise aufgrund eines Hardwarefehlers offline ist, versucht Azure Stack, die VM auf einem anderen Host neu zu starten. Im zweiten Beispiel wird das Patchen und Aktualisieren der Azure Stack-Software durchgeführt. Falls ein physischer Host neu gestartet werden muss, wird versucht, die auf diesem Host ausgeführten VMs auf einen anderen verfügbaren Host der Lösung zu verschieben.   

Diese VM-Verwaltung oder -Verschiebung kann nur erreicht werden, wenn reservierte Speicherkapazität vorhanden ist, damit der Neustart bzw. die Migration durchgeführt werden kann. Ein Teil des gesamten Hostarbeitsspeichers ist reserviert und steht nicht für die Platzierung von virtuellen Mandantencomputern zur Verfügung. 

Sie können im Verwaltungsportal ein Kreisdiagramm anzeigen, mit dem der freie und belegte Arbeitsspeicher in Azure Stack angezeigt wird. Im folgenden Diagramm ist die Kapazität des physischen Arbeitsspeichers in einer Azure Stack-Skalierungseinheit in Azure Stack dargestellt:

![Kapazität des physischen Arbeitsspeichers](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Der verwendete Arbeitsspeicher besteht aus mehreren Komponenten. Die folgenden Komponenten verbrauchen den Arbeitsspeicher, der im Kreisdiagramm im entsprechenden Abschnitt angegeben ist.  

- Nutzung des Hostbetriebssystems bzw. Reserve: Dies ist der Arbeitsspeicher, der vom Betriebssystem auf dem Host, von Auslagerungstabellen des virtuellen Arbeitsspeichers, unter dem Hostbetriebssystem ausgeführten Prozessen und dem Arbeitsspeichercache von „Direkte Speicherplätze“ genutzt wird. 
- Infrastrukturdienste: Dies sind die Infrastruktur-VMs, aus denen Azure Stack besteht. Ab Version 1902 von Azure Stack sind dies 31 VMs mit 242 GB + (4 GB · Anz. von Knoten). Diese interne Dienststruktur ermöglicht die zukünftige Einführung neuer Infrastrukturdienste im Rahmen ihrer Entwicklung.
- Resilienzreserve: Azure Stack reserviert einen Teil des Arbeitsspeichers, um die Mandantenverfügbarkeit während des Ausfalls eines einzelnen Hosts und beim Patchen und Aktualisieren sicherzustellen und so die erfolgreiche Livemigration von VMs zu ermöglichen. 
- Mandanten-VMs: Dies sind die virtuellen Mandantencomputer, die von Azure Stack-Benutzern erstellt werden. Zusätzlich zur Ausführung von VMs wird Arbeitsspeicher von allen VMs verbraucht, die im Fabric angeordnet sind. Dies bedeutet, dass VMs mit dem Status **Wird erstellt** oder **Fehler** oder über den Gast heruntergefahrene VMs Arbeitsspeicher verbrauchen. VMs, die beendet wurden und deren Zuordnung mit der entsprechenden Option aufgehoben wurde, verbrauchen aber keinen Arbeitsspeicher von Azure Stack mehr. 

Die beste Möglichkeit, den Arbeitsspeicherverbrauch im Portal zu verstehen, ist die Nutzung des [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner). Hiermit können Sie die Auswirkungen der unterschiedlichen Workloads verfolgen. Die folgende Berechnung wird auch vom Planner verwendet.

Diese Berechnung ergibt den insgesamt verfügbaren Arbeitsspeicher, der für die Platzierung von virtuellen Mandantencomputern verwendet werden kann. Diese Speicherkapazität gilt für die gesamte Azure Stack-Skalierungseinheit. 


  Verfügbarer Speicher für VM-Platzierung = Hostspeicher gesamt - Resilienzreserve - Von ausgeführten Mandanten-VMs verwendeter Arbeitsspeicher - Azure Stack-Infrastrukturmehraufwand <sup>1</sup>

  Resilienzreserve = H + R * ((N-1) * H) + V * (N-2)

> Hinweis:
> - H = Größe des einzelnen Serverspeichers
> - N = Größe der Skalierungseinheit (Anzahl der Server)
> - R = Betriebssystemreserve für Mehraufwand des Betriebssystems (in dieser Formel 0,15)<sup>2</sup>
> - V = Größter virtueller Computer in der Skalierungseinheit

  <sup>1</sup> Azure Stack-Infrastrukturmehraufwand = 242 GB + (4 GB · Anz. von Knoten). Rund 31 virtuelle Computer werden für das Hosting der Infrastruktur von Azure Stack verwendet und nutzen insgesamt etwa Speicher in Höhe von 242 GB + (4 GB · Anz. von Knoten) und 146 virtuelle Kerne. Der Grund für diese Anzahl von virtuellen Computern besteht darin, die erforderliche Diensttrennung zu erfüllen, um die Anforderungen an Sicherheit, Skalierbarkeit, Wartung und Patches zu gewährleisten. Diese interne Dienststruktur ermöglicht die zukünftige Einführung neuer Infrastrukturdienste im Rahmen ihrer Entwicklung. 

  <sup>2</sup> Betriebssystemreserve für Mehraufwand = 15 % (0,15) des Knotenspeichers. Der Wert der Betriebssystemreserve ist eine Schätzung und variiert je nach der physischen Speicherkapazität des Servers und dem allgemeinen Betriebssystemmehraufwand.


Der Wert V (größter virtueller Computer in der Skalierungseinheit) basiert dynamisch auf der VM-Speichergröße des größten Mandanten. Der größte Wert für den virtuellen Computer kann beispielsweise 7 GB oder 112 GB oder jede andere unterstützte VM-Speichergröße in der Azure Stack-Lösung sein. Wenn die größte VM im Azure Stack-Fabric geändert wird, führt dies zu einer Erhöhung der Resilienzreserve und auch zu einer Erhöhung des Arbeitsspeichers der VM selbst. 

> [!NOTE]
> Die Kapazitätsplanungsanforderungen für Netzwerke sind minimal, da nur die Größe der öffentlichen virtuellen IP-Adresse (VIP) konfigurierbar ist. Informationen zur Vorgehensweise beim Hinzufügen weiterer öffentlicher IP-Adressen zu Azure Stack finden Sie unter [Hinzufügen öffentlicher IP-Adressen](azure-stack-add-ips.md).

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über den [Azure Stack-Speicher](azure-stack-capacity-planning-storage.md).