---
title: Verwalten von Key Vault in Azure Stack Hub mithilfe von PowerShell
description: Hier erfahren Sie, wie Sie Key Vault in Azure Stack Hub mithilfe von PowerShell verwalten.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0c7dc4bffba85c60213df4e57c3fc95f756259df
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704062"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-powershell"></a>Verwalten von Key Vault in Azure Stack Hub mithilfe von PowerShell

In diesem Artikel wird beschrieben, wie Sie einen Schlüsseltresor in Azure Stack Hub mithilfe von PowerShell erstellen und verwalten. Sie erfahren, wie Sie Key Vault-PowerShell-Cmdlets für Folgendes verwenden:

* Erstellen eines Schlüsseltresors
* Speichern und Verwalten von kryptografischen Schlüsseln und Geheimnissen.
* Autorisieren von Benutzern bzw. Apps zum Aufrufen von Vorgängen im Tresor.

>[!NOTE]
>Die in diesem Artikel beschriebenen Key Vault-PowerShell-Cmdlets stehen im Azure PowerShell SDK zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen ein Angebot abonnieren, das den Azure Key Vault-Dienst umfasst.
* [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md)
* [Konfigurieren der Azure Stack Hub-PowerShell-Umgebung](azure-stack-powershell-configure-user.md)

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Aktivieren Ihres Mandantenabonnements für Key Vault-Vorgänge

Damit Sie Vorgänge für einen Schlüsseltresor ausgeben können, muss Ihr Mandantenabonnement für Tresorvorgänge aktiviert sein. Um sicherzustellen, dass Schlüsseltresorvorgänge aktiviert sind, führen Sie den folgenden Befehl aus:

```powershell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

Wenn Ihr Abonnement für Tresorvorgänge aktiviert ist, hat **RegistrationState** in der Ausgabe für alle Ressourcentypen eines Schlüsseltresors den Wert **Registered**.

![Registrierungsstatus für Schlüsseltresore in PowerShell](media/azure-stack-key-vault-manage-powershell/image1.png)

Wenn keine Tresorvorgänge aktiviert sind, geben Sie den folgenden Befehl aus, um den Key Vault-Dienst in Ihrem Abonnement zu registrieren:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

Wenn die Registrierung erfolgreich war, wird die folgende Ausgabe zurückgegeben:

![Registrierungsstatus für Schlüsseltresore in PowerShell erfolgreich](media/azure-stack-key-vault-manage-powershell/image2.png)

Wenn Sie die Schlüsseltresorbefehle aufrufen, wird möglicherweise eine Fehlermeldung wie diese ausgegeben: „Das Abonnement ist nicht für die Verwendung des Namespace ‚Microsoft.KeyVault‘ registriert.“ Wenn ein Fehler ausgegeben wird, bestätigen Sie, dass Sie den Key Vault-Ressourcenanbieter gemäß den Anweisungen aktiviert haben.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Erstellen Sie vor dem Erstellen eines Schlüsseltresors zunächst eine Ressourcengruppe, damit alle schlüsseltresorbezogenen Ressourcen in einer Ressourcengruppe enthalten sind. Verwenden Sie den folgenden Befehl, um eine neue Ressourcengruppe zu erstellen:

```powershell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

![Neue, in PowerShell generierte Ressourcengruppe](media/azure-stack-key-vault-manage-powershell/image3.png)

Erstellen Sie nun mithilfe des Cmdlets **New-AzureRMKeyVault** einen Schlüsseltresor in der zuvor erstellten Ressourcengruppe. Dieser Befehl liest drei erforderliche Parameter: Ressourcengruppenname, Schlüsseltresorname und geografischer Standort.

Führen Sie den folgenden Befehl aus, um einen Schlüsseltresor zu erstellen:

```powershell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

![Neuer, in PowerShell generierter Schlüsseltresor](media/azure-stack-key-vault-manage-powershell/image4.png)

Die Ausgabe dieses Befehls zeigt die Eigenschaften des von Ihnen erstellten Schlüsseltresors. Wenn eine App auf diesen Tresor zugreift, muss sie dazu die Eigenschaft **Vault URI** verwenden, in diesem Beispiel `https://vault01.vault.local.azurestack.external`.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Bereitstellung von Active Directory-Verbunddienste (AD FS)

Bei einer AD FS-Bereitstellung wird möglicherweise die Warnung ausgegeben, dass die Zugriffsrichtlinie nicht festgelegt wurde und dass keine Benutzer oder Anwendungen über Zugriffsberechtigungen für die Verwendung dieses Tresors verfügen. Legen Sie zur Behebung dieses Problems mithilfe des Befehls [**Set-AzureRmKeyVaultAccessPolicy**](#authorize-an-app-to-use-a-key-or-secret) eine Zugriffsrichtlinie für den Tresor fest:

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Verwalten von Schlüsseln und Geheimnissen

Führen Sie nach dem Erstellen eines Tresors diese Schritte aus, um Schlüssel und Geheimnisse im Tresor zu erstellen und zu verwalten.

### <a name="create-a-key"></a>Erstellen eines Schlüssels

Verwenden Sie das Cmdlet **Add-AzureKeyVaultKey**, um einen softwaregeschützten Schlüssel in einem Schlüsseltresor zu erstellen oder in einen Schlüsseltresor zu importieren:

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

Mit dem `-Destination`-Parameter wird angegeben, dass es sich um einen softwaregeschützten Schlüssel handelt. Nach erfolgreicher Erstellung des Schlüssels gibt der Befehl die Details des erstellten Schlüssels aus.

![Neuer, in PowerShell generierter Schlüsseltresorschlüssel](media/azure-stack-key-vault-manage-powershell/image5.png)

Sie können nun mithilfe des dazugehörigen URIs auf den erstellten Schlüssel verweisen. Wenn Sie einen Schlüssel mit einem Namen erstellen oder importieren, der dem Namen eines bereits vorhandenen Schlüssels entspricht, wird der ursprüngliche Schlüssel mit den Werten des neuen Schlüssels aktualisiert. Über den versionsspezifischen URI des Schlüssels können Sie auf die vorherige Version des Schlüssels zugreifen. Beispiel:

* Mit `https://vault10.vault.local.azurestack.external:443/keys/key01` können Sie immer die aktuelle Version abrufen.
* Mit `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` rufen Sie diese spezifische Version ab.

### <a name="get-a-key"></a>Abrufen eines Schlüssels

Verwenden Sie das Cmdlet **Get-AzureKeyVaultKey**, um einen Schlüssel und die zugehörigen Details zu lesen:

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Erstellen eines Geheimnisses

Verwenden Sie das Cmdlet **Set-AzureKeyVaultSecret**, um ein Geheimnis in einem Tresor zu erstellen oder zu aktualisieren. Ein Geheimnis wird erstellt, wenn bisher keines vorhanden ist. Wenn es bereits vorhanden ist, wird eine neue Version des Geheimnisses erstellt:

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Erstellen eines geheimen Schlüssels in PowerShell](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Abrufen eines Geheimnisses

Verwenden Sie das Cmdlet **Get-AzureKeyVaultSecret**, um ein Geheimnis in einem Schlüsseltresor zu lesen. Dieser Befehl kann alle oder bestimmte Versionen eines Geheimnisses zurückgeben:

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Nach dem Erstellen von Schlüsseln und Geheimnissen können Sie deren Verwendung durch externe Apps autorisieren.

## <a name="authorize-an-app-to-use-a-key-or-secret"></a>Autorisieren einer App zum Verwenden eines Schlüssels oder Geheimnisses

Verwenden Sie das Cmdlet **Set-AzureRmKeyVaultAccessPolicy**, um den Zugriff einer App auf einen Schlüssel oder auf ein Geheimnis im Schlüsseltresor zu autorisieren.

Im folgenden Beispiel lautet der Tresorname **ContosoKeyVault**, und die App, die Sie autorisieren möchten, hat die Client-ID **8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed**. Führen Sie den folgenden Befehl aus, um die App zu autorisieren. Mithilfe des Parameters **PermissionsToKeys** können Sie auch Berechtigungen für einen Benutzer, eine App oder eine Sicherheitsgruppe festlegen.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Wenn Sie dieselbe App zum Lesen von Geheimnissen in Ihrem Tresor autorisieren möchten, führen Sie das folgende Cmdlet aus:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines virtuellen Computers durch Abrufen des im Schlüsseltresor gespeicherten Kennworts](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Erstellen eines virtuellen Computers und Aufnehmen eines aus einem Schlüsseltresor abgerufenen Zertifikats](azure-stack-key-vault-push-secret-into-vm.md)
