---
title: Herstellen einer Verbindung mit Azure Stack Hub über PowerShell
description: Enthält Informationen zum Herstellen einer Verbindung mit Azure Stack Hub über PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 96858ce40e5839f3a5bcff55a11467155ebdc15e
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488364"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Herstellen einer Verbindung mit Azure Stack Hub über PowerShell

Sie können Azure Stack Hub für die Verwendung von PowerShell zum Verwalten von Ressourcen konfigurieren, z. B. die Erstellung von Angeboten, Plänen, Kontingenten und Warnungen. In diesem Thema können Sie die Betreiberumgebung konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die folgenden erforderlichen Schritte entweder über das [Azure Stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) oder auf einem Windows-basierten externen Client aus, sofern [eine VPN-Verbindung mit dem ASDK](../asdk/asdk-connect.md#connect-with-vpn) besteht.

- Installieren Sie [mit Azure Stack Hub kompatible Azure PowerShell-Module](azure-stack-powershell-install.md).  
- Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Herstellen einer Verbindung mit Azure AD

Sie können die Azure Stack Hub-Operatorumgebung mit PowerShell konfigurieren, indem Sie eins der Skripts unten ausführen. Ersetzen Sie darin den Azure Active Directory (Azure AD) tenantName und die Endpunktwerte von Azure Resource Manager durch ihre eigene Umgebungskonfiguration.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Herstellen einer Verbindung mit AD FS

Stellen Sie mit Azure Active Directory-Verbunddiensten (Azure AD FS) die Verbindung der Azure Stack Hub-Operatorumgebung mit PowerShell her. Für das ASDK wird dieser Azure Resource Manager-Endpunkt auf `https://adminmanagement.local.azurestack.external` festgelegt. Um den Azure Resource Manager-Endpunkt für integrierte Azure Stack Hub-Systeme zu erhalten, wenden Sie sich an Ihren Dienstanbieter.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS unterstützt nur die interaktive Authentifizierung mit Benutzeridentitäten. Wenn ein Objekt mit Anmeldeinformationen erforderlich ist, müssen Sie einen Dienstprinzipalnamen (SPN) verwenden. Weitere Informationen zum Einrichten eines Dienstprinzipals mit Azure Stack Hub und AD FS als Identitätsverwaltungsdienst finden Sie unter [Verwalten einer AD FS-App-Identität](azure-stack-create-service-principals.md#manage-an-ad-fs-app-identity).

## <a name="test-the-connectivity"></a>Testen der Konnektivität

Nachdem Sie nun alles eingerichtet haben, können Sie mit PowerShell Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und eine VM hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen **MyResourceGroup** zu erstellen.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln von Vorlagen für Azure Stack Hub](../user/azure-stack-develop-templates.md)
- [Bereitstellen von Vorlagen mit PowerShell](../user/azure-stack-deploy-template-powershell.md)
  - [Azure Stack Hub-Modulreferenz](/powershell/azure/azure-stack/overview)
