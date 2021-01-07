---
title: Verbindungsmodell von in Azure Stack Hub integrierten Systemen
description: Bestimmen von Verbindungsmodellen und andere Entscheidungen zur Bereitstellungsplanung für in Azure Stack Hub integrierte Systeme.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 4d3cf019366b1e3b9b42f83948a170427ffbb1bd
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871102"
---
# <a name="azure-stack-hub-integrated-systems-connection-models"></a>Verbindungsmodell von in Azure Stack Hub integrierten Systemen
Wenn Sie daran interessiert sind, ein in Azure Stack Hub integriertes System zu kaufen, sollten Sie einige der [wichtigsten Aspekte bei der Rechenzentrumsintegration](azure-stack-datacenter-integration.md) hinsichtlich der Azure Stack Hub-Bereitstellung verstehen, um zu bestimmen, wie das System in Ihr Rechenzentrum passt. Darüber hinaus müssen Sie sich überlegen, wie Sie Azure Stack Hub in Ihre Hybrid Cloud-Umgebung integrieren möchten. Dieser Artikel bietet einen Überblick über diese wichtigen Entscheidungen, so z. B. zu Azure-Verbindungsmodellen, zu Identitätsspeicher- und Abrechnungsmodelloptionen.

Wenn Sie ein integriertes System erwerben möchten, führt Sie Ihr OEM-Hardwareanbieter (Originalgerätehersteller) dabei ausführlicher durch den Planungsprozess. Der OEM-Hardwarehersteller führt auch die eigentliche Bereitstellung aus.

## <a name="choose-an-azure-stack-hub-deployment-connection-model"></a>Wählen eines Verbindungsmodells für die Azure Stack Hub-Bereitstellung
Sie können Azure Stack Hub entweder mit dem Internet (und mit Azure) verbunden oder unverbunden bereitstellen. Wählen Sie die Bereitstellung mit Azure-Verbindung, wenn Sie Azure Stack Hub einschließlich der Hybridszenarien zwischen Azure Stack Hub und Azure optimal nutzen möchten. Diese Auswahl legt fest, welche Optionen für Ihren Identitätsspeicher (Azure Active Directory oder Active Directory-Verbunddienste) und Ihr Abrechnungsmodell (nutzungs- oder kapazitätsbasiert) verfügbar sind, wie in der folgenden Abbildung und Tabelle zusammengefasst:

![Azure Stack Hub-Bereitstellungs- und Abrechnungsszenarien](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Dies ist eine überaus wichtige Entscheidung! Die Wahl von Active Directory-Verbunddienste (AD FS) oder Azure Active Directory (Azure AD) ist eine Entscheidung, die Sie bei der Bereitstellung einmalig treffen müssen. Sie können diese Einstellung später nicht mehr ändern, ohne das gesamte System neu zu installieren.  


|Tastatur|Mit Azure verbunden|Nicht mit Azure verbunden|
|-----|:-----:|:-----:|
|Azure AD|![Unterstützt](media/azure-stack-connection-models/check.png)| |
|AD FS|![Unterstützt](media/azure-stack-connection-models/check.png)|![Unterstützt](media/azure-stack-connection-models/check.png)|
|Nutzungsbasierte Abrechnung|![Unterstützt](media/azure-stack-connection-models/check.png)| |
|Kapazitätsbasierte Abrechnung|![Unterstützt](media/azure-stack-connection-models/check.png)|![Unterstützt](media/azure-stack-connection-models/check.png)|
|Lizenzierung| Enterprise Agreement oder Cloud Solution Provider | Enterprise Agreement |
|Patch und Update|Das Updatepaket kann direkt aus dem Internet in Azure Stack Hub heruntergeladen werden. |  Erforderlich<br><br>Erfordert auch Wechselmedien<br> und ein separates verbundenes Gerät |
| Registrierung | Automatisiert | Erforderlich<br><br>Erfordert auch Wechselmedien<br> und ein separates verbundenes Gerät |

Nachdem Sie sich für das Azure-Verbindungsmodell entschieden haben, das für die Bereitstellung von Azure Stack Hub verwendet werden soll, müssen zusätzliche, verbindungsabhängige Entscheidungen für den Identitätsspeicher und die Abrechnungsmethode getroffen werden.

## <a name="next-steps"></a>Nächste Schritte

[Planungsentscheidungen zu mit Azure verbundenen Bereitstellungen für in Azure Stack Hub integrierte Systeme](azure-stack-connected-deployment.md)

[Planungsentscheidungen zu nicht mit Azure verbundenen Bereitstellungen für in Azure Stack Hub integrierte Systeme](azure-stack-disconnected-deployment.md)
