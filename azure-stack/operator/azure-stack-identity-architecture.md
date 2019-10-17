---
title: Identitätsarchitektur für Azure Stack | Microsoft-Dokumentation
description: Enthält Informationen zur Identitätsarchitektur für Azure Stack und zu den Unterschieden zwischen Azure AD und AD FS.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 364028183445df7e74828605439bfd7b3784f01f
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019408"
---
# <a name="identity-architecture-for-azure-stack"></a>Identitätsarchitektur für Azure Stack

Bei der Auswahl eines Identitätsanbieters zur Verwendung mit Azure Stack ist es wichtig, die wesentlichen Unterschiede zwischen Azure Active Directory (Azure AD) und Active Directory-Verbunddienste (AD FS) zu verstehen.

## <a name="capabilities-and-limitations"></a>Funktionen und Einschränkungen

Die Wahl des Identitätsanbieters schränkt unter Umständen Ihre Optionen ein – einschließlich der Unterstützung mehrerer Mandanten.

|Funktion oder Szenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Verbindung mit dem Internet vorhanden     |Ja       |Optional|
|Unterstützung mehrerer Mandanten     |Ja       |Nein      |
|Elemente im Marketplace anbieten |Ja       |Ja (Nutzung des Tools für die [Marketplace-Offlinesyndikation](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) ist erforderlich)|
|Unterstützung der Active Directory Authentication Library (ADAL) |Ja |Ja|
|Unterstützung von Tools wie Azure-Befehlszeilenschnittstelle, Visual Studio und PowerShell  |Ja |Ja|
|Erstellen von Dienstprinzipalen über das Azure-Portal     |Ja |Nein|
|Erstellen von Dienstprinzipalen mit Zertifikaten      |Ja |Ja|
|Erstellen von Dienstprinzipalen mit Geheimnissen (Schlüsseln)    |Ja |Ja|
|Anwendungen können den Graph-Dienst verwenden           |Ja |Nein|
|Anwendungen können einen Identitätsanbieter zum Anmelden verwenden |Ja |Ja (für Anwendungen muss ein Verbund mit lokalen AD FS-Instanzen eingerichtet werden) |

## <a name="topologies"></a>Topologien

In den folgenden Abschnitten werden die verschiedenen Identitätstopologien behandelt, die Sie verwenden können.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: Topologie mit einem einzelnen Mandanten

Bei der Installation von Azure Stack und der Verwendung von Azure AD wird für Azure Stack standardmäßig eine Topologie mit nur einem Mandanten genutzt.

Eine Topologie mit nur einem Mandanten ist geeignet, wenn Folgendes gilt:
- Alle Benutzer sind Teil desselben Mandanten.
- Ein Dienstanbieter hostet eine Azure Stack-Instanz für eine Organisation.

![Azure Stack-Topologie mit einem einzelnen Mandanten und Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Merkmale dieser Topologie:

- Azure Stack registriert alle Apps und Dienste unter demselben Azure AD-Mandantenverzeichnis.
- Azure Stack authentifiziert nur die Benutzer und Apps aus diesem Verzeichnis (einschließlich Token).
- Identitäten für Administratoren (Cloudbetreiber) und Mandantenbenutzer befinden sich unter demselben Verzeichnismandanten.
- Sie müssen den [Benutzer als Gast in das Mandantenverzeichnis einladen](azure-stack-identity-overview.md#guest-users), damit ein Benutzer aus einem anderen Verzeichnis auf diese Azure Stack-Umgebung zugreifen kann.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: mehrinstanzenfähige Topologie

Cloudbetreiber können Azure Stack so konfigurieren, dass der Zugriff auf Apps mit Mandanten einer oder mehrerer Organisationen zulässig ist. Benutzer greifen über das Azure Stack-Benutzerportal auf Apps zu. In dieser Konfiguration ist das Administratorportal (vom Cloudbetreiber genutzt) auf Benutzer aus einem bestimmten Verzeichnis beschränkt.

Eine Topologie mit mehreren Mandanten ist geeignet, wenn Folgendes gilt:

- Ein Dienstanbieter möchte für Benutzer mehrerer Organisationen den Zugriff auf Azure Stack zulassen.

![Azure Stack-Topologie mit mehreren Mandanten und Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Merkmale dieser Topologie:

- Der Zugriff auf Ressourcen sollte pro Organisation erfolgen.
- Den Benutzern einer Organisation sollte es nicht möglich sein, Benutzern außerhalb ihrer Organisation Zugriff auf Ressourcen zu gewähren.
- Identitäten für Administratoren (Cloudbetreiber) können sich in einem anderen Verzeichnismandanten befinden als die Identitäten für Benutzer. Diese Trennung sorgt auf Identitätsanbieterebene für Kontoisolation.
 
### <a name="ad-fs"></a>AD FS

Die AD FS-Topologie ist erforderlich, wenn eine der folgenden Bedingungen zutrifft:

- Azure Stack stellt keine Verbindung mit dem Internet her.
- Azure Stack kann eine Verbindung mit dem Internet herstellen, aber Sie entscheiden sich für AD FS als Identitätsanbieter.
  
![Azure Stack-Topologie per AD FS](media/azure-stack-identity-architecture/adfs.png)

Merkmale dieser Topologie:

- Um diese Topologie in einer Produktionsumgebung verwenden zu können, müssen Sie die integrierte Azure Stack-AD FS-Instanz über eine Verbundvertrauensstellung in eine vorhandene AD FS-Instanz integrieren, die auf Active Directory basiert.
- Sie können den Graph-Dienst in Azure Stack in Ihre vorhandene Active Directory-Instanz integrieren. Sie können auch den OData-basierten Graph-API-Dienst verwenden, der mit der Azure AD-Graph-API konsistente APIs unterstützt.

  Für die Interaktion mit Ihrer Active Directory-Instanz benötigt die Graph-API Benutzeranmeldeinformationen mit Leseberechtigungen aus Ihrer Active Directory-Instanz.
  - Die integrierte AD FS-Instanz basiert auf Windows Server 2016.
  - Die AD FS- und die Active Directory-Instanz müssen mindestens auf Windows Server 2012 basieren.
  
  Zwischen Ihrer Active Directory-Instanz und der integrierten AD FS-Instanz sind Interaktionen nicht auf OpenID Connect beschränkt, und es können alle gegenseitig unterstützten Protokolle verwendet werden.
  - Benutzerkonten werden in Ihrer lokalen Active Directory-Instanz erstellt und verwaltet.
  - Dienstprinzipale und Registrierungen für Apps werden in der integrierten Active Directory-Instanz verwaltet.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Identitäten](azure-stack-identity-overview.md)
- [Azure Stack-Datencenterintegration: Identität](azure-stack-integrate-identity.md)
