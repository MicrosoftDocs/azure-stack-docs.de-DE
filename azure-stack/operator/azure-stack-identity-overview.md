---
title: Übersicht über die Identität für Azure Stack | Microsoft-Dokumentation
description: Es werden die Identitätssysteme beschrieben, die Sie mit Azure Stack verwenden können.
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
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f57ded9df4fe799a5795ee541f7a03e650202aab
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66459055"
---
# <a name="overview-of-identity-for-azure-stack"></a>Übersicht über die Identität für Azure Stack

Für Azure Stack ist Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) mit Unterstützung durch Active Directory als Identitätsanbieter erforderlich. Die Wahl eines Anbieters ist eine einmalige Entscheidung, die Sie bei der ersten Bereitstellung von Azure Stack treffen. Die Konzepte und Autorisierungsdetails in diesem Artikel können Ihnen bei der Wahl des Identitätsanbieters als Hilfe dienen.

Die Wahl von Azure AD oder AD FS wird durch den Modus beeinflusst, in dem Sie Azure Stack bereitstellen:

- Wenn Sie die Bereitstellung im Modus „Verbunden“ durchführen, können Sie entweder Azure AD oder AD FS verwenden.
- Bei der Bereitstellung im nicht verbundenen Modus ohne Internetverbindung wird nur AD FS unterstützt.

Weitere Informationen zu Ihren Optionen, die von Ihrer Azure Stack-Umgebung abhängen, finden Sie in den folgenden Artikeln:

- Azure Stack Deployment Kit: [Überlegungen zur Identität](azure-stack-datacenter-integration.md#identity-considerations)
- Integrierte Azure Stack-Systeme: [Verbindungsmodell von in Azure Stack integrierten Systemen](azure-stack-connection-models.md)

## <a name="common-concepts-for-identity"></a>Häufige Konzepte für die Identität

In den nächsten Abschnitten werden häufige Konzepte für Identitätsanbieter und deren Nutzung in Azure Stack beschrieben.

![Terminologie für Identitätsanbieter](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Verzeichnismandanten und Organisationen

Ein Verzeichnis ist ein Container mit Informationen zu *Benutzern*, *Anwendungen*, *Gruppen* und *Dienstprinzipalen*.

Ein Verzeichnismandant ist eine *Organisation*, z.B. Microsoft oder Ihr eigenes Unternehmen.

- Azure AD unterstützt mehrere Mandanten und kann, jeweils in einem eigenen Verzeichnis, auch mehrere Organisationen unterstützen. Wenn Sie Azure AD verwenden und über mehrere Mandanten verfügen, können Sie Anwendungen und Benutzern eines Mandanten Zugriff auf andere Mandanten desselben Verzeichnisses gewähren.
- AD FS unterstützt nur einen Mandanten und somit auch nur eine Organisation.

### <a name="users-and-groups"></a>Benutzer und Gruppen

Benutzerkonten (Identitäten) sind Standardkonten, die Einzelpersonen per Benutzer-ID und Kennwort authentifizieren. Gruppen können Benutzer oder andere Gruppen enthalten.

Die Erstellung und Verwaltung von Benutzern und Gruppen hängt von der Identitätslösung ab, die Sie verwenden.

In Azure Stack gilt für Benutzerkonten Folgendes:

- Sie werden im Format *Benutzername\@Domäne* erstellt. AD FS ordnet Benutzerkonten zwar einer Active Directory-Instanz zu, aber für AD FS wird die Verwendung des Formats *\\\<Domäne>\\\<Alias>* nicht unterstützt.
- Sie können für die Verwendung der mehrstufigen Authentifizierung konfiguriert werden.
- Sie sind auf das Verzeichnis beschränkt, für das sie zuerst registriert werden. Dies ist das Organisationsverzeichnis.
- Sie können aus Ihren lokalen Verzeichnissen importiert werden. Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Für die Anmeldung beim Mandantenportal Ihrer Organisation verwenden Sie die URL *https:\//portal.local.azurestack.external*. Wenn Sie sich am Azure Stack-Portal nicht über die Domäne anmelden, die zum Registrieren für Azure Stack verwendet wurde, sondern über andere Domänen, muss der Domänenname für die Registrierung bei Azure Stack an die Portal-URL angefügt werden. Wenn also beispielsweise Azure Stack mit „fabrikam.onmicrosoft.com“ registriert wurde und für die Anmeldung das Benutzerkonto admin@contoso.com verwendet wird, lautet die URL für die Anmeldung beim Benutzerportal wie folgt: https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Gastbenutzer

Gastbenutzer sind Benutzerkonten anderer Verzeichnismandanten, denen Zugriff auf die Ressourcen in Ihrem Verzeichnis gewährt wurde. Zu Unterstützung von Gastbenutzern verwenden Sie Azure AD und aktivieren die Unterstützung für Mehrinstanzenfähigkeit. Wenn die Unterstützung aktiviert ist, können Sie Gastbenutzer zum Zugreifen auf Ressourcen in Ihrem Verzeichnismandanten einladen, wodurch wiederum die Zusammenarbeit mit externen Organisationen ermöglicht wird.

Zum Einladen von Gastbenutzern können Cloudbetreiber und -benutzer die [Azure AD B2B-Zusammenarbeit](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) verwenden. Eingeladene Benutzer erhalten Zugriff auf Dokumente, Ressourcen und Anwendungen aus Ihrem Verzeichnis, und Sie behalten die Kontrolle über Ihre eigenen Ressourcen und Daten. 

Als Gastbenutzer können Sie sich am Verzeichnismandanten einer anderen Organisation anmelden. Hierzu hängen Sie den Verzeichnisnamen der Organisation an die Portal-URL an. Wenn Sie also beispielsweise der Organisation Contoso angehören und sich beim Fabrikam-Verzeichnis anmelden möchten, verwenden Sie „https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com“.

### <a name="applications"></a>ANWENDUNGEN

Sie können Anwendungen für Azure AD oder AD FS registrieren und diese dann Benutzern in Ihrer Organisation anbieten.

Dies können diese Arten von Anwendungen sein:

- **Webanwendung:** Beispiele hierfür sind das Azure-Portal und Azure Resource Manager. Sie unterstützen Web-API-Aufrufe.
- **Nativer Client:** Beispiele hierfür sind Azure PowerShell, Visual Studio und die Azure CLI.

Anwendungen können zwei Arten von Mandanten unterstützen:

- **Ein Mandant:** Unterstützt Benutzer und Dienste nur für das Verzeichnis, unter dem die Anwendung registriert ist.

  > [!NOTE]
  > Da AD FS nur ein einzelnes Verzeichnis unterstützt, sind Anwendungen, die Sie in einer AD FS-Topologie erstellen, entsprechend immer Anwendungen mit nur einem Mandanten.

- **Mehrere Mandanten** (Mehrinstanzenfähigkeit): Hierbei wird die Nutzung durch Benutzer und Dienste sowohl für das Verzeichnis, unter dem die Anwendung registriert ist, als auch für weitere Mandantenverzeichnisse unterstützt. Bei mehrinstanzenfähigen Anwendungen können sich Benutzer eines anderen Mandantenverzeichnisses (ein anderer Azure AD-Mandant) an Ihrer Anwendung anmelden. 

  Weitere Informationen zur Mehrinstanzenfähigkeit finden Sie unter [Aktivieren der Mehrinstanzenfähigkeit in Azure Stack](azure-stack-enable-multitenancy.md).

  Weitere Informationen zum Entwickeln einer mehrinstanzenfähigen App finden Sie unter [Anmelden von Azure Active Directory-Benutzern (AD) mit dem mehrinstanzenfähigen Anwendungsmuster](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Beim Registrieren einer Anwendung erstellen Sie zwei Objekte:

- **Anwendungsobjekt:** Die globale Darstellung der Anwendung über alle Mandanten hinweg. Dies ist eine 1:1-Beziehung mit der Softwareanwendung, die nur in dem Verzeichnis vorhanden ist, in dem die Anwendung zuerst registriert wurde.

- **Dienstprinzipalobjekt:** Ein Anmeldeinformationselement, das für eine Anwendung in dem Verzeichnis erstellt wird, in dem die Anwendung zuerst registriert wurde. Ein Dienstprinzipal wird auch in dem Verzeichnis jedes zusätzlichen Mandanten erstellt, in dem diese Anwendung verwendet wird. Dies kann eine 1:n-Beziehung mit der Softwareanwendung sein.

Weitere Informationen zu Anwendungs- und Dienstprinzipalobjekten finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Dienstprinzipale

Ein Dienstprinzipal umfasst einen Satz von *Anmeldeinformationen* für eine Anwendung oder einen Dienst zum Gewähren von Zugriff auf Ressourcen in Azure Stack. Bei der Verwendung eines Dienstprinzipals werden die Anwendungsberechtigungen von den Berechtigungen des Benutzers der Anwendung getrennt.

In jedem Mandanten, in dem die Anwendung genutzt wird, wird ein Dienstprinzipal erstellt. Der Dienstprinzipal richtet eine Identität für die Anmeldung und den Zugriff auf Ressourcen (z.B. Benutzer) ein, die durch diesen Mandanten geschützt sind.

- Eine Anwendung mit nur einem Mandanten verfügt nur in dem Verzeichnis, in dem sie zuerst erstellt wurde, über einen Dienstprinzipal. Dieser Dienstprinzipal wird während der Registrierung der Anwendung erstellt, und die Zustimmung für die Verwendung wird eingerichtet.
- Eine Webanwendung oder API mit mehreren Mandanten verfügt über einen Dienstprinzipal, der in jedem Mandanten erstellt wird, in dem ein Benutzer des Mandanten der Anwendungsnutzung zugestimmt hat.

Bei Anmeldeinformationen für Dienstprinzipale kann es sich entweder um einen Schlüssel, der über das Azure Portal generiert wird, oder um ein Zertifikat handeln. Die Nutzung eines Zertifikats ist für die Automatisierung geeignet, da Zertifikate im Vergleich zu Schlüsseln als sicherer anzusehen sind. 

> [!NOTE]
> Wenn Sie AD FS mit Azure Stack verwenden, kann nur der Administrator Dienstprinzipale erstellen. Bei AD FS sind für Dienstprinzipale Zertifikate erforderlich, die über den privilegierten Endpunkt (PEP) erstellt werden. Weitere Informationen finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](azure-stack-create-service-principals.md).

Weitere Informationen zu Dienstprinzipalen für Azure Stack finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](azure-stack-create-service-principals.md).

### <a name="services"></a>Dienste

Dienste in Azure Stack, die mit dem Identitätsanbieter interagieren, werden als Anwendungen beim Identitätsanbieter registriert. Wie bei Anwendungen auch, wird für Dienste durch die Registrierung die Authentifizierung beim Identitätssystem ermöglicht.

Für alle Azure-Dienste werden [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code)-Protokolle und [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) genutzt, um die Identität einzurichten. Da Protokolle von Azure AD und AD FS einheitlich genutzt werden, können Sie die [Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) von Azure verwenden, um die Authentifizierung lokal oder in Azure (in einem verbundenen Szenario) durchzuführen. Mit ADAL können Sie auch Tools wie Azure PowerShell und die Azure CLI für die cloudübergreifende und lokale Ressourcenverwaltung verwenden.

### <a name="identities-and-your-identity-system"></a>Identitäten und Ihr Identitätssystem

Zu den Identitäten für Azure Stack gehören Benutzerkonten, Gruppen und Dienstprinzipale.

Bei der Installation von Azure Stack werden mehrere integrierte Anwendungen und Dienste automatisch bei Ihrem Identitätsanbieter im Verzeichnismandanten registriert. Einige Dienste, die registriert werden, werden für die Verwaltung verwendet. Andere Dienste sind für Benutzer verfügbar. Über die Standardregistrierungen werden für Kerndienste Identitäten bereitgestellt, die sowohl untereinander als auch mit später hinzugefügten Identitäten interagieren können.

Wenn Sie Azure AD mit Mehrinstanzenfähigkeit einrichten, werden einige Anwendungen auf die neuen Verzeichnisse verteilt.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

### <a name="authentication-by-applications-and-users"></a>Authentifizierung von Anwendungen und Benutzern

![Identität zwischen Ebenen von Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

Für Anwendungen und Benutzer wird die Architektur von Azure Stack anhand von vier Ebenen beschrieben. Für Interaktionen zwischen den einzelnen Ebenen können unterschiedliche Authentifizierungsarten verwendet werden.

|Ebene    |Authentifizierung zwischen Ebenen  |
|---------|---------|
|Tools und Clients, z.B. das Admin-Portal     | Zum Zugreifen auf oder Ändern einer Ressource in Azure Stack nutzen Tools und Clients [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims), um einen Aufruf von Azure Resource Manager durchzuführen. <br>Azure Resource Manager überprüft das JSON Web Token und sieht sich die *Ansprüche* im ausgestellten Token an, um die Autorisierungsebene einzuschätzen, die für den Benutzer oder den Dienstprinzipal in Azure Stack besteht. |
|Azure Resource Manager und seine Kerndienste     |Azure Resource Manager kommuniziert mit Ressourcenanbietern, um die Kommunikation von Benutzern zu übertragen. <br> Für Übertragungen werden *direkte imperative* Aufrufe oder *deklarative* Aufrufe über [Azure Resource Manager-Vorlagen](/azure-stack/user/azure-stack-arm-templates) verwendet.|
|Ressourcenanbieter     |Aufrufe, die an Ressourcenanbieter übergeben werden, sind per zertifikatbasierter Authentifizierung geschützt. <br>Azure Resource Manager und der Ressourcenanbieter kommunizieren dann über eine API. Für jeden Aufruf, der vom Azure Resource Manager eingeht, überprüft der Ressourcenanbieter den Aufruf mit diesem Zertifikat.|
|Infrastruktur und Geschäftslogik     |Ressourcenanbieter kommunizieren mit der Geschäftslogik und der Infrastruktur, indem ein Authentifizierungsmodus ihrer Wahl verwendet wird. Für die Standardressourcenanbieter von Azure Stack wird die Windows-Authentifizierung verwendet, um die Kommunikation zu schützen.|

![Für Authentifizierung benötigte Informationen](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Authentifizierung bei Azure Resource Manager

Sie müssen über die folgenden Informationen verfügen, um die Authentifizierung mit dem Identitätsanbieter durchzuführen und ein JSON Web Token zu erhalten:

1. **URL für das Identitätssystem (Autorität):** Die URL, unter der Ihr Identitätsanbieter erreichbar ist. Beispiel: *https:\//login.windows.net*.
2. **App-ID-URI für Azure Resource Manager:** Der eindeutige Bezeichner für Azure Resource Manager, der bei Ihrem Identitätsanbieter registriert ist. Außerdem ist er für jede Azure Stack-Installation eindeutig.
3. **Anmeldeinformationen**: Die Anmeldeinformationen, die Sie zum Authentifizieren beim Identitätsanbieter verwenden
4. **URL für Azure Resource Manager:** Die URL gibt den Speicherort des Azure Resource Manager-Diensts an. Beispiel: *https:\//management.azure.com* oder *https:\//management.local.azurestack.external*.

Wenn ein Prinzipal (Client, Anwendung oder Benutzer) eine Authentifizierungsanforderung zum Zugreifen auf eine Ressource sendet, muss diese Folgendes enthalten:

- Die Anmeldeinformationen des Prinzipals.
- Der App-ID-URI der Ressource, auf die der Prinzipal zugreifen möchte.

Die Anmeldeinformationen werden vom Identitätsanbieter überprüft. Der Identitätsanbieter überprüft auch, ob der App-ID-URI für eine registrierte Anwendung bestimmt ist und ob der Prinzipal über die richtigen Berechtigungen zum Abrufen eines Tokens für die Ressource verfügt. Wenn die Anforderung gültig ist, wird ein JSON Web Token gewährt.

Das Token muss dann den Header einer Anforderung an Azure Resource Manager übergeben. Azure Resource Manager führt Folgendes durch (ohne feste Reihenfolge):

- Überprüft den Anspruch *issuer* (iss), um zu bestätigen, dass das Token vom richtigen Identitätsanbieter stammt.
- Überprüft den Anspruch *audience* (aud), um zu bestätigen, dass das Token für Azure Resource Manager ausgestellt wurde.
- Überprüft, ob das JSON Web Token mit einem Zertifikat signiert ist, das per OpenID konfiguriert wurde und das Azure Resource Manager bekannt ist.
- Überprüft die Ansprüche *issued at* (iat) und *expiry* (exp), um zu bestätigen, dass das Token aktiv ist und akzeptiert werden kann.

Nachdem alle Überprüfungen abgeschlossen sind, verwendet Azure Resource Manager die Ansprüche *objected* (oid) und *groups*, um eine Liste mit Ressourcen zu erstellen, auf die der Prinzipal zugreifen kann.

![Diagramm des Protokolls für den Tokenaustausch](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Nach der Bereitstellung ist die globale Azure Active Directory-Administratorberechtigung nicht erforderlich. Einige Vorgänge erfordern jedoch möglicherweise die Anmeldeinformationen für den globalen Administrator. Beispielsweise ein Ressourcenanbieter-Installationsskript oder ein neues Feature, dem eine Berechtigung erteilt werden muss. Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.

### <a name="use-role-based-access-control"></a>Verwenden der rollenbasierten Zugriffssteuerung

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure Stack ist mit der Implementierung in Microsoft Azure konsistent. Sie können den Zugriff auf Ressourcen verwalten, indem Sie Benutzern, Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle zuweisen.
Informationen zur Verwendung von RBAC mit Azure Stack finden Sie in den folgenden Artikeln:

- [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](/azure/role-based-access-control/overview)
- [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](/azure/role-based-access-control/role-assignments-portal)
- [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffssteuerung in Azure](/azure/role-based-access-control/custom-roles)
- [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md) (in Azure Stack)

### <a name="authenticate-with-azure-powershell"></a>Authentifizieren mit Azure PowerShell

Ausführliche Informationen zur Verwendung von Azure PowerShell für die Authentifizierung bei Azure Stack finden Sie unter [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](../user/azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Authentifizieren mit der Azure CLI

Informationen zur Verwendung von Azure PowerShell für die Authentifizierung bei Azure Stack finden Sie unter [Installieren und Konfigurieren der Azure CLI für die Verwendung mit Azure Stack](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="next-steps"></a>Nächste Schritte

- [Identitätsarchitektur für Azure Stack](azure-stack-identity-architecture.md)
- [Azure Stack-Datencenterintegration: Identität](azure-stack-integrate-identity.md)
