---
title: Identitätsarchitektur für Azure Stack Hub
description: Erfahren Sie mehr über die Identitätsarchitektur für Azure Stack Hub und über die Unterschiede zwischen Azure AD und AD FS.
author: BryanLa
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 07/20/2020
ms.openlocfilehash: fa79df515e2676655ea98b6024179d3f56c41fbf
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86566191"
---
# <a name="identity-architecture-for-azure-stack-hub"></a>Identitätsarchitektur für Azure Stack Hub

Bei der Auswahl eines Identitätsanbieters zur Verwendung mit Azure Stack Hub ist es wichtig, die wesentlichen Unterschiede zwischen Azure Active Directory (Azure AD) und den Active Directory-Verbunddiensten (Active Directory Federation Services, AD FS) zu kennen.

## <a name="capabilities-and-limitations"></a>Funktionen und Einschränkungen

Die Wahl des Identitätsanbieters schränkt unter Umständen Ihre Optionen ein – einschließlich der Unterstützung mehrerer Mandanten.

|Funktion oder Szenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Verbindung mit dem Internet vorhanden     |Ja       |Optional|
|Unterstützung mehrerer Mandanten     |Ja       |Nein      |
|Elemente im Marketplace anbieten |Ja       |Ja (Nutzung des Tools für die [Marketplace-Offlinesyndikation](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) ist erforderlich)|
|Unterstützung der Active Directory Authentication Library (ADAL) |Ja |Ja|
|Unterstützung von Tools wie Azure-Befehlszeilenschnittstelle, Visual Studio und PowerShell  |Ja |Ja|
|Erstellen von Dienstprinzipalen über das Azure-Portal     |Ja |Nein|
|Erstellen von Dienstprinzipalen mit Zertifikaten      |Ja |Ja|
|Erstellen von Dienstprinzipalen mit Geheimnissen (Schlüsseln)    |Ja |Ja|
|Anwendungen können den Graph-Dienst verwenden           |Ja |Nein|
|Anwendungen können einen Identitätsanbieter zum Anmelden verwenden |Ja |Ja (für Anwendungen muss ein Verbund mit lokalen AD FS-Instanzen eingerichtet werden) |
| Verwaltete Systemidentitäten | Nein | Nein |

## <a name="topologies"></a>Topologien

In den folgenden Abschnitten werden die verschiedenen Identitätstopologien behandelt, die Sie verwenden können.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: Topologie mit einem einzelnen Mandanten

Bei der Installation von Azure Stack Hub und der Verwendung von Azure AD nutzt Azure Stack Hub standardmäßig eine Topologie mit nur einem Mandanten.

Eine Topologie mit nur einem Mandanten ist geeignet, wenn Folgendes gilt:
- Alle Benutzer sind Teil desselben Mandanten.
- Ein Dienstanbieter hostet eine Azure Stack Hub-Instanz für eine Organisation.

![Azure Stack Hub-Topologie mit einem einzelnen Mandanten und Azure AD](media/azure-stack-identity-architecture/single-tenant.svg)

Merkmale dieser Topologie:

- Azure Stack Hub registriert alle Apps und Dienste im selben Azure AD-Mandantenverzeichnis.
- Azure Stack Hub authentifiziert nur die Benutzer und Apps aus diesem Verzeichnis (einschließlich Token).
- Identitäten für Administratoren (Cloudbetreiber) und Mandantenbenutzer befinden sich unter demselben Verzeichnismandanten.
- Damit ein Benutzer aus einem anderen Verzeichnis auf diese Azure Stack Hub-Umgebung zugreifen kann, müssen Sie diesen [Benutzer als Gast in das Mandantenverzeichnis einladen](azure-stack-identity-overview.md#guest-users).

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: mehrinstanzenfähige Topologie

Cloudoperatoren können Azure Stack Hub so konfigurieren, dass der Zugriff auf Apps durch Mandanten einer oder mehrerer Organisationen zulässig ist. Benutzer greifen über das Azure Stack Hub-Benutzerportal auf Apps zu. In dieser Konfiguration ist das Administratorportal (vom Cloudbetreiber genutzt) auf Benutzer aus einem bestimmten Verzeichnis beschränkt.

Eine Topologie mit mehreren Mandanten ist geeignet, wenn Folgendes gilt:

- Ein Dienstanbieter möchte für Benutzer mehrerer Organisationen den Zugriff auf Azure Stack Hub zulassen.

![Azure Stack Hub-Topologie mit mehreren Mandanten und Azure AD](media/azure-stack-identity-architecture/multi-tenant.svg)

Merkmale dieser Topologie:

- Der Zugriff auf Ressourcen sollte pro Organisation erfolgen.
- Den Benutzern einer Organisation sollte es nicht möglich sein, Benutzern außerhalb ihrer Organisation Zugriff auf Ressourcen zu gewähren.
- Identitäten für Administratoren (Cloudbetreiber) können sich in einem anderen Verzeichnismandanten befinden als die Identitäten für Benutzer. Diese Trennung sorgt auf Identitätsanbieterebene für Kontoisolation.
 
### <a name="ad-fs"></a>AD FS

Die AD FS-Topologie ist erforderlich, wenn eine der folgenden Bedingungen zutrifft:

- Azure Stack Hub stellt keine Verbindung mit dem Internet her.
- Azure Stack Hub kann eine Verbindung mit dem Internet herstellen, aber Sie entscheiden sich für AD FS als Identitätsanbieter.
  
![Azure Stack Hub-Topologie mit AD FS](media/azure-stack-identity-architecture/adfs.svg)

Merkmale dieser Topologie:

- Um die Verwendung dieser Topologie in einer Produktionsumgebung zu unterstützen, müssen Sie die integrierte Azure Stack Hub-AD FS-Instanz über eine Verbundvertrauensstellung in eine vorhandene AD FS-Instanz integrieren, die auf Active Directory basiert.
- Sie können den Graph-Dienst in Azure Stack Hub in Ihre vorhandene Active Directory-Instanz integrieren. Sie können auch den OData-basierten Graph-API-Dienst verwenden, der mit der Azure AD-Graph-API konsistente APIs unterstützt.

  Für die Interaktion mit Ihrer Active Directory-Instanz benötigt die Graph-API Benutzeranmeldeinformationen mit Leseberechtigungen aus Ihrer Active Directory-Instanz.
  - Die integrierte AD FS-Instanz basiert auf Windows Server 2016.
  - Die AD FS- und die Active Directory-Instanz müssen mindestens auf Windows Server 2012 basieren.
  
  Zwischen Ihrer Active Directory-Instanz und der integrierten AD FS-Instanz sind Interaktionen nicht auf OpenID Connect beschränkt, und es können alle gegenseitig unterstützten Protokolle verwendet werden.
  - Benutzerkonten werden in Ihrer lokalen Active Directory-Instanz erstellt und verwaltet.
  - Dienstprinzipale und Registrierungen für Apps werden in der integrierten Active Directory-Instanz verwaltet.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Identitäten](azure-stack-identity-overview.md)
- [Azure Stack-Datencenterintegration: Identität](azure-stack-integrate-identity.md)
