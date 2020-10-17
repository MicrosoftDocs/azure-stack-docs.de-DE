---
title: Übersicht über den Netzwerkcontroller in Azure Stack HCI
description: In diesem Thema erfahren Sie mehr über den Netzwerkcontroller für SDN (Software-Defined Networking) in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: c207b436571d85d0902cd69a2168144e176919b1
ms.sourcegitcommit: 6a0f7f452998c404a80ca9d788dbf3cdf4d78b38
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893899"
---
# <a name="what-is-network-controller"></a>Was ist ein Netzwerkcontroller?

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019

Der Netzwerkcontroller ist der Eckpfeiler der SDN-Verwaltung (Software-Defined Networking). Es handelt sich um eine hoch skalierbare Serverrolle, die einen zentralisierten, programmierbaren Automatisierungspunkt für die Verwaltung, Konfiguration, Überwachung und Problembehandlung der virtuellen Netzwerkinfrastruktur bietet.

Mit dem Netzwerkcontroller können Sie die Konfiguration und Verwaltung der Netzwerkinfrastruktur automatisieren, anstatt eine manuelle Konfiguration von Netzwerkgeräten und -diensten durchzuführen.

## <a name="how-network-controller-works"></a>Funktionsweise des Netzwerkcontrollers

Der Netzwerkcontroller stellt eine Anwendungsprogrammierschnittstelle (Application Programming Interface, API) bereit, die es Netzwerkcontrollern ermöglicht, mit Netzwerkgeräten, -diensten und -komponenten (Southbound-API) zu kommunizieren und diese zu verwalten, sowie eine zweite API, mit der Verwaltungsanwendungen dem Netzwerkcontroller mitteilen können, welche Netzwerkeinstellungen und -dienste sie benötigen (Northbound-API).

Mit der Southbound-API kann der Netzwerkcontroller Netzwerkgeräte und Netzwerkdienste verwalten und alle Informationen sammeln, die Sie über das Netzwerk benötigen. Der Netzwerkcontroller überwacht kontinuierlich den Zustand von Netzwerkgeräten und -diensten und stellt sicher, dass alle Konfigurationsabweichungen vom gewünschten Zustand wiederhergestellt werden.

Die Northbound-API von Netzwerkcontroller wird als REST-Schnittstelle implementiert. Sie bietet die Möglichkeit, Ihr Rechenzentrumsnetzwerk mittels Verwaltungsanwendungen zu verwalten. Für die Verwaltung können Benutzer die REST-API direkt verwenden oder die auf der REST-API aufbauende Windows PowerShell oder Verwaltungsanwendungen mit grafischer Benutzeroberfläche wie Windows Admin Center oder System Center Virtual Machine Manager.

## <a name="network-controller-features"></a>Funktionen des Netzwerkcontrollers

Der Netzwerkcontroller ermöglicht Ihnen die Verwaltung von SDN-Funktionen wie virtuelle Netzwerke, Firewalls, Software Load Balancer und RAS-Gateways. Nachfolgend finden Sie einige seiner zahlreichen Funktionen.

### <a name="virtual-network-management"></a>Verwalten des virtuellen Netzwerks

Mit dieser Netzwerkcontrollerfunktion können Sie die Hyper-V-Netzwerkvirtualisierung bereitstellen und konfigurieren, virtuelle Netzwerkadapter auf einzelnen virtuellen Computern konfigurieren und Richtlinien für virtuelle Netzwerke speichern und verteilen. Mit dieser Funktion können Sie virtuelle Netzwerke und Subnetze erstellen, virtuelle Computer (VMs) an diese Netzwerke anfügen und die Kommunikation zwischen virtuellen Computern im selben virtuellen Netzwerk aktivieren.

Der Netzwerkcontroller unterstützt VLAN-basierte (Virtual Local Area Network) Netzwerke, NVGRE (Network Virtualization Generic Routing Encapsulation) und VXLAN (Virtual Extensible Local Area Network).

## <a name="firewall-management"></a>Firewallverwaltung

Diese Netzwerkcontrollerfunktion ermöglicht Ihnen das Konfigurieren und Verwalten von zulassenden/verweigernden Firewall-Zugriffssteuerungsregeln für Ihre virtuellen Workloadcomputer für sowohl internen (Ost/West) als auch für den externen (Nord/Süd) Netzwerkdatenverkehr in Ihrem Rechenzentrum. Die Firewallregeln werden in den vSwitch-Port von virtuellen Workloadcomputern integriert und so auf Ihre Workloads im Rechenzentrum verteilt und zusammen mit Ihren Workloads verschoben.

Mithilfe der Northbound-API können Sie die Firewallregeln für eingehenden und ausgehenden Datenverkehr zu und von den virtuellen Workloadcomputern definieren. Außerdem können Sie jede Firewallregel so konfigurieren, dass der über die Regel zugelassene oder abgelehnte Datenverkehr protokolliert wird.

## <a name="software-load-balancer-management"></a>Verwalten des Software Load Balancers

Der [Software Load Balancer](software-load-balancer.md) ermöglicht es Ihnen, mehrere Server für das Hosting derselben Workload zu aktivieren, um so Hochverfügbarkeit und Skalierbarkeit zu gewährleisten. Mit dem Software Load Balancer können Sie den Lastenausgleich, die eingehende Netzwerkadressübersetzung (NAT) und den ausgehenden Zugriff auf das Internet für Workloads konfigurieren und verwalten, die mit herkömmlichen VLAN-Netzwerken und virtuellen Netzwerken verbunden sind.

## <a name="gateway-management"></a>Gatewayverwaltung

Das [RAS-Gateway (Remote Access Service, Remotezugriffsdienst)](gateway-overview.md) ermöglicht Ihnen die Bereitstellung, Konfiguration und Verwaltung von virtuellen Computern, die Mitglieder eines Gatewaypools sind, wodurch die Workloads Ihrer Kunden eine externe Netzwerkverbindung erhalten. Mit Gateways werden die folgenden Konnektivitätstypen zwischen Ihren virtuellen und Remotenetzwerken unterstützt:

- Site-to-Site-VPN-Gatewaykonnektivität mittels IPsec
- Site-to-Site-VPN-Gatewaykonnektivität mithilfe von GRE (Generic Routing Encapsulation)
- Layer 3-Weiterleitungsfunktion
 
Gatewayverbindungen unterstützen das Border Gateway Protocol (BGP) für die dynamische Routenverwaltung.

## <a name="virtual-appliance-chaining"></a>Verkettung virtueller Geräte

Diese Netzwerkcontrollerfunktion ermöglicht es Ihnen, virtuelle Netzwerkgeräte an Ihre virtuellen Netzwerke anzufügen. Diese Geräte können für erweiterte Firewallfunktionen, Lastenausgleich, Angriffserkennung und -schutz sowie für viele andere Netzwerkdienste verwendet werden. Sie können virtuelle Geräte hinzufügen, die benutzerdefinierte Routing- und Portspiegelungsfunktionen ausführen. Beim benutzerdefinierten Routing wird das virtuelle Gerät als Router zwischen den virtuellen Subnetzen im virtuellen Netzwerk verwendet. Bei der Portspiegelung wird der gesamte am überwachten Port ein- und ausgehende Netzwerkdatenverkehr dupliziert und zur Analyse an ein virtuelles Gerät gesendet.

Weitere Informationen zu benutzerdefinierten Routen finden Sie unter [Verwenden virtueller Netzwerkgeräte in einem virtuellen Netzwerk](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn).

## <a name="network-controller-deployment-considerations"></a>Überlegungen zur Bereitstellung von Netzwerkcontrollern

- Stellen Sie die Serverrolle „Netzwerkcontroller“ nicht auf physischen Hosts bereit. Der Netzwerkcontroller sollte auf eigenen dedizierten virtuellen Computern bereitgestellt werden.

- Sie können Netzwerkcontroller in Umgebungen mit und ohne Domänen bereitstellen. In Domänenumgebungen authentifiziert der Netzwerkcontroller Benutzer und Netzwerkgeräte mithilfe von Kerberos. In Umgebungen ohne Domänen müssen Sie Zertifikate für die Authentifizierung bereitstellen.

- Für die Bereitstellung von Netzwerkcontrollern ist es entscheidend, dass Sie Hochverfügbarkeit bereitstellen und in der Lage sind, entsprechend der Anforderungen Ihres Rechenzentrums problemlos zentral hoch- oder herunterzuskalieren. Verwenden Sie mindestens drei virtuelle Computer, um Hochverfügbarkeit für die Netzwerkcontrolleranwendung bereitzustellen.

- Zum Erzielen von Hochverfügbarkeit und Skalierbarkeit verwendet der Netzwerkcontroller Service Fabric. Service Fabric bietet eine Plattform für verteilte Systeme, um skalierbare, zuverlässige und einfach zu verwaltende Anwendungen zu erstellen. [Weitere Informationen zum Netzwerkcontroller als Service Fabric-Anwendung](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application).


## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Planen der Bereitstellung des Netzwerkcontrollers](network-controller.md)
- [Bereitstellen des Netzwerkcontrollers mithilfe von Windows PowerShell](../deploy/network-controller-powershell.md)
- [SDN in Azure Stack HCI](software-defined-networking.md)