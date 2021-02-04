---
title: Zugriff auf Azure Stack Hub-Key Vault-Geheimnisse durch Apps zulassen
description: Erfahren Sie, wie Sie eine Beispiel-App ausführen, die Schlüssel und Geheimnisse aus einem Schlüsseltresor in Azure Stack Hub abruft.
author: sethmanheim
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: e503bb3124e0b85ceb2816c6cd18af8580eaa61c
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99533993"
---
# <a name="allow-apps-to-access-azure-stack-hub-key-vault-secrets"></a>Zugriff auf Azure Stack Hub-Key Vault-Geheimnisse durch Apps zulassen

In den Schritten in diesem Artikel wird beschrieben, wie Sie die Beispiel-App **HelloKeyVault** ausführen, die Schlüssel und Geheimnisse aus einem Schlüsseltresor in Azure Stack Hub abruft.

## <a name="prerequisites"></a>Voraussetzungen

Sie können die folgenden erforderlichen Komponenten über das [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder einen Windows-basierten externen Client installieren, sofern [eine VPN-Verbindung besteht](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Installieren Sie [mit Azure Stack Hub kompatible Azure PowerShell-Module](../operator/powershell-install-az-module.md).
* Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Erstellen eines Schlüsseltresors und Registrieren einer App

Bereiten Sie die Beispielanwendung wie folgt vor:

* Erstellen Sie einen Schlüsseltresor in Azure Stack Hub.
* Registrieren Sie eine App in Azure Active Directory (Azure AD).

Verwenden Sie das Azure-Portal oder PowerShell, um die Vorbereitungen für die Beispiel-App durchzuführen.

> [!NOTE]
> Standardmäßig wird mit dem PowerShell-Skript eine neue App in Active Directory erstellt. Sie können aber auch eine Ihrer vorhandenen Anwendungen registrieren.

Stellen Sie vor dem Ausführen des folgenden Skripts sicher, dass Sie Werte für die Variablen `aadTenantName` und `applicationPassword` angeben. Wenn Sie keinen Wert für `applicationPassword` angeben, generiert dieses Skript ein zufälliges Kennwort.

### <a name="az-modules"></a>[Az-Module](#tab/az)

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack Hub user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack Hub operator's PowerShell environment.
Add-AzEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Connect-AzAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack Hub user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack Hub operator's PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRMAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRMADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRMADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRMResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRMKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRMKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

---



Die folgende Abbildung zeigt die Ausgabe des Skripts, das zum Erstellen des Schlüsseltresors verwendet wird:

![Schlüsseltresor mit Zugriffsschlüsseln](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Notieren Sie die vom Skript zurückgegeben Werte für **VaultUrl**, **AuthClientId** und **AuthClientSecret**. Diese Werte verwenden Sie zum Ausführen der Anwendung **HelloKeyVault**.

## <a name="download-and-configure-the-sample-application"></a>Herunterladen und Konfigurieren der Beispielanwendung

Laden Sie den Beispielschlüsseltresor von der Seite mit den [Azure Key Vault-Clientbeispielen](https://www.microsoft.com/download/details.aspx?id=45343) herunter. Extrahieren Sie den Inhalt der ZIP-Datei auf Ihrer Entwicklungsarbeitsstation. Der Ordner „samples“ enthält zwei Apps, von denen in diesem Artikel **HelloKeyVault** verwendet wird.

So laden Sie das **HelloKeyVault**-Beispiel:

1. Navigieren Sie zum Ordner **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**.
2. Öffnen Sie die App **HelloKeyVault** in Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

In Visual Studio:

1. Öffnen Sie die Datei „HelloKeyVault\App.config“, und suchen Sie nach dem Element `<appSettings>`.
2. Aktualisieren Sie die Schlüssel **VaultUrl**, **AuthClientId** und **AuthCertThumbprint** mit den beim Erstellen des Schlüsseltresors zurückgegebenen Werten. Standardmäßig enthält die Datei „App.config“ einen Platzhalter für `AuthCertThumbprint`. Ersetzen Sie diesen Platzhalter durch `AuthClientSecret`.

   ```xml
   <appSettings>
    <!-- Update these settings for your test environment -->
    <add key="VaultUrl" value="URL to your Vault" />
    <add key="AuthClientId" value="Client Id of your Service Principal" />
    <add key="AuthCertThumbprint" value="Thumbprint of the certificate used for authentication" />
    <add key="TracingEnabled" value="false" />
   </appSettings>
   ```

3. Erstellen Sie die Projektmappe neu.

## <a name="run-the-app"></a>Ausführen der App

Beim Ausführen von **HelloKeyVault** meldet sich die App bei Azure AD an und nutzt dann das `AuthClientSecret`-Token, um den Schlüsseltresor in Azure Stack Hub zu authentifizieren.

Sie können das Beispiel **HelloKeyVault** für folgende Zwecke verwenden:

* Führen Sie grundlegende Vorgänge wie das Erstellen, Verschlüsseln, Umschließen und Löschen der Schlüssel und Geheimnisse durch.
* Übergeben Sie Parameter (z.B. `encrypt` und `decrypt`) an **HelloKeyVault**, und wenden Sie die angegebenen Änderungen auf einen Schlüsseltresor an.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Kennwort](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Zertifikat](azure-stack-key-vault-push-secret-into-vm.md)
