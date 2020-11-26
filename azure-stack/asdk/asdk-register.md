---
title: Registrieren des ASDK bei Azure
description: Erfahren Sie, wie das ASDK bei Azure registriert wird, um die Azure Marketplace-Syndikation und die Berichterstellung zur Verwendung zu aktivieren.
author: justinha
ms.topic: article
ms.date: 11/14/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/14/2020
ms.openlocfilehash: 4fc89c86a2268c4648a7e90fa0f6e90cbc8b260e
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517241"
---
# <a name="register-the-asdk-with-azure"></a>Registrieren des ASDK bei Azure

Sie können Ihre ASDK-Installation (Azure Stack Development Kit) bei Azure registrieren, um Marketplace-Elemente von Azure herunterzuladen und die Berichterstellung zu E-Commerce-Daten für Microsoft einzurichten. Die Registrierung ist für die uneingeschränkte Unterstützung aller Azure Stack-Funktionen (einschließlich Marketplace-Syndikation) erforderlich. Eine Registrierung ist erforderlich, da Sie hierüber wichtige Azure Stack-Funktionen wie die Marketplace-Syndikation und Berichterstellung zur Verwendung testen können. Nachdem Sie Azure Stack registrieren, wird Ihre Nutzung an den Azure-Commerce übermittelt. Dies wird im Abonnement angezeigt, das Sie zur Registrierung verwendet haben. Die übermittelte Nutzung durch Benutzer des ASDK wird diesen jedoch nicht in Rechnung gestellt.

Wenn Sie Ihr ASDK nicht registrieren, wird möglicherweise die Warnung **Aktivierung erforderlich** angezeigt, die Ihnen dazu rät, Ihr ASDK zu registrieren. Dies ist das erwartete Verhalten.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die folgenden Anweisungen zur Registrierung des ASDK bei Azure befolgen, installieren Sie die mit Azure Stack kompatiblen PowerShell-Module, und laden Sie die Azure Stack-Tools herunter. Dies wird im Artikel [Konfiguration nach der Bereitstellung](asdk-post-deploy.md) beschrieben.

Außerdem muss der PowerShell-Sprachmodus auf dem Computer für die Registrierung des ASDK bei Azure auf **FullLanguage** festgelegt werden. Um zu überprüfen, ob der aktuelle Sprachmodus auf vollständig festgelegt ist, öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie die folgenden PowerShell-Befehle aus:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vergewissern Sie sich, dass in der Ausgabe **FullLanguage** zurückgegeben wird. Wenn ein anderer Sprachmodus zurückgegeben wird, muss die Registrierung auf einem anderen Computer ausgeführt oder der Sprachmodus auf **FullLanguage** festgelegt werden, damit Sie fortfahren können.

Das für die Registrierung verwendete Azure AD-Konto muss Zugriff auf das Azure-Abonnement besitzen und berechtigt sein, Identitäts-Apps und Dienstprinzipale in dem Verzeichnis zu erstellen, das diesem Abonnement zugeordnet ist. Wir empfehlen, Azure Stack durch [Erstellen eines Dienstkontos für die Registrierung](../operator/azure-stack-registration-role.md) zu registrieren, statt Anmeldeinformationen eines globalen Administrators zu verwenden.

## <a name="register-the-asdk"></a>Registrieren des ASDK

Führen Sie diese Schritte aus, um das ASDK bei Azure zu registrieren.

> [!NOTE]
> Alle diese Schritte müssen von einem Computer ausgeführt werden, der Zugriff auf den privilegierten Endpunkt hat. Beim ASDK ist das der ASDK-Hostcomputer.



### <a name="az-modules"></a>[Az-Module](#tab/az1)

1. Öffnen Sie eine PowerShell-Konsole als Administrator.  

2. Führen Sie die folgenden PowerShell-Befehle zum Registrieren Ihrer ASDK-Installation bei Azure aus. Melden Sie sich sowohl bei Ihrem Azure-Abrechnungsabonnement als auch bei der lokalen ASDK-Installation an. Falls Sie noch nicht über eine Azure-Abrechnungsabonnement-ID verfügen, können Sie [hier ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?b=17.06). Durch das Registrieren von Azure Stack fallen keine zusätzlichen Kosten in Ihrem Azure-Abonnement an.<br><br>Legen Sie einen eindeutigen Namen für die Registrierung fest, wenn Sie das Cmdlet **Set-AzsRegistration** ausführen. Der **RegistrationName**-Parameter hat den Standardwert **AzureStackRegistration**. Wenn Sie den gleichen Namen jedoch in mehreren Instanzen von Azure Stack verwenden, tritt beim Ausführen des Skripts ein Fehler auf.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzAccount -EnvironmentName "<environment name>"
    
    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
    
    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1
    
    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. Nach Abschluss des Skripts sollte diese Meldung angezeigt werden: **Ihre Umgebung ist nun registriert und mit den angegebenen Parametern aktiviert.**

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

1. Öffnen Sie eine PowerShell-Konsole als Administrator.  

2. Führen Sie die folgenden PowerShell-Befehle zum Registrieren Ihrer ASDK-Installation bei Azure aus. Melden Sie sich sowohl bei Ihrem Azure-Abrechnungsabonnement als auch bei der lokalen ASDK-Installation an. Falls Sie noch nicht über eine Azure-Abrechnungsabonnement-ID verfügen, können Sie [hier ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?b=17.06). Durch das Registrieren von Azure Stack fallen keine zusätzlichen Kosten in Ihrem Azure-Abonnement an.<br><br>Legen Sie einen eindeutigen Namen für die Registrierung fest, wenn Sie das Cmdlet **Set-AzsRegistration** ausführen. Der **RegistrationName**-Parameter hat den Standardwert **AzureStackRegistration**. Wenn Sie den gleichen Namen jedoch in mehreren Instanzen von Azure Stack verwenden, tritt beim Ausführen des Skripts ein Fehler auf.

    ```powershell  
    # Add the AzureRMure cloud subscription environment name. 
    # Supported environment names are AzureRMureCloud, AzureRMureChinaCloud, or AzureRMureUSGovernment depending which AzureRMure subscription you're using.
    Add-AzureRMAccount -EnvironmentName "<environment name>"
    
    # Register the AzureRMure Stack resource provider in your AzureRMure subscription
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureRMureStack
    
    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1
    
    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription
    
    # Register AzureRMure Stack
    $AzureRMureContext = Get-AzureRMContext
    $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzureRMsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. Nach Abschluss des Skripts sollte diese Meldung angezeigt werden: **Ihre Umgebung ist nun registriert und mit den angegebenen Parametern aktiviert.**

---



![Ihre Umgebung ist nun registriert.](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>Registrieren in nicht verbundenen Umgebungen

Wenn Sie Azure Stack in einer nicht mit Azure verbundenen Umgebung (ohne Internetverbindung) registrieren, müssen Sie ein Registrierungstoken aus der Azure Stack-Umgebung abrufen und dieses Token dann auf einem Computer verwenden, der eine Verbindung mit Azure herstellen kann. Dies dient zum Registrieren und Erstellen einer Aktivierungsressource für Ihre ASDK-Umgebung.

 > [!IMPORTANT]
 > Stellen Sie vor der Verwendung dieser Anleitung zum Registrieren von Azure Stack sicher, dass Sie PowerShell für Azure Stack installiert und die Azure Stack-Tools wie im Artikel zur [Konfiguration nach der Bereitstellung](asdk-post-deploy.md) beschrieben heruntergeladen haben. Dies gilt sowohl für den ASDK-Hostcomputer als auch für den Computer mit Internetzugriff, der zum Herstellen der Verbindung mit Azure und zum Durchführen der Registrierung verwendet wird.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Abrufen eines Registrierungstokens aus der Azure Stack-Umgebung

Starten Sie auf dem ASDK-Hostcomputer PowerShell als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-az**, das beim Herunterladen der Azure Stack-Tools erstellt wurde. Verwenden Sie die folgenden PowerShell-Befehle, um das Modul **RegisterWithAzure.psm1** zu importieren, und verwenden Sie anschließend das Cmdlet **Get-AzsRegistrationToken**, um das Registrierungstoken abzurufen:  

### <a name="az-modules"></a>[Az-Module](#tab/az2)

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm2)

  ```powershell  
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  # Create registration token
  $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
  $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationToken = Get-AzureRMsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
  -UsageReportingEnabled:$false `
  -PrivilegedEndpoint AzureRMS-ERCS01 `
  -BillingModel Development `
  -MarketplaceSyndicationEnabled:$false `
  -TokenOutputFilePath $FilePathForRegistrationToken
  ```

---

Speichern Sie dieses Registrierungstoken für die Verwendung auf dem Computer mit der Internetverbindung. Sie können die Datei oder den Text aus der Datei kopieren, die durch den Parameter `$FilePathForRegistrationToken` erstellt wurde.

### <a name="connect-to-azure-and-register"></a>Verbinden mit Azure und Registrieren

Verwenden Sie auf dem Computer mit Internetverbindung die folgenden PowerShell-Befehle, um das Modul **RegisterWithAzure.psm1** zu importieren. Verwenden Sie anschließend das Cmdlet **Register-AzsEnvironment** für die Registrierung bei Azure, indem Sie das gerade erstellte Registrierungstoken und einen eindeutigen Registrierungsnamen angeben:  

### <a name="az-modules"></a>[Az-Module](#tab/az3)

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```


### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm3)

  ```powershell  
  # Add the AzureRMure cloud subscription environment name. 
  # Supported environment names are AzureRMureCloud, AzureRMureChinaCloud or AzureRMureUSGovernment depending which AzureRMure subscription you are using.
  Add-AzureRMAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription

  # Register the AzureRMure Stack resource provider in your AzureRMure subscription
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureRMureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  # Register with AzureRMure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzureRMsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

---

Alternativ können Sie mit dem Cmdlet **Get-Content** auf eine Datei verweisen, die Ihr Registrierungstoken enthält:

### <a name="az-modules"></a>[Az-Module](#tab/az4)

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm4)

  ```powershell  
  # Add the AzureRMure cloud subscription environment name. 
  # Supported environment names are AzureRMureCloud, AzureRMureChinaCloud or AzureRMureUSGovernment depending which AzureRMure subscription you are using.
  Add-AzureRMAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription

  # Register the AzureRMure Stack resource provider in your AzureRMure subscription
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureRMureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  # Register with AzureRMure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzureRMsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

---

Nach Abschluss der Registrierung sollte eine Meldung ähnlich der folgenden angezeigt werden: **Ihre Azure Stack-Umgebung ist nun bei Azure registriert.**

> [!IMPORTANT]
> Lassen Sie das PowerShell-Fenster **geöffnet**.

Speichern Sie das Registrierungstoken und den Namen der Registrierungsressource zur späteren Verwendung.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Abrufen eines Aktivierungsschlüssels aus der Azure-Registrierungsressource

Verwenden Sie weiterhin den Computer mit Internetverbindung **und dasselbe PowerShell-Konsolenfenster**, und rufen Sie einen Aktivierungsschlüssel von der Registrierungsressource ab, die bei der Registrierung für Azure erstellt wurde.

Führen Sie die folgenden PowerShell-Befehle zum Abrufen des Aktivierungsschlüssels aus. Verwenden Sie den gleichen eindeutigen Wert für den Registrierungsnamen, den Sie bei der Registrierung bei Azure im vorherigen Schritt angegeben haben:  

### <a name="az-modules"></a>[Az-Module](#tab/az5)

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```



### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm5)

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzureRMsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

---


### <a name="create-an-activation-resource-in-azure-stack"></a>Erstellen einer Aktivierungsressource in Azure Stack

Kehren Sie mit der Datei oder dem Text aus dem Aktivierungsschlüssel, der von **Get-AzsActivationKey** erstellt wurde, zur Azure Stack-Umgebung zurück. Führen Sie die folgenden PowerShell-Befehle aus, um mit diesem Aktivierungsschlüssel eine Aktivierungsressource in Azure Stack zu erstellen:   

### <a name="az-modules"></a>[Az-Module](#tab/az6)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```



### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm6)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureRMureStack-Tools-Master\Registration\RegisterWithAzureRMure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzureRMsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzureRMS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

---


Alternativ können Sie mit dem Cmdlet **Get-Content** auf eine Datei verweisen, die Ihr Registrierungstoken enthält:

### <a name="az-modules"></a>[Az-Module](#tab/az7)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```



### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm7)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzureRMsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzureRMS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

---


Nach Abschluss der Aktivierung sollte eine Meldung ähnlich der folgenden angezeigt werden: **Der Registrierungs- und Aktivierungsvorgang für Ihre Umgebung wurde abgeschlossen.**

## <a name="verify-the-registration-was-successful"></a>Überprüfen der erfolgreichen Ausführung der Registrierung

Sie können die Kachel **Regionsverwaltung** verwenden, um zu überprüfen, ob die Azure Stack-Registrierung erfolgreich war. Diese Kachel steht im Standarddashboard im Administratorportal zur Verfügung.

1. Melden Sie am Azure Stack-Administratorportal (`https://adminportal.local.azurestack.external`) an.

2. Wählen Sie im Dashboard **Regionsverwaltung** aus.

    [![Kachel „Regionsverwaltung“ im Azure Stack-Administratorportal](media/asdk-register/admin1sm.png "Kachel „Regionsverwaltung“")](media/asdk-register/admin1.png#lightbox)

3. Wählen Sie **Eigenschaften** aus. Auf diesem Blatt werden der Status und Details Ihrer Umgebung angezeigt. Der Status kann **registriert** oder **nicht registriert** lauten. Beim Status „registriert“ wird außerdem die ID des Azure-Abonnements angezeigt, mit dem Sie Ihren Azure Stack registriert haben, sowie die Gruppe und der Name der Registrierungsressource.

## <a name="move-a-registration-resource"></a>Verschieben einer Registrierungsressource
Das Verschieben einer Registrierungsressource zwischen Ressourcengruppen im gleichen Abonnement **wird unterstützt**. Weitere Informationen zum Verschieben von Ressourcen in eine neue Ressourcengruppe finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen eines Azure Stack Hub-Marketplace-Elements](../operator/azure-stack-marketplace.md)
