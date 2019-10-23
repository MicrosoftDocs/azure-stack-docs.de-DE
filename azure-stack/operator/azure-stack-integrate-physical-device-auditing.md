---
title: Integrieren der Überwachung physischer Geräte in Ihr Azure Stack-Rechenzentrum | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie die Überwachung des Zugriffs auf physische Geräte in Ihr Azure Stack-Rechenzentrum integrieren.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
keywords: ''
ms.openlocfilehash: b5fa17b3913db7ebec210fc3bf986bac6414368e
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277226"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-datacenter"></a>Integrieren der Überwachung physischer Geräte in Ihr Azure Stack-Rechenzentrum

Alle physischen Geräte in Azure Stack, z. B. die Baseboard-Verwaltungscontroller (Baseboard Management Controllers, BMCs) und Netzwerkswitches, geben Überwachungsprotokolle aus. Diese können Sie in Ihre Gesamtlösung für die Überwachung integrieren. Da sich die Geräte für die unterschiedlichen Azure Stack-OEM-Hardwareanbieter unterscheiden, sollten Sie sich wegen der Dokumentation zur Überwachungsintegration an Ihren Anbieter wenden. Die Abschnitte unten enthalten einige allgemeine Informationen zur Überwachung von physischen Geräten in Azure Stack.  

## <a name="physical-device-access-auditing"></a>Überwachen des Zugriffs auf physische Geräte

Alle physischen Geräte in Azure Stack unterstützen TACACS oder RADIUS. Die Unterstützung umfasst auch den Zugriff auf den Baseboard-Verwaltungscontroller und auf Netzwerkswitches.

RADIUS und TACACS gehören nicht zum Leistungsumfang von Azure Stack-Lösungen. Die Lösungen wurden jedoch dahingehend überprüft, dass eine Verwendung mit bereits am Markt verfügbaren RADIUS- und TACACS-Lösungen unterstützt wird.

Für RADIUS (nicht jedoch für TACACS) wurde die Verwendung von MSCHAPv2 überprüft. Hierbei handelt es sich um die sicherste Implementierung mithilfe von RADIUS. Wenden Sie sich zum Aktivieren von TACAS oder RADIUS auf Geräten in Ihrer Azure Stack-Lösung an Ihren OEM-Hardwarehersteller.

## <a name="syslog-forwarding-for-network-devices"></a>Syslog-Weiterleitung für Netzwerkgeräte

Alle physischen Netzwerkgeräte in Azure Stack unterstützen Syslog-Nachrichten. Ein Syslog-Server gehört nicht zum Leistungsumfang von Azure Stack-Lösungen. Die Geräte wurden aber darauf überprüft, dass das Senden von Nachrichten an bereits am Markt verfügbare Syslog-Lösungen unterstützt wird.

Die Syslog-Zieladresse ist ein optionaler Parameter, der für die Bereitstellung erfasst wird. Er kann aber auch nach der Bereitstellung hinzugefügt werden. Wenden Sie sich an Ihren OEM-Hardwareanbieter, um die Syslog-Weiterleitung auf Ihren Netzwerkgeräten zu konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md)
