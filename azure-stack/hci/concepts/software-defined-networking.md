---
title: Software-Defined Networking (SDN) in Azure Stack HCI
description: Eine Übersicht über SDN-Themen im Zusammenhang mit Features in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/18/2020
ms.openlocfilehash: 5a2820de2c681ad5a49d0e4ee3e4973736e1169b
ms.sourcegitcommit: fb89c3d837e2673b28784fd997099c825deb32f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88591795"
---
# <a name="sdn-in-azure-stack-hci"></a>SDN in Azure Stack HCI

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019

Software-Defined Networking (SDN) ermöglicht die zentrale Konfiguration und Verwaltung von Netzwerken und Netzwerkdiensten wie Switching, Routing und Lastenausgleich in Ihrem Rechenzentrum. Mit SDN können Sie Ihr Netzwerk dynamisch erstellen, schützen und verbinden, um den Anforderungen Ihrer Apps gerecht zu werden. Der Betrieb globaler Rechenzentrumsnetzwerke für Dienste wie Microsoft Azure mit mehreren zehntausend Netzwerkänderungen pro Tag ist nur dank SDN möglich.

VNET-Elemente wie der [virtuelle Hyper-V-Switch](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch), [Hyper-V-Netzwerkvirtualisierung](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization), [Softwarelastenausgleich](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) und [RAS-Gateway](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn) wurden als integrale Elemente Ihrer SDN-Infrastruktur konzipiert. Sie können auch Ihre vorhandenen SDN-kompatiblen Geräte verwenden, um eine tiefere Integration zwischen Ihren Workloads zu erreichen, die in virtuellen Netzwerken und im physischen Netzwerk ausgeführt werden.

Azure Stack HCI bietet drei SDN-Hauptkomponenten, und Sie können wählen, welche Sie bereitstellen möchten: Netzwerkcontroller, Softwarelastenausgleich und Gateway.

## <a name="network-controller"></a>Netzwerkcontroller

Der [Netzwerkcontroller](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller) bietet einen zentralisierten, programmierbaren Automatisierungspunkt für die Verwaltung, Konfiguration, Überwachung und Problembehandlung im Zusammenhang mit der VNET-Infrastruktur in Ihrem Rechenzentrum. Dabei handelt es sich um eine hochgradig skalierbare Serverrolle, die Service Fabric nutzt, um Hochverfügbarkeit zu gewährleisten. Der Netzwerkcontroller muss auf eigenen dedizierten virtuellen Computern bereitgestellt werden.

Die Bereitstellung des Netzwerkcontrollers ermöglicht Folgendes:

- Erstellen und Verwalten virtueller Netzwerke und Subnetze Herstellen einer Verbindung zwischen virtuellen Computern (virtual machines, VMs) und virtuellen Subnetzen
- Konfigurieren und Verwalten der Mikrosegmentierung für virtuelle Computer, die mit virtuellen Netzwerken oder mit herkömmlichen VLAN-basierten Netzwerken verbunden sind
- Anfügen virtueller Geräte an Ihre virtuellen Netzwerke
- Konfigurieren von QoS-Richtlinien (Quality of Service) für virtuelle Computer, die an virtuelle Netzwerke oder herkömmliche VLAN-basierte Netzwerke angefügt wurden

Wir empfehlen die [Bereitstellung des Netzwerkcontrollers mithilfe von PowerShell](../deploy/network-controller-powershell.md), nachdem Sie einen Azure Stack HCI-Cluster erstellt haben.

## <a name="software-load-balancing"></a>Softwarelastenausgleich

[Softwarelastenausgleich](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) (Software Load Balancing, SLB) kann verwendet werden, um den Datenverkehr des Kundennetzwerks gleichmäßig auf mehrere virtuelle Computer zu verteilen. Dadurch können mehrere Server die gleiche Workload hosten, um Hochverfügbarkeit und Skalierbarkeit zu gewährleisten. Beim SLB wird das [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) verwendet, um dem physischen Netzwerk virtuelle IP-Adressen anzukündigen.

## <a name="gateway"></a>Gateway

Gateways werden verwendet, um Netzwerkdatenverkehr zwischen einem virtuellen Netzwerk und einem anderen Netzwerk (lokal oder remote) weiterzuleiten. Gateways eignen sich für Folgendes:

- Erstellen sicherer Site-to-Site-IPsec-Verbindungen zwischen virtuellen SDN-Netzwerken und externen Kundennetzwerken über das Internet
- Erstellen von GRE-Verbindungen (Generic Routing Encapsulation) zwischen virtuellen SDN-Netzwerken und externen Netzwerken. Der Unterschied zwischen Site-to-Site-Verbindungen und GRE-Verbindungen besteht darin, dass es sich bei einer GRE-Verbindung nicht um eine verschlüsselte Verbindung handelt. Weitere Informationen zu GRE-Konnektivitätsszenarien finden Sie unter [GRE-Tunneling in Windows Server 2016](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server).
- Erstellen von Layer 3-Verbindungen zwischen virtuellen SDN-Netzwerken und externen Netzwerken. In diesem Fall fungiert das SDN-Gateway einfach als Router zwischen Ihrem virtuellen Netzwerk und dem externen Netzwerk.

Von Gateways wird das [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) verwendet, um GRE-Endpunkte anzukündigen und Point-to-Point-Verbindungen herzustellen. Bei der SDN-Bereitstellung wird ein Standardgatewaypool erstellt, der alle Verbindungstypen unterstützt. Innerhalb dieses Pools können Sie angeben, wie viele Gateways im Standby für den Fall vorgehalten werden sollen, dass ein aktives Gateway ausfällt.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [SDN in Windows Server: Übersicht](/windows-server/networking/sdn/software-defined-networking)
- [Bereitstellen einer Software-Defined Networking-Infrastruktur mithilfe von Skripts](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
