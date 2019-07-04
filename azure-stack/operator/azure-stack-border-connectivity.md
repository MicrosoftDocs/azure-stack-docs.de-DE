---
title: Überlegungen zur Integration der Netzwerkkonnektivität über Border-Geräte für integrierte Azure Stack-Systeme | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Netzwerkkonnektivität über Border-Geräte für Rechenzentren mit Azure Stack-Systemen mit mehreren Knoten planen können.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 85da256828d69db600bd8e5847a110ee3519568b
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131405"
---
# <a name="border-connectivity"></a>Grenzkonnektivität 
Die Planung der Netzwerkintegration ist eine wichtige Voraussetzung, um erfolgreich integrierte Azure Stack-Systeme bereitstellen, betreiben und verwalten zu können. Bei der Planung der Konnektivität über Border-Geräte wird zunächst festgelegt, ob dynamisches Routing mit Border Gateway Protocol (BGP) verwendet werden soll. Hierfür muss eine autonome 16-Bit-BGP-Systemnummer (öffentlich oder privat) zugewiesen oder statisches Routing verwendet werden, wenn Border-Geräten eine statische Standardroute zugewiesen wird.

> [!IMPORTANT]
> Für TOR-Switches (Top of Rack) sind Layer 3-Uplinks mit für physische Schnittstellen konfigurierte Point-to-Point-IP-Adressen (Netzwerke vom Typ „/30“) erforderlich. Layer 2-Uplinks mit TOR-Switches mit Unterstützung für Azure Stack-Vorgänge werden nicht unterstützt. 

## <a name="bgp-routing"></a>BGP-Routing
Durch ein dynamisches Routingprotokoll wie BGP wird die Erkennung von Netzwerkänderungen durch Ihr System sichergestellt und die Verwaltung vereinfacht. Zur Verbesserung der Sicherheit kann für das BGP-Peering zwischen TOR und Grenze ein Kennwort festgelegt werden. 

Wie im folgenden Diagramm dargestellt, wird die Ankündigung des privaten IP-Bereichs für den TOR-Switch durch eine Präfixliste blockiert. Die Präfixliste lehnt die Ankündigung des privaten Netzwerks ab und wird als Routenzuordnung für die Verbindung zwischen TOR und Grenze angewendet.

Software Load Balancer (SLB) in der Azure Stack-Lösung stellt mittels Peering eine Verbindung mit TOR-Geräten her, damit die VIP-Adressen dynamisch angekündigt werden können.

Um sicherzustellen, dass Benutzerdatenverkehr nach einem Fehler umgehend und transparent wiederhergestellt wird, ermöglicht die zwischen den TOR-Geräten konfigurierte vPC- oder MLAG-Vernetzung die Verwendung von MLAG (Multi-Chassis Link Aggregation) für die Hosts sowie des HSRP- oder VRRP-Protokolls, das Redundanz für die IP-Netzwerke bietet.

![BGP-Routing](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statisches Routing
Statisches Routing erfordert zusätzliche Konfiguration für die Border-Geräte. Es erfordert weitere manuelle Eingriffe und Verwaltung sowie eine gründliche Analyse vor jeder Änderung, und ein Rollback für durch einen Konfigurationsfehler verursachte Probleme dauert je nach den vorgenommenen Änderungen möglicherweise länger. Dies ist zwar nicht die empfohlene Routingmethode, es besteht jedoch Unterstützung für diese.

Zum Integrieren von Azure Stack in Ihre Netzwerkumgebung mithilfe von statischem Routing müssen alle vier physischen Verknüpfungen zwischen dem Border- und dem Tor-Gerät verbunden sein, und hohe Verfügbarkeit kann aufgrund der Funktionsweise von statischem Routing nicht garantiert werden.

Das Border-Gerät muss für Datenverkehr an die *externen* Netzwerke oder öffentlichen VIP-Adressen und das *Infrastrukturnetzwerk* mit statischen Routen konfiguriert sein, die auf die P2P-Verbindungen der Tor-Geräte verweisen. Es benötigt statische Routen zum *BMC*-Netzwerk und *externen* Netzwerken für die Bereitstellung. Betreiber können statische Routen auch in der Border-Vorrichtung beibehalten, um auf Verwaltungsressourcen im *BMC*-Netzwerk zuzugreifen. Das Hinzufügen von statischen Routen zu *Switchinfrastruktur*- und *Switchverwaltungsnetzwerken* ist optional.

Die Tor-Geräte sind mit einer statischen Standardroute vorkonfiguriert, die sämtlichen Datenverkehr an die Border-Geräte sendet. Die einzige Datenverkehrsausnahme von der Standardregel ist der private Bereich. Dieser wird mithilfe einer Zugriffssteuerungsliste für die Verbindung zwischen dem Tor- und Border-Gerät blockiert.

Statisches Routing wird nur auf die Uplinks zwischen den Tor- und Border-Switches angewendet. Im Rack kommt nach wie vor dynamisches BGP-Routing zum Einsatz, da es ein unverzichtbares Hilfsmittel für SLB und andere Komponenten darstellt. Es kann daher nicht deaktiviert oder entfernt werden.

![Statisches Routing](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> Das BMC-Netzwerk ist nach der Bereitstellung optional.

<sup>\*\*</sup> Das Switchinfrastrukturnetzwerk ist optional, da das gesamte Netzwerk in das Switchverwaltungsnetzwerk eingeschlossen werden kann.

<sup>\*\*\*</sup> Das Switchverwaltungsnetzwerk ist erforderlich und kann separat aus dem Switchinfrastrukturnetzwerk hinzugefügt werden.

## <a name="transparent-proxy"></a>Transparenter Proxy
Wenn Ihr Rechenzentrum verlangt, dass für den gesamten Datenverkehr ein Proxy verwendet wird, müssen Sie einen *transparenten Proxy* konfigurieren. Dieser muss den gesamten Datenverkehr gemäß den Richtlinien verarbeiten und den Datenverkehr zwischen den Zonen in Ihrem Netzwerk trennen.

> [!IMPORTANT]
> Die Azure Stack-Lösung unterstützt keine normalen Webproxys.  

Ein transparenter Proxy (auch abfangender, inline oder erzwungener Proxy genannt) fängt die normale Kommunikation auf der Netzwerkschicht ab, ohne dass eine spezielle Clientkonfiguration erforderlich ist. Clients müssen nicht wissen, dass der Proxy vorhanden ist.

![Transparenter Proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Nächste Schritte
[DNS-Integration](azure-stack-integrate-dns.md)
