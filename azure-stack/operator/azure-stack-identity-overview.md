---
title: Übersicht über Identitätsanbieter für Azure Stack Hub
description: Es werden die Identitätsanbieter beschrieben, die Sie mit Azure Stack Hub verwenden können.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: c9a01d4aaa437549177f6e32c10f4600287732a7
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81244127"
---
# <a name="overview-of-identity-providers-for-azure-stack-hub"></a>Übersicht über Identitätsanbieter für Azure Stack Hub

Für Azure Stack Hub ist Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) mit Unterstützung durch Active Directory als Identitätsanbieter erforderlich. Die Wahl eines Anbieters ist eine einmalige Entscheidung, die Sie bei der ersten Bereitstellung von Azure Stack Hub treffen. Die Konzepte und Autorisierungsdetails in diesem Artikel können Ihnen bei der Wahl des Identitätsanbieters als Hilfe dienen.

Die Wahl von Azure AD oder AD FS wird durch den Modus beeinflusst, in dem Sie Azure Stack Hub bereitstellen:

- Wenn Sie die Bereitstellung im Modus „Verbunden“ durchführen, können Sie entweder Azure AD oder AD FS verwenden.
- Bei der Bereitstellung im nicht verbundenen Modus ohne Internetverbindung wird nur AD FS unterstützt.

Weitere Informationen zu Ihren Optionen, die von Ihrer Azure Stack Hub-Umgebung abhängen, finden Sie in den folgenden Artikeln:

- Azure Stack Hub-Bereitstellungskit: [Überlegungen zur Identität](azure-stack-datacenter-integration.md#identity-considerations)
- Integrierte Azure Stack Hub-Systeme: [Planungsentscheidungen zu mit Azure verbundenen Bereitstellungen für in Azure Stack Hub integrierte Systeme](azure-stack-connection-models.md)

## <a name="common-concepts-for-identity-providers"></a>Häufige Konzepte für Identitätsanbieter

In den nächsten Abschnitten werden häufige Konzepte für Identitätsanbieter und deren Nutzung in Azure Stack Hub beschrieben.

![Terminologie für Identitätsanbieter](media/azure-stack-identity-overview/terminology.svg)

### <a name="directory-tenants-and-organizations"></a>Verzeichnismandanten und Organisationen

Ein Verzeichnis ist ein Container mit Informationen zu *Benutzern*, *Anwendungen*, *Gruppen* und *Dienstprinzipalen*.

Ein Verzeichnismandant ist eine *Organisation*, z.B. Microsoft oder Ihr eigenes Unternehmen.

- Azure AD unterstützt mehrere Mandanten und kann, jeweils in einem eigenen Verzeichnis, auch mehrere Organisationen unterstützen. Wenn Sie Azure AD verwenden und über mehrere Mandanten verfügen, können Sie Apps und Benutzern eines Mandanten Zugriff auf andere Mandanten desselben Verzeichnisses gewähren.
- AD FS unterstützt nur einen Mandanten und somit auch nur eine Organisation.

### <a name="users-and-groups"></a>Benutzer und Gruppen

Benutzerkonten (Identitäten) sind Standardkonten, die Einzelpersonen per Benutzer-ID und Kennwort authentifizieren. Gruppen können Benutzer oder andere Gruppen enthalten.

Die Erstellung und Verwaltung von Benutzern und Gruppen hängt von der Identitätslösung ab, die Sie verwenden.

In Azure Stack Hub gilt für Benutzerkonten Folgendes:

- Sie werden im Format *Benutzername\@Domäne* erstellt. AD FS ordnet Benutzerkonten zwar einer Active Directory-Instanz zu, aber für AD FS wird die Verwendung des Formats *\\\<Domäne>\\\<Alias>* nicht unterstützt.
- Sie können für die Verwendung der mehrstufigen Authentifizierung konfiguriert werden.
- Sie sind auf das Verzeichnis beschränkt, für das sie zuerst registriert werden. Dies ist das Organisationsverzeichnis.
- Sie können aus Ihren lokalen Verzeichnissen importiert werden. Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Für die Anmeldung beim Benutzerportal Ihrer Organisation verwenden Sie die URL *https:\//portal.local.azurestack.external*. Wenn Sie sich beim Azure Stack Hub-Portal nicht über die Domäne anmelden, die zum Registrieren für Azure Stack Hub verwendet wurde, sondern über andere Domänen, muss der Domänenname für die Registrierung bei Azure Stack Hub an die Portal-URL angefügt werden. Wenn also beispielsweise Azure Stack Hub mit „fabrikam.onmicrosoft.com“ registriert wurde und für die Anmeldung das Benutzerkonto admin@contoso.com verwendet wird, lautet die URL für die Anmeldung beim Benutzerportal wie folgt: „https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com“.

### <a name="guest-users"></a>Gastbenutzer

Gastbenutzer sind Benutzerkonten anderer Verzeichnismandanten, denen Zugriff auf die Ressourcen in Ihrem Verzeichnis gewährt wurde. Zu Unterstützung von Gastbenutzern verwenden Sie Azure AD und aktivieren die Unterstützung für Mehrinstanzenfähigkeit. Wenn die Unterstützung aktiviert ist, können Sie Gastbenutzer zum Zugreifen auf Ressourcen in Ihrem Verzeichnismandanten einladen, wodurch wiederum die Zusammenarbeit mit externen Organisationen ermöglicht wird.

Zum Einladen von Gastbenutzern können Cloudbetreiber und -benutzer die [Azure AD B2B-Zusammenarbeit](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) verwenden. Eingeladene Benutzer erhalten Zugriff auf Dokumente, Ressourcen und Apps aus Ihrem Verzeichnis, und Sie behalten die Kontrolle über Ihre eigenen Ressourcen und Daten.

Als Gastbenutzer können Sie sich am Verzeichnismandanten einer anderen Organisation anmelden. Hierzu hängen Sie den Verzeichnisnamen der Organisation an die Portal-URL an. Wenn Sie also beispielsweise der Organisation Contoso angehören und sich beim Fabrikam-Verzeichnis anmelden möchten, verwenden Sie „https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com“.

### <a name="apps"></a>Apps

Sie können Apps für Azure AD oder AD FS registrieren und die Apps dann Benutzern in Ihrer Organisation anbieten.

Folgende Arten von Apps sind verfügbar:

- **Web-Apps**: Beispiele hierfür sind das Azure-Portal und Azure Resource Manager. Sie unterstützen Web-API-Aufrufe.
- **Nativer Client:** Beispiele hierfür sind Azure PowerShell, Visual Studio und die Azure CLI.

Apps können zwei Arten von Mandanten unterstützen:

- **Ein Mandant:** Unterstützt Benutzer und Dienste nur für das Verzeichnis, unter dem die App registriert ist.

  > [!NOTE]
  > Da AD FS nur ein einzelnes Verzeichnis unterstützt, sind Apps, die Sie in einer AD FS-Topologie erstellen, entsprechend immer Apps mit nur einem Mandanten.

- **Mehrere Mandanten** (Mehrinstanzenfähigkeit): Hierbei wird die Nutzung durch Benutzer und Dienste sowohl für das Verzeichnis, unter dem die App registriert ist, als auch für weitere Mandantenverzeichnisse unterstützt. Bei mehrinstanzenfähigen Apps können sich Benutzer eines anderen Mandantenverzeichnisses (anderer Azure AD-Mandant) an Ihrer App anmelden.

  Weitere Informationen zur Mehrinstanzenfähigkeit finden Sie unter [Aktivieren der Mehrinstanzenfähigkeit in Azure Stack](azure-stack-enable-multitenancy.md).

  Weitere Informationen zum Entwickeln einer mehrinstanzenfähigen App finden Sie unter [Anmelden von Azure Active Directory-Benutzern (AD) mit dem mehrinstanzenfähigen Anwendungsmuster](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Beim Registrieren einer App erstellen Sie zwei Objekte:

- **Anwendungsobjekt:** Die globale Darstellung der App über alle Mandanten hinweg. Dies ist eine 1:1-Beziehung mit der Software-App, die nur in dem Verzeichnis vorhanden ist, in dem die App zuerst registriert wurde.

- **Dienstprinzipalobjekt:** Anmeldeinformationen, die für eine App in dem Verzeichnis erstellt wurden, in dem diese zuerst registriert wurde. Ein Dienstprinzipal wird auch in dem Verzeichnis jedes zusätzlichen Mandanten erstellt, in dem diese App verwendet wird. Dies kann eine 1:n-Beziehung mit der Software-App sein.

Weitere Informationen zu App- und Dienstprinzipalobjekten finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Dienstprinzipale

Ein Dienstprinzipal umfasst einen Satz mit *Anmeldeinformationen* für eine App oder einen Dienst zum Gewähren von Zugriff auf Ressourcen in Azure Stack Hub. Bei Verwendung eines Dienstprinzipals werden die App-Berechtigungen von den Berechtigungen des Benutzers der App getrennt.

In jedem Mandanten, in dem die App genutzt wird, wird ein Dienstprinzipal erstellt. Der Dienstprinzipal richtet eine Identität für die Anmeldung und den Zugriff auf Ressourcen (z.B. Benutzer) ein, die durch diesen Mandanten geschützt sind.

- Eine App mit nur einem Mandanten verfügt nur in dem Verzeichnis, in dem sie zuerst erstellt wurde, über einen Dienstprinzipal. Dieser Dienstprinzipal wird während der Registrierung der App erstellt, und die Zustimmung für die Verwendung wird eingerichtet.
- Eine Web-App oder API mit mehreren Mandanten verfügt über einen Dienstprinzipal, der in jedem Mandanten erstellt wird, in dem ein Benutzer des Mandanten der App-Nutzung zugestimmt hat.

Bei Anmeldeinformationen für Dienstprinzipale kann es sich entweder um einen Schlüssel, der über das Azure Portal generiert wird, oder um ein Zertifikat handeln. Die Nutzung eines Zertifikats ist für die Automatisierung geeignet, da Zertifikate im Vergleich zu Schlüsseln als sicherer anzusehen sind.

> [!NOTE]
> Wenn Sie AD FS mit Azure Stack Hub verwenden, kann nur der Administrator Dienstprinzipale erstellen. Bei AD FS sind für Dienstprinzipale Zertifikate erforderlich, die über den privilegierten Endpunkt (PEP) erstellt werden. Weitere Informationen finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](azure-stack-create-service-principals.md).

Weitere Informationen zu Dienstprinzipalen für Azure Stack Hub finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](azure-stack-create-service-principals.md).

### <a name="services"></a>Dienste

Dienste in Azure Stack Hub, die mit dem Identitätsanbieter interagieren, werden als Apps beim Identitätsanbieter registriert. Wie bei Apps auch, wird für Dienste durch die Registrierung die Authentifizierung beim Identitätssystem ermöglicht.

Für alle Azure-Dienste werden [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code)-Protokolle und [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) genutzt, um die Identität einzurichten. Da Protokolle von Azure AD und AD FS einheitlich genutzt werden, können Sie die [Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) von Azure verwenden, um die Authentifizierung lokal oder in Azure (in einem verbundenen Szenario) durchzuführen. Mit ADAL können Sie auch Tools wie Azure PowerShell und die Azure CLI für die cloudübergreifende und lokale Ressourcenverwaltung verwenden.

### <a name="identities-and-your-identity-system"></a>Identitäten und Ihr Identitätssystem

Zu den Identitäten für Azure Stack Hub gehören Benutzerkonten, Gruppen und Dienstprinzipale.

Bei der Installation von Azure Stack Hub werden mehrere integrierte Apps und Dienste automatisch bei Ihrem Identitätsanbieter im Verzeichnismandanten registriert. Einige Dienste, die registriert werden, werden für die Verwaltung verwendet. Andere Dienste sind für Benutzer verfügbar. Über die Standardregistrierungen werden für Kerndienste Identitäten bereitgestellt, die sowohl untereinander als auch mit später hinzugefügten Identitäten interagieren können.

Wenn Sie Azure AD mit Mehrinstanzenfähigkeit einrichten, werden einige Apps auf die neuen Verzeichnisse verteilt.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

### <a name="authentication-by-apps-and-users"></a>Authentifizierung durch Apps und Benutzer

![Identität zwischen Ebenen von Azure Stack Hub](media/azure-stack-identity-overview/identity-layers.svg)

Für Apps und Benutzer wird die Architektur von Azure Stack Hub anhand von vier Ebenen beschrieben. Für Interaktionen zwischen den einzelnen Ebenen können unterschiedliche Authentifizierungsarten verwendet werden.

|Ebene    |Authentifizierung zwischen Ebenen  |
|---------|---------|
|Tools und Clients, z. B. das Administratorportal     | Zum Zugreifen auf oder Ändern einer Ressource in Azure Stack Hub nutzen Tools und Clients [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims), um einen Aufruf von Azure Resource Manager durchzuführen. <br>Azure Resource Manager überprüft das JSON Web Token und sieht sich die *Ansprüche* im ausgestellten Token an, um die Autorisierungsebene einzuschätzen, die für den Benutzer oder den Dienstprinzipal in Azure Stack Hub besteht. |
|Azure Resource Manager und seine Kerndienste     |Azure Resource Manager kommuniziert mit Ressourcenanbietern, um die Kommunikation von Benutzern zu übertragen. <br> Für Übertragungen werden *direkte imperative* Aufrufe oder *deklarative* Aufrufe über [Azure Resource Manager-Vorlagen](/azure-stack/user/azure-stack-arm-templates) verwendet.|
|Ressourcenanbieter     |Aufrufe, die an Ressourcenanbieter übergeben werden, sind per zertifikatbasierter Authentifizierung geschützt. <br>Azure Resource Manager und der Ressourcenanbieter kommunizieren dann über eine API. Für jeden Aufruf, der vom Azure Resource Manager eingeht, überprüft der Ressourcenanbieter den Aufruf mit diesem Zertifikat.|
|Infrastruktur und Geschäftslogik     |Ressourcenanbieter kommunizieren mit der Geschäftslogik und der Infrastruktur, indem ein Authentifizierungsmodus ihrer Wahl verwendet wird. Für die Standardressourcenanbieter von Azure Stack Hub wird die Windows-Authentifizierung verwendet, um die Kommunikation zu schützen.|

![Für Authentifizierung benötigte Informationen](media/azure-stack-identity-overview/authentication.svg)

### <a name="authenticate-to-azure-resource-manager"></a>Authentifizierung bei Azure Resource Manager

Sie müssen über die folgenden Informationen verfügen, um die Authentifizierung mit dem Identitätsanbieter durchzuführen und ein JSON Web Token zu erhalten:

1. **URL für das Identitätssystem (Autorität):** Die URL, unter der Ihr Identitätsanbieter erreichbar ist. Beispiel: *https:\//login.windows.net*.
2. **App-ID-URI für Azure Resource Manager:** Der eindeutige Bezeichner für Azure Resource Manager, der bei Ihrem Identitätsanbieter registriert ist. Außerdem ist er für jede Azure Stack Hub-Installation eindeutig.
3. **Anmeldeinformationen**: Die Anmeldeinformationen, die Sie zum Authentifizieren beim Identitätsanbieter verwenden
4. **URL für Azure Resource Manager:** Die URL gibt den Speicherort des Azure Resource Manager-Diensts an. Beispiel: *https:\//management.azure.com* oder *https:\//management.local.azurestack.external*.

Wenn ein Prinzipal (Client, Apps oder Benutzer) eine Authentifizierungsanforderung zum Zugreifen auf eine Ressource sendet, muss diese Folgendes enthalten:

- Die Anmeldeinformationen des Prinzipals.
- Der App-ID-URI der Ressource, auf die der Prinzipal zugreifen möchte.

Die Anmeldeinformationen werden vom Identitätsanbieter überprüft. Der Identitätsanbieter überprüft auch, ob der App-ID-URI für eine registrierte App bestimmt ist und ob der Prinzipal über die richtigen Berechtigungen zum Abrufen eines Tokens für die Ressource verfügt. Wenn die Anforderung gültig ist, wird ein JSON Web Token gewährt.

Das Token muss dann den Header einer Anforderung an Azure Resource Manager übergeben. Azure Resource Manager führt Folgendes durch (ohne feste Reihenfolge):

- Überprüft den Anspruch *issuer* (iss), um zu bestätigen, dass das Token vom richtigen Identitätsanbieter stammt.
- Überprüft den Anspruch *audience* (aud), um zu bestätigen, dass das Token für Azure Resource Manager ausgestellt wurde.
- Überprüft, ob das JSON Web Token mit einem Zertifikat signiert ist, das per OpenID konfiguriert wurde und das Azure Resource Manager bekannt ist.
- Überprüft die Ansprüche *issued at* (iat) und *expiry* (exp), um zu bestätigen, dass das Token aktiv ist und akzeptiert werden kann.

Nachdem alle Überprüfungen abgeschlossen sind, verwendet der Azure Resource Manager die Ansprüche *object id* (oid) und *groups*, um eine Liste mit Ressourcen zu erstellen, auf die der Prinzipal zugreifen kann.

![Diagramm des Protokolls für den Tokenaustausch](media/azure-stack-identity-overview/token-exchange.svg)

> [!NOTE]
> Nach der Bereitstellung ist die globale Azure Active Directory-Administratorberechtigung nicht erforderlich. Für einige Vorgänge werden aber ggf. die Anmeldeinformationen des globalen Administrators benötigt (z. B. für ein Ressourcenanbieter-Installationsskript oder zum Gewähren einer Berechtigung für ein neues Feature). Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.

### <a name="use-role-based-access-control"></a>Verwenden der rollenbasierten Zugriffssteuerung

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure Stack Hub ist mit der Implementierung in Microsoft Azure konsistent. Sie können den Zugriff auf Ressourcen verwalten, indem Sie Benutzern, Gruppen und Apps die jeweils geeignete RBAC-Rolle zuweisen. Informationen zur Verwendung von RBAC mit Azure Stack Hub finden Sie in den folgenden Artikeln:

- [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](/azure/role-based-access-control/overview)
- [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](/azure/role-based-access-control/role-assignments-portal)
- [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffssteuerung in Azure](/azure/role-based-access-control/custom-roles)
- [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md) in Azure Stack Hub

### <a name="authenticate-with-azure-powershell"></a>Authentifizieren mit Azure PowerShell

Ausführliche Informationen zur Verwendung von Azure PowerShell für die Authentifizierung bei Azure Stack Hub finden Sie unter [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](../user/azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Authentifizieren mit der Azure CLI

Informationen zur Verwendung von Azure PowerShell für die Authentifizierung bei Azure Stack Hub finden Sie unter [Installieren und Konfigurieren der Azure CLI für die Verwendung mit Azure Stack Hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="next-steps"></a>Nächste Schritte

- [Identitätsarchitektur für Azure Stack](azure-stack-identity-architecture.md)
- [Azure Stack-Datencenterintegration: Identität](azure-stack-integrate-identity.md)
