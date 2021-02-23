---
title: Übersicht über die Bereitstellung von Modular Data Centers (MDC) und deren Einrichtung für den HLH-Verwaltungsserver (Hardware Lifecycle Host) von Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, was Sie bei einer erfolgreichen lokalen Bereitstellung eines Modular Data Center (MDC) erwarten können – von der Planung bis zu den Aktionen nach der Bereitstellung.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 02/17/2021
ms.openlocfilehash: ad0a80f28b26e3c7da71860670feefd7efc15a0f
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647792"
---
# <a name="mdc-requirements-overview"></a>Übersicht über die MDC-Anforderungen

In dieser Anleitung sind die Anforderungen beschrieben, die zum Installieren und Konfigurieren eines Modular Data Center (MDC) erfüllt sein müssen. 

Dieses Handbuch hat die folgenden Ziele:

- Bereitstellen einer Prüfliste vor der Bereitstellung, um sicherzustellen, dass vor der Installation der Komponenten alle Voraussetzungen erfüllt sind
- Vorstellen der wichtigsten Komponenten eines MDC
- Überprüfen der Kundenbereitstellung

Voraussetzungen, um den Inhalt dieser Anleitung vollständig verstehen zu können, sind technische Erfahrungen mit Virtualisierung, Servern, Betriebssystemen, Netzwerken und Speicherlösungen. 

Der Schwerpunkt dieser Anleitung liegt auf der Bereitstellung der Kernkomponenten von Microsoft Azure Stack Hub sowie der Besonderheiten der MDC-Lösung. In der Anleitung werden keine Betriebsabläufe von Azure Stack Hub erläutert und auch nicht alle in Azure Stack Hub verfügbaren Features behandelt. 

## <a name="introduction"></a>Einführung

Das MDC ist ein integriertes Angebot für Azure Stack Hub in einem Standardcontainer mit einer Länge von 40 Fuß (12,19 m). Der Container enthält eine Klimasteuerungseinheit sowie ein Beleuchtungs- und ein Warnsystem. Die Azure Stack Hub-Kernkomponenten werden als drei unabhängige Pods installiert: Pod 1, Rack 1 und Rack 2, Pod 2, Rack 1 und Rack 2 sowie Pod 3, Rack 1 und Rack 2.

Jeder Pod besteht aus zwei Racks mit je 42 HE. Ein Pod umfasst die ToR- (Top-of-Rack) und Edgeswitches sowie einen Switch für den Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC). Außerdem enthält jeder Pod einen Hardwarelebenszyklus-Host (Hardware Lifecycle Host, HLH) und einen Konzentrator für die seriellen Anschlüsse. Die Kernkapazität für Compute und Speicher wird über Azure Stack Hub-Skalierungseinheiten (Scaling Units, SUs) bereitgestellt, die aus acht Servern vom Typ Rugged Edge Appliance (REA) R840 bestehen. Zusätzliche Speicherkapazität wird durch 48 Isilon-Speicherknoten bereitgestellt. Die physische Konfiguration aller Pods ist identisch.

## <a name="terminology"></a>Begriff

In der folgenden Tabelle sind einige der in dieser Anleitung verwendeten Begriffe aufgeführt.

|Begriff    |Definition |
|-------|-----------|
|Hardwarelebenszyklus-Host (HLH)|    Der HLH (Hardware Lifecycle Host) ist der physische Server, der für den anfänglichen Bootstrap bei der Bereitstellung und die fortlaufende Hardwareverwaltung, -unterstützung und -sicherung in der Azure Stack Hub-Infrastruktur verwendet wird. Auf dem HLH wird Windows Server 2019 mit Desktopoberfläche und Hyper-V-Rolle ausgeführt. Der Server dient zum Hosten von Hardwareverwaltungstools, Switchverwaltungstools, dem Azure Stack Hub-Partnertoolkit und dem virtuellen Computer für die Bereitstellung. |
|Virtueller Computer für die Bereitstellung (DVM)|    Die DVM (Deployment Virtual Machine) ist ein virtueller Computer, der für die Dauer der Azure Stack Hub-Softwarebereitstellung auf dem HLH erstellt wird. Die DVM führt eine Azure Stack Hub-Engine für die Softwareorchestrierung aus, die als Enterprise Cloud Engine (ECE) bezeichnet wird. Damit wird die Infrastruktursoftware im Azure Stack Hub-Fabric auf allen Servern der Azure Stack Hub-Skalierungseinheiten über das Netzwerk installiert und konfiguriert.|
|Azure Stack Hub-Partnertoolkit|    Eine Sammlung von Softwaretools zum Erfassen kundenspezifischer Eingabeparameter und zum Initiieren der Installation und Konfiguration von Azure Stack Hub. Sie enthält das Bereitstellungsarbeitsblatt, ein Tool mit grafischer Benutzeroberfläche, das zum Erfassen und Speichern konfigurierbarer Parameter für die Azure Stack Hub-Installation verwendet wird. Außerdem enthält es das Network Configuration Generator-Tool, das mithilfe der Eingaben des Bereitstellungsarbeitsblatts Netzwerkkonfigurationsdateien für alle physischen Netzwerkgeräte in der Lösung generiert.|
|OEM-Erweiterungspaket    |Ein Paket mit Firmware, Gerätetreibern und Hardwareverwaltungstools in einem speziellen Format, das während der anfänglichen Bereitstellung und Aktualisierung von Azure Stack Hub verwendet wird.|
|Konzentrator für serielle Anschlüsse    |Jeder Pod umfasst ein physisches Gerät, das Netzwerkzugriff auf die seriellen Anschlüsse von Netzwerkswitches für Bereitstellungs- und Verwaltungszwecke ermöglicht.|
|Skalierungseinheit    |Eine Kernkomponente von Azure Stack Hub, die Compute- und Speicherressourcen für die Infrastruktur und Workloads im Azure Stack Hub-Fabric bereitstellt. Jeder Pod umfasst acht MDC-R840-Server, die auch als Knoten bezeichnet werden.|
|Isilon-Speicher |    Eine Azure Stack Hub-Komponente, die für die MDC-Lösung spezifisch ist. Isilon stellt zusätzlichen Blob- und Dateispeicher für Azure Stack Hub-Workloads bereit. Jeder Pod umfasst 48 Isilon-Speicherknoten.|
|Pod    |Im Zusammenhang mit MDCs ist ein Pod eine unabhängige logische Einheit, die aus zwei miteinander verbundenen physischen Racks besteht. Eine vollständige Lösung umfasst drei Pods, die in einem einzelnen Container montiert sind.|

## <a name="deployment-workflow"></a>Bereitstellungsworkflow

Der MDC-Bereitstellungsprozess umfasst die folgenden grundlegenden Phasen:

### <a name="planning-phase"></a>Planungsphase
1. Planen der Leistung des Rechenzentrums
1. Planen der Konfiguration des logischen Netzwerks von Azure Stack Hub
1. Planen der [Netzwerkintegration des Rechenzentrums](../operator/azure-stack-network.md)
1. Planen der [Identitätsintegration](../operator/azure-stack-identity-overview.md)
1. Planen der [Sicherheitsintegration](../operator/azure-stack-security-foundations.md)
1. Planen der [PKI-Zertifikate](../operator/azure-stack-pki-certs.md)

### <a name="preparation-phase"></a>Vorbereitungsphase
1. Erfassen des Bestands
1. Anschließen der Stromversorgung und Einschalten der Lösung
1. Überprüfen der Integrität des HVAC-Systems
1. Überprüfen der Integrität des Systems zur Überwachung und Warnung bei Feuer
1. Überprüfen der Integrität der physischen Hardware

### <a name="execution-phase--separately-for-each-of-the-three-pods"></a>Ausführungsphase – einzeln für jeden der drei Pods
1. Konfigurieren des Hardwarelebenszyklus-Hosts
1. Konfigurieren der Netzwerkswitches
1. Integrieren in das Netzwerk des Rechenzentrums
1. Konfigurieren der Einstellungen für die physische Hardware
1. Konfigurieren des Isilon-Speichers
1. Bereitstellen der Infrastruktur für das Azure Stack Hub-Fabric
1. Integrieren der Rechenzentrumsidentität
1. Installieren von Add-Ons für erweiterte Funktionen

### <a name="validation-phase--separately-for-each-of-the-three-pods"></a>Validierungsphase – einzeln für jeden der drei Pods
1. Überprüfen der Integrität nach der Bereitstellung
1. Registrieren von Azure Stack Hub bei Microsoft
1. Übergabe an den Azure Stack Hub-Kunden