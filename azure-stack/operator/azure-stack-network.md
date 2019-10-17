---
title: Überlegungen zur Netzwerkintegration für integrierte Azure Stack-Systeme | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Netzwerkintegration für Rechenzentren mit Azure Stack-Systemen mit mehreren Knoten planen können.
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
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: dca5d863a046ec225b4d34c8cf5917153a3a5785
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037970"
---
# <a name="network-connectivity"></a>Netzwerkverbindung
Dieser Artikel enthält Informationen zur Netzwerkinfrastruktur von Azure Stack, die Sie bei der Entscheidung unterstützen, wie Sie Azure Stack am besten in Ihre bestehende Netzwerkumgebung integrieren können. 

> [!NOTE]
> Zum Auflösen von externen DNS-Namen von Azure Stack (beispielsweise „www\.bing.com“) müssen Sie DNS-Server für die Weiterleitung von DNS-Anforderungen bereitstellen. Weitere Informationen zu DNS-Anforderungen für Azure Stack finden Sie unter [Azure Stack-Rechenzentrumsintegration: DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Entwurf des physischen Netzwerks
Die Azure Stack-Lösung benötigt eine zuverlässige und hoch verfügbare physische Infrastruktur, um ihren Betrieb und ihre Dienste zu unterstützen. Uplinks zwischen TOR-Switches und Borderswitches sind auf SFP+- oder SFP28-Medien und auf Geschwindigkeiten von 1 GBit/s, 10 GBit/s oder 25 GBit/s beschränkt. Angaben zur Verfügbarkeit erhalten Sie bei Ihrem OEM-Hardwarehersteller (Original Equipment Manufacturer). In der folgenden Abbildung wird unser empfohlener Entwurf dargestellt:

![Empfohlener Entwurf des Azure Stack-Netzwerks](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logische Netzwerke
Logische Netzwerke stellen eine Abstraktion der zugrunde liegenden physischen Netzwerkinfrastruktur dar. Sie dienen zum Organisieren und Vereinfachen von Netzwerkzuweisungen für Hosts, virtuelle Computer und Dienste. Im Rahmen der Erstellung eines logischen Netzwerks werden Netzwerkstandorte erstellt, um die virtuellen lokalen Netzwerke (VLANs), IP-Subnetze und IP-Subnetz-/VLAN-Paare zu definieren, die dem logischen Netzwerk an jedem physischen Standort zugeordnet sind.

Die folgende Tabelle zeigt die logischen Netzwerke und die zugehörigen IPv4-Subnetzbereiche, die Sie berücksichtigen müssen:

| Logisches Netzwerk | BESCHREIBUNG | Size | 
| -------- | ------------- | ------------ | 
| Öffentliche VIP | Azure Stack verwendet insgesamt 31 Adressen aus diesem Netzwerk. Acht öffentliche IP-Adressen werden für einen kleinen Satz von Azure Stack-Diensten verwendet, und die restlichen Adressen werden von virtuellen Computern von Mandanten verwendet. Wenn Sie App Service und SQL-Ressourcenanbieter verwenden möchten, werden sieben weitere Adressen verwendet. Die verbleibenden 15 IP-Adressen sind für zukünftige Azure-Dienste reserviert. | /26 (62 Hosts) - /22 (1022 Hosts)<br><br>Empfohlen = /24 (254 Hosts) | 
| Switchinfrastruktur | Point-to-Point-IP-Adressen für Routingzwecke, dedizierte Switchverwaltungsschnittstellen und Loopbackadressen, die dem Switch zugewiesen sind. | /26 | 
| Infrastruktur | Für die Kommunikation mit internen Azure Stack-Komponenten. | /24 |
| Private | Für das Speichernetzwerk und die privaten virtuellen IP-Adressen. | /24 | 
| BMC | Für die Kommunikation mit BMCs auf den physischen Hosts. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Netzwerkinfrastruktur
Die Netzwerkinfrastruktur für Azure Stack besteht aus mehreren logischen Netzwerken, die auf den Switches konfiguriert sind. Das folgende Diagramm zeigt diese logischen Netzwerke und wie sie mit TOR- (Top-of-Rack), BMC- (Baseboard-Verwaltungscontroller) und Grenzswitches (Kundennetzwerk) integriert werden.

![Logisches Netzwerkdiagramm und Switchverbindungen](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-Netzwerk
Dieses Netzwerk ist für die Verbindung aller Baseboard-Verwaltungscontroller (BMCs, auch Dienstprozessoren genannt, z.B. iDRAC, iLO, iBMC, iBMC usw.) mit dem Verwaltungsnetzwerk bestimmt. Für die Kommunikation mit einem BMC-Knoten wird nur ein BMC-Konto verwendet. Falls vorhanden, befindet sich der Hardware Lifecycle Host (HLH) in diesem Netzwerk und kann OEM-spezifische Software für die Hardwarewartung oder -überwachung bereitstellen. 

Der HLH hostet auch den virtuellen Bereitstellungscomputer (Deployment VM, DVM). Der DVM wird im Rahmen der Azure Stack-Bereitstellung verwendet und nach Abschluss der Bereitstellung entfernt. In verbundenen Szenarien benötigt der DVM Internetzugriff, um mehrere Komponenten testen und überprüfen sowie darauf zugreifen zu können. Diese Komponenten können sich innerhalb oder außerhalb Ihres Unternehmensnetzwerks befinden. Beispiele wären etwa NTP, DNS und Azure. Weitere Informationen zu Konnektivitätsanforderungen finden Sie im [NAT-Abschnitt des Artikels zur Azure Stack-Firewallintegration](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Privates Netzwerk
Dieses Netzwerk vom Typ „/24“ (254 Host-IP-Adressen) ist für die Azure Stack-Region privat (reicht also nicht über die Grenzswitches der Azure Stack-Region hinaus) und in zwei Subnetze aufgeteilt:

- **Speichernetzwerk**. Ein Netzwerk des Typs „/25“ (126 Host-IP-Adressen), das zur Unterstützung der Verwendung von „Direkte Speicherplätze“ und Server Message Block-Speicherdatenverkehr (SMB) sowie der Livemigration virtueller Computer verwendet wird. 
- **Internes VIP-Netzwerk (Virtuelle IP-Adresse)** . Ein Netzwerk des Typs „/25“, das ausschließlich internen VIPs für den softwaregestützten Lastenausgleich vorbehalten ist.

### <a name="azure-stack-infrastructure-network"></a>Azure Stack-Infrastrukturnetzwerk
Dieses Netzwerk des Typs „/24“ ist internen Azure Stack-Komponenten zugeordnet, damit diese untereinander kommunizieren und Daten austauschen können. Dieses Subnetz erfordert routingfähige IP-Adressen, bleibt aber durch Verwendung von Zugriffssteuerungslisten (ACLs) für die Lösung privat. Es wird nicht erwartet, dass ein Routing über die Border-Switches erfolgt. Eine Ausnahme bildet ein kleiner Bereich der Größe eines Netzwerks des Typs „/27“, der von einigen dieser Dienste genutzt wird, wenn sie Zugriff auf externe Ressourcen und/oder das Internet benötigen. 

### <a name="public-vip-network"></a>Öffentliches VIP-Netzwerk
Das öffentliche VIP-Netzwerk wird dem Netzwerkcontroller in Azure Stack zugewiesen. Es ist kein logisches Netzwerk auf dem Switch. Die SLB nutzt den Adresspool und ordnet Netzwerke des Typs „/32“ für die Workloads von Mandanten zu. In der Routingtabelle auf dem Switch werden diese IP-Adressen des Typs „/32“ über BGP als verfügbare Route angekündigt. Dieses Netzwerk enthält die extern zugänglichen oder öffentlichen IP-Adressen. Die Azure Stack-Infrastruktur reserviert die ersten 31 Adressen aus diesem öffentlichen VIP-Netzwerk. Der Rest wird von virtuellen Mandantencomputern genutzt. Die Netzwerkgröße in diesem Subnetz kann von einem Minimum von „/26“ (64 Hosts) bis zu einem Maximum von „/22“ (1022 Hosts) reichen. Wir empfehlen Ihnen, ein Netzwerk des Typs „/24“ zu planen.

### <a name="switch-infrastructure-network"></a>Switchinfrastrukturnetzwerk
Dieses Netzwerk des Typs „/26“ ist das Subnetz, das die routingfähigen Punkt-zu-Punkt-IP-Subnetze des Typs „/30“ (2 Host-IP-Adressen) und die Loopbacks enthält, die dedizierte Subnetze des Typs „/32“ für die In-band-Switchverwaltung und BGP-Router-ID sind. Dieser IP-Adressbereich muss extern von der Azure Stack-Lösung zu Ihrem Rechenzentrum geroutet werden können. Es kann sich dabei um private oder öffentliche IP-Adressen handeln.

### <a name="switch-management-network"></a>Switchverwaltungsnetzwerk
Dieses Netzwerk des Typs „/29“ (6 Host-IP-Adressen) dient zum Verbinden der Verwaltungsports der Switches. Sie erlaubt einen Out-of-band-Zugriff für Bereitstellung, Verwaltung und Problembehandlung. Sie wird anhand des oben erwähnten Switchinfrastrukturnetzwerks berechnet.




## <a name="next-steps"></a>Nächste Schritte
[Netzwerkkonnektivität über Border-Geräte](azure-stack-border-connectivity.md)
