---
title: Schutz von in Azure Stack Hub bereitgestellten VMsProtect VMs deployed on Azure Stack Hub
description: Erfahren Sie, wie Sie einen Wiederherstellungsplan erstellen, um in Azure Stack Hub bereitgestellte VMs vor Datenverlust und ungeplanten Ausfallzeiten zu schützen.
author: mattbriggs
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/5/2020
ms.openlocfilehash: 913d0eeed1ba2cfce0b062385a4f544919889f43
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79512608"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Schutz von in Azure Stack Hub bereitgestellten VMsProtect VMs deployed on Azure Stack Hub

Dieser Artikel unterstützt Sie bei der Entwicklung einer Datenschutz- und Notfallwiederherstellungsstrategie für virtuelle IaaS-Computer (virtual machines, VMs), die benutzerseitig in Azure Stack Hub bereitgestellt werden.

Zum Schutz vor Datenverlusten und längeren Ausfallzeiten empfiehlt es sich, einen Sicherungs- oder Notfallwiederherstellungsplan für Benutzeranwendungen und deren Daten zu implementieren. Dazu müssen die einzelnen Anwendungen im Rahmen einer umfassenden BCDR-Strategie (Business Continuity & Disaster Recovery) Ihrer Organisation evaluiert werden. Ein guter Ausgangspunkt ist [Azure Stack Hub: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Azure Stack: Überlegungen zu Business Continuity & Disaster Recovery).

## <a name="considerations-for-protecting-iaas-vms"></a>Überlegungen zum Schutz virtueller IaaS-Computer

### <a name="roles-and-responsibilities"></a>Rollen und Zuständigkeiten

Sorgen Sie zunächst für Klarheit hinsichtlich der Rollen und Aufgaben von Anwendungsbesitzern und Operatoren in puncto Schutz und Wiederherstellung.

Benutzer sind für den Schutz der virtuellen Computer zuständig. Operatoren kümmern sich darum, dass Azure Stack Hub online ist und ordnungsgemäß funktioniert. Azure Stack Hub beinhaltet einen Dienst, der interne Dienstdaten von Infrastrukturdiensten sichert. Benutzerdaten wie benutzerseitig erstellte virtuelle Computer, Speicherkonten mit Benutzer- oder Anwendungsdaten sowie Benutzerdatenbanken werden dabei **nicht** gesichert.


| Anwendungsbesitzer/-architekten   | Azure Stack Hub-Operator  |
|---    |---    |
| <ul><li>Abstimmen der Anwendungsarchitektur auf die Cloudentwurfsprinzipien</li></br><li>Bedarfsgerechtes Modernisieren herkömmlicher Anwendungen, um sie auf die Cloudumgebung vorzubereiten</li></br><li>Definieren akzeptabler RTO- und RPO-Werte für die Anwendung</li></br><li>Ermitteln von Anwendungsressourcen und Datenrepositorys, die geschützt werden müssen</li></br><li>Implementieren einer auf die Anwendungsarchitektur und die Kundenanforderungen abgestimmten Methode für die Daten- und Anwendungswiederherstellung</li></ul>     | <ul><li>Ermitteln der BCDR-Ziele der Organisation</li></br><li>Bereitstellen von genügend Azure Stack Hub-Instanzen, um die BCDR-Ziele der Organisation zu erfüllen</li></br><li>Entwerfen und Betreiben der Infrastruktur für den Anwendungs-/Datenschutz</li></br><li>Bereitstellen von verwalteten Lösungen oder Self-Service-Zugriff auf Schutzdienste</li></br><li>Zusammenarbeiten mit Anwendungsbesitzern/-architekten, um den Anwendungsentwurf zu verstehen und Schutzstrategien zu empfehlen</li></br><li>Ermöglichen von Infrastruktursicherungen für die Dienstreparatur und Cloudwiederherstellung</li></ul>    |

## <a name="sourcetarget-combinations"></a>Quelle/Ziel-Kombinationen

Benutzer, die sich vor dem Ausfall eines Rechenzentrums oder Standorts schützen müssen, können eine weitere Azure Stack Hub-Instanz oder Azure verwenden, um Hochverfügbarkeit bereitzustellen oder eine schnelle Wiederherstellung zu ermöglichen. Mit einem primären und sekundären Standort können Benutzer Anwendungen in einer Aktiv/Aktiv- oder Aktiv/Passiv-Konfiguration in zwei Umgebungen bereitstellen. Für weniger kritische Workloads können Benutzer auf Kapazitäten des sekundären Standorts zurückgreifen, um Anwendungen nach Bedarf aus einer Sicherung wiederherzustellen.

In einem Rechenzentrum können einzelne oder mehrere Azure Stack Hub-Clouds bereitgestellt werden. Die Bereitstellung mindestens einer Azure Stack Hub-Cloud in einem anderen Rechenzentrum ermöglicht im Notfall Failover für Workloads und trägt zur Minimierung ungeplanter Ausfallzeiten bei. Sollten Sie nur über eine einzelne Azure Stack Hub-Instanz verfügen, empfiehlt es sich gegebenenfalls, die öffentliche Azure-Cloud als Wiederherstellungscloud zu verwenden. Die Entscheidung, wo Ihre Anwendung ausgeführt werden kann, hängt von behördlichen Vorgaben, Unternehmensrichtlinien und strengen Wartezeitanforderungen ab. Der passende Wiederherstellungsort kann flexibel pro Anwendung bestimmt werden. So ist es beispielsweise möglich, dass Anwendungen eines bestimmten Abonnements Daten in einem anderen Rechenzentrum sichern und Anwendungen eines anderen Abonnements Daten in der öffentlichen Azure-Cloud replizieren.

## <a name="application-recovery-objectives"></a>Ziele für die Anwendungswiederherstellung

Anwendungsbesitzer sind in erster Linie dafür zuständig, zu bestimmen, in welchem Umfang Ausfallzeiten und Datenverluste von der Anwendung und der Organisation toleriert werden können. Die Quantifizierung akzeptabler Ausfallzeiten und Datenverluste ermöglicht die Erstellung eines Wiederherstellungsplans, der die Auswirkungen einer Katastrophe für Ihre Organisation minimiert. Machen Sie sich für jede Anwendung Gedanken zu folgenden Aspekten:

 - **Recovery Time Objective (RTO)**  
Die RTO ist der maximal zulässige Zeitraum, in dem eine App nach einem Vorfall nicht verfügbar sein darf. Ein RTO-Wert von 90 Minuten bedeutet beispielsweise, dass Sie in der Lage sein müssen, die App innerhalb von 90 Minuten nach Beginn eines Notfalls wieder in den Ausführungszustand zu versetzen. Falls für die RTO ein niedriger Wert angesetzt ist, führen Sie ggf. eine zweite Bereitstellung durchgehend im Standby aus, um vor einem regionalen Ausfall geschützt zu sein.
 - **Recovery Point Objective (RPO)**  
RPO ist der maximale Zeitraum bei einem Notfall, in dem ein Datenverlust zulässig ist. Wenn Sie Daten beispielsweise in einer einzelnen Datenbank speichern, die nicht gesichert wird und auch keine Replikation in andere Datenbanken durchführt, können Daten für bis zu eine Stunde verloren gehen.

Eine weitere Metrik ist *Mean Time to Recover* (MTTR) – der durchschnittliche Zeitraum, der zur Wiederherstellung der Anwendung nach einem Ausfall erforderlich ist. MTTR ist ein empirischer Wert für ein System. Wenn der MTTR-Wert den RTO-Wert übersteigt, führt ein Ausfall des Systems zu einer nicht akzeptablen Störung des Geschäftsbetriebs, da das System nicht innerhalb des definierten RTO-Zeitraums wiederhergestellt werden kann.

## <a name="protection-options"></a>Schutzoptionen 

### <a name="backup-restore"></a>Sicherungswiederherstellung

Die Sicherung Ihrer Anwendungen und Datasets ermöglicht eine schnelle Erholung nach einem Ausfall infolge einer Katastrophe oder aufgrund von beschädigten Daten oder versehentlichen Löschungen. Bei Anwendungen, die auf virtuellen IaaS-Computern basieren, können Anwendungsdaten, die Betriebssystemkonfiguration und auf Volumes gespeicherte Daten mithilfe eines gastinternen Agents geschützt werden. 

#### <a name="backup-using-in-guest-agent"></a>Sichern mithilfe eines gastinternen Agents

Die Sicherung eines virtuellen Computers mithilfe eines Gastbetriebssystem-Agents beinhaltet in der Regel die Erfassung der Betriebssystemkonfiguration, Dateien/Ordner, Datenträger, Anwendungsbinärdateien oder Anwendungsdaten. 

Wenn eine Anwendung aus einem Agent wiederhergestellt werden soll, muss der virtuelle Computer neu erstellt, das Betriebssystem installiert und der Gast-Agent installiert werden. An diesem Punkt können Daten im Gastbetriebssystem oder direkt in der Anwendung wiederhergestellt werden.

#### <a name="backup-using-disk-snapshot-for-stopped-vms"></a>Sichern mithilfe einer Datenträgermomentaufnahme für beendete virtuelle Computer

Sicherungsprodukte können die Konfiguration virtueller IaaS-Computer sowie die Datenträger schützen, die an einen beendeten virtuellen Computer angefügt sind. Verwenden Sie Sicherungsprodukte mit Azure Stack Hub-API-Integration, um die VM-Konfiguration zu erfassen und Datenträgermomentaufnahmen zu erstellen. Sollte für die Anwendung geplante Ausfallzeit akzeptabel sein, vergewissern Sie sich vor dem Starten des Sicherungsworkflows, dass der virtuelle Computer beendet ist.  

#### <a name="backup-using-disk-snapshot-snapshot-for-running-vms"></a>Sichern mithilfe einer Datenträgermomentaufnahme für aktive virtuelle Computer

> [!Important]  
> Die Verwendung von Datenträgermomentaufnahmen wird für aktive virtuelle Computer derzeit nicht unterstützt. Die Erstellung einer Momentaufnahme eines Datenträgers, der an einen aktiven virtuellen Computer angefügt ist, kann die Leistung oder die Verfügbarkeit des Betriebssystems oder der Anwendung auf dem virtuellen Computer beeinträchtigen. Falls keine geplante Ausfallzeit möglich ist, sollte zum Schutz der Anwendung ein gastinterner Agent verwendet werden. 

### <a name="vms-in-a-scale-set-or-availability-group"></a>Virtuelle Computer in einer Skalierungs- oder Verfügbarkeitsgruppe

Virtuelle Computer in einer Skalierungs- oder Verfügbarkeitsgruppe, die als kurzlebige Ressourcen gelten, sollten nicht auf der VM-Ebene gesichert werden. Das gilt insbesondere für zustandslose Anwendungen. Bei zustandsbehafteten Anwendungen in einer Skalierungs- oder Verfügbarkeitsgruppe sollten die Anwendungsdaten (beispielsweise eine Datenbank oder ein Volume in einem Speicherpool) ggf. geschützt werden. 

### <a name="replicationmanual-failover"></a>Replikation und manuelles Failover

Bei Anwendungen, die minimale Datenverluste oder minimale Ausfallzeiten erfordern, kann die Datenreplikation auf der Gastbetriebssystem- oder Anwendungsebene aktiviert werden, um Daten an einem anderen Standort zu replizieren. Einige Anwendungen (etwa Microsoft SQL Server) unterstützen die Replikation nativ. Sollte die Anwendung keine Replikation unterstützen, können Sie Datenträger mithilfe von Software im Gastbetriebssystem replizieren oder eine Partnerlösung verwenden, die als Agent im Gastbetriebssystem installiert wird.

Bei diesem Ansatz wird die Anwendung in einer Cloud bereitgestellt, und die Daten werden in der anderen Cloud repliziert (lokal oder in Azure). Im Falle eines Failovers muss die Anwendung am Ziel gestartet und mit den replizierten Daten verknüpft werden, um Anforderungen verarbeiten zu können.
 
### <a name="high-availabilityautomatic-failover"></a>Hochverfügbarkeit und automatisches Failover

Bei zustandslosen Anwendungen, bei denen nur Ausfallzeiten von wenigen Sekunden oder Minuten akzeptabel sind, empfiehlt sich eine Hochverfügbarkeitskonfiguration. Hochverfügbare Anwendungen sind für die Bereitstellung an mehreren Standorten in einer Aktiv/Aktiv-Topologie konzipiert, in der alle Instanzen Anforderungen verarbeiten können. Bei lokalen Hardwareausfällen implementiert die Azure Stack Hub-Infrastruktur Hochverfügbarkeit im physischen Netzwerk mithilfe von zwei Top-of-Rack-Switches. Bei Ausfällen auf der Computeebene verwendet Azure Stack Hub mehrere Knoten in einer Skalierungseinheit. Auf der VM-Ebene können Sie Skalierungsgruppen in Kombination mit Fehlerdomänen verwenden, um sicherzustellen, dass Knotenausfälle nicht zum Ausfall Ihrer Anwendung führen. Hierzu muss die gleiche Anwendung an einem sekundären Standort in der gleichen Konfiguration bereitgestellt werden. Um eine Aktiv/Aktiv-Anwendung zu erhalten, können Anforderungen mithilfe eines Lastenausgleichs oder DNS an alle verfügbaren Instanzen weitergeleitet werden.

### <a name="no-recovery"></a>Keine Wiederherstellung

Einige Apps in Ihrer Umgebung erfordern keinen Schutz vor ungeplanten Ausfallzeiten oder Datenverlusten. Die für die Entwicklung und Tests verwendeten VMs beispielsweise müssen normalerweise nicht wiederhergestellt werden. Sie entscheiden selbst, ob Sie auf den Schutz einer Anwendung oder eines Datasets verzichten möchten.

## <a name="recommended-topologies"></a>Empfohlene Topologien

Wichtige Überlegungen zu Ihrer Azure Stack Hub-Bereitstellung:

|     | Empfehlung | Kommentare |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherung bzw. Wiederherstellung von VMs in einem bereits im Rechenzentrum bereitgestellten externen Sicherungsziel | Empfohlen | Nutzen Sie die vorhandene Sicherungsinfrastruktur und Ressourcen Ihres Betriebs. Passen Sie die Größe der Sicherungsinfrastruktur an, damit diese für den Schutz weiterer VM-Instanzen bereit ist. Stellen Sie sicher, dass sich die Sicherungsinfrastruktur nicht in der Nähe Ihrer Quelle befindet. Sie können VMs in der Azure Stack Hub-Quellinstanz, in einer sekundären Azure Stack Hub-Instanz oder in Azure wiederherstellen. |
| Sicherung bzw. Wiederherstellung von VMs in einem für Azure Stack Hub dedizierten Sicherungsziel | Empfohlen | Sie können eine neue Sicherungsinfrastruktur erwerben oder eine dedizierte Sicherungsinfrastruktur für Azure Stack Hub bereitstellen. Stellen Sie sicher, dass sich die Sicherungsinfrastruktur nicht in der Nähe Ihrer Quelle befindet. Sie können VMs in der Azure Stack Hub-Quellinstanz, in einer sekundären Azure Stack Hub-Instanz oder in Azure wiederherstellen. |
| Direkte Sicherung bzw. Wiederherstellung von VMs in einer globalen Azure-Umgebung oder bei einem vertrauenswürdige Dienstanbieter | Empfohlen | Solange Sie Ihre Datenschutz- und behördlichen Anforderungen erfüllen können, können Sie Ihre Sicherungen in einer globalen Azure-Umgebung oder bei einem vertrauenswürdigen Dienstanbieter speichern. Im Idealfall führt der Dienstanbieter auch Azure Stack Hub aus. In diesem Fall profitieren Sie bei der Wiederherstellung von einer konsistenten Erfahrung hinsichtlich des Betriebs. |
| Replikation und Durchführung eines Failovers für VMs in einer separaten Azure Stack Hub-Instanz | Empfohlen | Bei einem Failover müssen Sie über eine zweite vollständig funktionsfähige Azure Stack Hub-Cloud verfügen, um längere App-Ausfallzeiten zu vermeiden. |
| Direkte Replikation und Durchführung eines Failovers für VMs in Azure oder bei einem vertrauenswürdige Dienstanbieter | Empfohlen | Solange Sie Ihre Datenschutz- und behördlichen Anforderungen erfüllen können, können Sie Ihre Daten in einer globalen Azure-Umgebung oder bei einem vertrauenswürdigen Dienstanbieter replizieren. Im Idealfall führt der Dienstanbieter auch Azure Stack Hub aus. In diesem Fall profitieren Sie nach einem Failover von einer konsistenten Erfahrung hinsichtlich des Betriebs. |
| Stellen Sie ein Sicherungsziel in der gleichen Azure Stack Hub-Instanz bereit, die auch als Host für die Anwendungen fungiert, die durch das gleiche Sicherungsziel geschützt werden. | Eigenständiges Ziel: Nicht empfohlen </br> Ziel mit externer Replikation von Sicherungsdaten: Empfohlen | Wenn Sie eine Sicherungsappliance in Azure Stack Hub bereitstellen, um die Betriebswiederherstellung zu optimieren, müssen Sie sicherstellen, dass alle Daten kontinuierlich an einen externen Sicherungsspeicherort kopiert werden. |
| Bereitstellen einer physischen Sicherungsappliance im selben Rack, auf dem die Azure Stack Hub-Lösung installiert ist | Nicht unterstützt | Zurzeit können Sie keine anderen Geräte mit Top-of-Rack-Switches verbinden, die nicht Teil der ursprünglichen Lösung sind. |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden allgemeine Richtlinien zum Schützen der virtuellen Computer von Benutzern beschrieben, die unter Azure Stack Hub bereitgestellt werden. Informationen zur Verwendung von Azure-Diensten zum Schützen der virtuellen Computer von Benutzern finden Sie unter:

- [Azure Stack IaaS: Teil 4: Schützen Ihrer Daten](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/)
- [Azure Stack: Überlegungen zu Business Continuity & Disaster Recovery](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure Backup Server
 - [Sichern von Dateien und Apps in Azure Stack Hub](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server-Unterstützung für Azure Stack Hub](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Azure Site Recovery-Unterstützung für Azure Stack Hub](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Partnerprodukte
 - [Datenblatt mit Partnerunternehmen für die Azure Stack Hub-Rechenzentrumsintegration](https://aka.ms/azurestackbcdrpartners)
