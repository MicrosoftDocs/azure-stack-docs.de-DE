---
title: Ausführen von API-Anforderungen an Azure Stack Hub
description: Erfahren Sie, wie Sie Authentifizierungsinformationen aus Azure abrufen, um API-Anforderungen an Azure Stack Hub zu richten.
author: sethmanheim
ms.topic: article
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 70a1a6e1d2fb4eb6766948a4e02d5072f4e04281
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623318"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack-hub"></a>Ausführen von API-Anforderungen an Azure Stack Hub

Mit den Azure Stack Hub-REST-APIs können Sie Vorgänge wie das Hinzufügen eines virtuellen Computers (VM) zu Ihrer Azure Stack Hub-Cloud automatisieren.

Für die APIs ist die Authentifizierung Ihres Clients beim Microsoft Azure-Anmeldungsendpunkt erforderlich. Der Endpunkt gibt ein Token zurück, das im Header jeder Anforderung verwendet wird, die an die Azure Stack Hub-APIs gesendet wird. Microsoft Azure basiert auf OAuth 2.0.

Dieser Artikel enthält Beispiele, in denen mithilfe des Hilfsprogramms **cURL** Azure Stack Hub-Anforderungen erstellt werden. cURL ist ein Befehlszeilenprogramm mit einer Bibliothek zum Übertragen von Daten. Mit diesen Beispiele wird der Prozess zum Abrufen eines Tokens für den Zugriff auf die Azure Stack Hub-APIs veranschaulicht. Die meisten Programmiersprachen bieten OAuth 2.0-Bibliotheken, die eine stabile Tokenverwaltung aufweisen und Aufgaben wie das Aktualisieren des Tokens abwickeln.

Sehen Sie sich den gesamten Prozess für die Verwendung der Azure Stack Hub-REST-APIs mit einem generischen REST-Client an (z. B. **cURL**), um die zugrunde liegenden Anforderungen besser verstehen zu können und eine Vorstellung davon zu haben, was Sie in einer Antwortnutzlast erwarten können.

In diesem Artikel werden nicht alle verfügbaren Optionen für das Abrufen von Token wie die interaktive Anmeldung oder das Erstellen dedizierter App-IDs untersucht. Informationen zu diesen Themen finden Sie in der [Azure-REST-API-Referenz](/rest/api/).

## <a name="get-a-token-from-azure"></a>Abrufen eines Tokens von Azure

Erstellen Sie einen mit dem Inhaltstyp `x-www-form-urlencoded` formatierten Anforderungstext zum Abrufen eines Zugriffstokens. POSTen Sie Ihre Anforderung an den Endpunkt für Azure-REST-Authentifizierung und -Anmeldung.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

Die **Mandanten-ID** ist eine der Folgenden:

- Ihre Mandantendomäne z.B. `fabrikam.onmicrosoft.com`
- Ihre Mandanten-ID z.B. `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- Standardwert für mandantenunabhängige Schlüssel: `common`

### <a name="post-body"></a>Posttext

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Für jeden Wert:

- **grant_type**:  
   Der Typ des von Ihnen verwendeten Authentifizierungsschemas. In diesem Beispiel ist der Wert `password`.

- **resource**:  
   Die Ressource, auf die das Token zugreift. Sie finden die Ressource durch Abfragen des Azure Stack Hub-Verwaltungsmetadaten-Endpunkts. Betrachten Sie den Abschnitt **audiences**.

- **Azure Stack Hub-Verwaltungsendpunkt**:

   ```bash
   https://management.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Wenn Sie als Administrator versuchen, auf die Mandanten-API zuzugreifen, sollten Sie sicherstellen, dass Sie den Mandantenendpunkt verwenden, z. B. `https://adminmanagement.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-011`.

  Beispielsweise mit dem Azure Stack Development Kit als Endpunkt:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Antwort:

  ```bash
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Beispiel

  ```bash
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

- **client_id**

  Dieser Wert ist mit einem Standardwert hartcodiert:

  ```bash
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternative Optionen sind für bestimmte Szenarien verfügbar:

  | Application | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **username**

  Beispielsweise das Azure Stack Hub-Azure AD-Konto:

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- **password**

  Das Azure Stack Hub-Azure AD-Administratorkennwort.

### <a name="example"></a>Beispiel

Anforderung:

```bash
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Antwort:

```bash
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API-Abfragen

Nachdem Sie das Zugriffstoken abgerufen haben, fügen Sie es Ihren API-Anforderungen jeweils als Header hinzu. Für das Hinzufügen als Header erstellen Sie einen Header vom Typ **Autorisierung** mit dem Wert `Bearer <access token>`. Beispiel:

Anforderung:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Antwort:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL-Struktur und Abfragesyntax

Generischer Anforderungs-URI, besteht aus: `{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **URI-Schema**:  
Der URI gibt das zum Senden der Anforderung verwendete Protokoll an. Zum Beispiel: `http` oder `https`.
- **URI-Host**:  
Der Host gibt den Domänennamen oder die IP-Adresse des Servers an, auf dem der REST-Dienstendpunkt gehostet wird, z.B. `graph.microsoft.com` oder `adminmanagement.local.azurestack.external`.
- **Ressourcenpfad**:  
Der Pfad gibt die Ressource oder Ressourcensammlung an, die mehrere Segmente enthalten kann, die vom Dienst bei der Bestimmung der Auswahl dieser Ressourcen verwendet wird. Beispiel: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` kann verwendet werden, um die Liste der Besitzer einer bestimmten Anwendung innerhalb der Anwendungensammlung abzufragen.
- **Abfragezeichenfolge**:  
Die Zeichenfolge enthält zusätzliche einfache Parameter, z.B. die API-Version oder Ressourcenauswahlkriterien.

## <a name="azure-stack-hub-request-uri-construct"></a>Azure Stack Hub-Anforderungs-URI-Konstrukt

```bash
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI-Syntax

```bash
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Abfrage-URI-Beispiel

```bash
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung der Azure-REST-Endpunkte finden Sie in der [Azure REST-API-Referenz](/rest/api/).
