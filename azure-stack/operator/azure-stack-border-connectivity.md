---
title: Grenzkonnektivität und Netzwerkintegration für integrierte Azure Stack Hub-Systeme
description: Erfahren Sie, wie Sie die Netzwerkkonnektivität über Border-Geräte für Rechenzentren in integrierten Azure Stack Hub-Systemen planen.
author: PatAltimore
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 01/14/2021
ms.openlocfilehash: 091b6e5d1ac4c097c39e425cb6b15da4db96a7a1
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570649"
---
# <a name="border-connectivity"></a>Grenzkonnektivität 
Die Planung der Netzwerkintegration ist eine wichtige Voraussetzung, um erfolgreich integrierte Azure Stack Hub-Systeme bereitstellen, betreiben und verwalten zu können. Bei der Planung der Konnektivität über Border-Geräte entscheiden Sie zuerst, ob Sie dynamisches Routing mit Border Gateway Protocol (BGP) verwenden möchten. Hierfür muss eine autonome 16-Bit-Systemnummer (ASN) zugewiesen werden, die öffentlich oder privat ist oder statisches Routing verwendet.

> [!IMPORTANT]
> Für TOR-Switches (Top of Rack) sind Layer 3-Uplinks mit für physische Schnittstellen konfigurierte Point-to-Point-IP-Adressen (Netzwerke vom Typ „/30“) erforderlich. Layer 2-Uplinks mit TOR-Switches mit Unterstützung für Azure Stack Hub-Vorgänge werden nicht unterstützt. Das Border-Gerät kann die autonome 32-Bit-BGP-Systemnummer (ASN) unterstützen.

## <a name="bgp-routing"></a>BGP-Routing
Durch ein dynamisches Routingprotokoll wie BGP wird die Erkennung von Netzwerkänderungen durch Ihr System sichergestellt und die Verwaltung vereinfacht. Zur Verbesserung der Sicherheit kann für das BGP-Peering zwischen TOR und Grenze ein Kennwort festgelegt werden.

Wie im folgenden Diagramm dargestellt, wird die Ankündigung des privaten IP-Bereichs für den TOR-Switch durch eine Präfixliste blockiert. Die Präfixliste lehnt die Ankündigung des privaten Netzwerks ab und wird als Routenzuordnung für die Verbindung zwischen TOR und Grenze angewendet.

Der Software Load Balancer (SLB) in der Azure Stack Hub-Lösung stellt mittels Peering eine Verbindung mit TOR-Geräten her, damit die VIP-Adressen dynamisch angekündigt werden können.

Um sicherzustellen, dass Benutzerdatenverkehr nach einem Fehler umgehend und transparent wiederhergestellt wird, ermöglicht die zwischen den TOR-Geräten konfigurierte vPC- oder MLAG-Vernetzung die Verwendung von MLAG (Multi-Chassis Link Aggregation) für die Hosts sowie des HSRP- oder VRRP-Protokolls, das Redundanz für die IP-Netzwerke bietet.

![BGP-Routing](media/azure-stack-border-connectivity/bgp-routing.svg)

## <a name="static-routing"></a>Statisches Routing
Statisches Routing erfordert zusätzliche Konfiguration für die Border-Geräte. Vor jeder Änderung sind mehr manuelle Intervention und Verwaltung sowie eine gründliche Analyse erforderlich. Bei Problemen, die durch Konfigurationsfehler verursacht wurden, kann der Rollback je nach den vorgenommenen Änderungen relativ zeitintensiv sein. Diese Routingmethode wird nicht empfohlen, jedoch unterstützt.

Zum Integrieren von Azure Stack Hub in Ihre Netzwerkumgebung mithilfe von statischem Routing müssen alle vier physischen Verknüpfungen zwischen dem Border- und dem TOR-Gerät verbunden sein. Hohe Verfügbarkeit kann aufgrund der Funktionsweise von statischem Routing nicht garantiert werden.

Das Border-Gerät muss für Datenverkehr an Netzwerke innerhalb von Azure Stack Hub mit statischen Routen konfiguriert sein, die auf jede der vier festgelegten P2P-IP-Adressen zwischen dem TOR-Gerät und dem Border-Gerät verweisen, für den Betrieb ist jedoch nur das *externe* oder öffentliche VIP-Netzwerk erforderlich. Statische Routen zum *BMC*-Netzwerk und den *externen* Netzwerken sind für die erste Bereitstellung erforderlich. Betreiber können statische Routen auch in der Border-Vorrichtung beibehalten, um auf Verwaltungsressourcen im *BMC*-Netzwerk und *Infrastrukturnetzwerk* zuzugreifen. Das Hinzufügen von statischen Routen zu *Switchinfrastruktur*- und *Switchverwaltungsnetzwerken* ist optional.

Die Tor-Geräte sind mit einer statischen Standardroute vorkonfiguriert, die sämtlichen Datenverkehr an die Border-Geräte sendet. Die einzige Datenverkehrsausnahme von der Standardregel ist der private Bereich. Dieser wird mithilfe einer Zugriffssteuerungsliste für die Verbindung zwischen dem Tor- und Border-Gerät blockiert.

Statisches Routing wird nur auf die Uplinks zwischen den Tor- und Border-Switches angewendet. Im Rack kommt nach wie vor dynamisches BGP-Routing zum Einsatz, da es ein unverzichtbares Hilfsmittel für SLB und andere Komponenten darstellt. Es kann daher nicht deaktiviert oder entfernt werden.

![Statisches Routing](media/azure-stack-border-connectivity/static-routing.svg)

<sup>\*</sup> Das BMC-Netzwerk ist nach der Bereitstellung optional.

<sup>\*\*</sup> Das Switchinfrastrukturnetzwerk ist optional, da das gesamte Netzwerk in das Switchverwaltungsnetzwerk eingeschlossen werden kann.

<sup>\*\*\*</sup> Das Switchverwaltungsnetzwerk ist erforderlich und kann separat aus dem Switchinfrastrukturnetzwerk hinzugefügt werden.

## <a name="next-steps"></a>Nächste Schritte

- [DNS-Integration](azure-stack-integrate-dns.md)
- [Transparenter Proxy für Azure Stack Hub](azure-stack-transparent-proxy.md)
