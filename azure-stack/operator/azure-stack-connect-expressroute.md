---
title: Herstellen einer Verbindung von Azure Stack mit Azure mithilfe von ExpressRoute | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von ExpressRoute eine Verbindung zwischen virtuellen Netzwerken in Azure Stack und virtuellen Netzwerken in Azure herstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: efea925c421a031206ac33b49a13bd486bab1eea
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73636888"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Herstellen einer Verbindung zwischen Azure Stack und Azure mithilfe von Azure ExpressRoute

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesem Artikel wird beschrieben, wie Sie ein virtuelles Azure Stack-Netzwerk über eine direkte [Microsoft Azure ExpressRoute](/azure/expressroute/)-Verbindung mit einem virtuellen Azure-Netzwerk verbinden.

Sie können diesen Artikel als Tutorial nutzen und mithilfe der Beispiele die gleiche Testumgebung einrichten. Alternativ können Sie den Artikel als exemplarische Vorgehensweise nutzen und anhand der Anweisungen Ihre eigene ExpressRoute-Umgebung einrichten.

## <a name="overview-assumptions-and-prerequisites"></a>Übersicht, Annahmen und Voraussetzungen

Mit Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine von einem Konnektivitätsanbieter bereitgestellte private Verbindung auf die Microsoft Cloud ausdehnen. Bei ExpressRoute handelt es sich nicht um eine VPN-Verbindung über das öffentliche Internet.

Weitere Informationen zu Azure ExpressRoute finden Sie unter [ExpressRoute-Übersicht](/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Annahmen

In diesem Artikel wird Folgendes vorausgesetzt:

* Sie verfügen über ausreichende praktische Kenntnisse von Azure.
* Sie verfügen über grundlegende Kenntnisse von Azure Stack.
* Sie verfügen über grundlegende Kenntnisse von Netzwerken.

### <a name="prerequisites"></a>Voraussetzungen

Für die Verbindungsherstellung zwischen Azure Stack und Azure mithilfe von ExpressRoute gelten die folgenden Anforderungen:

* Eine bereitgestellte [ExpressRoute-Verbindung](/azure/expressroute/expressroute-circuit-peerings) über einen [Konnektivitätsanbieter](/azure/expressroute/expressroute-locations).
* Ein Azure-Abonnement zum Erstellen einer ExpressRoute-Verbindung und von VNets in Azure.
* Ein Router, der Folgendes unterstützt:
  * Site-to-Site-VPN-Verbindungen zwischen seiner LAN-Schnittstelle und dem mehrinstanzenfähigen Azure Stack-Gateway
  * Erstellung mehrerer VRF-Instanzen (Virtual Routing and Forwarding), falls die Azure Stack-Bereitstellung mehrere Mandanten umfasst
* Ein Router, der über Folgendes verfügt:
  * Einen WAN-Port, der mit der ExpressRoute-Verbindung verbunden ist
  * Einen LAN-Port, der mit dem mehrinstanzenfähigen Azure Stack-Gateway verbunden ist

### <a name="expressroute-network-architecture"></a>ExpressRoute-Netzwerkarchitektur

Die folgende Abbildung zeigt die Azure Stack- und die Azure-Umgebung nach der Einrichtung von ExpressRoute anhand der Beispiele in diesem Artikel:

![ExpressRoute-Netzwerk](media/azure-stack-connect-expressroute/Conceptual.png)

In der folgenden Abbildung ist dargestellt, wie für mehrere Mandanten eine Verbindung von der Azure Stack-Infrastruktur über den ExpressRoute-Router mit Azure im hergestellt wird:

![Verbindungen für mehrere Mandanten mit ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

Im Beispiel in diesem Artikel wird die in diesem Diagramm dargestellte mehrinstanzenfähige Architektur verwendet, um Azure Stack mithilfe von privatem ExpressRoute-Peering mit Azure zu verbinden. Bei der Verbindung handelt es sich um eine Site-to-Site-VPN-Verbindung zwischen dem Gateway des virtuellen Netzwerks in Azure Stack und einem ExpressRoute-Router.

Die Schritte in diesem Artikel veranschaulichen, wie Sie eine End-to-End-Verbindung zwischen zwei VNETs von zwei unterschiedlichen Mandanten in Azure Stack mit entsprechenden VNETs in Azure herstellen. Die Einrichtung von zwei Mandanten ist optional. Sie können diese Schritte auch für einen einzelnen Mandanten verwenden.

## <a name="configure-azure-stack"></a>Konfigurieren von Azure Stack

Verwenden Sie zum Einrichten der Azure Stack-Umgebung für den ersten Mandanten die folgenden Schritte als Leitfaden. Falls Sie mehrere Mandanten einrichten, wiederholen Sie die folgenden Schritte:

>[!NOTE]
>In diesen Schritten wird gezeigt, wie Sie Ressourcen im Azure Stack-Portal erstellen, Sie können jedoch auch PowerShell verwenden.

![Einrichtung des Azure Stack-Netzwerks](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit der Konfiguration von Azure Stack beginnen, benötigen Sie Folgendes:

* Eine Bereitstellung eines integrierten Azure Stack-Systems oder ein Azure Stack Development Kit (ASDK). Informationen zum Bereitstellen des ASDK finden Sie im [Schnellstart für die Azure Stack Development Kit-Bereitstellung](../asdk/asdk-download.md).
* Ein Angebot in Azure Stack, das von Ihren Benutzern abonniert werden kann. Weitere Informationen finden Sie unter [Übersicht über Dienste, Pläne, Angebote und Abonnements](service-plan-offer-subscription-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Erstellen von Netzwerkressourcen in Azure Stack

Verwenden Sie die folgenden Verfahren, um die erforderlichen Netzwerkressourcen in Azure Stack für einen Mandanten zu erstellen.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Erstellen des virtuellen Netzwerks und des VM-Subnetzes

1. Melden Sie sich beim Azure Stack-Benutzerportal an.

2. Klicken Sie im Portal auf **+ Ressource erstellen**.

3. Wählen Sie unter **Azure Marketplace** die Option **Netzwerk** aus.

4. Wählen Sie unter **Ausgewählte** die Option **Virtuelles Netzwerk** aus.

5. Geben Sie unter **Virtuelles Netzwerk erstellen** die in der folgenden Tabelle aufgeführten Werte in die entsprechenden Felder ein:

   |Feld  |Wert  |
   |---------|---------|
   |NAME     |Tenant1VNet1         |
   |Adressraum     |10.1.0.0/16|
   |Subnetzname     |Tenant1-Sub1|
   |Subnetzadressbereich     |10.1.1.0/24|

6. Daraufhin sollte das Feld **Abonnement** mit dem zuvor erstellten Abonnement aufgefüllt werden. Für die verbleibenden Felder:

    * Klicken Sie unter **Ressourcengruppe** auf **Neu erstellen**, um eine neue Ressourcengruppe zu erstellen, oder wählen Sie **Vorhandene verwenden** aus, wenn Sie bereits über eine Ressourcengruppe verfügen.
    * Überprüfen Sie den standardmäßigen **Speicherort**.
    * Klicken Sie auf **Create**.
    * Optional: Klicken Sie auf **An Dashboard anheften**.

#### <a name="create-the-gateway-subnet"></a>Erstellen des Gatewaysubnetzes

1. Wählen Sie unter **Virtuelles Netzwerk** die Option **Tenant1VNet1** aus.
1. Wählen Sie unter **EINSTELLUNGEN** die Option **Subnetze** aus.
1. Klicken Sie auf **+ Gatewaysubnetz**, um dem virtuellen Netzwerk ein Gatewaysubnetz hinzuzufügen.
1. Der Name des Subnetzes wird standardmäßig auf **GatewaySubnet** festgelegt. Gatewaysubnetze stellen einen Sonderfall dar und müssen diesen Namen besitzen, damit sie ordnungsgemäß funktionieren.
1. Überprüfen Sie, ob die unter **Adressbereich** angegebene Adresse **10.1.0.0/24** lautet.
1. Klicken Sie auf **OK**, um das Gatewaysubnetz zu erstellen.

#### <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

1. Klicken Sie im Azure Stack-Benutzerportal auf **+ Ressource erstellen**.
1. Wählen Sie unter **Azure Marketplace** die Option **Netzwerk** aus.
1. Wählen Sie in der Liste mit den Netzwerkressourcen den Eintrag **Gateway von Virtual Network** aus.
1. Geben Sie **GW1** in das Feld **Name** ein.
1. Wählen Sie **Virtuelles Netzwerk** aus.
1. Wählen Sie in der Dropdownliste **Tenant1VNet1** aus.
1. Klicken Sie auf **Öffentliche IP-Adresse**, auf **Öffentliche IP-Adresse auswählen** und anschließend auf **Neu erstellen**.
1. Geben Sie im Feld **Name** die Zeichenfolge **GW1-PiP** ein, und klicken Sie auf **OK**.
1. Für **VPN-Typ** sollte standardmäßig **Routenbasiert** ausgewählt sein. Behalten Sie diese Einstellung bei.
1. Vergewissern Sie sich, dass **Abonnement** und **Speicherort** korrekt sind. Klicken Sie auf **Create**.

#### <a name="create-the-local-network-gateway"></a>Erstellen des Gateways des lokalen Netzwerks

Die Ressource „Gateway für lokales Netzwerk“ gibt das Remotegateway am anderen Ende der VPN-Verbindung an. In diesem Beispiel ist das Remoteende der Verbindung die LAN-Unterschnittstelle des ExpressRoute-Routers. Für „Tenant 1“ (Mandant 1) im vorherigen Diagramm lautet die Remoteadresse 10.60.3.255.

1. Melden Sie sich beim Azure Stack-Benutzerportal an, und wählen Sie **+ Ressource erstellen** aus.
1. Wählen Sie unter **Azure Marketplace** die Option **Netzwerk** aus.
1. Wählen Sie in der Ressourcenliste den Eintrag für das **lokale Netzwerkgateway** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **ER-Router-GW** ein.
1. Ziehen Sie für das Feld **IP-Adresse** die vorherige Abbildung heran. Die IP-Adresse der LAN-Unterschnittstelle für den ExpressRoute-Router von „Tenant 1“ lautet 10.60.3.255. Geben Sie für Ihre eigene Umgebung die IP-Adresse der entsprechenden Schnittstelle Ihres Routers ein.
1. Geben Sie im Feld **Adressraum** den Adressraum der VNETs ein, mit denen Sie in Azure eine Verbindung herstellen möchten. Die Subnetze für den Mandanten 1 lauten wie folgt:

   * 192.168.2.0/24 ist das Hub-VNET in Azure.
   * 10.100.0.0/16 ist das Spoke-VNET in Azure.

   > [!IMPORTANT]
   > In diesem Beispiel wird davon ausgegangen, dass Sie statische Routen für die Site-to-Site-VPN-Verbindung zwischen dem Azure Stack-Gateway und dem ExpressRoute-Router verwenden.

1. Vergewissern Sie sich, dass Ihr **Abonnement**, die **Ressourcengruppe** und der **Speicherort** richtig sind. Klicken Sie anschließend auf **Erstellen**.

#### <a name="create-the-connection"></a>Erstellen der Verbindung

1. Klicken Sie im Azure Stack-Benutzerportal auf **+ Ressource erstellen**.
1. Wählen Sie unter **Azure Marketplace** die Option **Netzwerk** aus.
1. Wählen Sie in der Ressourcenliste die Option **Verbindung** aus.
1. Wählen Sie unter **Grundlagen** die Option **Standort-zu-Standort (IPsec)** als **Verbindungstyp** aus.
1. Wählen Sie das **Abonnement**, die **Ressourcengruppe** und den **Speicherort** aus. Klicken Sie auf **OK**.
1. Klicken Sie unter **Einstellungen** auf **Gateway für virtuelle Netzwerke**, und wählen Sie dann **GW1** aus.
1. Wählen Sie **Gateway für lokales Netzwerk** und dann **Azure-GW** aus.
1. Geben Sie **ConnectToAzure** in das Feld **Verbindungsname** ein.
1. Geben Sie **abc123** in das Feld **Gemeinsam verwendeter Schlüssel (PSK)** ein, und klicken Sie dann auf **OK**.
1. Klicken Sie unter **Zusammenfassung** auf **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Abrufen der öffentlichen IP-Adresse des Gateways für virtuelle Netzwerke

Nachdem Sie das Gateway für virtuelle Netzwerke erstellt haben, können Sie die öffentliche IP-Adresse des Gateways abrufen. Notieren Sie sich diese Adresse für den Fall, dass Sie sie später für Ihre Bereitstellung benötigen. Je nach Bereitstellung wird diese Adresse als **Interne IP-Adresse** verwendet.

1. Klicken Sie im Azure Stack-Benutzerportal auf **Alle Ressourcen**.
1. Wählen Sie unter **Alle Ressourcen** das Gateway für virtuelle Netzwerke aus (**GW1** im Beispiel).
1. Wählen Sie unter **Gateway für virtuelle Netzwerke** in der Ressourcenliste die Option **Übersicht** aus. Alternativ können Sie auf **Eigenschaften** klicken.
1. Notieren Sie die unter **Öffentliche IP-Adresse** angezeigte IP-Adresse. Für die Beispielkonfiguration lautet diese Adresse 192.68.102.1.

#### <a name="create-a-virtual-machine-vm"></a>Erstellen eines virtuellen Computers (VM)

Zum Testen des Datenverkehrs über die VPN-Verbindung benötigen Sie virtuelle Computer, um Daten im Azure Stack-VNET senden und empfangen zu können. Erstellen Sie eine VM, und stellen Sie sie im VM-Subnetz für Ihr virtuelles Netzwerk bereit.

1. Klicken Sie im Azure Stack-Benutzerportal auf **+ Ressource erstellen**.
1. Wählen Sie unter **Azure Marketplace** die Option **Compute** aus.
1. Wählen Sie in der Liste mit den VM-Images das Image **Windows Server 2016 Datacenter Eval** aus.

   >[!NOTE]
   >Falls das für diesen Artikel verwendete Image nicht verfügbar ist, bitten Sie Ihren Azure Stack-Bediener, ein anderes Windows Server-Image bereitzustellen.

1. Wählen Sie unter **Virtuellen Computer erstellen** die Option **Grundlagen** aus, und geben Sie **VM01** als **Name** ein.
1. Geben Sie einen gültigen Benutzernamen und ein Kennwort ein. Mit diesem Konto melden Sie sich bei dem virtuellen Computer an, nachdem dieser erstellt wurde.
1. Geben Sie ein **Abonnement**, eine **Ressourcengruppe** und einen **Speicherort** an. Klicken Sie auf **OK**.
1. Wählen Sie unter **Größe auswählen** eine VM-Größe für diese Instanz aus, und klicken Sie dann auf **Auswählen**.
1. Überprüfen Sie unter **Einstellungen** Folgendes:

   * Das virtuelle Netzwerk ist **Tenant1VNet1**.
   * Das Subnetz ist auf **10.1.1.0/24** festgelegt.

   Verwenden Sie die Standardeinstellungen, und klicken Sie auf **OK**.

1. Überprüfen Sie unter **Zusammenfassung** die VM-Konfiguration, und klicken Sie anschließend auf **OK**.

Um weitere Mandanten hinzuzufügen, wiederholen Sie die Schritte, die Sie in den folgenden Abschnitten ausgeführt haben:

* [Erstellen des virtuellen Netzwerks und des VM-Subnetzes](#create-the-virtual-network-and-vm-subnet)
* [Erstellen des Gatewaysubnetzes](#create-the-gateway-subnet)
* [Erstellen des Gateways für virtuelle Netzwerke](#create-the-virtual-network-gateway)
* [Erstellen des Gateways des lokalen Netzwerks](#create-the-local-network-gateway)
* [Erstellen der Verbindung](#create-the-connection)
* [Erstellen eines virtuellen Computers](#create-a-virtual-machine)

Vergessen Sie nicht, die IP-Adressen zu ändern, um Überlappungen zu vermeiden, falls Sie den Mandanten 2 als Beispiel verwenden.

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>Konfigurieren des virtuellen NAT-Computers für das Durchlaufen des Gateways

> [!IMPORTANT]
> Dieser Abschnitt gilt nur für ASDK-Bereitstellungen. NAT ist für Bereitstellungen mit mehreren Knoten nicht erforderlich.

Das ASDK ist eigenständig und von dem Netzwerk isoliert, in dem der physische Host bereitgestellt wird. Das VIP-Netzwerk, mit dem die Gateways verbunden sind, ist nicht extern, sondern befindet sich hinter einem Router, der Netzwerkadressenübersetzung (Network Address Translation, NAT) ausführt.

Der Router ist der ASDK-Host, der mit der RRAS-Rolle (Routing and Remote Access Services) ausgeführt wird. Sie müssen die NAT auf dem ASDK-Host konfigurieren, um die Site-to-Site-VPN-Verbindung an beiden Endpunkten zu ermöglichen.

#### <a name="configure-the-nat"></a>Konfigurieren der NAT

1. Melden Sie sich mit Ihrem Administratorkonto am Azure Stack-Hostcomputer an.
1. Führen Sie das Skript in einer PowerShell-ISE mit erhöhten Rechten aus. Das Skript gibt Ihre **externe BGPNAT-Adresse** zurück.

   ```powershell
   Get-NetNatExternalAddress
   ```

1. Kopieren und bearbeiten Sie das folgende PowerShell-Skript, um die NAT zu konfigurieren. Ersetzen Sie im Skript die `External BGPNAT address` und `Internal IP address` durch die folgenden Beispielwerte:

   * Verwenden Sie für die *externe BGPNAT-Adresse* 10.10.0.62.
   * Verwenden Sie für die *interne IP-Adresse* 192.168.102.1.

   Führen Sie das folgende Skript in einer PowerShell ISE mit erhöhten Rechten aus:

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>Konfigurieren von Azure

Nachdem Sie Azure Stack konfiguriert haben, können Sie die Azure-Ressourcen bereitstellen. die folgende Abbildung zeigt ein Beispiel für ein virtuelles Mandantennetzwerk in Azure. Sie können für Ihr VNET in Azure ein beliebiges Namens- und Adressierungsschema verwenden. Der Adressbereich der VNETs in Azure und Azure Stack muss aber eindeutig sein und darf sich nicht überschneiden:

![Azure-VNETs](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Die Ressourcen, die Sie in Azure bereitstellen, ähneln den Ressourcen, die Sie in Azure Stack bereitgestellt haben. Sie stellen die folgenden Komponenten bereit:

* Virtuelle Netzwerke und Subnetze
* Ein Gatewaysubnetz
* Ein virtuelles Netzwerkgateway
* Eine Verbindung
* Eine ExpressRoute-Verbindung

Die Azure-Netzwerkinfrastruktur im Beispiel ist wie folgt konfiguriert:

* Es wird ein Hub-and-Spoke-VNET-Standardmodell verwendet (Hub: 192.168.2.0/24, Spoke 10.100.0.0./16). Weitere Informationen zu einer Hub-Spoke-Netzwerktopologie finden Sie unter [Implementieren einer Hub-Spoke-Netzwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Die Workloads werden im Spoke-VNET bereitgestellt, und die ExpressRoute-Verbindung wird mit dem Hub-VNET hergestellt.
* Die beiden VNETs sind über VNET-Peering verbunden.

### <a name="configure-the-azure-vnets"></a>Konfigurieren der Azure-VNETs

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen am Azure-Portal an.
1. Erstellen Sie das Hub-VNET mit dem Adressbereich 192.168.2.0/24.
1. Erstellen Sie über den Adressbereich 192.168.2.0/25 ein Subnetz, und fügen Sie ein Gatewaysubnetz hinzu, indem Sie den Adressbereich 192.168.2.128/27 nutzen.
1. Erstellen Sie das Spoke-VNET und das Subnetz über den Adressbereich 10.100.0.0/16.

Weitere Informationen zum Erstellen virtueller Netzwerke in Azure finden Sie unter [Create a virtual network](/azure/virtual-network/manage-virtual-network#create-a-virtual-network) (Erstellen eines virtuellen Netzwerks).

### <a name="configure-an-expressroute-circuit"></a>Konfigurieren einer ExpressRoute-Verbindung

1. Überprüfen Sie die ExpressRoute-Voraussetzungen unter [Voraussetzungen und Checkliste für ExpressRoute](/azure/expressroute/expressroute-prerequisites).

1. Führen Sie die Schritte unter [Erstellen und Ändern einer ExpressRoute-Verbindung](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) aus, um mit Ihrem Azure-Abonnement eine ExpressRoute-Verbindung zu erstellen.

   >[!NOTE]
   >Geben Sie für Ihren Dienst den Dienstschlüssel für Ihre Verbindung an, damit die ExpressRoute-Verbindung an diesem Ende eingerichtet werden kann.

1. Führen Sie die Schritte unter [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung](/azure/expressroute/expressroute-howto-routing-portal-resource-manager) aus, um das private Peering für die ExpressRoute-Verbindung zu konfigurieren.

### <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

Führen Sie die Schritte unter [Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute mit PowerShell](/azure/expressroute/expressroute-howto-add-gateway-resource-manager) aus, um ein Gateway des virtuellen Netzwerks für ExpressRoute im Hub-VNET zu erstellen.

### <a name="create-the-connection"></a>Erstellen der Verbindung

Führen Sie die Schritte unter [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager) aus, um die ExpressRoute-Verbindung mit dem Hub-VNET zu verknüpfen.

### <a name="peer-the-vnets"></a>Verknüpfen der VNETs per Peering

Verknüpfen Sie die Hub-and-Spoke-VNETs, indem Sie die Schritte unter [Herstellen einer Verbindung zwischen virtuellen Netzwerken mittels Peering über das Azure-Portal](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal) ausführen. Stellen Sie beim Konfigurieren des VNET-Peerings sicher, dass Sie die folgenden Optionen auswählen:

* Vom Hub zum Spoke: **Gatewaytransit zulassen**
* Vom Spoke zum Hub: **Remotegateways verwenden**

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Stellen Sie Ihre virtuellen Workloadcomputer im Spoke-VNET bereit.

Wiederholen Sie diese Schritte für alle zusätzlichen Mandanten-VNETs, für die Sie in Azure über die jeweiligen ExpressRoute-Verbindungen eine Verbindung herstellen möchten.

## <a name="configure-the-router"></a>Konfigurieren des Routers

Sie können das folgende Konfigurationsdiagramm des ExpressRoute-Routers als Orientierungshilfe für die Konfiguration Ihres ExpressRoute-Routers verwenden. In dieser Abbildung sind zwei Mandanten („Tenant 1“ und „Tenant 2“) mit ihren jeweiligen ExpressRoute-Verbindungen dargestellt. Jeder Mandant ist mit einer eigenen VRF-Instanz (Virtual Routing and Forwarding) auf der LAN- und WAN-Seite des ExpressRoute-Routers verbunden. Durch diese Konfiguration wird eine End-to-End-Isolation zwischen den beiden Mandanten sichergestellt. Notieren Sie sich die IP-Adressen, die in den Routerschnittstellen verwendet werden, während Sie das Konfigurationsbeispiel durcharbeiten.

![Konfiguration des ExpressRoute-Routers](media/azure-stack-connect-expressroute/EndToEnd.png)

Sie können einen beliebigen Router verwenden, der ein IKEv2-VPN und BGP unterstützt, um die Site-to-Site-VPN-Verbindung von Azure Stack zu beenden. Derselbe Router wird verwendet, um über eine ExpressRoute-Verbindung eine Verbindung mit Azure herzustellen.

Das folgende Konfigurationsbeispiel für den Aggregation Services Router der ASR 1000-Reihe von Cisco unterstützt die im Diagramm *Konfiguration des ExpressRoute-Routers* dargestellte Netzwerkinfrastruktur.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testen der Verbindung

Testen Sie Ihre Verbindung, nachdem Sie die Site-to-Site-Verbindung und die ExpressRoute-Verbindung hergestellt haben.

Führen Sie folgende Pingtests durch:

* Melden Sie sich bei einer der VMs an, die Sie in Ihrem Azure VNET erstellt haben, und pingen Sie die VM, die Sie in Azure Stack erstellt haben.
* Melden Sie sich bei einer der VMs an, die Sie in Azure Stack erstellt haben, und pingen Sie die VM, die Sie in Azure VNET erstellt haben.

>[!NOTE]
>Um sicherzustellen, dass Sie den Datenverkehr über die Site-to-Site- und ExpressRoute-Verbindungen senden, müssen Sie die dedizierte IP-Adresse (DIP) der VM an beiden Enden pingen, und nicht die VIP-Adresse der VM.

### <a name="allow-icmp-in-through-the-firewall"></a>Zulassen von ICMP in eingehender Richtung über die Firewall

Standardmäßig lässt Windows Server 2016 keine eingehenden ICMP-Pakete durch die Firewall zu. Für jeden virtuellen Computer, den Sie für Pingtests verwenden, müssen Sie eingehende ICMP-Pakete zulassen. Führen Sie zum Erstellen einer Firewallregel für ICMP das folgende Cmdlet in einem PowerShell-Fenster mit erhöhten Rechten aus:

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-vm"></a>Pingen der Azure Stack-VM

1. Melden Sie sich beim Azure Stack-Benutzerportal an.

1. Suchen Sie den erstellten virtuellen Computer, und wählen Sie ihn aus.

1. Wählen Sie **Verbinden**aus.

1. Geben Sie an einer Windows- oder PowerShell-Eingabeaufforderung mit erhöhten Rechten **ipconfig /all** ein. Notieren Sie die IPv4-Adresse, die in der Ausgabe zurückgegeben wird.

1. Pingen Sie die IPv4-Adresse von der VM im Azure VNET aus.

   In der Beispielumgebung stammt die IPv4-Adresse aus dem Subnetz 10.1.1.x/24. In Ihrer Umgebung wird unter Umständen eine andere Adresse verwendet, sie sollte aber innerhalb des Subnetzes liegen, das Sie für das VNET-Subnetz des Mandanten erstellt haben.

### <a name="view-data-transfer-statistics"></a>Anzeigen der Datenübertragungsstatistik

Wenn Sie wissen möchten, wie viel Datenverkehr über Ihre Verbindung übertragen wird, finden Sie diese Informationen im Azure Stack-Benutzerportal. Die Anzeige von Datenübertragungsstatistiken ist auch eine gute Möglichkeit festzustellen, ob Ihre Pingtestdaten über die VPN- und ExpressRoute-Verbindungen gesendet wurden:

1. Melden Sie sich beim Azure Stack-Benutzerportal an, und wählen Sie **Alle Ressourcen** aus.
1. Navigieren Sie zur Ressourcengruppe für Ihr VPN Gateway, und wählen Sie den Objekttyp **Verbindung** aus.
1. Wählen Sie in der Liste die Verbindung **ConnectToAzure** aus.
1. Unter **Verbindungen** > **Übersicht** werden Statistiken für **Eingehende Daten** und **Ausgehende Daten** angezeigt. Dort sollten Werte angezeigt werden, die nicht Null sind.

   ![Eingehende Daten und ausgehende Daten](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Nächste Schritte

[Deploy apps to Azure and Azure Stack (Bereitstellen von Apps in Azure und Azure Stack)](../user/azure-stack-solution-pipeline.md)
