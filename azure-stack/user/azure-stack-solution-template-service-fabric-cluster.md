---
title: Erstellen eines gesicherten Service Fabric-Clusters in Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen gesicherten Service Fabric-Cluster in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 29adea1cb8c07acc309ef7aae2856b9a14759de3
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985889"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Bereitstellen eines Service Fabric-Clusters in Azure Stack

Verwenden Sie das Element **Service Fabric-Cluster** aus dem Azure Marketplace, um einen gesicherten Service Fabric-Cluster in Azure Stack bereitzustellen. 

Weitere Informationen zur Verwendung von Service Fabric finden Sie in der Azure-Dokumentation unter [Übersicht über Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) und [Szenarien für die Clustersicherheit in Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

Der Service Fabric-Cluster in Azure Stack verwendet nicht den Ressourcenanbieter „Microsoft.ServiceFabric“. In Azure Stack ist der Service Fabric-Cluster stattdessen eine VM-Skalierungsgruppe mit vorinstallierter Software, die Desired State Configuration (DSC) nutzt.

## <a name="prerequisites"></a>Voraussetzungen

Folgendes ist für die Bereitstellung des Service Fabric-Clusters erforderlich:
1. **Clusterzertifikat**  
   Dies ist das X.509-Serverzertifikat, dass Sie Key Vault beim Bereitstellen von Service Fabric hinzufügen. 
   - Der **CN** dieses Zertifikats muss mit dem vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) des von Ihnen erstellten Service Fabric-Clusters übereinstimmen. 
   - Das Zertifikat muss im PFX-Format vorliegen, da sowohl der öffentliche als auch der private Schlüssel benötigt werden. 
     Informationen zum Erstellen dieses serverseitigen Zertifikats finden Sie unter [Szenarien für die Clustersicherheit in Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

     > [!NOTE]  
     > Anstelle des x.509-Serverzertifikats können Sie zu Testzwecken ein selbstsigniertes Zertifikat verwenden. Selbstsignierte Zertifikate müssen nicht mit dem FQDN des Clusters übereinstimmen.

1. **Clientzertifikat des Administrators:** Hierbei handelt es sich um das Zertifikat, das der Client für die Authentifizierung beim Service Fabric-Cluster verwendet. Dies kann ein selbstsigniertes Zertifikat sein. Informationen zum Erstellen dieses Clientzertifikats finden Sie unter [Szenarien für die Clustersicherheit in Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

1. **Die folgenden Elemente müssen im Azure Stack-Marketplace verfügbar sein:**
    - **Windows Server 2016**: Die Vorlage verwendet das Windows Server 2016-Image zum Erstellen des Clusters.  
    - **Benutzerdefinierte Erweiterung:** VM-Erweiterung von Microsoft.  
    - **PowerShell Desired Stage Configuration** (PowerShell-Konfiguration für gewünschte Phase): VM-Erweiterung von Microsoft


## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault
Für die Bereitstellung eines Service Fabric-Clusters müssen Sie die richtige *Geheimnis-ID* oder URL von Key Vault für den Service Fabric-Cluster angeben. Für die Azure Resource Manager-Vorlage wird ein Key Vault-Element als Eingabe verwendet. Anschließend ruft die Vorlage das Clusterzertifikat ab, wenn der Service Fabric-Cluster installiert wird.

> [!IMPORTANT]  
> Sie müssen PowerShell verwenden, um Key Vault ein Geheimnis für die Verwendung mit Service Fabric hinzuzufügen. Verwenden Sie nicht das Portal.  

Verwenden Sie das folgende Skript, um die Key Vault-Instanz zu erstellen, und fügen Sie ihr das *Clusterzertifikat* hinzu. (Siehe [Voraussetzungen](#prerequisites).) Überprüfen Sie vor der Ausführung des Skripts das Beispielskript, und aktualisieren Sie die angegebenen Parameter entsprechend Ihrer Umgebung. Dieses Skript gibt auch die Werte aus, die Sie in der Azure Resource Manager-Vorlage angeben müssen. 

> [!TIP]  
> Damit das Skript erfolgreich ausgeführt werden kann, ist ein öffentliches Angebot erforderlich, das die Dienste für Compute, Netzwerk, Speicher und Key Vault enthält. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Weitere Informationen finden Sie unter [Verwalten von Key Vault in Azure Stack mithilfe von PowerShell](azure-stack-key-vault-manage-powershell.md).

## <a name="deploy-the-marketplace-item"></a>Bereitstellen des Marketplace-Elements

1. Navigieren Sie im Benutzerportal zu **+ Ressource erstellen** > **Compute** > **Service Fabric-Cluster**. 

   ![Auswählen des Service Fabric-Clusters](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. Füllen Sie auf jeder Seite (etwa *Grundlagen*) das Bereitstellungsformular aus. Verwenden Sie Standardwerte, wenn Sie bei einem Wert unsicher sind. Klicken Sie auf **OK**, um zur nächsten Seite zu gelangen:

   ![Grundlagen](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. Auf der Seite *Netzwerkeinstellungen* können Sie angeben, dass bestimmte Ports für Ihre Anwendungen geöffnet werden sollen:

   ![Netzwerkeinstellungen](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. Fügen Sie auf der Seite *Sicherheit* die Werte hinzu, die Sie beim [Erstellen von Azure Key Vault](#add-a-secret-to-key-vault) und Hochladen des Geheimnisses erhalten haben.

   Geben Sie unter *Admin Client Certificate Thumbprint* (Fingerabdruck des Administratorclientzertifikats) den Fingerabdruck des *Clientzertifikats des Administrators* ein. (Siehe [Voraussetzungen](#prerequisites).)
   
   - Quellschlüsseltresor:  Geben Sie die gesamte Zeichenfolge der Key Vault-ID (*keyVault id*) aus den Skriptergebnissen an. 
   - Cluster Certificate URL (Clusterzertifikat-URL): Geben Sie die gesamte URL der Geheimnis-ID (*Secret Id*) aus den Skriptergebnissen an. 
   - Cluster Certificate thumbprint (Clusterzertifikatfingerabdruck): Geben Sie den *Clusterzertifikatfingerabdruck* aus den Skriptergebnissen an.
   - Admin Client Certificate Thumbprints (Fingerabdrücke des Administratorclientzertifikats): Geben Sie den *Fingerabdruck des Administratorclientzertifikats* an, der unter „Voraussetzungen“ erstellt wurde. 

   ![Skriptausgabe](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Sicherheit](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Schließen Sie den Assistenten ab, und klicken Sie dann auf **Erstellen**, um den Service Fabric-Cluster bereitzustellen.



## <a name="access-the-service-fabric-cluster"></a>Zugreifen auf den Service Fabric-Cluster
Sie können auf den Service Fabric-Cluster entweder über Service Fabric Explorer oder Service Fabric PowerShell zugreifen.


### <a name="use-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer
1.  Überprüfen Sie, ob der Webbrowser auf Ihr Administratorclientzertifikat zugreifen und sich beim Service Fabric-Cluster authentifizieren kann.  

    a. Öffnen Sie Internet Explorer, und navigieren Sie zu **Internetoptionen** > **Inhalte** > **Zertifikate**.
  
    b. Klicken Sie unter „Zertifikate“ auf **Importieren**, um den *Zertifikatimport-Assistenten* zu starten, und klicken Sie dann auf **Weiter**. Klicken Sie auf der Seite *Zu importierende Datei* auf **Durchsuchen**, und wählen Sie das **Administratorclientzertifikat** aus, das Sie in der Azure Resource Manager-Vorlage angegeben haben.
        
       > [!NOTE]  
       > Dieses Zertifikat ist nicht das Clusterzertifikat, das zuvor zu KeyVault hinzugefügt wurde.  

    c. Stellen Sie sicher, dass in der Dropdownliste für Erweiterungen des Datei-Explorer-Fensters die Option „Privater Informationsaustausch“ ausgewählt ist.  

       ![Privater Informationsaustausch](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Wählen Sie auf der Seite *Zertifikatspeicher* die Option **Persönlich**, und schließen Sie dann den Assistenten ab.  
       ![Zertifikatspeicher](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. So ermitteln Sie den FQDN des Service Fabric-Clusters  

    a. Navigieren Sie zu der Ihrem Service Fabric-Cluster zugewiesenen Ressourcengruppe, und suchen Sie die Ressourcen *Öffentliche IP-Adresse*. Wählen Sie das mit der öffentlichen IP-Adresse verknüpfte Objekt aus, um das Blatt *Öffentliche IP-Adresse* zu öffnen.  

      ![Öffentliche IP-Adresse](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. Auf dem Blatt „Öffentliche IP-Adresse“ wird der FQDN unter *DNS-Name* angezeigt.  

      ![DNS-Name](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Sehen Sie sich die Ergebnisse der Vorlagenbereitstellung an, um die URL für Service Fabric Explorer und den Clientverbindungsendpunkt zu ermitteln.

1. Navigieren Sie im Browser zu https://*FQDN*:19080. Ersetzen Sie *FQDN* durch den FQDN Ihres Service Fabric-Clusters aus Schritt 2.   
   Wenn Sie ein selbstsigniertes Zertifikat verwendet haben, erhalten Sie eine Warnung, dass die Verbindung nicht sicher ist. Klicken Sie zum Aufrufen der Website auf **Weitere Informationen** und dann auf **Webseite trotzdem laden**. 

1. Für die Authentifizierung bei der Website müssen Sie ein Zertifikat auswählen. Klicken Sie auf **More choices** (Weitere Optionen), wählen Sie das entsprechende Zertifikat aus, und klicken Sie dann auf **OK**, um eine Verbindung mit Service Fabric Explorer herzustellen. 

   ![Authentifizieren](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Verwenden von Service Fabric PowerShell

1. Installieren Sie das *Microsoft Azure Service Fabric SDK* aus [Vorbereiten Ihrer Entwicklungsumgebung unter Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) in der Azure Service Fabric-Dokumentation.  

1. Konfigurieren Sie nach Abschluss der Installation die Systemumgebungsvariablen, um sicherzustellen, dass über PowerShell auf die Service Fabric-Cmdlets zugegriffen werden kann.  
    
    a. Wechseln Sie zu **Systemsteuerung** > **System und Sicherheit** > **System**, und klicken Sie dann auf **Erweiterte Systemeinstellungen**.  
    
      ![Systemsteuerung](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Klicken Sie unter *Systemeigenschaften* auf der Registerkarte **Erweitert** auf **Umgebungsvariablen**.  

    c. Bearbeiten Sie unter *Systemvariablen* den **Pfad**. Stellen Sie sicher, dass sich **C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** ganz oben in der Liste der Umgebungsvariablen befindet.  

      ![Liste der Umgebungsvariablen](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Nachdem Sie die Reihenfolge der Umgebungsvariablen geändert haben, starten Sie PowerShell neu, und führen Sie dann das folgende PowerShell-Skript aus, um Zugriff auf den Service Fabric-Cluster zu erhalten:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > Der Name des Clusters im Skript fängt nicht mit *https://* an. Port 19000 ist erforderlich.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
