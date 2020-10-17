---
title: Übersicht über den BGP-Routenreflektor in Azure Stack HCI
description: In diesem Thema erfahren Sie mehr über den BGP-Routenreflektor für SDN (Software-Defined Networking) in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: ca130c60e8cf08484001f606c4d0f84d786ca16b
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858980"
---
# <a name="what-is-route-reflector"></a>Was ist ein Routenreflektor?

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Border Gateway Protocol (BGP)-Routenfeflektor ist in [RAS-Gateway (Remote Access Service, Remotezugriffsdienst)](gateway-overview.md) enthalten und bietet eine Alternative zur BGP-Full-Mesh-Topologie, die für die Routensynchronisierung zwischen Routern erforderlich ist. Ein Routenreflektor in einer Software-Defined Networking-Bereitstellung ist eine logische Entität, die sich auf der Steuerungsebene zwischen den RAS-Gateways und dem [Netzwerkcontroller](network-controller-overview.md) befindet. Er ist jedoch nicht am Routing auf Datenebene beteiligt.

## <a name="how-route-reflector-works"></a>Funktionsweise von Routenreflektoren

Bei der Full-Mesh-Synchronisierung müssen alle BGP-Router eine Verbindung mit allen anderen Routern in der Routingtopologie herstellen. Wenn Sie den Routenreflektor verwenden, ist der Routenreflektor jedoch der einzige Router, der mit allen anderen Routern (als BGP-Routenreflektorclients bezeichnet) eine Verbindung herstellt, wodurch die Routensynchronisierung vereinfacht und der Netzwerkdatenverkehr reduziert wird. Der Routenreflektor lernt alle Routen, berechnet optimale Routen und verteilt die optimalen Routen wieder an seine BGP-Clients.

Sie können die Remotezugriffstunnel eines einzelnen Mandanten so konfigurieren, dass sie auf mehr als einem virtuellen RAS-Gatewaycomputer (VM) enden. Dies bietet mehr Flexibilität für Clouddienstanbieter (Cloud Service Providers, CSPs) in Situationen, in denen ein virtueller RAS-Gatewaycomputer nicht alle Bandbreitenanforderungen der Mandantenverbindungen erfüllen kann.

Diese Funktion führt jedoch die zusätzliche Komplexität der Routenverwaltung und der effektiven Synchronisierung von Routen zwischen den Remotestandorten des Mandanten und deren zugehörigen virtuellen Ressourcen im Cloudrechenzentrum ein. Das Bereitstellen von Verbindungen mit mehreren RAS-Gateways macht außerdem die Konfiguration auf Unternehmensseite noch komplexer, wo dann jeder Mandantenstandort gesonderte Routingnachbarn haben wird.

Ein BGP-Routenreflektor auf Steuerungsebene löst diese Probleme und macht die interne Fabric-Bereitstellung des CSP für die Unternehmensmandanten transparent.

- Wenn Sie Ihrem Rechenzentrum einen neuen Mandanten hinzufügen, konfiguriert der Netzwerkcontroller automatisch das erste RAS-Gateway des Mandanten als Routenreflektor.

- Jeder Mandant besitzt einen entsprechenden Routenreflektor, der sich auf einem der virtuellen RAS-Gatewaycomputer befindet, die diesem Mandanten zugeordnet sind.

- Ein Mandant fungiert als Routenreflektor für alle virtuellen RAS-Gatewaycomputer, die diesem Mandanten zugeordnet sind. Alle anderen Mandantengateways, die nicht der RAS-Gateway-Routenreflektor sind, sind die Routenreflektorclients. Der Routenreflektor führt die Routensynchronisierung zwischen allen Routenreflektorclients aus, damit das tatsächliche Datenpfadrouting erfolgen kann.

- Ein Routenreflektor stellt keine Dienste für das RAS-Gateway bereit, auf dem er konfiguriert ist.

- Ein Routenreflektor aktualisiert den Netzwerkcontroller mit den Unternehmensrouten, die den Unternehmensstandorten des Mandanten entsprechen. Dadurch kann der Netzwerkcontroller die erforderlichen Hyper-V-Netzwerkvirtualisierungsrichtlinien im virtuellen Mandantennetzwerk für den End-to-End-Datenpfadzugriff konfigurieren.

- Wenn Ihre Unternehmenskunden das BGP-Routing im Kundenadressraum verwenden, ist der RAS-Gateway-Routenreflektor der einzige externe BGP-Nachbar (eBGP) für alle Standorte des entsprechenden Mandanten. Dies gilt unabhängig von den Tunnelabschlusspunkten des Unternehmens. Anders ausgedrückt: Unabhängig davon, auf welchem virtuellen RAS-Gatewaycomputer im CSP-Rechenzentrum der Site-to-Site-VPN-Tunnel für einen Mandantenstandort endet, ist der eBGP-Peer für alle Mandantenstandorte der Routenreflektor.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Übersicht über RAS-Gateways](gateway-overview.md)
- [RAS-Gateway-Bereitstellungsarchitektur](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Internet Engineering Task Force (IETF) RFC (Request for Comments) Thema RFC 4456 BGP-Routenreflektion: Eine Alternative zum Full Mesh Internal BGP](https://tools.ietf.org/html/rfc4456)