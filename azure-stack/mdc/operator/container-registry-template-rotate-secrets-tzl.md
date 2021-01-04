---
title: Rotieren von Containerregistrierungsgeheimnissen in Azure Stack Hub – MDC
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie die Containerregistrierungsgeheimnisse in Azure Stack Hub für ein Modular Data Center rotieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 932f63ef4b442578baf9f217ae0f25a6fe29290e
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598214"
---
# <a name="rotate-container-registry-secrets-in-azure-stack-hub---modular-data-center-mdc"></a>Rotieren von Containerregistrierungsgeheimnissen in Azure Stack Hub – Modular Data Center (MDC)

Ihre Azure Stack Hub-Benutzer können die Geheimnisse (Zertifikate, Benutzername und Kennwort) für die Bereitstellung einer Containerregistrierungsvorlage rotieren. Sie können ein Skript zum Ausfüllen neuer Geheimniswerte in Microsoft Azure Key Vault ausführen und die vorhandenen Containerregistrierungsvorlagen-Instanzen **erneut bereitstellen**. Das Rotieren von Geheimnissen allein erfordert keine neue Bereitstellung.

## <a name="prerequisites-for-the-user"></a>Voraussetzungen für den Benutzer

 - Der Benutzer muss die Azure Stack Hub-PowerShell-Module installiert haben. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack](../../operator/powershell-install-az-module.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).

 - Rufen Sie die Aktualisierungsgeheimnisse für die Containerregistrierungsvorlage ab. Für den Zugriff auf die Docker-Registrierung können Sie ein neues SSL-Zertifikat oder eine neue Kombination aus Benutzername und Kennwort verwenden.

 - Rufen Sie nach dem Herunterladen der ZIP-Datei aus dem GitHub-Repository [msazurestackworkloads/azurestack-gallery](https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip) die unter `\registry\scripts` zu findenden Skripts ab.

## <a name="import-new-secrets-into-key-vault"></a>Importieren von neuen Geheimnissen in Key Vault

Befolgen Sie die nachstehenden Anweisungen, um neue Geheimnisse in Key Vault festzulegen.

### <a name="set-updated-registry-user-password-for-existing-username"></a>Festlegen eines aktualisierten Registrierungsbenutzerkennworts für einen vorhandenen Benutzernamen

1.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie `Import-Module .\\pre-reqs.ps1` aus dem Ordner „scripts“ aus.

2.  Führen Sie das folgende Cmdlet aus, um den Wert für den vorhandenen Registrierungsbenutzer zu aktualisieren:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <username> `
        -RegistryUserPassword <newpassword> `
        -SkipExistCheck $true
    ```
    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin `
        -RegistryUserPassword password1 `
        -SkipExistCheck $true 
    
    Check if key vault secret name (admin) exists.
    Creating key vault secret name (admin) as it does not exist.
    ```

1.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus, um zu überprüfen, ob ein neuer Wert für diesen Datensatz eingegeben wurde:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    ```
    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 2a1495372c474cc890c888518f02b19f
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/2a1495372c474cc890c888518f02b19f
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 7:05:56 PM
    Updated      : 12/18/2019 7:05:56 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 3fd65c1719c74997984648de18a1fa0e
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/3fd65c1719c74997984648de18a1fa0e
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:05:56 AM
    Updated      : 12/17/2019 5:05:56 AM
    Content Type : user credentials
    Tags         : 
    ```

### <a name="set-new-registry-username-and-password"></a>Festlegen eines neuen Registrierungsbenutzernamens und eines entsprechenden Kennworts

1.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten und `Import-Module .\pre-reqs.ps1` aus dem Ordner „scripts“.

2.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus, um ein neues Geheimnis für den neuen Benutzernamen und das Kennwort zu erstellen:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <newusername> `
        -RegistryUserPassword <newpassword> 
    ```
    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin1 `
        -RegistryUserPassword password1
    
    Check if key vault secret name (admin1) exists.
    Creating key vault secret name (admin1) as it does not exist. 
    ```

1.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus, um zu überprüfen, ob ein neues Geheimnis erstellt wurde:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName \<KeyVaultName> -Name \<username>
    ```

    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin1
    
    
    Vault Name   : newregkv
    Name         : admin1
    Version      : 2ae9a7239f4044be82ca9d1e9b80e85a
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/admin1/2ae9a7239f4044be82ca9d1e9b80e85a
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:28:18 PM
    Updated      : 12/18/2019 11:28:18 PM
    Content Type : user credentials
    Tags         : 
    ```

> [!Important]  
> Wenn Sie ein neues Geheimnis erstellen (Kombination aus Benutzername und Kennwort), müssen Sie das alte Key Vault-Geheimnis löschen. Wenn Sie die vorhandene Containerregistrierungsvorlage erneut bereitstellen, ohne das alte Geheimnis zu löschen, sind sowohl die alte als auch die neue Kombination aus Benutzername und Kennwort für das Anmelden bei der Registrierung gültig.

### <a name="update-the-ssl-certificate-for-existing-key-vault-secret"></a>Aktualisieren des SSL-Zertifikats für vorhandenes Key Vault-Geheimnis

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

    ```powershell
    Set-CertificateSecret -KeyVaultName \<keyvaultname> `
     -CertificateSecretName \<originalsecretnameforcertificate> `
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <originalsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    ```

    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    Check if key vault secret name (containersecret) exists.
    Creating key vault secret name (containersecret) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourceGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61
    ---------------------------------------------------------------- 
    ```

1.  Sie verwenden die Werte, die von dieser Funktion erzeugt werden, wenn Sie die vorhandene Containerregistrierungsvorlage erneut bereitstellen.

2.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus, um zu überprüfen, ob eine neue Version des vorhandenen Geheimnisses erstellt wurde:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName <KeyVaultName> -Name <secretname>
    ```

    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name containersecret -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : a07ece6b9914408e8f20c516e15b66c9
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:46:28 PM
    Updated      : 12/18/2019 11:46:28 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : 0199c7ec1d8d41bb9ddff0f39dca9931
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/0199c7ec1d8d41bb9ddff0f39dca9931
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:06:03 AM
    Updated      : 12/17/2019 5:06:03 AM
    Content Type : pfx
    Tags         : 
    ```

### <a name="set-a-new-ssl-certificate-for-the-container-registry-template"></a>Festlegen eines neuen SSL-Zertifikats für die Containerregistrierungsvorlage

1.  Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

    ```powershell  
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <newsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword>
    ```

    Dieses Cmdlet gibt z. B. die folgende Ausgabe zurück:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts>    Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret121719 `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> 
    Check if key vault secret name (containersecret121719) exists.
    Creating key vault secret name (containersecret121719) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourc
    eGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.c
    om:443/secrets/containersecret121719/bb2cfe4df7bc4fbe854a00799afa8566
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61 
    ```

## <a name="redeploy-existing-container-registry-template"></a>Erneutes Bereitstellen einer vorhandenen Containerregistrierungsvorlage

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

2.  Navigieren Sie zu der Ressourcengruppe, in der die Containerregistrierungsvorlagen-VM bereitgestellt wird.

    ![Resource group](./media/container-registry-template-rotating-secrets-tzl/resource-group.png)

3. Wählen Sie unter **Bereitstellungen** die Bereitstellungen aus.

    ![Screenshot: Seite „Bereitstellungen“](./media/container-registry-template-rotating-secrets-tzl/deployments.png)

4.  Wenn Sie zum ersten Mal Geheimnisse rotieren, wählen Sie die ursprüngliche Bereitstellung aus. Wenn dies nicht die erste Geheimnisrotation ist, wählen Sie die neueste Bereitstellung aus, und klicken Sie dann auf **Erneut bereitstellen**.

    ![Screenshot: Seite „Übersicht“ mit ausgewählter Aktion „Erneut bereitstellen“](./media/container-registry-template-rotating-secrets-tzl/redeploy.png)

5.  Wählen Sie unter **Lösungsvorlage bereitstellen** bei „Ressourcengruppe“ die Option **Vorhandene verwenden** aus, und wählen Sie die Ressourcengruppe aus, die bei der ursprünglichen Bereitstellung der Containerregistrierungsvorlage verwendet wurde. Damit eine erneute Bereitstellung erfolgreich ist, muss sie dieselbe Ressourcengruppe verwenden.

    ![Lösungsvorlage bereitstellen](./media/container-registry-template-rotating-secrets-tzl/deploy-solution-template.png)

6.  Überprüfen Sie unter **Parameter**, ob die Parameter denen bei der ursprünglichen Bereitstellung entsprechen. Die Client-ID des Dienstprinzipals und das Dienstprinzipalgeheimnis müssen hinzugefügt werden.

    - Wenn Sie nur den Benutzernamen und das Kennwort für den Registrierungsdienst rotieren, müssen Sie lediglich die Dienstprinzipalparameter hinzufügen.

    - Wenn Sie das Zertifikat rotieren, müssen Sie die neuen Werte für PFXKeyVaultSecretURL und PFXThumbprint eingeben, die beim Festlegen der neuen Geheimnisse ausgegeben wurden.

    ![Screenshot: Fenster „Parameter“](./media/container-registry-template-rotating-secrets-tzl/parameters.png)

7.  Wählen Sie **OK** und dann **Erstellen** aus. Die erneute Bereitstellung wird fortgesetzt. Während der erneuten Bereitstellung funktioniert die Registrierungsfunktionalität weiterhin.

    - Wenn Sie Benutzername und Kennwort rotieren, müssen Sie sich nach Abschluss der erneuten Bereitstellung noch mal bei der Registrierung authentifizieren.

    - Wenn Sie das Zertifikat rotieren, sollte es zu keinem Verlust des Zugriffs auf die Registrierung kommen. Hierbei wird davon ausgegangen, dass Sie ein Zertifikat von einem vertrauenswürdigen Zertifikatanbieter verwenden. Wenn Sie ein privates Zertifikat verwenden, muss dieses Zertifikat auf Clients installiert sein, um einen Verlust des Zugriffs zu verhindern.

## <a name="next-steps"></a>Nächste Schritte

[Der Azure Stack-Marketplace – Übersicht](../../operator/azure-stack-marketplace.md)
