---
title: Einführung in Azure Stack Hub Ruggedized-Netzwerke.
description: Erfahren Sie mehr über die Netzwerkfunktionalität für das Azure Stack Hub Ruggedized-Gerät.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 3a6312a81632bffbb128049088119b30de79a732
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867800"
---
# <a name="azure-stack-hub-ruggedized-network-introduction"></a>Einführung in Azure Stack Hub Ruggedized-Netzwerke

## <a name="network-design-overview"></a>Übersicht über den Netzwerkentwurf

### <a name="physical-network-design"></a>Entwurf des physischen Netzwerks

Die Azure Stack Hub Ruggedized-Lösung erfordert eine zuverlässige und hoch verfügbare physische Infrastruktur, um Vorgänge und Dienste zu unterstützen. Uplinks zwischen TOR-Switches und Borderswitches sind auf SFP+- oder SFP28-Medien und auf Geschwindigkeiten von 1 GBit/s, 10 GBit/s oder 25 GBit/s beschränkt. Angaben zur Verfügbarkeit erhalten Sie bei Ihrem OEM-Hardwarehersteller (Original Equipment Manufacturer).

In der folgenden Abbildung wird der empfohlene Entwurf für Azure Stack Hub Ruggedized dargestellt.

 ![Physisches Azure Stack Hub Ruggedized-Netzwerk](media/network-introduction/physical-network-design-basic.png) 

### <a name="logical-network-design"></a>Entwurf des logischen Netzwerks

Der Entwurf eines logischen Netzwerks stellt eine Abstraktion einer physischen Netzwerkinfrastruktur dar. Sie dienen zum Organisieren und Vereinfachen von Netzwerkzuweisungen für Hosts, VMs und Dienste. Im Rahmen der Erstellung des logischen Netzwerks werden Netzwerkstandorte erstellt, um Folgendes zu definieren:
- Virtuelle lokale Netzwerke (VLANs)
- IP-Subnetze
- IP-Subnetz-VLAN-Paare

Alle diese Elemente werden dem logischen Netzwerk an jedem physischen Standort zugeordnet.

Die folgende Tabelle zeigt die logischen Netzwerke und die zugehörigen IPv4-Subnetzbereiche, die Sie berücksichtigen müssen:

| **Logisches Netzwerk**   | **Beschreibung**  | **Größe** |
|-----------------------|------------------|----------|
| Öffentliche virtuelle IP-Adresse (VIP)       | Azure Stack Hub Ruggedized verwendet insgesamt 31 Adressen aus diesem Netzwerk. Acht öffentliche IP-Adressen werden für einige Azure Stack Hub Ruggedized-Dienste verwendet, der Rest wird von Mandanten-VMs verwendet. Wenn Sie App Service und SQL-Ressourcenanbieter verwenden möchten, werden sieben weitere Adressen verwendet. Die verbleibenden 15 IP-Adressen sind für zukünftige Azure-Dienste reserviert. | /26 (62 Hosts) bis<br>/22 (1022 Hosts)<br><br>Empfohlen = /24 (254 Hosts) |
| Switchinfrastruktur | Point-to-Point-IP-Adressen für Routingzwecke, dedizierte Switchverwaltungsschnittstellen und Loopbackadressen, die dem Switch zugewiesen sind. | /26 |
| Infrastruktur        | Wird für die Kommunikation interner Azure Stack Hub Ruggedized-Komponenten verwendet. | /24 |
| Privat               | Für das Speichernetzwerk, private VIPs, Infrastrukturcontainer und für andere interne Funktionen | /20 |
| Baseboard-Verwaltungscontroller | Für die Kommunikation mit den Baseboard-Verwaltungscontrollern auf den physischen Hosts | /26 |

### <a name="network-infrastructure"></a>Netzwerkinfrastruktur

Die Netzwerkinfrastruktur für Azure Stack Hub Ruggedized besteht aus mehreren logischen Netzwerken, die auf den Switches konfiguriert sind. Das folgende Diagramm zeigt diese logischen Netzwerke und wie sie auf ToR- (Top-of-Rack), BMC- (Baseboard Management Controller, Baseboard-Verwaltungscontroller) und Grenzswitches (Kundennetzwerk) integriert werden können.

Diagramm des logischen Azure Stack Hub Ruggedized-Netzwerks:

 ![Logisches Azure Stack Hub Ruggedized-Netzwerk](media/network-introduction/logical-network-design.png) 

#### <a name="bmc-network"></a>BMC-Netzwerk

Dieses Netzwerk ist für die Verbindung aller BMCs (auch Dienstprozessoren genannt) mit dem Verwaltungsnetzwerk bestimmt. Zum Beispiel: iDRAC, iLO, iBMC usw. Für die Kommunikation mit einem BMC-Knoten wird nur ein BMC-Konto verwendet. Falls vorhanden, befindet sich der Hardware Lifecycle Host (HLH) in diesem Netzwerk und kann OEM-spezifische Software für die Hardwarewartung oder -überwachung bereitstellen.

Der HLH hostet auch den virtuellen Bereitstellungscomputer (Deployment VM, DVM). Die DVM wird im Rahmen der Azure Stack Hub Ruggedized-Bereitstellung verwendet und nach Abschluss der Bereitstellung entfernt. In verbundenen Szenarien benötigt die DVM Internetzugriff, um mehrere Komponenten testen und überprüfen sowie darauf zugreifen zu können. Diese Komponenten können sich innerhalb oder außerhalb Ihres Unternehmensnetzwerks befinden (z. B.: NTP, DNS, und Azure). Weitere Informationen zu Konnektivitätsanforderungen finden Sie im NAT-Abschnitt des Artikels zur Azure Stack Hub Ruggedized-Firewallintegration.

#### <a name="private-network"></a>Privates Netzwerk

Das Netzwerk der Größe /20 (4.096 Host-IP-Adressen) ist ein privates Netzwerk im Azure Stack Hub Ruggedized-Bereich. Es wird nicht über die Grenzswitchgeräte des Azure Stack Hub Ruggedized-Bereichs hinaus erweitert. Dieses Netzwerk ist in mehrere Subnetze unterteilt, z. B.:

- **Speichernetzwerk:** Ein Netzwerk der Größe „/25“ (128 Host-IP-Adressen), das zur Unterstützung der Verwendung von „Direkte Speicherplätze“ und SMB-Speicherdatenverkehr (Server Message Block) sowie der Livemigration von virtuellen Computern verwendet wird.
- **Internes VIP-Netzwerk (Virtuelle IP-Adresse):** Ein Netzwerk des Typs „/25“, das ausschließlich internen VIPs für den softwaregestützten Lastenausgleich vorbehalten ist.
- **Containernetzwerk:** Ein dediziertes Netzwerk der Größe /23 (512 IP-Adressen) für internen Datenverkehr zwischen Containern mit Infrastrukturdiensten

Die Größe für das private Netzwerk in einem privaten IP-Adressraum beträgt /20 (4.096 IP-Adressen). Das Netzwerk ist ein privates Netzwerk im Azure Stack Hub Ruggedized-System. In ihm erfolgt keine Weiterleitung über die Grenzswitchgeräte des Azure Stack Hub Ruggedized-Systems hinaus, und es kann in mehreren Azure Stack Hub Ruggedized-Systemen wiederverwendet werden. Das Netzwerk ist zwar privat für Azure Stack Hub Ruggedized, darf sich aber nicht mit anderen Netzwerken im Rechenzentrum überschneiden. Als Leitfaden für den privaten IP-Adressraum empfehlen wir RFC 1918.

Der private IP-Adressraum /20 ist in mehrere Netzwerke unterteilt, die in zukünftigen Releases die Ausführung der Azure Stack Hub Ruggedized-Systeminfrastruktur in Containern ermöglichen. Details finden Sie in den Versionshinweisen zu Release 1910. Dieser neue private IP-Adressraum ermöglicht auch kontinuierliche Maßnahmen zur Verringerung des erforderlichen routingfähigen IP-Adressraums vor der Bereitstellung.

#### <a name="azure-stack-hub-ruggedized-infrastructure-network"></a>Azure Stack Hub Ruggedized-Infrastrukturnetzwerk

Das Netzwerk der Größe /24 ist für interne Azure Stack Hub Ruggedized-Komponenten vorgesehen, damit diese untereinander kommunizieren und Daten austauschen können. In diesem Subnetz ist ein externes Routing von der Azure Stack Hub Ruggedized-Lösung zu Ihrem Rechenzentrum möglich. In diesem Subnetz sollten **keine** öffentlichen oder über das Internet weitergeleitete IP-Adressen verwendet werden. Dieses Netzwerk wird zwar für den Grenzbereich angekündigt, die meisten der zugehörigen IP-Adressen werden jedoch durch Zugriffssteuerungslisten (Access Control Lists, ACLs) geschützt. Die für den Zugriff zugelassenen IP-Adressen liegen in einem kleinen Bereich, der einem Netzwerk der Größe /27 entspricht. Unter diesen IP-Adressen werden Dienste wie der privilegierte Endpunkt (PEP) und die Azure Stack Hub Ruggedized-Sicherung gehostet.

#### <a name="public-vip-network"></a>Öffentliches VIP-Netzwerk

Das öffentliche VIP-Netzwerk wird dem Netzwerkcontroller in Azure Stack Hub Ruggedized zugewiesen. Es ist kein logisches Netzwerk auf dem Switch. Die SLB nutzt den Adresspool und ordnet Netzwerke des Typs „/32“ für die Workloads von Mandanten zu. In der Routingtabelle auf dem Switch werden diese IP-Adressen aus dem Bereich /32 über das BGP (Border Gateway Protocol) als verfügbare Route angekündigt. Dieses Netzwerk enthält öffentliche Adressen, auf die extern zugegriffen werden kann. Die Azure Stack Hub Ruggedized-Infrastruktur reserviert die ersten 31 Adressen aus diesem öffentlichen VIP-Netzwerk, der Rest wird von Mandanten-VMs verwendet. Die Netzwerkgröße in diesem Subnetz kann von einem Minimum von „/26“ (64 Hosts) bis zu einem Maximum von „/22“ (1022 Hosts) reichen. Es wird empfohlen, ein Netzwerk der Größe /24 zu planen.

#### <a name="switch-infrastructure-network"></a>Switchinfrastrukturnetzwerk

Das Netzwerk der Größe /26 ist das Subnetz, das die routingfähigen Point-to-Point-IP-Subnetze der Größe /30 (zwei Host-IP-Adressen) und die Loopbacks enthält. Dies sind dedizierte Subnetze der Größe /32 für die In-Band-Switchverwaltung und die BGP-Router-ID. Dieser IP-Adressbereich muss außerhalb der Azure Stack Hub Ruggedized-Lösung zu Ihrem Rechenzentrum geroutet werden können. Es kann sich sowohl um private als auch um öffentliche IP-Adressen handeln.

#### <a name="switch-management-network"></a>Switchverwaltungsnetzwerk

Dieses Netzwerk der Größe /29 (6 Host-IP-Adressen) dient zum Verbinden der Verwaltungsports der Switches. Das Netzwerk erlaubt einen Out-of-band-Zugriff für Bereitstellung, Verwaltung und Problembehandlung. Sie wird anhand des oben erwähnten Switchinfrastrukturnetzwerks berechnet.

## <a name="dns-design-overview"></a>DNS-Entwurfsübersicht

Um von außerhalb eines Azure Stack Hub Ruggedized-Rechenzentrums auf Azure Stack Hub Ruggedized-Endpunkte wie *portal*, *adminportal*, *management* und *adminmanagement* zugreifen zu können, müssen Sie die Azure Stack Hub Ruggedized-DNS-Dienste in die DNS-Server integrieren, die die DNS-Zonen hosten, die Sie in Azure Stack Hub Ruggedized verwenden möchten.

### <a name="azure-stack-hub-ruggedized-dns-namespace"></a>DNS-Namespace von Azure Stack Hub Ruggedized

Sie müssen einige wichtige Informationen zum DNS angeben, wenn Sie Azure Stack Hub Ruggedized bereitstellen.

| **Feld** | **Beschreibung** | **Beispiel** |
|-----------|-----------------|-------------|
| Region                   | Der geografische Standort Ihrer Azure Stack Hub Ruggedized-Bereitstellung. | Osten               |
| Externer Domänenname     | Der Name der Zone, die Sie für Ihre Azure Stack Hub Ruggedized-Bereitstellung verwenden möchten. | cloud.fabrikam.com |
| Interner Domänenname     | Der Name der internen Zone, die für die Infrastrukturdienste in Azure Stack Hub Ruggedized verwendet wird. Sie ist in den Verzeichnisdienst integriert und privat (nicht von außerhalb der Azure Stack Hub Ruggedized-Bereitstellung erreichbar). | azurestack.local   |
| DNS-Weiterleitungen           | DNS-Server, die zum Weiterleiten von DNS-Abfragen verwendet werden, DNS-Zonen und -Einträge, die außerhalb von Azure Stack Hub Ruggedized gehostet werden – entweder im Unternehmensintranet oder im öffentlichen Internet. Sie können den Wert für die DNS-Weiterleitung nach der Bereitstellung mit dem **Set-AzSDnsForwarder**-Cmdlet bearbeiten. |                    |
| Namenspräfix (optional) | Das Namenspräfix, das die Computernamen der Rolleninstanz Ihrer Azure Stack Hub Ruggedized-Infrastruktur aufweisen sollen. Wenn dieses nicht angegeben ist, lautet der Standardwert „azs“. | azs                |

Der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) Ihrer Azure Stack Hub Ruggedized-Bereitstellung und -Endpunkte ist die Kombination aus den Parametern „Region“ und „externer Domänenname“. Wenn die Werte aus den Beispielen der vorherigen Tabelle verwendet werden, lautet der vollqualifizierte Domänenname für diese Azure Stack Hub Ruggedized-Bereitstellung *east.cloud.fabrikam.com*.

Beispiele für einige Endpunkte dieser Bereitstellung würden wie folgende URLs aussehen:

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

Für die Verwendung dieses DNS-Beispielnamespace für eine Azure Stack Hub Ruggedized-Bereitstellung gelten die folgenden Bedingungen:

- Die Zone fabrikam.com ist bei einer Domänenregistrierungsstelle, einem internen DNS-Server des Unternehmens oder bei beiden registriert. Die Registrierung hängt von Ihren Anforderungen für die Namensauflösung ab.
- Die untergeordnete Domäne cloud.fabrikam.com ist der Zone fabrikam.com untergeordnet.
- Die DNS-Server, die die Zonen „fabrikam.com“ und „cloud.fabrikam.com“ hosten, können von der Azure Stack Hub Ruggedized-Bereitstellung aus erreicht werden.

Sie müssen die DNS-Server integrieren, um die DNS-Namen für Azure Stack Hub Ruggedized-Endpunkte und -Instanzen außerhalb von Azure Stack Hub Ruggedized aufzulösen. Dies schließt auch die Server ein, die die externe DNS-Zone für Azure Stack Hub Ruggedized hosten, sowie die DNS-Server, die die zu verwendende übergeordnete Zone hosten.

#### <a name="dns-name-labels"></a>DNS-Namensbezeichnungen

Azure Stack Hub Ruggedized unterstützt das Hinzufügen einer DNS-Namensbezeichnung zu einer öffentlichen IP-Adresse, um eine Namensauflösung für öffentliche IP-Adressen zu ermöglichen. Benutzer können DNS-Bezeichnungen als bequeme Möglichkeit nutzen, um in Azure Stack Hub Ruggedized gehostete Apps und Dienste anhand des Namens zu erreichen. Die DNS-Namensbezeichnung verwendet einen etwas anderen Namespace als die Infrastrukturendpunkte. Gemäß dem vorherigen Beispiel-Namespace lautet der Namespace für DNS-Namensbezeichnungen *\*.east.cloudapp.cloud.fabrikam.com*.

Wenn ein Mandant den Wert **MyApp** im Feld für den DNS-Namen einer Ressource mit öffentlicher IP-Adresse angibt, wird auf dem externen DNS-Server von Azure Stack Hub Ruggedized ein A-Eintrag für **myapp** in der Zone **east.cloudapp.cloud.fabrikam.com** erstellt. Der resultierende vollqualifizierte Domänenname lautet *myapp.east.cloudapp.cloud.fabrikam.com*.

Wenn Sie diese Funktion und diesen Namespace verwenden möchten, müssen Sie die DNS-Server integrieren. Dies schließt auch die Server ein, die die externe DNS-Zone für Azure Stack Hub Ruggedized hosten, sowie die DNS-Server, die die zu verwendende übergeordnete Zone hosten. Dies ist ein anderer Namespace als der für die Azure Stack Hub Ruggedized-Dienstendpunkte verwendete Namespace. Daher müssen Sie eine zusätzliche Delegierung oder Regel für bedingte Weiterleitung erstellen.

Weitere Informationen zur Funktionsweise der DNS-Namensbezeichnung finden Sie unter „Verwenden von DNS in Azure Stack Hub Ruggedized“.

### <a name="resolution-and-delegation"></a>Auflösung und Delegierung

Es gibt zwei Arten von DNS-Server:

- Ein autoritativer DNS-Server hostet DNS-Zonen. Er antwortet nur auf DNS-Abfragen nach Einträgen für diese Zonen.
- Ein rekursiver DNS-Server hostet keine DNS-Zonen. Er reagiert auf alle DNS-Abfragen durch Aufrufen der autoritativen DNS-Server, um die benötigten Daten zu erfassen.

Azure Stack Hub Ruggedized enthält autoritative und rekursive DNS-Server. Die rekursiven Server werden verwendet, um alle Namen außer denen der internen privaten Zone und der externen öffentlichen DNS-Zone für die Azure Stack Hub Ruggedized-Bereitstellung aufzulösen.

## <a name="resolving-external-dns-names-from-azure-stack-hub-ruggedized"></a>Auflösen von externen DNS-Namen in Azure Stack Hub Ruggedized

Zum Auflösen von DNS-Namen für Endpunkte außerhalb von Azure Stack Hub (z. B. „www.bing.com“) müssen Sie DNS-Server bereitstellen, die Azure Stack Hub Ruggedized für die Weiterleitung von DNS-Anforderungen verwenden kann, für die Azure Stack Hub Ruggedized nicht autoritativ ist. Die DNS-Server, an die Azure Stack Hub Ruggedized Anforderungen weiterleitet, müssen im Arbeitsblatt der Bereitstellung angegeben werden (im Feld „DNS-Weiterleitung“). Geben Sie für die Fehlertoleranz mindestens zwei Server in diesem Feld an. Ohne diese Werte kommt es bei der Azure Stack Hub Ruggedized-Bereitstellung zu Fehlern. Sie können die Werte für die DNS-Weiterleitung nach der Bereitstellung mit dem **Set-AzSDnsForwarder**-Cmdlet bearbeiten.

## <a name="firewall-design-overview"></a>Übersicht über den Firewallentwurf

Es empfiehlt sich, Azure Stack Hub Ruggedized mit einem Firewallgerät zu schützen. Firewalls können bei der Verteidigung vor Dingen wie DDoS-Angriffen (Denial of Service), bei der Angriffserkennung sowie der Inhaltsuntersuchung helfen. Sie können allerdings auch zu einem Durchsatzengpass für Azure-Speicherdienste wie Blobs, Tabellen und Warteschlangen werden.

Bei Verwendung eines getrennten Bereitstellungsmodus muss der AD FS-Endpunkt veröffentlicht werden. Weitere Informationen finden Sie im Artikel zur Rechenzentrumsintegration mithilfe von Identitäten.

Für die Endpunkte „Azure Resource Manager (Administrator)“, „Portal (Administrator)“ und „Schlüsseltresor (Administrator)“ ist nicht zwingend eine externe Veröffentlichung erforderlich. Als Dienstanbieter können Sie beispielsweise die Angriffsfläche verkleinern, indem Sie Azure Stack Hub Ruggedized nur innerhalb Ihres Netzwerks (und nicht über das Internet) verwalten.

Bei einem Unternehmen kann das Unternehmensnetzwerk als externes Netzwerk fungieren. In diesem Szenario müssen Sie Endpunkte veröffentlichen, um Azure Stack Hub Ruggedized über das Unternehmensnetzwerk betreiben zu können.

### <a name="network-address-translation"></a>Netzwerkadressenübersetzung

Die Netzwerkadressenübersetzung (Network Address Translation, NAT) ist die empfohlene Methode, mit der der virtuelle Bereitstellungscomputer (Deployment Virtual Machine, DVM) während der Bereitstellung auf externe Ressourcen zugreifen kann. Dies gilt auch für die ERCS-VMs (Emergency Recovery Console, Notfallwiederherstellungskonsole) bzw. den privilegierten Endpunkt (PEP) während der Registrierung und Problembehandlung. 

Die NAT kann auch alternativ zu öffentlichen IP-Adressen im externen Netzwerk oder öffentlichen VIPs verwendet werden. Hiervon wird aber abgeraten, da diese die Benutzerfreundlichkeit des Mandanten verringert und die Komplexität erhöht. Eine Option wäre eine 1:1-NAT, die immer noch eine öffentliche IP pro Benutzer-IP im Pool erfordert. Eine andere Option ist eine n:1-NAT, die eine NAT-Regel pro Benutzer-VIP für alle Ports erfordert, die ein Benutzer verwenden könnte.

Die Verwendung der NAT für öffentliche VIPs hat folgende Nachteile:
- Der Verwaltungsaufwand für Firewallregeln erhöht sich, da Benutzer ihre eigenen Endpunkte und Veröffentlichungsregeln im SDN-Stapel (Software-Defined Networking) steuern. Benutzer müssen sich zur Veröffentlichung ihrer VIPs sowie zur Aktualisierung der Portliste an den Azure Stack Hub Ruggedized-Operator wenden. 
- Die Verwendung der NAT beeinträchtigt zwar die Benutzerfreundlichkeit, gibt dem Betreiber jedoch die volle Kontrolle über Veröffentlichungsanforderungen.
- In Hybrid Cloud-Szenarien mit Azure wird die Einrichtung eines VPN-Tunnels zu einem Endpunkt mit NAT nicht unterstützt.

### <a name="ssl-interception"></a>Abfangen von SSL

Zurzeit wird empfohlen, das Abfangen von SSL (z. B. Entschlüsselungsabladung) für den gesamten Azure Stack Hub Ruggedized-Datenverkehr zu deaktivieren. Wenn diese Funktion in zukünftigen Updates unterstützt wird, werden Anleitungen zum Aktivieren von SSL-Abfangen für Azure Stack Hub Ruggedized bereitgestellt.

###  <a name="edge-deployment-firewall-scenario"></a>Firewallszenario bei Edgebereitstellungen

Azure Stack Hub Ruggedized wird in einer Edgebereitstellung direkt hinter dem Edgerouter oder der Firewall bereitgestellt. In diesen Szenarien darf die Firewall über der Grenze liegen (Szenario 1) und Firewallkonfigurationen der Typen Aktiv/Aktiv und Aktiv/Passiv unterstützen. Sie kann auch als Grenzgerät fungieren (Szenario 2). In diesem Fall werden nur Firewallkonfigurationen vom Typ Aktiv/Aktiv unterstützt. Für Szenario 2 ist ECMP (Equal-Cost Multi Path) mit BGP oder statischem Routing für das Failover erforderlich.

Öffentliche routingfähige IP-Adressen werden für den öffentlichen VIP-Pool aus dem externen Netzwerk zur Bereitstellungszeit angegeben. Aus Sicherheitsgründen wird in einem Edgeszenario davon **abgeraten**, öffentliche routingfähige IP-Adressen in anderen Netzwerken zu verwenden. Dadurch erhalten Benutzer eine vollständig selbstgesteuerte Cloudumgebung – genau wie bei einer öffentlichen Cloud (z.B. Azure).

 ![Edgefirewallszenario mit Azure Stack Hub Ruggedized](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Firewallszenario mit Unternehmensintranet oder -umkreisnetzwerk

Bei einer Bereitstellung im Unternehmensintranet oder -umkreisnetzwerk wird Azure Stack Hub Ruggedized in einer Firewall mit mehreren Zonen oder zwischen der Edgefirewall und der internen Unternehmensnetzwerkfirewall bereitgestellt. Dann wird wie im Folgenden beschrieben der Datenverkehr zwischen dem sicheren Umkreisnetzwerk (oder der DMZ) und unsicheren Zonen verteilt:

- **Sichere Zone**: Dies ist das interne Netzwerk, das interne oder routingfähige Unternehmens-IP-Adressen verwendet. Das sichere Netzwerk kann unterteilt werden. Es kann über die Netzwerkadressenübersetzung der Firewall ausgehenden Zugriff auf das Internet haben. Normalerweise ist der Zugriff über das interne Netzwerk innerhalb Ihres Rechenzentrums möglich. Alle Azure Stack Hub Ruggedized-Netzwerke sollten sich in der sicheren Zone befinden, der öffentliche VIP-Pool im externen Netzwerk ausgenommen.
- **Umkreiszone**: Das Umkreisnetzwerk wird dort eingesetzt, wo in der Regel externe oder Internet-Apps, wie z. B. Webserver, bereitgestellt werden. Es wird normalerweise durch eine Firewall überwacht, um Angriffe wie DDoS-Angriffe und Eindringversuche (Hacken) zu verhindern, während gleichzeitig angegebener eingehender Datenverkehr aus dem Internet zugelassen wird. In der DMZ-Zone sollte sich nur der öffentliche VIP-Pool des externen Netzwerks von Azure Stack Hub Ruggedized befinden.
- **Unsichere Zone**: Dies ist das externe Netzwerk, das Internet. Die Bereitstellung von Azure Stack Hub Ruggedized in der unsicheren Zone wird **nicht** empfohlen.

 ![Firewallszenario mit einem Umkreisnetzwerk](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>VPN-Entwurfsübersicht

Obwohl es sich bei VPN um ein Benutzerkonzept handelt, gibt es einige wichtige Überlegungen, die Besitzer und Operatoren einer Lösung kennen müssen.

Um Netzwerkdatenverkehr zwischen Ihrem virtuellen Azure-Netzwerk und Ihrem lokalen Standort senden zu können, müssen Sie für Ihr virtuelles Netzwerk zunächst ein Gateway für virtuelle Netzwerke (VPN) erstellen.

Ein VPN Gateway ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr über eine öffentliche Verbindung gesendet wird. Sie können VPN-Gateways für einen sicheren Datenverkehr zwischen einem virtuellen Netzwerk in Azure Stack Hub Ruggedized und einem virtuellen Netzwerk in Azure verwenden. Zudem können Sie den Datenverkehr zwischen einem virtuellen Netzwerk und einem anderen Netzwerk, das mit einem VPN-Gerät verbunden ist, sicher übertragen.

Der gewünschte Gatewaytyp wird beim Erstellen eines Gateways für virtuelle Netzwerke angegeben. Azure Stack Hub Ruggedized unterstützt einen Typ von Gateway für virtuelle Netzwerke: **VPN**.

Jedes virtuelle Netzwerk kann über zwei Gateways für virtuelle Netzwerke, aber nur über eins von jedem Typ verfügen. Abhängig von den gewählten Einstellungen können Sie ggf. mehrere Verbindungen mit einem einzelnen VPN-Gateway erstellen. Ein Beispiel für diese Art Einrichtung ist eine Verbindungskonfiguration mit mehreren Standorten.

Bevor Sie VPN-Gateways für Azure Stack Hub Ruggedized erstellen und konfigurieren, sollten Sie die Überlegungen zu Azure Stack Hub Ruggedized-Netzwerken lesen. Dort erfahren Sie, wie sich die Konfigurationen für Azure Stack Hub Ruggedized von Azure unterscheiden.

In Azure muss der Bandbreitendurchsatz für die ausgewählte VPN Gateway-SKU auf alle Verbindungen aufgeteilt werden, die mit dem Gateway hergestellt wurden. In Azure Stack Hub Ruggedized wird der Bandbreitenwert jedoch für die VPN Gateway-SKU auf jede Verbindungsressource angewendet, die mit dem Gateway verbunden ist. Beispiel: 

- In Azure bietet die VPN Gateway-SKU „Basic“ eine aggregierte Durchsatzkapazität von etwa 100 Mbit/s. Wenn Sie zwei Verbindungen mit diesem VPN Gateway erstellen und eine dieser Verbindungen eine Bandbreite von 50 Mbit/s beansprucht, stehen für die andere Verbindung noch 50 Mbit/s zur Verfügung. 
- In Azure Stack Hub Ruggedized wird jeder Verbindung mit der VPN-Gateway-SKU „Basic“ ein Durchsatz von 100 MBit/s zugeordnet.

### <a name="vpn-types"></a>VPN-Typen

Wenn Sie das Gateway des virtuellen Netzwerks für eine VPN-Gatewaykonfiguration erstellen, müssen Sie einen VPN-Typ angeben. Der ausgewählte VPN-Typ hängt von der Verbindungstopologie ab, die Sie erstellen möchten. Der VPN-Typ kann zudem von der verwendeten Hardware abhängen. S2S-Konfigurationen erfordern ein VPN-Gerät. Einige VPN-Geräte unterstützen nur einen bestimmten VPN-Typ. 

>[!IMPORTANT]
> Aktuell unterstützt Azure Stack Hub Ruggedized nur den routenbasierten VPN-Typ. Wenn Ihr Gerät nur richtlinienbasierte VPNs unterstützt, werden Verbindungen mit diesen Geräten über Azure Stack Hub Ruggedized nicht unterstützt. Darüber hinaus bietet Azure Stack Hub Ruggedized aktuell keine Unterstützung für die Verwendung von richtlinienbasierten Datenverkehrsselektoren für routenbasierte Gateways, da benutzerdefinierte IPSec/IKE-Richtlinienkonfigurationen nicht unterstützt werden.

- **PolicyBased**: Richtlinienbasierte VPNs verschlüsseln die Pakete und leiten sie über IPsec-Tunnel gemäß den IPsec-Richtlinien weiter. Die Richtlinien werden mit den Kombinationen aus Adresspräfixen zwischen Ihrem lokalen Netzwerk und dem Azure Stack Hub Ruggedized-VNET konfiguriert. Die Richtlinie (auch Datenverkehrsselektor genannt) ist in der Regel eine Zugriffsliste in der VPN-Gerätekonfiguration.  **PolicyBased** wird in Azure, aber nicht in Azure Stack Hub Ruggedized unterstützt. 
- **RouteBased**: Routenbasierte VPNs verwenden Routen, die in der IP-Weiterleitungs- oder Routingtabelle konfiguriert sind. Über die Routen werden die Pakete an die entsprechenden Tunnelschnittstellen weitergeleitet. An den Tunnelschnittstellen werden die Pakete dann ver- bzw. entschlüsselt. Die Richtlinie (bzw. der Datenverkehrsselektor) für **routenbasierte** VPNs wird im Any-to-Any-Format (bzw. unter Verwendung von Platzhaltern) konfiguriert. Sie können standardmäßig nicht geändert werden. Der Wert für einen **RouteBased**-VPN-Typ lautet **RouteBased**.

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

Eine VPN Gateway-S2S-Verbindung (*Site-to-Site*) ist eine Verbindung über einen IPsec-/IKE-VPN-Tunnel (IKEv2). Dieser Verbindungstyp erfordert ein lokales VPN-Gerät, das einer öffentlichen IP-Adresse zugewiesen ist. Dieses Gerät darf sich nicht hinter einer NAT befinden. S2S-Verbindungen können für standortübergreifende Konfigurationen und Hybridkonfigurationen verwendet werden.

##### <a name="multi-site"></a>Multi-Site

Bei einer *Multi-Site*-Verbindung handelt es sich um eine Abwandlung der Site-to-Site-Verbindung. Sie erstellen mehrere VPN-Verbindungen über Ihr Gateway für virtuelle Netzwerke, durch die in der Regel mehrere lokale Standorte verbunden werden. Bei Verwendung mehrerer Verbindungen müssen Sie den VPN-Typ „RouteBased“ verwenden (wird bei Verwendung klassischer VNETs als dynamisches Gateway bezeichnet). Da jedes virtuelle Netzwerk nur über ein einzelnes VPN-Gateway verfügen kann, wird die verfügbare Bandbreite von allen über das Gateway laufenden Verbindungen gemeinsam genutzt.

### <a name="gateway-skus"></a>Gateway-SKUs

Beim Erstellen eines Gateways für virtuelle Netzwerke für Azure Stack Hub Ruggedized müssen Sie die gewünschte Gateway-SKU angeben. Folgende SKUs für VPN-Gateways werden unterstützt:

- Basic
- Standard
- Leistung

Wenn Sie eine höhere Gateway-SKU auswählen, werden dem Gateway mehr CPUs und eine höhere Netzwerkbandbreite zugeordnet. Dadurch kann das Gateway einen höheren Netzwerkdurchsatz an das virtuelle Netzwerk bewältigen.

Die Gateway-SKU „Höchstleistung“ wird ausschließlich mit Express Route verwendet und von Azure Stack Hub Ruggedized nicht unterstützt.

Beachten Sie bei der Auswahl der SKU Folgendes:

- Richtlinienbasierte Gateways werden von Azure Stack Hub Ruggedized nicht unterstützt.
- BGP wird in der Basic-SKU nicht unterstützt.
- Die gemeinsame Verwendung von VPN Gateway und ExpressRoute wird in Azure Stack Hub Ruggedized nicht unterstützt.

#### <a name="gateway-availability"></a>Gatewayverfügbarkeit

Hochverfügbarkeitsszenarien können nur in der Verbindungs-SKU **Hochleistungsgateway** konfiguriert werden. Im Gegensatz zu Azure, das Verfügbarkeit durch sowohl Aktiv/Aktiv- als auch Aktiv/Passiv-Konfigurationen bietet, unterstützt Azure Stack Hub Ruggedized nur die Aktiv/Passiv-Konfiguration.

#### <a name="failover"></a>Failover

In Azure Stack Hub Ruggedized gibt es drei mehrinstanzenfähige VMs mit Gatewayinfrastruktur. Zwei dieser virtuellen Computer befinden sich im aktiven Modus, während sich die dritte im redundanten Modus befindet. Aktive virtuelle Computer ermöglichen die Erstellung von VPN-Verbindungen auf sich, und die redundante VM akzeptiert nur VPN-Verbindungen, wenn ein Failover eintritt. Wenn eine aktive Gateway-VM nicht mehr verfügbar ist, führt die VPN-Verbindung nach einem kurzen Zeitraum (ein paar Sekunden) der Verbindungsunterbrechung ein Failover zur redundanten VM durch.

### <a name="estimated-aggregate-throughput-by-sku"></a>Voraussichtlicher aggregierter Durchsatz nach SKU

In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz nach Gateway-SKU angegeben:

|                          | **VPN Gateway-Durchsatz (1)** | **Max. IPsec-Tunnel für VPN Gateway (2)** |
|--------------------------|--------------------------------|---------------------------------------|
| **Basic-SKU** **(3)**    | 100 MBit/s                       | 20                                    |
| **Standard-SKU**         | 100 MBit/s                       | 20                                    |
| **HighPerformance-SKU** | 200 MBit/s                       | 10                                    |

Anmerkungen zur Tabelle

**(1)** Der VPN-Durchsatz ist kein garantierter Durchsatz für standortübergreifende Verbindungen über das Internet. Hierbei wird der maximal mögliche Durchsatz gemessen.  
**(2)** Bei der maximalen Tunnelanzahl handelt es sich um die Summe pro Azure Stack Hub Ruggedized-Bereitstellung für alle Abonnements.  
**(3)** Das BGP-Routing wird in der Basic-SKU nicht unterstützt.  

>[!IMPORTANT]
> Zwischen zwei Azure Stack Hub Ruggedized-Bereitstellungen kann nur eine Site-to-Site-VPN-Verbindung hergestellt werden. Der Grund: Wegen einer Einschränkung ist für die Plattform nur eine einzelne VPN-Verbindung mit der gleichen IP-Adresse zulässig. Da Azure Stack Hub Ruggedized das mehrinstanzenfähige Gateway nutzt, das eine öffentliche IP-Adresse für alle VPN-Gateways im Azure Stack Hub Ruggedized-System verwendet, kann zwischen zwei Azure Stack Hub Ruggedized-Systemen nur eine VPN-Verbindung hergestellt werden. 
>
> Diese Einschränkung gilt auch für das Herstellen mehrerer Site-to-Site-VPN-Verbindungen mit einem beliebigen VPN-Gateway, das eine einzige IP-Adresse nutzt. Azure Stack Hub Ruggedized lässt nicht zu, dass mehrere Ressourcen des lokalen Netzwerkgateways mit der gleichen IP-Adresse erstellt werden.** 


### <a name="ipsecike-parameters"></a>IPsec-/IKE-Parameter

Wenn Sie eine VPN-Verbindung in Azure Stack Hub Ruggedized einrichten, müssen Sie die Verbindung an beiden Endpunkten konfigurieren. Wenn Sie eine VPN-Verbindung zwischen Azure Stack Hub Ruggedized und einem Hardwaregerät konfigurieren, müssen Sie an diesem Gerät möglicherweise zusätzliche Einstellungen vornehmen. Ein Beispiel ist ein Switch oder Router, der als VPN-Gateway fungiert.

Im Gegensatz zu Azure, das mehrere Angebote als Initiator und Antwortdienst unterstützt, bietet Azure Stack Hub Ruggedized standardmäßig nur Unterstützung für ein Angebot. Wenn Sie verschiedene IPSec/IKE-Einstellungen mit Ihrem VPN-Gerät verwenden müssen, stehen Ihnen weitere Einstellungen zur Verfügung, um Ihre Verbindung manuell zu konfigurieren.

#### <a name="ike-phase-1-main-mode-parameters"></a>Parameter der IKE-Phase 1 (Hauptmodus)

| **Eigenschaft**                    | **Wert**      |
|---------------------------------|----------------|
| IKE-Version                     | IKEv2          |
| Diffie-Hellman-Gruppe            | ECP384         |
| Authentifizierungsmethode           | Vorab ausgetauschter Schlüssel |
| Verschlüsselung und Hashalgorithmen | AES256, SHA384 |
| SA-Gültigkeitsdauer (Zeit)              | 28.800 Sekunden |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Parameter der IKE-Phase 2 (Schnellmodus)

| **Eigenschaft**                                     | **Wert**      |
|--------------------------------------------------|----------------|
| IKE-Version                                      | IKEv2          |
| Verschlüsselung und Hashalgorithmen (Verschlüsselung)     | GCMAES256      |
| Verschlüsselung und Hashalgorithmen (Authentifizierung) | GCMAES256      |
| SA-Gültigkeitsdauer (Zeit)                               | 27.000 Sekunden |
| SA-Gültigkeitsdauer (KB)                          | 33.553.408     |
| Perfect Forward Secrecy (PFS)                    | ECP384         |
| Dead Peer Detection                              | Unterstützt      |

### <a name="configure-custom-ipsecike-connection-policies"></a>Konfigurieren benutzerdefinierter IPsec-/IKE-Verbindungsrichtlinien

Der IPsec- und IKE-Protokollstandard unterstützt ein breites Spektrum von Kryptografiealgorithmen in verschiedenen Kombinationen. Informationen zu den in Azure Stack Hub Ruggedized für die Erfüllung Ihrer Anforderungen an Compliance oder Sicherheit unterstützten Parametern finden Sie unter „IPsec/IKE-Parameter“.

Dieser Artikel enthält eine Anleitung zum Erstellen und Konfigurieren einer IPsec/IKE-Richtlinie und zu ihrem Anwenden auf eine neue oder vorhandene Verbindung.

#### <a name="considerations"></a>Überlegungen

Beachten Sie die folgenden wichtigen Informationen, wenn Sie diese Richtlinien verwenden:

- Die IPsec/IKE-Richtlinie kann nur für die Gateway-SKUs *Standard* und *HighPerformance* (routenbasiert) verwendet werden.
- Pro Verbindung kann jeweils nur **eine** Richtlinienkombination angegeben werden.
- Sie müssen alle Algorithmen und Parameter für IKE (Hauptmodus) und IPsec (Schnellmodus) angeben. Eine teilweise Angabe von Richtlinien ist unzulässig.
- Vergewissern Sie sich in den Spezifikationen Ihres VPN-Geräteanbieters, dass die Richtlinie von Ihren lokalen VPN-Geräten unterstützt wird. Site-to-Site-Verbindungen können nicht hergestellt werden, wenn die Richtlinien inkompatibel sind.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Workflow zum Erstellen und Festlegen der IPsec/IKE-Richtlinie

In diesem Abschnitt wird der Workflow zum Erstellen und Aktualisieren der IPsec/IKE-Richtlinie für eine Site-to-Site-VPN-Verbindung beschrieben:

1.  Erstellen eines virtuellen Netzwerks und eines VPN-Gateways
2.  Erstellen eines Gateways für das lokale Netzwerk für standortübergreifende Verbindungen
3.  Erstellen einer IPsec/IKE-Richtlinie mit ausgewählten Algorithmen und Parametern
4.  Erstellen einer IPsec-Verbindung mit der IPsec/IKE-Richtlinie
5.  Hinzufügen/Aktualisieren/Entfernen einer IPsec/IKE-Richtlinie für eine vorhandene Verbindung

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Unterstützte Kryptografiealgorithmen und Schlüsselstärken

Die folgende Tabelle gibt Aufschluss über die unterstützten Kryptografiealgorithmen und Schlüsselstärken, die von den Azure Stack Hub Ruggedized-Kunden konfiguriert werden können:

| **IPsec/IKEv2**  | **Optionen** |
|------------------|-------------|
| IKEv2-Verschlüsselung | AES256, AES192, AES128, DES3, DES                                                                                                                         |
| IKEv2-Integrität  | SHA384, SHA256, SHA1, MD5                                                                                                                                 |
| DH-Gruppe         | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Keine                                                                                          |
| IPsec-Verschlüsselung | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, keine                                                                                  |
| IPsec-Integrität  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                                                                                                        |
| PFS-Gruppe        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, keine                                                                                                          |
| QM-SA-Gültigkeitsdauer   | (Optional: Wenn kein Wert angegeben wird, werden die Standardwerte verwendet.)                                                                                                      |
|                  | Sekunden (ganze Zahl; min. 300/Standard: 27.000 Sekunden)                                                                                                         |
|                  | KB (ganze Zahl; min. 1.024/Standard: 102.400.000 KB)                                                                                                      |
| Datenverkehrsselektor | Richtlinienbasierte Datenverkehrsselektoren werden in Azure Stack Hub Ruggedized nicht unterstützt.                                                                                           |

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
- Die SA-Gültigkeitsdauer von IKEv2 (Hauptmodus) ist für die Azure Stack Hub Ruggedized-VPN-Gateways auf 28.800 Sekunden festgelegt.

Die folgende Tabelle enthält die entsprechenden Diffie-Hellman-Gruppen, die von der benutzerdefinierten Richtlinie unterstützt werden:

| **Diffie-Hellman-Gruppe** | **DHGroup**          | **PFSGroup** | **Schlüssellänge** |
|--------------------------|----------------------|--------------|----------------|
| 1                        | DHGroup1             | PFS1         | 768-Bit-MODP   |
| 2                        | DHGroup2             | PFS2         | 1024-Bit-MODP  |
| 14                       | DHGroup14            | PFS2048      | 2048-Bit-MODP  |
|                          | DHGroup2048          |              |                |
| 19                       | ECP256               | ECP256       | 256-Bit-ECP    |
| 20                       | ECP384               | ECP384       | 384-Bit-ECP    |
| 24                       | DHGroup24            | PFS24        | 2048-Bit-MODP  |

### <a name="connect-azure-stack-hub-ruggedized-to-azure-using-azure-expressroute"></a>Herstellen einer Verbindung von Azure Stack Hub Ruggedized mit Azure über Azure ExpressRoute

#### <a name="overview-assumptions-and-prerequisites"></a>Übersicht, Annahmen und Voraussetzungen

Mit dem Dienst Azure ExpressRoute können Sie Ihre lokalen Netzwerke in die Microsoft-Cloud erweitern. Sie verwenden eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird. Bei ExpressRoute handelt es sich nicht um eine VPN-Verbindung über das öffentliche Internet.

Weitere Informationen zu Azure ExpressRoute finden Sie unter ExpressRoute-Übersicht.

#### <a name="assumptions"></a>Annahmen

In diesem Artikel wird Folgendes vorausgesetzt:

- Sie verfügen über ausreichende praktische Kenntnisse von Azure.
- Sie verfügen über grundlegende Kenntnisse von Azure Stack Hub Ruggedized.
- Sie verfügen über grundlegende Kenntnisse von Netzwerken.

#### <a name="prerequisites"></a>Voraussetzungen

Für die Verbindungsherstellung zwischen Azure Stack Hub Ruggedized und Azure mithilfe von ExpressRoute gelten die folgenden Anforderungen:

- Eine bereitgestellte ExpressRoute-Verbindung über einen Konnektivitätsanbieter.
- Ein Azure-Abonnement zum Erstellen einer ExpressRoute-Verbindung und von VNets in Azure.
- Ein Router, der Folgendes unterstützt:
  - Site-to-Site-VPN-Verbindungen zwischen seiner LAN-Schnittstelle und dem mehrinstanzenfähigen Azure Stack Hub Ruggedized-Gateway
  - Erstellung mehrerer VRF-Instanzen (Virtual Routing and Forwarding), falls die Azure Stack Hub Ruggedized-Bereitstellung mehrere Mandanten umfasst
- Ein Router, der über Folgendes verfügt:
  - Einen WAN-Port, der mit der ExpressRoute-Verbindung verbunden ist
  - Einen LAN-Port, der mit dem mehrinstanzenfähigen Azure Stack Hub Ruggedized-Gateway verbunden ist

#### <a name="expressroute-network-architecture"></a>ExpressRoute-Netzwerkarchitektur

Die folgende Abbildung zeigt die Azure Stack Hub Ruggedized- und die Azure-Umgebung nach der Einrichtung von ExpressRoute anhand der Beispiele in diesem Artikel:

 ![ExpressRoute-Netzwerkarchitektur](media/network-introduction/expressroute-network-architecture-60.png) 

In der folgenden Abbildung ist dargestellt, wie für mehrere Mandanten eine Verbindung von der Azure Stack Hub Ruggedized-Infrastruktur über den ExpressRoute-Router mit Azure hergestellt wird:

 ![ExpressRoute-Netzwerkarchitektur mit mehreren Mandanten](media/network-introduction/expressroute-network-architecture-multi-tenant-60.png) 

