---
title: Azure Stack Hub-Computekapazität
description: Hier erfahren Sie mehr über die Computekapazitätsplanung für Azure Stack Hub-Bereitstellungen.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: dd82a3a9c2b6eff74c8f6823fc37b8767431ca02
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489231"
---
# <a name="azure-stack-hub-compute-capacity"></a>Azure Stack Hub-Computekapazität

Die für Azure Stack Hub unterstützten [VM-Größen (virtuelle Computer)](../user/azure-stack-vm-sizes.md) stellen eine Teilmenge der in Azure unterstützten VM-Größen dar. Azure erzwingt Ressourcengrenzwerte auf verschiedene Arten, um einen übermäßigen Ressourcenverbrauch (auf dem lokalen Server und auf der Dienstebene) zu vermeiden. Wenn keine Einschränkungen für die Nutzung durch Mandanten gelten würden, würde die Mandantenerfahrung beeinträchtigt, wenn andere Mandanten Ressourcen übermäßig nutzen. Für ausgehenden Netzwerkdatenverkehr des virtuellen Computers gelten Bandbreitenobergrenzen für Azure Stack Hub, die mit den Azure-Einschränkungen übereinstimmen. Für Speicherressourcen in Azure Stack Hub werden Speicher-IOPS-Grenzwerte verwendet, um beim Speicherzugriff grundsätzlich einen übermäßigen Ressourcenverbrauch durch Mandanten zu vermeiden.

>[!IMPORTANT]
>Mit dem [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) wird die IOPS-Leistung nicht berücksichtigt oder garantiert.

## <a name="vm-placement"></a>VM-Platzierung

Die Azure Stack Hub-Platzierungsengine verteilt Mandanten-VMs auf die verfügbaren Hosts.

Bei der Platzierung virtueller Computer werden von Azure Stack Hub zwei Aspekte berücksichtigt. Erstens: Steht auf dem Host genügend Arbeitsspeicher für den VM-Typ zur Verfügung? Zweitens: Gehören die VMs zu einer [Verfügbarkeitsgruppe](/azure/virtual-machines/windows/manage-availability), oder handelt es sich bei Ihnen um [VM-Skalierungsgruppen](/azure/virtual-machine-scale-sets/overview)?

Zur Erreichung von Hochverfügbarkeit für ein Produktionssystem mit mehreren virtuellen Computern (VMs) in Azure Stack Hub werden die VMs in einer Verfügbarkeitsgruppe platziert, um sie auf mehrere Fehlerdomänen zu verteilen. Die Definition einer Fehlerdomäne in einer Verfügbarkeitsgruppe ist ein einzelner Knoten in der Skalierungseinheit. Azure Stack Hub unterstützt die Verwendung einer Verfügbarkeitsgruppe mit maximal drei Fehlerdomänen, um Konsistenz mit Azure zu erzielen. In einer Verfügbarkeitsgruppe angeordnete VMs werden physisch voneinander isoliert, indem sie so gleichmäßig wie möglich auf mehrere Fehlerdomänen (Azure Stack Hub-Hosts) verteilt werden. Bei einem Hardwarefehler werden VMs aus der fehlerhaften Fehlerdomäne in anderen Fehlerdomänen neu gestartet. Sie werden nach Möglichkeit getrennt von den anderen VMs in separaten Fehlerdomänen in derselben Verfügbarkeitsgruppe gespeichert. Nachdem der Host wieder in den Onlinezustand versetzt wurde, wird für die VMs ein neuer Ausgleichsvorgang durchgeführt, um die Hochverfügbarkeit sicherzustellen.  

Für VM-Skalierungsgruppen werden Verfügbarkeitsgruppen auf dem Back-End verwendet, und es wird sichergestellt, dass jede Instanz der VM-Skalierungsgruppe in einer anderen Fehlerdomäne platziert wird. Dies bedeutet, dass hierfür separate Azure Stack Hub-Infrastrukturknoten verwendet werden. Bei einem Azure Stack Hub-System mit vier Knoten kann es beispielsweise zu einer Situation kommen, in der für eine VM-Skalierungsgruppe mit drei Instanzen bei der Erstellung ein Fehler auftritt, weil bei einer Kapazität von vier Knoten nicht drei VM-Skalierungsgruppeninstanzen auf drei separaten Azure Stack Hub-Knoten platziert werden können. Außerdem können Azure Stack Hub-Knoten vor der Durchführung der Platzierung auf verschiedenen Ebenen gefüllt werden.

In Azure Stack Hub wird nicht mehr Arbeitsspeicher als nötig zugewiesen. Bei der Anzahl von physischen Kernen ist dies aber zulässig.

Da Platzierungsalgorithmen das vorhandene Verhältnis der Überbereitstellung von virtuellen Kernen im Vergleich zu physischen Kernen nicht als Faktor berücksichtigen, kann jeder Host ein anderes Verhältnis aufweisen. Microsoft gibt aufgrund der unterschiedlichen Anforderungen in Bezug auf Workloads und Servicelevel keine Empfehlung zum Verhältnis von physischen und virtuellen Kernen ab.

## <a name="consideration-for-total-number-of-vms"></a>Überlegung zur Gesamtanzahl von VMs

Es gibt eine neue Überlegung für die genaue Planung von Azure Stack Hub-Kapazität. Mit dem Update 1901 (und jedem künftigen Update) gibt es jetzt ein Limit für die Gesamtanzahl von VMs, die erstellt werden können. Dieses Limit soll temporär sein, um Lösungsinstabilitäten zu vermeiden. Die Ursache des Stabilitätsproblems bei einer höheren Anzahl von VMs wird untersucht, aber es steht kein genauer Zeitplan für die Korrektur fest. Die Anzahl ist nun auf 60 VMs pro Server und auf 700 VMs pro Gesamtlösung begrenzt. Beispielsweise wäre ein Azure Stack Hub-VM-Limit bei 480 Servern 8 (8 * 60). Bei einer Azure Stack Hub-Lösung mit 12 bis 16 Servern läge das Limit bei 700. Dieses Limit wurde unter Berücksichtigung aller Überlegungen hinsichtlich der Computekapazität eingerichtet, z. B. der Resilienzreserve und des CPU-Verhältnisses zwischen virtuell und physisch, die ein Operator bei dem Stamp beibehalten möchte. Weitere Informationen finden Sie in der neuen Version von Capacity Planner.

Falls das VM-Skalierungslimit erreicht wird, werden die folgenden Fehlercodes als Ergebnis zurückgegeben: `VMsPerScaleUnitLimitExceeded` und `VMsPerScaleUnitNodeLimitExceeded`.

## <a name="considerations-for-deallocation"></a>Überlegungen zur Aufhebung der Zuweisung

Wenn sich eine VM im Status _Zuweisung aufgehoben_ befindet, werden keine Speicherressourcen verwendet. Dadurch können andere VMs im System platziert werden.

Wenn die nicht zugewiesene VM dann noch mal gestartet wird, erfolgt die Speicherauslastung oder -belegung wie beim Platzieren einer neuen VM im System, und der verfügbare Speicherplatz wird verbraucht.

Wenn kein Speicherplatz verfügbar ist, wird die VM nicht gestartet.

## <a name="azure-stack-hub-memory"></a>Azure Stack Hub-Arbeitsspeicher

Azure Stack Hub ist so konzipiert, dass erfolgreich bereitgestellte VMs weiter ausgeführt werden. Wenn ein Host beispielsweise aufgrund eines Hardwarefehlers offline ist, versucht Azure Stack Hub, die VM auf einem anderen Host neu zu starten. Im zweiten Beispiel wird das Patchen und Aktualisieren der Azure Stack Hub-Software durchgeführt. Falls ein physischer Host neu gestartet werden muss, wird versucht, die auf diesem Host ausgeführten VMs auf einen anderen verfügbaren Host der Lösung zu verschieben.

Diese VM-Verwaltung oder -Verschiebung kann nur erreicht werden, wenn reservierte Speicherkapazität vorhanden ist, damit der Neustart bzw. die Migration durchgeführt werden kann. Ein Teil des gesamten Hostarbeitsspeichers ist reserviert und steht nicht für die Platzierung von virtuellen Mandantencomputern zur Verfügung.

Sie können im Administratorportal ein Kreisdiagramm anzeigen, mit dem der freie und belegte Arbeitsspeicher in Azure Stack Hub angezeigt wird. Im folgenden Diagramm ist die Kapazität des physischen Arbeitsspeichers in einer Azure Stack Hub-Skalierungseinheit in Azure Stack Hub dargestellt:

![Kapazität des physischen Speichers in einer Azure Stack Hub-Skalierungseinheit](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Der verwendete Arbeitsspeicher besteht aus mehreren Komponenten. Die folgenden Komponenten beanspruchen den Arbeitsspeicher, der im entsprechenden Abschnitt des Kreisdiagramms angegeben ist:  

- **Nutzung des Hostbetriebssystems bzw. Reserve:** Dies ist der Arbeitsspeicher, der vom Betriebssystem auf dem Host, von Auslagerungstabellen des virtuellen Arbeitsspeichers, unter dem Hostbetriebssystem ausgeführten Prozessen und dem Arbeitsspeichercache von „Direkte Speicherplätze“ genutzt wird. Dieser Wert kann schwanken, da er von dem Arbeitsspeicher abhängt, der von den verschiedenen, auf dem Host ausgeführten Hyper-V-Prozessen beansprucht wird.
- **Infrastrukturdienste:** Dies sind die Infrastruktur-VMs, aus denen Azure Stack Hub besteht. Ab der Releaseversion 1904 von Azure Stack Hub sind dies ~31 virtuelle Computer, die 242 GB (+ 4 GB pro Knoten) beanspruchen. Wir arbeiten weiter an der Verbesserung der Skalierbarkeit und Resilienz unserer Infrastrukturdienste, weshalb sich die Arbeitsspeicherverwendung dieser Komponente noch ändern kann.
- **Resilienzreserve:** Azure Stack Hub reserviert einen Teil des Arbeitsspeichers, um die Mandantenverfügbarkeit während des Ausfalls eines einzelnen Hosts und beim Patchen und Aktualisieren sicherzustellen und so die erfolgreiche Livemigration von VMs zu ermöglichen.
- **Mandanten-VMs:** Dies sind die virtuellen Mandanten-VMs, die von Azure Stack Hub-Benutzern erstellt werden. Zusätzlich zur Ausführung von VMs wird Arbeitsspeicher von allen VMs verbraucht, die im Fabric angeordnet sind. Das bedeutet, dass virtuelle Computer mit dem Zustand „Wird erstellt“ oder „Fehler“ sowie virtuelle Computer, die über den Gast heruntergefahren werden, Arbeitsspeicher beanspruchen. VMs, deren Zuordnung mithilfe der entsprechenden Option über das Portal/mit PowerShell/über die Befehlszeilenschnittstelle aufgehoben wurde, beanspruchen allerdings keinen Arbeitsspeicher von Azure Stack Hub mehr.
- **Ressourcenanbieter (RPs), die Mehrwert schaffen:** Dies sind VMs, die für die Ressourcenanbieter bereitgestellt wurden, die Mehrwert schaffen (etwa SQL, MySQL, App Service usw).

Die beste Möglichkeit, den Arbeitsspeicherverbrauch im Portal zu verstehen, ist die Nutzung des [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner). Hiermit können Sie die Auswirkungen der unterschiedlichen Workloads verfolgen. Die folgende Berechnung wird auch vom Planner verwendet.

Diese Berechnung ergibt den insgesamt verfügbaren Arbeitsspeicher, der für die Platzierung von virtuellen Mandantencomputern verwendet werden kann. Diese Speicherkapazität gilt für die gesamte Azure Stack Hub-Skalierungseinheit.

Verfügbarer Speicher für VM-Platzierung = Hostspeicher gesamt - Resilienzreserve - von ausgeführten Mandanten-VMs verwendeter Arbeitsspeicher - Azure Stack Hub-Infrastrukturmehraufwand <sup>1</sup>

Resilienzreserve = H + R * ((N-1) * H) + V * (N-2)

> Hierbei gilt:
> -    H = Größe des einzelnen Serverspeichers
> - N = Größe der Skalierungseinheit (Anzahl der Server)
> -    R = Betriebssystemreserve für Mehraufwand des Betriebssystems (in dieser Formel 0,15)<sup>2</sup>
> -    V = Größter virtueller Computer in der Skalierungseinheit

<sup>1</sup> Azure Stack Hub-Infrastrukturmehraufwand = 242 GB + (4 GB * Anzahl von Knoten). Rund 31 virtuelle Computer werden für das Hosting der Infrastruktur von Azure Stack Hub verwendet und nutzen insgesamt etwa Speicher in Höhe von 242 GB + (4 GB x Anz. von Knoten) und 146 virtuelle Kerne. Der Grund für diese Anzahl von VMs besteht darin, die erforderliche Diensttrennung zu erfüllen, um die Anforderungen an Sicherheit, Skalierbarkeit, Wartung und Patches zu gewährleisten. Diese interne Dienststruktur ermöglicht die zukünftige Einführung neuer Infrastrukturdienste im Rahmen ihrer Entwicklung.

<sup>2</sup> Betriebssystemreserve für Mehraufwand = 15 % (0,15) des Knotenspeichers. Der Wert der Betriebssystemreserve ist eine Schätzung und variiert je nach der physischen Speicherkapazität des Servers und dem allgemeinen Betriebssystemmehraufwand.

Der Wert V (größter virtueller Computer in der Skalierungseinheit) basiert dynamisch auf der VM-Speichergröße des größten Mandanten. Der größte Wert für den virtuellen Computer kann beispielsweise 7 GB oder 112 GB oder jede andere unterstützte VM-Speichergröße in der Azure Stack Hub-Lösung sein. Wenn die größte VM im Azure Stack Hub-Fabric geändert wird, führt dies zu einer Erhöhung der Resilienzreserve und auch zu einer Erhöhung des Arbeitsspeichers der VM selbst.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F:** Von meinem Mandanten wurde eine neue VM bereitgestellt. Wie lange dauert es, bis die verbleibende Kapazität im Funktionsdiagramm des Administratorportals angezeigt wird?

**A:** Das Kapazitätsblatt wird alle 15 Minuten aktualisiert.

**F:** Meine Kapazität schwankt, obwohl sich die Anzahl bereitgestellter VMs in meiner Azure Stack Hub-Instanz nicht geändert hat. Warum?

**A:** Der verfügbare Arbeitsspeicher für die VM-Platzierung ist von verschiedenen Faktoren abhängig – unter anderem von der Reserve des Hostbetriebssystems. Der Wert hängt davon ab, wie viel Arbeitsspeicher von den verschiedenen auf dem Host ausgeführten Hyper-V-Prozessen beansprucht wird, was kein konstanter Wert ist.

**F:** In welchem Zustand müssen sich Mandanten-VMs befinden, damit sie Arbeitsspeicher beanspruchen?

**A:** Zusätzlich zur Ausführung von VMs wird Arbeitsspeicher von allen VMs verbraucht, die im Fabric angeordnet sind. Dies bedeutet, dass VMs mit dem Zustand „Wird erstellt“ oder „Fehler“ Arbeitsspeicher beanspruchen. VMs, die über den Gast heruntergefahren werden, beanspruchen im Gegensatz zu virtuellen Computern, deren Zuordnung mithilfe der entsprechenden Option über das Portal/mit PowerShell/über die Befehlszeilenschnittstelle aufgehoben wurde, ebenfalls Arbeitsspeicher.

**F:** Ich verfüge über eine Azure Stack Hub-Instanz mit vier Hosts. Mein Mandant umfasst drei VMs, die jeweils 56 GB RAM (D5_v2) beanspruchen. Nachdem die Größe eines der virtuellen Computer in 112 GB RAM (D14_v2) geändert wurde, wurde beim verfügbaren Arbeitsspeicher auf dem Kapazitätsblatt des Dashboards eine Spitzenauslastung von 168 GB gemeldet. Bei der späteren Änderung der Größe der anderen beiden VMs von „D5_v2“ in „D14_v2“ wurde dagegen nur ein RAM-Zuwachs von jeweils 56 GB verzeichnet. Woran liegt das?

**A:** Der verfügbare Arbeitsspeicher ist eine Funktion der von Azure Stack Hub verwalteten Resilienzreserve. Die Resilienzreserve ist eine Funktion der größten VM-Größe im Azure Stack Hub-Bereich. Zu Beginn lag der Arbeitsspeicherwert des größten virtuellen Computers bei 56 GB. Nach Änderung der Größe der VM lag der Arbeitsspeicherwert der größten VM bei 112 GB. Dadurch hat sich nicht nur der von dieser Mandanten-VM beanspruchte Arbeitsspeicher erhöht, sondern auch die Resilienzreserve. Diese Änderung führte zu einem Anstieg von 56 GB (Arbeitsspeichererhöhung um 56 GB auf 112 GB für die Mandanten-VM) plus 112 GB Arbeitsspeichererhöhung für die Resilienzreserve. Bei der späteren Größenänderung für die anderen virtuellen Computer lag die Größe des größten virtuellen Computers weiterhin bei 112 GB, sodass keine weitere Erhöhung der Resilienzreserve erforderlich war. Somit entsprach der Anstieg bei der Arbeitsspeichernutzung lediglich der Arbeitsspeichererhöhung für die Mandanten-VM (56 GB).

> [!NOTE]
> Die Kapazitätsplanungsanforderungen für Netzwerke sind minimal, da nur die Größe der öffentlichen virtuellen IP-Adresse (VIP) konfigurierbar ist. Informationen zur Vorgehensweise beim Hinzufügen weiterer öffentlicher IP-Adressen zu Azure Stack Hub finden Sie unter [Hinzufügen öffentlicher IP-Adressen](azure-stack-add-ips.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu [Azure Stack Hub-Speicher](azure-stack-capacity-planning-storage.md)
