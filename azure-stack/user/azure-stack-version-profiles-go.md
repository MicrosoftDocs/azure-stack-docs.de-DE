---
title: Verwenden von API-Versionsprofilen mit GO in Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie API-Versionsprofile mit GO in Azure Stack Hub verwenden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 543279763592fe482ea7e18c74a7e51ce8b92146
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878270"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack-hub"></a>Verwenden von API-Versionsprofilen mit Go in Azure Stack Hub

## <a name="go-and-version-profiles"></a>Go und Versionsprofile

Ein Profil ist eine Kombination aus verschiedenen Ressourcentypen mit unterschiedlichen Versionen aus verschiedenen Diensten. Mithilfe eines Profils können Sie verschiedene Ressourcentypen kombinieren. Profile bieten die folgenden Vorteile:

- Stabilität für Ihre App durch Festlegung auf bestimmte API-Versionen
- Kompatibilität für Ihre App mit Azure Stack Hub und regionalen Azure-Datencentern

Im Go SDK sind Profile unter dem Pfad „profiles“ verfügbar. Profilversionsnummern sind im Format **JJJJ-MM-TT** angegeben. Die neueste Azure Stack Hub-API-Profilversion ist **2019-03-01** für Azure Stack Hub-Version 1904 oder höher. Zum Importieren eines bestimmten Diensts aus einem Profil importieren Sie sein entsprechendes Moduls aus dem Profil. Beispielsweise importieren Sie den Dienst **Compute** aus dem Profil **2019-03-01** mit dem folgenden Code:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>Installieren des Azure SDK für Go

1. Installieren Sie Git. Anweisungen hierzu finden Sie unter [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Erste Schritte: Installieren von Git).
2. Installieren Sie die [Programmiersprache Go](https://golang.org/dl). Für API-Profile für Azure ist mindestens Version 1.9 von Go erforderlich.
3. Installieren Sie das Azure Go SDK und die zugehörigen Abhängigkeiten, indem Sie den folgenden Bash-Befehl ausführen:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Das Go SDK

Weitere Informationen zum Azure Go SDK finden Sie unter den folgenden Links:

- [Installieren des Azure SDK für Go](/go/azure/azure-sdk-go-install)
- Das Azure Go SDK ist öffentlich auf GitHub im Repository [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) verfügbar.

### <a name="go-autorest-dependencies"></a>Go-AutoRest-Abhängigkeiten

Das Go SDK ist zum Senden von REST-Anforderungen an Azure Resource Manager-Endpunkte von den **Azure-Go-AutoRest-Modulen** abhängig. Sie müssen die **Azure-Go-AutoRest**-Modulabhängigkeiten von [Azure-Go-AutoRest auf GitHub](https://github.com/Azure/go-autorest) herunterladen. Die Bash-Befehle zur Installation finden Sie im Abschnitt **Install** (Installation).

## <a name="how-to-use-go-sdk-profiles-on-azure-stack-hub"></a>Verwenden von Go SDK-Profilen in Azure Stack Hub

So führen Sie ein Beispiel für Go-Code in Azure Stack Hub aus

1. Installieren Sie das Azure SDK für Go und die zugehörigen Abhängigkeiten. Anweisungen finden Sie im vorherigen Abschnitt zum [Installieren des Azure SDK für Go](#install-the-azure-sdk-for-go).
2. Rufen Sie die Metadateninformationen vom Resource Manager-Endpunkt ab. Der Endpunkt gibt eine JSON-Datei mit den zum Ausführen des Go-Codes erforderlichen Informationen zurück.

   > [!NOTE]  
   > Der **ResourceManagerUrl**-Wert im Azure Stack Development Kit (ASDK) lautet `https://management.local.azurestack.external/`.  
   > Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet `https://management.<region>.<fqdn>/`.  
   > Zum Abrufen der erforderlichen Metadaten verwenden Sie Folgendes: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.
  
   JSON-Beispieldatei:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Erstellen Sie ein Abonnement, wenn keins verfügbar ist, und speichern Sie die Abonnement-ID zur späteren Verwendung. Informationen zum Erstellen eines Abonnements finden Sie unter [Erstellen von Abonnements für Angebote in Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Erstellen Sie einen Dienstprinzipal, der einen geheimen Clientschlüssel verwendet, mit dem Bereich **Abonnement** und der Rolle **Besitzer**. Speichern Sie die ID und das Geheimnis des Dienstprinzipals. Informationen zum Erstellen eines Dienstprinzipals für Azure Stack Hub finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md). Ihre Azure Stack Hub-Umgebung ist nun eingerichtet.

5. Importieren Sie ein Dienstmodul aus dem Go SDK-Profil in Ihren Code. Die aktuelle Version des Azure Stack Hub-Profils ist **2019-03-01**. Verwenden Sie zum Importieren eines Netzwerkmoduls aus dem Profiltyp **2019-03-01** den folgenden Code:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. Erstellen und authentifizieren Sie in Ihrer Funktion einen Client mit einem Funktionsaufruf für **neue** Clients. Sie können den folgenden Code zum Erstellen eines Clients für virtuelle Netzwerke verwenden:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Legen Sie `<baseURI>` auf den in Schritt 2 verwendeten **ResourceManagerUrl**-Wert fest. Legen Sie `<subscriptionID>` auf den in Schritt 3 gespeicherten **SubscriptionID**-Wert fest.

   Informationen zum Erstellen des Tokens finden Sie im nächsten Abschnitt.  

7. Rufen Sie API-Methoden mithilfe des im vorherigen Schritt erstellten Clients auf. Informationen zum Erstellen eines virtuellen Netzwerks mithilfe des Clients aus dem vorherigen Schritt finden Sie im folgenden Beispiel:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Ein vollständiges Beispiel zum Erstellen eines virtuellen Netzwerks in Azure Stack Hub mithilfe des Go SDK-Profils finden Sie im [Beispiel](#example).

## <a name="authentication"></a>Authentifizierung

Installieren Sie die **Go-AutoRest**-Module, um mithilfe des Go SDK die **Authorizer**-Eigenschaft aus Azure Active Directory abzurufen. Diese Module sollten bei der Installation des Go SDK bereits installiert worden sein. Falls nicht, können Sie das [Authentifizierungspaket von GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal) installieren.

Als Authorizer-Wert muss der Autorisierer für den Ressourcenclient festgelegt werden. Es gibt verschiedene Methoden zum Abrufen von Autorisierertoken in Azure Stack Hub mithilfe von Clientanmeldeinformationen:

1. Wenn ein Dienstprinzipal mit der Rolle „Besitzer“ im Abonnement verfügbar ist, überspringen Sie diesen Schritt. Andernfalls finden Sie Anleitungen zum Erstellen eines Dienstprinzipals, der einen geheimen Clientschlüssel verwendet, und Hilfe dazu, wie Sie ihm die Rolle „Besitzer“ mit Ihrem Abonnement als Gültigkeitsbereich zuweisen, unter [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md). Stellen Sie sicher, dass Sie die Anwendungs-ID und das Geheimnis des Dienstprinzipals speichern.

2. Importieren Sie das **adal**-Paket aus **Go-AutoRest** in Ihren Code.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Erstellen Sie ein **oauthConfig**-Element mithilfe der NewOAuthConfig-Methode aus dem **adal**-Modul.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Legen Sie `<activeDirectoryEndpoint>` auf den Wert der `loginEndpoint`-Eigenschaft aus den `ResourceManagerUrl`-Metadaten fest, die Sie weiter oben in diesem Dokument abgerufen haben. Legen Sie den Wert `<tenantID>` auf Ihre Azure Stack Hub-Mandanten-ID fest.

4. Erstellen Sie abschließend mithilfe der `NewServicePrincipalToken`-Methode aus dem **adal**-Modul ein Dienstprinzipaltoken:

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Legen Sie `<activeDirectoryResourceID>` auf einen der Werte in der Liste „audience“ der **ResourceManagerUrl**-Metadaten fest, die Sie weiter oben in diesem Artikel abgerufen haben.
    Legen Sie `<clientID>` auf die Anwendungs-ID des Dienstprinzipals fest, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Artikels gespeichert wurde.
    Legen Sie `<clientSecret>` auf das Anwendungsgeheimnis des Dienstprinzipals fest, das beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Artikels gespeichert wurde.

## <a name="example"></a>Beispiel

Dies ist ein Beispiel für Go-Code zum Erstellen eines virtuellen Netzwerks in Azure Stack Hub. Vollständige Beispiele des Go SDK finden Sie im [Repository mit Azure Go SDK-Beispielen](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack Hub-Beispiele stehen unter dem Pfad „hybrid“ in den Dienstordnern des Repositorys zur Verfügung.

> [!NOTE]  
> Vergewissern Sie sich zum Ausführen des Codes in diesem Beispiel, dass für das verwendete Abonnement der **Netzwerkressourcenanbieter** als **registriert** festgelegt ist. Navigieren Sie dazu im Azure Stack Hub-Portal zum Abonnement, und wählen Sie **Ressourcenanbieter** aus.

1. Importieren Sie die erforderlichen Pakete in Ihren Code. Verwenden Sie das neueste verfügbare Profil in Azure Stack Hub, um das Netzwerkmodul zu importieren:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Definieren Sie Ihre Umgebungsvariablen. Zum Erstellen eines virtuellen Netzwerks benötigen Sie eine Ressourcengruppe.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Nachdem Sie nun die Umgebungsvariablen definiert haben, fügen Sie eine Methode hinzu, um mithilfe des **adal**-Pakets ein Authentifizierungstoken zu erstellen. Weitere Informationen zur Authentifizierung finden Sie im vorherigen Abschnitt.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Fügen Sie die `main`-Methode hinzu. Die `main`-Methode ruft zunächst ein Token ab. Dabei wird die im vorherigen Schritt definierte Methode verwendet. Anschließend wird mithilfe des Netzwerkmoduls aus dem Profil ein Client erstellt. Abschließend wird ein virtuelles Netzwerk erstellt.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletionRef(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

Unter anderem sind bei Verwendung des Go SDK folgende Codebeispiele für Azure Stack Hub verfügbar:

- [Erstellen eines virtuellen Computers](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Speicherdatenebene](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Verwenden von Managed Disks](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (ein Beispiel mit „2019-03-01-hybrid“ für die aktuellsten API-Versionen, die von Azure Stack Hub unterstützt werden)

## <a name="next-steps"></a>Nächste Schritte

- [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](azure-stack-powershell-configure-user.md)
