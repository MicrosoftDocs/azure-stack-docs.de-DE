---
title: Erstellen eines VPN-Tunnels mithilfe von GRE in Azure Stack Hub
description: Erfahren Sie, wie Sie einen VPN-Tunnel mithilfe von GRE in Azure Stack Hub erstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 11/13/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: 95c894e218b2776bab83f2bdde8435eac2826c1d
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674529"
---
# <a name="how-to-create-a-vpn-tunnel-using-gre-in-azure-stack-hub"></a>Erstellen eines VPN-Tunnels mithilfe von GRE in Azure Stack Hub

Mithilfe der Azure Stack Hub-Resource Manager-Vorlage in dieser Lösung können Sie zwei Azure Stack Hub-VNETs in derselben Azure Stack Hub-Umgebung verbinden. Sie können Azure Stack Hub-VNETs [nicht](./azure-stack-network-differences.md) mithilfe des integrierten Gateways des virtuellen Netzwerks verbinden. Zum jetzigen Zeitpunkt müssen Sie virtuelle Netzwerkgeräte verwenden, um einen VPN-Tunnel zwischen zwei Azure Stack Hub-VNETs zu erstellen. Mit der Lösungsvorlage werden zwei virtuelle Windows Server 2016-Computer mit installiertem RRAS bereitgestellt. Mit der Lösung werden die beiden RRAS-Server für die Verwendung eines IKEv2-Tunnels für Site-to-Site-VPNs zwischen den beiden VNETs konfiguriert. Die entsprechenden NSG- und UDR-Regeln werden erstellt, um das Routing zwischen den Subnetzen in jedem VNET zuzulassen, das als **intern** festgelegt ist. 

Dieses Bereitstellungsmuster bildet die Grundlage, auf der Sie VPN-Tunnel nicht nur innerhalb der Azure Stack Hub-Instanz, sondern auch zwischen Azure Stack Hub-Instanzen und mit anderen Ressourcen erstellen können, z. B. lokale Netzwerke mit Verwendung von Windows-RRAS-Tunnel für Site-to-Site-VPNs.

Die Vorlagen finden Sie im GitHub-Repository [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Die Vorlage befindet sich im Ordner **rras-gre-vnet-vnet**. 

![Das Diagramm zeigt eine Implementierung, die einen VPN-Tunnel zwischen zwei virtuellen privaten Netzwerken (VNETs) bereitstellt. In jedem VNET gibt es einen RRAS-Server, sowie ein internes Subnetz und ein Tunnelsubnetz.](./media/azure-stack-network-howto-vpn-tunnel-gre/overview.png)

## <a name="requirements"></a>Requirements (Anforderungen)

- Ein System, das mit den neuesten Updates bereitgestellt wird. 
- Erforderliche Azure Stack Hub-Marketplace-Elemente:
    -  Windows Server 2016 Datacenter (neuester Build empfohlen)
    -  Benutzerdefinierte Skripterweiterung

## <a name="things-to-consider"></a>Zu beachtende Aspekte

- Eine Netzwerksicherheitsgruppe wird auf das Tunnelsubnetz der Vorlage angewandt. Schützen Sie das interne Subnetz in jedem VNET durch eine zusätzliche Netzwerksicherheitsgruppe.
- Eine Regel zum Ablehnen von RDP wird auf die Netzwerksicherheitsgruppe des Tunnels angewandt und muss auf „Zulassen“ festgelegt werden, wenn Sie über die öffentliche IP-Adresse auf die virtuellen Computer zugreifen möchten.
- Bei dieser Lösung wird die DNS-Auflösung nicht berücksichtigt.
- Die Zusammensetzung aus VNET-Name und vmName muss weniger als 15 Zeichen umfassen.
- Diese Vorlage ist darauf ausgelegt, dass die VNET-Namen für VNet1 und VNet2 angepasst werden.
- In dieser Vorlage werden BYOL-Fenster verwendet.
- Beim Löschen der Ressourcengruppe müssen Sie derzeit in der aktuellen Version (1907) die Netzwerksicherheitsgruppen manuell vom Tunnelsubnetz trennen, um sicherzustellen, dass der Löschvorgang der Ressourcengruppe durchgeführt wird.
- In dieser Vorlage wird ein virtueller DS3v2-Computer verwendet. Mit dem RRAS-Dienst wird eine Windows-interne SQL Server-Instanz installiert und ausgeführt. Das kann zu Speicherproblemen führen, wenn die VM-Größe zu klein ist. Überprüfen Sie die Leistung vor dem Verringern der VM-Größe.
- Dies ist keine Lösung mit Hochverfügbarkeit. Wenn Sie eine Lösung mit Hochverfügbarkeit benötigen, können Sie einen zweiten virtuellen Computer hinzufügen. Sie müssen dann die Route in der Routingtabelle manuell auf die interne IP-Adresse der zweiten Schnittstelle ändern. Außerdem müssen Sie die verschiedenen Tunnel für Querverbindungen konfigurieren.

## <a name="options"></a>Tastatur

- Mit den Parametern „_artifactsLocation“ und „_artifactsLocationSasToken“ können Sie Ihr eigenes Blob Storage-Konto und Ihr eigenes SAS-Token verwenden.
- Diese Vorlage enthält zwei Ausgaben: INTERNALSUBNETREFVNET1 und INTERNALSUBNETREFVNET2. Dies sind die Ressourcen-IDs für die internen Subnetze, wenn Sie dies in einem Bereitstellungsmuster im Pipelineformat verwenden möchten.

Diese Vorlage enthält Standardwerte für die VNET-Benennung und die IP-Adressierung. Hierfür ist ein Kennwort für den Administrator (rrasadmin) erforderlich. Außerdem haben Sie die Möglichkeit, Ihr eigenes Speicherblob mit SAS-Token zu verwenden. Achten Sie darauf, dass diese Werte innerhalb der zulässigen Bereiche liegen, da bei der Bereitstellung Fehler auftreten können. Das PowerShell-DSC-Paket wird auf jedem virtuellen RRAS-Computer ausgeführt und installiert das Routing und alle erforderlichen abhängigen Dienste und Funktionen. Dieses DSC-Paket kann bei Bedarf weiter angepasst werden. Die benutzerdefinierte Skripterweiterung führt das folgende Skript aus, und „Add-Site2SiteGRE.ps1“ konfiguriert den Tunnel für Site-to-Site-VPNs zwischen den beiden RRAS-Servern mit einem gemeinsam verwendeten Schlüssel. Sie können die ausführliche Ausgabe über die benutzerdefinierte Skripterweiterung anzeigen, um die Ergebnisse der VPN-Tunnelkonfiguration zu prüfen.

![Das Diagramm mit dem Titel „S2SVPNTunnel“ zeigt zwei VNETs, die durch einen Site-to-Site-VPN-Tunnel verbunden sind.](./media/azure-stack-network-howto-vpn-tunnel-gre/s2svpntunnel.svg)

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Einrichten eines VPN-Tunnels zwischen mehreren Standorten](network-howto-vpn-tunnel.md)
