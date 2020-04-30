---
title: Planen der Netzwerkintegration für Azure Stack Hub
description: Erfahren Sie, wie Sie die Integration von Rechenzentrumsnetzwerken in integrierte Azure Stack Hub-Systeme planen.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: f447f4969e9cb9bcb4d56ea5961473e1028e44f3
ms.sourcegitcommit: 7b8e067cb449e67ca9c2935580684d78840ad495
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106906"
---
# <a name="network-integration-planning-for-azure-stack"></a>Planen der Netzwerkintegration für Azure Stack

Dieser Artikel enthält Informationen zur Netzwerkinfrastruktur von Azure Stack, die Sie bei der Entscheidung unterstützen, wie Sie Azure Stack am besten in Ihre bestehende Netzwerkumgebung integrieren können. 

> [!NOTE]
> Zum Auflösen von externen DNS-Namen von Azure Stack (beispielsweise „www\.bing.com“) müssen Sie DNS-Server für die Weiterleitung von DNS-Anforderungen bereitstellen. Weitere Informationen zu DNS-Anforderungen für Azure Stack finden Sie unter [Azure Stack-Rechenzentrumsintegration: DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Entwurf des physischen Netzwerks

Die Azure Stack-Lösung benötigt eine zuverlässige und hoch verfügbare physische Infrastruktur, um ihren Betrieb und ihre Dienste zu unterstützen. Zum Integrieren von Azure Stack in das Netzwerk werden Uplinks von den Top-of-Rack-Switches (ToR) zum nächsten Switch oder Router benötigt, der in dieser Dokumentation als „Grenze“ (Border) bezeichnet wird. Die ToR-Einheiten können per Uplink mit einer Grenze bzw. einem Grenzpaar verbunden werden. Die ToR-Einheit wird mit unserem Automatisierungstool vorkonfiguriert. Zwischen ToR und Grenze wird mindestens eine Verbindung erwartet, wenn BGP-Routing und mindestens zwei Verbindungen (eine pro ToR) zwischen ToR und Grenze verwendet werden und das statische Routing genutzt wird. Für jede Routingoption sind maximal vier Verbindungen möglich. Diese Verbindungen sind auf SFP+- oder SFP28-Medien und Geschwindigkeiten von 1 GB, 10 GB oder 25 GB beschränkt. Angaben zur Verfügbarkeit erhalten Sie bei Ihrem OEM-Hardwarehersteller (Original Equipment Manufacturer). In der folgenden Abbildung ist der empfohlene Entwurf dargestellt:

![Empfohlener Entwurf des Azure Stack-Netzwerks](media/azure-stack-network/physical-network.svg)


## <a name="logical-networks"></a>Logische Netzwerke

Logische Netzwerke stellen eine Abstraktion der zugrunde liegenden physischen Netzwerkinfrastruktur dar. Sie dienen zum Organisieren und Vereinfachen von Netzwerkzuweisungen für Hosts, VMs und Dienste. Im Rahmen der Erstellung eines logischen Netzwerks werden Netzwerkstandorte erstellt, um die virtuellen lokalen Netzwerke (VLANs), IP-Subnetze und IP-Subnetz-/VLAN-Paare zu definieren, die dem logischen Netzwerk an jedem physischen Standort zugeordnet sind.

Die folgende Tabelle zeigt die logischen Netzwerke und die zugehörigen IPv4-Subnetzbereiche, die Sie berücksichtigen müssen:

| Logisches Netzwerk | BESCHREIBUNG | Size | 
| -------- | ------------- | ------------ | 
| Öffentliche VIP | Azure Stack verwendet insgesamt 31 Adressen aus diesem Netzwerk. Acht öffentliche IP-Adressen werden für ein paar Azure Stack-Dienste verwendet, und die restlichen Adressen werden von VMs von Mandanten verwendet. Wenn Sie App Service und SQL-Ressourcenanbieter verwenden möchten, werden sieben weitere Adressen verwendet. Die verbleibenden 15 IP-Adressen sind für zukünftige Azure-Dienste reserviert. | /26 (62 Hosts) - /22 (1022 Hosts)<br><br>Empfohlen = /24 (254 Hosts) | 
| Switchinfrastruktur | Point-to-Point-IP-Adressen für Routingzwecke, dedizierte Switchverwaltungsschnittstellen und Loopbackadressen, die dem Switch zugewiesen sind. | /26 | 
| Infrastruktur | Für die Kommunikation mit internen Azure Stack-Komponenten. | /24 |
| Privat | Wird für das Speichernetzwerk, für private VIPs, für Infrastrukturcontainer und für andere interne Funktionen verwendet. Ab 1910 wird die Größe für dieses Subnetz in „/20“ geändert. Weitere Details finden Sie im Abschnitt [Privates Netzwerk](#private-network) dieses Artikels. | /20 | 
| BMC | Für die Kommunikation mit BMCs auf den physischen Hosts. | /26 | 
| | | |

> [!NOTE]
> Wenn das System auf Version 1910 aktualisiert wird, wird der Operator durch eine Warnung im Portal daran erinnert, das neue PEP-Cmdlet **Set-AzsPrivateNetwork** auszuführen, um einen neuen privaten IP-Adressbereich der Größe „/20“ hinzuzufügen. Anweisungen zum Ausführen des Cmdlets finden Sie in den [Versionshinweisen zu 1910](release-notes.md). Weitere Informationen und Anleitungen zum Auswählen des privaten IP-Adressraums der Größe „/20“ finden Sie im Abschnitt [Privates Netzwerk](#private-network) dieses Artikels.

## <a name="network-infrastructure"></a>Netzwerkinfrastruktur

Die Netzwerkinfrastruktur für Azure Stack besteht aus mehreren logischen Netzwerken, die auf den Switches konfiguriert sind. Das folgende Diagramm zeigt diese logischen Netzwerke und wie sie in TOR- (Top-of-Rack), BMC- (Baseboard Management Controller = Baseboard-Verwaltungscontroller) und Grenzswitches (Kundennetzwerk) integriert werden können.

![Logisches Netzwerkdiagramm und Switchverbindungen](media/azure-stack-network/networkdiagram.svg)

### <a name="bmc-network"></a>BMC-Netzwerk

Dieses Netzwerk ist für die Verbindung aller BMCs (auch Dienstprozessoren genannt) mit dem Verwaltungsnetzwerk bestimmt. Zum Beispiel: iDRAC, iLO, iBMC usw. Für die Kommunikation mit einem BMC-Knoten wird nur ein BMC-Konto verwendet. Falls vorhanden, befindet sich der Hardware Lifecycle Host (HLH) in diesem Netzwerk und kann OEM-spezifische Software für die Hardwarewartung oder -überwachung bereitstellen.

Der HLH hostet auch den virtuellen Bereitstellungscomputer (Deployment VM, DVM). Der DVM wird im Rahmen der Azure Stack-Bereitstellung verwendet und nach Abschluss der Bereitstellung entfernt. In verbundenen Szenarien benötigt der DVM Internetzugriff, um mehrere Komponenten testen und überprüfen sowie darauf zugreifen zu können. Diese Komponenten können sich innerhalb oder außerhalb Ihres Unternehmensnetzwerks befinden (z. B.: NTP, DNS, und Azure). Weitere Informationen zu Konnektivitätsanforderungen finden Sie im [NAT-Abschnitt des Artikels zur Azure Stack-Firewallintegration](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Privates Netzwerk

Dieses Netzwerk der Größe „/20“ (4.096 IP-Adressen) ist für die Azure Stack-Region privat. (Das Routing geht also nicht über die Grenzswitchgeräte des Azure Stack-Systems hinaus.) Außerdem ist das Netzwerk in mehrere Subnetze unterteilt. Hierzu zählen beispielsweise:

- **Speichernetzwerk:** Ein Netzwerk der Größe „/25“ (128 Host-IP-Adressen), das zur Unterstützung der Verwendung von „Direkte Speicherplätze“ und SMB-Speicherdatenverkehr (Server Message Block) sowie der Livemigration von virtuellen Computern verwendet wird.
- **Internes VIP-Netzwerk (Virtuelle IP-Adresse):** Ein Netzwerk des Typs „/25“, das ausschließlich internen VIPs für den softwaregestützten Lastenausgleich vorbehalten ist.
- **Containernetzwerk:** Ein dediziertes Netzwerk der Größe „/23“ (512 IP-Adressen) für internen Datenverkehr zwischen Containern mit Infrastrukturdiensten.

Ab dem Release 1910 **benötigt** das Azure Stack Hub-System einen zusätzlichen privaten internen IP-Adressraum der Größe „/20“. Dieses Netzwerk ist für das Azure Stack-Region privat. (Das Routing geht also nicht über die Grenzswitchgeräte des Azure Stack-Systems hinaus.) Außerdem kann es für mehrere Azure Stack-Systeme innerhalb Ihres Rechenzentrums wiederverwendet werden. Das Netzwerk ist zwar ein privates Netzwerk für Azure Stack, es darf sich aber nicht mit anderen Netzwerken im Rechenzentrum überschneiden. Der private IP-Adressbereich „/20“ ist in mehrere Netzwerke unterteilt, die die Ausführung der Azure Stack Hub-Infrastruktur in Containern ermöglichen (wie zuvor in den [Versionshinweisen für 1905](release-notes.md?view=azs-1905) erwähnt). Darüber hinaus ermöglicht dieser neue private IP-Adressbereich auch kontinuierliche Maßnahmen zur Verringerung des erforderlichen routingfähigen IP-Adressraums vor der Bereitstellung. Die Ausführung der Azure Stack Hub-Infrastruktur in Containern dient zur Optimierung der Auslastung sowie zur Verbesserung der Leistung. Darüber hinaus wird der private IP-Adressbereich „/20“ auch verwendet, um kontinuierliche Maßnahmen zur Verringerung des erforderlichen routingfähigen IP-Adressraums vor der Bereitstellung zu ermöglichen. Als Leitfaden für den privaten IP-Adressraum empfehlen wir [RFC 1918](https://tools.ietf.org/html/rfc1918).

Für Systeme, die vor 1910 bereitgestellt wurden, ist dieses Subnetz der Größe „/20“ ein zusätzliches Netzwerk, das nach dem Aktualisieren auf 1910 in Systemen eingerichtet werden muss. Das zusätzliche Netzwerk muss über das PEP-Cmdlet **Set-AzsPrivateNetwork** für das System bereitgestellt werden.

> [!NOTE]
> Die Eingabe „/20“ ist eine Voraussetzung für das nächste Azure Stack Hub-Update nach 1910. Wenn das nächste Azure Stack Hub-Update nach 1910 veröffentlicht wurde und versucht wird, das Update zu installieren, ist das Update nicht erfolgreich, wenn die Eingabe „/20“ nicht vorgenommen wurde, wie in den folgenden Problembehandlungsschritten beschrieben. Im Administratorportal wird eine Warnung angezeigt, bis die oben erwähnten Problembehandlungsschritte ausgeführt wurden. Informationen zur Nutzung dieses neuen privaten Adressraums finden Sie im Artikel [Planen der Netzwerkintegration](azure-stack-network.md#private-network).

**Problembehandlungsschritte**: Gehen Sie zur Problembehandlung wie unter [Zugreifen auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) beschrieben vor. Bereiten Sie einen [privaten internen IP-Adressbereich](azure-stack-network.md#logical-networks) der Größe „/20“ vor, und führen Sie in der PEP-Sitzung das folgende Cmdlet (erst verfügbar ab 1910) unter Verwendung des folgenden Beispiels aus: `Set-AzsPrivateNetwork -UserSubnet 10.87.0.0/20`. Wenn der Vorgang erfolgreich ausgeführt wurde, erhalten Sie eine Meldung mit dem Hinweis, dass der interne Azs-Netzwerkbereich der Konfiguration hinzugefügt wurde (**Azs Internal Network range added to the config**). Nach erfolgreichem Abschluss wird die Warnung im Administratorportal geschlossen. Das Azure Stack Hub-System kann nun auf die nächste Version aktualisiert werden.

### <a name="azure-stack-infrastructure-network"></a>Azure Stack-Infrastrukturnetzwerk
Dieses Netzwerk des Typs „/24“ ist internen Azure Stack-Komponenten zugeordnet, damit diese untereinander kommunizieren und Daten austauschen können. Dieses Subnetz kann extern von der Azure Stack-Lösung zu Ihrem Rechenzentrum geroutet werden. Von der Verwendung öffentlicher oder über das Internet routingfähiger IP-Adressen in diesem Subnetz wird abgeraten. Dieses Netzwerk wird für das Border-Gerät angekündigt, die meisten der zugehörigen IP-Adressen werden jedoch durch Zugriffssteuerungslisten (ACLs) geschützt. Die für den Zugriff zulässigen IP--Adressen befinden sich in einem kleinen Bereich, der einem Netzwerk des Typs „/27“ und Hostdiensten wie z. B. dem [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md) und [Azure Stack Backup](azure-stack-backup-reference.md) entspricht.

### <a name="public-vip-network"></a>Öffentliches VIP-Netzwerk

Das öffentliche VIP-Netzwerk wird dem Netzwerkcontroller in Azure Stack zugewiesen. Es ist kein logisches Netzwerk auf dem Switch. Die SLB nutzt den Adresspool und ordnet Netzwerke des Typs „/32“ für die Workloads von Mandanten zu. In der Routingtabelle auf dem Switch werden diese IP-Adressen des Typs „/32“ über BGP als verfügbare Route angekündigt. Dieses Netzwerk enthält die extern zugänglichen oder öffentlichen IP-Adressen. Die Azure Stack-Infrastruktur reserviert die ersten 31 Adressen aus diesem öffentlichen VIP-Netzwerk. Der Rest wird von virtuellen Mandantencomputern genutzt. Die Netzwerkgröße in diesem Subnetz kann von einem Minimum von „/26“ (64 Hosts) bis zu einem Maximum von „/22“ (1022 Hosts) reichen. Es wird empfohlen, ein Netzwerk des Typs „/24“ zu planen.

### <a name="switch-infrastructure-network"></a>Switchinfrastrukturnetzwerk

Dieses Netzwerk des Typs „/26“ ist das Subnetz, das die routingfähigen Punkt-zu-Punkt-IP-Subnetze des Typs „/30“ (32 Host-IP-Adressen) und die Loopbacks enthält, die dedizierte Subnetze des Typs „/32“ für die In-band-Switchverwaltung und BGP-Router-ID sind. Dieser IP-Adressbereich muss außerhalb der Azure Stack-Lösung zu Ihrem Rechenzentrum geroutet werden können. Es kann sich sowohl um eine private als auch um eine öffentliche IP-Adresse handeln.

### <a name="switch-management-network"></a>Switchverwaltungsnetzwerk

Dieses Netzwerk des Typs „/29“ (6 Host-IP-Adressen) dient zum Verbinden der Verwaltungsports der Switches. Sie erlaubt einen Out-of-band-Zugriff für Bereitstellung, Verwaltung und Problembehandlung. Sie wird anhand des oben erwähnten Switchinfrastrukturnetzwerks berechnet.

## <a name="permitted-networks"></a>Zugelassene Netzwerke

Ab 1910 steht auf dem Arbeitsblatt für die Bereitstellung dieses neue Feld zur Verfügung, mit dem der Operator einige Zugriffssteuerungslisten (Access Control Lists, ACLs) ändern kann, um den Zugriff auf Verwaltungsschnittstellen für Netzwerkgeräte sowie auf den Hardwarelebenszyklus-Host (HLH) über einen vertrauenswürdigen Netzwerkbereich des Rechenzentrums zu ermöglichen. Durch die Änderung der Zugriffssteuerungsliste kann der Operator seinen Jumpbox-VMs für die Verwaltung innerhalb eines bestimmten Netzwerkbereichs Zugriff auf die Schnittstelle für die Switchverwaltung, das HLH-Betriebssystem und den HLH-BMC gewähren. Der Operator kann Subnetze für diese Liste angeben. Ist die Liste leer, wird der Zugriff standardmäßig verweigert. Dank dieser neuen Funktion ist nach der Bereitstellung kein manueller Eingriff mehr erforderlich, wie unter [Ändern bestimmter Einstellungen in der Azure Stack-Switchkonfiguration](https://docs.microsoft.com/azure-stack/operator/azure-stack-customer-defined#access-control-list-updates) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Netzwerkplanung: [Grenzkonnektivität](azure-stack-border-connectivity.md)
