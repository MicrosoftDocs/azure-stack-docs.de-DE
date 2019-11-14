---
title: Entscheidungen zu nicht mit Azure verbundenen Bereitstellungen für in Azure Stack integrierte Systeme | Microsoft-Dokumentation
description: Enthält Informationen zur nicht mit Azure verbundenen Bereitstellung von integrierten Azure Stack-Systemen und zu den relevanten Planungsentscheidungen.
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
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 183abaa85607f0ad0c1bb9f82901b86176ae5c65
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955233"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Planungsentscheidungen zu nicht mit Azure verbundenen Bereitstellungen für in Azure Stack integrierte Systeme
Nachdem Sie entschieden haben, [wie Sie Azure Stack in Ihre Hybrid Cloud-Umgebung integrieren](azure-stack-connection-models.md), können Sie Ihre Entscheidungsfindung für die Azure Stack-Bereitstellung abschließen.

Sie können Azure Stack ohne Verbindung mit dem Internet bereitstellen und verwenden. Bei einer nicht mit Azure verbundenen Bereitstellung sind Sie aber auf einen AD FS-Identitätsspeicher (Active Directory-Verbunddienste) und das kapazitätsbasierte Abrechnungsmodell beschränkt. Da für Mehrinstanzenfähigkeit Azure Active Directory (Azure AD) verwendet werden muss, wird sie bei nicht verbundenen Bereitstellungen nicht unterstützt.

Wählen Sie diese Option in den folgenden Fällen:
- Es gelten Sicherheits- oder andere Einschränkungen, die es erforderlich machen, Azure Stack in einer Umgebung bereitzustellen, die nicht mit dem Internet verbunden ist.
- Sie möchten verhindern, dass Daten (einschließlich Nutzungsdaten) an Azure gesendet werden.
- Sie möchten Azure Stack als reine private Cloudlösung nutzen, die im Intranet Ihres Unternehmens bereitgestellt wird, und interessieren sich nicht für Hybridszenarien.

> [!TIP]
> Diese Art von Umgebung wird auch mit einem *U-Boot* verglichen.

Eine nicht mit Azure verbundene Bereitstellung hindert Sie nicht daran, später Ihre Azure Stack-Instanz für hybride VM-Mandantenszenarien mit Azure zu verbinden. Es bedeutet, dass Sie während der Bereitstellung keine Verbindung mit Azure haben bzw. Azure AD nicht als Identitätsspeicher verwenden möchten.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funktionen, die in von Azure getrennten Bereitstellungen eingeschränkt oder nicht verfügbar sind 
Azure Stack wurde so konzipiert, dass die Lösung am besten funktioniert, wenn sie mit Azure verbunden ist. Daher ist es wichtig zu beachten, dass es einige Features und Funktionen gibt, die entweder beeinträchtigt oder im von Azure getrennten Modus nicht verfügbar sind.

|Feature|Beeinträchtigung im von Azure getrennten Modus|
|-----|-----|
|VM-Bereitstellung mit DSC-Erweiterung zum Konfigurieren von VMs nach der Bereitstellung|Eingeschränkt: DSC-Erweiterung sucht das neueste WMF im Internet.|
|VM-Bereitstellung mit Docker-Erweiterung zum Ausführen von Docker-Befehlen|Eingeschränkt: Docker durchsucht das Internet nach der neuesten Version, und diese Prüfung schlägt fehl.|
|Dokumentationslinks im Azure Stack-Portal|Nicht verfügbar: Links, z. B. Feedback geben, Hilfe und Schnellstart, für die eine Internet-URL verwendet wird, funktionieren nicht.|
|Informationen zur Behebung von Warnungen, die auf eine online verfügbare Anleitung verweisen|Nicht verfügbar: Links zu einer Behebung von Warnungen, für die eine Internet-URL verwendet wird, funktionieren nicht.|
|Marketplace: Die Möglichkeit, Katalogpakete direkt aus Azure Marketplace auszuwählen und hinzuzufügen.|Beeinträchtigt: Wenn Sie Azure Stack in einem nicht verbundenen Modus bereitstellen, können Sie über das Azure Stack-Portal keine Marketplace-Elemente herunterladen. Sie können jedoch das [Tool für die Marketplace-Syndikation](azure-stack-download-azure-marketplace-item.md) verwenden, um die Marketplace-Elemente auf einen Computer mit Internetverbindung herunterzuladen und anschließend in Ihre Azure Stack-Umgebung zu übertragen.|
|Verwenden von Azure AD-Verbundkonten zur Verwaltung einer Azure Stack-Bereitstellung|Nicht verfügbar: Hierfür ist eine Verbindung mit Azure erforderlich. Stattdessen muss AD FS mit einer lokalen Active Directory-Instanz verwendet werden.|
|App-Dienste|Beeinträchtigt: Für WebApps ist für aktualisierte Inhalte ggf. Internetzugriff erforderlich.|
|Befehlszeilenschnittstelle (Command Line Interface, CLI)|Beeinträchtigt: Die Befehlszeilenschnittstelle verfügt über eine eingeschränkte Funktionalität in Bezug auf die Authentifizierung und Bereitstellung von Dienstprinzipalen.|
|Visual Studio: Cloud Discovery|Beeinträchtigt: Cloud Discovery ermittelt entweder andere Clouds oder funktioniert überhaupt nicht.|
|Visual Studio: AD FS|Beeinträchtigt: Nur Visual Studio Enterprise und Visual Studio Code unterstützen die AD FS-Authentifizierung.
Telemetrie|Nicht verfügbar: Telemetriedaten für Azure Stack und alle Katalogpakete von Drittanbietern, die von Telemetriedaten abhängig sind.|
|Zertifikate|Nicht verfügbar: Im Kontext von HTTPS ist eine Internetverbindung für die Dienste „Zertifikatsperrliste“ (Certificate Revocation List, CRL) und „Online Certificate Status-Protokoll“ (OSCP) erforderlich.|
|Key Vault|Beeinträchtigt: Ein typischer Anwendungsfall für Key Vault ist das Lesen von Geheimnissen durch eine App zur Laufzeit. Für diesen Anwendungsfall benötigt die App einen Dienstprinzipal im Verzeichnis. In Azure AD ist es regulären Benutzern (die keine Administratoren sind) standardmäßig erlaubt, Dienstprinzipale hinzuzufügen. In Azure AD (mit AD FS) ist dies für reguläre Benutzer nicht zulässig. Diese Einschränkung stellt ein Hindernis für die Benutzer dar, da stets ein Verzeichnisadministrator benötigt wird, um eine App hinzuzufügen.

## <a name="learn-more"></a>Weitere Informationen
- Informationen zu Anwendungsfällen, zum Erwerb, zu Partnern und zu OEM-Hardwareanbietern finden Sie auf der Produktseite von [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Informationen zur Roadmap und geografischen Verfügbarkeit für integrierte Azure Stack-Systeme finden Sie im Whitepaper: [Azure Stack: Eine Erweiterung von Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Um mehr über Microsoft Azure Stack-Pakete und -Preise zu erfahren, [laden Sie die PDF-Datei herunter](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nächste Schritte
[Rechenzentrums-Netzwerkintegration](azure-stack-network.md)
