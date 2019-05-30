---
title: Verwenden der Azure Stack-API | Microsoft-Dokumentation
description: Informationen zum Abrufen einer Authentifizierung von Azure, um API-Anforderungen an Azure Stack zu richten
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 22aeab6c6f33462ebea50bafa795630a648e2dd5
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269401"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Verwenden der Azure Stack-API

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Mit der Anwendungsprogrammierschnittstelle (API) können Sie Vorgänge wie das Hinzufügen einer VM zu einer Azure Stack-Cloud automatisieren.

Die API erfordert die Authentifizierung Ihres Clients beim Microsoft Azure-Anmeldungsendpunkt. Der Endpunkt gibt einen Token zurück, das im Header jeder Anforderung verwendet wird, die an die Azure Stack-API gesendet wird. Microsoft Azure basiert auf OAuth 2.0.

Dieser Artikel enthält Beispiele, in denen mithilfe des Hilfsprogramms **cURL** Azure Stack-Anforderungen erstellt werden. Die Anwendung cURL ist ein Befehlszeilenprogramm mit einer Bibliothek zum Übertragen von Daten. Diese Beispiele zeigen eine exemplarische Vorgehensweise zum Abrufen eines Tokens für den Zugriff auf die Azure Stack-API. Die meisten Programmiersprachen bieten OAuth 2.0-Bibliotheken, die eine stabile Tokenverwaltung aufweisen und Aufgaben wie das Aktualisieren des Tokens abwickeln.

Wenn Sie den gesamten Prozess der Verwendung der Azure Stack-REST-API mit einem generischen REST-Client wie z.B. **cURL** überprüfen, können Sie die zugrunde liegenden Anforderungen besser verstehen, und besser einschätzen, was Sie in der Antwortnutzlast erwarten können.

In diesem Artikel werden nicht alle verfügbaren Optionen für das Abrufen von Token wie die interaktive Anmeldung oder das Erstellen dedizierter App-IDs untersucht. Unter [Azure-REST-API-Referenz](https://docs.microsoft.com/rest/api/) erfahren Sie, wie Informationen zu diesen Themen abgerufen werden.

## <a name="get-a-token-from-azure"></a>Abrufen eines Tokens von Azure

Erstellen Sie einen mit dem Inhaltstyp „x-www-form-urlencoded“ formatierten Anforderungstext zum Abrufen eines Zugriffstokens. POSTEN Sie Ihre Anforderung an den Endpunkt für Azure-REST-Authentifizierung und -Anmeldung.

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

- **grant_type**  
   Der Typ des von Ihnen verwendeten Authentifizierungsschemas. In diesem Beispiel ist der Wert `password`.

- **resource**  
   Die Ressource, auf die das Token zugreift. Sie finden die Ressource durch Abfragen des Azure Stack-Verwaltungsmetadaten-Endpunkts. Betrachten Sie den Abschnitt **audiences**.

- **Azure Stack-Verwaltungsendpunkt**  
   ```
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Wenn Sie als Administrator versuchen, auf die Mandanten-API zuzugreifen, müssen Sie darauf achten, den Mandantenendpunkt zu verwenden, z.B. `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`.  

  Beispielsweise mit dem Azure Stack Development Kit als Endpunkt:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Antwort:

  ```
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

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Dieser Wert ist mit einem Standardwert hartcodiert:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternative Optionen sind für bestimmte Szenarien verfügbar:

  
  | Anwendung | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  Beispielsweise das Azure Stack-AAD-Konto:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  Das Azure Stack-AAD-Administratorkennwort.

### <a name="example"></a>Beispiel

Anforderung:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Antwort:

```
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

Nachdem Sie das Zugriffstoken abgerufen haben, müssen Sie es jeder Ihrer API-Anforderungen als Header hinzufügen. Zu diesem Zweck müssen Sie eine Header-**Authorization** mit dem Wert `Bearer <access token>` erstellen. Beispiel: 

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

Generischer Anforderungs-URI, bestehend aus: {URI-Schema} :// {URI-Host} / {Ressourcenpfad}? {Abfragezeichenfolge}

- **URI-Schema**:  
Der URI gibt das zum Senden der Anforderung verwendete Protokoll an. Beispiel: `http` oder `https`.
- **URI-Host**:  
Der Host gibt den Domänennamen oder die IP-Adresse des Servers an, auf dem der REST-Dienstendpunkt gehostet wird, z.B. `graph.microsoft.com` oder `adminmanagement.local.azurestack.external`.
- **Ressourcenpfad**:  
Der Pfad gibt die Ressource oder Ressourcensammlung an, die mehrere Segmente enthalten kann, die vom Dienst bei der Bestimmung der Auswahl dieser Ressourcen verwendet wird. Beispiel: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` kann verwendet werden, um die Liste der Besitzer einer bestimmten Anwendung innerhalb der Anwendungensammlung abzufragen.
- **Abfragezeichenfolge**:  
Die Zeichenfolge enthält zusätzliche einfache Parameter, z.B. die API-Version oder Ressourcenauswahlkriterien.

## <a name="azure-stack-request-uri-construct"></a>Azure Stack-Anforderungs-URI-Konstrukt

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI-Syntax

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Abfrage-URI-Beispiel

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung der Azure-RESTful-Endpunkte finden Sie unter [Azure REST-API-Referenz](https://docs.microsoft.com/rest/api/).
