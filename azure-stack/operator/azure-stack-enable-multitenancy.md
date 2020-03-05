---
title: Konfigurieren der Mehrinstanzenfähigkeit in Azure Stack Hub
description: Erfahren Sie, wie Sie mehrere Azure Active Directory-Mandanten in Azure Stack Hub aktivieren und deaktivieren.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 11d0ce3e676516eff6ab6aca344178556cdab2e6
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699914"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Konfigurieren der Mehrinstanzenfähigkeit in Azure Stack Hub

Sie können Azure Stack Hub so konfigurieren, dass Benutzer mehrerer Azure AD-Mandanten (Azure Active Directory) Dienste in Azure Stack Hub verwenden können. Betrachten Sie beispielsweise das folgende Szenario:

- Sie sind Dienstadministrator von „contoso.onmicrosoft.com“, dort ist Azure Stack Hub installiert.
- Mary ist Verzeichnisadministratorin von „fabrikam.onmicrosoft.com“, wo sich Gastbenutzer befinden.
- Marys Unternehmen bezieht IaaS- und PaaS-Dienste von Ihrem Unternehmen und muss es Benutzern aus dem Gastverzeichnis (fabrikam.onmicrosoft.com) ermöglichen, sich bei Azure Stack Hub-Ressourcen in „contoso.onmicrosoft.com“ anzumelden und diese zu verwenden.

Der vorliegende Leitfaden beschreibt die erforderlichen Schritte – im Kontext dieses Szenarios –, um die Mehrinstanzenfähigkeit in Azure Stack Hub zu konfigurieren. In diesem Szenario müssen sowohl Sie als auch Mary einige Schritte ausführen, um es Benutzern von Fabrikam zu ermöglichen, sich bei der Azure Stack Hub-Bereitstellung in Contoso anzumelden und dort Dienste zu nutzen.

## <a name="enable-multi-tenancy"></a>Aktivieren der Mehrinstanzenfähigkeit

Damit Sie die Mehrinstanzenfähigkeit in Azure Stack Hub konfigurieren können, müssen einige Voraussetzungen erfüllt sein:
  
 - Mary und Sie müssen Verwaltungsschritte sowohl in dem Verzeichnis, in dem Azure Stack Hub installiert ist (Contoso), als auch im Gastverzeichnis (Fabrikam) koordinieren.
 - Stellen Sie sicher, dass Sie PowerShell für Azure Stack Hub [installiert](azure-stack-powershell-install.md) und [konfiguriert](azure-stack-powershell-configure-admin.md) haben.
 - [Laden Sie die Azure Stack Hub-Tools herunter](azure-stack-powershell-download.md), und importieren Sie die Module „Connect“ und „Identity“:

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>Konfigurieren des Azure Stack Hub-Verzeichnisses

In diesem Abschnitt konfigurieren Sie Azure Stack Hub so, dass Anmeldungen aus Fabrikam-Azure AD-Verzeichnismandanten zugelassen werden.

Integrieren Sie den Gastverzeichnismandanten (Fabrikam) in Azure Stack Hub, indem Sie Azure Resource Manager so konfigurieren, dass Benutzer und Dienstprinzipale aus dem Gastverzeichnismandanten akzeptiert werden.

Der Dienstadministrator von „contoso.onmicrosoft.com“ führt die folgenden Befehle aus:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
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

Nachdem der Azure Stack Hub-Operator die Verwendung des Fabrikam-Verzeichnisses mit Azure Stack Hub aktiviert hat, muss Mary Azure Stack Hub beim Verzeichnismandanten von Fabrikam registrieren.

#### <a name="registering-azure-stack-hub-with-the-guest-directory"></a>Registrieren von Azure Stack Hub beim Gastverzeichnis

Mary (Verzeichnisadministratorin von Fabrikam) führt im Gastverzeichnis „fabrikam.onmicrosoft.com“ die folgenden Befehle aus:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Wenn Ihr Azure Stack Hub-Administrator künftig neue Dienste oder Updates installiert, müssen Sie dieses Skript unter Umständen erneut ausführen.
>
> Sie können dieses Skript zu einem beliebigen Zeitpunkt erneut ausführen, um den Status der Azure Stack Hub-Apps in Ihrem Verzeichnis zu überprüfen.
>
> Wenn Sie Probleme beim Erstellen von virtuellen Computern in Managed Disks festgestellt haben (eingeführt im Update 1808), wurde ein neuer **Datenträger-Ressourcenanbieter** hinzugefügt, der eine erneute Ausführung dieses Skripts erfordert.

### <a name="direct-users-to-sign-in"></a>Weiterleiten von Benutzern zur Anmeldung

Nachdem Mary und Sie die Schritte zum Integrieren von Marys Verzeichnis abgeschlossen haben, kann Mary Fabrikam-Benutzer zur Anmeldung weiterleiten. Fabrikam-Benutzer (mit dem Suffix „fabrikam.onmicrosoft.com“) melden sich hier an: „https\://portal.local.azurestack.external“.

Mary leitet alle [fremden Prinzipale](/azure/role-based-access-control/rbac-and-directory-admin-roles) im Fabrikam-Verzeichnis (Benutzer im Fabrikam-Verzeichnis ohne das Suffix „fabrikam.onmicrosoft.com“) zur Anmeldung an diese Adresse weiter: „https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com“. Wenn diese Benutzer diese URL nicht verwenden, werden sie an ihr Standardverzeichnis (Fabrikam) weitergeleitet und erhalten eine Fehlermeldung mit dem Hinweis, dass ihr Administrator nicht zugestimmt hat.

## <a name="disable-multi-tenancy"></a>Deaktivieren der Mehrinstanzenfähigkeit

Wenn Sie in Azure Stack Hub nicht länger mehrere Mandanten benötigen, können Sie die Mehrinstanzenfähigkeit deaktivieren, indem Sie die folgenden Schritte in der angegebenen Reihenfolge ausführen:

1. Als Administrator für das Gastverzeichnis (in diesem Szenario Mary) führen Sie *Unregister-AzsWithMyDirectoryTenant* aus. Das Cmdlet deinstalliert alle Azure Stack Hub-Apps aus dem neuen Verzeichnis.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Als Dienstadministrator von Azure Stack Hub (in diesem Szenario also Sie) führen Sie *Unregister-AzSGuestDirectoryTenant* aus.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
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
- [Wichtige Konzepte in Azure Stack Hub](azure-stack-overview.md)
- [Verwalten der Nutzung und Abrechnung für Azure Stack Hub als Cloud Solution Provider](azure-stack-add-manage-billing-as-a-csp.md)
- [Hinzufügen eines Mandanten für Nutzung und Abrechnung zu Azure Stack Hub](azure-stack-csp-howto-register-tenants.md)
