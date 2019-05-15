---
title: Azure Stack Development Kit – Architektur | Microsoft-Dokumentation
description: In diesem Artikel wird die Architektur des Azure Stack Development Kits (ASDK) beschrieben.
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
ms.date: 01/21/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 520b90bdd272086bcef3a27928f6d31b3314b0fa
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617358"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit – Architektur
Das Azure Stack Development Kit (ASDK) ist eine Bereitstellung von Azure Stack mit einem Knoten, die auf einem einzelnen Hostcomputer ausgeführt wird. Edge-Routingkomponenten werden auf dem Hostcomputer installiert, um NAT- und VPN-Funktionen für Azure Stack bereitzustellen. Azure Stack-Infrastrukturrollen werden auf der Hyper-V-Ebene des physischen Hostcomputers ausgeführt.


## <a name="virtual-machine-roles"></a>Rollen virtueller Computer
Das ASDK bietet Dienste unter Verwendung der folgenden auf dem Hostcomputer des Development Kits gehosteten VMs:

| NAME | BESCHREIBUNG |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack-Speicherdienste|
| **AzS-ADFS01** | Active Directory-Verbunddienste (ADFS)  |
| **AzS-CA01** | Zertifizierungsstellendienste für Azure Stack-Rollendienste|
| **AzS-DC01** | Active Directory, DNS und DHCP-Dienste für Microsoft Azure Stack|
| **AzS-ERCS01** | Notfallwiederherstellungskonsolen-VM |
| **AzS-GWY01** | Edge-Gateway-Dienste, z.B. VPN-Site-to-Site-Verbindungen für Mandantennetzwerke|
| **AzS-NC01** | Netzwerkcontroller, der Azure Stack-Netzwerkdienste verwaltet  |
| **AzS-SLB01** | Lastenausgleichs-Multiplexerdienste in Azure Stack für beide Mandanten und Azure Stack-Infrastrukturdienste  |
| **AzS-SQL01** | Interner Datenspeicher für Azure Stack-Infrastrukturrollen  |
| **AzS-WAS01** | Azure Stack-Verwaltungsportal und Azure Resource Manager-Dienste|
| **AzS-WASP01**| Azure Stack-Benutzerportal (Mandantenportal) und Azure Resource Manager-Dienste|
| **AzS-XRP01** | Infrastrukturverwaltungscontroller für Microsoft Azure Stack, einschließlich Compute-, Netzwerk- und Speicherressourcenanbietern|


## <a name="next-steps"></a>Nächste Schritte
[Informationen zu grundlegenden ASDK-Verwaltungsaufgaben](asdk-admin-basics.md)
