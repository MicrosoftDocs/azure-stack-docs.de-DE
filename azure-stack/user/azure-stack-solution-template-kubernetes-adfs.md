---
title: Bereitstellen von Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste (AD FS) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste (AD FS) bereitstellen.
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
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: ca0dd74a08ce1abe454cb497a2569aae0b958d7c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311502"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Bereitstellen von Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!Note]  
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Das Szenario mit nicht verbundenem Azure Stack wird von der Preview zurzeit nicht unterstützt.

Sie können die Schritte in diesem Artikel zum Bereitstellen und Einrichten der Ressourcen für Kubernetes befolgen. Verwenden Sie diese Schritte, wenn Sie Active Directory-Verbunddienste (AD FS) als Ihren Identitätsverwaltungsdienst nutzen.

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie zum Einstieg sicher, dass Sie über die erforderlichen Berechtigungen verfügen und dass Ihre Azure Stack-Instanz vorbereitet ist.

1. Generieren Sie ein öffentlich-privates SSH-Schlüsselpaar für die Anmeldung beim virtuellen Linux-Computer in Azure Stack. Sie benötigen den öffentlichen Schlüssel bei der Clustererstellung.

    Anweisungen zum Generieren eines Schlüssels finden Sie unter [SSH Key Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation) (SSH-Schlüsselgenerierung).

1. Stellen Sie sicher, dass Sie ein gültiges Abonnement im Azure Stack-Mandantenportal besitzen und ausreichend öffentliche IP-Adressen zum Hinzufügen neuer Anwendungen verfügbar sind.

    Der Cluster kann nicht in einem Azure Stack-Abonnement vom Typ **Administrator** bereitgestellt werden. Sie müssen ein Abonnement vom Typ **Benutzer** verwenden. 

1. Sie benötigen den Key Vault-Dienst in Ihrem Azure Stack-Abonnement.

1. Sie benötigen den Kubernetes-Clusters in Ihrem Marketplace. 

Falls Ihnen die Marketplace-Elemente Key Vault-Dienst und Kubernetes-Cluster fehlen, wenden Sie sich an Ihren Azure Stack-Administrator.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Falls Sie als Identitätslösung AD FS verwenden, müssen Sie mit Ihrem Azure Stack-Administrator zusammenarbeiten, um den Dienstprinzipal einzurichten. Der Dienstprinzipal verschafft Ihrer Anwendung Zugriff auf Azure Stack-Ressourcen.

1. Ihr Azure Stack-Administrator stellt Ihnen ein Zertifikat und die Informationen für den Dienstprinzipal bereit.

   - Die Dienstprinzipalinformationen sollten wie folgt aussehen:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : azs-ercs01
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

   - Ihr Zertifikat ist eine Datei mit der Erweiterung `.pfx`. Sie speichern Ihr Zertifikat als Geheimnis in einem Schlüsseltresor.

2. Weisen Sie dem neuen Dienstprinzipal eine Rolle als Mitwirkender für Ihr Abonnement zu. Eine Anleitung hierzu finden Sie unter [Zuweisen einer Rolle](../operator/azure-stack-create-service-principals.md).

3. Erstellen Sie einen Schlüsseltresor zum Speichern des Zertifikats für die Bereitstellung. Verwenden Sie die folgenden PowerShell-Skripts anstelle des Portals.

   - Sie benötigen die folgenden Informationen:

       | Wert | BESCHREIBUNG |
       | ---   | ---         |
       | Azure Resource Manager Endpoint | Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.<br>Der Endpunkt im Azure Stack Development Kit (ASDK) lautet: `https://management.local.azurestack.external/`.<br>Der Endpunkt in integrierten Systemen lautet: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`. |
       | Ihre Abonnement-ID | Mit der [Abonnement-ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) greifen Sie in Azure Stack auf Angebote zu. |
       | Ihr Benutzername | Verwenden Sie nur Ihren Benutzernamen statt Ihres Domänen- und Benutzernamens, z. B. `username` anstelle von `azurestack\username`. |
       | Der Name der Ressourcengruppe  | Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein. |
       | KeyVault-Name | Der Name des Tresors.<br> RegEx-Muster: `^[a-zA-Z0-9-]{3,24}$` |
       | Ressourcengruppenstandort | Der Standort der Ressourcengruppe. Dies ist die Region, die Sie für die Azure Stack-Installation auswählen. |

   - Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und [stellen Sie eine Verbindung mit Azure Stack her](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Führen Sie das folgende Skript mit den passenden Parameterwerten aus:

   ```powershell  
       $armEndpoint="<Azure Resource Manager Endpoint>"
       $subscriptionId="<Your Subscription ID>"
       $username="<your user name >"
       $resource_group_name = "<the resource group name >"
       $key_vault_name = "<keyvault name>"
       $resource_group_location="<resource group location>"
        
       # Login Azure Stack Environment
       Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
       $mycreds = Get-Credential
       Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
       # Create new Resource group and key vault
       New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
       # Note, Do not omit -EnabledForTemplateDeployment flag
       New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
       # Obtain the security identifier(SID) of the active directory user
       $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
       $objectSID = $adUser.SID.Value
       # Set the key vault access policy
       Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
     ```

4. Laden Sie Ihr Zertifikat in den Schlüsseltresor hoch.

   - Sie benötigen die folgenden Informationen:

       | Wert | BESCHREIBUNG |
       | ---   | ---         |
       | Zertifikatpfad | Der FQDN oder der Dateipfad zum Zertifikat. |
       | Zertifikatkennwort | Das Kennwort für das Zertifikat. |
       | Geheimnisname | Der Geheimnisname, mit dem auf das im Tresor gespeicherte Zertifikat verwiesen wird. |
       | Name des Schlüsseltresors | Der Namen des im vorherigen Schritt erstellten Schlüsseltresors. |
       | Azure Resource Manager Endpoint | Der Endpunkt im Azure Stack Development Kit (ASDK) lautet: `https://management.local.azurestack.external/`.<br>Der Endpunkt in integrierten Systemen lautet: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`. |
       | Ihre Abonnement-ID | Mit der [Abonnement-ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) greifen Sie in Azure Stack auf Angebote zu. |

   - Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und [stellen Sie eine Verbindung mit Azure Stack her](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Führen Sie das folgende Skript mit den passenden Parameterwerten aus:

    ```powershell
        
     # upload the pfx to key vault
     $tempPFXFilePath = "<certificate path>"
     $password = "<certificate password>"
     $keyVaultSecretName = "<secret name>"
     $keyVaultName = "<key vault name>"
     $armEndpoint="<Azure Resource Manager Endpoint>"
     $subscriptionId="<Your Subscription ID>"
     # Login Azure Stack Environment
     Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
     $mycreds = Get-Credential
     Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
     $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
     $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
     $jsonObject = @"
     {
     "data": "$pfxAsBase64EncodedString",
     "dataType" :"pfx",
     "password": "$password"
     }
     "@
     $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
     $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
     $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
     $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
     ```

## <a name="deploy-kubernetes"></a>Bereitstellen von Kubernetes

1. Öffnen Sie das [Azure Stack-Portal](https://portal.local.azurestack.external).

1. Wählen Sie **+ Ressource erstellen** > **Compute** > **Kubernetes-Cluster** aus. Klicken Sie auf **Create**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Grundlagen

1. Wählen Sie unter „Kubernetes-Cluster erstellen“ die Option **Grundlagen**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Wählen Sie die ID für Ihr **Abonnement** aus.

1. Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein.

1. Wählen Sie den **Standort** der Ressourcengruppe aus. Dies ist die Region, die Sie für die Azure Stack-Installation auswählen.

### <a name="2-kubernetes-cluster-settings"></a>2. Einstellungen für Kubernetes-Cluster

1. Wählen Sie unter „Kubernetes Cluster erstellen“ die Option **Kubernetes Cluster Settings** (Einstellungen für Kubernetes-Cluster).

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Geben Sie unter **Linux VM Admin Username** (Benutzername des Linux-VM-Administrators) einen Namen ein. Hierbei handelt es sich um den Benutzernamen für die virtuellen Linux-Computer, die Teil des Kubernetes-Clusters und des DVM (Deployment Virtual Machine, virtueller Computer für die Bereitstellung) sind.

1. Geben Sie den **öffentlichen SSH-Schlüssel** für die Autorisierung bei allen Linux-Computern ein, die als Teil des Kubernetes-Clusters und des DVM erstellt werden.

1. Geben Sie das für die Region eindeutige **DNS-Präfix des Masterprofils** ein. Diese Angabe muss ein für die Region eindeutiger Name sein, beispielsweise `k8s-12345`. Es wird empfohlen, den gleichen Namen wie für die Ressourcengruppe zu wählen.

    > [!Note]  
    > Verwenden Sie für jeden Cluster ein neues, eindeutiges DNS-Präfix des Masterprofils.

1. Wählen Sie **Kubernetes Master Pool Profile Count** (Kubernetes-Masterpool-Profilanzahl). Die Anzahl gibt die Anzahl von Knoten im Masterpool an. Sie können einen Wert von 1 bis 7 angeben. Dieser Wert sollte eine ungerade Zahl sein.

1. Wählen Sie **The VMSize of the Kubernetes master VMs** (VM-Größe der Kubernetes-Master-VMs).

1. Wählen Sie **Kubernetes Node Pool Profile Count** (Kubernetes-Knotenpool-Profilanzahl). Er gibt die Anzahl von Agents im Cluster an. 

1. Wählen Sie **Storage Profile** (Speicherprofil). Sie können **Blob Disk** (Blobdatenträger) oder **Managed Disk** (Verwalteter Datenträger) wählen. Hiermit wird die VM-Größe der Kubernetes-Knoten-VMs angegeben. 

1. Wählen Sie **AD FS** als **Azure Stack-Identitätssystem** für Ihre Azure Stack-Installation aus.

1. Geben Sie unter **Service Principal ClientId** (Dienstprinzipal-ClientId) einen Wert ein. Dieser wird vom Kubernetes Azure-Cloudanbieter verwendet. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack-Administrator den Dienstprinzipal erstellt hat.

1. Geben Sie die **Key Vault-Ressourcengruppe** ein, die den Schlüsseltresor einbindet, der Ihr Zertifikat enthält.

1. Geben Sie den **Namen des Schlüsseltresors** des Schlüsseltresors ein, der Ihr Zertifikat als Geheimnis enthält. 

1. Geben Sie das **Geheimnis des Schlüsseltresors** ein. Der Geheimnisname verweist auf Ihr Zertifikat.

1. Geben Sie die **Version des Kubernetes Azure Cloudanbieters** ein. Dabei handelt es sich um die Version des Kubernetes Azure-Anbieters. Azure Stack veröffentlicht für jede Azure Stack-Version einen benutzerdefinierten Kubernetes-Build.

### <a name="3-summary"></a>3. Zusammenfassung

1. Wählen Sie „Zusammenfassung“. Auf dem Blatt wird eine Überprüfungsnachricht für Ihre Konfigurationseinstellungen des Kubernetes-Clusters angezeigt.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Überprüfen Sie Ihre Einstellungen.

3. Wählen Sie **OK**, um Ihren Cluster bereitzustellen.

> [!TIP]  
>  Wenn Sie Fragen zu Ihrer Bereitstellung haben, können Sie Ihre Frage im [Azure Stack-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen bzw. ermitteln, ob jemand die Frage dort bereits beantwortet hat. 

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Clusterverbindung](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Aktivieren des Kubernetes-Dashboards](azure-stack-solution-template-kubernetes-dashboard.md)