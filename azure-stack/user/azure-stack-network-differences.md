---
title: Unterschiede bei Azure Stack Hub-Netzwerken
description: Dieser Artikel beschreibt die Unterschiede und zu berücksichtigende Aspekte bei der Verwendung von Netzwerken in Azure Stack Hub.
author: mattbriggs
ms.date: 5/27/2020
ms.topic: article
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 2a71320a660c7dbf74ad885226bb7fcbfa78b35f
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111393"
---
# <a name="differences-and-considerations-for-azure-stack-hub-networking"></a>Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen

Azure Stack Hub-Netzwerke bieten viele Features von Azure-Netzwerken. Es gibt jedoch einige wichtige Unterschiede, die Sie kennen sollten, bevor Sie ein Azure Stack Hub-Netzwerk bereitstellen.

Dieser Artikel enthält eine Übersicht über die besonderen Überlegungen für Azure Stack Hub-Netzwerke und die entsprechenden Features. Informationen zu allgemeinen Unterschieden zwischen Azure Stack Hub und Azure finden Sie im Artikel [Wichtige Aspekte](azure-stack-considerations.md).

## <a name="cheat-sheet-networking-differences"></a>Cheat Sheet: Unterschiede bei Netzwerken

| Dienst | Funktion | Azure (global) | Azure Stack Hub |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Mehrinstanzenfähiges DNS | Unterstützt | Noch nicht unterstützt |
|  | DNS-AAAA-Datensätze | Unterstützt | Nicht unterstützt |
|  | DNS-Zonen pro Abonnement | 100 (Standard)<br>Kann auf Anforderung erhöht werden | 100 |
|  | DNS-Datensätze pro Zone | 5000 (Standard)<br>Kann auf Anforderung erhöht werden | 5\.000 |
|  | Namenserver für die Zonendelegierung | Azure bietet vier Namenserver für jede erstellte Benutzerzone (Mandantenzone). | Azure Stack Hub bietet zwei Namenserver für jede erstellte Benutzerzone (Mandantenzone). |
| Azure Firewall | Netzwerksicherheitsdienst | Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. | Noch nicht unterstützt. |
| Virtual Network | Peering in virtuellen Netzwerken | Verbinden von zwei virtuellen Netzwerken in derselben Region über das Azure-Backbonenetzwerk | Noch nicht unterstützt |
|  | IPv6-Adressen | Sie können eine IPv6-Adresse als Teil der [Netzwerkschnittstellenkonfiguration](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions) zuweisen. | Es wird nur IPv4 unterstützt. |
|  | DDoS Protection-Plan | Unterstützt | Noch nicht unterstützt. |
|  | IP-Konfigurationen für Skalierungsgruppe | Unterstützt | Noch nicht unterstützt. |
|  | Dienste für privaten Zugriff (Subnetz) | Unterstützt | Noch nicht unterstützt. |
|  | Dienstendpunkte | Unterstützt für interne (kein Internet) Verbindung mit Azure-Diensten | Noch nicht unterstützt. |
|  | Dienstendpunkt-Richtlinien | Unterstützt | Noch nicht unterstützt. |
|  | Diensttunnel | Unterstützt | Noch nicht unterstützt.  |
| Netzwerksicherheitsgruppen | Ergänzte Sicherheitsregeln | Unterstützt | Unterstützt. |
|  | Effektive Sicherheitsregeln | Unterstützt | Noch nicht unterstützt. |
|  | Anwendungssicherheitsgruppen | Unterstützt | Noch nicht unterstützt. |
|  | Regelprotokolle | TCP, UDP, ICMP, Any (Beliebig) | Nur TCP, UDP oder Any (Beliebig) |
| Gateways für virtuelle Netzwerke | Point-to-Site-VPN Gateway | Unterstützt | Noch nicht unterstützt. |
|  | VNet-zu-VNet-Gateway | Unterstützt | Noch nicht unterstützt. |
|  | Typ des Gateways für das virtuelle Netzwerk | Azure unterstützt VPN<br> ExpressRoute <br> Hyper Net | Azure Stack Hub unterstützt derzeit nur den VPN-Typ. |
|  | VPN Gateway-SKUs | Unterstützung für Basic, GW1, GW2, GW3, Standard High Performance, Ultra-High Performance | Unterstützung für Basic-, Standard- und High Performance-SKUs |
|  | VPN-Typ | Azure unterstützt sowohl richtlinienbasierte als auch routenbasierte VPN-Typen. | Azure Stack Hub unterstützt nur routenbasierte VPN-Typen. |
|  | BGP-Einstellungen | Azure unterstützt die Konfiguration der BGP-Peering-Adresse und Peer-Gewichtung. | BGP-Peering-Adresse und Peer-Gewichtung werden in Azure Stack Hub automatisch konfiguriert. Es gibt keine Möglichkeit für den Benutzer, diese Einstellungen mit eigenen Werten zu konfigurieren. |
|  | Gateway-Standardwebsite | Azure unterstützt die Konfiguration einer Standardwebsite für Tunnelerzwingung. | Noch nicht unterstützt. |
|  | Ändern der Gatewaygröße | Azure unterstützt das Ändern der Gatewaygröße nach der Bereitstellung. | Die Größenänderung wird nicht unterstützt. |
|  | Verfügbarkeitskonfiguration | Aktiv/aktiv | Aktiv/passiv |
|  | UsePolicyBasedTrafficSelectors | Azure unterstützt die Verwendung richtlinienbasierter Datenverkehrsselektoren mit routenbasierten Gatewayverbindungen. | Noch nicht unterstützt. |
| Load Balancer | SKU | Load Balancer Basic und Standard werden unterstützt. | Nur Load Balancer Basic wird unterstützt.<br>Die SKU-Eigenschaft wird nicht unterstützt.<br>Der Load Balancer der SKU „Basic“ (/path/) darf nicht mehr als fünf Front-End-IP-Konfigurationen aufweisen.  |
|  | Zones | Verfügbarkeitszonen werden unterstützt. | Noch nicht unterstützt |
|  | Unterstützung eingehender NAT-Regeln für Dienstendpunkte | Azure unterstützt das Festlegen von Dienstendpunkten für eingehende NAT-Regeln. | Da Azure Stack Hub noch keine Dienstendpunkte unterstützt, können diese nicht festgelegt werden. |
|  | Protocol | Azure unterstützt das Festlegen von GRE oder ESP. | Protokollklassen werden in Azure Stack Hub nicht unterstützt. |
| Öffentliche IP-Adresse | Version der öffentlichen IP-Adresse | Azure unterstützt sowohl IPv6 als auch IPv4. | Es wird nur IPv4 unterstützt. |
| | SKU | Azure unterstützt Basic und Standard. | Nur Basic wird unterstützt. |
| Netzwerkschnittstelle | Abrufen der effektiven Routentabelle | Unterstützt | Noch nicht unterstützt. |
|  | Abrufen der effektiven ACLs | Unterstützt | Noch nicht unterstützt. |
|  | Aktivieren des beschleunigten Netzwerkbetriebs | Unterstützt | Noch nicht unterstützt. |
|  | SSL-Weiterleitung | Standardmäßig deaktiviert.  Kann aktiviert werden. | Das Umschalten dieser Einstellung wird nicht unterstützt.  Standardmäßig aktiviert. |
|  | Anwendungssicherheitsgruppen | Unterstützt | Noch nicht unterstützt. |
|  | Interne DNS-Namensbezeichnung | Unterstützt | Noch nicht unterstützt. |
|  | Version der privaten IP-Adresse | IPv6 und IPv4 werden unterstützt. | Es wird nur IPv4 unterstützt. |
|  | Statische MAC-Adresse | Nicht unterstützt | Wird nicht unterstützt. Für jedes Azure Stack Hub-System wird derselbe MAC-Adresspool verwendet. |
| Network Watcher | Überwachungsfunktionen für Network Watcher-Mandantennetzwerk | Unterstützt | Noch nicht unterstützt. |
| CDN | Content Delivery Network-Profile | Unterstützt | Noch nicht unterstützt. |
| Anwendungsgateway | Lastenausgleich auf Schicht 7 | Unterstützt | Noch nicht unterstützt. |
| Traffic Manager | Weiterleiten von eingehendem Datenverkehr zur Optimierung der Anwendungsleistung und -zuverlässigkeit | Unterstützt | Noch nicht unterstützt. |
| ExpressRoute | Einrichten einer schnellen privaten Verbindung mit Microsoft Cloud Services in Ihrer lokalen Infrastruktur oder Kollokationsumgebung | Unterstützt | Unterstützung zum Verbinden von Azure Stack Hub mit einer Express Route-Verbindung |

## <a name="api-versions"></a>API-Versionen 

Azure Stack Hub-Netzwerke unterstützen die folgenden API-Versionen: 

- 2018-11-01
- 2018-10-01
- 2018-08-01
- 2018-07-01
- 2018-06-01
- 2018-05-01
- 2018-04-01
- 2018-03-01
- 2018-02-01
- 2018-01-01
- 2017-11-01
- 2017-10-01

## <a name="next-steps"></a>Nächste Schritte

[DNS in Azure Stack Hub](azure-stack-dns.md)
