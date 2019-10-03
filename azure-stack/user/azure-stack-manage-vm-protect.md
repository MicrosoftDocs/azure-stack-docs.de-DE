---
title: Schutz von in Azure Stack bereitgestellten VMs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Wiederherstellungsplan erstellen, um in Azure Stack bereitgestellte VMs vor Datenverlust und ungeplanten Ausfallzeiten zu schützen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: f633f000968fcf4f373fc502898fa18084f93f80
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824304"
---
# <a name="protect-vms-deployed-on-azure-stack"></a>Schutz von in Azure Stack bereitgestellten VMs

Verwenden Sie diesen Artikel als Leitfaden für die Entwicklung eines Plans zum Schützen von virtuellen Computern (VMs), die Ihre Benutzer unter Azure Stack bereitstellen.


Zum Schutz vor Datenverlusten und ungeplanten Ausfallzeiten müssen Sie einen Sicherungs- oder Notfallwiederherstellungsplan für Benutzer-Apps und die dazugehörigen Daten implementieren. Dieser Plan ist ggf. für jede App eindeutig, folgt aber einem Framework, der im Zuge einer weitreichenden Geschäftskontinuitäts- und Notfallwiederherstellungsstrategie (BC/DR) Ihrer Organisation entwickelt wurde. Ein guter Ausgangspunkt ist [Azure Stack: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Azure Stack: Überlegungen zu Business Continuity & Disaster Recovery).

## <a name="azure-stack-infrastructure-recovery"></a>Wiederherstellung der Azure Stack-Infrastruktur

Benutzer sind dafür verantwortlich, ihre VMs separat von den Infrastrukturdiensten von Azure Stack zu schützen.

Der Wiederherstellungsplan für Azure Stack-Infrastrukturdienste umfasst **nicht** die Wiederherstellung von benutzerseitigen VMs, Speicherkonten oder Datenbanken. Als App-Besitzer sind Sie für die Implementierung eines Wiederherstellungsplans für Ihre Apps und Daten verantwortlich.

Wenn die Azure Stack-Cloud längere Zeit offline oder dauerhaft nicht wiederherstellbar ist, benötigen Sie einen Wiederherstellungsplan, der Folgendes erfüllt:

* Sicherstellung von möglichst geringen Ausfallzeiten.
* Unterbrechungsfreie Ausführung kritischer VMs, z.B. Datenbankserver.
* Weiterführung der Bearbeitung von Benutzeranforderungen durch Apps.

Der Betreiber der Azure Stack-Cloud ist für die Erstellung eines Wiederherstellungsplans für die zugrunde liegende Azure Stack-Infrastruktur und die Dienste zuständig. Weitere Informationen finden Sie unter [Wiederherstellen nach schwerwiegendem Datenverlust](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>Überlegungen zu virtuellen IaaS-Computern
Das auf dem virtuellen IaaS-Computer installierte Betriebssystem hat Auswirkungen darauf, welche Produkte Sie zum Schutz der enthaltenen Daten verwenden können. Bei Windows-basierten IaaS-VMs können Sie Produkte von Microsoft sowie Partnerprodukte verwenden, um Daten zu schützen. Für Linux-basierte IaaS-Computer stehen dagegen nur Partnerprodukte zur Verfügung. [Dieses Datenblatt](https://aka.ms/azurestackbcdrpartners) enthält alle Business Continuity & Disaster Recovery-Partner mit geprüften Produkten für Azure Stack.

## <a name="sourcetarget-combinations"></a>Quelle/Ziel-Kombinationen

Jede Azure Stack-Cloud wird in einem Rechenzentrum bereitgestellt. Für die Wiederherstellung Ihrer Apps ist eine separate Umgebung erforderlich. Bei der Wiederherstellungsumgebung kann es sich um eine andere Azure Stack-Cloud in einem anderen Rechenzentrum oder die öffentliche Azure-Cloud handeln. Von Ihren Anforderungen hinsichtlich der Datenhoheit und des Datenschutzes hängt ab, welche Wiederherstellungsumgebung für Ihre App erforderlich ist. Bei der Aktivierung des Schutzes für die Apps können Sie für jede App eine bestimmte Wiederherstellungsoption auswählen. So können Apps in einem Abonnement enthalten sein, das Daten in einem anderen Rechenzentrum sichert. In einem anderen Abonnement können Sie Daten in der öffentlichen Azure-Cloud replizieren.

Planen Sie Ihre Sicherungswiederherstellungs- und Notfallwiederherstellungsstrategie für die einzelnen Apps, um das Ziel für jede App zu bestimmen. Mit einem Wiederherstellungsplan kann Ihre Organisation die Größe der lokal erforderlichen Speicherkapazität entsprechend anpassen und den Verbrauch in der öffentlichen Cloud kalkulieren.

|  | Globale Azure-Umgebung | Azure Stack-Bereitstellung in einem von CSP betriebenem Rechenzentrum | Azure Stack-Bereitstellung in einem vom Kunden betriebenen Rechenzentrum |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack-Bereitstellung in einem von CSP betriebenen Rechenzentrum** | Es werden Benutzer-VMs für die von CSP betriebene Azure Stack-Instanz bereitgestellt.<br><br>Benutzer-VMs werden über eine Sicherung oder ein Failover direkt in Azure wiederhergestellt. | CSP betreibt die primären und sekundären Instanzen von Azure Stack in seinen eigenen Rechenzentren.<br><br>Benutzer-VMs werden wiederhergestellt, oder es wird ein Failover zwischen den beiden Azure Stack-Instanzen durchgeführt. | CSP betreibt Azure Stack am primären Standort.<br><br>Das Rechenzentrum des Kunden ist das Wiederherstellungs- oder Failoverziel. |
| **Azure Stack-Bereitstellung in einem vom Kunden betriebenen Rechenzentrum** | Es werden Benutzer-VMs für die vom Kunden betriebene Azure Stack-Instanz bereitgestellt.<br><br>Benutzer-VMs werden über eine Sicherung oder ein Failover direkt in Azure wiederhergestellt. | Der Kunde betreibt Azure Stack am primären Standort.<br><br>Das Rechenzentrum von CSP ist das Wiederherstellungs- oder Failoverziel. | Der Kunde betreibt die primären und sekundären Instanzen von Azure Stack in seinen eigenen Rechenzentren.<br><br>Benutzer-VMs werden wiederhergestellt, oder es wird ein Failover zwischen den beiden Azure Stack-Instanzen durchgeführt. |

![Quelle/Ziel-Kombinationen](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Ziele für die App-Wiederherstellung

Ermitteln, welche Ausfallzeiten und Datenverluste Ihre Organisation für eine App jeweils tolerieren kann. Indem Sie die Ausfallzeiten und Datenverluste quantifizieren, können Sie einen Wiederherstellungsplan erstellen, mit dem die Auswirkungen einer Katastrophe für Ihre Organisation gering gehalten werden. Erwägen Sie für jede App:

 - **Recovery Time Objective (RTO)**  
Die RTO ist der maximal zulässige Zeitraum, in dem eine App nach einem Vorfall nicht verfügbar sein darf. Ein RTO-Wert von 90 Minuten bedeutet beispielsweise, dass Sie in der Lage sein müssen, die App innerhalb von 90 Minuten nach Beginn eines Notfalls wieder in den Ausführungszustand zu versetzen. Falls für die RTO ein niedriger Wert angesetzt ist, führen Sie ggf. eine zweite Bereitstellung durchgehend im Standby aus, um vor einem regionalen Ausfall geschützt zu sein.
 - **Recovery Point Objective (RPO)**  
RPO ist der maximale Zeitraum bei einem Notfall, in dem ein Datenverlust zulässig ist. Wenn Sie Daten beispielsweise in einer einzelnen Datenbank speichern, die nicht gesichert wird und auch keine Replikation in andere Datenbanken durchführt, können Daten für bis zu eine Stunde verloren gehen.

RTO und RPO sind geschäftliche Anforderungen. Führen Sie eine Risikobewertung durch, um die RTO und RPO der App zu definieren.

Eine weitere Metrik ist **Mean Time to Recover** (MTTR) – der durchschnittliche Zeitraum, der zur Wiederherstellung der App nach einem Ausfall erforderlich ist. MTTR ist ein empirischer Wert für ein System. Wenn der MTTR-Wert größer als der RTO-Wert ist, führt ein Ausfall des Systems zu einer nicht akzeptablen geschäftlichen Störung, weil es nicht möglich ist, das System innerhalb des definierten RTO-Zeitraums wiederherzustellen.

### <a name="backup-restore"></a>Sicherungswiederherstellung

Das Schema zum Schutz der am häufigsten verwendeten VM-basierten Apps ist die Verwendung von Sicherungssoftware. Die Sicherung einer VM umfasst in der Regel das Betriebssystem, die Betriebssystemkonfiguration, App-Binärdateien und App-Daten. Die Sicherungen werden durch Erstellen einer Momentaufnahme der Volumes, Datenträger oder der gesamten VM erstellt. Mit Azure Stack haben Sie die Möglichkeit, innerhalb des Kontexts des Gastbetriebssystems oder über Speicher- und Compute-APIs von Azure Stack Sicherungen durchzuführen. Azure Stack unterstützt nicht die Durchführung von Sicherungen auf Hypervisorebene.
 
![Sicherungswiederherstellung](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Für die Wiederherstellung der App muss mindestens eine VM in der gleichen Cloud oder in einer neuen Cloud wiederhergestellt werden. Sie können als Ziel eine Cloud in Ihrem Rechenzentrum oder in der öffentlichen Cloud festlegen. Die von Ihnen gewählte Cloud unterliegt Ihrer vollständigen Kontrolle und basiert auf Ihren Anforderungen hinsichtlich des Datenschutzes und der Datenhoheit.

 - RTO: In Stunden gemessene Ausfallzeit
 - RPO: Variabler Datenverlust (je nach Sicherungshäufigkeit)
 - Bereitstellungstopologie: Aktiv/passiv

#### <a name="planning-your-backup-strategy"></a>Planen der Sicherungsstrategie

Bei der Planung Ihrer Sicherungsstrategie und Definition der Skalierungsanforderungen müssen Sie zunächst die Anzahl der VM-Instanzen ermitteln, die geschützt werden müssen. Die Sicherung sämtlicher VMs auf allen Servern in einer Umgebung ist eine häufig angewendete Strategie. Bei Azure Stack gibt es jedoch einige VMs, die in jedem Fall gesichert werden müssen. VMs in einer Skalierungsgruppe werden beispielsweise als kurzlebige Ressourcen behandelt, die auch ohne vorherige Ankündigung beliebig hinzugefügt und entfernt werden können. Permanent zu speichernde Daten, die geschützt werden müssen, werden in einem separaten Repository gespeichert, z.B. einem Datenbank- oder Objektspeicher.

Wichtige Überlegungen zum Sichern von VMs in Azure Stack:

 - **Kategorisierung**
    - Ziehen Sie ein Modell in Betracht, bei dem Benutzer sich für die VM-Sicherung entscheiden.
    - Definieren Sie eine Vereinbarung zum Servicelevel (SLA) zur Wiederherstellung basierend auf der Priorität der Apps oder der Folgen für das Unternehmen.
 - **Skalieren**
    - Ziehen Sie gestaffelte Sicherungen in Betracht, wenn Sie eine große Anzahl neuer VMs integrieren (sofern eine Sicherung erforderlich ist).
    - Bewerten Sie Sicherungsprodukte, die effizient Sicherungsdaten erfassen und übertragen können, um Ressourceninhalte für die Lösung minimieren zu können.
    - Bewerten Sie Sicherungsprodukte, die Sicherungsdaten mit inkrementellen oder differenziellen Sicherungen effizient speichern, um die Notwendigkeit vollständiger Sicherungen für alle VMs in der Umgebung zu minimieren.
 - **Wiederherstellen**
    - Sicherungsprodukte können virtuelle Datenträger, App-Daten auf einer vorhandenen VM oder die gesamte VM-Ressource mit den zugehörigen virtuellen Datenträgern wiederherstellen. Das benötigte Wiederherstellungsschema hängt davon ab, wie Sie die App wiederherstellen möchten. Beispielsweise kann es einfacher sein, SQL Server über eine Vorlage erneut bereitzustellen und dann anstelle der gesamten VM oder Gruppe von VMs die Datenbanken wiederherzustellen.

### <a name="replicationmanual-failover"></a>Replikation und manuelles Failover

Ein alternativer Ansatz zur Unterstützung von Hochverfügbarkeit besteht darin, Ihre App-VMs in einer anderen Cloud zu replizieren und ein manuelles Failover zu veranlassen. Die Replikation des Betriebssystems, der Binärdateien der App und der App-Daten kann auf VM- oder Gastbetriebssystemebene ausgeführt werden. Das Failover wird mit zusätzlicher Software verwaltet, die nicht Teil der App ist.

Bei diesem Ansatz wird die App in einer Cloud bereitgestellt, und die dazugehörige VM wird in der anderen Cloud repliziert. Wenn ein Failover ausgelöst wird, müssen die sekundären VMs in der zweiten Cloud eingeschaltet werden. In einigen Szenarien erstellt das Failover die VMs und fügt Datenträger daran an. Die Durchführung dieses Vorgangs kann lange dauern, insbesondere bei einer Multi-Tier-App, für die eine bestimmte Startsequenz erforderlich ist. Bevor die App mit der Anforderungsverarbeitung beginnen kann, müssen eventuell entsprechende Schritte ausgeführt werden.

![Replikation und manuelles Failover](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: In Minuten gemessene Ausfallzeit
 - RPO: Variabler Datenverlust (je nach Replikationshäufigkeit)
 - Bereitstellungstopologie: Aktiv/passiv und Standby
 
### <a name="high-availabilityautomatic-failover"></a>Hochverfügbarkeit und automatisches Failover

Bei Apps, bei denen Ihr Unternehmen nur Ausfallzeiten von wenigen Sekunden oder Minuten und minimalen Datenverlust tolerieren kann, sollten Sie eine Hochverfügbarkeitskonfiguration in Betracht ziehen. Hochverfügbarkeits-Apps sind für die schnelle und automatische Wiederherstellung nach Ausfällen konzipiert. Bei lokalen Hardwareausfällen implementiert die Azure Stack-Infrastruktur Hochverfügbarkeit im physischen Netzwerk mithilfe von zwei Top-of-Rack-Switches. Bei Ausfällen auf Compute-Ebene verwendet Azure Stack mehrere Knoten in einer Skalierungseinheit. Auf VM-Ebene können Sie Skalierungsgruppen in Kombination mit Fehlerdomänen verwenden, um sicherzustellen, dass Knotenausfälle nicht zum Ausfall Ihrer App führen.

In Kombination mit Skalierungsgruppen muss Ihre App native Unterstützung für Hochverfügbarkeit oder die Verwendung von Clustersoftware bieten. Microsoft SQL Server bietet z.B. native Unterstützung für Hochverfügbarkeit für Datenbanken mit synchronem Commitmodus. Wenn Sie nur asynchrone Replikationen unterstützen können, so geht dies mit einem gewissen Datenverlust einher. Apps können auch in einem Failovercluster bereitgestellt werden, in dem die Clustersoftware das automatische Failover der App verarbeitet.

Bei dieser Vorgehensweise ist die App zwar nur in einer Cloud aktiv, doch die Software wird in mehreren Clouds bereitgestellt. Die anderen Clouds befinden sich im Standbymodus, um die App beim Auslösen des Failovers zu starten.

 - RTO: In Sekunden gemessene Ausfallzeit
 - RPO: Minimaler Datenverlust
 - Bereitstellungstopologie: Aktiv/aktiv und Standby

### <a name="fault-tolerance"></a>Fehlertoleranz

Die Verfügbarkeit des physischen Redundanz- und Infrastrukturdiensts Azure Stack schützt nur vor Fehlern oder Ausfällen auf Hardwareebene, z.B. in Bezug auf einen Datenträger, die Stromversorgung, einen Netzwerkport oder einen Knoten. Falls die App durchgängig verfügbar sein muss und keine Daten verloren gehen dürfen, müssen Sie aber die Fehlertoleranz nativ in Ihrer App implementieren oder zusätzliche Software nutzen, um die Fehlertoleranz zu aktivieren.

Zunächst müssen Sie sicherstellen, dass die App-VMs zum Schutz vor Ausfällen auf Knotenebene über Skalierungsgruppen bereitgestellt werden. Um zu verhindern, dass die Cloud offline geschaltet wird, muss die App bereits einer anderen Cloud bereitgestellt worden sein, damit die Anforderungsverarbeitung unterbrechungsfrei fortgesetzt werden kann. Dieses Modell wird in der Regel als „Aktiv/Aktiv-Bereitstellung“ bezeichnet.

Bedenken Sie, dass Azure Stack-Clouds unabhängig voneinander sind, weshalb die Clouds aus Sicht der Infrastruktur immer als aktiv gelten. In diesem Fall werden mehrere aktive Instanzen der App in einer oder in mehreren aktiven Clouds bereitgestellt.

 - RTO: Keine Ausfallzeit
 - RPO: Kein Datenverlust
 - Bereitstellungstopologie: Aktiv/aktiv

### <a name="no-recovery"></a>Keine Wiederherstellung

Einige Apps in Ihrer Umgebung erfordern keinen Schutz vor ungeplanten Ausfallzeiten oder Datenverlusten. Die für die Entwicklung und Tests verwendeten VMs beispielsweise müssen normalerweise nicht wiederhergestellt werden. Es bleibt demnach Ihre Entscheidung, ob Sie eine App oder eine bestimmte VM mit einem Schutz versehen oder nicht. Azure Stack bietet keine Sicherung oder Replikation von VMs über die zugrunde liegende Infrastruktur. Ähnlich wie bei Azure müssen Sie den Schutz für die einzelnen VMs in jedem Ihrer Abonnements auswählen.

 - RTO: Nicht behebbar
 - RPO: Vollständiger Datenverlust

## <a name="recommended-topologies"></a>Empfohlene Topologien

Wichtige Überlegungen zu Ihrer Azure Stack-Bereitstellung:

|     | Empfehlung | Kommentare |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherung bzw. Wiederherstellung von VMs in einem bereits im Rechenzentrum bereitgestellten externen Sicherungsziel | Empfohlen | Nutzen Sie die vorhandene Sicherungsinfrastruktur und Ressourcen Ihres Betriebs. Passen Sie die Größe der Sicherungsinfrastruktur an, damit diese für den Schutz weiterer VM-Instanzen bereit ist. Stellen Sie sicher, dass sich die Sicherungsinfrastruktur nicht in der Nähe Ihrer Quelle befindet. Sie können VMs in der Azure Stack-Quellinstanz, in einer sekundären Azure Stack-Instanz oder in Azure wiederherstellen. |
| Sicherung bzw. Wiederherstellung von VMs in einem für Azure Stack dedizierten Sicherungsziel | Empfohlen | Sie können eine neue Sicherungsinfrastruktur erwerben oder eine dedizierte Sicherungsinfrastruktur für Azure Stack bereitstellen. Stellen Sie sicher, dass sich die Sicherungsinfrastruktur nicht in der Nähe Ihrer Quelle befindet. Sie können VMs in der Azure Stack-Quellinstanz, in einer sekundären Azure Stack-Instanz oder in Azure wiederherstellen. |
| Direkte Sicherung bzw. Wiederherstellung von VMs in einer globalen Azure-Umgebung oder bei einem vertrauenswürdige Dienstanbieter | Empfohlen | Solange Sie Ihre Datenschutz- und behördlichen Anforderungen erfüllen können, können Sie Ihre Sicherungen in einer globalen Azure-Umgebung oder bei einem vertrauenswürdigen Dienstanbieter speichern. Im Idealfall führt der Dienstanbieter auch Azure Stack aus. In diesem Fall profitieren Sie bei der Wiederherstellung von einer konsistenten Erfahrung hinsichtlich des Betriebs. |
| Replikation und Durchführung eines Failovers für VMs in einer separaten Azure Stack-Instanz | Empfohlen | Bei einem Failover müssen Sie über eine zweite vollständig funktionsfähige Azure Stack-Cloud verfügen, um längere App-Ausfallzeiten zu vermeiden. |
| Direkte Replikation und Durchführung eines Failovers für VMs in Azure oder bei einem vertrauenswürdige Dienstanbieter | Empfohlen | Solange Sie Ihre Datenschutz- und behördlichen Anforderungen erfüllen können, können Sie Ihre Daten in einer globalen Azure-Umgebung oder bei einem vertrauenswürdigen Dienstanbieter replizieren. Im Idealfall führt der Dienstanbieter auch Azure Stack aus. In diesem Fall profitieren Sie nach einem Failover von einer konsistenten Erfahrung hinsichtlich des Betriebs. |
| Bereitstellen eines Sicherungsziels in der gleichen Azure Stack-Cloud mit Ihren App-Daten | Nicht empfohlen | Speichern Sie Sicherungen nicht in der gleichen Azure Stack-Cloud. Ungeplante Ausfallzeiten der Cloud können Sie am Zugriff auf Ihre primären Daten und Sicherungsdaten hindern. Wenn Sie ein Sicherungsziel als virtuelles Gerät bereitstellen möchten (zwecks Optimierung der Sicherung und Wiederherstellung), müssen Sie sicherstellen, dass alle Daten fortlaufend in einen externen Speicherort für die Sicherung kopiert werden. |
| Bereitstellen einer physischen Sicherungsappliance im selben Rack, auf dem die Azure Stack-Lösung installiert ist | Nicht unterstützt | Zurzeit können Sie keine anderen Geräte mit Top-of-Rack-Switches verbinden, die nicht Teil der ursprünglichen Lösung sind. |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden allgemeine Richtlinien zum Schützen der virtuellen Computer von Benutzern beschrieben, die unter Azure Stack bereitgestellt werden. Informationen zur Verwendung von Azure-Diensten zum Schützen der virtuellen Computer von Benutzern finden Sie unter:

 - [Sichern von Dateien und Apps in Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server-Unterstützung für Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery-Unterstützung für Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Weitere Informationen zu den Partnerprodukten, die VM-Schutz in Azure Stack bieten, finden Sie unter [Schutz von Apps und Daten in Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
