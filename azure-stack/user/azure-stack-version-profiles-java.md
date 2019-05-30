---
title: Verwenden von API-Versionsprofilen mit Java in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie mehr zur Verwendung von API-Versionsprofilen mit Java in Azure Stack.
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
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 44ebb631ca916ed1c5b933517d40a756c987fee0
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269480"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Verwenden von API-Versionsprofilen mit Java in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Das Java-SDK für Azure Stack Resource Manager umfasst Tools zum Erstellen und Verwalten Ihrer Infrastruktur. Zu den Ressourcenanbietern im SDK zählen Compute, Netzwerk, Speicher, App-Dienste und [KeyVault](/azure/key-vault/key-vault-whatis). Das Java-SDK bindet API-Profile durch Einschließen von Abhängigkeiten in der Datei „Pom.xml“ ein, welche die richtigen Module in der JAVA-Datei lädt. Sie können jedoch mehrere Profile als Abhängigkeiten hinzufügen, z.B. **2018-03-01-hybrid** oder **Neueste** als Azure-Profil. Bei Verwendung dieser Abhängigkeiten wird das richtige Modul geladen, sodass Sie beim Erstellen Ihres Ressourcentyps auswählen können, welche API-Version aus diesen Profilen Sie verwenden möchten. Dadurch können Sie beim Entwickeln für die aktuellen API-Versionen für Azure Stack die neuesten Versionen in Azure verwenden. Das Java-SDK ermöglicht eine echte Hybrid Cloud-Entwicklungsumgebung. API-Profile im Java-SDK ermöglichen die Entwicklung einer Hybrid Cloud, indem es Ihnen ermöglicht wird, zwischen globalen Azure-Ressourcen und Ressourcen unter Azure Stack zu wechseln.

## <a name="java-and-api-version-profiles"></a>Java und API-Versionsprofile

Ein API-Profil ist eine Kombination aus Ressourcenanbietern und API-Versionen. Sie können ein API-Profil verwenden, um die aktuelle, stabilste Version der einzelnen Ressourcentypen in einem Ressourcenanbieterpaket abzurufen.

- Verwenden Sie das **aktuelle** Profil als Abhängigkeit, um die aktuellen Versionen aller Dienste zu nutzen.

  - Die Abhängigkeit zur Verwendung des aktuellen Profils ist **com.microsoft.azure**.

  - Zur Verwendung der mit Azure Stack kompatiblen Dienste verwenden Sie das Profil**com.microsoft.azure.profile\_2018\_03\_01\_hybrid**.

    - Dieses wird in der Datei „Pom.xml“ als Abhängigkeit angegeben, die Module automatisch lädt, wenn Sie in der Dropdownliste die richtige Klasse auswählen, wie es auch bei .NET der Fall ist.

    - Der obere Bereich der einzelnen Module sieht folgendermaßen aus: `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`

  - Abhängigkeiten sehen folgendermaßen aus:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Zum Verwenden von bestimmten API-Versionen für einen Ressourcentyp in einen bestimmten Ressourcenanbieter verwenden Sie die über IntelliSense definierten API-Versionen.

Hinweis: Sie können alle Optionen in derselben Anwendung kombinieren.

## <a name="install-the-azure-java-sdk"></a>Installieren des Azure Java-SDK

Führen Sie die folgenden Schritte aus, um das Java-SDK zu installieren:

1. Befolgen Sie die offizielle Anleitung zur Installation von Git. Anweisungen hierzu finden Sie unter [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Erste Schritte: Installieren von Git).

2. Befolgen Sie die Anleitung zur Installation des [Java SDK](https://zulu.org/download/) und von [Maven](https://maven.apache.org/). Die richtige Version ist Version 8 des Java Developer Kit. Die richtige Apache Maven ist Version 3.0 oder höher. Damit Sie den Schnellstart durchführen können, muss die Umgebungsvariable „JAVA_HOME“ auf den Installationsspeicherort des Java Development Kit festgelegt sein. Weitere Informationen finden Sie unter [Erstellen der ersten Funktion mit Java und Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Zum Installieren der richtigen Abhängigkeitspakete öffnen Sie die Datei „Pom.xml“ in Ihrer Java-Anwendung. Fügen Sie eine Abhängigkeit wie im folgenden Code gezeigt hinzu:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Welche Paketsammlungen installiert werden müssen, hängt von der Profilversion ab, die Sie verwenden möchten. Die Paketnamen für die Profilversionen sind:

   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **Neueste**

5. Erstellen Sie ein Abonnement, wenn keins verfügbar ist, und speichern Sie die Abonnement-ID zur späteren Verwendung. Eine Anleitung zum Erstellen eines Abonnements finden Sie unter [Erstellen von Abonnements für Angebote in Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Erstellen Sie einen Dienstprinzipal, und speichern Sie die Client-ID und das Clientgeheimnis. Eine Anleitung zum Erstellen eines Dienstprinzipals für Azure Stack finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md). Hinweis: Die Client-ID wird beim Erstellen eines Dienstprinzipals auch als „Anwendungs-ID“ bezeichnet.

7. Stellen Sie sicher, dass Ihr Dienstprinzipal über die Rolle „Mitwirkender“ bzw. „Besitzer“ für Ihr Abonnement verfügt. Eine Anleitung zum Zuweisen einer Rolle zum Dienstprinzipal finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung des Azure Java-SDK mit Azure Stack müssen Sie die folgenden Werte angeben und dann Werte mit Umgebungsvariablen festlegen. Befolgen Sie die Anweisungen in der Tabelle für Ihr Betriebssystem, um die Umgebungsvariablen festzulegen.

| Wert                     | Umgebungsvariablen | BESCHREIBUNG                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mandanten-ID                 | AZURE_TENANT_ID            | Der Wert Ihrer [Mandanten-ID](../operator/azure-stack-identity-overview.md) für Azure Stack.                                                          |
| Client-ID                 | AZURE_CLIENT_ID             | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt gespeichert wurde.                                                                                              |
| Abonnement-ID           | AZURE_SUBSCRIPTION_ID      | Mit der [Abonnement-ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) greifen Sie in Azure Stack auf Angebote zu.                |
| Geheimer Clientschlüssel             | AZURE_CLIENT_SECRET        | Das Geheimnis der Dienstprinzipalanwendung, die bei der Erstellung des Dienstprinzipals gespeichert wurde.                                                                                                                                   |
| Resource Manager-Endpunkt | ARM_ENDPOINT              | Siehe [Azure Stack-Resource Manager-Endpunkt](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Location                  | RESOURCE_LOCATION    | **Lokal** für Azure Stack.                                                                                                                                                                                                |

Folgen Sie den [hier](../operator/azure-stack-csp-ref-operations.md) beschriebenen Anweisungen, um die Mandanten-ID für Ihre Azure Stack-Instanz zu ermitteln. Zum Festlegen Ihrer Umgebungsvariablen führen Sie folgende Schritte aus:

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

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack als vertrauenswürdig

Bei Verwendung des ASDK müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Für die integrierten Systeme ist dies nicht erforderlich.

#### <a name="windows"></a>Windows

1. Exportieren Sie das selbstsignierte Azure Stack-Zertifikat auf Ihren Desktop.

1. Navigieren Sie in einer Eingabeaufforderung zum Verzeichnis „%JAVA_HOME%\bin“.

1. Führen Sie den folgenden Befehl aus:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Der Azure Stack-Resource Manager-Endpunkt

Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.

Sie können die Metadateninformationen vom Resource Manager-Endpunkt abrufen. Der Endpunkt gibt eine JSON-Datei zurück, die die erforderlichen Informationen zum Ausführen Ihres Codes enthält.

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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Vorhandene API-Profile

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Für Azure Stack erstelltes neuestes Profil. Verwenden Sie dieses Profil für Dienste für die höchste Kompatibilität mit Azure Stack, sofern Sie bei Stempel 1808 oder weiter sind.

- **com.microsoft.azure**: Profil, das aus den neuesten Versionen aller Dienste besteht. Verwenden Sie die neuesten Versionen aller Dienste.

Weitere Informationen zu Azure Stack und API-Profilen finden Sie in der [Zusammenfassung zu API-Profilen](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Verwendung des API-Profils aus dem Azure Java-SDK

Der folgende Code authentifiziert den Dienstprinzipal in Azure Stack. Erstellt anhand der Mandanten-ID und der Authentifizierungsbasis ein für Azure Stack spezifisches Token:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Dies ermöglicht Ihnen die Verwendung der API-Profilabhängigkeiten zum erfolgreichen Bereitstellen Ihrer Anwendung in Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definieren von Einstellungsfunktionen für die Azure Stack-Umgebung

Verwenden Sie den folgenden Code, um die Azure Stack-Cloud bei den richtigen Endpunkten zu registrieren.

```java
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

Der `getActiveDirectorySettings`-Aufruf im folgenden Code ruft die Endpunkte aus den Metadaten-Endpunkten ab. Er gibt die Umgebungsvariablen aus dem vorgenommenen Aufruf an:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Beispiele für die Verwendung von API-Profilen

Sie können die folgenden GitHub-Beispiele als Referenzen zum Erstellen von Lösungen mit .NET- und Azure Stack-API-Profilen verwenden.

- [Verwalten von Ressourcengruppen](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Verwalten von Speicherkonten](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Verwalten eines virtuellen Computers](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Beispiel für Komponententestprojekt

1. Klonen Sie das Repository mit dem folgenden Befehl:

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Erstellen Sie einen Azure-Dienstprinzipal, und weisen Sie eine Rolle zu, um auf das Abonnement zuzugreifen. Eine Anleitung zur Erstellung eines Dienstprinzipals finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md).

3. Rufen Sie die folgenden erforderlichen Umgebungsvariablenwerte ab:

   - AZURE_TENANT_ID
   - AZURE_CLIENT_ID
   - AZURE_CLIENT_SECRET
   - AZURE_SUBSCRIPTION_ID
   - ARM_ENDPOINT
   - RESOURCE_LOCATION

4. Legen Sie die folgenden Umgebungsvariablen fest, und verwenden Sie dabei die Informationen, die Sie aus dem mithilfe der Eingabeaufforderung erstellten Dienstprinzipal abgerufen haben:

   - export AZURE_TENANT_ID={Ihre Mandanten-ID}
   - export AZURE_CLIENT_ID={Ihre Client-ID}
   - export AZURE_CLIENT_SECRET={Ihr geheimer Clientschlüssel}
   - export AZURE_SUBSCRIPTION_ID={Ihre Abonnement-ID}
   - export ARM_ENDPOINT={Ihre Azure Stack-Resource Manager-URL}
   - export RESOURCE_LOCATION={Speicherort von Azure Stack}

   Verwenden Sie unter Windows **set** anstelle von **export**.

5. Verwenden Sie den `getactivedirectorysettings`-Code zum Abrufen des ARM-Metadaten-Endpunkts und den HTTP-Client zum Festzulegen der Endpunktinformationen.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6. Fügen Sie in der Datei „pom.xml“ die folgende Abhängigkeit hinzu, um das Profil **2018-03-01-hybrid** für Azure Stack zu verwenden. Diese Abhängigkeit installiert die Module, die diesem Profil zugeordnet sind, für die Ressourcenanbieter Compute, Netzwerk, Speicher, KeyVault und App Services:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. Geben Sie an der Eingabeaufforderung, die zum Festlegen der Umgebungsvariablen geöffnet wurde, den folgenden Befehl ein:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu API-Profilen finden Sie unter:

- [Versionsprofile in Azure Stack](azure-stack-version-profiles.md)
- [Von Profilen unterstützte API-Versionen von Ressourcenanbietern](azure-stack-profiles-azure-resource-manager-versions.md)
