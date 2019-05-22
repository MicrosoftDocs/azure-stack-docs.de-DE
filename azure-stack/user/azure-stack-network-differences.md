---
title: 'Azure Stack-Netzwerke: Unterschiede und Überlegungen | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Unterschiede und zu berücksichtigende Aspekte bei der Verwendung von Netzwerken in Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 05/16/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 841e30367ded29e10c28573056f4a70361e28a9e
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782569"
---
# <a name="considerations-for-azure-stack-networking"></a>Überlegungen zu Azure Stack-Netzwerken

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack-Netzwerke bieten viele Features von Azure-Netzwerken. Es gibt jedoch einige wichtige Unterschiede, die Sie kennen sollten, bevor Sie ein Azure Stack-Netzwerk bereitstellen.

Dieser Artikel enthält eine Übersicht über die besonderen Überlegungen für Azure Stack-Netzwerke und die entsprechenden Features. Informationen zu allgemeinen Unterschieden zwischen Azure Stack und Azure finden Sie im Artikel [Key considerations](azure-stack-considerations.md) (Wichtige Aspekte).

## <a name="cheat-sheet-networking-differences"></a>Cheat Sheet: Unterschiede bei Netzwerken

| Dienst | Feature | Azure (global) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Mehrinstanzenfähiges DNS | Unterstützt | Noch nicht unterstützt |
|  | DNS-AAAA-Datensätze | Unterstützt | Nicht unterstützt |
|  | DNS-Zonen pro Abonnement | 100 (Standard)<br>Kann auf Anforderung erhöht werden | 100 |
|  | DNS-Datensätze pro Zone | 5000 (Standard)<br>Kann auf Anforderung erhöht werden | 5.000 |
|  | Namenserver für die Zonendelegierung | Azure bietet vier Namenserver für jede erstellte Benutzerzone (Mandantenzone). | Azure Stack bietet zwei Namenserver für jede erstellte Benutzerzone (Mandantenzone). |
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
|  | Typ des Gateways für das virtuelle Netzwerk | Azure unterstützt VPN<br> ExpressRoute <br> Hyper Net | Azure Stack unterstützt derzeit nur VPN als Typ. |
|  | VPN Gateway-SKUs | Unterstützung für Basic, GW1, GW2, GW3, Standard High Performance, Ultra-High Performance | Unterstützung für Basic-, Standard- und High Performance-SKUs |
|  | VPN-Typ | Azure unterstützt sowohl „Richtlinienbasiert“ als auch „Routenbasiert“. | Azure Stack unterstützt nur „Routenbasiert“. |
|  | BGP-Einstellungen | Azure unterstützt die Konfiguration der BGP-Peering-Adresse und Peer-Gewichtung. | BGP-Peering-Adresse und Peer-Gewichtung werden in Azure Stack automatisch konfiguriert. Es gibt keine Möglichkeit für den Benutzer, diese Einstellungen mit eigenen Werten zu konfigurieren. |
|  | Gateway-Standardwebsite | Azure unterstützt die Konfiguration einer Standardwebsite für Tunnelerzwingung. | Noch nicht unterstützt. |
|  | Ändern der Gatewaygröße | Azure unterstützt das Ändern der Gatewaygröße nach der Bereitstellung. | Die Änderung der Größe wird nicht unterstützt. |
|  | Aktiv/Aktiv-Konfiguration | Unterstützt | Noch nicht unterstützt. |
|  | UsePolicyBasedTrafficSelectors | Azure unterstützt die Verwendung richtlinienbasierter Datenverkehrsselektoren mit routenbasierten Gatewayverbindungen. | Noch nicht unterstützt. |
| Load Balancer | SKU | Load Balancer Basic und Standard werden unterstützt. | Nur Load Balancer Basic wird unterstützt.  Die SKU-Eigenschaft wird nicht unterstützt. |
|  | Zones | Verfügbarkeitszonen werden unterstützt. | Noch nicht unterstützt |
|  | Unterstützung eingehender NAT-Regeln für Dienstendpunkte | Azure unterstützt das Festlegen von Dienstendpunkten für eingehende NAT-Regeln. | Azure Stack unterstützt noch keine Dienstendpunkte, daher können keine festgelegt werden. |
|  | Protocol | Azure unterstützt das Festlegen von GRE oder ESP. | Protokollklassen werden in Azure Stack nicht unterstützt. |
| Öffentliche IP-Adresse | Version der öffentlichen IP-Adresse | Azure unterstützt IPv6 und IPv4 | Es wird nur IPv4 unterstützt. |
| Netzwerkschnittstelle | Abrufen der effektiven Routentabelle | Unterstützt | Noch nicht unterstützt. |
|  | Abrufen der effektiven ACLs | Unterstützt | Noch nicht unterstützt. |
|  | Aktivieren des beschleunigten Netzwerkbetriebs | Unterstützt | Noch nicht unterstützt. |
|  | SSL-Weiterleitung | Standardmäßig deaktiviert.  Kann aktiviert werden. | Das Umschalten dieser Einstellung wird nicht unterstützt.  Standardmäßig aktiviert. |
|  | Anwendungssicherheitsgruppen | Unterstützt | Noch nicht unterstützt. |
|  | Interne DNS-Namensbezeichnung | Unterstützt | Noch nicht unterstützt. |
|  | Version der privaten IP-Adresse | IPv6 und IPv4 werden unterstützt. | Es wird nur IPv4 unterstützt. |
| Network Watcher | Überwachungsfunktionen für Network Watcher-Mandantennetzwerk | Unterstützt | Noch nicht unterstützt. |
| CDN | Content Delivery Network-Profile | Unterstützt | Noch nicht unterstützt. |
| Anwendungsgateway | Lastenausgleich auf Schicht 7 | Unterstützt | Noch nicht unterstützt. |
| Traffic Manager | Weiterleiten von eingehendem Datenverkehr zur Optimierung der Anwendungsleistung und -zuverlässigkeit | Unterstützt | Noch nicht unterstützt. |
| ExpressRoute | Einrichten einer schnellen privaten Verbindung mit Microsoft Cloud Services in Ihrer lokalen Infrastruktur oder Kollokationsumgebung | Unterstützt | Unterstützung zum Verbinden von Azure Stack mit einer Express Route-Verbindung |

## <a name="next-steps"></a>Nächste Schritte

[DNS in Azure Stack](azure-stack-dns.md)
