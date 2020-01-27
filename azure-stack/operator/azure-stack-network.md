---
title: Planen der Netzwerkintegration für Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Integration von Rechenzentrumsnetzwerken in integrierte Azure Stack Hub-Systeme planen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 19091f3d55da79f025a7f002e873ba9d8996764c
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810749"
---
# <a name="network-integration-planning-for-azure-stack-hub"></a>Planen der Netzwerkintegration für Azure Stack Hub

Dieser Artikel enthält Informationen zur Netzwerkinfrastruktur von Azure Stack Hub, die Sie bei der Entscheidung unterstützen, wie Sie Azure Stack Hub am besten in Ihre bestehende Netzwerkumgebung integrieren können. 

> [!NOTE]
> Zum Auflösen von externen DNS-Namen von Azure Stack Hub (beispielsweise „www\.bing.com“) müssen Sie DNS-Server für die Weiterleitung von DNS-Anforderungen bereitstellen. Weitere Informationen zu DNS-Anforderungen für Azure Stack Hub finden Sie unter [Azure Stack Hub-Rechenzentrumsintegration: DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Entwurf des physischen Netzwerks

Die Azure Stack Hub-Lösung erfordert eine zuverlässige und hoch verfügbare physische Infrastruktur, um Vorgänge und Dienste zu unterstützen. Uplinks zwischen TOR-Switches und Borderswitches sind auf SFP+- oder SFP28-Medien und auf Geschwindigkeiten von 1 GBit/s, 10 GBit/s oder 25 GBit/s beschränkt. Angaben zur Verfügbarkeit erhalten Sie bei Ihrem OEM-Hardwarehersteller (Original Equipment Manufacturer). In der folgenden Abbildung wird unser empfohlener Entwurf dargestellt:

![Empfohlener Entwurf des Azure Stack Hub-Netzwerks](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logische Netzwerke

Logische Netzwerke stellen eine Abstraktion der zugrunde liegenden physischen Netzwerkinfrastruktur dar. Sie dienen zum Organisieren und Vereinfachen von Netzwerkzuweisungen für Hosts, VMs und Dienste. Im Rahmen der Erstellung eines logischen Netzwerks werden Netzwerkstandorte erstellt, um die virtuellen lokalen Netzwerke (VLANs), IP-Subnetze und IP-Subnetz-/VLAN-Paare zu definieren, die dem logischen Netzwerk an jedem physischen Standort zugeordnet sind.

Die folgende Tabelle zeigt die logischen Netzwerke und die zugehörigen IPv4-Subnetzbereiche, die Sie berücksichtigen müssen:

| Logisches Netzwerk | Beschreibung | Size | 
| -------- | ------------- | ------------ | 
| Öffentliche VIP | Azure Stack Hub verwendet insgesamt 31 Adressen aus diesem Netzwerk. Acht öffentliche IP-Adressen werden für einige Azure Stack Hub-Dienste verwendet, der Rest wird von Mandanten-VMs verwendet. Wenn Sie App Service und SQL-Ressourcenanbieter verwenden möchten, werden sieben weitere Adressen verwendet. Die verbleibenden 15 IP-Adressen sind für zukünftige Azure-Dienste reserviert. | /26 (62 Hosts) - /22 (1022 Hosts)<br><br>Empfohlen = /24 (254 Hosts) | 
| Switchinfrastruktur | Point-to-Point-IP-Adressen für Routingzwecke, dedizierte Switchverwaltungsschnittstellen und Loopbackadressen, die dem Switch zugewiesen sind. | /26 | 
| Infrastruktur | Wird für die Kommunikation interner Azure Stack Hub-Komponenten verwendet. | /24 |
| Privat | Wird für das Speichernetzwerk, für private VIPs, für Infrastrukturcontainer und für andere interne Funktionen verwendet. Ab 1910 wird die Größe dieses Subnetzes in „/20“ geändert. Weitere Informationen finden Sie in diesem Artikel im Abschnitt [Privates Netzwerk](#private-network). | /20 | 
| BMC | Für die Kommunikation mit BMCs auf den physischen Hosts. | /26 | 
| | | |

> [!NOTE]
> Wenn das System auf die Version 1910 aktualisiert wird, wird der Operator durch eine Warnung im Portal daran erinnert, das neue PEP-Cmdlet **Set-AzsPrivateNetwork** auszuführen, um einen neuen privaten IP-Adressbereich der Größe „/20“ hinzuzufügen. Anweisungen zum Ausführen des Cmdlets finden Sie in den [Versionshinweisen zu 1910](release-notes.md). Weitere Informationen und Anleitungen zum Auswählen des privaten IP-Adressraums der Größe „/20“ finden Sie in diesem Artikel im Abschnitt [Privates Netzwerk](#private-network).

## <a name="network-infrastructure"></a>Netzwerkinfrastruktur

Die Netzwerkinfrastruktur für Azure Stack Hub besteht aus mehreren logischen Netzwerken, die auf den Switches konfiguriert sind. Das folgende Diagramm zeigt diese logischen Netzwerke und wie sie in TOR- (Top-of-Rack), BMC- (Baseboard Management Controller = Baseboard-Verwaltungscontroller) und Grenzswitches (Kundennetzwerk) integriert werden können.

![Logisches Netzwerkdiagramm und Switchverbindungen](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-Netzwerk

Dieses Netzwerk ist für die Verbindung aller BMCs (auch Dienstprozessoren genannt) mit dem Verwaltungsnetzwerk bestimmt. Zum Beispiel: iDRAC, iLO, iBMC usw. Für die Kommunikation mit einem BMC-Knoten wird nur ein BMC-Konto verwendet. Falls vorhanden, befindet sich der Hardware Lifecycle Host (HLH) in diesem Netzwerk und kann OEM-spezifische Software für die Hardwarewartung oder -überwachung bereitstellen.

Der HLH hostet auch den virtuellen Bereitstellungscomputer (Deployment VM, DVM). Die DVM wird im Rahmen der Azure Stack Hub-Bereitstellung verwendet und nach Abschluss der Bereitstellung entfernt. In verbundenen Szenarien benötigt der DVM Internetzugriff, um mehrere Komponenten testen und überprüfen sowie darauf zugreifen zu können. Diese Komponenten können sich innerhalb oder außerhalb Ihres Unternehmensnetzwerks befinden (z. B.: NTP, DNS, und Azure). Weitere Informationen zu Konnektivitätsanforderungen finden Sie im [NAT-Abschnitt des Artikels zur Azure Stack Hub-Firewallintegration](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Privates Netzwerk

Dieses Netzwerk der Größe „/20“ (4096 IP-Adressen) ist für die Azure Stack Hub-Region privat (es erfolgt also kein Routing über die Grenzswitchgeräte des Azure Stack Hub-Systems hinaus) und in mehrere Subnetze unterteilt. Hier einige Beispiele:

- **Speichernetzwerk:** Ein Netzwerk der Größe „/25“ (128 Host-IP-Adressen), das zur Unterstützung der Verwendung von „Direkte Speicherplätze“ und SMB-Speicherdatenverkehr (Server Message Block) sowie der Livemigration von virtuellen Computern verwendet wird.
- **Internes VIP-Netzwerk (Virtuelle IP-Adresse):** Ein Netzwerk des Typs „/25“, das ausschließlich internen VIPs für den softwaregestützten Lastenausgleich vorbehalten ist.
- **Containernetzwerk:** Ein dediziertes Netzwerk der Größe „/23“ (512 IP-Adressen) für internen Datenverkehr zwischen Containern mit Infrastrukturdiensten.

Ab 1910 ändert sich die Größe für das private Netzwerk in einen privaten IP-Adressraum der Größe „/20“ (4.096 IP-Adressen). Dieses Netzwerk ist für das Azure Stack Hub-System privat (es erfolgt also kein Routing über die Grenzswitchgeräte des Azure Stack Hub-Systems hinaus) und kann in mehrere Azure Stack Hub-Systemen innerhalb Ihres Rechenzentrums wiederverwendet werden. Das Netzwerk ist zwar privat für Azure Stack Hub, darf sich aber nicht mit anderen Netzwerken im Rechenzentrum überschneiden. Treten Überschneidungen auf, leitet Azure Stack Hub unter Umständen Netzwerkdatenverkehr des Unternehmens nicht extern weiter. Als Leitfaden für den privaten IP-Adressraum empfehlen wir [RFC 1918](https://tools.ietf.org/html/rfc1918).

Dieser private IP-Adressraum der Größe „/20“ wird in mehrere Netzwerke unterteilt, die in zukünftigen Releases die Ausführung der internen Infrastruktur des Azure Stack Hub-Systems in Containern ermöglichen. Weitere Informationen finden Sie in den [Versionshinweisen zu 1910](release-notes.md). Darüber hinaus ermöglicht dieser neue private IP-Adressbereich auch kontinuierliche Maßnahmen zur Verringerung des erforderlichen routingfähigen IP-Adressraums vor der Bereitstellung.

Für Systeme, die vor 1910 bereitgestellt wurden, ist dieses Subnetz der Größe „/20“ ein zusätzliches Netzwerk, das nach dem Aktualisieren auf 1910 in Systemen eingerichtet werden muss. Das zusätzliche Netzwerk muss über das PEP-Cmdlet **Set-AzsPrivateNetwork** für das System bereitgestellt werden. Anleitungen zu diesem Cmdlet finden Sie in den [Versionshinweisen zu 1910](release-notes.md).

### <a name="azure-stack-hub-infrastructure-network"></a>Azure Stack Hub-Infrastrukturnetzwerk

Dieses Netzwerk des Typs „/24“ ist für interne Azure Stack Hub-Komponenten vorgesehen, damit diese untereinander kommunizieren und Daten austauschen können. In diesem Subnetz ist ein externes Routing von der Azure Stack Hub-Lösung zu Ihrem Rechenzentrum möglich. In diesem Subnetz sollten keine öffentlichen oder über das Internet gerouteten IP-Adressen verwendet werden. Dieses Netzwerk wird zwar für den Grenzbereich angekündigt, die meisten der zugehörigen IP-Adressen werden jedoch durch Zugriffssteuerungslisten (Access Control Lists, ACLs) geschützt. Die für den Zugriff zulässigen IP-Adressen befinden sich in einem kleinen Bereich, der einem Netzwerk der Größe „/27“ entspricht, und dienen zum Hosten von Diensten wie dem [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md) und [Azure Stack Hub-Backup](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Öffentliches VIP-Netzwerk

Das öffentliche VIP-Netzwerk wird dem Netzwerkcontroller in Azure Stack Hub zugewiesen. Es ist kein logisches Netzwerk auf dem Switch. Die SLB nutzt den Adresspool und ordnet Netzwerke des Typs „/32“ für die Workloads von Mandanten zu. In der Routingtabelle auf dem Switch werden diese IP-Adressen des Typs „/32“ über BGP als verfügbare Route angekündigt. Dieses Netzwerk enthält die extern zugänglichen oder öffentlichen IP-Adressen. Die Azure Stack Hub-Infrastruktur reserviert die ersten 31 Adressen aus diesem öffentlichen VIP-Netzwerk, der Rest wird von Mandanten-VMs verwendet. Die Netzwerkgröße in diesem Subnetz kann von einem Minimum von „/26“ (64 Hosts) bis zu einem Maximum von „/22“ (1022 Hosts) reichen. Es wird empfohlen, ein Netzwerk des Typs „/24“ zu planen.

### <a name="switch-infrastructure-network"></a>Switchinfrastrukturnetzwerk

Dieses Netzwerk des Typs „/26“ ist das Subnetz, das die routingfähigen Punkt-zu-Punkt-IP-Subnetze des Typs „/30“ (32 Host-IP-Adressen) und die Loopbacks enthält, die dedizierte Subnetze des Typs „/32“ für die In-band-Switchverwaltung und BGP-Router-ID sind. Dieser IP-Adressbereich muss außerhalb der Azure Stack Hub-Lösung zu Ihrem Rechenzentrum geroutet werden können. Es kann sich sowohl um eine private als auch um eine öffentliche IP-Adresse handeln.

### <a name="switch-management-network"></a>Switchverwaltungsnetzwerk

Dieses Netzwerk des Typs „/29“ (6 Host-IP-Adressen) dient zum Verbinden der Verwaltungsports der Switches. Sie erlaubt einen Out-of-band-Zugriff für Bereitstellung, Verwaltung und Problembehandlung. Sie wird anhand des oben erwähnten Switchinfrastrukturnetzwerks berechnet.

## <a name="permitted-networks"></a>Zugelassene Netzwerke

Ab 1910 steht auf dem Arbeitsblatt für die Bereitstellung ein neues Feld zur Verfügung, mit dem der Operator einige Zugriffssteuerungslisten (Access Control Lists, ACLs) ändern kann, um den Zugriff auf Verwaltungsschnittstellen für Netzwerkgeräte sowie auf den Hardwarelebenszyklus-Host (HLH) über einen vertrauenswürdigen Netzwerkbereich des Rechenzentrums zu ermöglichen. Durch die Änderung der Zugriffssteuerungsliste kann der Operator seinen Jumpbox-VMs für die Verwaltung innerhalb eines bestimmten Netzwerkbereichs Zugriff auf die Schnittstelle für die Switchverwaltung, das HLH-Betriebssystem und den HLH-BMC gewähren. Der Operator kann Subnetze für diese Liste angeben. Ist die Liste leer, wird der Zugriff standardmäßig verweigert. Dank dieser neuen Funktion ist nach der Bereitstellung kein manueller Eingriff mehr erforderlich, wie unter [Ändern bestimmter Einstellungen in der Azure Stack Hub-Switchkonfiguration](azure-stack-customer-defined.md#access-control-list-updates) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Netzwerkplanung: [Grenzkonnektivität](azure-stack-border-connectivity.md)
