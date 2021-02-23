---
title: Bereitstellung und Einrichtung von Azure Stack Hub Ruggedized für den HLH-Verwaltungsserver (Hardware Lifecycle Host) von Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, was Sie bei einer erfolgreichen lokalen Bereitstellung von Azure Stack Hub Ruggedized erwarten können – von der Planung bis zu den Aktionen nach der Bereitstellung.
services: azure-stack
documentationcenter: ''
author: ashika789
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
ms.openlocfilehash: 4d61fc75cd7db67f113369981fe0fa4742178dd5
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100648064"
---
# <a name="azure-stack-hub-ruggedized-deployment-overview"></a>Übersicht über die Bereitstellung von Azure Stack Hub Ruggedized

In dieser Bereitstellungsanleitung werden die Schritte zum Installieren und Konfigurieren von Azure Stack Hub Ruggedized beschrieben. 

Dieses Handbuch hat die folgenden Ziele:

- Bereitstellen einer Prüfliste vor der Bereitstellung, um sicherzustellen, dass vor der Installation der Komponenten alle Voraussetzungen erfüllt sind
- Vorstellung der Hauptkomponenten von Azure Stack Hub Ruggedized
- Beschreiben der Installation und Konfiguration der Schlüsselkomponenten
- Überprüfen der Kundenbereitstellung

Diese Bereitstellungsanleitung richtet sich an das im Feld eingesetzte Team von Microsoft-Experten, das Azure Stack Hub Ruggedized an einem Kundenstandort bereitstellt.

Voraussetzungen, um den Inhalt dieser Anleitung vollständig verstehen zu können, sind technische Erfahrungen mit Virtualisierung, Servern, Betriebssystemen, Netzwerken und Speicherlösungen. Der Bereitstellungstechniker muss über Kenntnisse von Microsoft Windows Server 2019 mit Hyper-V, Azure Stack Hub, Azure und Microsoft PowerShell verfügen.

Der Schwerpunkt dieser Anleitung liegt auf der Bereitstellung der Kernkomponenten von Microsoft Azure Stack Hub sowie den Besonderheiten von Azure Stack Hub Ruggedized. In der Anleitung werden keine Betriebsabläufe von Azure Stack Hub erläutert und auch nicht alle in Azure Stack Hub verfügbaren Features behandelt. Weitere Informationen finden Sie in der [Dokumentation für Azure Stack Hub-Operatoren](../operator/index.yml).

## <a name="introduction"></a>Einführung

Azure Stack Hub Ruggedized ist eine robuste und im Feld bereitstellbare Lösung für Microsoft Azure Stack Hub. Die Kernkomponenten, z. B. Server und Switches, befinden sich in Transportkisten, die als Pods bezeichnet werden.

Ein Pod ist ein 4 Höheneinheiten (HE) umfassendes Rack-Gehäuse, das kleiner als ein reguläres 4-HE-Rack ist. Es sind ein Verwaltungspod und zwei SU-Pods (Skalierungseinheit, SU) vorhanden. Der Verwaltungspod umfasst den Hardwarelebenszyklushost (HLH), zwei ToR-Switches (Top-of-Rack) mit jeweils 25 GbE und einen Switch für den Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC).

Jeder SU-Pod enthält zwei Azure Stack Hub Ruggedized-R640-SU-Server. Ein R640-Server belegt 2 HE im Rack. Die Server in den SU-Pods sind während der Bereitstellung mit den BMC- und ToR-Switches im Verwaltungspod verbunden.

## <a name="terminology"></a>Begriff

In der folgenden Tabelle sind einige der in dieser Anleitung verwendeten Begriffe aufgeführt.

|Begriff   | Definition |
|-------|------------|
|Hardwarelebenszyklus-Host (HLH)| Der HLH (Hardware Lifecycle Host) ist der physische Server, der für den anfänglichen Bootstrap bei der Bereitstellung und die fortlaufende Hardwareverwaltung, -unterstützung und -sicherung in der Azure Stack Hub-Infrastruktur verwendet wird. Auf dem HLH wird Windows Server 2019 mit Desktopoberfläche und Hyper-V-Rolle ausgeführt. Der Server dient zum Hosten von Hardwareverwaltungstools, Switchverwaltungstools, dem Azure Stack Hub-Partnertoolkit und dem virtuellen Computer für die Bereitstellung. |
|Virtueller Computer für die Bereitstellung (DVM)|  Die DVM (Deployment Virtual Machine) ist ein virtueller Computer, der für die Dauer der Azure Stack Hub-Softwarebereitstellung auf dem HLH erstellt wird. Die DVM führt eine Azure Stack Hub-Engine für die Softwareorchestrierung aus, die als Enterprise Cloud Engine (ECE) bezeichnet wird. Damit wird die Infrastruktursoftware im Azure Stack Hub-Fabric auf allen Servern der Azure Stack Hub-Skalierungseinheiten über das Netzwerk installiert und konfiguriert.|
|Azure Stack Hub-Partnertoolkit|   Eine Sammlung von Softwaretools zum Erfassen kundenspezifischer Eingabeparameter und zum Initiieren der Installation und Konfiguration von Azure Stack Hub. Sie enthält das Bereitstellungsarbeitsblatt, ein Tool mit grafischer Benutzeroberfläche, das zum Erfassen und Speichern konfigurierbarer Parameter für die Azure Stack Hub-Installation verwendet wird. Außerdem enthält es das Network Configuration Generator-Tool, das mithilfe der Eingaben des Bereitstellungsarbeitsblatts Netzwerkkonfigurationsdateien für alle physischen Netzwerkgeräte in der Lösung generiert.|
|OEM-Erweiterungspaket  |Ein Paket mit Firmware, Gerätetreibern und Hardwareverwaltungstools in einem speziellen Format, das während der anfänglichen Bereitstellung und Aktualisierung von Azure Stack Hub verwendet wird.|
|Integrated Dell Remote Access Controller (iDRAC)|  Ein iDRAC mit Lifecycle Controller ist ein Baseboard-Verwaltungscontroller, der in jeden Azure Stack Hub Ruggedized-R640-Server eingebettet ist. iDRAC bietet Out-of-Band-Verwaltungsfunktionen zum Bereitstellen, Aktualisieren, Überwachen und Warten von Azure Stack Hub-Servern.|
|Skalierungseinheit |Eine Kernkomponente von Azure Stack Hub, die Compute- und Speicherressourcen für die Infrastruktur und Workloads im Azure Stack Hub-Fabric bereitstellt. Sie besteht aus vier Azure Stack Hub Ruggedized-R640-Servern (auch als Knoten bezeichnet) und kann dynamisch auf bis zu 16 Knoten skaliert werden.|
|Pod    |Ein Pod im Kontext von Azure Stack Hub Ruggedized ist ein robuster physischer Behälter, der von zwei Personen getragen wird. Er enthält Rackkonsolen und Stoßdämpfer, um die Azure Stack Hub Ruggedized-Hardware vor physischer Belastung zu schützen. Für den Transport der Hardware können an der Transportkiste eine vordere und hintere Abdeckung befestigt werden, die plombiert werden kann. Eine Komplettlösung mit Mindestkonfiguration umfasst drei Pods.|


## <a name="deployment-workflow"></a>Bereitstellungsworkflow

Im Allgemeinen besteht der MDC-Bereitstellungsprozess aus den in den folgenden Abschnitten beschriebenen Phasen.

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
