---
title: Registrieren des ASDK bei Azure
description: Erfahren Sie, wie das ASDK bei Azure registriert wird, um die Azure Marketplace-Syndikation und die Berichterstellung zur Verwendung zu aktivieren.
author: PatAltimore
ms.topic: article
ms.date: 1/20/2021
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 1/20/2021
ms.openlocfilehash: d5ef14d0e5674c8eefac6b4b4b0877aed8541207
ms.sourcegitcommit: c87d1e26a4f96be4651f63fbf5ea3d98d6f14832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659390"
---
# <a name="register-the-asdk-with-azure"></a>Registrieren des ASDK bei Azure

Sie können Ihre ASDK-Installation (Azure Stack Development Kit) bei Azure registrieren, um Marketplace-Elemente von Azure herunterzuladen und die Berichterstellung zu E-Commerce-Daten für Microsoft einzurichten. Die Registrierung ist für die uneingeschränkte Azure Stack Hub-Funktionalität erforderlich (einschließlich der Marketplace-Syndikation). Sie ist außerdem erforderlich, damit Sie wichtige Azure Stack Hub-Funktionen wie die Marketplace-Syndikation und die Berichterstellung zur Nutzung testen können. Nach dem Registrieren von Azure Stack Hub wird die Nutzung an Azure Commerce übermittelt. Dies wird im Abonnement angezeigt, das Sie zur Registrierung verwendet haben. Die übermittelte Nutzung durch Benutzer des ASDK wird diesen jedoch nicht in Rechnung gestellt.

Wenn Sie Ihr ASDK nicht registrieren, wird möglicherweise die Warnung **Aktivierung erforderlich** angezeigt, die Ihnen dazu rät, Ihr ASDK zu registrieren. Dies ist das erwartete Verhalten.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das ASDK mithilfe der folgenden Anleitung bei Azure registrieren, vergewissern Sie sich, dass Sie die mit Azure Stack Hub kompatiblen PowerShell-Module installiert sowie die Azure Stack Hub-Tools heruntergeladen haben, so wie dies im Artikel [Konfiguration nach der Bereitstellung](asdk-post-deploy.md) beschrieben ist.

Außerdem muss der PowerShell-Sprachmodus auf dem Computer für die Registrierung des ASDK bei Azure auf **FullLanguage** festgelegt werden. Um zu überprüfen, ob der aktuelle Sprachmodus auf vollständig festgelegt ist, öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie die folgenden PowerShell-Befehle aus:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vergewissern Sie sich, dass in der Ausgabe **FullLanguage** zurückgegeben wird. Wenn ein anderer Sprachmodus zurückgegeben wird, muss die Registrierung auf einem anderen Computer ausgeführt oder der Sprachmodus auf **FullLanguage** festgelegt werden, damit Sie fortfahren können.

Das für die Registrierung verwendete Azure AD-Konto muss Zugriff auf das Azure-Abonnement besitzen und berechtigt sein, Identitäts-Apps und Dienstprinzipale in dem Verzeichnis zu erstellen, das diesem Abonnement zugeordnet ist. Es wird empfohlen, Azure Stack Hub durch [Erstellen eines Dienstkontos für die Registrierung](../operator/azure-stack-registration-role.md) bei Azure zu registrieren, statt die Anmeldeinformationen eines globalen Administrators zu verwenden.

## <a name="register-the-asdk"></a>Registrieren des ASDK

Führen Sie diese Schritte aus, um das ASDK bei Azure zu registrieren.

> [!NOTE]
> Alle diese Schritte müssen von einem Computer ausgeführt werden, der Zugriff auf den privilegierten Endpunkt hat. Beim ASDK ist das der ASDK-Hostcomputer.



### <a name="az-modules"></a>[Az-Module](#tab/az1)

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.  

2. Führen Sie die folgenden PowerShell-Cmdlets zum Registrieren Ihrer ASDK-Installation bei Azure aus. Melden Sie sich sowohl bei Ihrem Azure-Abrechnungsabonnement als auch bei der lokalen ASDK-Installation an. Falls Sie noch nicht über eine Azure-Abrechnungsabonnement-ID verfügen, können Sie [hier ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?b=17.06). Durch das Registrieren von Azure Stack Hub fallen keine zusätzlichen Kosten in Ihrem Azure-Abonnement an.<br><br>Legen Sie einen eindeutigen Namen für die Registrierung fest, wenn Sie das Cmdlet **Set-AzsRegistration** ausführen. Der **RegistrationName**-Parameter hat den Standardwert **AzureStackRegistration**. Wenn Sie den gleichen Namen jedoch in mehreren Instanzen von Azure Stack Hub verwenden, tritt beim Ausführen des Skripts ein Fehler auf.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzAccount -EnvironmentName "<environment name>"
    
    # Register the Azure Stack Hub resource provider in your Azure subscription
    Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
    
    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1
    
    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription
    
    # Register Azure Stack Hub
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

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.  

2. Führen Sie die folgenden PowerShell-Befehle zum Registrieren Ihrer ASDK-Installation bei Azure aus. Melden Sie sich sowohl bei Ihrem Azure-Abrechnungsabonnement als auch bei der lokalen ASDK-Installation an. Falls Sie noch nicht über eine Azure-Abrechnungsabonnement-ID verfügen, können Sie [hier ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?b=17.06). Durch das Registrieren von Azure Stack Hub fallen keine zusätzlichen Kosten in Ihrem Azure-Abonnement an.<br><br>Legen Sie einen eindeutigen Namen für die Registrierung fest, wenn Sie das Cmdlet **Set-AzsRegistration** ausführen. Der **RegistrationName**-Parameter hat den Standardwert **AzureStackRegistration**. Wenn Sie den gleichen Namen jedoch in mehreren Instanzen von Azure Stack Hub verwenden, tritt beim Ausführen des Skripts ein Fehler auf.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"
    
    # Register the Azure Stack Hub resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
    
    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
    
    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack Hub
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration  `
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

Wenn Sie Azure Stack Hub in einer nicht verbundenen Umgebung (ohne Internetverbindung) registrieren, müssen Sie ein Registrierungstoken aus der Azure Stack Hub-Umgebung abrufen und dieses dann auf einem Computer verwenden, der eine Verbindung mit Azure herstellen kann. Dies dient zum Registrieren und Erstellen einer Aktivierungsressource für Ihre ASDK-Umgebung.

 > [!IMPORTANT]
 > Stellen Sie vor der Registrierung von Azure Stack Hub anhand dieser Anleitung sicher, dass Sie PowerShell für Azure Stack Hub installiert und die Azure Stack Hub-Tools wie im Artikel zur [Konfiguration nach der Bereitstellung](asdk-post-deploy.md) beschrieben heruntergeladen haben. Dies gilt sowohl für den ASDK-Hostcomputer als auch für den Computer mit Internetzugriff, der zum Herstellen der Verbindung mit Azure und zum Durchführen der Registrierung verwendet wird.

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Abrufen eines Registrierungstokens aus der Azure Stack Hub-Umgebung

Starten Sie PowerShell auf dem ASDK-Hostcomputer als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-az**, das beim Herunterladen der Azure Stack Hub-Tools erstellt wurde. Verwenden Sie die folgenden PowerShell-Befehle, um das Modul **RegisterWithAzure.psm1** zu importieren, und verwenden Sie anschließend das Cmdlet **Get-AzsRegistrationToken**, um das Registrierungstoken abzurufen:  

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
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

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

  # Register the Azure Stack Hub resource provider in your Azure subscription
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
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRMAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription

  # Register the Azure Stack Hub resource provider in your Azure subscription
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
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

  # Register the Azure Stack Hub resource provider in your Azure subscription
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
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRMAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription

  # Register the Azure Stack Hub resource provider in your Azure subscription
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

---

Nach Abschluss der Registrierung sollte eine Meldung ähnlich der folgenden angezeigt werden: **Ihre Azure Stack Hub-Umgebung ist nun bei Azure registriert.**

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
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

---


### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Erstellen einer Aktivierungsressource in Azure Stack Hub

Kehren Sie mit der Datei oder dem Text aus dem Aktivierungsschlüssel, der von **Get-AzsActivationKey** erstellt wurde, zur Azure Stack Hub-Umgebung zurück. Führen Sie die folgenden PowerShell-Befehle aus, um mit diesem Aktivierungsschlüssel eine Aktivierungsressource in Azure Stack Hub zu erstellen:   

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
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
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
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

---


Nach Abschluss der Aktivierung sollte eine Meldung ähnlich der folgenden angezeigt werden: **Der Registrierungs- und Aktivierungsvorgang für Ihre Umgebung wurde abgeschlossen.**

## <a name="verify-the-registration-was-successful"></a>Überprüfen der erfolgreichen Ausführung der Registrierung

Sie können die Kachel **Regionsverwaltung** verwenden, um zu überprüfen, ob die Azure Stack Hub-Registrierung erfolgreich war. Diese Kachel steht im Standarddashboard im Administratorportal zur Verfügung.

1. Melden Sie sich beim Azure Stack Hub-Administratorportal `https://adminportal.local.azurestack.external` an.

2. Wählen Sie im Dashboard **Regionsverwaltung** aus.

    [![Kachel „Regionsverwaltung“ im Azure Stack Hub-Administratorportal](media/asdk-register/admin1sm.png "Kachel „Regionsverwaltung“")](media/asdk-register/admin1.png#lightbox)

3. Wählen Sie **Eigenschaften** aus. Auf diesem Blatt werden der Status und Details Ihrer Umgebung angezeigt. Der Status kann **registriert** oder **nicht registriert** lauten. Beim Status „registriert“ wird außerdem die ID des Azure-Abonnements angezeigt, mit dem Sie Ihre Azure Stack Hub-Instanz registriert haben, sowie die Gruppe und der Name der Registrierungsressource.

## <a name="move-a-registration-resource"></a>Verschieben einer Registrierungsressource
Das Verschieben einer Registrierungsressource zwischen Ressourcengruppen im gleichen Abonnement **wird unterstützt**. Weitere Informationen zum Verschieben von Ressourcen in eine neue Ressourcengruppe finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen eines Azure Stack Hub-Marketplace-Elements](../operator/azure-stack-marketplace.md)
