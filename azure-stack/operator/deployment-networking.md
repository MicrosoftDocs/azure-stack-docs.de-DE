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
ms.date: 12/05/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: 3dc764bd94712dd95ae76358148a4cb0a533fb48
ms.sourcegitcommit: 8e5a4659d8669ccfe6cd75d03200ae09a16b5f8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74898551"
---
# <a name="about-deployment-network-traffic"></a>Informationen zum Netzwerkdatenverkehr bei der Bereitstellung
Wenn Sie bei der Azure Stack-Bereitstellung mit dem Netzwerkdatenverkehr vertraut sind, trägt dies zum Erfolg der Bereitstellung bei. In diesem Artikel wird Schritt für Schritt der Ablauf des Netzwerkdatenverkehrs während der Bereitstellung beschrieben, damit Sie wissen, was zu erwarten ist.

In dieser Abbildung sind alle Komponenten und Verbindungen des Bereitstellungsvorgangs dargestellt:

![Netzwerktopologie bei der Azure Stack-Bereitstellung](media/deployment-networking/figure1.png)

> [!NOTE]
> In diesem Artikel werden die Anforderungen für eine verbundene Bereitstellung beschrieben. Informationen zu weiteren Bereitstellungsmethoden finden Sie unter [Verbindungsmodelle für die Azure Stack-Bereitstellung](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Virtueller Bereitstellungscomputer
Die Azure Stack-Lösung umfasst eine Gruppe von Servern, die zum Hosten von Azure Stack-Komponenten verwendet werden, und einen zusätzlichen Server mit dem Namen Hardwarelebenszyklushost (HLH). Auf diesem Server wird der Lebenszyklus der Lösung bereitgestellt und verwaltet sowie der virtuelle Bereitstellungsserver (Deployment VM, DVM) während der Bereitstellung gehostet.

Azure Stack-Lösungsanbieter stellen ggf. zusätzliche VMs für die Verwaltung bereit. Holen Sie eine Bestätigung vom Lösungsanbieter ein, bevor Sie Änderungen an den Verwaltungs-VMs des Lösungsanbieters vornehmen.

## <a name="deployment-requirements"></a>Bereitstellungsanforderungen
Es gibt einige Mindestanforderungen, die Sie vor Beginn der Bereitstellung vom Originalgerätehersteller überprüfen lassen können, um sicherzustellen, dass die Bereitstellung erfolgreich abgeschlossen wird:

-   [Certificates](azure-stack-pki-certs.md)
-   [Azure-Abonnement](azure-stack-validate-registration.md). Gegebenenfalls ist eine Überprüfung Ihres Abonnements erforderlich.
-   Zugriff auf das Internet
-   DNS
-   NTP

> [!NOTE]
> Dieser Artikel konzentriert sich auf die letzten drei Anforderungen. Weitere Informationen zu den ersten beiden Anforderungen erhalten Sie über die Links oben.

## <a name="deployment-network-traffic"></a>Netzwerkdatenverkehr bei der Bereitstellung
Der virtuelle Bereitstellungsserver wird mit einer IP-Adresse aus dem BMC-Netzwerk konfiguriert und benötigt Netzwerkzugriff auf das Internet. Auch wenn nicht für alle BMC-Netzwerkkomponenten externes Routing oder der Zugriff auf das Internet erforderlich ist, ist es möglich, dass dies auch für einige OEM-spezifische Komponenten erforderlich ist, die IP-Adressen aus diesem Netzwerk nutzen.

Während der Bereitstellung führt der virtuelle Bereitstellungsserver über ein Azure-Konto Ihres Abonnements die Authentifizierung bei Azure Active Directory (Azure AD) durch. Hierzu benötigt der virtuelle Bereitstellungsserver Internetzugriff auf eine Liste mit [spezifischen Ports und URLs](azure-stack-integrate-endpoints.md). Der virtuelle Bereitstellungsserver nutzt einen DNS-Server zum Weiterleiten von DNS-Anforderungen, die von internen Komponenten an externe URLs erfolgen. Der interne DNS leitet diese Anforderungen an die Adresse der DNS-Weiterleitung weiter, die Sie dem Originalgerätehersteller vor der Bereitstellung mitteilen. Dasselbe gilt für den NTP-Server, einen zuverlässigen Zeitserver, der erforderlich ist, um die Konsistenz und Zeitsynchronisierung für alle Azure Stack-Komponenten zu verwalten.

Der für den virtuellen Bereitstellungsserver erforderliche Internetzugriff während der Bereitstellung gilt nur für ausgehenden Datenverkehr, da während der Bereitstellung keine eingehenden Aufrufe erfolgen. Berücksichtigen Sie, dass er seine IP-Adresse als Quelle verwendet und dass Azure Stack keine Proxykonfigurationen unterstützt. Daher müssen Sie gegebenenfalls einen transparenten Proxy oder NAT für den Zugriff auf das Internet angeben. Während der Bereitstellung beginnen einige interne Komponenten, mithilfe öffentlicher VIPs über das externe Netzwerk auf das Internet zuzugreifen. Nach Abschluss der Bereitstellung erfolgt die gesamte Kommunikation zwischen Azure und Azure Stack mithilfe öffentlicher VIPs über das externe Netzwerk.

Netzwerkkonfigurationen auf Azure Stack-Switches enthalten Zugriffssteuerungslisten (ACLs), die den Datenverkehr zwischen bestimmten Netzwerkquellen und -zielen einschränken. Der virtuelle Bereitstellungsserver ist die einzige Komponente mit uneingeschränktem Zugriff. Selbst der Zugriff für den HLH ist eingeschränkt. Sie können sich bei Ihrem Originalgerätehersteller nach Anpassungsoptionen erkundigen, über die sich die Verwaltung und der Zugriff über Ihre Netzwerke vereinfachen lässt. Aufgrund dieser ACLs sollten die Adressen des DNS- und NTP-Servers zum Zeitpunkt der Bereitstellung auf keinen Fall geändert werden. Andernfalls müssen Sie alle Switches für die Lösung neu konfigurieren.

Nach Abschluss der Bereitstellung werden die bereitgestellten Adressen des DNS- und NTP-Servers weiterhin von den Komponenten des Systems verwendet, und zwar über den Hostnamen mithilfe des externen Netzwerks. Wenn Sie beispielsweise nach Abschluss der Bereitstellung DNS-Anforderungen überprüfen, wird die Quelle von der IP-Adresse des virtuellen Bereitstellungsservers in eine öffentliche VIP geändert.

## <a name="next-steps"></a>Nächste Schritte
[Überprüfen der Azure-Registrierung](azure-stack-validate-registration.md)
