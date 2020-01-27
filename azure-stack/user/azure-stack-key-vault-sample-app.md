---
title: Zugriff auf Azure Stack Hub-Key Vault-Geheimnisse durch Apps zulassen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Beispiel-App ausführen, die Schlüssel und Geheimnisse aus einem Schlüsseltresor in Azure Stack Hub abruft.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: f73af34f3069b0fe550c8266253dce32efcb6b90
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883625"
---
# <a name="allow-apps-to-access-azure-stack-hub-key-vault-secrets"></a>Zugriff auf Azure Stack Hub-Key Vault-Geheimnisse durch Apps zulassen

Nutzen Sie die Schritte in diesem Artikel, um die Beispiel-App **HelloKeyVault** auszuführen, die Schlüssel und Geheimnisse aus einem Schlüsseltresor in Azure Stack Hub abruft.

## <a name="prerequisites"></a>Voraussetzungen

Sie können die folgenden erforderlichen Komponenten über das [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder einen Windows-basierten externen Client installieren, sofern [eine VPN-Verbindung besteht](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Installieren Sie [mit Azure Stack Hub kompatible Azure PowerShell-Module](../operator/azure-stack-powershell-install.md).
* Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Erstellen eines Schlüsseltresors und Registrieren einer App

Bereiten Sie die Beispielanwendung wie folgt vor:

* Erstellen Sie einen Schlüsseltresor in Azure Stack Hub.
* Registrieren Sie eine App in Azure Active Directory (Azure AD).

Verwenden Sie das Azure-Portal oder PowerShell, um die Vorbereitungen für die Beispiel-App durchzuführen.

> [!NOTE]
> Standardmäßig wird mit dem PowerShell-Skript eine neue App in Active Directory erstellt. Sie können aber auch eine Ihrer vorhandenen Anwendungen registrieren.

Stellen Sie vor dem Ausführen des folgenden Skripts sicher, dass Sie Werte für die Variablen `aadTenantName` und `applicationPassword` angeben. Wenn Sie keinen Wert für `applicationPassword` angeben, generiert dieses Skript ein zufälliges Kennwort.

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
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

Die folgende Abbildung zeigt die Ausgabe des Skripts, das zum Erstellen des Schlüsseltresors verwendet wird:

![Schlüsseltresor mit Zugriffsschlüsseln](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Notieren Sie die vom Skript zurückgegeben Werte für **VaultUrl**, **AuthClientId** und **AuthClientSecret**. Diese Werte verwenden Sie zum Ausführen der Anwendung **HelloKeyVault**.

## <a name="download-and-configure-the-sample-application"></a>Herunterladen und Konfigurieren der Beispielanwendung

Laden Sie den Beispielschlüsseltresor von der Seite mit den [Azure Key Vault-Clientbeispielen](https://www.microsoft.com/download/details.aspx?id=45343) herunter. Extrahieren Sie den Inhalt der ZIP-Datei auf Ihrer Entwicklungsarbeitsstation. Der Beispielordner enthält zwei Apps. In diesem Artikel wird **HelloKeyVault** verwendet.

So laden Sie das **HelloKeyVault**-Beispiel:

1. Navigieren Sie zum Ordner **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**.
2. Öffnen Sie die App **HelloKeyVault** in Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

In Visual Studio:

1. Öffnen Sie die Datei „HelloKeyVault\App.config“, und suchen Sie nach dem Element `<appSettings>`.
2. Aktualisieren Sie die Schlüssel **VaultUrl**, **AuthClientId** und **AuthClientSecret** mit den beim Erstellen des Schlüsseltresors zurückgegebenen Werten. Standardmäßig enthält die Datei „App.config“ einen Platzhalter für `AuthCertThumbprint`. Ersetzen Sie diesen Platzhalter durch `AuthClientSecret`.

   ![App-Einstellungen](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Erstellen Sie die Projektmappe neu.

## <a name="run-the-app"></a>Ausführen der App

Beim Ausführen von **HelloKeyVault** meldet sich die App bei Azure AD an und nutzt dann das `AuthClientSecret`-Token, um den Schlüsseltresor in Azure Stack Hub zu authentifizieren.

Sie können das Beispiel **HelloKeyVault** für folgende Zwecke verwenden:

* Führen Sie grundlegende Vorgänge wie das Erstellen, Verschlüsseln, Umschließen und Löschen der Schlüssel und Geheimnisse durch.
* Übergeben Sie Parameter (z.B. `encrypt` und `decrypt`) an **HelloKeyVault**, und wenden Sie die angegebenen Änderungen auf einen Schlüsseltresor an.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Kennwort](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Zertifikat](azure-stack-key-vault-push-secret-into-vm.md)
