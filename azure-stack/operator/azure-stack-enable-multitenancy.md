---
title: Konfigurieren der Mehrinstanzenfähigkeit in Azure Stack Hub
description: Hier erfahren Sie, wie Sie die Mehrinstanzfähigkeit für Azure Active Directory-Gastmandanten in Azure Stack Hub konfigurieren.
author: BryanLa
ms.topic: how-to
ms.date: 01/26/2021
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 01/26/2021
ms.openlocfilehash: 3de6c5db42285f90e1d4ce6c1ebf6736d7ce4863
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975944"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Konfigurieren der Mehrinstanzenfähigkeit in Azure Stack Hub

Sie können Azure Stack Hub so konfigurieren, dass Anmeldungen von Benutzern aus anderen Azure AD-Verzeichnissen (Azure Active Directory) zugelassen werden. Dadurch können diese die Dienste in Azure Stack Hub nutzen. Diese Verzeichnisse stehen in einer Gastbeziehung zu Ihrer Azure Stack Hub-Instanz und gelten daher als Azure AD-Gastmandanten. Betrachten Sie beispielsweise das folgende Szenario:

- Sie sind der Dienstadministrator von contoso.onmicrosoft.com, dem Azure AD-Stammmandanten für Identitäts- und Zugriffsverwaltungsdienste für Ihre Azure Stack Hub-Instanz.
- Mary ist Verzeichnisadministratorin von fabrikam.onmicrosoft.com, dem Azure AD-Gastmandanten, auf dem sich die Gastbenutzer befinden.
- Marys Arbeitgeber (Fabrikam) setzt IaaS- und PaaS-Dienste Ihres Unternehmens ein. Fabrikam möchte, dass Benutzer aus dem Gastverzeichnis (fabrikam.onmicrosoft.com) sich anmelden und von contoso.onmicrosoft.com geschützte Azure Stack Hub-Ressourcen verwenden können.

In dieser Anleitung finden Sie die für dieses Szenario erforderlichen Schritte, um die Mehrinstanzenfähigkeit für Gastverzeichnismandanten in Azure Stack Hub zu aktivieren oder zu deaktivieren. Sie und Mary erreichen dieses Ziel, indem Sie das Gastverzeichnis registrieren oder die Registrierung aufheben. Dadurch werden Anmeldungen bei Azure Stack Hub und die Nutzung von Azure Stack Hub-Diensten durch Fabrikam-Benutzer aktiviert bzw. deaktiviert. 

Als Cloud Solution Provider (CSP) haben Sie zusätzliche Möglichkeiten, einen [Azure Stack Hub für mehrere Mandanten zu konfigurieren und zu verwalten](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="prerequisites"></a>Voraussetzungen

Bevor ein Gastverzeichnis registriert bzw. dessen Registrierung aufgehoben wird, müssen Sie und Mary einige Verwaltungsschritte für die jeweiligen Azure AD-Mandanten vornehmen: das Azure Stack Hub-Stammverzeichnis (Contoso) und das Gastverzeichnis (Fabrikam):

 - [Installieren](powershell-install-az-module.md) und [Konfigurieren](azure-stack-powershell-configure-admin.md) von PowerShell für die Verwendung mit Azure Stack Hub
 - [Laden Sie die Azure Stack Hub-Tools herunter](azure-stack-powershell-download.md), und importieren Sie die Module „Connect“ und „Identity“:

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

## <a name="register-a-guest-directory"></a>Registrieren eines Gastverzeichnisses

Damit Sie ein Gastverzeichnis für die Mehrinstanzfähigkeit registrieren können, müssen das Azure Stack Hub-Stammverzeichnis und das Gastverzeichnis konfiguriert werden.

#### <a name="configure-azure-stack-hub-directory"></a>Konfigurieren des Azure Stack Hub-Verzeichnisses

Als Dienstadministrator von contoso.onmicrosoft.com müssen Sie zunächst ein Onboarding für den Fabrikam-Gastverzeichnismandanten in Azure Stack Hub durchführen. Das folgende Skript konfiguriert den Azure Resource Manager so, dass Anmeldungen von Benutzern und Dienstprinzipalen des Mandanten fabrikam.onmicrosoft.com akzeptiert werden:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest directory tenant. 
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

#### <a name="configure-guest-directory"></a>Konfigurieren des Gastverzeichnisses

Als Nächstes muss Mary, die Verzeichnisadministratorin von Fabrikam, Azure Stack Hub beim Gastverzeichnis fabrikam.onmicrosoft.com registrieren, indem sie das folgende Skript ausführt:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest directory tenant.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Wenn Ihr Azure Stack Hub-Administrator künftig neue Dienste oder Updates installiert, müssen Sie dieses Skript möglicherweise erneut ausführen.
>
> Sie können dieses Skript zu einem beliebigen Zeitpunkt erneut ausführen, um den Status der Azure Stack Hub-Apps in Ihrem Verzeichnis zu überprüfen.
>
> Wenn Sie Probleme beim Erstellen von virtuellen Computern in Managed Disks festgestellt haben (eingeführt im Update 1808), wurde ein neuer **Datenträger-Ressourcenanbieter** hinzugefügt, der eine erneute Ausführung dieses Skripts erfordert.

### <a name="direct-users-to-sign-in"></a>Weiterleiten von Benutzern zur Anmeldung

Schließlich kann Mary Fabrikam-Benutzer mit @fabrikam.onmicrosoft.com-Konten zur Anmeldung weiterleiten, indem diese das [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md) aufrufen. Für Systeme mit mehreren Knoten wird das URL-Format `https://management.<region>.<FQDN>` für das Benutzerportal benötigt. Für ASDK-Bereitstellungen lautet die URL `https://portal.local.azurestack.external`.

Mary muss außerdem alle fremden Prinzipale (Benutzer im Fabrikam-Verzeichnis ohne das Suffix fabrikam.onmicrosoft.com) weiterleiten, damit diese sich über `https://<user-portal-url>/fabrikam.onmicrosoft.com` anmelden. Wenn diese Benutzer den Verzeichnismandanten `/fabrikam.onmicrosoft.com` nicht in der URL angeben, werden sie an ihr Standardverzeichnis weitergeleitet und erhalten eine Fehlermeldung mit dem Hinweis, dass ihr Administrator nicht zugestimmt hat.

## <a name="unregister-a-guest-directory"></a>Aufheben der Registrierung eines Gastverzeichnisses

Wenn Sie Anmeldungen bei Azure Stack Hub-Diensten nicht mehr über Gastverzeichnismandanten zulassen möchten, können Sie die Registrierung des Verzeichnisses aufheben. Auch in diesem Fall müssen das Azure Stack Hub-Stammverzeichnis und das Gastverzeichnis konfiguriert werden:

1. Als Administrator für das Gastverzeichnis (Mary in diesem Szenario) führen Sie `Unregister-AzsWithMyDirectoryTenant` aus. Das Cmdlet deinstalliert alle Azure Stack Hub-Apps aus dem neuen Verzeichnis.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest directory tenant.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Der Dienstadministrator von Azure Stack Hub (Sie in diesem Szenario) muss das Cmdlet `Unregister-AzSGuestDirectoryTenant` ausführen:

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest directory tenant. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant resource was created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Die Schritte zur Deaktivierung der Mehrinstanzenfähigkeit müssen in dieser Reihenfolge ausgeführt werden. Schritt 1 schlägt fehl, wenn Schritt 2 zuerst abgeschlossen wird.

## <a name="retrieve-azure-stack-hub-identity-health-report"></a>Abrufen des Berichts zur Azure Stack Hub-Identitätsintegrität 

Ersetzen Sie die Platzhalter `<region>`, `<domain>` und `<homeDirectoryTenant>`, und führen Sie dann das folgende Cmdlet als Azure Stack Hub-Administrator aus:

```powershell

$AdminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
$DirectoryName = "<homeDirectoryTenant>.onmicrosoft.com"
$healthReport = Get-AzsHealthReport -AdminResourceManagerEndpoint $AdminResourceManagerEndpoint -DirectoryTenantName $DirectoryName
Write-Host "Healthy directories: "
$healthReport.directoryTenants | Where status -EQ 'Healthy' | Select -Property tenantName,tenantId,status | ft


Write-Host "Unhealthy directories: "
$healthReport.directoryTenants | Where status -NE 'Healthy' | Select -Property tenantName,tenantId,status | ft
```

## <a name="update-azure-ad-tenant-permissions"></a>Aktualisieren von Azure AD-Mandantenberechtigungen

Durch die folgende Aktion wird eine Warnung in Azure Stack Hub gelöscht, die darauf hinweist, dass ein Verzeichnis aktualisiert werden muss. Führen Sie im Ordner **Azurestack-tools-master/identity** den folgenden Befehl aus:

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack Hub is registered to:
$homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com"

Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
   -DirectoryTenantName $homeDirectoryTenantName -Verbose
```

Sie werden vom Skript zur Eingabe von Administratoranmeldeinformationen im Azure AD-Mandanten aufgefordert. Die Ausführung dauert einige Minuten. Die Warnung sollte nach dem Ausführen des Cmdlets nicht mehr angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten delegierter Anbieter](azure-stack-delegated-provider.md)
- [Wichtige Konzepte in Azure Stack Hub](azure-stack-overview.md)
- [Verwalten der Nutzung und Abrechnung für Azure Stack Hub als Cloud Solution Provider](azure-stack-add-manage-billing-as-a-csp.md)
- [Hinzufügen eines Mandanten für Nutzung und Abrechnung zu Azure Stack Hub](azure-stack-csp-howto-register-tenants.md)
