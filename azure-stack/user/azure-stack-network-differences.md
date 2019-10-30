---
title: Unterschiede bei Azure Stack-Netzwerken | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Unterschiede und zu berücksichtigende Aspekte bei der Verwendung von Netzwerken in Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 09e75656fc6a00181ffb31087e19e80b92760ed2
ms.sourcegitcommit: b72729305234e13c65de3771cb08678d46ba1348
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72543679"
---
# <a name="differences-and-considerations-for-azure-stack-networking"></a>Azure Stack-Netzwerke: Unterschiede und Überlegungen

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack-Netzwerke bieten viele Features von Azure-Netzwerken. Es gibt jedoch einige wichtige Unterschiede, die Sie kennen sollten, bevor Sie ein Azure Stack-Netzwerk bereitstellen.

Dieser Artikel enthält eine Übersicht über die besonderen Überlegungen für Azure Stack-Netzwerke und die entsprechenden Features. Informationen zu allgemeinen Unterschieden zwischen Azure Stack und Azure finden Sie im Artikel [Key considerations](azure-stack-considerations.md) (Wichtige Aspekte).

## <a name="cheat-sheet-networking-differences"></a>Cheat Sheet: Unterschiede bei Netzwerken

| Dienst | Feature | Azure (global) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Mehrinstanzenfähiges DNS | Unterstützt | Noch nicht unterstützt |
|  | DNS-AAAA-Datensätze | Unterstützt | Nicht unterstützt |
|  | DNS-Zonen pro Abonnement | 100 (Standard)<br>Kann auf Anforderung erhöht werden | 100 |
|  | DNS-Datensätze pro Zone | 5000 (Standard)<br>Kann auf Anforderung erhöht werden | 5\.000 |
|  | Namenserver für die Zonendelegierung | Azure bietet vier Namenserver für jede erstellte Benutzerzone (Mandantenzone). | Azure Stack bietet zwei Namenserver für jede erstellte Benutzerzone (Mandantenzone). |
| Azure Firewall | Netzwerksicherheitsdienst | Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. | Noch nicht unterstützt. |
| Virtual Network | Peering in virtuellen Netzwerken | Verbinden von zwei virtuellen Netzwerken in derselben Region über das Azure-Backbonenetzwerk | Noch nicht unterstützt |
|  | IPv6-Adressen | Sie können eine IPv6-Adresse als Teil der [Netzwerkschnittstellenkonfiguration](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions) zuweisen. | Es wird nur IPv4 unterstützt. |
|  | DDoS Protection-Plan | Unterstützt | Noch nicht unterstützt. |
|  | IP-Konfigurationen für Skalierungsgruppe | Unterstützt | Noch nicht unterstützt. |
|  | Dienste für privaten Zugriff (Subnetz) | Unterstützt | Noch nicht unterstützt. |
|  | Dienstendpunkte | Unterstützt für interne (kein Internet) Verbindung mit Azure-Diensten | Noch nicht unterstützt. |
|  | Dienstendpunkt-Richtlinien | Unterstützt | Noch nicht unterstützt. |
|  | Diensttunnel | Unterstützt | Noch nicht unterstützt.  |
| Netzwerksicherheitsgruppen | Ergänzte Sicherheitsregeln | Unterstützt | Noch nicht unterstützt. |
|  | Effektive Sicherheitsregeln | Unterstützt | Noch nicht unterstützt. |
|  | Anwendungssicherheitsgruppen | Unterstützt | Noch nicht unterstützt. |
| Gateways für virtuelle Netzwerke | Point-to-Site-VPN Gateway | Unterstützt | Noch nicht unterstützt. |
|  | VNet-zu-VNet-Gateway | Unterstützt | Noch nicht unterstützt. |
|  | Typ des Gateways für das virtuelle Netzwerk | Azure unterstützt VPN<br> ExpressRoute <br> Hyper Net | Azure Stack unterstützt derzeit nur den VPN-Typ. |
|  | VPN Gateway-SKUs | Unterstützung für Basic, GW1, GW2, GW3, Standard High Performance, Ultra-High Performance | Unterstützung für Basic-, Standard- und High Performance-SKUs |
|  | VPN-Typ | Azure unterstützt sowohl richtlinienbasierte als auch routenbasierte VPN-Typen. | Azure Stack unterstützt nur routenbasierte VPN-Typen. |
|  | BGP-Einstellungen | Azure unterstützt die Konfiguration der BGP-Peering-Adresse und Peer-Gewichtung. | BGP-Peering-Adresse und Peer-Gewichtung werden in Azure Stack automatisch konfiguriert. Es gibt keine Möglichkeit für den Benutzer, diese Einstellungen mit eigenen Werten zu konfigurieren. |
|  | Gateway-Standardwebsite | Azure unterstützt die Konfiguration einer Standardwebsite für Tunnelerzwingung. | Noch nicht unterstützt. |
|  | Ändern der Gatewaygröße | Azure unterstützt das Ändern der Gatewaygröße nach der Bereitstellung. | Die Größenänderung wird nicht unterstützt. |
|  | Verfügbarkeitskonfiguration | Aktiv/aktiv | Aktiv/passiv |
|  | UsePolicyBasedTrafficSelectors | Azure unterstützt die Verwendung richtlinienbasierter Datenverkehrsselektoren mit routenbasierten Gatewayverbindungen. | Noch nicht unterstützt. |
| Load Balancer | SKU | Load Balancer Basic und Standard werden unterstützt. | Nur Load Balancer Basic wird unterstützt.<br>Die SKU-Eigenschaft wird nicht unterstützt.<br>Der Load Balancer der SKU „Basic“ (/path/) darf nicht mehr als fünf Front-End-IP-Konfigurationen aufweisen.  |
|  | Zones | Verfügbarkeitszonen werden unterstützt. | Noch nicht unterstützt |
|  | Unterstützung eingehender NAT-Regeln für Dienstendpunkte | Azure unterstützt das Festlegen von Dienstendpunkten für eingehende NAT-Regeln. | Da Azure Stack noch keine Dienstendpunkte unterstützt, können diese nicht festgelegt werden. |
|  | Protocol | Azure unterstützt das Festlegen von GRE oder ESP. | Protokollklassen werden in Azure Stack nicht unterstützt. |
| Öffentliche IP-Adresse | Version der öffentlichen IP-Adresse | Azure unterstützt sowohl IPv6 als auch IPv4. | Es wird nur IPv4 unterstützt. |
| Netzwerkschnittstelle | Abrufen der effektiven Routentabelle | Unterstützt | Noch nicht unterstützt. |
|  | Abrufen der effektiven ACLs | Unterstützt | Noch nicht unterstützt. |
|  | Aktivieren des beschleunigten Netzwerkbetriebs | Unterstützt | Noch nicht unterstützt. |
|  | SSL-Weiterleitung | Standardmäßig deaktiviert.  Kann aktiviert werden. | Das Umschalten dieser Einstellung wird nicht unterstützt.  Standardmäßig aktiviert. |
|  | Anwendungssicherheitsgruppen | Unterstützt | Noch nicht unterstützt. |
|  | Interne DNS-Namensbezeichnung | Unterstützt | Noch nicht unterstützt. |
|  | Version der privaten IP-Adresse | IPv6 und IPv4 werden unterstützt. | Es wird nur IPv4 unterstützt. |
|  | Statische MAC-Adresse | Nicht unterstützt | Nicht unterstützt. Für jedes Azure Stack-System wird derselbe MAC-Adresspool verwendet. |
| Network Watcher | Überwachungsfunktionen für Network Watcher-Mandantennetzwerk | Unterstützt | Noch nicht unterstützt. |
| CDN | Content Delivery Network-Profile | Unterstützt | Noch nicht unterstützt. |
| Anwendungsgateway | Lastenausgleich auf Schicht 7 | Unterstützt | Noch nicht unterstützt. |
| Traffic Manager | Weiterleiten von eingehendem Datenverkehr zur Optimierung der Anwendungsleistung und -zuverlässigkeit | Unterstützt | Noch nicht unterstützt. |
| ExpressRoute | Einrichten einer schnellen privaten Verbindung mit Microsoft Cloud Services in Ihrer lokalen Infrastruktur oder Kollokationsumgebung | Unterstützt | Unterstützung zum Verbinden von Azure Stack mit einer Express Route-Verbindung |

## <a name="api-versions"></a>API-Versionen 

Azure Stack-Netzwerke unterstützen die folgenden API-Versionen: 

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

[DNS in Azure Stack](azure-stack-dns.md)
