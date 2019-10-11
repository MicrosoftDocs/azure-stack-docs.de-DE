---
title: Verwenden von API-Versionsprofilen mit Ruby in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie API-Versionsprofile mit Ruby in Azure Stack verwenden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: d9ef8ab09031db59311317693f72433b63737c34
ms.sourcegitcommit: 3d14ae30ce3ee44729e5419728cce14b3000e968
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71814464"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Verwenden von API-Versionsprofilen mit Ruby in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Ruby und API-Versionsprofile

Das Ruby-SDK für Azure Stack Resource Manager umfasst Tools zum Erstellen und Verwalten Ihrer Infrastruktur. Im SDK enthaltene Ressourcenanbieter sind beispielsweise Compute, Virtual Network und Storage mit der Sprache Ruby. API-Profile im Ruby-SDK ermöglichen die Entwicklung einer Hybrid Cloud, indem es Ihnen ermöglicht wird, zwischen globalen Azure-Ressourcen und Ressourcen unter Azure Stack zu wechseln.

Ein API-Profil ist eine Kombination aus Ressourcenanbietern und Dienstversionen. Sie können ein API-Profil verwenden, um unterschiedliche Ressourcentypen zu kombinieren.

- Verwenden Sie das Profil **Latest** des Azure SDK-Rollup-Gem, um die aktuellen Versionen aller Dienste zu nutzen.
- Verwenden Sie das Profil **V2019_03_01_Hybrid** oder **V2018_03_01** des Azure SDK-Rollup-Gems, um die Dienste zu nutzen, die mit Azure Stack kompatibel sind.
- Verwenden Sie das Profil **Latest** des spezifischen Gem, um die aktuelle **API-Version** eines Diensts zu nutzen. Wenn Sie beispielsweise nur die aktuelle **API-Version** des Computediensts nutzen möchten, können Sie das Profil **Latest** des Gem **Compute** verwenden.
- Verwenden Sie die im Gem definierten spezifischen **API-Versionen**, um eine bestimmte API-Version für einen Dienst zu nutzen.

> [!NOTE]
> Sie können alle Optionen in derselben Anwendung kombinieren.

## <a name="install-the-azure-ruby-sdk"></a>Installieren des Azure Ruby-SDK

- Befolgen Sie die offizielle Anleitung zur Installation von [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Befolgen Sie die offizielle Anleitung zur Installation von [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Wählen Sie bei der Installation die Option **Ruby zur Variable „PATH“ hinzufügen** aus.
  - Installieren Sie das Development Kit, wenn Sie während der Ruby-Installation dazu aufgefordert werden.
  - Installieren Sie als Nächstes den Bundler, indem Sie den folgenden Befehl verwenden: 

       ```Ruby
       Gem install bundler
       ```

- Erstellen Sie ein Abonnement, wenn keins verfügbar ist, und speichern Sie die Abonnement-ID zur späteren Verwendung. Eine Anleitung zum Erstellen eines Abonnements finden Sie im Artikel [Erstellen von Abonnements für Angebote in Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
- Erstellen Sie einen Dienstprinzipal, und speichern Sie die ID und das Geheimnis. Eine Anleitung zum Erstellen eines Dienstprinzipals für Azure Stack finden Sie im Artikel [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md).
- Stellen Sie sicher, dass Ihrem Dienstprinzipal die Rolle „Mitwirkender“ bzw. „Besitzer“ für Ihr Abonnement zugewiesen ist. Eine Anleitung zum Zuweisen einer Rolle zu einem Dienstprinzipal finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Installieren der RubyGem-Pakete

Sie können die Azure-RubyGem-Pakete direkt installieren.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

Oder verwenden Sie sie in Ihrer Gemfile-Datei.

```Ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Das Azure Resource Manager Ruby SDK befindet sich in der Vorschauphase und es kommt in den nächsten Releases voraussichtlich zu grundlegenden Änderungen der Oberfläche. Eine höhere Zahl, die für die Nebenversion angegeben ist, kann auf grundlegende Änderungen hinweisen.

## <a name="use-the-azure_sdk-gem"></a>Verwendung des Gem „azure_sdk“

Das Gem **azure_sdk** ist ein Rollup aller unterstützten Gems im Ruby SDK. Dieses Gem enthält das Profil  **Latest** , das die aktuelle Version aller Dienste unterstützt. Es enthält die mit Versionsangabe versehenen Profile **V2017_03_09** und **V2019_03_01_Hybrid**, die für Azure Stack erstellt werden.

Sie können das Rollup-Gem „azure_sdk“ mit dem folgenden Befehl installieren:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Voraussetzungen

Um das Ruby Azure SDK mit Azure Stack zu verwenden, müssen Sie die folgenden Werte angeben und dann Werte mit Umgebungsvariablen festlegen. Befolgen Sie die Anweisungen in der Tabelle für Ihr Betriebssystem, um die Umgebungsvariablen festzulegen.

| Wert | Umgebungsvariablen | BESCHREIBUNG |
| --- | --- | --- |
| Mandanten-ID | `AZURE_TENANT_ID` | Ihre Azure Stack-[Mandanten-ID](../operator/azure-stack-identity-overview.md). |
| Client-ID | `AZURE_CLIENT_ID` | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Artikels gespeichert wurde.  |
| Abonnement-ID | `AZURE_SUBSCRIPTION_ID` | Sie verwenden die [Abonnement-ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions), um in Azure Stack auf Angebote zuzugreifen. |
| Geheimer Clientschlüssel | `AZURE_CLIENT_SECRET` | Das App-Geheimnis des Dienstprinzipals, das bei der Erstellung des Dienstprinzipals gespeichert wurde. |
| Resource Manager-Endpunkt | `ARM_ENDPOINT` | Siehe [Azure Stack-Resource Manager-Endpunkt](#the-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Der Azure Stack-Resource Manager-Endpunkt

Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.

Sie können die Metadateninformationen vom Resource Manager-Endpunkt abrufen. Der Endpunkt gibt eine JSON-Datei mit den zum Ausführen des Codes erforderlichen Informationen zurück.

 > [!NOTE]  
 > Der **ResourceManagerUrl**-Wert im Azure Stack Development Kit (ASDK) lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Zum Abrufen der erforderlichen Metadaten verwenden Sie Folgendes: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.
  
 JSON-Beispieldatei:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

#### <a name="microsoft-windows"></a>Microsoft Windows

Verwenden Sie das folgende Format, um die Umgebungsvariablen in der Windows-Eingabeaufforderung festzulegen:

```shell
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>macOS-, Linux- und Unix-basierte Systeme

In Unix-basierten Systemen verwenden Sie den folgenden Befehl:

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>Vorhandene API-Profile

Das Rollup-Gem **Azure_sdk** enthält die folgenden drei Profile:

- **V2019_03_01_Hybrid**: Für Azure Stack erstelltes Profil. Verwenden Sie dieses Profil für alle neuesten Versionen der Dienste, die in Azure Stack Version 1904 oder höher verfügbar sind.
- **V2017_03_09**: Für Azure Stack erstelltes Profil. Verwenden Sie dieses Profil für Dienste, um die beste Kompatibilität mit Azure Stack Version 1808 oder niedriger zu erzielen.
- **Aktuell**: Das Profil umfasst die aktuellen Versionen aller Dienste. Verwenden Sie die neuesten Versionen aller Dienste.

Weitere Informationen zu Azure Stack und API-Profilen finden Sie in der [Zusammenfassung zu API-Profilen](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Verwendung des API-Profils aus dem Azure Ruby-SDK

Verwenden Sie den folgenden Code, um einen Profilclient zu instanziieren. Dieser Parameter ist nur für Azure Stack oder andere private Clouds erforderlich. Für Azure sind diese Einstellungen standardmäßig global vorhanden.

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

Der Profilclient kann verwendet werden, um auf einzelne Ressourcenanbieter zuzugreifen, z. B. Compute, Storage und Networking:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Definieren von Einstellungsfunktionen für die Azure Stack-Umgebung

Definieren Sie die Endpunkte mit `get_active_directory_settings()`, um den Dienstprinzipal für die Azure Stack-Umgebung zu authentifizieren. Bei dieser Methode wird die Umgebungsvariable **ARM_Endpoint** genutzt, die Sie zuvor festgelegt haben:

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>Beispiele für die Verwendung von API-Profilen

Verwenden Sie die folgenden Beispiele auf GitHub als Referenz für die Erstellung von Lösungen mit Ruby und Azure Stack-API-Profilen:

- [Verwalten von Azure-Ressourcen und -Ressourcengruppen mit Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).
- [Manage virtual machines using Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (Verwalten von virtuellen Computern mit Ruby) (Beispiel, in dem das Profil „2019-03-01-hybrid“ für die aktuellsten API-Versionen verwendet wird, die von Azure Stack unterstützt werden.)
- [Deploy an SSH Enabled VM with a Template in Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment) (Bereitstellen eines SSH-fähigen virtuellen Computers mit einer Vorlage in Ruby).

### <a name="sample-resource-manager-and-groups"></a>Beispiel für Resource Manager und Gruppen

Vergewissern Sie sich, dass Sie Ruby installiert haben, um das Beispiel auszuführen. Wenn Sie Visual Studio Code verwenden, laden Sie zusätzlich das Ruby SDK als Erweiterung herunter.

> [!NOTE]  
> Das Repository für das Beispiel ist [Hybrid-Resource-Manager-Ruby-Resources-And-Groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).

1. Klonen Sie das Repository:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Installieren Sie die Abhängigkeiten per Bundle:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Erstellen Sie über PowerShell einen Azure-Dienstprinzipal, und rufen Sie die benötigten Werte ab.

   Eine Anleitung zur Erstellung eines Dienstprinzipals finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md).

   Benötigte Werte:

   - Mandanten-ID
   - Client-ID
   - Geheimer Clientschlüssel
   - Abonnement-ID
   - Resource Manager-Endpunkt

   Legen Sie die folgenden Umgebungsvariablen fest, indem Sie die Informationen verwenden, die Sie aus dem erstellten Dienstprinzipal abgerufen haben:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`

   > [!NOTE]  
   > Verwenden Sie unter Windows `set` anstelle von `export`.

4. Stellen Sie sicher, dass die Standortvariable auf Ihren Azure Stack-Standort festgelegt ist, zum Beispiel `LOCAL="local"`.

5. Fügen Sie die folgende Codezeile hinzu, wenn Sie Azure Stack oder andere private Clouds nutzen, um auf die richtigen Active Directory-Endpunkte abzuzielen:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. Fügen Sie in der Variablen `options` die Active Directory-Einstellungen und die Basis-URL hinzu, um Azure Stack zu verwenden:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Erstellen Sie einen Profilclient, der auf das Azure Stack-Profil ausgerichtet ist:

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Für die Authentifizierung des Dienstprinzipals mit Azure Stack sollten die Endpunkte mit **get_active_directory_settings()** definiert werden. Bei dieser Methode wird die Umgebungsvariable **ARM_Endpoint** genutzt, die Sie zuvor festgelegt haben:

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Führen Sie das Beispiel aus.

   ```Ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](azure-stack-powershell-configure-user.md)  
