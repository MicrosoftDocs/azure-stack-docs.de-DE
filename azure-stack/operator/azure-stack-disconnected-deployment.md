---
title: Entscheidungen zu nicht mit Azure verbundenen Bereitstellungen für in Azure Stack integrierte Systeme | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Entscheidungen bei der Bereitstellungsplanung für nicht mit Azure verbundenen Azure Stack-Bereitstellungen mit mehreren Knoten treffen.
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
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 239e2f1ad0ee75a7342c3092521e3604d4bc97ae
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985534"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Planungsentscheidungen zu nicht mit Azure verbundenen Bereitstellungen für in Azure Stack integrierte Systeme
Nachdem Sie entschieden haben, [wie Sie Azure Stack in Ihre Hybrid Cloud-Umgebung integrieren](azure-stack-connection-models.md), können Sie Ihre Azure Stack-Bereitstellungsentscheidungen abschließen.

Sie können Azure Stack ohne Verbindung mit dem Internet bereitstellen und verwenden. Bei einer nicht mit Azure verbundenen Bereitstellung sind Sie jedoch auf einen AD FS-Identitätsspeicher und das kapazitätsbasierte Abrechnungsmodell beschränkt. Weil für Mehrinstanzenfähigkeit Azure Active Directory (Azure AD) verwendet werden muss, wird sie bei nicht verbundenen Bereitstellungen nicht unterstützt. 

Wählen Sie diese Option in den folgenden Fällen:
- Es gelten Sicherheits- oder andere Einschränkungen, die es erforderlich machen, Azure Stack in einer Umgebung bereitzustellen, die nicht mit dem Internet verbunden ist.
- Sie möchten verhindern, dass Daten (einschließlich Nutzungsdaten) an Azure gesendet werden.
- Sie möchten Azure Stack als reine private Cloudlösung nutzen, die im Intranet Ihres Unternehmens bereitgestellt wird, und interessieren sich nicht für Hybridszenarien.

> [!TIP]
> Mitunter wird diese Art von Umgebung auch mit einem *U-Boot* verglichen.

Eine nicht mit Azure verbundene Bereitstellung hindert Sie nicht daran, später Ihre Azure Stack-Instanz mit Azure für hybride VM-Mandantenszenarien zu verbinden. Es bedeutet, dass Sie während der Bereitstellung keine Verbindung mit Azure haben bzw. Azure AD nicht als Identitätsspeicher verwenden möchten.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funktionen, die in von Azure getrennten Bereitstellungen eingeschränkt oder nicht verfügbar sind 
Azure Stack wurde so konzipiert, dass die Lösung am besten funktioniert, wenn sie mit Azure verbunden ist. Daher ist es wichtig zu beachten, dass es einige Features und Funktionen gibt, die entweder beeinträchtigt sind oder im von Azure getrennten Modus nicht verfügbar sind. 

|Feature|Beeinträchtigung im von Azure getrennten Modus|
|-----|-----|
|VM-Bereitstellung mit DSC-Erweiterung zum Konfigurieren von VMs nach der Bereitstellung|Eingeschränkt: DSC-Erweiterung sucht das neueste WMF im Internet.|
|VM-Bereitstellung mit Docker-Erweiterung zum Ausführen von Docker-Befehlen|Eingeschränkt: Docker durchsucht das Internet nach der neuesten Version, und diese Prüfung schlägt fehl.|
|Dokumentationslinks im Azure Stack-Portal|Nicht verfügbar: Links, z.B. Feedback geben, Hilfe, Schnellstart usw., die eine Internet-URL verwenden, funktionieren nicht.|
|Informationen zur Behebung von Warnungen, die auf eine online verfügbare Anleitung verweisen|Nicht verfügbar: Links zu einer Behebung von Warnungen, die eine Internet-URL verwenden, funktionieren nicht.|
|Marketplace: Die Möglichkeit, Katalogpakete direkt aus Azure Marketplace auszuwählen und hinzuzufügen|Beeinträchtigt: Wenn Sie Azure Stack in einem nicht verbundenen Modus (ohne Internetverbindung) bereitstellen, können Sie keine Marketplace-Elemente über das Azure Stack-Portal herunterladen. Sie können jedoch das [Tool für die Marketplace-Syndikation](azure-stack-download-azure-marketplace-item.md) verwenden, um die Marketplace-Elemente auf einen Computer mit Internetverbindung herunterzuladen und anschließend in Ihre Azure Stack-Umgebung zu übertragen.|
|Verwenden von Azure Active Directory-Verbundkonten zur Verwaltung einer Azure Stack-Bereitstellung|Nicht verfügbar: Hierfür ist eine Verbindung mit Azure erforderlich. Stattdessen muss AD FS mit einer lokalen Active Directory-Instanz verwendet werden.|
|App-Dienste|Beeinträchtigt: WebApps erfordern möglicherweise Internetzugriff für aktualisierte Inhalte.|
|Befehlszeilenschnittstelle (Command Line Interface, CLI)|Beeinträchtigt: Die Befehlszeilenschnittstelle bietet weniger Funktionalität in Bezug auf die Authentifizierung und Bereitstellung von Dienstprinzipalen.|
|Visual Studio: Cloud Discovery|Beeinträchtigt: Cloud Discovery ermittelt entweder andere Clouds oder funktioniert überhaupt nicht.|
|Visual Studio: AD FS|Beeinträchtigt: Nur Visual Studio Enterprise unterstützt AD FS.
Telemetrie|Nicht verfügbar: Telemetriedaten für Azure Stack sowie alle Katalogpakete von Drittanbietern, die von Telemetriedaten abhängig sind.|
|Zertifikate|Nicht verfügbar: Im Kontext von HTTPS ist eine Internetverbindung für die Dienste Zertifikatsperrliste (Certificate Revocation List, CRL) und Online Certificate Status-Protokoll (OSCP) erforderlich.|
|Key Vault|Beeinträchtigt: Ein typischer Anwendungsfall für Key Vault ist das Lesen von Geheimnissen durch eine Anwendung zur Laufzeit. Dazu benötigt die Anwendung einen Dienstprinzipal im Verzeichnis. In Azure Active Directory ist es normalen Benutzern (die keine Administratoren sind) standardmäßig erlaubt, Dienstprinzipale hinzuzufügen. In AD (bei Verwenden von AD FS) dürfen sie das nicht. Dies stellt ein Hindernis für die Benutzer dar, da stets ein Verzeichnisadministrator benötigt wird, um eine Anwendung hinzuzufügen.| 

## <a name="learn-more"></a>Weitere Informationen
- Informationen zu Anwendungsfällen, zum Erwerb, zu Partnern und zu OEM-Hardwareanbietern finden Sie auf der Produktseite von [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Informationen zur Roadmap und geografischen Verfügbarkeit für integrierte Azure Stack-Systeme finden Sie im Whitepaper: [Azure Stack: Eine Erweiterung von Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Weitere Informationen zu Microsoft Azure Stack-Paketen und -Preisen finden Sie im Datenblatt zu [Paketerstellung und Preisen](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nächste Schritte
[Rechenzentrums-Netzwerkintegration](azure-stack-network.md)
