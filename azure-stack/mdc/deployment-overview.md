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
ms.date: 10/20/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: aa5d67405ff471cecf147256d4b2109e94d993ef
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92898586"
---
# <a name="mdc-deployment-overview"></a>Übersicht über die MDC-Bereitstellung

In dieser Bereitstellungsanleitung werden die Schritte zum Installieren und Konfigurieren eines Modular Data Center (MDC) beschrieben. Außerdem wird der automatisierte Prozess zum Einrichten des HLH-Verwaltungsservers (Hardware Lifecycle Host) von Azure Stack Hub für die Bereitstellung beschrieben.

Dieses Handbuch hat die folgenden Ziele:

- Bereitstellen einer Prüfliste vor der Bereitstellung, um sicherzustellen, dass vor der Installation der Komponenten alle Voraussetzungen erfüllt sind
- Vorstellen der wichtigsten Komponenten eines MDC
- Beschreiben der Installation und Konfiguration der Schlüsselkomponenten
- Überprüfen der Kundenbereitstellung

Voraussetzungen, um den Inhalt dieser Anleitung vollständig verstehen zu können, sind technische Erfahrungen mit Virtualisierung, Servern, Betriebssystemen, Netzwerken und Speicherlösungen. Der Bereitstellungstechniker muss über Kenntnisse von Microsoft Windows Server 2019 mit Hyper-V, Azure Stack Hub, Azure und Microsoft PowerShell verfügen.

Der Schwerpunkt dieser Anleitung liegt auf der Bereitstellung der Kernkomponenten von Microsoft Azure Stack Hub sowie der Besonderheiten der MDC-Lösung. In der Anleitung werden keine Betriebsabläufe von Azure Stack Hub erläutert und auch nicht alle in Azure Stack Hub verfügbaren Features behandelt. Weitere Informationen finden Sie in der [Dokumentation für Azure Stack Hub-Operatoren](https://docs.microsoft.com/azure-stack/operator/).

## <a name="introduction"></a>Einführung

Das MDC ist ein integriertes Angebot für Azure Stack Hub in einem Standardcontainer mit einer Länge von 40 Fuß (12,19 m). Der Container enthält eine Klimasteuerungseinheit sowie ein Beleuchtungs- und ein Warnsystem. Die Kernkomponenten von Azure Stack Hub, z. B. Server und Switches, sind in sechs physische Racks montiert, die logisch in drei unabhängigen Pods gruppiert sind.

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

Der MDC-Bereitstellungsprozess umfasst im Allgemeinen die folgenden Schritte:

1. Planungsphase:
   1. Planen der Leistung des Rechenzentrums
   1. Planen der Konfiguration des logischen Netzwerks von Azure Stack Hub
   1. Planen der Netzwerkintegration des Rechenzentrums
   1. Planen der Integration von Identitäts- und Sicherheitsfunktionen
   1. Planen der PKI-Zertifikate
1. Vorbereitungsphase:
   1. Erfassen des Bestands
   1. Anschließen der Stromversorgung und Einschalten der Lösung
   1. Überprüfen der Integrität des HVAC-Systems
   1. Überprüfen der Integrität des Systems zur Überwachung und Warnung bei Feuer
   1. Überprüfen der Integrität der physischen Hardware
1. Ausführungsphase – einzeln für jeden der drei Pods:
   1. Konfigurieren des Hardwarelebenszyklus-Hosts
   1. Konfigurieren der Netzwerkswitches
   1. Integrieren in das Netzwerk des Rechenzentrums
   1. Konfigurieren der Einstellungen für die physische Hardware
   1. Konfigurieren des Isilon-Speichers
   1. Bereitstellen der Infrastruktur für das Azure Stack Hub-Fabric
   1. Integrieren der Rechenzentrumsidentität
   1. Installieren von Add-Ons für erweiterte Funktionen
1. Validierungsphase – einzeln für jeden der drei Pods:
   1. Überprüfen der Integrität nach der Bereitstellung
   1. Registrieren von Azure Stack Hub bei Microsoft
   1. Übergabe an den Azure Stack Hub-Operator
  
Jedes der obigen Themen wird in dieser Anleitung ausführlich erläutert.
