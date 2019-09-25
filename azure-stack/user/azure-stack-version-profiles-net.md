---
title: Verwenden von API-Versionsprofilen mit .NET in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie API-Versionsprofile mit .NET SDK in Azure Stack verwenden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 6bb5c0cf6b5e4f2ddf60a19734a9e7c63df5d329
ms.sourcegitcommit: 95f30e32e5441599790d39542ff02ba90e70f9d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71070260"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Verwenden von API-Versionsprofilen mit .NET in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Das .NET SDK für Azure Stack Resource Manager umfasst Tools zum Erstellen und Verwalten Ihrer Infrastruktur. Zu den Ressourcenanbietern im SDK zählen Compute, Networking, Storage, App Services und [Key Vault](/azure/key-vault/key-vault-whatis). Das .NET SDK umfasst 14 NuGet-Pakete. Sie müssen diese Pakete jedes Mal, wenn Sie das Projekt kompilieren, in Ihre Projektmappe herunterladen. Sie können aber speziell den Ressourcenanbieter herunterladen, den Sie für das Profil **2019-03-01-hybrid** oder **2018-03-01-hybrid** verwenden möchten, um den Arbeitsspeicher für Ihre App zu optimieren. Jedes Paket besteht aus einem Ressourcenanbieter, der entsprechenden API-Version und dem API-Profil, zu dem es gehört. API-Profile im .NET SDK ermöglichen die Entwicklung einer Hybrid Cloud, indem sie das Wechseln zwischen globalen Azure-Ressourcen und Ressourcen in Azure Stack leicht machen.

## <a name="net-and-api-version-profiles"></a>.NET und API-Versionsprofile

Ein API-Profil ist eine Kombination aus Ressourcenanbietern und API-Versionen. Verwenden Sie ein API-Profil, um die aktuelle, stabilste Version der einzelnen Ressourcentypen in einem Ressourcenanbieterpaket abzurufen.

- Verwenden Sie das **aktuelle** Profil von Paketen, um die aktuellen Versionen aller Dienste zu nutzen. Dieses Profil ist Teil des NuGet-Pakets **Microsoft.Azure.Management**.

- Verwenden Sie eines der folgenden Pakete, um die mit Azure Stack kompatiblen Dienste zu nutzen:
  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg** 
  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  Stellen Sie sicher, dass der **ResourceProvider**-Teil des NuGet-Pakets oben in den richtigen Anbieter geändert wird.

- Verwenden Sie das **aktuelle** Profil des bestimmten NuGet-Pakets, um die aktuelle API-Version eines Diensts zu nutzen. Wenn Sie beispielsweise nur die **aktuelle API-Version** des Computediensts nutzen möchten, verwenden Sie das **aktuelle** Profil des **Compute**-Pakets. Das **aktuelle** Profil ist Teil des NuGet-Pakets **Microsoft.Azure.Management**.

- Zum Verwenden von bestimmten API-Versionen für einen Ressourcentyp in einen bestimmten Ressourcenanbieter verwenden Sie die im Paket definierten API-Versionen.

Sie können alle Optionen in derselben Anwendung kombinieren.

## <a name="install-the-azure-net-sdk"></a>Installieren des Azure .NET SDK

- Installieren Sie Git. Anweisungen hierzu finden Sie unter [Erste Schritte – Installieren von Git][] (Erste Schritte: Installieren von Git).

- Informationen zum Installieren der richtigen NuGet-Pakete finden Sie unter [Suchen und Installieren eines Pakets][].

- Welche Pakete installiert werden müssen, hängt von der Profilversion ab, die Sie verwenden möchten. Die Paketnamen für die Profilversionen sind:

   - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

   - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

- Zum Installieren der richtigen NuGet-Pakete für Visual Studio Code, rufen Sie den folgenden Link zum Herunterladen der [NuGet-Paket-Manager-Anweisungen][] auf.

- Erstellen Sie ein Abonnement, wenn keins verfügbar ist, und speichern Sie die Abonnement-ID zur späteren Verwendung. Informationen zum Erstellen eines Abonnements finden Sie unter [Erstellen von Abonnements für Angebote in Azure Stack][].

- Erstellen Sie einen Dienstprinzipal, und speichern Sie die Client-ID und das Clientgeheimnis. Informationen zum Erstellen eines Dienstprinzipals für Azure Stack finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack][]. Die Client-ID wird beim Erstellen eines Dienstprinzipals auch als „Anwendungs-ID“ bezeichnet.

- Stellen Sie sicher, dass Ihr Dienstprinzipal über die Rolle „Mitwirkender“ bzw. „Besitzer“ für Ihr Abonnement verfügt. Informationen zum Zuweisen einer Rolle zum Dienstprinzipal finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack][].

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung des .NET Azure SDK mit Azure Stack müssen Sie die folgenden Werte angeben und dann die Werte mit Umgebungsvariablen festlegen. Befolgen Sie die Anweisungen in der Tabelle für Ihr Betriebssystem, um die Umgebungsvariablen festzulegen.

| Wert                     | Umgebungsvariablen   | BESCHREIBUNG                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Mandanten-ID                 | `AZURE_TENANT_ID `      | Der Wert Ihrer [*Mandanten-ID*][] für Azure Stack.                                                                          |
| Client-ID                 | `AZURE_CLIENT_ID `      | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Artikels gespeichert wurde. |
| Abonnement-ID           | `AZURE_SUBSCRIPTION_ID` | Mit der [*Abonnement-ID*][] greifen Sie in Azure Stack auf Angebote zu.                                                      |
| Geheimer Clientschlüssel             | `AZURE_CLIENT_SECRET`   | Das App-Geheimnis des Dienstprinzipals, das bei der Erstellung des Dienstprinzipals gespeichert wurde.                                      |
| Resource Manager-Endpunkt | `ARM_ENDPOINT`          | Siehe [*Der Azure Stack-Resource Manager-Endpunkt*][].                                                                    |
| Location                  | `RESOURCE_LOCATION`     | Speicherort von Azure Stack.

Folgen Sie den Anweisungen [in diesem Artikel](../operator/azure-stack-csp-ref-operations.md), um die Mandanten-ID für Ihre Azure Stack-Instanz zu suchen. Zum Festlegen Ihrer Umgebungsvariablen führen Sie folgende Schritte aus:

### <a name="windows"></a>Windows

Verwenden Sie das folgende Format, um die Umgebungsvariablen in der Windows-Eingabeaufforderung zu verwenden:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS-, Linux- und Unix-basierte Systeme

In Unix-basierten Systemen verwenden Sie den folgenden Befehl:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Der Azure Stack-Resource Manager-Endpunkt

Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.

Sie können die Metadateninformationen vom Resource Manager-Endpunkt abrufen. Der Endpunkt gibt eine JSON-Datei mit den zum Ausführen des Codes erforderlichen Informationen zurück.

Beachten Sie die folgenden Überlegungen:

- Der **ResourceManagerUrl**-Wert im Azure Stack Development Kit (ASDK) lautet: https://management.local.azurestack.external/.

- Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.
Zum Abrufen der erforderlichen Metadaten: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

JSON-Beispieldatei:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
         "loginEndpoint": "https://login.windows.net/",
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Vorhandene API-Profile

- **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**: Für Azure Stack erstelltes neuestes Profil. Verwenden Sie dieses Profil für Dienste für die höchste Kompatibilität mit Azure Stack, sofern Sie bei Version 1904 oder höher sind.

- **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**: Verwenden Sie dieses Profil für Dienste, um Kompatibilität mit Azure Stack für Versionen ab 1808 zu erzielen.

- **Aktuell**: Profil, das aus den neuesten Versionen aller Dienste besteht. Verwenden Sie die neuesten Versionen aller Dienste. Dieses Profil ist Teil des NuGet-Pakets **Microsoft.Azure.Management**.

Weitere Informationen zu Azure Stack und API-Profilen finden Sie in der [Zusammenfassung zu API-Profilen][].

## <a name="azure-net-sdk-api-profile-usage"></a>Verwendung des API-Profils aus dem Azure .NET SDK

Verwenden Sie den folgenden Code zum Instanziieren eines Ressourcenverwaltungsclients. Ähnlicher Code kann verwendet werden, um andere Ressourcenanbieterclients (z. B. Compute, Networking und Storage) zu instanziieren.

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Der Parameter `credentials` in diesem Code ist erforderlich, um einen Client zu instanziieren. Der folgende Code generiert ein Authentifizierungstoken aus der Mandanten-ID und dem Dienstprinzipal:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

Der `getActiveDirectoryServiceSettings`-Aufruf im Code ruft Azure Stack-Endpunkte aus dem Metadatenendpunkt ab. Er gibt die Umgebungsvariablen aus dem vorgenommenen Aufruf an:

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```

Diese Schritte ermöglichen Ihnen die Verwendung der NuGet-Pakete des API-Profils zum erfolgreichen Bereitstellen Ihrer Anwendung in Azure Stack.

## <a name="samples-using-api-profiles"></a>Beispiele für die Verwendung von API-Profilen

Sie können die folgenden Beispiele als Referenzen zum Erstellen von Lösungen mit .NET und den Azure Stack-API-Profilen verwenden:

- [Verwalten von Ressourcengruppen](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Verwalten von Speicherkonten](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Verwalten eines virtuellen Computers](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm): In diesem Beispiel wird das von Azure Stack unterstützte Profil **2019-03-01-hybrid** genutzt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu API-Profilen:

- [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md)
- [Von Profilen unterstützte API-Versionen von Ressourcenanbietern](azure-stack-profiles-azure-resource-manager-versions.md)

  [Erste Schritte – Installieren von Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Suchen und Installieren eines Pakets]: /nuget/tools/package-manager-ui
  [NuGet-Paket-Manager-Anweisungen]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Erstellen von Abonnements für Angebote in Azure Stack]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Bereitstellen des Anwendungszugriffs auf Azure Stack]: ../operator/azure-stack-create-service-principals.md
  [*Mandanten-ID*]: ../operator/azure-stack-identity-overview.md
  [*Abonnement-ID*]: ../operator/azure-stack-plan-offer-quota-overview.md#subscriptions
  [*Der Azure Stack-Resource Manager-Endpunkt*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Zusammenfassung zu API-Profilen]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
