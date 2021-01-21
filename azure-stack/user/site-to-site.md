---
title: Problembehandlung bei Site-to-Site-VPN-Verbindungen in Azure Stack Hub
description: Hier finden Sie Schritte zur Problembehandlung, die Sie nach dem Konfigurieren einer Site-to-Site-VPN-Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure Stack Hub-Netzwerk ausführen können.
author: sethmanheim
ms.author: sethm
ms.date: 11/22/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: a39cbd5a845e79114393710c8ec77d37f30a68a0
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243509"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>Problembehandlung bei Site-to-Site-VPN-Verbindungen

In diesem Artikel werden die Schritte zur Problembehandlung beschrieben, die Sie ausführen können, wenn Sie eine S2S-VPN-Verbindung (Site-to-Site-VPN) zwischen einem lokalen Netzwerk und einem virtuellen Azure Stack Hub-Netzwerk hergestellt haben und die Verbindung plötzlich getrennt wird und nicht mehr hergestellt werden kann.

Besuchen Sie das [Azure Stack Hub-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack), falls Sie Ihr Azure Stack Hub-Problem mit diesem Artikel nicht beheben konnten.

Sie können auch eine Azure-Supportanfrage senden. Weitere Informationen finden Sie unter [Supportquellen](../operator/azure-stack-manage-basics.md#where-to-get-support).

> [!NOTE]
> Zwischen zwei Azure Stack Hub-Bereitstellungen kann nur eine Site-to-Site-VPN-Verbindung hergestellt werden. Der Grund: Wegen einer Einschränkung ist für die Plattform nur eine einzelne VPN-Verbindung mit der gleichen IP-Adresse zulässig. Da Azure Stack Hub das mehrinstanzenfähige Gateway nutzt, das eine öffentliche IP-Adresse für alle VPN-Gateways im Azure Stack Hub-System verwendet, kann zwischen zwei Azure Stack Hub-Systemen nur eine VPN-Verbindung hergestellt werden. Diese Einschränkung gilt auch für das Herstellen mehrerer Site-to-Site-VPN-Verbindungen mit einem beliebigen VPN-Gateway, das eine einzige IP-Adresse nutzt. Azure Stack Hub lässt nicht zu, dass mehrere Ressourcen des lokalen Netzwerkgateways mit der gleichen IP-Adresse erstellt werden.

## <a name="initial-troubleshooting-steps"></a>Erste Schritte bei der Problembehandlung

Die Azure Stack Hub-Standardparameter für IPsec/IKEV2 haben sich [seit Build 1910](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters) geändert. Weitere Informationen zur Buildversion erhalten Sie von Ihrem Azure Stack Hub-Operator.

> [!IMPORTANT]
> Bei Verwendung eines S2S-Tunnels werden Pakete mit zusätzlichen Headern weiter gekapselt. Durch diese Kapselung vergrößert sich das Paket insgesamt. In diesen Szenarien müssen Sie TCP **MSS** mit **1350** verknüpfen. Wenn Ihre VPN-Geräte MSS-Clamping nicht unterstützen, können Sie stattdessen den MTU-Wert der Tunnelschnittstelle auf **1400** Bytes festlegen. Weitere Informationen finden Sie unter [Optimieren der TCP-/IP-Leistung von virtuellen Netzwerken](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

- Vergewissern Sie sich, dass die VPN-Konfiguration routenbasiert ist (IKEv2). Richtlinienbasierte Konfigurationen (IKEv1) werden von Azure Stack Hub nicht unterstützt.

- Überprüfen Sie, ob [das VPN-Gerät und die Version des Betriebssystems validiert wurden](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable). Wenn es sich bei dem Gerät nicht um ein überprüftes VPN-Gerät handelt, müssen Sie sich möglicherweise beim Gerätehersteller erkundigen, ob ein Kompatibilitätsproblem vorliegt.

- Stellen Sie sicher, dass die IP-Adressbereiche zwischen dem virtuellen Azure Stack Hub-Netzwerk und dem lokalen Netzwerk nicht überlappen. Andernfalls kann dies Verbindungsprobleme verursachen. 

- Überprüfen Sie die VPN-Peer-IP-Adressen:

  - Die IP-Definition im Objekt **Lokales Netzwerkgateway** in Azure Stack Hub muss mit der IP-Adresse des lokalen Geräts übereinstimmen.

  - Die Azure Stack Hub-Gateway-IP-Definition, die auf dem lokalen Gerät festgelegt ist, muss der Azure Stack Hub-Gateway-IP-Adresse entsprechen.

## <a name="status-not-connected---intermittent-disconnects"></a>Status „Nicht verbunden“ – zeitweilige Verbindungsunterbrechungen

### <a name="az-modules"></a>[Az-Module](#tab/az)

- Vergleichen Sie den gemeinsam verwendeten Schlüssel des lokalen VPN-Geräts und des AzSH-VPN des virtuellen Netzwerks, um sicherzustellen, dass die Schlüssel übereinstimmen. Verwenden Sie zum Anzeigen des gemeinsamen Schlüssels für die AzSH-VPN-Verbindung eine der folgenden Methoden:

  - **Azure Stack Hub-Mandantenportal:** Gehen Sie zu der von Ihnen erstellen VPN-Gateway-Site-to-Site-Verbindung. Wählen Sie im Abschnitt **Einstellungen** die Option **Gemeinsam verwendeter Schlüssel** aus.

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="VPN-Verbindung":::

  - **Azure PowerShell:** Verwenden Sie den folgenden PowerShell-Befehl:

```powershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

- Vergleichen Sie den gemeinsam verwendeten Schlüssel des lokalen VPN-Geräts und des AzSH-VPN des virtuellen Netzwerks, um sicherzustellen, dass die Schlüssel übereinstimmen. Verwenden Sie zum Anzeigen des gemeinsamen Schlüssels für die AzSH-VPN-Verbindung eine der folgenden Methoden:

  - **Azure Stack Hub-Mandantenportal:** Gehen Sie zu der von Ihnen erstellen VPN-Gateway-Site-to-Site-Verbindung. Wählen Sie im Abschnitt **Einstellungen** die Option **Gemeinsam verwendeter Schlüssel** aus.

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="VPN-Verbindung":::

  - **Azure PowerShell:** Verwenden Sie den folgenden PowerShell-Befehl:

```powershell
Get-AzurerRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
```

---

## <a name="status-connected---traffic-not-flowing"></a>Status „Verbunden“, aber kein Datenverkehrsfluss

- Suchen und entfernen Sie benutzerdefiniertes Routing (UDR) und Netzwerksicherheitsgruppen (NSG) im Gatewaysubnetz, und testen Sie dann das Ergebnis. Wenn das Problem behoben wurde, überprüfen Sie die vom UDR oder den NSG angewendeten Einstellungen.

   Eine benutzerdefinierte Route auf dem Gatewaysubnetz schränkt möglicherweise einen Teil des Datenverkehrs ein und lässt einen anderen zu. Deshalb scheint die VPN-Verbindung für einen Teil des Datenverkehrs unzuverlässig und für einen anderen Teil brauchbar zu sein.

- Überprüfen Sie die Adresse der externen Schnittstelle des lokalen VPN-Geräts. 

  - Wenn die IP-Adresse des VPN-Geräts mit Internetzugriff in der Definition **Lokales Netzwerk** in Azure Stack Hub enthalten ist, kann es vereinzelt zur Trennung der Verbindung kommen.

  - Die externe Schnittstelle des Geräts muss sich direkt im Internet befinden. Es darf sich keine Netzwerkadressübersetzung oder Firewall zwischen dem Internet und dem Gerät befinden.

  - Um Firewallclustering konfigurieren, um über eine virtuelle IP-Adresse zu verfügen, müssen Sie den Cluster unterbrechen und das VPN-Gerät direkt an eine öffentliche Schnittstelle anschließen, mit der das Gateway kompatibel ist.

- Überprüfen Sie, ob die Subnetze genau übereinstimmen.

  - Stellen Sie sicher, dass der Adressraum bzw. die Adressräume des virtuellen Netzwerks zwischen dem virtuellen Netzwerk in Azure Stack Hub und den lokalen Definitionen genau übereinstimmen.

  - Stellen Sie sicher, dass die Subnetze zwischen dem **lokalen Netzwerkgateway** und den lokalen Definitionen für das lokale Netzwerk genau übereinstimmen.

## <a name="create-a-support-ticket"></a>Erstellen ein Supporttickets

Wenn das Problem mit keinem der oben genannten Schritte behoben werden kann, erstellen Sie ein [Supportticket](../operator/azure-stack-manage-basics.md#where-to-get-support), und verwenden Sie das [Tool für die bedarfsgesteuerte Protokollsammlung](../operator/diagnostic-log-collection.md), um Protokolle bereitzustellen.