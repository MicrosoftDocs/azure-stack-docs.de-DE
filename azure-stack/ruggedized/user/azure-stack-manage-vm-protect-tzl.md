---
title: Schutz von in Azure Stack bereitgestellten VMs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Wiederherstellungsplan erstellen, um in Azure Stack bereitgestellte VMs vor Datenverlust und ungeplanten Ausfallzeiten zu schützen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: c57e421140dfea9eac949b5dcf97cc90745ab861
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910958"
---
# <a name="protect-vms-deployed-on-azure-stack-hub---ruggedized"></a>Schutz von in Azure Stack Hub bereitgestellten VMs – Ruggedized

Verwenden Sie diesen Artikel als Leitfaden für die Entwicklung eines Plans zum Schützen von VMs (virtuellen Computern), die Ihre Benutzer unter Azure Stack Hub bereitstellen.

Implementieren Sie einen Datenschutz- und Notfallwiederherstellungsplan für auf VMs basierende Anwendungen in Azure Stack Hub, um Datenverlusten und ungeplanter Downtime vorzubeugen. Der implementierte Schutzplan hängt von den Geschäftsanforderungen und dem Entwurf der Anwendung ab. Dieser Plan sollte das Framework befolgen, das von der umfassenden BCDR-Strategie (Business Continuity & Disaster Recovery) Ihrer Organisation etabliert wurde. Eine allgemeine Übersicht über BCDR-Aspekte für Azure Stack Hub finden Sie im Artikel „Azure Stack: Überlegungen zu Business Continuity & Disaster Recovery“.

## <a name="application-recovery-objectives"></a>Ziele für die Anwendungswiederherstellung

Ermitteln Sie die Menge der Downtime und Datenverluste, die Ihre Organisation für jede Anwendung tolerieren kann. Indem Sie die Ausfallzeiten und Datenverluste quantifizieren, können Sie einen Wiederherstellungsplan erstellen, mit dem die Auswirkungen einer Katastrophe für Ihre Organisation gering gehalten werden. Für die einzelnen Anwendungen sollten Sie Folgendes in Erwägung ziehen:

- **Recovery Time Objective (RTO)** \
    Die RTO ist der maximal zulässige Zeitraum, in dem eine App nach einem Vorfall nicht verfügbar sein darf. Ein RTO-Wert von 90 Minuten bedeutet beispielsweise, dass Sie in der Lage sein müssen, die App innerhalb von 90 Minuten nach Beginn eines Notfalls wieder in den Ausführungszustand zu versetzen. Falls für die RTO ein niedriger Wert angesetzt ist, führen Sie ggf. eine zweite Bereitstellung durchgehend im Standby aus, um vor einem regionalen Ausfall geschützt zu sein.

- **Recovery Point Objective (RPO)** \
    RPO ist der maximale Zeitraum bei einem Notfall, in dem ein Datenverlust zulässig ist. Wenn Sie Daten beispielsweise in einer einzelnen Datenbank speichern, die nicht gesichert wird und auch keine Replikation in andere Datenbanken durchführt, können Daten für bis zu eine Stunde verloren gehen.

Führen Sie eine Bewertung durch, um RTO und RPO für jede Anwendung zu definieren.

Eine weitere wichtige Metrik, die Sie beachten sollten, ist **MTTR** (Mean Time to Recover). Dabei handelt es sich um den durchschnittlichen Zeitraum, der zur Wiederherstellung der Anwendung nach einem Ausfall erforderlich ist. MTTR ist ein empirischer Wert für ein System. Wenn der MTTR-Wert größer als der RTO-Wert ist, führt ein Ausfall des Systems zu einer nicht akzeptablen geschäftlichen Störung, weil es nicht möglich ist, das System innerhalb des definierten RTO-Zeitraums wiederherzustellen.

## <a name="protection-options-for-iaas-vms"></a>Schutzoptionen für IaaS-VMs

### <a name="backup-restore"></a>Sicherungswiederherstellung

Das Schema zum Schutz der am häufigsten verwendeten VM-basierten Apps ist die Verwendung von Sicherungssoftware. Die Sicherung einer VM umfasst in der Regel das Betriebssystem, die Betriebssystemkonfiguration, Anwendungsbinärdateien und persistente Anwendungsdaten, die in der VM enthalten sind. Die Sicherungen werden mithilfe eines Agents im Gastbetriebssystem erstellt, um die Anwendung, das Betriebssystem oder das Dateisystem/Volume zu erfassen. Ein anderer Ansatz nutzt keinen Agent, indem die Integration mit Azure Stack Hub-APIs zum Lesen von Informationen über die VM-Konfiguration und zum Erstellen einer Momentaufnahme der Datenträger verwendet wird, die an die VM angefügt sind. Beachten Sie, dass Azure Stack Hub die direkte Sicherung von Hypervisor nicht unterstützt.

### <a name="planning-your-backup-strategy"></a>Planen der Sicherungsstrategie

Bei der Planung Ihrer Sicherungsstrategie und Definition der Skalierungsanforderungen müssen Sie zunächst die Anzahl der VM-Instanzen ermitteln, die geschützt werden müssen. Die Sicherung aller VMs im System ist möglicherweise nicht die effizienteste Methode zum Schützen der Anwendung. Mit Azure Stack Hub sollten VMs in einer Skalierungsgruppe oder Verfügbarkeitsgruppe nicht auf der VM-Ebene gesichert werden. Diese VMs gelten als kurzlebig, da die VM-Gruppe horizontal hoch- und herunterskaliert werden kann. Im Idealfall befinden sich alle Daten, die gespeichert werden müssen, in einem separaten Repository, z. B. in einer Datenbank oder einem Objektspeicher. Wenn die in einer Architektur mit horizontaler Skalierung bereitgestellten Anwendungen Daten enthalten, die gespeichert und geschützt werden müssen, ist eine Sicherung auf Anwendungsebene erforderlich, die native Funktionen verwendet, die von der Anwendung oder einem Agent bereitgestellt werden.

Wichtige Überlegungen zum Sichern von VMs in Azure Stack:

- **Kategorisierung**
  - Ziehen Sie ein Modell in Betracht, bei dem Benutzer sich für die VM-Sicherung entscheiden.
  - Definieren Sie eine Vereinbarung zum Servicelevel (SLA) zur Wiederherstellung basierend auf der Priorität der Anwendungen oder der Folgen für das Unternehmen.
- **Skalieren**
  - Ziehen Sie gestaffelte Sicherungen in Betracht, wenn Sie eine große Anzahl neuer VMs integrieren (sofern eine Sicherung erforderlich ist).
  - Bewerten Sie Sicherungsprodukte, die effizient Sicherungsdaten erfassen und übertragen können, um Ressourceninhalte für die Lösung minimieren zu können.
  - Bewerten Sie Sicherungsprodukte, die Sicherungsdaten mit inkrementellen oder differenziellen Sicherungen effizient speichern, um die Notwendigkeit vollständiger Sicherungen für alle VMs in der Umgebung zu minimieren.
- **Wiederherstellen**
  - Sicherungsprodukte können virtuelle Datenträger, App-Daten auf einer vorhandenen VM oder die gesamte VM-Ressource mit den zugehörigen virtuellen Datenträgern wiederherstellen. Das benötigte Wiederherstellungsschema hängt davon ab, wie Sie die App wiederherstellen möchten. Beispielsweise kann es einfacher sein, SQL Server über eine Vorlage erneut bereitzustellen und dann anstelle der gesamten VM oder Gruppe von VMs die Datenbanken wiederherzustellen.

### <a name="replicationmanual-failover"></a>Replikation und manuelles Failover

Ein alternativer Ansatz zur Unterstützung der Wiederherstellung besteht darin, Daten in einer anderen Umgebung zu replizieren. Die Daten können wie bei der Datenbankreplikation auf die Anwendung oder im Gastbetriebssystem mithilfe eines Agents auf das Betriebssystem begrenzt werden. Durch die Integration mit Azure Stack Hub-APIs ist auch eine Begrenzung auf VM-Ebene möglich. Bei einem Notfall ist ein Failover auf den sekundären Standort erforderlich. Das Failover kann wie bei SQL-Verfügbarkeitsgruppen nativ von der Anwendung, mithilfe von Agents oder Clustertechnologie auf Gastbetriebssystemebene oder mithilfe eines Schutzprodukts auf VM-Ebene verarbeitet werden.

### <a name="high-availabilityautomatic-failover"></a>Hochverfügbarkeit und automatisches Failover

Anwendungen, die die Hochverfügbarkeit nativ unterstützen oder auf Clustersoftware für die Hochverfügbarkeit auf mehreren Clustern basieren, können für eine Gruppe von VMs in einer Azure Stack Hub-Instanz oder in mehreren Azure Stack Hub-Instanzen bereitgestellt werden. In allen Fällen ist ein gewisses Maß an Lastenausgleichen erforderlich, um sicherzustellen, dass der Anwendungsdatenverkehr ordnungsgemäß weitergeleitet wird. In dieser Konfiguration kann die Anwendung bei Ausfällen automatisch Wiederherstellungen durchführen. Bei lokalen Hardwareausfällen implementiert die Azure Stack Hub-Infrastruktur Hochverfügbarkeit und Fehlertoleranz in der physischen Infrastruktur. Bei Ausfällen auf der Computeebene verwendet Azure Stack Hub mehrere Knoten in einer Skalierungseinheit mit einer N-1-Konfiguration. Auf der VM-Ebene modellieren Verfügbarkeits- und Skalierungsgruppe jeden Knoten in der Skalierungseinheit in Form einer Fehlerdomäne, um Antiaffinität auf Knotenebene zu gewährleisten, damit Knotenausfälle nicht zum Ausfall einer verteilten Anwendung führen.

### <a name="no-protection"></a>Kein Schutz

Einige Anwendungen weisen möglicherweise keine Daten auf, die beibehalten werden müssen. Beispielsweise müssen VMs, die zu Entwicklungs- und Testzwecken verwendet werden, in der Regel nicht wiederhergestellt werden. Ein weiteres Beispiel sind zustandslose Anwendungen, die bei einer Ausfall über eine CI/CD-Pipeline wieder bereitgestellt werden können. Es ist wichtig, dass Anwendungen identifiziert werden, die keinen Schutz erfordern, um überflüssigen Schutz von VMs zu vermeiden.

<!-- ## Recommended topologies

Important considerations for your Azure Stack deployment: -->

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden allgemeine Richtlinien zum Schützen der virtuellen Computer von Benutzern beschrieben, die unter Azure Stack bereitgestellt werden. Informationen zur Verwendung von Azure-Diensten zum Schützen der virtuellen Computer von Benutzern finden Sie unter:

- [Azure Stack: Überlegungen zu Business Continuity & Disaster Recovery](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="partner-products"></a>Partnerprodukte

- [Datenblatt mit Partnerunternehmen für die Azure Stack-Rechenzentrumsintegration](https://aka.ms/azurestackbcdrpartners)
- Weitere Informationen zu den Partnerprodukten, die VM-Schutz in Azure Stack bieten, finden Sie unter [Schutz von Apps und Daten in Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
