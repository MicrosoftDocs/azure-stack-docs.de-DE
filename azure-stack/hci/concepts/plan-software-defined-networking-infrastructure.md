---
title: Planen einer softwaredefinierten Netzwerkinfrastruktur
description: Dieses Thema enthält Informationen zum Planen der Bereitstellung einer softwaredefinierten Netzwerkinfrastruktur (Software Defined Network, SDN).
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 09/11/2020
ms.openlocfilehash: 7d7eeaec5f82e08cf33a307f429389f03e712987
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045544"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>Planen einer softwaredefinierten Netzwerkinfrastruktur

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server (halbjährlicher Kanal), Windows Server 2016

Erfahren Sie mehr über die Bereitstellungsplanung für eine softwaredefinierte Netzwerkinfrastruktur (SDN), einschließlich der Hardware- und Softwarevoraussetzungen. Dieses Thema enthält Planungsanforderungen für die physische und logische Netzwerkkonfiguration, für Routing, Gateways, Netzwerkhardware usw. Es umfasst außerdem Aspekte der Erweiterung einer SDN-Infrastruktur und der Verwendung einer stufenweisen Bereitstellung.

## <a name="prerequisites"></a>Voraussetzungen
Es gibt verschiedene Hardware- und Softwarevoraussetzungen für eine SDN-Infrastruktur, einschließlich:
- **Sicherheitsgruppen und dynamische DNS-Registrierung**. Sie müssen Ihr Rechenzentrum für die Bereitstellung des Netzwerkcontrollers vorbereiten, wofür eine Reihe von virtuellen Computern (Virtual Machines, VMS) erforderlich ist. Bevor Sie den Netzwerkcontroller bereitstellen können, müssen Sie Sicherheitsgruppen und die dynamische DNS-Registrierung konfigurieren.

    Weitere Informationen zur Bereitstellung von Netzwerkcontrollern für Ihr Rechenzentrum finden Sie unter [Anforderungen für die Bereitstellung von Netzwerkcontrollern](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller).

- **Physisches Netzwerk**. Sie benötigen Zugriff auf ihre physischen Netzwerkgeräte, um virtuelle lokale Netzwerke (Virtual Local Area Networks, VLANs), Routing und das Border Gateway Protocol (BGP) zu konfigurieren. Dieses Thema bietet Anweisungen für die manuelle Switchkonfiguration sowie Optionen zur Verwendung von entweder BGP-Peering auf Layer-3-Switches/-Routern oder einer RRAS-VM (Routing and Remote Access Server).

- **Physischer Compute-Hosts**. Diese Hosts führen Hyper-V aus und sind erforderlich, um eine SDN-Infrastruktur und Mandanten-VMs zu hosten. Für optimale Leistung erfordern diese Hosts spezifische Netzwerkhardware, wie im Abschnitt [Netzwerkhardware](#network-hardware) beschrieben.

## <a name="physical-and-logical-network-configuration"></a>Physische und logische Netzwerkkonfiguration
Jeder physische Computehost erfordert eine Netzwerkverbindung über mindestens einen Netzwerkadapter, der mit einem physischen Switchport verbunden ist. Ein Layer-2-[VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) unterstützt Netzwerke, die in mehrere logische Netzwerksegmente aufgeteilt sind.

>[!TIP]
>Verwenden Sie VLAN 0 für logische Netzwerke im Zugriffsmodus oder als nicht markiert.

>[!IMPORTANT]
>Softwaredefinierte Netzwerke unter Windows Server 2016unterstützen IPv4-Adressierung für die Unter- und Überlagerung. IPv6 wird nicht unterstützt. Windows Server 2019 unterstützt sowohl IPv4- als auch IPv6-Adressierung.

### <a name="logical-networks"></a>Logical networks
In diesem Abschnitt werden die Anforderungen an die Planung der SDN-Infrastruktur für das logische Verwaltungsnetzwerk und das logische Netzwerk der Hyper-V-Netzwerkvirtualisierung (HNV) behandelt. Es enthält Details zur Bereitstellung zusätzlicher logischer Netzwerke für die Verwendung von Gateways und des Software Load Balancers (SLB) sowie eine Beispielnetzwerktopologie.

#### <a name="management-and-hnv-provider"></a>Verwaltung und HNV-Anbieter
Alle physischen Computehosts müssen auf das logische Verwaltungsnetzwerk und das logische Netzwerk des HNV-Anbieters zugreifen. Bei der Planung von IP-Adressen muss jedem physischen Computehost mindestens eine IP-Adresse aus dem logischen Verwaltungsnetzwerk zugewiesen werden. Der Netzwerkcontroller erfordert eine reservierte IP-Adresse aus diesem Netzwerk, um als REST-IP-Adresse (Representational State Transfer) fungieren zu können.

Das HNV-Anbieternetzwerk dient als zugrunde liegendes physisches Netzwerk für den Ost/West-Mandantendatenverkehr (intern-intern), den Nord/Süd-Mandantendatenverkehr (extern-intern) und für den Austausch von BGP-Peeringinformationen mit dem physischen Netzwerk.

Ein DHCP-Server kann automatisch IP-Adressen für das Verwaltungsnetzwerk zuweisen, oder Sie können statische IP-Adressen manuell zuweisen. Der SDN-Stapel weist automatisch IP-Adressen für das logische Netzwerk des HNV-Anbieters für die einzelnen Hyper-V-Hosts aus einem IP-Adresspool zu. Der Netzwerkcontroller gibt den IP-Adresspool an und verwaltet diesen.

>[!NOTE]
>Der Netzwerkcontroller weist einem physischen Computehost eine HNV-Anbieter-IP-Adresse erst zu, nachdem der Netzwerkcontroller-Host-Agent eine Netzwerkrichtlinie für eine bestimmte Mandanten-VM empfangen hat.

| Situation                                                    | Folge                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| Die logischen Netzwerke verwenden VLANs,                          | der physische Computehost muss eine Verbindung mit einem Switchporttrunk herstellen, der Zugriff auf die VLANs hat. Es ist wichtig, dabei zu beachten, dass für die physischen Netzwerkadapter auf dem Computehost keine VLAN-Filterung aktiviert sein darf.|
| Sie verwenden Switch-Embedded-Teaming (SET) und verfügen über mehrere NIC-Teammitglieder (Network Interface Card, Netzwerkschnittstellenkarte), z. B. Netzwerkadapter,| Sie müssen alle NIC-Teammitglieder für diesen spezifischen Host mit derselben Layer-2-Broadcastdomäne verbinden.|
| Auf dem physischen Computehost werden zusätzliche Infrastruktur-VMs ausgeführt, z. B. Netzwerkcontroller, der SLB/Multiplexer (MUX) oder ein Gateway, | stellen Sie sicher, dass das logische Verwaltungsnetzwerk über ausreichende IP-Adressen für jede gehostete VM verfügt. Stellen Sie ferner sicher, dass das logische Netzwerk des HNV-Anbieters über ausreichende IP-Adressen verfügt, um jedem SLB/MUX und jeder Gatewayinfrastruktur-VM eine zuweisen zu können. Zwar wird die IP-Reservierung vom Netzwerkcontroller verwaltet wird, doch kann das Fehlschlagen der Reservierung einer neuen IP-Adresse aufgrund von Nichtverfügbarkeit zu doppelten IP-Adressen in Ihrem Netzwerk führen.|

Informationen zur Hyper-V-Netzwerkvirtualisierung (HNV), mit der Sie Netzwerke in einer Microsoft SDN-Bereitstellung virtualisieren können, finden Sie unter [Hyper-V-Netzwerkvirtualisierung](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization).

#### <a name="gateways-and-the-software-load-balancer-slb"></a>Gateways und der Softwarelastenausgleich (SLB)
Sie müssen zusätzliche logische Netzwerke erstellen und bereitstellen, um Gateways und den SLB zu verwenden. Stellen Sie sicher, dass Sie die richtigen IP-Präfixe, VLAN-IDs und Gateway-IP-Adressen für diese Netzwerke erhalten.

|                                |                     |
| :----------------------------- | :------------------ |
| **Logisches Netzwerk mit öffentlicher VIP** | Das logische Netzwerk mit öffentlicher virtueller IP (VIP) muss IP-Subnetzpräfixe verwenden, die außerhalb der Cloudumgebung routingfähig sind (in der Regel routingfähig über das Internet). Dies sind die Front-End-IP-Adressen, die externe Clients für den Zugriff auf Ressourcen in den virtuellen Netzwerken verwenden, einschließlich der Front-End-VIP für das Site-to-Site-Gateway. Sie müssen diesem Netzwerk kein VLAN zuweisen. |
| **Logisches Netzwerk mit privater VIP** | Das logische Netzwerk mit privater VIP muss nicht außerhalb der Cloud routingfähig sein. Dies liegt daran, dass nur VIPs, auf die von internen Cloudclients zugegriffen werden kann, z. B. private Dienste, dieses verwenden. Sie müssen diesem Netzwerk kein VLAN zuweisen. |
| **Logisches Netzwerk mit GRE VIP** | Das GRE-VIP-Netzwerk (Generic Routing Encapsulation) ist ein Subnetz, das ausschließlich zum Definieren von VIPs vorhanden ist. Die VIPs werden Gateway-VMs zugewiesen, die auf Ihrem SDN-Fabric für einen S2S-GRE-Verbindungstyp (Site-to-Site) ausgeführt werden. Sie müssen dieses Netzwerk weder in ihren physischen Switches oder im Router vorkonfigurieren noch ihm ein VLAN zuweisen. |

#### <a name="sample-network-topology"></a>Beispielnetzwerktopologie
Ändern Sie die Beispiel-IP-Subnetzpräfixe und -VLAN-IDs entsprechend Ihrer Umgebung.

| Netzwerkname | Subnet | Mask | VLAN-Kennung auf Trunk | Gateway | Reservierung (Beispiele) |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| Verwaltung              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1 – Router<br> 10.184.108.4 – Netzwerkcontroller<br> 10.184.108.10 – Computehost 1<br> 10.184.108.11 – Computehost 2<br> 10.184.108.X – Computehost X |
| HNV-Anbieter             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1 – Router<br> 10.10.56.2 – SLB/MUX1<br> 10.10.56.5 – Gateway1 |
| Öffentliche VIP               |  41.40.40.0  |    27    |          NA                |  41.40.40.1    | 41.40.40.1 – Router<br> 41.40.40.3 – IPSec S2S VPN VIP |
| Private VIP              |  20.20.20.0  |    27    |          NA                |  20.20.20.1    | 20.20.20.1 – Standard-GW (Router) |
| GRE-VIP                  |  31.30.30.0  |    24    |          NA                |  31.30.30.1    | 31.30.30.1 – Standard-GW |

## <a name="routing-infrastructure"></a>Routinginfrastruktur
Routinginformationen \(wie nächster Hop\) für die VIP-Subnetze werden vom SLB/MUX und den RAS-Gateways (Remotezugriffsserver) im physischen Netzwerk mithilfe des internen BGP-Peerings angekündigt. Den logischen VIP-Netzwerken ist kein VLAN zugewiesen, und sie sind nicht im Layer-2-Switch vorkonfiguriert (z. B. dem Top-of-Rack-Switch).

Sie müssen einen BGP-Peer auf dem Router erstellen, der von Ihrer SDN-Infrastruktur zum Empfangen von Routen für die von den SLB/MUXes und RAS-Gateways angekündigten logischen VIP-Netzwerken verwendet wird. BGP-Peering muss nur in eine Richtung erfolgen (vom SLB/MUX oder RAS-Gateway zum externen BGP-Peer). Oberhalb der ersten Routingebene können Sie statische Routen oder ein anderes dynamisches Routingprotokoll verwenden, z. B. OSPF (Open Shortest Path First). Wie bereits erwähnt, muss das IP-Subnetzpräfix für die logischen VIP-Netzwerke jedoch vom physischen Netzwerk an den externen BGP-Peer routingfähig sein.

BGP-Peering wird in der Regel in einem verwalteten Switch oder Router als Teil der Netzwerkinfrastruktur konfiguriert. Der BGP-Peer könnte auch auf einem Windows-Server konfiguriert werden, auf dem die RAS-Rolle in einem reinen Routingmodus (Nur Routing) installiert ist. Der BGP-Routerpeer in der Netzwerkinfrastruktur muss so konfiguriert sein, dass er seine eigenen ASN (Autonomous System Numbers) verwendet und Peering von einer ASN zulässt, die den SDN-Komponenten \(SLB/MUX und RAS-Gateways\) zugewiesen ist.

Sie müssen die folgenden Informationen von Ihrem physischen Router oder vom Netzwerkadministrator, der diesen Router kontrolliert, abrufen:
- Router-ASN
- Router-IP-Adresse

>[!NOTE]
>4-Byte-ASNs werden vom SLB/MUX nicht unterstützt. Sie müssen dem SLB/MUX sowie dem Router, mit dem er eine Verbindung herstellt, 2-Byte-ASNs zuordnen. 4-Byte-ASNs können Sie an anderer Stelle in Ihrer Umgebung verwenden.

Sie oder Ihr Netzwerkadministrator müssen den BGP-Routerpeer so konfigurieren, dass er Verbindungen von der ASN und der IP-Adresse oder der Subnetzadresse des logischen Netzwerks des HNV-Anbieters akzeptiert, den Ihr RAS-Gateway und die SLB/MUXes verwenden.

Weitere Informationen finden Sie unter [Border Gateway Protocol (BGP)](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp).

## <a name="default-gateways"></a>Standardgateways
Für Computer, die für die Verbindung mit mehreren Netzwerken konfiguriert sind, z. B. physische Hosts, SLB/MUX und Gateway-VMs, darf nur ein Standardgateway konfiguriert sein. Verwenden Sie die folgenden Standardgateways für die Hosts und die Infrastruktur-VMs:
- Verwenden Sie für Hyper-V-Hosts das Verwaltungsnetzwerk als Standardgateway.
- Verwenden Sie für Netzwerkcontroller-VMs das Verwaltungsnetzwerk als Standardgateway.
- Verwenden Sie für SLB/MUX-VMs das Verwaltungsnetzwerk als Standardgateway.
- Verwenden Sie für die Gateway-VMs das Netzwerk des HNV-Anbieters als Standardgateway. Dies sollte auf der Front-End-NIC der Gateway-VMs festgelegt werden.

## <a name="network-hardware"></a>Netzwerkhardware
In diesem Abschnitt werden Anforderungen an die Netzwerkhardwarebereitstellung für NICs und physische Switches bereitgestellt.

### <a name="network-interface-cards-nics"></a>Netzwerkschnittstellenkarten (NICs)
Die NICs, die Sie in Ihren Hyper-V-Hosts und Speicherhosts verwenden, erfordern bestimmte Funktionen, um die bestmögliche Leistung erzielen zu können.

Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) ist eine Methode zur Umgehung des Kernels, mit der große Datenmengen ohne Verwendung der Host-CPU übertragen werden können, wodurch die CPU zur Durchführung anderer Aufgaben freigegeben wird. Switch Embedded Teaming (SET) ist eine alternative NIC-Teaminglösung, die Sie in Umgebungen verwenden können, die Hyper-V und den SDN-Stapel enthalten. SET integriert einige NIC-Teamingfunktionen in den virtuellen Hyper-V-Switch.

Weitere Informationen finden Sie unter [Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) und SET (Switch Embedded Teaming)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Um den Mehraufwand für den Datenverkehr des virtuellen Mandantennetzwerks zu berücksichtigen, der durch VXLAN- oder NVGRE-Kapselungsheader verursacht wird, muss die maximale Übertragungseinheit (MTU) des Layer-2-Fabric-Netzwerks (Switches und Hosts) auf einen Wert größer als oder gleich 1674 Bytes, \(einschließlich Layer-2-Ethernet-Headern\) festgelegt sein.

NICs, die das neue, erweiterte Adapterschlüsselwort *EncapOverhead* unterstützen, legen die MTU automatisch über den Netzwerkcontroller-Host-Agent fest. NICs, die das neue Schlüsselwort *EncapOverhead* nicht unterstützen, müssen die MTU-Größe auf jedem physischen Host mit dem Schlüsselwort *JumboPacket* \(oder einem entsprechenden Schlüsselwort\) festlegen.

### <a name="switches"></a>Switches
Bei der Auswahl eines physischen Switches und Routers für Ihre Umgebung sollten Sie sicherstellen, dass er die folgenden Funktionen unterstützt:
- Switchport-MTU-Einstellungen \(erforderlich\)
- MTU festgelegt auf >= 1674 Bytes, \(einschließlich L2-Ethernet-Header\)
- L3-Protokolle \(erforderlich\)
- ECMP-Routing (Equal-Cost Multi Path)
- Auf BGP \(IETF RFC 4271\)\- basierendes ECMP

Implementierungen sollten die MUST-Anweisungen in den folgenden IETF-Standards unterstützen:
- RFC 2545: [BGP-4-Multiprotokollerweiterungen für das domänenübergreifende IPv6-Routing](https://tools.ietf.org/html/rfc2545)
- RFC 4760: [Multiprotokollerweiterungen für BGP-4](https://tools.ietf.org/html/rfc4760)
- RFC 4893: [BGP-Unterstützung für 4-Oktett-ASN-Räume](https://tools.ietf.org/html/rfc4893)
- RFC 4456: [BGP-Routenreflektion: Eine Alternative zum Full Mesh Internal BGP (IBGP)](https://tools.ietf.org/html/rfc4456)
- RFC 4724: [Ordnungsgemäßer Neustartmechanismus für BGP](https://tools.ietf.org/html/rfc4724)

Die folgenden Markierungsprotokolle sind erforderlich:
- VLAN – Isolation verschiedener Arten von Datenverkehr
- 802.1q-Trunk

Die folgenden Elemente bieten Verknüpfungskontrolle:
- Quality of Service \(QoS\) \(PFC nur erforderlich bei Verwendung von ROCE\)
- Enhanced Traffic Selection \(802.1Qaz\)
- Priority-Based Flow Control (PFC) \(802.1p/Q und 802.1Qbb\)

Die folgenden Elemente bieten Verfügbarkeit und Redundanz:
- Switchverfügbarkeit (erforderlich)
- Ein hochverfügbarer Router ist erforderlich, um Gatewayfunktionen auszuführen. Diesen können Sie bereitstellen, indem Sie einen Mehrfach-Chassis-Switch/Router verwenden oder Technologien wie das Virtual Router Redundancy Protocol (VRRP).

### <a name="switch-configuration-examples"></a>Switchkonfigurationsbeispiele
Zur Unterstützung der Konfiguration Ihres physischen Switchs oder Routers steht ein Satz von Beispielkonfigurationsdateien für eine Vielzahl unterschiedlicher Switchmodelle und -hersteller im [Microsoft SDN-GitHub-Repository](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples) zur Verfügung. Eine ausführliche Infodatei sowie getestete CLI-Befehle (Command-Line Interface, Befehlszeilenschnittstelle ) für bestimmte Switches werden bereitgestellt.

## <a name="compute"></a>Compute
Auf allen Hyper-V-Hosts muss das geeignete Betriebssystem installiert sowie für Hyper-V aktiviert sein, und sie müssen einen externen virtuellen Hyper-V-Switch mit mindestens einem physischer Adapter verwenden, der mit dem logischen Verwaltungsnetzwerk verbunden ist. Der Host muss über eine der vNIC des Verwaltungshosts zugewiesene Verwaltungs-IP-Adresse erreichbar sein.

Sie können jeden Speichertyp verwenden, der mit Hyper-V kompatibel ist, freigegeben oder lokal.

> [!TIP]
> Es ist praktisch, denselben Namen für alle virtuellen Switches zu verwenden, was aber nicht obligatorisch ist. Wenn Sie Skripts für die Bereitstellung verwenden möchten, lesen Sie den Kommentar, der der `vSwitchName`-Variablen in der Datei „config.psd1“ zugeordnet ist.

### <a name="host-compute-requirements"></a>Hostcomputeanforderungen
Im Folgenden werden die Mindestanforderungen an die Hardware und Software für die vier physischen Hosts gezeigt, die in der Beispielbereitstellung verwendet werden.

Host|Hardwareanforderungen|Softwareanforderungen|
--------|-------------------------|-------------------------
|Physischer Hyper-V-Host|4-Kern-CPU, 2,66 GHz<br> 32 GB RAM<br> 300 GB Festplattenspeicherplatz<br> 1 Gbit/s (oder schnellerer) physischer Netzwerkadapter|Betriebssystem: Wie definiert unter<br> „Gilt für“ am Anfang dieses Themas.<br> Installierte Hyper-V-Rolle|

### <a name="sdn-infrastructure-vm-role-requirements"></a>Anforderungen an die VM-Rolle der SDN-Infrastruktur
Im Folgenden finden Sie die Anforderungen an die VM-Rollen.

Rolle|vCPU-Anforderungen|Speicheranforderungen|Datenträgeranforderungen|
--------|-----------------------------|-----------------------|--------------------------
|Netzwerkcontroller (drei Knoten)|4 vCPUs|4 GB Minimum<br> (8 GB empfohlen)|75 GB für das Betriebssystemlaufwerk
|SLB/MUX (drei Knoten)|8 vCPUs|8 GB empfohlen|75 GB für das Betriebssystemlaufwerk
|RAS-Gateway<br> (einzelner Pool mit drei Knoten<br> Gateways, zwei aktiv, eins passiv)|8 vCPUs|8 GB empfohlen|75 GB für das Betriebssystemlaufwerk
|RAS-Gateway-BGP-Router<br> für SLB/MUX-Peering<br> (verwenden Sie alternativ einen ToR-Switch<br> als BGP-Router)|2 vCPUs|2 GB|75 GB für das Betriebssystemlaufwerk|

Wenn Sie System Center Virtual Machine Manager (VMM) für die Bereitstellung verwenden, sind zusätzliche Infrastruktur-VM-Ressourcen für VMM und andere Nicht-SDN-Infrastruktur erforderlich. Weitere Informationen finden Sie unter [Systemanforderungen für System Center Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/system-requirements?view=sc-vmm-2019&preserve-view=true).

## <a name="extending-your-infrastructure"></a>Erweitern Ihrer Infrastruktur
Die Anforderungen an Dimensionierung und Ressourcen für Ihre Infrastruktur hängen von den Mandantenworkload-VMs ab, die Sie zu hosten planen. CPU-, Arbeitsspeicher- und Datenträgeranforderungen für die Infrastruktur-VMs (z. B.: Netzwerkcontroller, SLB, Gateway usw.) sind in der vorherigen Tabelle definiert. Sie können je nach Bedarf weitere Infrastruktur-VMs hinzufügen. Alle Mandanten-VMs, die auf den Hyper-V-Hosts ausgeführt werden, verfügen jedoch über eigene CPU-, Arbeitsspeicher- und Datenträgeranforderungen, die Sie beachten müssen.

Wenn die Mandantenworkload-VMs beginnen, zu viele Ressourcen auf den physischen Hyper-V-Hosts zu belegen, können Sie Ihre Infrastruktur erweitern, indem Sie zusätzliche physische Hosts hinzufügen. Sie können entweder Windows Admin Center, VMM oder PowerShell-Skripts verwenden, um neue Serverressourcen über den Netzwerkcontroller zu erstellen. Welche Methode Sie verwenden, hängt davon ab, wie Sie die Infrastruktur anfänglich bereitgestellt haben. Wenn Sie zusätzliche IP-Adressen für das Netzwerk des HNV-Anbieters hinzufügen müssen, können Sie neue logische Subnetze (mit den entsprechenden IP-Pools) erstellen, die die Hosts verwenden können.

## <a name="phased-deployment"></a>Stufenweise Bereitstellung
Basierend auf Ihren Anforderungen müssen Sie möglicherweise einen Teil der SDN-Infrastruktur bereitstellen. Wenn Sie z. B. nur Workloads von Kunden in Ihrem Rechenzentrum hosten möchten und keine externe Kommunikation erforderlich ist, können Sie den Netzwerkcontroller bereitstellen und die Bereitstellung von SLB/MUX und Gateway-VMs überspringen. Im Folgenden werden die Anforderungen an Netzwerkfunktionen der Infrastruktur für eine stufenweise Bereitstellung der SDN-Infrastruktur beschrieben.

Funktion|Anforderungen für die Bereitstellung|Netzwerkanforderungen|
--------|-------------------------|-------------------------
|Logische Netzwerkverwaltung<br> Zugriffssteuerungslisten (ACLs) (für VLAN-basiertes Netzwerk)<br> Quality of Service (QoS) (für VLAN-basierte Netzwerke)<br>|Netzwerkcontroller|Keine|
|Virtuelle Netzwerke<br> Benutzerdefiniertes Routing<br> ACLs (für virtuelles Netzwerk)<br> Verschlüsselte Subnetze<br> QoS (für virtuelle Netzwerke)<br> Peering in virtuellen Netzwerken|Netzwerkcontroller|HNV PA VLAN, Subnetz, Router|
|Eingehende/Ausgehende NAT<br> Lastenausgleich|Netzwerkcontroller<br> SLB/MUX|BGP in HNV PA-Netzwerk<br> Private und öffentliche VIP-Subnetze|
|GRE-Gatewayverbindungen|Netzwerkcontroller<br> Gateway|BGP in HNV PA-Netzwerk<br> GRE-VIP-Subnetz|
|IPSec-Gatewayverbindungen|Netzwerkcontroller<br> SLB/MUX<br> Gateway|BGP in HNV PA-Netzwerk<br> Öffentliches VIP-Subnetz|
|L3-Gatewayverbindungen|Netzwerkcontroller<br> Gateway|Mandanten-VLAN, Subnetz, Router<br> BGP in Mandanten-VLAN ist optional.|

## <a name="next-steps"></a>Nächste Schritte
Verwandte Informationen:
- [Anforderungen für die Bereitstellung von Netzwerkcontrollern](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [SDN in Azure Stack HCI](https://docs.microsoft.com/azure-stack/hci/concepts/software-defined-networking)