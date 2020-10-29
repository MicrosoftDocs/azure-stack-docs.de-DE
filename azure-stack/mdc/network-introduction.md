---
title: Einführung in MDC-Netzwerke (Modular Data Center)
description: Erfahren Sie mehr über Netzwerke für das MDC-Gerät.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/31/2019
ms.lastreviewed: 12/31/2019
ms.openlocfilehash: 511ea66e0f70041ffc237463e33fccdbf390360d
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183430"
---
# <a name="modular-data-center-mdc-network-introduction"></a>Einführung in MDC-Netzwerke (Modular Data Center)

## <a name="package-content"></a>Paketinhalt

Die Lösung wird mit den zusätzlichen Netzwerkgeräten ausgeliefert, die unten beschrieben werden. Mithilfe dieser Geräte wird der Container mit Ihrem Netzwerk verbunden.

Überprüfen Sie, ob die Switchoptiken Ihrer Umgebung entsprechen.

| Menge | Typ | Modell |
|-----|---------------|-------|
| 12 | 12 × QSFP-40G | „SR“ |
| 12 | 12 × SFP+ 10 GB | „SR“ |
| 12 | 12 × SFP 1 G | „SR“ |
| 12 | 12 × QSFP-40G | „LR“ |
| 12 | 12 × SFP+ 10 GB | „LR“ |
| 12 | 12 × SFP 1 G | „LR“ |

## <a name="network-design-overview"></a>Übersicht über den Netzwerkentwurf

### <a name="physical-network-design"></a>Entwurf des physischen Netzwerks

Die MDC-Lösung benötigt eine zuverlässige und hochverfügbare physische Infrastruktur, um ihren Betrieb und ihre Dienste zu unterstützen. Uplinks zwischen ToR-Switches und Grenzswitches sind auf SFP+- oder SFP28-Medien und auf Geschwindigkeiten von 1 GBit/s, 10 GBit/s oder 40 GBit/s beschränkt.

In der folgenden Abbildung wird der empfohlene Entwurf für ein MDC dargestellt:

![Entwurfsempfehlung für das physische Netzwerk](media/network-introduction/physical-network-design.png)

### <a name="logical-network-design"></a>Entwurf des logischen Netzwerks

Der Entwurf eines logischen Netzwerks stellt eine Abstraktion einer physischen Netzwerkinfrastruktur dar. Sie dienen zum Organisieren und Vereinfachen von Netzwerkzuweisungen für Hosts, VMs und Dienste. Im Rahmen der Erstellung des logischen Netzwerks werden Netzwerkstandorte erstellt, um Folgendes zu definieren:
- Virtuelle lokale Netzwerke (VLANs)
- IP-Subnetze
- IP-Subnetz-VLAN-Paare

Alle diese Elemente werden dem logischen Netzwerk an jedem physischen Standort zugeordnet.

Die folgende Tabelle zeigt die logischen Netzwerke und die zugehörigen IPv4-Subnetzbereiche, die Sie berücksichtigen müssen:

| **Logisches Netzwerk** | **Beschreibung** | **Größe** |
|---------------------|-----------------|----------|
| Öffentliche virtuelle IP-Adresse (VIP) | Das MDC verwendet insgesamt 31 Adressen aus diesem Netzwerk. Acht öffentliche IP-Adressen werden für einige wenige MDC-Dienste verwendet, und die restlichen Adressen werden von VMs von Mandanten verwendet. Wenn Sie App Service und SQL-Ressourcenanbieter verwenden möchten, werden sieben weitere Adressen verwendet. Die verbleibenden 15 IP-Adressen sind für zukünftige Azure-Dienste reserviert. | /26 (62 Hosts) - /22 (1022 Hosts) <br><Br>Empfohlen = /24 (254 Hosts) |
| Switchinfrastruktur | Point-to-Point-IP-Adressen für Routingzwecke, dedizierte Switchverwaltungsschnittstellen und Loopbackadressen, die dem Switch zugewiesen sind. | /26 |
| Infrastruktur | Für die Kommunikation mit internen MDC-Komponenten | /24 |
| Privat | Für das Speichernetzwerk, private VIPs, Infrastrukturcontainer und für andere interne Funktionen | /20 |
| Baseboard-Verwaltungscontroller | Für die Kommunikation mit den Baseboard-Verwaltungscontrollern auf den physischen Hosts | /26 |
| Isilon | Für die Kommunikation mit dem Isilon-Speicher | 1 × /25 ToR, 1 × /25 BMC (Verwaltung) |

### <a name="network-infrastructure"></a>Netzwerkinfrastruktur

Die Netzwerkinfrastruktur für ein MDC besteht aus mehreren logischen Netzwerken, die auf den Switches konfiguriert sind. Das folgende Diagramm zeigt diese logischen Netzwerke und wie sie auf ToR- (Top-of-Rack), BMC- (Baseboard Management Controller, Baseboard-Verwaltungscontroller) und Grenzswitches (Kundennetzwerk) integriert werden können.

Diagramm des virtuellen MDC-Netzwerks:

![Entwurf des logischen Netzwerks](media/network-introduction/logical-network-design.png)

#### <a name="bmc-network"></a>BMC-Netzwerk

Dieses Netzwerk ist für die Verbindung aller BMCs (auch Dienstprozessoren genannt) mit dem Verwaltungsnetzwerk bestimmt. Zum Beispiel: iDRAC, iLO, iBMC usw. Für die Kommunikation mit einem BMC-Knoten wird nur ein BMC-Konto verwendet. Falls vorhanden, befindet sich der Hardware Lifecycle Host (HLH) in diesem Netzwerk und kann OEM-spezifische Software für die Hardwarewartung oder -überwachung bereitstellen.

Der HLH hostet auch den virtuellen Bereitstellungscomputer (Deployment VM, DVM). Die DVM wird während der MDC-Bereitstellung verwendet und nach Abschluss der Bereitstellung entfernt. In verbundenen Szenarien benötigt die DVM Internetzugriff, um mehrere Komponenten testen und überprüfen sowie darauf zugreifen zu können. Diese Komponenten können sich innerhalb oder außerhalb Ihres Unternehmensnetzwerks befinden (z. B.: NTP, DNS, und Azure). Weitere Informationen zu Konnektivitätsanforderungen finden Sie im NAT-Abschnitt des Artikels zur MDC-Firewallintegration.

#### <a name="private-network"></a>Privates Netzwerk

Das Netzwerk der Größe /20 (4.096 Host-IP-Adressen) ist im MDC-Bereich privat. Es wird nicht über die Grenzswitchgeräte des MDC-Bereichs hinaus erweitert. Dieses Netzwerk ist in mehrere Subnetze unterteilt, z. B.:

- **Speichernetzwerk:** Ein Netzwerk der Größe „/25“ (128 Host-IP-Adressen), das zur Unterstützung der Verwendung von „Direkte Speicherplätze“ und SMB-Speicherdatenverkehr (Server Message Block) sowie der Livemigration von virtuellen Computern verwendet wird.
- Internes VIP-Netzwerk (Virtuelle IP-Adresse): Ein Netzwerk der Größe /25, das ausschließlich internen VIPs für den softwaregestützten Lastenausgleich vorbehalten ist.
- **Containernetzwerk:** Ein dediziertes Netzwerk der Größe /23 (512 IP-Adressen) für internen Datenverkehr zwischen Containern mit Infrastrukturdiensten

Die Größe für das geänderte private Netzwerk in einem privaten IP-Adressraum ist /20 (4.096 IP-Adressen). Dieses Netzwerk ist für das MDC-System privat. Es wird nicht über die Grenzswitchgeräte des MDC-Systems hinaus erweitert und kann auf mehreren MDC-Systemen wiederverwendet werden. Das Netzwerk ist zwar ein privates Netzwerk für das MDC, es darf sich aber nicht mit anderen Netzwerken im Rechenzentrum überschneiden. Als Leitfaden für den privaten IP-Adressraum empfehlen wir RFC 1918.

Der private IP-Adressraum /20 ist in mehrere Netzwerke unterteilt, die in zukünftigen Releases die Ausführung der MDC-Systeminfrastruktur in Containern ermöglichen. Details finden Sie in den Versionshinweisen zu Release 1910. Dieser neue private IP-Adressraum ermöglicht auch kontinuierliche Maßnahmen zur Verringerung des erforderlichen routingfähigen IP-Adressraums vor der Bereitstellung.

#### <a name="mdc-infrastructure-network"></a>MDC-Infrastrukturnetzwerk

Das Netzwerk der Größe /24 ist internen MDC-Komponenten zugeordnet, damit diese untereinander kommunizieren und Daten austauschen können. Dieses Subnetz kann extern von der MDC-Lösung zu Ihrem Rechenzentrum weitergeleitet werden. In diesem Subnetz sollten **keine** öffentlichen oder über das Internet weitergeleitete IP-Adressen verwendet werden. Dieses Netzwerk wird für das Border-Gerät angekündigt, die meisten der zugehörigen IP-Adressen werden jedoch durch Zugriffssteuerungslisten (ACLs) geschützt. Die für den Zugriff zugelassenen IP-Adressen liegen in einem kleinen Bereich, der einem Netzwerk der Größe /27 entspricht. Unter diesen IP-Adressen werden Dienste wie der privilegierte Endpunkt (PEP) und die MDC-Sicherung gehostet.

#### <a name="public-vip-network"></a>Öffentliches VIP-Netzwerk

Das öffentliche VIP-Netzwerk wird dem Netzwerkcontroller im MDC zugewiesen. Es ist kein logisches Netzwerk auf dem Switch. Die SLB nutzt den Adresspool und ordnet Netzwerke des Typs „/32“ für die Workloads von Mandanten zu. In der Routingtabelle auf dem Switch werden diese IP-Adressen aus dem Bereich /32 über das BGP (Border Gateway Protocol) als verfügbare Route angekündigt. Dieses Netzwerk enthält öffentliche Adressen, auf die extern zugegriffen werden kann. Die MDC-Infrastruktur reserviert die ersten 31 Adressen aus diesem öffentlichen VIP-Netzwerk. Der Rest wird von virtuellen Computern von Mandanten genutzt. Die Netzwerkgröße in diesem Subnetz kann von einem Minimum von „/26“ (64 Hosts) bis zu einem Maximum von „/22“ (1022 Hosts) reichen. Es wird empfohlen, ein Netzwerk des Typs „/24“ zu planen.

#### <a name="switch-infrastructure-network"></a>Switchinfrastrukturnetzwerk

Das Netzwerk der Größe /26 ist das Subnetz, das die routingfähigen Point-to-Point-IP-Subnetze der Größe /30 (zwei Host-IP-Adressen) und die Loopbacks enthält. Dies sind dedizierte Subnetze der Größe /32 für die In-Band-Switchverwaltung und die BGP-Router-ID. Dieser IP-Adressbereich muss außerhalb der MDC-Lösung zu Ihrem Rechenzentrum weitergeleitet werden können. Es kann sich sowohl um private als auch um öffentliche IP-Adressen handeln.

#### <a name="switch-management-network"></a>Switchverwaltungsnetzwerk

Dieses Netzwerk der Größe /29 (6 Host-IP-Adressen) dient zum Verbinden der Verwaltungsports der Switches. Das Netzwerk erlaubt einen Out-of-band-Zugriff für Bereitstellung, Verwaltung und Problembehandlung. Sie wird anhand des oben erwähnten Switchinfrastrukturnetzwerks berechnet.

#### <a name="isilon-network"></a>Isilon-Netzwerk

Es gibt zwei Netzwerke der Größe /25: eines auf dem ToR-Switch und eines der Größe /25 auf dem BMC-Switch für die Verwaltung.

## <a name="dns-design-overview"></a>DNS-Entwurfsübersicht

Für den Zugriff auf MDC-Endpunkte ( *portal* ,  *adminportal* ,  *management* , *adminmanagement* ) von außerhalb des MDC müssen Sie die MDC-DNS-Dienste in die DNS-Server integrieren, die die in MDC zu verwendenden DNS-Zonen hosten.

### <a name="mdc-dns-namespace"></a>MDC-DNS-Namespace

Sie müssen einige wichtige Informationen zum DNS angeben, wenn Sie ein MDC bereitstellen.

| **Feld** | **Beschreibung** | **Beispiel** |
|-----------|-----------------|-------------|
| Region | Der geografische Standort Ihrer MDC-Bereitstellung. | *east* |
| Externer Domänenname | Der Name der Zone, die Sie für Ihre MDC-Bereitstellung verwenden möchten. | *cloud.fabrikam.com* |
| Interner Domänenname | Der Name der internen Zone, die für die Infrastrukturdienste im MDC verwendet wird. Sie ist im Verzeichnisdienst integriert und privat (nicht von außerhalb der MDC-Bereitstellung erreichbar). | *azurestack.local* |
| DNS-Weiterleitungen | DNS-Server für die Weiterleitung von DNS-Abfragen, DNS-Zonen und -Einträge, die außerhalb des MDC gehostet werden, entweder im Unternehmensintranet oder im öffentlichen Internet. Sie können den Wert für die DNS-Weiterleitung nach der Bereitstellung mit dem Cmdlet  **Set-AzSDnsForwarder** bearbeiten. | |
| Namenspräfix (optional) | Das Namenspräfix für die Computernamen der Rolleninstanz Ihrer MDC-Infrastruktur. Wenn dieses nicht angegeben wird, lautet der Standardwert *azs* . | *azs* |

Der vollqualifizierte Domänenname (fully qualified domain name, FQDN) Ihrer MDC-Bereitstellung und -Endpunkte ist die Kombination aus den Parametern für die Region und den externen Domänennamen. Wenn die Werte aus den Beispielen der vorherigen Tabelle verwendet werden, lautet der vollqualifizierte Domänenname für diese MDC-Bereitstellung *east.cloud.fabrikam.com* .

Beispiele für einige Endpunkte dieser Bereitstellung würden wie folgende URLs aussehen:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

Folgende Bedingungen müssen erfüllt sein, um diesen DNS-Beispielnamespace für eine MDC-Bereitstellung zu verwenden:

- Die Zone fabrikam.com ist bei einer Domänenregistrierungsstelle, einem internen DNS-Server des Unternehmens oder bei beiden registriert. Die Registrierung hängt von Ihren Anforderungen für die Namensauflösung ab.

- Die untergeordnete Domäne cloud.fabrikam.com ist der Zone fabrikam.com untergeordnet.

- Die DNS-Server, die die Zonen fabrikam.com und cloud.fabrikam.com hosten, können von der MDC-Bereitstellung aus erreicht werden.

Um DNS-Namen für MDC-Endpunkte und -Instanzen von außerhalb des MDC aufzulösen, müssen Sie die DNS-Server integrieren. Dies schließt auch die Server ein, die die externe DNS-Zone für das MDC hosten, sowie die DNS-Server, die die zu verwendende übergeordnete Zone hosten.

#### <a name="dns-name-labels"></a>DNS-Namensbezeichnungen

MDCs unterstützen das Hinzufügen einer DNS-Namensbezeichnung zu einer öffentlichen IP-Adresse, um eine Namensauflösung für öffentliche IP-Adressen zu ermöglichen. Benutzer können DNS-Bezeichnungen als bequeme Möglichkeit nutzen, um im MDC gehostete Apps und Dienste anhand des Namens zu erreichen. Die DNS-Namensbezeichnung verwendet einen etwas anderen Namespace als die Infrastrukturendpunkte. Gemäß dem vorherigen Beispiel-Namespace lautet der Namespace für DNS-Namensbezeichnungen *\*.east.cloudapp.cloud.fabrikam.com* . 

Wenn ein Mandant den Wert  **MyApp** im Feld für die DNS-Namensbezeichnung einer Ressource mit öffentlicher IP-Adresse angibt, wird auf dem externen DNS-Server des MDC ein A-Datensatz für myapp in der Zone  **east.cloudapp.cloud.fabrikam.com** erstellt. Der resultierende vollqualifizierte Domänenname lautet *myapp.east.cloudapp.cloud.fabrikam.com* . 

Wenn Sie diese Funktion und diesen Namespace verwenden möchten, müssen Sie die DNS-Server integrieren. Dies schließt auch die Server ein, die die externe DNS-Zone für das MDC hosten, sowie die DNS-Server, die die zu verwendende übergeordnete Zone hosten. Dies ist ein anderer Namespace als der für die MDC-Dienstendpunkte verwendete Namespace. Daher müssen Sie eine zusätzliche Delegierung oder Regel für bedingte Weiterleitung erstellen.

Weitere Informationen zur Funktionsweise der DNS-Namensbezeichnung finden Sie unter „Verwenden von DNS in MDC“.

### <a name="resolution-and-delegation"></a>Auflösung und Delegierung

Es gibt zwei Arten von DNS-Server:

- Ein autoritativer DNS-Server hostet DNS-Zonen. Er antwortet nur auf DNS-Abfragen nach Einträgen für diese Zonen.

- Ein rekursiver DNS-Server hostet keine DNS-Zonen. Er reagiert auf alle DNS-Abfragen durch Aufrufen der autoritativen DNS-Server, um die benötigten Daten zu erfassen.

Das MDC enthält autoritative und rekursive DNS-Server. Die rekursiven Server dienen dazu, alle Namen außer denen der internen privaten Zone und der externen öffentlichen DNS-Zone für diese MDC-Bereitstellung aufzulösen.

### <a name="resolving-external-dns-names-from-mdc"></a>Auflösen von externen DNS-Namen vom MDC

Zum Auflösen von DNS-Namen für Endpunkte außerhalb des MDC (z. B. www.bing.com) müssen Sie dem MDC DNS-Server für die Weiterleitung von DNS-Anforderungen bereitstellen, für die das MDC nicht autoritativ ist. DNS-Server, an die das MDC Anforderungen weiterleitet, sind im Bereitstellungsarbeitsblatt erforderlich (im Feld „DNS-Weiterleitung“). Geben Sie für die Fehlertoleranz mindestens zwei Server in diesem Feld an. Ohne diese Werte führt die MDC-Bereitstellung zu einem Fehler. Sie können die Werte für die DNS-Weiterleitung nach der Bereitstellung mit dem Cmdlet  **Set-AzSDnsForwarder** bearbeiten.

## <a name="firewall-design-overview"></a>Übersicht über den Firewallentwurf

Es empfiehlt sich, das MDC mit einem Firewallgerät zu schützen. Firewalls können bei der Verteidigung vor Dingen wie DDoS-Angriffen (Denial of Service), bei der Angriffserkennung sowie der Inhaltsuntersuchung helfen. Sie können allerdings auch zu einem Durchsatzengpass für Azure-Speicherdienste wie Blobs, Tabellen und Warteschlangen werden.

Bei Verwendung eines getrennten Bereitstellungsmodus muss der AD FS-Endpunkt veröffentlicht werden. Weitere Informationen finden Sie im Artikel zur Rechenzentrumsintegration mithilfe von Identitäten.

Für die Endpunkte „Azure Resource Manager (Administrator)“, „Portal (Administrator)“ und „Schlüsseltresor (Administrator)“ ist nicht zwingend eine externe Veröffentlichung erforderlich. Als Dienstanbieter könnten Sie beispielsweise die Angriffsfläche verkleinern, indem Sie das MDC nur innerhalb Ihres Netzwerks (und nicht über das Internet) verwalten.

Bei einem Unternehmen kann das Unternehmensnetzwerk als externes Netzwerk fungieren. In diesem Szenario müssen Sie Endpunkte veröffentlichen, um das MDC über das Unternehmensnetzwerk betreiben zu können.

### <a name="network-address-translation"></a>Netzwerkadressenübersetzung

Die Netzwerkadressenübersetzung (Network Address Translation, NAT) ist die empfohlene Methode, mit der der virtuelle Bereitstellungscomputer (Deployment Virtual Machine, DVM) während der Bereitstellung auf externe Ressourcen zugreifen kann. Dies gilt auch für die ERCS-VMs (Emergency Recovery Console, Notfallwiederherstellungskonsole) bzw. den privilegierten Endpunkt (PEP) während der Registrierung und Problembehandlung.

Die NAT kann auch alternativ zu öffentlichen IP-Adressen im externen Netzwerk oder öffentlichen VIPs verwendet werden. Hiervon wird aber abgeraten, da diese die Benutzerfreundlichkeit des Mandanten verringert und die Komplexität erhöht. Eine Option wäre eine 1:1-NAT, die immer noch eine öffentliche IP pro Benutzer-IP im Pool erfordert. Eine andere Option ist eine n:1-NAT, die eine NAT-Regel pro Benutzer-VIP für alle Ports erfordert, die ein Benutzer verwenden könnte.

Die Verwendung der NAT für öffentliche VIPs hat folgende Nachteile:

- Der Verwaltungsaufwand für Firewallregeln erhöht sich, da Benutzer ihre eigenen Endpunkte und Veröffentlichungsregeln im SDN-Stapel (Software-Defined Networking) steuern. Benutzer müssen sich zur Veröffentlichung ihrer VIPs sowie zur Aktualisierung der Portliste an den MDC-Operator wenden. 
- Die Verwendung der NAT beeinträchtigt zwar die Benutzerfreundlichkeit, gibt dem Betreiber jedoch die volle Kontrolle über Veröffentlichungsanforderungen.
- In Hybrid Cloud-Szenarien mit Azure wird die Einrichtung eines VPN-Tunnels zu einem Endpunkt mit NAT nicht unterstützt.

### <a name="ssl-interception"></a>Abfangen von SSL

Zurzeit wird empfohlen, das Abfangen von SSL (z. B. Entschlüsselungsabladung) für den gesamten MDC-Datenverkehr zu deaktivieren. Wenn diese Funktion in zukünftigen Updates unterstützt wird, werden Anleitungen zum Aktivieren von SSL-Abfangen für das MDC bereitgestellt.

### <a name="edge-deployment-firewall-scenario"></a>Firewallszenario bei Edgebereitstellungen

MDCs werden in einer Edgebereitstellung direkt hinter dem Edgerouter oder der Firewall bereitgestellt. In diesen Szenarien darf die Firewall über der Grenze liegen (Szenario 1) und Firewallkonfigurationen der Typen Aktiv/Aktiv und Aktiv/Passiv unterstützen. Sie kann auch als Grenzgerät fungieren (Szenario 2). In diesem Fall werden nur Firewallkonfigurationen vom Typ Aktiv/Aktiv unterstützt. Für Szenario 2 ist ECMP (Equal-Cost Multi Path) mit BGP oder statischem Routing für das Failover erforderlich.

Öffentliche routingfähige IP-Adressen werden für den öffentlichen VIP-Pool aus dem externen Netzwerk zur Bereitstellungszeit angegeben. Aus Sicherheitsgründen wird in einem Edgeszenario davon **abgeraten** , öffentliche routingfähige IP-Adressen in anderen Netzwerken zu verwenden. Dadurch erhalten Benutzer eine vollständig selbstgesteuerte Cloudumgebung – genau wie bei einer öffentlichen Cloud (z.B. Azure).

 ![MDC-Szenario mit Edgefirewall](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Firewallszenario mit Unternehmensintranet oder -umkreisnetzwerk

Bei einer Bereitstellung im Intranet oder Umkreisnetzwerk eines Unternehmens wird das MDC in einer Firewall mit mehreren Zonen oder zwischen der Edgefirewall und der internen Firewall des Unternehmensnetzwerks bereitgestellt. Dann wird wie im Folgenden beschrieben der Datenverkehr zwischen dem sicheren Umkreisnetzwerk (oder der DMZ) und unsicheren Zonen verteilt:

- **Sichere Zone** : Dies ist das interne Netzwerk, das interne oder routingfähige Unternehmens-IP-Adressen verwendet. Das sichere Netzwerk kann unterteilt werden. Es kann über die Netzwerkadressenübersetzung der Firewall ausgehenden Zugriff auf das Internet haben. Normalerweise ist der Zugriff über das interne Netzwerk innerhalb Ihres Rechenzentrums möglich. Alle MDC-Netzwerke sollten sich in der sicheren Zone befinden, mit Ausnahme des öffentlichen VIP-Pools im externen Netzwerk.
- **Umkreiszone:** Das Umkreisnetzwerk wird dort eingesetzt, wo in der Regel externe oder Internet-Apps, wie z. B. Webserver, bereitgestellt werden. Es wird normalerweise durch eine Firewall überwacht, um Angriffe wie DDoS-Angriffe und Eindringversuche (Hacken) zu verhindern, während gleichzeitig angegebener eingehender Datenverkehr aus dem Internet zugelassen wird. In der DMZ-Zone sollte sich nur der öffentliche VIP-Pool des externen Netzwerks des MDC befinden. 
- **Unsichere Zone:** Dies ist das externe Netzwerk, das Internet. Das Bereitstellen von MDCs in der unsicheren Zone wird **nicht** empfohlen.

![Firewallszenario mit einem Umkreisnetzwerk](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>VPN-Entwurfsübersicht

Obwohl es sich bei VPN um ein Benutzerkonzept handelt, gibt es einige wichtige Überlegungen, die Besitzer und Operatoren einer Lösung kennen müssen.

Um Netzwerkdatenverkehr zwischen Ihrem virtuellen Azure-Netzwerk und Ihrem lokalen Standort senden zu können, müssen Sie für Ihr virtuelles Netzwerk zunächst ein Gateway für virtuelle Netzwerke (VPN) erstellen.

Ein VPN Gateway ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr über eine öffentliche Verbindung gesendet wird. Sie können VPN-Gateways für sicheren Datenverkehr zwischen einem virtuellen Netzwerk im MDC und einem virtuellen Netzwerk in Azure verwenden. Zudem können Sie den Datenverkehr zwischen einem virtuellen Netzwerk und einem anderen Netzwerk, das mit einem VPN-Gerät verbunden ist, sicher übertragen.

Der gewünschte Gatewaytyp wird beim Erstellen eines Gateways für virtuelle Netzwerke angegeben. MDCs unterstützen einen Typ von Gateway für virtuelle Netzwerke: VPN.

Jedes virtuelle Netzwerk kann über zwei Gateways für virtuelle Netzwerke, aber nur über eins von jedem Typ verfügen. Abhängig von den gewählten Einstellungen können Sie ggf. mehrere Verbindungen mit einem einzelnen VPN-Gateway erstellen. Ein Beispiel für diese Art Einrichtung ist eine Verbindungskonfiguration mit mehreren Standorten.

Lesen Sie vor dem Erstellen und Konfigurieren von VPN-Gateways für ein MDC die Überlegungen zu MDC-Netzwerken. Sie erfahren, wie sich die Konfigurationen für MDCs von Azure unterscheiden.

In Azure muss der Bandbreitendurchsatz für die ausgewählte VPN Gateway-SKU auf alle Verbindungen aufgeteilt werden, die mit dem Gateway hergestellt wurden. In einem MDC wird der Bandbreitenwert jedoch für die VPN-Gateway-SKU auf jede Verbindungsressource angewandt, die mit dem Gateway verbunden ist. Beispiel: 

- In Azure bietet die VPN Gateway-SKU „Basic“ eine aggregierte Durchsatzkapazität von etwa 100 Mbit/s. Wenn Sie zwei Verbindungen mit diesem VPN Gateway erstellen und eine dieser Verbindungen eine Bandbreite von 50 Mbit/s beansprucht, stehen für die andere Verbindung noch 50 Mbit/s zur Verfügung.

- In einem MDC wird jeder Verbindung mit der VPN-Gateway-SKU „Basic“ ein Durchsatz von 100 MBit/s zugeordnet.

### <a name="vpn-types"></a>VPN-Typen

Wenn Sie das Gateway des virtuellen Netzwerks für eine VPN-Gatewaykonfiguration erstellen, müssen Sie einen VPN-Typ angeben. Der ausgewählte VPN-Typ hängt von der Verbindungstopologie ab, die Sie erstellen möchten. Der VPN-Typ kann zudem von der verwendeten Hardware abhängen. S2S-Konfigurationen erfordern ein VPN-Gerät. Einige VPN-Geräte unterstützen nur einen bestimmten VPN-Typ.

>[!IMPORTANT]
> Derzeit unterstützen MDCs nur den routenbasierten VPN-Typ. Wenn Ihr Gerät nur richtlinienbasierte VPNs unterstützt, werden Verbindungen mit diesen Geräten vom MDC nicht unterstützt. Darüber hinaus unterstützen MDCs derzeit nicht die Verwendung von richtlinienbasierten Datenverkehrsselektoren für routenbasierte Gateways, da benutzerdefinierte IPsec-/IKE-Richtlinienkonfigurationen noch nicht unterstützt werden. 

- **PolicyBased** : Richtlinienbasierte VPNs verschlüsseln die Pakete und leiten sie über IPsec-Tunnel gemäß den IPsec-Richtlinien weiter. Die Richtlinien werden mit den Kombinationen aus Adresspräfixen zwischen Ihrem lokalen Netzwerk und dem MDC-VNET konfiguriert. Die Richtlinie (auch Datenverkehrsselektor genannt) ist in der Regel eine Zugriffsliste in der VPN-Gerätekonfiguration. **PolicyBased** wird in Azure, aber nicht in MDCs unterstützt. 
- **RouteBased** : Routenbasierte VPNs verwenden Routen, die in der IP-Weiterleitungs- oder Routingtabelle konfiguriert sind. Über die Routen werden die Pakete an die entsprechenden Tunnelschnittstellen weitergeleitet. An den Tunnelschnittstellen werden die Pakete dann ver- bzw. entschlüsselt. Die Richtlinie (bzw. der Datenverkehrsselektor) für  **routenbasierte** VPNs wird im Any-to-Any-Format (bzw. mithilfe von Platzhaltern) konfiguriert. Sie können standardmäßig nicht geändert werden. Der Wert für einen **routenbasierten** VPN-Typ ist **RouteBased** .

### <a name="configuring-a-vpn-gateway"></a>Konfigurieren eines VPN-Gateways

Eine VPN Gateway-Verbindung basiert auf mehreren, mit spezifischen Einstellungen konfigurierten Ressourcen. Die meisten dieser Ressourcen können separat konfiguriert werden. In manchen Fällen ist allerdings eine bestimmte Reihenfolge einzuhalten.

#### <a name="settings"></a>Einstellungen

Die Einstellungen, die Sie für die einzelnen Ressourcen auswählen, sind für die erfolgreiche Herstellung einer Verbindung entscheidend.

Dieser Artikel hilft Ihnen bei Folgendem:

- Gatewaytypen, VPN-Typen und Verbindungstypen.
- Gatewaysubnetze, lokale Netzwerkgateways und andere Ressourceneinstellungen, die Sie berücksichtigen sollten.

#### <a name="connection-topology-diagrams"></a>Verbindungstopologiediagramme

Für VPN-Gatewayverbindungen sind verschiedene Konfigurationen verfügbar. Ermitteln Sie, welche Konfiguration am besten zu Ihren Anforderungen passt. In den folgenden Abschnitten können Sie sich Informationen und Topologiediagramme zu den folgenden VPN-Gatewayverbindungen ansehen:

- Verfügbares Bereitstellungsmodell
- Verfügbare Konfigurationstools
- Direkte Links zu Artikeln, sofern verfügbar

Orientieren Sie sich bei der Wahl einer geeigneten Verbindungstopologie an den Diagrammen und Beschreibungen in den folgenden Abschnitten. Die Diagramme zeigen die grundlegenden Topologien, aber Sie können auch komplexere Topologien erstellen, indem Sie die Diagramme als Anhaltspunkte verwenden.

#### <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site-to-Site und Multi-Site (IPsec-/IKE-VPN-Tunnel)

##### <a name="site-to-site"></a>Site-to-Site

Eine VPN Gateway-S2S-Verbindung (Site-to-Site) ist eine Verbindung über einen IPsec-/IKE-VPN-Tunnel (IKEv2). Dieser Verbindungstyp erfordert ein lokales VPN-Gerät, das einer öffentlichen IP-Adresse zugewiesen ist. Dieses Gerät darf sich nicht hinter einer NAT befinden. S2S-Verbindungen können für standortübergreifende Konfigurationen und Hybridkonfigurationen verwendet werden.

##### <a name="multi-site"></a>Multi-Site

Bei einer Multi-Site-Verbindung handelt es sich um eine Abwandlung der Site-to-Site-Verbindung. Sie erstellen mehrere VPN-Verbindungen über Ihr Gateway für virtuelle Netzwerke, durch die in der Regel mehrere lokale Standorte verbunden werden. Bei Verwendung mehrerer Verbindungen müssen Sie den VPN-Typ „RouteBased“ verwenden (wird bei Verwendung klassischer VNETs als dynamisches Gateway bezeichnet). Da jedes virtuelle Netzwerk nur über ein einzelnes VPN-Gateway verfügen kann, wird die verfügbare Bandbreite von allen über das Gateway laufenden Verbindungen gemeinsam genutzt.

### <a name="gateway-skus"></a>Gateway-SKUs

Beim Erstellen eines Gateways des virtuellen Netzwerks für das MDC geben Sie die gewünschte Gateway-SKU an. Folgende SKUs für VPN-Gateways werden unterstützt:

- Basic
- Standard
- Leistung

Wenn Sie eine höhere Gateway-SKU auswählen, werden dem Gateway mehr CPUs und eine höhere Netzwerkbandbreite zugeordnet. Dadurch kann das Gateway einen höheren Netzwerkdurchsatz an das virtuelle Netzwerk bewältigen.

Die Gateway-SKU „Höchstleistung“ wird ausschließlich mit ExpressRoute verwendet und von MDCs nicht unterstützt. Beachten Sie bei der Auswahl der SKU Folgendes:

- Richtlinienbasierte Gateways werden von MDCs nicht unterstützt.
- BGP wird in der Basic-SKU nicht unterstützt.
- Die gemeinsame Verwendung von VPN Gateway und ExpressRoute wird in MDCs nicht unterstützt.

#### <a name="gateway-availability"></a>Gatewayverfügbarkeit

Hochverfügbarkeitsszenarien können nur in der Verbindungs-SKU „Hochleistungsgateway“ konfiguriert werden. Im Gegensatz zu Azure, wo Verfügbarkeit durch sowohl Aktiv/Aktiv- als auch Aktiv/Passiv-Konfigurationen angeboten wird, unterstützen MDCs nur die Aktiv/Passiv-Konfiguration.

#### <a name="failover"></a>Failover

Es gibt in einem MDC drei mehrinstanzenfähige VMs mit Gatewayinfrastruktur. Zwei dieser virtuellen Computer befinden sich im aktiven Modus, während sich die dritte im redundanten Modus befindet. Aktive virtuelle Computer ermöglichen die Erstellung von VPN-Verbindungen auf sich, und die redundante VM akzeptiert nur VPN-Verbindungen, wenn ein Failover eintritt. Wenn eine aktive Gateway-VM nicht mehr verfügbar ist, führt die VPN-Verbindung nach einem kurzen Zeitraum (ein paar Sekunden) der Verbindungsunterbrechung ein Failover zur redundanten VM durch.

### <a name="estimated-aggregate-throughput-by-sku"></a>Voraussichtlicher aggregierter Durchsatz nach SKU

In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz nach Gateway-SKU angegeben:

| | **VPN Gateway-Durchsatz (1)** | **Max. IPsec-Tunnel für VPN Gateway (2)** |
|-|--------------------------------|---------------------------------------|
| Basic-SKU (3) | 100 MBit/s | 20 |
| Standard-SKU | 100 MBit/s | 20 |
| HighPerformance-SKU | 200 MBit/s | 10 |

Anmerkungen zur Tabelle

1\. Der VPN-Durchsatz ist kein garantierter Durchsatz für standortübergreifende Verbindungen über das Internet. Hierbei wird der maximal mögliche Durchsatz gemessen.  
2\. Bei der maximalen Tunnelanzahl handelt es sich um die Summe pro MDC-Bereitstellung für alle Abonnements.  
3\. Das BGP-Routing wird in der Basic-SKU nicht unterstützt.

>[!IMPORTANT]
> Zwischen zwei MDC-Bereitstellungen kann nur eine Site-to-Site-VPN-Verbindung hergestellt werden. Der Grund: Wegen einer Einschränkung ist für die Plattform nur eine einzelne VPN-Verbindung mit der gleichen IP-Adresse zulässig. Da MDCs das mehrinstanzenfähige Gateway nutzen, das eine öffentliche IP-Adresse für alle VPN-Gateways im MDC-System verwendet, kann zwischen zwei MDC-Systemen nur eine VPN-Verbindung hergestellt werden. 
>
> Diese Einschränkung gilt auch für das Herstellen mehrerer Site-to-Site-VPN-Verbindungen mit einem beliebigen VPN-Gateway, das eine einzige IP-Adresse nutzt. Das MDC lässt nicht zu, dass mehrere Ressourcen des lokalen Netzwerkgateways mit der gleichen IP-Adresse erstellt werden.

### <a name="ipsecike-parameters"></a>IPsec-/IKE-Parameter

Wenn Sie in einem MDC eine VPN-Verbindung einrichten, müssen Sie die Verbindung an beiden Enden konfigurieren. Wenn Sie eine VPN-Verbindung zwischen einem MDC und einem Hardwaregerät konfigurieren, müssen Sie an diesem Gerät möglicherweise zusätzliche Einstellungen vornehmen. Ein Beispiel ist ein Switch oder Router, der als VPN-Gateway fungiert.

Im Gegensatz zu Azure, wo mehrere Angebote als Initiator und Antwortdienst unterstützt werden, unterstützen MDCs standardmäßig lediglich ein einzelnes Angebot. Wenn Sie verschiedene IPSec/IKE-Einstellungen mit Ihrem VPN-Gerät verwenden müssen, stehen Ihnen weitere Einstellungen zur Verfügung, um Ihre Verbindung manuell zu konfigurieren.

#### <a name="ike-phase-1-main-mode-parameters"></a>Parameter der IKE-Phase 1 (Hauptmodus)

| **Eigenschaft** | **Wert** |
|--------------|-----------|
| IKE-Version | IKEv2 |
| Diffie-Hellman-Gruppe | ECP384 |
| Authentifizierungsmethode | Vorab ausgetauschter Schlüssel |
| Verschlüsselung und Hashalgorithmen | AES256, SHA384 |
| SA-Gültigkeitsdauer (Zeit) | 28.800 Sekunden |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Parameter der IKE-Phase 2 (Schnellmodus)

| **Eigenschaft** | **Wert** |
|--------------|-----------|
| IKE-Version | IKEv2 |
| Verschlüsselung und Hashalgorithmen (Verschlüsselung) | GCMAES256 |
| Verschlüsselung und Hashalgorithmen (Authentifizierung) | GCMAES256 |
| SA-Gültigkeitsdauer (Zeit) | 27.000 Sekunden |
| SA-Gültigkeitsdauer (KB) | 33.553.408 |
| Perfect Forward Secrecy (PFS) | ECP384 |
| Dead Peer Detection | Unterstützt |

### <a name="configure-custom-ipsecike-connection-policies"></a>Konfigurieren benutzerdefinierter IPsec-/IKE-Verbindungsrichtlinien

Der IPsec- und IKE-Protokollstandard unterstützt ein breites Spektrum von Kryptografiealgorithmen in verschiedenen Kombinationen. Informationen zu den bei MDCs für die Erfüllung der Anforderungen an Compliance oder Sicherheit unterstützten Parametern finden Sie unter „IPsec-/IKE-Parameter“.

Dieser Artikel enthält eine Anleitung zum Erstellen und Konfigurieren einer IPsec/IKE-Richtlinie und zu ihrem Anwenden auf eine neue oder vorhandene Verbindung.

#### <a name="considerations"></a>Überlegungen

Beachten Sie die folgenden wichtigen Informationen, wenn Sie diese Richtlinien verwenden:

- Die IPsec/IKE-Richtlinie kann nur für die Gateway-SKUs Standard und HighPerformance (routenbasiert) verwendet werden.
- Pro Verbindung kann jeweils nur eine Richtlinienkombination angegeben werden.
- Sie müssen alle Algorithmen und Parameter für IKE (Hauptmodus) und IPsec (Schnellmodus) angeben. Eine teilweise Angabe von Richtlinien ist unzulässig.
- Vergewissern Sie sich in den Spezifikationen Ihres VPN-Geräteanbieters, dass die Richtlinie von Ihren lokalen VPN-Geräten unterstützt wird. Site-to-Site-Verbindungen können nicht hergestellt werden, wenn die Richtlinien inkompatibel sind.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Workflow zum Erstellen und Festlegen der IPsec/IKE-Richtlinie

In diesem Abschnitt wird der Workflow zum Erstellen und Aktualisieren der IPsec/IKE-Richtlinie für eine Site-to-Site-VPN-Verbindung beschrieben:

1. Erstellen eines virtuellen Netzwerks und eines VPN-Gateways
2. Erstellen eines Gateways für das lokale Netzwerk für standortübergreifende Verbindungen
3. Erstellen einer IPsec/IKE-Richtlinie mit ausgewählten Algorithmen und Parametern
4. Erstellen einer IPsec-Verbindung mit der IPsec/IKE-Richtlinie
5. Hinzufügen/Aktualisieren/Entfernen einer IPsec/IKE-Richtlinie für eine vorhandene Verbindung

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Unterstützte Kryptografiealgorithmen und Schlüsselstärken

Die folgende Tabelle gibt Aufschluss über die unterstützten Kryptografiealgorithmen und Schlüsselstärken, die von MDC-Kunden konfiguriert werden können:

| **IPsec/IKEv2** | **Optionen** |
|-----------------|-------------|
| IKEv2-Verschlüsselung | AES256, AES192, AES128, DES3, DES |
| IKEv2-Integrität | SHA384, SHA256, SHA1, MD5 |
| DH-Gruppe | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Keine |
| IPsec-Verschlüsselung | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, keine |
| IPsec-Integrität | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-Gruppe | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, keine |
| QM-SA-Gültigkeitsdauer | (Optional: Wenn kein Wert angegeben wird, werden die Standardwerte verwendet.) |
| | Sekunden (ganze Zahl; min. 300/Standard: 27.000 Sekunden) |
| | KB (ganze Zahl; min. 1.024/Standard: 102.400.000 KB) |
| Datenverkehrsselektor | Richtlinienbasierte Datenverkehrsselektoren werden von MDCs nicht unterstützt. |

Ihre lokale VPN-Gerätekonfiguration muss folgenden Algorithmus- und Parameterangaben für die Azure-IPsec-/IKE-Richtlinie entsprechen oder selbige enthalten:

 - IKE-Verschlüsselungsalgorithmus (Hauptmodus/Phase 1)
 - IKE-Integritätsalgorithmus (Hauptmodus/Phase 1)
 - DH-Gruppe (Hauptmodus/Phase 1)
 - IPsec-Verschlüsselungsalgorithmus (Schnellmodus/Phase 2)
 - IPsec-Integritätsalgorithmus (Schnellmodus/Phase 2)
 - PFS-Gruppe (Schnellmodus/Phase 2)
 - Bei der SA-Lebensdauer handelt es sich lediglich um eine lokale Angabe, die nicht übereinstimmen muss.

Wenn GCMAES als IPsec-Verschlüsselungsalgorithmus verwendet wird, müssen Sie denselben GCMAES-Algorithmus und dieselbe Schlüssellänge für die IPsec-Integrität auswählen. Beispiel: Verwendung von GCMAES128 für beide.

Für die obige Tabelle gilt Folgendes:

 - IKEv2 entspricht Hauptmodus oder Phase 1
 - IPsec entspricht Hauptmodus oder Phase 2
 - Die DH-Gruppe gibt die im Hauptmodus oder in Phase 1 verwendete Diffie-Hellmen-Gruppe an
 - Die PFS-Gruppe gibt die im Schnellmodus oder in Phase 2 verwendete Diffie-Hellmen-Gruppe an
- Die SA-Lebensdauer von IKEv2 (Hauptmodus) ist für die MDC-VPN-Gateways auf 28.800 Sekunden festgelegt.

Die folgende Tabelle enthält die entsprechenden Diffie-Hellman-Gruppen, die von der benutzerdefinierten Richtlinie unterstützt werden:

| **Diffie-Hellman-Gruppe** | **DHGroup** | **PFSGroup** | **Schlüssellänge** |
|--------------------------|-------------|--------------|----------------|
| 1 | DHGroup1 | PFS1 | 768-Bit-MODP |
| 2 | DHGroup2 | PFS2 | 1024-Bit-MODP |
| 14 | DHGroup14 | PFS2048 | 2048-Bit-MODP |
| | DHGroup2048 | | |
| 19 | ECP256 | ECP256 | 256-Bit-ECP |
| 20 | ECP384 | ECP384 | 384-Bit-ECP |
| 24 | DHGroup24 | PFS24 | 2048-Bit-MODP |

### <a name="connect-mdc-to-azure-using-azure-expressroute"></a>Herstellen einer Verbindung zwischen einem MDC und Azure mithilfe von Azure ExpressRoute

#### <a name="overview-assumptions-and-prerequisites"></a>Übersicht, Annahmen und Voraussetzungen

Mit dem Dienst Azure ExpressRoute können Sie Ihre lokalen Netzwerke in die Microsoft-Cloud erweitern. Sie verwenden eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird. Bei ExpressRoute handelt es sich nicht um eine VPN-Verbindung über das öffentliche Internet.

Weitere Informationen zu Azure ExpressRoute finden Sie unter ExpressRoute-Übersicht.

#### <a name="assumptions"></a>Annahmen

In diesem Artikel wird Folgendes vorausgesetzt:

- Sie verfügen über ausreichende praktische Kenntnisse von Azure.
- Sie verfügen über grundlegende Kenntnisse von MDCs.
- Sie verfügen über grundlegende Kenntnisse von Netzwerken.

#### <a name="prerequisites"></a>Voraussetzungen

Für die Verbindungsherstellung zwischen dem MDC und Azure mithilfe von ExpressRoute gelten die folgenden Anforderungen:

- Eine bereitgestellte ExpressRoute-Verbindung über einen Konnektivitätsanbieter.
- Ein Azure-Abonnement zum Erstellen einer ExpressRoute-Verbindung und von VNets in Azure.
- Ein Router, der Folgendes unterstützt:
  - Site-to-Site-VPN-Verbindungen zwischen seiner LAN-Schnittstelle und dem mehrinstanzenfähigen Azure Stack-Gateway
  - Erstellung mehrerer VRF-Instanzen (Virtual Routing and Forwarding), falls die MDC-Bereitstellung mehrere Mandanten umfasst

- Ein Router, der über Folgendes verfügt:
  - Einen WAN-Port, der mit der ExpressRoute-Verbindung verbunden ist
  - Einen LAN-Port, der mit dem mehrinstanzenfähigen MDC-Gateway verbunden ist

#### <a name="expressroute-network-architecture"></a>ExpressRoute-Netzwerkarchitektur

Die folgende Abbildung zeigt die MDC- und die Azure-Umgebung nach der Einrichtung von ExpressRoute anhand der Beispiele in diesem Artikel:

 ![ExpressRoute-Netzwerkarchitektur](media/network-introduction/expressroute-network-architecture-60.png) 

