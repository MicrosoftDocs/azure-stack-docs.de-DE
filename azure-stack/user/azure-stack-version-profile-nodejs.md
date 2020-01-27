---
title: Verwenden von API-Versionsprofilen mit Node.js in Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie mehr zur Verwendung von API-Versionsprofilen mit Node.js in Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: e6e65ebf39ae08b56f02fca437369f4861613ade
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878304"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack-hub"></a>Verwenden von API-Versionsprofilen mit dem Node.js-SDK (Software Development Kit) in Azure Stack Hub

## <a name="nodejs-and-api-version-profiles"></a>Node.js und API-Versionsprofile

Sie können das Node. js-SDK verwenden, um das Erstellen und Verwalten der Infrastruktur für Ihre Apps zu unterstützen. API-Profile im Node.js-SDK stellen eine Hilfe für Hybrid Cloud-Lösungen dar, indem Sie Ihnen den Wechsel zwischen globalen Azure-Ressourcen und Azure Stack Hub-Ressourcen ermöglichen. Sie können Ihren Code einmal erstellen und ihn sowohl für globales Azure als auch Azure Stack Hub nutzen. 

In diesem Artikel können Sie [Visual Studio Code](https://code.visualstudio.com/) als Entwicklungstool verwenden. Visual Studio Code kann das Node.js-SDK debuggen und ermöglicht Ihnen das Ausführen der App und das Hochladen der App auf Ihre Azure Stack Hub-Instanz. Sie können in Visual Studio Code oder mithilfe eines Terminalfensters debuggen, in dem Sie den Befehl `node <nodefile.js>` ausführen.

## <a name="the-nodejs-sdk"></a>Das Node.js SDK

Das Node.js-SDK stellt Tools des Azure Stack Hub Resource Manager zur Verfügung. Zu den Ressourcenanbietern im SDK zählen Compute, Netzwerk, Speicher, App-Dienste und KeyVault. Es gibt 10 Clientbibliotheken für Ressourcenanbieter, die Sie in Ihrer Node.js-Anwendung installieren können. Sie können jedoch auch gezielt den Ressourcenanbieter herunterladen, den Sie für das Profil **2018-03-01-hybrid** oder **2019-03-01-profile** verwenden möchten, um den Arbeitsspeicher für Ihre Anwendung zu optimieren. Jedes Modul besteht aus einem Ressourcenanbieter, der entsprechenden API-Version und dem API-Profil. 

Ein API-Profil ist eine Kombination aus Ressourcenanbietern und API-Versionen. Sie können ein API-Profil verwenden, um die aktuelle, stabilste Version der einzelnen Ressourcentypen in einem Ressourcenanbieterpaket abzurufen.

  -   Verwenden Sie das **aktuelle** Profil von Paketen, um die aktuellen Versionen aller Dienste zu nutzen.

  -   Um die mit Azure Stack Hub kompatiblen Dienste einzusetzen, verwenden Sie **\@azure/arm-resources-profile-hybrid-2019-03-01** oder **\@azure/arm-storage-profile-2019-03-01-hybrid**

### <a name="packages-in-npm"></a>Pakete in npm

Jeder Ressourcenanbieter verfügt über ein eigenes Paket. Sie können das Paket aus der [NPM-Registrierung](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) abrufen.

Dort sind die folgenden Pakete zu finden:

| Ressourcenanbieter | Paket |
| --- | --- |
| [App Service](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Azure Resource Manager-Abonnements](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Azure Resource Manager-Richtlinien](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [Azure Resource Manager-DNS](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [Autorisierung](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [Compute](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [Storage](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Network](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [Ressourcen](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [KeyVault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

Um mit der aktuellsten API-Version eines Diensts zu arbeiten, verwenden Sie das **aktuellste** Profil der spezifischen Clientbibliothek. Wenn Sie beispielsweise die aktuellste API-Version des Ressourcendiensts allein verwenden möchten, verwenden Sie das `azure-arm-resource`-Profil des **Resource Management-Clientbibliothek**- Pakets.

Die spezifischen API-Versionen eines Ressourcenanbieters zu erhalten Sie, indem Sie die im Paket definierten spezifischen API-Versionen verwenden.

  > [!Note]  
  > Sie können alle Optionen in derselben Anwendung kombinieren.

## <a name="install-the-nodejs-sdk"></a>Installieren des Node.js SDK

1. Installieren Sie Git. Anweisungen hierzu finden Sie unter [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Erste Schritte: Installieren von Git).

2. Installieren Sie die aktuelle Version von [Node.js](https://nodejs.org/en/download/), oder führen Sie ein Upgrade darauf aus. Node.js enthält außerdem den JavaScript-Paket-Manager [npm](https://www.npmjs.com/).

3. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/), oder führen Sie ein Upgrade aus, und installieren Sie die [Node.js-Erweiterung](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) für Visual Studio Code.

2.  Installieren Sie die Clientpakete für den Azure Stack Hub Resource Manger. Weitere Informationen finden Sie unter [Installieren von Clientbibliotheken](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  Welche Pakete installiert werden müssen, hängt von der Profilversion ab, die Sie verwenden möchten. Eine Liste der Ressourcenanbieter finden Sie im Abschnitt [Pakete in npm](#packages-in-npm).

4. Installieren Sie die Clientbibliothek des Ressourcenanbieters mithilfe von npm. Führen Sie dies an der Befehlszeile aus: `npm install <package-name>`. Beispielsweise können Sie `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` ausführen, um die Autorisierungsbibliothek des Ressourcenanbieters zu installieren.

5.  Erstellen Sie ein Abonnement, und notieren Sie sich die Abonnement-ID zum Verwenden des SDKs. Eine Anleitung finden Sie unter [Erstellen von Abonnements für Angebote in Azure Stack Hub](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Erstellen Sie einen Dienstprinzipal, und speichern Sie die Client-ID und das Clientgeheimnis. Die Client-ID wird beim Erstellen eines Dienstprinzipals auch als „Anwendungs-ID“ bezeichnet. Weitere Anleitungen finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

7.  Stellen Sie sicher, dass Ihr Dienstprinzipal über die Rolle „Mitwirkender“ bzw. „Besitzer“ für Ihr Abonnement verfügt. Eine Anleitung zum Zuweisen einer Rolle zum Dienstprinzipal finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>Node.js-Voraussetzungen 

Zur Verwendung des Node.js-Azure SDK mit Azure Stack Hub müssen Sie die folgenden Werte angeben und dann Werte mit Umgebungsvariablen festlegen. Befolgen Sie die Anweisungen in der Tabelle für Ihr Betriebssystem, um die Umgebungsvariablen festzulegen.

| value | Umgebungsvariablen | Beschreibung |
| --- | --- | --- |
| Mandanten-ID | TENANT\_ID | Der Wert Ihrer [Mandanten-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) für Azure Stack Hub. |
| Client-ID | CLIENT\_ID | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Dokuments gespeichert wurde.  |
| Abonnement-ID | AZURE\_SUBSCRIPTION\_ID   Mithilfe der [Abonnement-ID](/azure-stack/operator/service-plan-offer-subscription-overview#subscriptions) greifen Sie auf Angebote in Azure Stack Hub zu.  |
| Geheimer Clientschlüssel | APPLICATION\_SECRET | Die Geheimnisanwendung des Dienstprinzipals, die bei der Erstellung des Dienstprinzipals gespeichert wurde. |
| Resource Manager-Endpunkt | ARM\_ENDPOINT | Siehe [Der Resource Manager-Endpunkt von Azure Stack Hub](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-hub-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Festlegen Ihrer Umgebungsvariablen für Node.js

So legen Sie Ihre Umgebungsvariablen fest:

  - **Microsoft Windows**  

    Verwenden Sie das folgende Format, um die Umgebungsvariablen in der Windows-Eingabeaufforderung zu verwenden:
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **macOS-, Linux- und Unix-basierte Systeme**  

    Verwenden Sie das folgende Format, um an einer Bash-Eingabeaufforderung die Umgebungsvariablen festzulegen:

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Der Resource Manager-Endpunkt von Azure Stack Hub**

Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.

Sie können die Metadateninformationen vom Resource Manager-Endpunkt abrufen. Der Endpunkt gibt eine JSON-Datei zurück, die die erforderlichen Informationen zum Ausführen Ihres Codes enthält.

> [!Note]  
> Der **ResourceManagerUrl**-Wert im Azure Stack Development Kit (ASDK) lautet: `https://management.local.azurestack.external` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com` Zum Abrufen der erforderlichen Metadaten: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

JSON-Beispieldatei:

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>Vorhandene API-Profile

-  **\@azure/arm-resourceprovider-profile-2019-03-01-hybrid**

    Für Azure Stack Hub erstelltes neuestes Profil. Verwenden Sie dieses Profil für Dienste für die höchste Kompatibilität mit Azure Stack Hub, sofern Sie bei Stempel 1808 oder weiter sind.

-  **\@azure-arm-resource**

    Das Profil umfasst die aktuellen Versionen aller Dienste. Verwenden Sie die neuesten Versionen aller Dienste in Azure.

Weitere Informationen zu Azure Stack Hub und API-Profilen finden Sie unter [Zusammenfassung zu API-Profilen](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles).

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Verwendung des API-Profils aus dem Azure Node.js SDK

Die folgenden Zeilen sollten verwendet werden, um einen Profilclient zu instanziieren. Dieser Parameter ist nur für Azure Stack Hub oder andere private Clouds erforderlich. Für globales Azure sind diese Einstellungen in @azure-arm-resource und @azure-arm-storage standardmäßig vorhanden.

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Zum Authentifizieren des Dienstprinzipals in Azure Stack Hub ist der folgende Code erforderlich. Erstellt anhand der Mandanten-ID und der Authentifizierungsbasis ein für Azure Stack Hub spezifisches Token.

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

Dies ermöglicht Ihnen die Verwendung der API-Profil-Clientbibliothek zum erfolgreichen Bereitstellen Ihrer Anwendung in Azure Stack Hub.

Im Codeausschnitt unten werden der Azure Resource Manager-Endpunkt verwendet, den Sie für Ihre Azure Stack Hub-Instanz angegeben haben, und die oben dargestellten Daten erfasst, wie Katalogendpunkt, Graph-Endpunkt, Zielgruppen und Portalendpunkte.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Umgebungseinstellungen

Verwenden Sie den folgenden Code, um den Dienstprinzipal in der Azure Stack Hub-Umgebung zu authentifizieren: Wenn Sie diesen Code verwenden und Ihre Umgebungsvariablen an der Eingabeaufforderung festlegen, wird automatisch diese Zuordnung für den Entwickler erstellt.

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>Beispiele für die Verwendung von API-Profilen

Sie können die folgenden Beispiele als Referenzen zum Erstellen von Lösungen mit Node.js- und Azure Stack Hub-API-Profilen verwenden. Sie können die Beispiele von GitHub in den folgenden Repositorys abrufen:

- [Storage node resource provider getting started](https://github.com/sijuman/storage-node-resource-provider-getting-started) (Erste Schritte mit dem Speicherknoten-Ressourcenanbieter)
- [Compute node manage](https://github.com/sijuman/compute-node-manage-vm) (Computeknotenverwaltung)
- [Resource-manager node resources and groups](https://github.com/sijuman/resource-manager-node-resources-and-groups) (Ressourcen und Gruppen für Resource Manager-Knoten)

### <a name="sample-create-storage-account"></a>Beispiel für die Erstellung eines Speicherkontos 

1.  Klonen Sie das Repository.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  Erstellen Sie einen Azure-Dienstprinzipal, und weisen Sie eine Rolle zu, um auf das Abonnement zuzugreifen. Anweisungen finden Sie unter [Verwenden von Azure PowerShell zum Erstellen eines Dienstprinzipals mit einem Zertifikat](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3.  Rufen Sie die folgenden erforderlichen Werte ab:
    - Mandanten-ID
    - Client-ID
    - Geheimer Clientschlüssel
    - Azure-Abonnement-ID
    - Resource Manager-Endpunkt von Azure Stack Hub

4.  Legen Sie die folgenden Umgebungsvariablen fest, und verwenden Sie dabei die Informationen, die Sie aus dem mithilfe der Eingabeaufforderung erstellten Dienstprinzipal abgerufen haben:

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Hub Resource manager URL>
    ```

    > [!Note]  
    > Verwenden Sie unter Windows **set** anstelle von **export**.

5.  Öffnen Sie die `index.js`-Datei der Beispielanwendung.

6.  Legen Sie die Standortvariable auf Ihren Azure Stack Hub-Standort fest. Beispiel: `LOCAL = "local"`.

7.  Legen Sie die Anmeldeinformationen fest, mit denen Sie sich bei Azure Stack Hub authentifizieren können. Dieser Teil des Codes ist in diesem Beispiel zur index.js-Datei enthalten.

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  Überprüft, ob Sie die richtigen Clientbibliotheken festgelegt haben, und verwendet dazu eine Kombination der oben aufgeführten Clientbibliotheken. In diesem Beispiel haben wir die folgenden unten Dargestellten verwendet:

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  Suchen Sie mithilfe von [npm modules search](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) nach **2019-03-01-hybrid**, und installieren Sie die Pakete, die diesem Profil zugeordnet sind, für die Ressourcenanbieter Compute, Netzwerk, Speicher, KeyVault und App Services.

    Dies kann dadurch erfolgen, dass Sie eine Eingabeaufforderung öffnen, diese zum Stammordner des Repositorys umleiten und für jeden verwendeten Ressourcenanbieter `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` ausführen.

10.  Führen Sie an der Eingabeaufforderung den Befehl `npm install` aus, um alle node.js-Module zu installieren.

11.  Führen Sie das Beispiel aus.

        ```Node.js  
        node index.js
        ```

12.  Führen Sie das Bereinigungsskript aus, um nach „index.js“ aufzuräumen.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  Das Beispiel wurde erfolgreich abgeschlossen. Weitere Informationen zum Beispiel finden Sie unten.

### <a name="what-does-indexjs-do"></a>Welche Funktion hat index.js?

Das Beispiel erstellt ein neues Speicherkonto, listet die Speicherkonten im Abonnement oder der Ressourcengruppe auf, listet die Speicherkontoschlüssel auf, generiert die Speicherkontoschlüssel neu, ruft die Speicherkontoeigenschaften ab, aktualisiert die Speicherkonto-SKU und überprüft die Verfügbarkeit des Speicherkontonamens.

Das Beispiel beginnt mit der Anmeldung mithilfe Ihres Dienstprinzipals und dem Erstellen der Objekte **ResourceManagementClient** und **StorageManagementClient** mithilfe Ihrer Anmeldeinformationen und Ihrer Abonnement-ID.

Das Beispiel richtet anschließend eine Ressourcengruppe ein, in der das neue Speicherkonto erstellt wird.

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>Erstellen eines neuen Speicherkontos

Als nächstes erstellt das Beispiel ein neues Speicherkonto, das der im vorhergehenden Schritt erstellten Ressourcengruppe zugeordnet ist.

In diesem Fall wird der Name des Speicherkontos nach dem Zufallsprinzip generiert, um Eindeutigkeit sicherzustellen. Der Aufruf zum Erstellen eines neuen Speicherkontos ist jedoch auch dann erfolgreich, wenn bereits ein Konto mit dem gleichen Namen im Abonnement vorhanden ist.

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>Auflisten von Speicherkonten im Abonnement oder der Ressourcengruppe

Im Beispiel werden alle Speicherkonten in einem bestimmten Abonnement aufgelistet:

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>Lesen und erneutes Generieren von Speicherkontoschlüsseln

Im Beispiel werden die Speicherkontoschlüssel für das neu erstellte Speicherkonto und die Ressourcengruppe aufgelistet:

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

Es generiert die Zugriffsschlüssel für die Konten außerdem neu:

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>Abrufen der Eigenschaften von Speicherkonten

Das Beispiel liest die Eigenschaften des Speicherkontos:

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>Überprüfen der Verfügbarkeit des Speicherkontonamens

Das Beispiel überprüft, ob ein bestimmter Speicherkontoname in Azure verfügbar ist:

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>Löschen von Speicherkonto und Ressourcengruppe

Durch Ausführen von „cleanup.js“ wird das Speicherkonto gelöscht, das im Beispiel erstellt wurde:

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

Außerdem wird die im Beispiel erstellte Ressourcengruppe gelöscht:

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu API-Profilen finden Sie unter:

- [Verwalten von API-Versionsprofilen in Azure Stack Hub](azure-stack-version-profiles.md)
- [Von Profilen unterstützte API-Versionen von Ressourcenanbietern](azure-stack-profiles-azure-resource-manager-versions.md)
