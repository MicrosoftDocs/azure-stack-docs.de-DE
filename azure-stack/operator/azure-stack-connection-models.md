---
title: Verbindungsmodell von in Azure Stack integrierten Systemen | Microsoft-Dokumentation
description: Bestimmen von Verbindungsmodellen und andere Entscheidungen zur Bereitstellungsplanung für in Azure Stack integrierte Systeme.
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
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: de9051ceee89244182c1d6d9d5724fa80a594ae6
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094392"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Verbindungsmodell von in Azure Stack integrierten Systemen
Wenn Sie daran interessiert sind, ein in Azure Stack integriertes System zu kaufen, sollten Sie einige der [wichtigsten Aspekte bei der Rechenzentrumsintegration](azure-stack-datacenter-integration.md) hinsichtlich der Azure Stack-Bereitstellung verstehen, um zu bestimmen, wie das System in Ihr Rechenzentrum passt. Darüber hinaus müssen Sie sich überlegen, wie Sie Azure Stack in Ihre Hybrid Cloud-Umgebung integrieren möchten. Dieser Artikel bietet einen Überblick über diese wichtigen Entscheidungen, so z. B. zu Azure-Verbindungsmodellen, zu Identitätsspeicher- und Abrechnungsmodelloptionen.

Wenn Sie ein integriertes System erwerben möchten, führt Sie Ihr OEM-Hardwareanbieter (Originalgerätehersteller) dabei ausführlicher durch den Planungsprozess. Der OEM-Hardwarehersteller führt auch die eigentliche Bereitstellung aus.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Wählen eines Verbindungsmodells für die Azure Stack-Bereitstellung
Sie können Azure Stack entweder bei vorhandener Verbindung mit dem Internet (und mit Azure) oder offline bereitstellen. Wählen Sie die Bereitstellung mit Azure-Verbindung, wenn Sie Azure Stack einschließlich der Hybridszenarien zwischen Azure Stack und Azure optimal nutzen möchten. Diese Auswahl legt fest, welche Optionen für Ihren Identitätsspeicher (Azure Active Directory oder Active Directory-Verbunddienste) und Ihr Abrechnungsmodell (nutzungs- oder kapazitätsbasiert) verfügbar sind, wie in der folgenden Abbildung und Tabelle zusammengefasst:

![Azure Stack-Bereitstellungs- und Abrechnungsszenarien](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Dies ist eine überaus wichtige Entscheidung! Die Wahl von Active Directory-Verbunddienste (AD FS) oder Azure Active Directory (Azure AD) ist eine Entscheidung, die Sie bei der Bereitstellung einmalig treffen müssen. Sie können diese Einstellung später nicht mehr ändern, ohne das gesamte System neu zu installieren.  


|Optionen|Mit Azure verbunden|Nicht mit Azure verbunden|
|-----|:-----:|:-----:|
|Azure AD|![Unterstützt](media/azure-stack-connection-models/check.png)| |
|AD FS|![Unterstützt](media/azure-stack-connection-models/check.png)|![Unterstützt](media/azure-stack-connection-models/check.png)|
|Nutzungsbasierte Abrechnung|![Unterstützt](media/azure-stack-connection-models/check.png)| |
|Kapazitätsbasierte Abrechnung|![Unterstützt](media/azure-stack-connection-models/check.png)|![Unterstützt](media/azure-stack-connection-models/check.png)|
|Lizenzierung| Enterprise Agreement oder Cloud Solution Provider | Enterprise Agreement |
|Patch und Update|Das Updatepaket kann direkt aus dem Internet in Azure Stack heruntergeladen werden. |  Erforderlich<br><br>Erfordert auch Wechselmedien<br> und ein separates verbundenes Gerät |
| Registrierung | Automatisiert | Erforderlich<br><br>Erfordert auch Wechselmedien<br> und ein separates verbundenes Gerät |

Nachdem Sie sich für das Azure-Verbindungsmodell entschieden haben, das für die Bereitstellung von Azure Stack verwendet werden soll, müssen zusätzliche, verbindungsabhängige Entscheidungen für den Identitätsspeicher und die Abrechnungsmethode getroffen werden.

## <a name="next-steps"></a>Nächste Schritte

[Planungsentscheidungen zu mit Azure verbundenen Bereitstellungen für in Azure Stack integrierte Systeme](azure-stack-connected-deployment.md)

[Planungsentscheidungen zu nicht mit Azure verbundenen Bereitstellungen für in Azure Stack integrierte Systeme](azure-stack-disconnected-deployment.md)
