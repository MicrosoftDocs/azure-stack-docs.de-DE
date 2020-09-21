---
title: Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Benutzer
description: Enthält Informationen zum Herstellen einer Verbindung mit Azure Stack Hub über PowerShell, um die interaktive Eingabeaufforderung zu verwenden oder Skripts zu schreiben.
author: mattbriggs
ms.topic: article
ms.date: 8/4/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 8/4/2020
ms.openlocfilehash: 0539bd452db54b298f681fc47ba7b9183ba75202
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90574022"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Benutzer

Sie können eine Verbindung mit Azure Stack Hub über PowerShell herstellen, um Azure Stack-Ressourcen zu verwalten. Beispielsweise können Sie PowerShell verwenden, um Angebote zu abonnieren, virtuelle Computer (VMs) zu erstellen und Azure Resource Manager-Vorlagen bereitzustellen.

Führen Sie zum Einrichten die folgenden Schritte aus:
  - Stellen Sie sicher, dass die Voraussetzungen erfüllt sind.
  - Stellen Sie eine Verbindung mit Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) her. 
  - Registrieren Sie Ressourcenanbieter.
  - Testen Sie die Verbindung.

## <a name="prerequisites-to-connecting-with-powershell"></a>Voraussetzungen für das Herstellen einer Verbindung mit PowerShell

Konfigurieren Sie diese Voraussetzungen über das [Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder auf einem Windows-basierten externen Client, sofern eine [VPN-Verbindung](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) besteht:

* Installieren Sie [mit Azure Stack Hub kompatible Azure PowerShell-Module](../operator/azure-stack-powershell-install.md).
* Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](../operator/azure-stack-powershell-download.md).

Sie müssen die folgenden Skriptvariablen durch Werte der Azure Stack Hub-Konfiguration ersetzen:

- **Name des Azure AD-Mandanten**  
  Der Name Ihres Azure AD-Mandanten, der zum Verwalten von Azure Stack Hub verwendet wird. Beispiel: „yourdirectory.onmicrosoft.com“.
- **Azure Resource Manager-Endpunkt**  
  Beim Azure Stack Development Kit wird dieser Wert auf `https://management.local.azurestack.external` festgelegt. Um diesen Wert für integrierte Azure Stack Hub-Systeme zu erhalten, wenden Sie sich an Ihren Dienstanbieter.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Herstellen einer Verbindung mit Azure Stack Hub über Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Herstellen einer Verbindung mit Azure Stack Hub über AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Registrieren von Ressourcenanbietern

Ressourcenanbieter werden nicht automatisch für neue Benutzerabonnements registriert, für die über das Portal keine Ressourcen bereitgestellt wurden. Sie können explizit einen Ressourcenanbieter registrieren, indem Sie das folgende Skript ausführen:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie die Konnektivität testen, indem Sie PowerShell zum Erstellen von Ressourcen in Azure Stack Hub verwenden. Erstellen Sie testweise eine Ressourcengruppe für eine Anwendung, und fügen Sie eine VM hinzu. Führen Sie den folgenden Befehl aus, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln von Vorlagen für Azure Stack Hub](azure-stack-develop-templates.md)
- [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
- [PowerShell-Modulreferenz für Azure Stack Hub](/powershell/azure/azure-stack/overview)
- Wenn Sie PowerShell für die Umgebung des Cloud-Operators einrichten möchten, finden Sie weitere Informationen im Artikel zum [Konfigurieren der PowerShell-Umgebung für den Azure Stack Hub-Operator](../operator/azure-stack-powershell-configure-admin.md).
