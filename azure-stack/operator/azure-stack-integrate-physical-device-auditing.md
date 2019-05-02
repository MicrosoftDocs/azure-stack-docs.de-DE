---
title: Überwachen von physischen Geräten in Azure Stack
description: Es wird beschrieben, wie Sie die Überwachung des Zugriffs auf physische Geräte in Azure Stack integrieren.
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 02/11/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 02/11/2019
keywords: ''
ms.openlocfilehash: 7e39370879884dc8900671d174fc6e0708907d83
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307110"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integration des Azure Stack-Datencenters – Überwachen von physischen Geräten

Alle physischen Geräte in Azure Stack, z. B. die Baseboard-Verwaltungscontroller (Baseboard Management Controllers, BMCs) und Netzwerkswitches, geben Überwachungsprotokolle aus. Diese können Sie in Ihre Gesamtlösung für die Überwachung integrieren. Da sich die Geräte für die unterschiedlichen Azure Stack-OEM-Hardwareanbieter unterscheiden, sollten Sie sich wegen der Dokumentation zur Überwachungsintegration an Ihren Anbieter wenden.
Die Abschnitte unten enthalten einige allgemeine Informationen zur Überwachung von physischen Geräten in Azure Stack.  

## <a name="physical-device-access-auditing"></a>Überwachen des Zugriffs auf physische Geräte

Alle physischen Geräte in Azure Stack unterstützen TACACS oder RADIUS. Die Unterstützung umfasst auch den Zugriff auf den Baseboard-Verwaltungscontroller und auf Netzwerkswitches.

RADIUS und TACACS gehören nicht zum Leistungsumfang von Azure Stack-Lösungen. Die Lösungen wurden jedoch dahingehend überprüft, dass eine Verwendung mit bereits am Markt verfügbaren RADIUS- und TACACS-Lösungen unterstützt wird.

Für RADIUS (nicht jedoch für TACACS) wurde die Verwendung von MSCHAPv2 überprüft. Hierbei handelt es sich um die sicherste Implementierung mithilfe von RADIUS.
Wenden Sie sich zum Aktivieren von TACAS oder RADIUS auf Geräten in Ihrer Azure Stack-Lösung an Ihren OEM-Hardwarehersteller.

## <a name="syslog-forwarding-for-network-devices"></a>Syslog-Weiterleitung für Netzwerkgeräte

Alle physischen Netzwerkgeräte in Azure Stack unterstützen Syslog-Nachrichten. Ein Syslog-Server gehört nicht zum Leistungsumfang von Azure Stack-Lösungen. Die Geräte wurden aber darauf überprüft, dass das Senden von Nachrichten an bereits am Markt verfügbare Syslog-Lösungen unterstützt wird.

Die Syslog-Zieladresse ist ein optionaler Parameter, der für die Bereitstellung erfasst wird. Er kann aber auch nach der Bereitstellung hinzugefügt werden. Wenden Sie sich an Ihren OEM-Hardwareanbieter, um die Syslog-Weiterleitung auf Ihren Netzwerkgeräten zu konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md)
