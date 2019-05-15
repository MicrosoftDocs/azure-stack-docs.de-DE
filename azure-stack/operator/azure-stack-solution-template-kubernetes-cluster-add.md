---
title: Hinzufügen von Kubernetes zum Azure Stack-Marketplace | Microsoft Docs
description: Erfahren Sie, wie Sie dem Azure Stack-Marketplace Kubernetes hinzufügen.
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
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 09fa7b503f0c594d2af0c6f16a6d4618cec0fac3
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308478"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Hinzufügen von Kubernetes zum Azure Stack-Marketplace

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!note]  
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Das Szenario mit nicht verbundenem Azure Stack wird von der Preview zurzeit nicht unterstützt.

Sie können Kubernetes als ein Marketplace-Element für Ihre Benutzer anbieten. Ihre Benutzer können dann Kubernetes in einem einzelnen, koordinierten Vorgang bereitstellen.

Im folgenden Artikel wird das Bereitstellen der Ressourcen für einen eigenständigen Kubernetes-Cluster mithilfe einer Azure Resource Manager-Vorlage betrachtet. Bevor Sie beginnen, überprüfen Sie Ihren Azure Stack und Ihre globalen Azure-Mandanteneinstellungen. Sammeln Sie die erforderlichen Informationen zu Ihrem Azure Stack. Fügen Sie Ihrem Mandanten und dem Azure Stack Marketplace erforderliche Ressourcen hinzu. Der Cluster ist von einem Ubuntu-Server, einem benutzerdefinierten Skript und davon abhängig, dass das Marketplace-Element „Kubernetes-Cluster“ im Marketplace vorhanden ist.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Erstellen eines Plans, eines Angebots und eines Abonnements

Erstellen Sie einen Plan, ein Angebot und ein Abonnement für das Marketplace-Element für Kubernetes. Sie können auch einen vorhandenen Plan und ein vorhandenes Angebot verwenden.

1. Melden Sie sich beim [Verwaltungsportal](https://adminportal.local.azurestack.external) an.

1. Erstellen Sie einen Plan als Basisplan. Anweisungen hierzu finden Sie unter [Erstellen von Plänen in Azure Stack](azure-stack-create-plan.md).

1. Erstellen Sie ein Angebot. Anweisungen hierzu finden Sie unter [Erstellen von Angeboten in Azure Stack](azure-stack-create-offer.md).

1. Wählen Sie **Angebote** aus, und suchen Sie das Angebot, das Sie erstellt haben.

1. Wählen Sie auf dem Blatt „Angebot“ die Option **Übersicht** aus.

1. Wählen Sie **Status ändern** aus. Wählen Sie **Öffentlich** aus.

1. Wählen Sie **Ressource erstellen** > **Angebote und Pläne** > **Abonnement** aus, um ein Abonnement zu erstellen.

    a. Geben Sie einen **Anzeigenamen** ein.

    b. Geben Sie einen **Benutzer** ein. Verwenden Sie das Azure AD-Konto, das mit Ihrem Mandanten verknüpft ist.

    c. **Anbieterbeschreibung**

    d. Legen Sie **Verzeichnismandant** auf den Azure AD-Mandanten für Ihren Azure Stack fest. 

    e. Wählen Sie **Angebot** aus. Wählen Sie den Namen des Angebots aus, das Sie erstellt haben. Notieren Sie sich die Abonnement-ID.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Erstellen eines Dienstprinzipals und von Anmeldeinformationen in AD FS

Wenn Sie Active Directory-Verbunddienste (AD FS) als Ihren Identitätsverwaltungsdienst verwenden, müssen Sie einen Dienstprinzipal für Benutzer erstellen, die einen Kubernetes-Cluster bereitstellen.

1. Erstellen und exportieren Sie ein selbstsigniertes Zertifikat, das zum Erstellen des Dienstprinzipals verwendet wird. 

    - Sie benötigen die folgenden Informationen:

       | Wert | BESCHREIBUNG |
       | ---   | ---         |
       | Kennwort | Geben Sie ein neues Kennwort für das Zertifikat ein. |
       | Lokaler Zertifikatpfad | Geben Sie den Pfad und Dateiname des Zertifikats ein. Beispiel: `c:\certfilename.pfx` |
       | Zertifikatsname | Geben Sie den Namen des Zertifikats ein. |
       | Zertifikatspeicherort |  Zum Beispiel, `Cert:\LocalMachine\My` |

    - Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten. Führen Sie das folgende Skript mit den passenden Parameterwerten aus:

        ```powershell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  Notieren Sie sich die neue Zertifikat-ID, die in Ihrer PowerShell-Sitzung angezeigt wird, `1C2ED76081405F14747DC3B5F76BB1D83227D824`. Die ID wird verwendet, wenn Sie den Dienstprinzipal erstellen.

    ```powershell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. Erstellen Sie mithilfe des Zertifikats einen Dienstprinzipal.

    - Sie benötigen die folgenden Informationen:

       | Wert | BESCHREIBUNG                     |
       | ---   | ---                             |
       | ERCS IP | Im ASDK ist der privilegierte Endpunkt normalerweise `AzS-ERCS01`. |
       | Anwendungsname | Geben Sie einen einfachen Namen für den Anwendungsdienstprinzipal ein. |
       | Zertifikatspeicherort | Der Pfad auf Ihrem Computer, in dem Sie das Zertifikat gespeichert haben. Dieser wird durch den Speicherort und die Zertifikat-ID angezeigt, die im ersten Schritt generiert werden. Beispiel: `Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       Wenn Sie zur Eingabe aufgefordert werden, verwenden Sie die folgenden Anmeldeinformationen, um eine Verbindung mit dem privilegierten Endpunkt herzustellen. 
        - Benutzername: Geben Sie das CloudAdmin-Konto im Format `<Azure Stack domain>\cloudadmin` an. (Für ASDK lautet der Benutzername „azurestack\cloudadmin“.)
        - Password (Kennwort): Geben Sie das gleiche Kennwort ein, das während der Installation für das AzureStackAdmin-Domänenadministratorkonto bereitgestellt wurde.

    - Führen Sie das folgende Skript mit den passenden Parameterwerten aus:

        ```powershell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - Die Dienstprinzipaldetails sehen wie der Codeausschnitt unten aus.

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Fügen Sie ein Ubuntu-Serverimage hinzu.

Fügen Sie dem Marketplace das folgende Ubuntu-Serverimage hinzu:

1. Melden Sie sich beim [Verwaltungsportal](https://adminportal.local.azurestack.external) an.

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `Ubuntu Server` ein.

1. Wählen Sie die neueste Version des Servers aus. Überprüfen Sie die vollständige Version, und stellen Sie sicher, dass Sie über die neueste Version verfügen:
    - **Herausgeber**: Canonical
    - **Angebot**: UbuntuServer
    - **Version**: 16.04.201806120 (oder neueste Version)
    - **SKU**: 16.04-LTS

1. Wählen Sie **Herunterladen** aus.

## <a name="add-a-custom-script-for-linux"></a>Hinzufügen eines benutzerdefinierten Skripts für Linux

Fügen Sie Kubernetes aus dem Marketplace hinzu:

1. Öffnen Sie das [Verwaltungsportal](https://adminportal.local.azurestack.external).

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `Custom Script for Linux` ein.

1. Wählen Sie das Skript mit dem folgenden Profil aus:
   - **Angebot**: Custom Script for Linux 2.0
   - **Version**: 2.0.6 (oder neueste Version)
   - **Herausgeber**: Microsoft Corp.

     > [!Note]  
     > Möglicherweise werden mehrere Versionen des benutzerdefinierten Skripts für Linux aufgeführt. Sie müssen die neueste Version des Elements hinzufügen.

1. Wählen Sie **Herunterladen** aus.


## <a name="add-kubernetes-to-the-marketplace"></a>Hinzufügen von Kubernetes zum Marketplace

1. Öffnen Sie das [Verwaltungsportal](https://adminportal.local.azurestack.external).

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `Kubernetes` ein.

1. Wählen Sie `Kubernetes Cluster` aus.

1. Wählen Sie **Herunterladen** aus.

    > [!note]  
    > Es kann fünf Minuten dauern, bis das Marketplace-Element im Marketplace angezeigt wird.

    ![kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualisieren oder Entfernen von Kubernetes 

Beim Aktualisieren des Kubernetes-Elements müssen Sie das vorherige Element im Marketplace entfernen. Befolgen Sie die Anweisungen in diesem Artikel, um das Kubernetes-Update zum Marketplace hinzuzufügen.

So entfernen Sie das Kubernetes-Element:

1. Stellen Sie als Operator eine Verbindung mit Azure Stack über PowerShell her. Anleitungen dazu finden Sie unter [Herstellen einer Verbindung mit Azure Stack über PowerShell als Operator](azure-stack-powershell-configure-admin.md).

2. Suchen Sie im Katalog nach dem aktuellen Element „Kubernetes-Cluster“.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Notieren Sie sich den Namen des aktuellen Elements, z.B. `Microsoft.AzureStackKubernetesCluster.0.3.0`.

4. Verwenden Sie das folgende PowerShell-Cmdlet, um das Element zu entfernen:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von Kubernetes in Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Übersicht über das Anbieten von Diensten in Azure Stack](azure-stack-offer-services-overview.md)
