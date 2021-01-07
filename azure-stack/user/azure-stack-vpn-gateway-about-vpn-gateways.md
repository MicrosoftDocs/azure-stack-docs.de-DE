---
title: Erstellen von VPN-Gateways für Azure Stack Hub
description: Erstellen und Konfigurieren von VPN-Gateways für Azure Stack Hub
author: sethmanheim
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: e21839e5333a03b1a36322f0c632a2b278da9665
ms.sourcegitcommit: 8790b8a4ecf4421409534df5ff510d537cc000da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802013"
---
# <a name="create-vpn-gateways-for-azure-stack-hub"></a>Erstellen von VPN-Gateways für Azure Stack Hub

Um Netzwerkdatenverkehr zwischen Ihrem virtuellen Azure-Netzwerk und Ihrem lokalen Standort senden zu können, müssen Sie für Ihr virtuelles Netzwerk zunächst ein Gateway für virtuelle Netzwerke (VPN) erstellen.

Ein VPN Gateway ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr über eine öffentliche Verbindung gesendet wird. Sie können VPN-Gateways für einen sicheren Datenverkehr zwischen einem virtuellen Netzwerk in Azure Stack Hub und einem virtuellen Netzwerk in Azure verwenden. Zudem können Sie den Datenverkehr zwischen einem virtuellen Netzwerk und einem anderen Netzwerk, das mit einem VPN-Gerät verbunden ist, sicher übertragen.

Der gewünschte Gatewaytyp wird beim Erstellen eines Gateways für virtuelle Netzwerke angegeben. Azure Stack Hub unterstützt eine Art von Gateway für virtuelle Netzwerke: **VPN**.

Jedes virtuelle Netzwerk kann über zwei Gateways für virtuelle Netzwerke, aber nur über eins von jedem Typ verfügen. Abhängig von den gewählten Einstellungen können Sie ggf. mehrere Verbindungen mit einem einzelnen VPN-Gateway erstellen. Ein Beispiel für diese Art Einrichtung ist eine Verbindungskonfiguration mit mehreren Standorten.

Bevor Sie VPN-Gateways für Azure Stack Hub erstellen und konfigurieren, sollten Sie die [Überlegungen zu Azure Stack Hub-Netzwerken](azure-stack-network-differences.md) lesen, um zu erfahren, wie Konfigurationen für Azure Stack Hub sich von Azure unterscheiden.

> [!NOTE]
> In Azure muss der Bandbreitendurchsatz für die ausgewählte VPN Gateway-SKU auf alle Verbindungen aufgeteilt werden, die mit dem Gateway hergestellt wurden. In Azure Stack Hub wird der Bandbreitenwert jedoch für die VPN Gateway-SKU auf jede Verbindungsressource angewendet, die mit dem Gateway verbunden ist.
>
> Beispiel:
>
> * In Azure bietet die VPN Gateway-SKU „Basic“ eine aggregierte Durchsatzkapazität von etwa 100 Mbit/s. Wenn Sie zwei Verbindungen mit diesem VPN Gateway erstellen und eine dieser Verbindungen eine Bandbreite von 50 Mbit/s beansprucht, stehen für die andere Verbindung noch 50 Mbit/s zur Verfügung.
> * In Azure Stack Hub wird jeder Verbindung mit der VPN-Gateway-SKU „Basic“ ein Durchsatz von 100 MBit/s zugeordnet.

## <a name="configuring-a-vpn-gateway"></a>Konfigurieren eines VPN-Gateways

Eine VPN Gateway-Verbindung basiert auf mehreren, mit spezifischen Einstellungen konfigurierten Ressourcen. Die meisten dieser Ressourcen können separat konfiguriert werden. In manchen Fällen ist allerdings eine bestimmte Reihenfolge einzuhalten.

### <a name="settings"></a>Einstellungen

Die Einstellungen, die Sie für die einzelnen Ressourcen auswählen, sind für die erfolgreiche Herstellung einer Verbindung entscheidend.

Informationen zu einzelnen Ressourcen und Einstellungen für VPN Gateway finden Sie unter [VPN-Gatewaykonfigurationseinstellungen für Azure Stack Hub](azure-stack-vpn-gateway-settings.md). Dieser Artikel hilft Ihnen bei Folgendem:

* Gatewaytypen, VPN-Typen und Verbindungstypen.
* Gatewaysubnetze, lokale Netzwerkgateways und andere Ressourceneinstellungen, die Sie berücksichtigen sollten.

### <a name="deployment-tools"></a>Bereitstellungstools

Sie können mit einem Konfigurationstool wie dem Azure-Portal Ressourcen erstellen und konfigurieren. Später können Sie mit einem anderen Tool wie PowerShell zusätzliche Ressourcen konfigurieren oder ggf. vorhandene Ressourcen ändern. Derzeit können nicht alle Ressourcen und Ressourceneinstellungen über das Azure-Portal konfiguriert werden. Sollte ein bestimmtes Konfigurationstool benötigt werden, ist dies in den Anleitungen der Artikel zu den einzelnen Verbindungstopologien angegeben.

## <a name="connection-topology-diagrams"></a>Verbindungstopologiediagramme

Für VPN-Gatewayverbindungen sind verschiedene Konfigurationen verfügbar. Ermitteln Sie, welche Konfiguration am besten zu Ihren Anforderungen passt. In den folgenden Abschnitten können Sie sich Informationen und Topologiediagramme zu den folgenden VPN-Gatewayverbindungen ansehen:

* Verfügbares Bereitstellungsmodell
* Verfügbare Konfigurationstools
* Direkte Links zu Artikeln, sofern verfügbar

Orientieren Sie sich bei der Wahl einer geeigneten Verbindungstopologie an den Diagrammen und Beschreibungen in den folgenden Abschnitten. Die Diagramme zeigen die grundlegenden Topologien, aber Sie können auch komplexere Topologien erstellen, indem Sie die Diagramme als Anhaltspunkte verwenden.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site-to-Site und Multi-Site (IPsec-/IKE-VPN-Tunnel)

### <a name="site-to-site"></a>Site-to-Site

Eine VPN Gateway-S2S-Verbindung (*Site-to-Site*) ist eine Verbindung über einen IPsec-/IKE-VPN-Tunnel (IKEv2). Dieser Verbindungstyp erfordert ein lokales VPN-Gerät, das einer öffentlichen IP-Adresse zugewiesen ist. Dieses Gerät kann sich nicht hinter einer NAT befinden. S2S-Verbindungen können für standortübergreifende Konfigurationen und Hybridkonfigurationen verwendet werden.

![Konfigurationsbeispiel für eine Site-to-Site-VPN-Verbindung](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multi-Site

Bei einer *Multi-Site*-Verbindung handelt es sich um eine Abwandlung der Site-to-Site-Verbindung. Sie erstellen mehrere VPN-Verbindungen über Ihr Gateway für virtuelle Netzwerke, durch die in der Regel mehrere lokale Standorte verbunden werden. Bei Verwendung mehrerer Verbindungen müssen Sie den VPN-Typ „RouteBased“ verwenden (wird bei Verwendung klassischer VNETs als dynamisches Gateway bezeichnet). Da jedes virtuelle Netzwerk nur über ein einzelnes VPN-Gateway verfügen kann, wird die verfügbare Bandbreite von allen über das Gateway laufenden Verbindungen gemeinsam genutzt.

![Beispiel für Multi-Site-Verbindung per Azure VPN Gateway](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway-SKUs

Beim Erstellen eines Gateways für virtuelle Netzwerke für Azure Stack Hub müssen Sie die gewünschte Gateway-SKU angeben. Folgende SKUs für VPN-Gateways werden unterstützt:

* Basic
* Standard
* Leistung

Wenn Sie eine höhere Gateway-SKU (beispielsweise Standard über Basic oder Hochleistung über Standard oder Basic) auswählen, werden dem Gateway mehr CPUs und mehr Netzwerkbandbreite zugeteilt. Dadurch kann das Gateway einen höheren Netzwerkdurchsatz an das virtuelle Netzwerk bewältigen.

Die Gateway-SKU „Höchstleistung“ wird ausschließlich mit ExpressRoute verwendet und von Azure Stack Hub nicht unterstützt.

Beachten Sie bei der Auswahl der SKU Folgendes:

* Richtlinienbasierte Gateways werden von Azure Stack Hub nicht unterstützt.
* Border Gateway Protocol (BGP) wird in der Basic-SKU nicht unterstützt.
* Die gemeinsame Verwendung von VPN Gateway und ExpressRoute wird in Azure Stack Hub nicht unterstützt.

## <a name="gateway-availability"></a>Gatewayverfügbarkeit

Hochverfügbarkeitsszenarien können nur in der **Hochleistungsgateway**-Verbindungs-SKU konfiguriert werden. Im Gegensatz zu Azure, das Verfügbarkeit durch sowohl Aktiv/Aktiv- als auch Aktiv/Passiv-Konfigurationen bietet, unterstützt Azure Stack Hub nur die Aktiv/Passiv-Konfiguration.

### <a name="failover"></a>Failover

Es gibt drei mehrinstanzenfähige VMs mit Gatewayinfrastruktur in Azure Stack Hub. Zwei dieser virtuellen Computer befinden sich im aktiven Modus, während sich die dritte im redundanten Modus befindet. Aktive virtuelle Computer ermöglichen die Erstellung von VPN-Verbindungen auf sich, und die redundante VM akzeptiert nur VPN-Verbindungen, wenn ein Failover eintritt. Wenn eine aktive Gateway-VM nicht mehr verfügbar ist, führt die VPN-Verbindung nach einem kurzen Zeitraum (ein paar Sekunden) der Verbindungsunterbrechung ein Failover zur redundanten VM durch.

## <a name="estimated-aggregate-tunnel-throughput-by-sku"></a>Geschätzter aggregierter Tunneldurchsatz nach SKU

In der folgenden Tabelle sind die Gatewaytypen und der geschätzte aggregierte Durchsatz für die einzelnen Tunnel/Gateways nach Gateway-SKU angegeben:

|| Tunneldurchsatz (1) | Max. IPsec-Tunnel für VPN Gateway (2) |
|-------|-------|-------|
|**Basic-SKU** **(3)** | 100 MBit/s | 20 |
|**Standard-SKU** | 100 MBit/s | 20 |
|**High-Performance-SKU** | 200 MBit/s | 10 |

### <a name="table-notes"></a>Anmerkungen zur Tabelle

**(1)** Der Tunneldurchsatz ist kein garantierter Durchsatz für standortübergreifende Verbindungen über das Internet. Hierbei wird der maximal mögliche Durchsatz gemessen.  
**(2)** Bei der maximalen Tunnelanzahl handelt es sich um die Summe pro Azure Stack Hub-Bereitstellung für alle Abonnements.  
**(3)** BGP-Routing wird von der Basic-SKU nicht unterstützt.

> [!NOTE]
> Zwischen zwei Azure Stack Hub-Bereitstellungen kann nur eine Site-to-Site-VPN-Verbindung hergestellt werden. Der Grund: Wegen einer Einschränkung ist für die Plattform nur eine einzelne VPN-Verbindung mit der gleichen IP-Adresse zulässig. Da Azure Stack Hub das mehrinstanzenfähige Gateway nutzt, das eine öffentliche IP-Adresse für alle VPN-Gateways im Azure Stack Hub-System verwendet, kann zwischen zwei Azure Stack Hub-Systemen nur eine VPN-Verbindung hergestellt werden. Diese Einschränkung gilt auch für das Herstellen mehrerer Site-to-Site-VPN-Verbindungen mit einem beliebigen VPN-Gateway, das eine einzige IP-Adresse nutzt. Azure Stack Hub lässt nicht zu, dass mehrere Ressourcen des lokalen Netzwerkgateways mit der gleichen IP-Adresse erstellt werden.

## <a name="next-steps"></a>Nächste Schritte

* [VPN-Gatewaykonfigurationseinstellungen für Azure Stack Hub](azure-stack-vpn-gateway-settings.md)
