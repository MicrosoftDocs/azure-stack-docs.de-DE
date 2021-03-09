---
title: Transparenter Proxy für integrierte Azure Stack Hub-Systeme
description: Enthält eine Übersicht über die Eigenschaft „Transparent“ in integrierten Azure Stack Hub-Systemen.
author: PatAltimore
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: patricka
ms.reviewer: sranthar
ms.lastreviewed: 01/25/2021
ms.openlocfilehash: a7fc47edf63a83e1ee05c46b03d8533787b1983c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840693"
---
# <a name="transparent-proxy-for-azure-stack-hub"></a>Transparenter Proxy für Azure Stack Hub

Ein transparenter Proxy (auch abfangender, Inline- oder erzwungener Proxy genannt) fängt die normale Kommunikation auf der Netzwerkschicht ab, ohne dass eine spezielle Clientkonfiguration erforderlich ist. Clients müssen nicht wissen, dass der Proxy vorhanden ist.

Wenn Ihr Rechenzentrum verlangt, dass für den gesamten Datenverkehr ein Proxy verwendet wird, konfigurieren Sie einen transparenten Proxy. Der gesamte Datenverkehr wird dann gemäß der entsprechenden Richtlinie verarbeitet, indem der Datenverkehr zwischen den Zonen in Ihrem Netzwerk getrennt wird.

## <a name="traffic-types"></a>Datenverkehrstypen

Ausgehender Datenverkehr von Azure Stack Hub wird entweder als Mandanten- oder als Infrastrukturdatenverkehr kategorisiert.

Mandantendatenverkehr wird von den Mandanten über virtuelle Computer, Lastenausgleichsmodule, VPN-Gateways, App-Dienste usw. generiert.

Der Infrastrukturdatenverkehr wird aus dem ersten `/27`-Bereich des Pools mit den öffentlichen virtuellen IP-Adressen generiert, der den Infrastrukturdiensten zugewiesen ist, z. B. Identität, Patches/Updates, Nutzungsmetriken, Marketplace-Syndikation, Registrierung, Protokollsammlung, Windows Defender usw. Der Datenverkehr dieser Dienste wird an [Azure-Endpunkte](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) weitergeleitet. Azure akzeptiert keinen Datenverkehr, der von einem Proxy geändert wurde, und keinen abgefangenen TLS/SSL-Datenverkehr. Dies ist der Grund, warum Azure Stack Hub keine native Proxykonfiguration unterstützt.

Beim Konfigurieren eines transparenten Proxys können Sie auswählen, ob der gesamte ausgehende Datenverkehr oder nur Infrastrukturdatenverkehr über den Proxy gesendet werden soll.

## <a name="partner-integration"></a>Partnerintegration

Microsoft hat mit branchenführenden Proxyanbietern als Partnern zusammengearbeitet, um die Anwendungsfallszenarien von Azure Stack Hub mit einer transparenten Proxykonfiguration zu überprüfen. Das folgende Diagramm ist ein Beispiel für eine Azure Stack Hub-Netzwerkkonfiguration mit Proxys für Hochverfügbarkeit. Externe Proxygeräte müssen „nördlich“ von den Border-Geräten angeordnet sein.

![Netzwerkdiagramm mit Proxy vor Border-Geräten](./media/azure-stack-transparent-proxy/proxy.svg)

 Darüber hinaus müssen die Border-Geräte so konfiguriert werden, dass Datenverkehr aus Azure Stack Hub auf eine der folgenden Arten weitergeleitet wird:
- Weiterleiten des gesamten ausgehenden Datenverkehrs von Azure Stack Hub an die Proxygeräte
- Weiterleiten des gesamten ausgehenden Datenverkehrs aus dem ersten `/27`-Bereich des Pools mit den virtuellen IP-Adressen von Azure Stack Hub an die Proxygeräte per richtlinienbasiertem Routing  

Ein Beispiel für eine Border-Konfiguration finden Sie in diesem Artikel unter [Beispiel für Border-Konfiguration](#example-border-configuration).

Sehen Sie sich in den folgenden Dokumenten die überprüften Konfigurationen mit transparentem Proxy für Azure Stack Hub an:

- [Konfigurieren eines transparenten Proxys für ein Check Point-Sicherheitsgateway](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk171559)
- [Konfigurieren eines transparenten Proxys für eine Sophos XG-Firewall](https://community.sophos.com/xg-firewall/f/recommended-reads/124106/xg-firewall-integration-with-azure-stack-hub)
- [Integrieren von Citrix ADC und Citrix Secure Web Gateway in Azure Stack Hub](https://www.citrix.com/blogs/2021/02/19/integrating-citrix-adc-citrix-secure-web-gateway-with-azure-stack-hub/)

Für Szenarien, in denen ausgehender Datenverkehr aus Azure Stack Hub über einen expliziten Proxy fließen muss, verfügen Sophos- und Check Point-Geräte über einen Dualmodus. Hierbei kann konfiguriert werden, dass für bestimmte Bereiche des Datenverkehrs der transparente Modus und für andere Bereiche der explizite Modus gilt. Mit diesem Feature können die Proxygeräte so konfiguriert werden, dass nur Infrastrukturdatenverkehr über den transparenten Proxy gesendet wird, während für den gesamten Mandantendatenverkehr der explizite Modus verwendet wird.

> [!IMPORTANT]
> Das Abfangen von SSL-Datenverkehr wird nicht unterstützt und kann beim Zugriff auf Endpunkte zu Dienstfehlern führen. Das maximal unterstützte Zeitlimit für die Kommunikation mit Endpunkten, die für die Identität erforderlich sind, ist 60 Sekunden mit drei Wiederholungsversuchen. Weitere Informationen finden Sie unter [Azure Stack Hub-Firewallintegration](azure-stack-firewall.md#ssl-interception).

## <a name="example-border-configuration"></a>Beispiel für Border-Konfiguration

Die Lösung basiert auf dem richtlinienbasierten Routing (Policy-Based Routing, PBR), bei dem von einem Administrator definierte Kriterien, die über eine Zugriffssteuerungsliste (Access Control List, ACL) implementiert werden, verwendet werden. Mit der Zugriffssteuerungsliste wird der Datenverkehr kategorisiert, der für die IP-Adresse des nächsten Hops der Proxygeräte bestimmt ist, die in einer Routenzuordnung implementiert sind. Dies ist anders als beim üblichen Routing, das ausschließlich auf IP-Zieladressen basiert. Spezifischer Infrastruktur-Netzwerkdatenverkehr für die Ports 80 und 443 wird von den Border-Geräten an die Bereitstellung des transparenten Proxys weitergeleitet. Der transparente Proxy führt eine URL-Filterung durch, und *unzulässiger* Datenverkehr wird verworfen.

Das folgende Konfigurationsbeispiel gilt für ein Cisco Nexus 9508-Gehäuse.

In diesem Szenario lauten die Infrastruktur-Quellnetzwerke, für die Internetzugriff erforderlich ist, wie folgt:

- Öffentliche VIP: Erster „/27“-Bereich
- Infrastrukturnetzwerk: Letzter „/27“-Bereich
- BMC-Netzwerk: Letzter „/27“-Bereich

Für die folgenden Subnetze wird in diesem Szenario richtlinienbasiertes Routing (Policy-Based Routing, PBR) verwendet:

| Netzwerk | IP-Bereich | Subnetz mit PBR
|---------|----------|-------------------------------
| Pool mit öffentlichen virtuellen IP-Adressen | Erster „/27“-Bereich von 172.21.107.0/27 | 172.21.107.0/27 = 172.21.107.1 bis 172.21.107.30
| Infrastrukturnetzwerk | Letzter „/27“-Bereich von 172.21.7.0/24 | 172.21.7.224/27 = 172.21.7.225 bis 172.21.7.254
| BMC-Netzwerk | Letzter „/27“-Bereich von 10.60.32.128/26 | 10.60.32.160/27 = 10.60.32.161 bis 10.60.32.190

### <a name="configure-border-device"></a>Konfigurieren des Border-Geräts

Aktivieren Sie PBR, indem Sie den Befehl `feature pbr` eingeben. 

```
****************************************************************************
PBR Configuration for Cisco Nexus 9508 Chassis
PBR Enivronment configured to use VRF08
The test rack has is a 4-node Azure Stack stamp with 2x TOR switches and 1x BMC switch. Each TOR switch 
has a single uplink to the Nexus 9508 chassis using BGP for routing. In this example the test rack 
is in it's own VRF (VRF08)
****************************************************************************
!
feature pbr
!

<Create VLANs that the proxy devices will use for inside and outside connectivity>

!
VLAN 801
name PBR_Proxy_VRF08_Inside
VLAN 802
name PBR_Proxy_VRF08_Outside
!
interface vlan 801
description PBR_Proxy_VRF08_Inside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.1/29
!
interface vlan 802
description PBR_Proxy_VRF08_Outside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.33/28
!
!
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www
110 permit tcp 172.21.107.0/27 any eq 443
120 permit tcp 172.21.7.224/27 any eq www
130 permit tcp 172.21.7.224/27 any eq 443
140 permit tcp 10.60.32.160/27 any eq www
150 permit tcp 10.60.32.160/27 any eq 443
!
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
!
!
interface Ethernet1/1
  description DownLink to TOR-1:TeGig1/0/47
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.193/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!
interface Ethernet2/1
  description DownLink to TOR-2:TeGig1/0/48
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.205/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!

<Interface configuration for inside/outside connections to proxy devices. In this example there are 2 firewalls>

!
interface Ethernet1/41
  description management interface for Firewall-1
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet1/42
  description Proxy interface for Firewall-1
  switchport
  switchport access vlan 802
  no shutdown
!
interface Ethernet2/41
  description management interface for Firewall-2
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet2/42
  description Proxy interface for Firewall-2
  switchport
  switchport access vlan 802
  no shutdown
!

<BGP network statements for VLAN 801-802 subnets and neighbor statements for R023171A-TOR-1/R023171A-TOR-2> 

!
router bgp 65000
!
vrf VRF08
address-family ipv4 unicast
network 10.60.3.0/29
network 10.60.3.32/28
!
neighbor 192.168.32.194
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-1:TeGig1/0/47
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
neighbor 192.168.32.206
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-2:TeGig1/0/48
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
!
!
```

Erstellen Sie die neue Zugriffssteuerungsliste für die Identifizierung des Datenverkehrs, auf den PBR angewendet wird. Bei diesem Datenverkehr handelt es sich um Webdatenverkehr (HTTP-Port 80 und HTTPS-Port 443) von den Hosts bzw. aus den Subnetzen im Test-Rack, für den der Proxydienst genutzt wird (wie in diesem Beispiel beschrieben). Der Name der Zugriffssteuerungsliste lautet beispielsweise **PERMITTED_TO_PROXY_ENV1**.

```
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www <<HTTP traffic from CL04 Public Admin VIPs leaving test rack>>
110 permit tcp 172.21.107.0/27 any eq 443 <<HTTPS traffic from CL04 Public Admin VIPs leaving test rack>>
120 permit tcp 172.21.7.224/27 any eq www <<HTTP traffic from CL04 INF-pub-adm leaving test rack>>
130 permit tcp 172.21.7.224/27 any eq 443 <<HTTPS traffic from CL04 INF-pub-adm leaving test rack>>
140 permit tcp 10.60.32.160/27 any eq www <<HTTP traffic from DVM and HLH leaving test rack>>
150 permit tcp 10.60.32.160/27 any eq 443 <<HTTPS traffic from DVM and HLH leaving test rack>>
```

Die Kernkomponente der PBR-Funktionalität wird über die Routenzuordnung **TRAFFIC_TO_PROXY_ENV1** implementiert. Die Option **pbr-statistics** wird hinzugefügt, um die Anzeige der Statistik zum Richtlinienabgleich zu ermöglichen. Hiermit wird die Anzahl von Paketen überprüft, für die die PBR-Weiterleitung durchgeführt bzw. nicht durchgeführt wird. Sequenz 10 der Routenzuordnung lässt die PBR-Behandlung für Datenverkehr zu, der die Kriterien der Zugriffssteuerungsliste **PERMITTED_TO_PROXY_ENV1** erfüllt. Dieser Datenverkehr wird an die IP-Adressen des nächsten Hops von `10.60.3.34` und `10.60.3.35` weitergeleitet. Dies sind die VIPs für die primären/sekundären Proxygeräte in unserer Beispielkonfiguration.

```
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
```

Zugriffssteuerungslisten werden als Abgleichkriterien für die Routenzuordnung **TRAFFIC_TO_PROXY_ENV1** verwendet. Wenn Datenverkehr die Kriterien der Zugriffssteuerungsliste **PERMITTED_TO_PROXY_ENV1** erfüllt, wird die reguläre Routingtabelle durch PBR außer Kraft gesetzt, und der Datenverkehr wird stattdessen an die als nächste Hops angegebenen IP-Adressen weitergeleitet.
 
Die Richtlinie **TRAFFIC_TO_PROXY_ENV1** von PBR wird auf Datenverkehr angewendet, der auf dem Border-Gerät von CL04-Hosts und öffentlichen VIPs sowie vom HLH und der DVM im Test-Rack eintrifft.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Firewallintegration finden Sie unter [Azure Stack Hub-Firewallintegration](azure-stack-firewall.md).
