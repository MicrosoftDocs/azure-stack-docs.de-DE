---
title: Verbinden von zwei Azure Stack Hub-VNETs
description: Erfahren Sie, wie Sie zwei Azure Stack Hub-Systeme miteinander verbinden.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: b6488b98317decf6b592339bfa66d15834f1f55d
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543390"
---
# <a name="vnet-to-vnet-connectivity-with-rras"></a>VNET-zu-VNET-Konnektivität mit RRAS

Sie können zwei Azure Stack Hub-VNETs innerhalb derselben Azure Stack Hub-Umgebung miteinander verbinden. Es ist derzeit nicht möglich, Azure Stack Hub-VNETs mithilfe des integrierten [Gateways des virtuellen Netzwerks](./azure-stack-network-differences.md) zu verbinden. Sie müssen virtuelle Netzwerkgeräte (NVAs) verwenden, um einen VPN-Tunnel zwischen zwei Azure Stack Hub-VNETs zu erstellen. In der Vorlage, auf die in diesem Artikel verwiesen wird, werden zwei Windows Server 2016-VMs mit installiertem RRAS bereitgestellt. Die beiden RRAS-Server sind so konfiguriert, dass ein S2SVPN IKEv2-Tunnel zwischen zwei VNETs implementiert wird. Die entsprechenden NSG- und UDR-Regeln werden erstellt, um das Routing zwischen den Subnetzen in jedem VNET zuzulassen, das als **intern** festgelegt ist. 

Dieses Bereitstellungsmuster bildet die Grundlage, auf der Sie VPN-Tunnel nicht nur innerhalb der Azure Stack Hub-Instanz, sondern auch zwischen Azure Stack Hub-Instanzen und mit anderen Ressourcen erstellen können, z. B. lokale Netzwerke mit Verwendung von Windows-RRAS-Tunnel für Site-to-Site-VPNs. 

Die Vorlagen finden Sie im GitHub-Repository [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
). Die Vorlage befindet sich im Ordner **S2SVPNTunnel**.

![Das Diagramm zeigt eine Implementierung, die einen VPN-Tunnel zwischen zwei virtuellen privaten Netzwerken (VNETs) bereitstellt. In jedem VNET gibt es einen RRAS-Server, sowie ein internes Subnetz und ein Tunnelsubnetz.](./media/azure-stack-network-howto-vnet-peering/overview.svg)

## <a name="requirements"></a>Requirements (Anforderungen)

- Eine Bereitstellung mit den neuesten Updates. 
- Erforderliche Azure Stack Hub-Marketplace-Elemente:
    -  Windows Server 2016 Datacenter (neuester Build empfohlen)
    -  Benutzerdefinierte Skripterweiterung

## <a name="things-to-consider"></a>Zu beachtende Aspekte

- Eine Netzwerksicherheitsgruppe wird auf das Tunnelsubnetz der Vorlage angewandt. Es wird empfohlen, das interne Subnetz in jedem VNET durch eine zusätzliche Netzwerksicherheitsgruppe zu schützen.
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

Die Vorlage enthält Standardwerte für die VNET-Benennung und die IP-Adressierung. Hierfür ist ein Kennwort für den Administrator (rrasadmin) erforderlich. Außerdem haben Sie die Möglichkeit, Ihr eigenes Speicherblob mit SAS-Token zu verwenden. Achten Sie darauf, dass diese Werte innerhalb der zulässigen Bereiche liegen, da bei der Bereitstellung Fehler auftreten können. Das PowerShell-DSC-Paket wird auf jedem virtuellen RRAS-Computer ausgeführt und installiert das Routing und alle erforderlichen abhängigen Dienste und Funktionen. Dieses DSC-Paket kann bei Bedarf weiter angepasst werden. Die benutzerdefinierte Skripterweiterung führt das folgende Skript aus, und `Add-Site2Site.ps1` konfiguriert den Tunnel für Site-to-Site-VPNs zwischen den beiden RRAS-Servern mit einem gemeinsam verwendeten Schlüssel. Sie können die ausführliche Ausgabe über die benutzerdefinierte Skripterweiterung anzeigen, um die Ergebnisse der VPN-Tunnelkonfiguration zu prüfen.

![Das Diagramm mit dem Titel „S2SVPNTunnel“ zeigt zwei VNETs, die durch einen Site-to-Site-VPN-Tunnel verbunden sind.](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.svg)

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
