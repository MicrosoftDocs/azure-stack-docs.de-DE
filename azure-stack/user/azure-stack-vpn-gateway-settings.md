---
title: Konfigurieren von VPN-Gatewayeinstellungen für Azure Stack Hub
description: Erfahren Sie mehr über das Konfigurieren von VPN-Gatewayeinstellungen für Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 465c07d1f943a0a3abad8a8fc2d900444b366112
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975927"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack-hub"></a>Konfigurieren von VPN-Gatewayeinstellungen für Azure Stack Hub

Ein VPN-Gateway ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr zwischen Ihrem virtuellen Netzwerk in Azure Stack Hub und einem Remote-VPN-Gateway gesendet wird. Das Remote-VPN-Gateway kann sich in Azure befinden, es kann sich dabei aber auch um ein Gerät in Ihrem Datencenter oder um ein Gerät an einem anderen Standort handeln. Falls die beiden Endpunkte über Netzwerkkonnektivität verfügen, können Sie zwischen den beiden Netzwerken eine sichere S2S-VPN-Verbindung (Site-to-Site) herstellen.

Für eine VPN Gateway-Verbindung müssen mehrere Ressourcen konfiguriert werden, die jeweils konfigurierbare Einstellungen enthalten. In diesem Artikel werden die Ressourcen und Einstellungen beschrieben, die sich auf ein im Resource Manager-Bereitstellungsmodell erstelltes VPN-Gateway für ein virtuelles Netzwerk beziehen. Beschreibungen und Topologiediagramme für die einzelnen Verbindungslösungen finden Sie im Artikel mit [Erstellen von VPN-Gateways für Azure Stack Hub](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Einstellungen von VPN-Gateways

### <a name="gateway-types"></a>Gatewaytypen

Jedes virtuelle Azure Stack Hub-Netzwerk unterstützt ein einzelnes Gateway für virtuelle Netzwerke, das vom Typ **VPN** sein muss. Diese Unterstützung unterscheidet sich von Azure, das zusätzliche Typen unterstützt.

Achten Sie beim Erstellen eines Gateways für virtuelle Netzwerke darauf, dass der Gatewaytyp für Ihre Konfiguration richtig ist. Ein VPN-Gateway erfordert das `-GatewayType Vpn`-Flag. Beispiel:

```powershell
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
   -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
   -VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway-SKUs

Beim Erstellen eines Gateways des virtuellen Netzwerks müssen Sie die gewünschte Gateway-SKU angeben. Wählen Sie die SKUs aus, die Ihre Anforderungen im Bezug auf Workloadtypen, Durchsätze, Funktionen und SLAs erfüllen.

Die folgende Tabelle zeigt die VPN-Gateway-SKUs, die für Azure Stack Hub angeboten werden:

| | Tunneldurchsatz |Maximale IPsec-Tunnel für das VPN-Gateway |
|-------|-------|-------|
|**Basic-SKU**  | 100 MBit/s    | 20    |
|**Standard-SKU**   | 100 MBit/s  | 20 |
|**High-Performance-SKU** | 200 MBit/s | 10 |

### <a name="resizing-gateway-skus"></a>Anpassen der Größe von Gateway-SKUs

Azure Stack Hub unterstützt keinen SKU-Wechsel zwischen den unterstützten Legacy-SKUs.

Analog dazu bietet Azure Stack Hub keine Unterstützung für den Wechsel von einer unterstützten Legacy-SKU (**Basic**, **Standard** und **HighPerformance**) zu einer neueren, von Azure unterstützten SKU (**VpnGw1**, **VpnGw2** und **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Konfigurieren der Gateway-SKU

#### <a name="azure-stack-hub-portal"></a>Azure Stack Hub-Portal

Wenn Sie das Azure Stack Hub-Portal verwenden, um ein Resource Manager-Gateway für virtuelle Netzwerke zu erstellen, können Sie die Gateway-SKU in der Dropdownliste auswählen. Die Optionen entsprechen dem ausgewählten Gateway- und VPN-Typ.

#### <a name="powershell"></a>PowerShell

Im folgenden PowerShell-Beispiel wird der Parameter `-GatewaySku` als **Standard** angegeben:

```powershell
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
   -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
   -GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Verbindungstypen

Im Resource Manager-Bereitstellungsmodell ist für jede Konfiguration ein bestimmter Typ der Verbindung mit dem Gateway eines virtuellen Netzwerks erforderlich. Der verfügbare Resource Manager-PowerShell-Wert für `-ConnectionType` ist **IPsec**.

Im folgenden PowerShell-Beispiel wird eine Site-to-Site-Verbindung (S2S) erstellt, die den Verbindungstyp „IPsec“ erfordert:

```powershell
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN-Typen

Wenn Sie das Gateway des virtuellen Netzwerks für eine VPN-Gatewaykonfiguration erstellen, müssen Sie einen VPN-Typ angeben. Der ausgewählte VPN-Typ hängt von der Verbindungstopologie ab, die Sie erstellen möchten. Der VPN-Typ kann zudem von der verwendeten Hardware abhängen. S2S-Konfigurationen erfordern ein VPN-Gerät. Einige VPN-Geräte unterstützen nur einen bestimmten VPN-Typ.

> [!IMPORTANT]  
> Aktuell unterstützt Azure Stack Hub nur den routenbasierten VPN-Typ. Wenn Ihr Gerät nur richtlinienbasierte VPNs unterstützt, werden Verbindungen mit diesen Geräten über Azure Stack Hub nicht unterstützt.  
>
> Darüber hinaus bietet Azure Stack Hub aktuell keine Unterstützung für die Verwendung von richtlinienbasierten Datenverkehrsselektoren für routenbasierte Gateways, da benutzerdefinierte IPSec/IKE-Richtlinienkonfigurationen nicht unterstützt werden.

* **PolicyBased**: Bei richtlinienbasierten VPNs werden Pakete verschlüsselt und durch IPsec-Tunnel geleitet. Grundlage hierfür sind die IPsec-Richtlinien, die jeweils durch die Kombination aus Adresspräfixen zwischen Ihrem lokalen Netzwerk und dem Azure Stack Hub-VNET konfiguriert werden. Die Richtlinie (auch Datenverkehrsselektor genannt) ist in der Regel eine Zugriffsliste in der VPN-Gerätekonfiguration.

  >[!NOTE]
  >**PolicyBased** wird in Azure, aber nicht in Azure Stack Hub unterstützt.

* **RouteBased**: Bei routenbasierten VPNs werden Pakete auf der Grundlage von Routen, die in der IP-Weiterleitungstabelle oder -Routingtabelle konfiguriert sind, an die entsprechenden Tunnelschnittstellen weitergeleitet. An den Tunnelschnittstellen werden die Pakete dann ver- bzw. entschlüsselt. Die Richtlinie (bzw. der Datenverkehrsselektor) für **routenbasierte** VPNs wird im Any-to-Any-Format (bzw. unter Verwendung von Platzhaltern) konfiguriert. Sie können standardmäßig nicht geändert werden. Der Wert für einen **RouteBased**-VPN-Typ lautet **RouteBased**.

Das folgende PowerShell-Beispiel gibt `-VpnType` als **RouteBased** an. Achten Sie beim Erstellen eines Gateways darauf, dass `-VpnType` für Ihre Konfiguration richtig ist.

```powershell
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
   -Location 'West US' -IpConfigurations $gwipconfig `
   -GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Gatewayanforderungen

Die folgende Tabelle enthält die Anforderungen für VPN-Gateways.

| |Basic-VPN-Gateway (richtlinienbasiert) | Basic-VPN-Gateway (routenbasiert) | Standard-VPN-Gateway (routenbasiert) | Hochleistungs-VPN-Gateway (routenbasiert)|
|--|--|--|--|--|
| **Site-to-Site-Konnektivität (S2S-Konnektivität)** | Nicht unterstützt | Routenbasierte VPN-Konfiguration | Routenbasierte VPN-Konfiguration | Routenbasierte VPN-Konfiguration |
| **Authentifizierungsmethode**  | Nicht unterstützt | Vorinstallierter Schlüssel für S2S-Konnektivität  | Vorinstallierter Schlüssel für S2S-Konnektivität  | Vorinstallierter Schlüssel für S2S-Konnektivität  |
| **Maximale Anzahl von S2S-Verbindungen**  | Nicht unterstützt | 20 | 20| 10|
|**Aktive Routingunterstützung (BGP)** | Nicht unterstützt | Nicht unterstützt | Unterstützt | Unterstützt |

### <a name="gateway-subnet"></a>Gatewaysubnetz

Bevor Sie ein VPN-Gateway erstellen, müssen Sie ein Gatewaysubnetz erstellen. Das Gatewaysubnetz verfügt über die IP-Adressen, die von den virtuellen Computern und Diensten des Gateways für virtuelle Netzwerke verwendet werden. Bei der Erstellung des Gateways des virtuellen Netzwerks, werden Gateway-VMs für das Gatewaysubnetz bereitgestellt und mit den erforderlichen VPN Gateway-Einstellungen konfiguriert. Stellen Sie für das Gatewaysubnetz nichts anderes bereit (beispielsweise zusätzliche virtuelle Computer).

>[!IMPORTANT]
>Das Gatewaysubnetz muss den Namen **GatewaySubnet** aufweisen, damit es einwandfrei funktioniert. Anhand dieses Namens ermittelt Azure Stack Hub das Subnetz, für das die VMs und Dienste des Gateways für virtuelle Netzwerke bereitgestellt werden sollen.

Bei der Gatewayerstellung geben Sie die Anzahl der im Subnetz enthaltenen IP-Adressen an. Die IP-Adressen im Gatewaysubnetz werden den Gatewaydiensten und -VMs zugeordnet. Einige Konfigurationen erfordern mehr IP-Adressen als andere. Sehen Sie sich die Anweisungen für die Konfiguration an, die Sie erstellen möchten, und vergewissern Sie sich, dass das Gatewaysubnetz, das sie erstellen möchten, diese Anforderungen erfüllt.

Stellen Sie außerdem sicher, dass Ihr Gatewaysubnetz über genügend IP-Adressen für zukünftige Konfigurationen verfügt. Sie können zwar ein Gatewaysubnetz mit einer Größe von nur /29 erstellen, aber es empfiehlt sich, ein Gatewaysubnetz mit einer Größe von mindestens /28 (/28, /27, /26 usw.) zu erstellen. Wenn Sie später Funktionen hinzufügen, müssen Sie Ihr Gateway nicht beenden und anschließend das Gatewaysubnetz löschen und erneut erstellen, um weitere IP-Adressen zu ermöglichen.

Im folgenden Resource Manager-PowerShell-Beispiel wird ein Gatewaysubnetz mit dem Namen **GatewaySubnet** gezeigt. Sie erkennen, das mit der CIDR-Notation die Größe /27 angegeben wird. Dies ist für eine ausreichende Zahl von IP-Adressen für die meisten Konfigurationen, die derzeit üblich sind, groß genug.

```powershell
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Vermeiden Sie bei der Verwendung von Gatewaysubnetzen die Zuordnung einer Netzwerksicherheitsgruppe (NSG) zum Gatewaysubnetz. Das Zuordnen einer Netzwerksicherheitsgruppe zu diesem Subnetz kann dazu führen, dass das VPN-Gateway nicht mehr wie erwartet funktioniert. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Lokale Netzwerkgateways

Wenn Sie eine VPN-Gatewaykonfiguration in Azure erstellen, stellt das Gateway des lokalen Netzwerks meist Ihren lokalen Standort dar. In Azure Stack stellt es ein beliebiges Remote-VPN-Gerät dar, das sich außerhalb von Azure Stack Hub befindet. Bei diesem Gerät kann es sich um ein VPN-Gerät in Ihrem Datencenter (oder in einem Remotedatencenter) oder um ein VPN Gateway in Azure handeln.

Sie geben dem Gateway des lokalen Netzwerks einen Namen, stellen die öffentliche IP-Adresse des VPN-Geräts bereit und geben die Adresspräfixe an, die sich am lokalen Standort befinden. Azure sucht unter den Zieladresspräfixen nach Netzwerkdatenverkehr, sieht in der Konfiguration nach, die Sie für das Gateway des lokalen Netzwerks angegeben haben, und leitet die Pakete entsprechend weiter.

In diesem PowerShell-Beispiel wird ein neues Gateway des lokalen Netzwerks erstellt:

```powershell
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
   -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Manchmal müssen Sie die Einstellungen des lokalen Netzwerkgateways ändern, z.B. wenn Sie den Adressbereich hinzufügen oder ändern oder wenn sich die IP-Adresse des VPN-Geräts ändert. Weitere Informationen hierzu finden Sie im Artikel [Ändern der Einstellungen des lokalen Netzwerkgateways mit PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec-/IKE-Parameter

Wenn Sie eine VPN-Verbindung in Azure Stack Hub einrichten, müssen Sie die Verbindung an beiden Endpunkten konfigurieren. Wenn Sie eine VPN-Verbindung zwischen Azure Stack Hub und einem Hardwaregerät (z. B. einem als VPN-Gateway fungierenden Switch oder Router) konfigurieren, sind für dieses Gerät unter Umständen weitere Einstellungen erforderlich.

Im Gegensatz zu Azure, das mehrere Angebote als Initiator und Antwortdienst unterstützt, bietet Azure Stack Hub standardmäßig nur Unterstützung für ein Angebot. Wenn Sie verschiedene IPSec/IKE-Einstellungen mit Ihrem VPN-Gerät verwenden müssen, stehen Ihnen weitere Einstellungen zur Verfügung, um Ihre Verbindung manuell zu konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren einer IPsec/IKE-Richtlinie für Site-to-Site-VPN-Verbindungen](azure-stack-vpn-s2s.md).

> [!IMPORTANT] 
> Bei Verwendung des S2S-Tunnels werden Pakete mit zusätzlichen Headern weiter verschlüsselt, wodurch sich das Paket insgesamt vergrößert. In diesen Szenarien müssen Sie TCP **MSS** mit **1350** verknüpfen. Wenn Ihre VPN-Geräte MSS-Clamping nicht unterstützen, können Sie stattdessen auch den **MTU**-Wert der Tunnelschnittstelle auf **1400** Bytes festlegen. Weitere Informationen finden Sie unter [Optimieren der TCP-/IP-Leistung von virtuellen Netzwerken](/azure/virtual-network/virtual-network-tcpip-performance-tuning).
>

### <a name="ike-phase-1-main-mode-parameters"></a>Parameter der IKE-Phase 1 (Hauptmodus)

| Eigenschaft              | Wert|
|-|-|
| IKE-Version           | IKEv2 |
|Diffie-Hellman-Gruppe*   | ECP384 |
| Authentifizierungsmethode | Vorab ausgetauschter Schlüssel |
|Verschlüsselung und Hashalgorithmen* | AES256, SHA384 |
|SA-Gültigkeitsdauer (Zeit)     | 28.800 Sekunden|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parameter der IKE-Phase 2 (Schnellmodus)

| Eigenschaft| Wert|
|-|-|
|IKE-Version |IKEv2 |
|Verschlüsselung und Hashalgorithmen (Verschlüsselung)     | GCMAES256|
|Verschlüsselung und Hashalgorithmen (Authentifizierung) | GCMAES256|
|SA-Gültigkeitsdauer (Zeit)  | 27.000 Sekunden  |
|SA-Gültigkeitsdauer (KB) | 33.553.408     |
|Perfect Forward Secrecy (PFS)* | ECP384 |
|Dead Peer Detection | Unterstützt| 

>[!NOTE]
>Die Standardwerte für die Diffie-Hellman-Gruppe, Hashalgorithmen und Perfect Forward Secrecy wurden für Builds ab 1910 und höher geändert. Verwenden Sie die folgenden Werte für die oben genannten Parameter, wenn sich Azure Stack Hub auf einer Buildversion unter 1910 befindet:

>| Eigenschaft| Wert|
>|-|-|
>|Diffie-Hellman-Gruppe   | DHGroup2 |
>|Hashalgorithmen | SHA256 |
>|Perfect Forward Secrecy (PFS) | Keine |

\* neuer oder geänderter Parameter

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mithilfe von ExpressRoute](../operator/azure-stack-connect-expressroute.md)
