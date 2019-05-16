---
title: Netzwerkdatenverkehr bei der Azure Stack-Bereitstellung | Microsoft-Dokumentation
description: In diesem Artikel werden die zu erwartenden Netzwerkvorgänge bei der Azure Stack-Bereitstellung beschrieben.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 20ec9e6ea8831e6667a282074ba28b16da3180f9
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618983"
---
# <a name="about-deployment-network-traffic"></a>Informationen zum Netzwerkdatenverkehr bei der Bereitstellung
Für eine erfolgreiche Bereitstellung ist es wichtig, den Netzwerkdatenverkehr bei der Azure Stack-Bereitstellung einschätzen zu können. In diesem Artikel wird der erwartete Netzwerkdatenverkehr während des Bereitstellungsvorgangs beschrieben, sodass Sie eine Vorstellung davon bekommen, was zu erwarten ist.

In dieser Abbildung sind alle Komponenten und Verbindungen des Bereitstellungsvorgangs dargestellt:

![Netzwerktopologie bei der Azure Stack-Bereitstellung](media/deployment-networking/figure1.png)

> [!NOTE]
> In diesem Artikel werden die Anforderungen für eine verbundene Bereitstellung beschrieben. Informationen zu weiteren Bereitstellungsmethoden finden Sie unter [Verbindungsmodelle für die Azure Stack-Bereitstellung](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Virtueller Bereitstellungscomputer
Die Azure Stack-Lösung umfasst eine Gruppe von Servern, die zum Hosten von Azure Stack-Komponenten verwendet werden, und einen zusätzlichen Server mit dem Namen Hardwarelebenszyklushost (HLH). Auf diesem Server wird der Lebenszyklus der Lösung bereitgestellt und verwaltet sowie der virtuelle Bereitstellungsserver (Deployment VM, DVM) während der Bereitstellung gehostet.

## <a name="deployment-requirements"></a>Bereitstellungsanforderungen
Es gibt einige Mindestanforderungen, die Sie vor Beginn der Bereitstellung vom Originalgerätehersteller überprüfen lassen können, um sicherzustellen, dass die Bereitstellung erfolgreich abgeschlossen wird. Anhand dieser Anforderungen können Sie die Umgebung vorbereiten und dafür sorgen, dass die Überprüfung erfolgreich ist. Die Anforderungen betreffen Folgendes:

-   [Certificates](azure-stack-pki-certs.md)
-   [Azure-Abonnement](https://azure.microsoft.com/free/?b=17.06)
-   Zugriff auf das Internet
-   DNS
-   NTP

> [!NOTE]
> Dieser Artikel konzentriert sich auf die letzten drei Anforderungen. Weitere Informationen zu den ersten beiden Anforderungen erhalten Sie über die Links oben.

## <a name="deployment-network-traffic"></a>Netzwerkdatenverkehr bei der Bereitstellung
Der virtuelle Bereitstellungsserver wird mit einer IP-Adresse aus dem BMC-Netzwerk konfiguriert und benötigt Netzwerkzugriff auf das Internet. Auch wenn nicht für alle BMC-Netzwerkkomponenten externes Routing oder der Zugriff auf das Internet erforderlich ist, ist es möglich, dass dies auch für einige OEM-spezifische Komponenten erforderlich ist, die IP-Adressen aus diesem Netzwerk nutzen.

Während der Bereitstellung führt der virtuelle Bereitstellungsserver über ein Azure-Konto Ihres Abonnements die Authentifizierung bei Azure Active Directory (Azure AD) durch. Dazu benötigt der virtuelle Bereitstellungsserver Internetzugriff auf eine Liste spezifischer Ports und URLs. Die vollständige Liste finden Sie in der Dokumentation zum [Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md). Der virtuelle Bereitstellungsserver nutzt einen DNS-Server zum Weiterleiten von DNS-Anforderungen, die von internen Komponenten an externe URLs erfolgen. Der interne DNS leitet diese Anforderungen an die Adresse der DNS-Weiterleitung weiter, die Sie dem Originalgerätehersteller vor der Bereitstellung mitteilen. Dasselbe gilt für den NTP-Server, einen zuverlässigen Zeitserver, der erforderlich ist, um die Konsistenz und Zeitsynchronisierung für alle Azure Stack-Komponenten zu verwalten.

Der für den virtuellen Bereitstellungsserver erforderliche Internetzugriff während der Bereitstellung gilt nur für ausgehenden Datenverkehr, da während der Bereitstellung keine eingehenden Aufrufe erfolgen. Berücksichtigen Sie, dass er seine IP-Adresse als Quelle verwendet und dass Azure Stack keine Proxykonfigurationen unterstützt. Daher müssen Sie gegebenenfalls einen transparenten Proxy oder NAT für den Zugriff auf das Internet angeben. Während der Bereitstellung beginnen einige interne Komponenten, mithilfe öffentlicher VIPs über das externe Netzwerk auf das Internet zuzugreifen. Nach Abschluss der Bereitstellung erfolgt die gesamte Kommunikation zwischen Azure und Azure Stack mithilfe öffentlicher VIPs über das externe Netzwerk.

Netzwerkkonfigurationen auf Azure Stack-Switches enthalten Zugriffssteuerungslisten (ACLs), die den Datenverkehr zwischen bestimmten Netzwerkquellen und -zielen einschränken. Der virtuelle Bereitstellungsserver ist die einzige Komponente mit unbeschränktem Zugriff, selbst der Zugriff für den HLH ist sehr eingeschränkt. Sie können sich bei Ihrem Originalgerätehersteller nach Anpassungsoptionen erkundigen, über die sich die Verwaltung und der Zugriff über Ihre Netzwerke vereinfachen lässt. Aufgrund dieser ACLs sollten die Adressen des DNS- und NTP-Servers zum Zeitpunkt der Bereitstellung auf keinen Fall geändert werden. Andernfalls müssen Sie alle Switches für die Lösung neu konfigurieren.

Nach Abschluss der Bereitstellung werden die bereitgestellten Adressen des DNS- und NTP-Servers weiterhin direkt von den Komponenten des Systems verwendet. Wenn Sie beispielsweise nach Abschluss der Bereitstellung DNS-Anforderungen überprüfen, wird die Quelle von der IP-Adresse des virtuellen Bereitstellungsservers in eine Adresse aus dem Bereich des externen Netzwerks geändert.

Nach Abschluss der Bereitstellung werden die bereitgestellten Adressen des DNS- und NTP-Servers weiterhin von den Komponenten des Systems verwendet, und zwar über den Hostnamen mithilfe des externen Netzwerks. Wenn Sie beispielsweise nach Abschluss der Bereitstellung DNS-Anforderungen überprüfen, wird die Quelle von der IP-Adresse des virtuellen Bereitstellungsservers in eine öffentliche VIP geändert.

## <a name="next-steps"></a>Nächste Schritte
[Überprüfen der Azure-Registrierung](azure-stack-validate-registration.md)
