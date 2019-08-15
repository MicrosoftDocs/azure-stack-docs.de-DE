---
title: Mehrinstanzenfähigkeit in Azure Stack
description: Erfahren Sie, wie Sie mehrere Azure Active Directory-Verzeichnisse in Azure Stack unterstützen.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 3b06945eefcdb8cb51ffea2e9c4d626d538f3202
ms.sourcegitcommit: 94669fe8a55fadd3103e80be307e9e8c823bf746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68940237"
---
# <a name="multi-tenancy-in-azure-stack"></a>Mehrinstanzenfähigkeit in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können Azure Stack so konfigurieren, dass Benutzer aus mehreren Azure Active Directory-Mandanten (Azure AD) Dienste in Azure Stack verwenden können. Betrachten Sie beispielsweise das folgende Szenario:

- Sie sind Dienstadministrator von contoso.onmicrosoft.com, worunter Azure Stack installiert ist.
- Mary ist Verzeichnisadministrator von fabrikam.onmicrosoft.com, wo sich Gastbenutzer befinden.
- Marys Unternehmen bezieht IaaS- und PaaS-Dienste von Ihrem Unternehmen und muss es Benutzern aus dem Gastverzeichnis (fabrikam.onmicrosoft.com) ermöglichen, sich bei Azure Stack-Ressourcen in contoso.onmicrosoft.com anzumelden und diese zu verwenden.

Der vorliegende Leitfaden beschreibt die erforderlichen Schritte – im Kontext dieses Szenarios –, um die Mehrinstanzenfähigkeit in Azure Stack zu konfigurieren. In diesem Szenario müssen sowohl Sie als auch Mary einige Schritte ausführen, um es Benutzern von Fabrikam zu ermöglichen, sich bei der Azure Stack-Bereitstellung in Contoso anzumelden und dort Dienste zu nutzen.  

## <a name="enable-multi-tenancy"></a>Aktivieren der Mehrinstanzenfähigkeit

Bevor Sie die Mehrinstanzenfähigkeit in Azure Stack konfigurieren können, müssen einige Voraussetzungen erfüllt werden:
  
 - Mary und Sie müssen Verwaltungsschritte sowohl in dem Verzeichnis, in dem Azure Stack installiert ist (Contoso), als auch im Gastverzeichnis (Fabrikam) koordinieren.  
 - Stellen Sie sicher, dass Sie die PowerShell für Azure Stack [installiert](azure-stack-powershell-install.md) und [konfiguriert](azure-stack-powershell-configure-admin.md) haben.
 - [Laden Sie die Azure Stack-Tools](azure-stack-powershell-download.md) herunter, und importieren Sie die Module „Connect“ und „Identity“:

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Konfigurieren des Azure Stack-Verzeichnisses

In diesem Abschnitt konfigurieren Sie Azure Stack so, dass Anmeldungen aus Fabrikam-Azure AD-Verzeichnismandanten zugelassen werden.

Integrieren Sie den Gastverzeichnismandanten (Fabrikam) in Azure Stack, indem Sie Azure Resource Manager so konfigurieren, dass Benutzer und Dienstprinzipale aus dem Gastverzeichnismandanten akzeptiert werden.

Der Dienstadministrator von contoso.onmicrosoft.com führt die folgenden Befehle aus.

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

### <a name="configure-guest-directory"></a>Konfigurieren des Gastverzeichnisses

Nachdem der Azure Stack-Administrator bzw. -Bediener das Fabrikam-Verzeichnis für die Verwendung mit Azure Stack aktiviert hat, muss Mary Azure Stack für den Verzeichnismandanten von Fabrikam registrieren.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrieren von Azure Stack beim Gastverzeichnis

Mary, die Verzeichnisadministratorin von Fabrikam, führt im Gastverzeichnis „fabrikam.onmicrosoft.com“ die folgenden Befehle aus.

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Wenn Ihr Azure Stack-Administrator künftig neue Dienste oder Updates installiert, müssen Sie dieses Skript möglicherweise erneut ausführen.
>
> Führen Sie dieses Skript zu einem beliebigen Zeitpunkt erneut aus, um den Status der Azure Stack-Anwendungen in Ihrem Verzeichnis zu überprüfen.
>
> Wenn Sie Probleme beim Erstellen von virtuellen Computern in Managed Disks festgestellt haben (eingeführt im Update 1808): Es wurde ein neuer **Datenträger-Ressourcenanbieter** hinzugefügt, der eine erneute Ausführung dieses Skripts erfordert.

### <a name="direct-users-to-sign-in"></a>Weiterleiten von Benutzern zur Anmeldung

Nachdem Mary und Sie die Schritte zum Integrieren von Marys Verzeichnis abgeschlossen haben, kann Mary Fabrikam-Benutzer zur Anmeldung weiterleiten.  Fabrikam-Benutzer (also Benutzer mit dem Suffix „fabrikam.onmicrosoft.com“) melden sich hier an: https\://portal.local.azurestack.external.  

Mary leitet alle [fremden Prinzipale](/azure/role-based-access-control/rbac-and-directory-admin-roles) im Fabrikam-Verzeichnis (also Benutzer im Fabrikam-Verzeichnis ohne das Suffix „fabrikam.onmicrosoft.com“) zur Anmeldung an diese Adresse weiter: https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Wenn diese Benutzer diese URL nicht verwenden, werden sie an ihr Standardverzeichnis (Fabrikam) weitergeleitet und erhalten eine Fehlermeldung mit dem Hinweis, dass ihr Administrator nicht zugestimmt hat.

## <a name="disable-multi-tenancy"></a>Deaktivieren der Mehrinstanzenfähigkeit

Wenn Sie in Azure Stack nicht länger mehrere Mandanten benötigen, können Sie die Mehrinstanzenfähigkeit deaktivieren, indem Sie die folgenden Schritte in dieser Reihenfolge ausführen:

1. Als Administrator für das Gastverzeichnis (Mary in diesem Szenario) führen Sie *Unregister-AzsWithMyDirectoryTenant* aus. Das Cmdlet deinstalliert alle Azure Stack-Anwendungen aus dem neuen Verzeichnis.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Als Dienstadministrator von Azure Stack (Sie in diesem Szenario) führen Sie *Unregister-AzSGuestDirectoryTenant* aus. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Die Schritte zur Deaktivierung der Mehrinstanzenfähigkeit müssen in dieser Reihenfolge ausgeführt werden. Schritt 1 schlägt fehl, wenn Schritt 2 zuerst abgeschlossen wird.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten delegierter Anbieter](azure-stack-delegated-provider.md)
- [Schlüsselkonzepte für Azure Stack](azure-stack-overview.md)
- [Verwalten von Nutzung und Abrechnung für Azure Stack als Cloud-Dienstanbieter](azure-stack-add-manage-billing-as-a-csp.md)
- [Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack](azure-stack-csp-howto-register-tenants.md)
