---
title: Übersicht zum RAS-Gateway in Azure Stack HCI
description: In diesem Thema erfahren Sie mehr über das RAS-Gateway für SDN (Software-Defined Networking) in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: a7f05f7e07960e83681a13c92f4653b0993668de
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858982"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>Was ist das RAS-Gateway für SDN (Software-Defined Networking)?

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019

RAS-Gateway (Remote Access Service, Remotezugriffsdienst) ist ein softwarebasierter, BGP-fähiger (Border Gateway Protocol) Router, der auf Clouddienstanbieter (Cloud Service Providers, CSPs) und Unternehmen ausgelegt ist, die virtuelle Netzwerke mit mehreren Mandanten mithilfe von Hyper-V-Netzwerkvirtualisierung (HNV) hosten. Sie können RAS-Gateways verwenden, um Netzwerkdatenverkehr zwischen einem virtuellen Netzwerk und einem anderen Netzwerk (lokal oder remote) weiterzuleiten.

Das RAS-Gateway erfordert einen [Netzwerkcontroller](network-controller-overview.md), der die Bereitstellung von Gatewaypools ausführt, Mandantenverbindungen auf jedem Gateway konfiguriert und im Falle eines Gatewayausfalls die Netzwerkdatenverkehrs-Flüsse auf ein Standbygateway umleitet.

  > [!NOTE]
  > Mehrinstanzenfähigkeit bezeichnet die Fähigkeit einer Cloudinfrastruktur, die Arbeitsauslastungen virtueller Computer mehrerer Mandanten zu unterstützen, diese dabei jedoch voneinander zu trennen, während alle Arbeitsauslastungen aber weiterhin in derselben Infrastruktur ausgeführt werden. Mehrere Arbeitsauslastungen eines einzelnen Mandanten können miteinander verbunden und remote verwaltet werden. Es gibt jedoch keine Verbindung zwischen diesen Systemen und den Arbeitsauslastungen anderer Mandanten, und auch die Remoteverwaltung durch andere Mandanten ist nicht möglich.

## <a name="ras-gateway-features"></a>RAS-Gatewayfunktionen

Das RAS-Gateway bietet eine Reihe von Funktionen für virtuelle private Netzwerke (VPN), Tunneln, Weiterleitung und dynamisches Routing.

### <a name="site-to-site-ipsec-vpn"></a>Site-to-Site-IPsec-VPN

Mithilfe dieser RAS-Gatewayfunktion können Sie zwei Netzwerke an verschiedenen physischen Standorten über das Internet mithilfe einer Site-to-Site-VPN-Verbindung (S2S; virtuelles privates Netzwerk) miteinander verbinden. Hierbei handelt es sich um eine verschlüsselte Verbindung, die das IKEv2-VPN-Protokoll verwendet.

Für CSPs, die viele Mandanten in ihren Rechenzentren hosten, kann das RAS-Gateway als mehrinstanzenfähige Gatewaylösung verwendet werden. Mandanten können auf ihre Ressourcen über Standard-zu-Standort-VPN-Verbindungen von Remotestandorten aus zugreifen. Das RAS-Gateway erlaubt den Fluss von Netzwerkdatenverkehr zwischen virtuellen Ressourcen in Ihrem Rechenzentrum und dem jeweiligen physischen Netzwerk.

### <a name="site-to-site-gre-tunnels"></a>Site-to-Site-GRE-Tunnel

Tunnel, die auf GRE (Generic Routing Encapsulation) basieren, ermöglichen Konnektivität zwischen virtuellen Mandantennetzwerken und externen Netzwerken. Da das GRE-Protokoll schlank ist und die Unterstützung für GRE auf den meisten Netzwerkgeräten verfügbar ist, ist es eine ideale Wahl für das Tunnelverfahren, bei dem keine Datenverschlüsselung erforderlich ist.

Die GRE-Unterstützung in S2S-Tunneln löst das Problem der Weiterleitung zwischen virtuellen Mandantennetzwerken und externen Mandantennetzwerken mithilfe eines mehrinstanzenfähigen Gateways.

### <a name="layer-3-forwarding"></a>Ebene-3-Weiterleitung

Eine L3-Weiterleitung (Layer 3) ermöglicht eine Verbindung zwischen der physischen Infrastruktur im Rechenzentrum und der virtualisierten Infrastruktur in der Hyper-V-Netzwerkvirtualisierungscloud. Mithilfe der L3-Weiterleitungsverbindung können virtuelle Computer des Mandantennetzwerks eine Verbindung über ein physisches Netzwerk durch das SDN-Gateway herstellen, das in einer SDN-Umgebung (Software-Defined Networking) bereits konfiguriert ist. In diesem Fall fungiert das Hostnamensgateway als Router zwischen dem virtuellen und dem physischen Netzwerk.

### <a name="dynamic-routing-with-bgp"></a>Dynamisches Routing mit BGP

BGP verringert den Bedarf an manueller Routingkonfiguration auf Routern, da es ein dynamisches Routingprotokoll ist, das automatisch Routen zwischen Standorten lernt, die über die Standort-zu-Standort-VPN-Verbindungen verbunden sind. Wenn Ihre Organisation über mehrere Standorte verfügt, die mithilfe von BGP-fähigen Routern wie RAS-Gateway verbunden sind, ermöglicht BGP den Routern die automatische Berechnung und Verwendung gültiger Routen untereinander, wenn es zu Netzwerkunterbrechungen oder Ausfällen kommt.

Der im RAS-Gateway enthaltene BGP-Routenfeflektor bietet eine Alternative zur BGP-Full-Mesh-Topologie, die für die Routensynchronisierung zwischen Routern erforderlich ist. Weitere Informationen finden Sie unter [Was ist ein Routenreflektor?](route-reflector-overview.md)

## <a name="how-ras-gateway-works"></a>Funktionsweise des RAS-Gateways

Das RAS-Gateway leitet Netzwerkdatenverkehr zwischen dem physischen Netzwerk und VM-Netzwerkressourcen weiter, unabhängig vom Standort. Sie können den Netzwerkdatenverkehr am selben physischen Standort oder an vielen verschiedenen Standorten weiterleiten.

Sie können das RAS-Gateway in Hochverfügbarkeitspools bereitstellen, die mehrere Funktionen gleichzeitig verwenden. Gatewaypools enthalten mehrere Instanzen des RAS-Gateways für Hochverfügbarkeit und Failover.

Sie können einen Gatewaypool problemlos zentral hoch- oder herunterskalieren, indem Sie virtuelle Gatewaycomputer im Pool hinzufügen oder entfernen. Das Entfernen oder Hinzufügen von Gateways beeinträchtigt nicht die Dienste, die von einem Pool bereitgestellt werden. Sie können auch ganze Pools von Gateways hinzufügen oder entfernen. Weitere Informationen finden Sie unter [RAS-Gatewayhochverfügbarkeit](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability).

Jeder Gatewaypool bietet „M+N“-Redundanz. Dies bedeutet, dass „M“ aktive Gateway-VMs von „N“ virtuellen Standbygatewaycomputern gesichert werden. „M+N“-Redundanz bietet Ihnen mehr Flexibilität bei der Ermittlung des Zuverlässigkeitsniveaus, das Sie bei der Bereitstellung des RAS-Gateways benötigen.

Sie können allen Pools oder einer Teilmenge von Pools eine einzelne öffentliche IP-Adresse zuweisen. Dadurch wird die Anzahl der öffentlichen IP-Adressen, die Sie verwenden müssen, erheblich reduziert, da alle Mandanten über eine einzige IP-Adresse eine Verbindung mit der Cloud herstellen können.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [RAS-Gateway-Bereitstellungsarchitektur](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Netzwerkcontroller – Übersicht](network-controller-overview.md)
- [Planen der Bereitstellung des Netzwerkcontrollers](network-controller.md)
- [SDN in Azure Stack HCI](software-defined-networking.md)