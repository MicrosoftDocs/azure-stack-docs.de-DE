---
title: Verwenden von API-Versionsprofilen mit Java in Azure Stack Hub
description: Hier erfahren Sie, wie Sie API-Versionsprofile mit Java in Azure Stack Hub verwenden.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: c0be80757eb12daac9d452f79ed7c0f5b7326120
ms.sourcegitcommit: 390eac7abc94cea1405178e8d6a9358f6488f5d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78231637"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack-hub"></a>Verwenden von API-Versionsprofilen mit Java in Azure Stack Hub

Das Java SDK für Azure Stack Hub Resource Manager stellt Tools zum Erstellen und Verwalten Ihrer Infrastruktur bereit. Zu den Ressourcenanbietern im SDK zählen Compute, Networking, Storage, App Services und [Key Vault](/azure/key-vault/key-vault-whatis).

Das Java SDK bindet API-Profile durch Einschließen von Abhängigkeiten in der Datei **Pom.xml** ein, welche die richtigen Module in der **JAVA**-Datei lädt. Sie können jedoch mehrere Profile als Abhängigkeiten hinzufügen, z.B. **2019-03-01-hybrid** oder **Neueste** als Azure-Profil. Bei Verwendung dieser Abhängigkeiten wird das richtige Modul geladen, sodass Sie beim Erstellen Ihres Ressourcentyps auswählen können, welche API-Version aus diesen Profilen verwendet werden soll. So können Sie beim Entwickeln für die aktuellen API-Versionen für Azure Stack Hub die neuesten Versionen in Azure verwenden.

Das Java-SDK ermöglicht eine echte Hybrid Cloud-Entwicklungsumgebung. API-Profile im Java SDK ermöglichen die Entwicklung für die Hybrid Cloud, da Sie zwischen globalen Azure-Ressourcen und Ressourcen in Azure Stack Hub wechseln können.

## <a name="java-and-api-version-profiles"></a>Java und API-Versionsprofile

Ein API-Profil ist eine Kombination aus Ressourcenanbietern und API-Versionen. Verwenden Sie ein API-Profil, um die aktuelle, stabilste Version der einzelnen Ressourcentypen in einem Ressourcenanbieterpaket abzurufen.

- Verwenden Sie das **aktuelle** Profil als Abhängigkeit, um die aktuellen Versionen aller Dienste zu nutzen.

  - Die Abhängigkeit zur Verwendung des aktuellen Profils ist **com.microsoft.azure**.

  - Um die neuesten unterstützten Dienste zu nutzen, die in Azure Stack Hub verfügbar sind, verwenden Sie das Profil **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**.

    - Das Profil wird in der Datei **Pom.xml** als Abhängigkeit angegeben, die Module automatisch lädt, wenn Sie in der Dropdownliste die richtige Klasse auswählen (wie dies auch bei .NET der Fall ist).

  - Abhängigkeiten sehen folgendermaßen aus:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta-1</version>
     </dependency>
     ```

  - Zum Verwenden von bestimmten API-Versionen für einen Ressourcentyp in einen bestimmten Ressourcenanbieter verwenden Sie die über IntelliSense definierten API-Versionen.

Sie können alle Optionen in derselben Anwendung kombinieren.

## <a name="install-the-azure-java-sdk"></a>Installieren des Azure Java-SDK

Führen Sie diese Schritte aus, um das Java SDK zu installieren:

1. Befolgen Sie die offizielle Anleitung zur Installation von Git. Weitere Informationen finden Sie unter [Erste Schritte – Installieren von Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Befolgen Sie die Anleitung zur Installation des [Java SDK](https://zulu.org/download/) und von [Maven](https://maven.apache.org/). Die richtige Version ist Version 8 des Java Developer Kit. Die richtige Version von Apache Maven ist 3.0 oder höher. Damit Sie den Schnellstart durchführen können, muss die Umgebungsvariable `JAVA_HOME` auf den Installationsspeicherort des Java Development Kit festgelegt sein. Weitere Informationen finden Sie unter [Erstellen der ersten Funktion mit Java und Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Zum Installieren der richtigen Abhängigkeitspakete öffnen Sie die Datei **Pom.xml** in Ihrer Java-Anwendung. Fügen Sie eine Abhängigkeit wie im folgenden Code gezeigt hinzu:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta-1</version>
   </dependency>
   ```

4. Welche Paketsammlungen installiert werden müssen, hängt von der Profilversion ab, die Sie verwenden möchten. Die Paketnamen für die Profilversionen sind:

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **Neueste**

5. Erstellen Sie ein Abonnement, wenn keins verfügbar ist, und speichern Sie die Abonnement-ID zur späteren Verwendung. Eine Anleitung zum Erstellen eines Abonnements finden Sie unter [Erstellen von Abonnements für Angebote in Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Erstellen Sie einen Dienstprinzipal, und speichern Sie die Client-ID und das Clientgeheimnis. Eine Anleitung zum Erstellen eines Dienstprinzipals für Azure Stack Hub finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack Hub](../operator/azure-stack-create-service-principals.md). Die Client-ID wird beim Erstellen eines Dienstprinzipals auch als „Anwendungs-ID“ bezeichnet.

7. Stellen Sie sicher, dass Ihr Dienstprinzipal über die Rolle „Mitwirkender“ bzw. „Besitzer“ für Ihr Abonnement verfügt. Eine Anleitung zum Zuweisen einer Rolle zum Dienstprinzipal finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung des Azure Java SDK mit Azure Stack Hub müssen Sie die folgenden Werte angeben und dann Werte mit Umgebungsvariablen festlegen. Befolgen Sie die Anweisungen in der Tabelle für Ihr Betriebssystem, um die Umgebungsvariablen festzulegen.

| value                     | Umgebungsvariablen | BESCHREIBUNG                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mandanten-ID                 | `AZURE_TENANT_ID`            | Ihre [Mandanten-ID](../operator/azure-stack-identity-overview.md) für Azure Stack Hub.                                                          |
| Client-ID                 | `AZURE_CLIENT_ID`             | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt gespeichert wurde.                                                                                              |
| Abonnement-ID           | `AZURE_SUBSCRIPTION_ID`      | Sie verwenden die [Abonnement-ID](../operator/service-plan-offer-subscription-overview.md#subscriptions), um in Azure Stack Hub auf Angebote zuzugreifen.                |
| Geheimer Clientschlüssel             | `AZURE_CLIENT_SECRET`        | Das Geheimnis der Dienstprinzipalanwendung, die bei der Erstellung des Dienstprinzipals gespeichert wurde.                                                                                                                                   |
| Resource Manager-Endpunkt | `ARM_ENDPOINT`              | Informationen hierzu finden Sie im Artikel zum [Resource Manager-Endpunkt von Azure Stack Hub](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint). |
| Location                  | `RESOURCE_LOCATION`    | **Lokal** für Azure Stack Hub.                                                                                                                                                                                                |

Folgen Sie den [hier](../operator/azure-stack-csp-ref-operations.md) beschriebenen Anweisungen, um die Mandanten-ID für Ihre Azure Stack Hub-Instanz zu ermitteln. Verwenden Sie die Verfahren in den folgenden Abschnitten, um Ihre Umgebungsvariablen festzulegen:

### <a name="microsoft-windows"></a>Microsoft Windows

Verwenden Sie das folgende Format, um die Umgebungsvariablen in der Windows-Eingabeaufforderung zu verwenden:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS-, Linux- und Unix-basierte Systeme

In Unix-basierten Systemen verwenden Sie den folgenden Befehl:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub als vertrauenswürdig

Bei Verwendung des Azure Stack Development Kit (ASDK) müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Es ist nicht erforderlich, dass Sie das Zertifizierungsstellen-Stammzertifikat für die integrierten Azure Stack Hub-Systeme als vertrauenswürdig einstufen.

#### <a name="windows"></a>Windows

1. Exportieren Sie das selbstsignierte Azure Stack Hub-Zertifikat auf Ihren Desktop.

1. Navigieren Sie an der Eingabeaufforderung zum Verzeichnis `%JAVA_HOME%\bin`.

1. Führen Sie den folgenden Befehl aus:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Der Resource Manager-Endpunkt von Azure Stack Hub

Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.

Sie können die Metadateninformationen vom Resource Manager-Endpunkt abrufen. Der Endpunkt gibt eine JSON-Datei mit den zum Ausführen des Codes erforderlichen Informationen zurück.

Beachten Sie die folgenden Überlegungen:

- Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/`.

- Die **ResourceManagerUrl** in integrierten Systemen lautet `https://management.region.<fqdn>/`, wobei `<fqdn>` Ihr vollqualifizierter Domänenname ist.

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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Vorhandene API-Profile

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**: Für Azure Stack Hub erstelltes neuestes Profil. Verwenden Sie dieses Profil, um für Dienste die höchste Kompatibilität mit Azure Stack Hub zu erzielen, sofern Sie Version 1904 oder höher nutzen.

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Für Azure Stack Hub erstelltes Profil. Verwenden Sie dieses Profil für Dienste, um Kompatibilität mit Azure Stack Hub Version 1808 oder höher zu erzielen.

- **com.microsoft.azure**: Profil, das aus den neuesten Versionen aller Dienste besteht. Verwenden Sie die neuesten Versionen aller Dienste.

Weitere Informationen zu Azure Stack Hub und API-Profilen finden Sie in der [Zusammenfassung zu API-Profilen](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Verwendung des API-Profils aus dem Azure Java-SDK

Der folgende Code authentifiziert den Dienstprinzipal in Azure Stack Hub. Er erstellt anhand der Mandanten-ID und der Authentifizierungsbasis ein für Azure Stack Hub spezifisches Token:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Dieser Code ermöglicht Ihnen das Verwenden der API-Profilabhängigkeiten zum erfolgreichen Bereitstellen Ihrer App in Azure Stack Hub.

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Definieren von Einstellungsfunktionen für die Azure Stack Hub-Umgebung

Verwenden Sie den folgenden Code, um die Azure Stack Hub-Cloud bei den richtigen Endpunkten zu registrieren:

```java
// Get Azure Stack Hub cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

Mit dem `getActiveDirectorySettings`-Aufruf im obigen Code werden die Endpunkte von den Metadaten-Endpunkten abgerufen. Er gibt die Umgebungsvariablen aus dem vorgenommenen Aufruf an:

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);

        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }

        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>Beispiele für die Verwendung von API-Profilen

Verwenden Sie die folgenden GitHub-Beispiele als Referenzen zum Erstellen von Lösungen mit .NET- und Azure Stack Hub-API-Profilen:

- [Verwalten von Ressourcengruppen](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Verwalten von Speicherkonten](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Verwalten eines virtuellen Computers](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (aktualisiert mit dem Profil „2019-03-01-hybrid“)

### <a name="sample-unit-test-project"></a>Beispiel für Komponententestprojekt

1. Klonen Sie das Repository mit dem folgenden Befehl:

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. Erstellen Sie einen Azure-Dienstprinzipal, und weisen Sie eine Rolle zu, um auf das Abonnement zuzugreifen. Eine Anleitung zur Erstellung eines Dienstprinzipals finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md).

3. Rufen Sie die folgenden erforderlichen Umgebungsvariablen ab:

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. Legen Sie die folgenden Umgebungsvariablen fest, und verwenden Sie dabei die Informationen, die Sie aus dem mithilfe der Eingabeaufforderung erstellten Dienstprinzipal abgerufen haben:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack Hub}`

   Verwenden Sie unter Windows **set** anstelle von **export**.

5. Verwenden Sie die `getActiveDirectorySettings`-Funktion, um die Azure Resource Manager-Metadatenendpunkte abzurufen.

    ```java
    // Get Azure Stack Hub cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. Fügen Sie in der Datei **Pom.xml** die folgende Abhängigkeit hinzu, um das Profil **2019-03-01-hybrid** für Azure Stack Hub zu verwenden. Diese Abhängigkeit installiert die Module, die diesem Profil zugeordnet sind, für die Ressourcenanbieter Compute, Networking, Storage, KeyVault und App Services:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
      <artifactId>azure</artifactId>
      <version>1.0.0-beta-1</version>
    </dependency>
    ```

7. Geben Sie an der Eingabeaufforderung, die zum Festlegen der Umgebungsvariablen geöffnet wurde, den folgenden Befehl ein:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu API-Profilen finden Sie unter:

- [Versionsprofile in Azure Stack Hub](azure-stack-version-profiles.md)
- [Von Profilen unterstützte API-Versionen von Ressourcenanbietern](azure-stack-profiles-azure-resource-manager-versions.md)
