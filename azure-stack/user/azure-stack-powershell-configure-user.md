---
title: Herstellen einer Verbindung mit Azure Stack über PowerShell als Benutzer | Microsoft-Dokumentation
description: Schritte zum Herstellen einer Verbindung mit Azure Stack über PowerShell.
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
ms.date: 04/26/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 855d7c03f4a18c4409d36b8ac5fd702c8549e413
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986247"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Herstellen einer Verbindung mit Azure Stack über PowerShell als Benutzer

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können eine Verbindung mit Azure Stack über PowerShell herstellen. Sie müssen eine Verbindung herstellen, um Azure Stack-Ressourcen mit PowerShell zu verwalten. Beispielsweise können Sie PowerShell verwenden, um Angebote zu abonnieren, virtuelle Computer zu erstellen und Azure Resource Manager-Vorlagen bereitzustellen.

Bei der Einrichtung ist Folgendes zu beachten:
  - Stellen Sie sicher, dass die Voraussetzungen erfüllt sind.
  - Stellen Sie eine Verbindung mit Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) her. 
  - Registrieren Sie Ressourcenanbieter.
  - Testen Sie die Verbindung.

## <a name="prerequisites-to-connect-using-powershell"></a>Voraussetzungen für das Herstellen einer Verbindung über PowerShell

Konfigurieren Sie diese Voraussetzungen über das [Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder auf einem Windows-basierten externen Client, sofern [eine VPN-Verbindung](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) besteht:

* Installieren Sie [mit Azure Stack kompatible Azure PowerShell-Module](../operator/azure-stack-powershell-install.md).
* Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack benötigt werden](../operator/azure-stack-powershell-download.md).

Sie müssen die folgenden Skriptvariablen durch Werte der Azure Stack-Konfiguration ersetzen:

- **Name des Azure AD-Mandanten**  
  Der Name des zum Verwalten von Azure Stack verwendeten Azure AD-Mandanten, z.B. ihrverzeichnis.onmicrosoft.com.
- **Azure Resource Manager-Endpunkt**  
  Beim Azure Stack Development Kit wird dieser Wert auf https://management.local.azurestack.external festgelegt. Um diesen Wert für integrierte Azure Stack-Systeme zu erhalten, wenden Sie sich an Ihren Dienstanbieter.

## <a name="connect-with-azure-ad"></a>Herstellen einer Verbindung mit Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Herstellen einer Verbindung mit AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
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

## <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit PowerShell die Konnektivität testen, um Ressourcen in Azure Stack zu erstellen. Erstellen Sie als Test eine Ressourcengruppe für eine Anwendung, und fügen Sie einen virtuellen Computer hinzu. Führen Sie den folgenden Befehl aus, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln von Vorlagen für Azure Stack](azure-stack-develop-templates.md)
- [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
- [PowerShell-Modulreferenz für Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Wenn Sie PowerShell für die Umgebung des Cloudbetreibers einrichten möchten, finden Sie weitere Informationen im Artikel [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](../operator/azure-stack-powershell-configure-admin.md).
