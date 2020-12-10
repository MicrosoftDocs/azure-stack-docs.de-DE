---
title: Hinzufügen einer Containerregistrierung in Azure Stack Hub | Microsoft-Dokumentation
titleSuffix: Azure Stack
description: Hier erfahren Sie, wie Sie dem Azure Stack Hub-Marketplace eine Containerregistrierung hinzufügen.
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
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 44cf8fe76ec203fb0a6260a92c4e47641d4f40f8
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935002"
---
# <a name="add-a-container-registry-to-azure-stack-hub"></a>Hinzufügen einer Containerregistrierung in Azure Stack Hub

Sie können Ihrem Azure Stack Hub-Marketplace die Containerregistrierung hinzufügen, damit Ihre Benutzer ihre eigene Containerregistrierung bereitstellen und verwalten können. Diese Lösungsvorlage installiert und konfiguriert die Open-Source-Containerregistrierung von Docker im Abonnement eines Benutzers, das auf dem Ubuntu 16.04-LTS-Basisimage für AKS ausgeführt wird. Die Vorlage unterstützt sowohl verbundene als auch nicht verbundene (mit Air Gap) Bereitstellungen. Außerdem werden sowohl in Azure Stack Hub-Instanzen bereitgestellte Instanzen von Azure Active Directory (Azure AD) als auch von Active Directory-Verbunddiensten (AD FS) unterstützt.

## <a name="get-the-marketplace-item"></a>Abrufen des Marketplace-Elements

Das Marketplace-Element **Containerregistrierungsvorlage** finden Sie im folgenden GitHub-Repository: https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/registry-v1.0.2/Microsoft.AzureStackContainerRegistry.1.0.2.azpkg. Das Marketplace-Element steht in ausgewählten Abonnements über das Portal zur Verfügung.

Sie können das Element Ihrem Marketplace auch mithilfe von Microsoft.AzureStackDockerContainerRegistry.1.0.2.azpkg hinzufügen (quer laden). Der Zugriff auf die Skripts dieses Artikels ist möglich, indem das Git-Repository als Paket (ZIP) von https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip heruntergeladen wird und die Dateien extrahiert werden. Das Skript finden Sie im `azurestack-gallery-master\registry\scripts`-Ordner.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen die folgenden Elemente, bevor Sie das Marketplace-Element „Containerregistrierung“ in Azure Stack Hub hinzufügen können.

| Element | type | Details |
| --- | --- | --- |
| PowerShell-Module für Azure Stack Hub (Azs.Gallery.Admin) | PowerShell-Module | Dies ist nur erforderlich, wenn das Katalogelement „Containerregistrierungsvorlage“ quer geladen wird. Die PowerShell-Module für Azure Stack Hub werden verwendet, um Katalogelemente hinzuzufügen und zu entfernen.<br>[Installieren des PowerShell AzureRM-Moduls für Azure Stack Hub](../../operator/powershell-install-az-module.md) |
| Containerregistrierungsvorlage | Marketplace-Element | Wenn Sie die Containerregistrierung als Azure Stack Hub-Benutzer bereitstellen möchten, muss das Marketplace-Element der Containerregistrierungsvorlage in Ihrem Abonnement verfügbar sein oder manuell in Ihren Azure Stack Hub-Marketplace hinzugefügt (quer geladen) werden. Beim Querladen befolgen Sie die Anleitung zum Querladen des Pakets in der `readme.md`-Datei im [GitHub-Repository](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/registry-v1.0.1). |
| Ubuntu 16.04-LTS-Basisimage für AKS, September 2019 (Mindestversion) | Marketplace-Element | Damit Ihre Azure Stack Hub-Benutzer die Containerregistrierung bereitstellen können, müssen Sie dafür sorgen, dass das AKS-Basisimage im Marketplace verfügbar ist. Die Containerregistrierungsvorlage verwendet das Image, wenn eine Ubuntu-VM aus dem Abonnement installiert wird, das die Binärdateien der Docker-Containerregistrierung hostet. |
| Benutzerdefinierte Linux-Skripterweiterung 2.0 | Marketplace-Element | Damit Ihre Azure Stack Hub-Benutzer die Containerregistrierung bereitstellen können, müssen Sie dafür sorgen, dass die benutzerdefinierte Linux-Skripterweiterung im Marketplace verfügbar ist. Die Bereitstellung der Containerregistrierungsvorlage verwendet die Erweiterung zum Konfigurieren der Registrierung. |
| SSL-Zertifikat | Zertifikat | Benutzer, die die Containerregistrierungsvorlage bereitstellen, müssen ein PFX-Zertifikat bereitstellen, das verwendet wird, wenn die SSL-Verschlüsselung für den Registrierungsdienst konfiguriert wird. Bei Verwendung des Skripts müssen Sie die PowerShell-Sitzung in einer Eingabeaufforderung mit erhöhten Rechten ausführen. Eine DVM- oder HLH-Ausführung sollte vermieden werden.<br>Einen allgemeinen Leitfaden zu den Anforderungen an PKI-Zertifikate für Azure Stack Hub mit öffentlichen oder privaten Zertifikaten bzw. Unternehmenszertifikaten finden Sie in der Dokumentation [Zertifikatanforderungen für Azure Stack Hub-PKI (Public Key-Infrastruktur)](../../operator/azure-stack-pki-certs.md).<br>Der vollqualifizierter Domänenname für das Zertifikat sollte dem Muster `<vmname>.<location>.cloudapp.<fqdn>` folgen, wenn keine benutzerdefinierte Domäne/kein benutzerdefinierter DNS-Eintrag für den Endpunkt verwendet wird. Der Name sollte mit einem Buchstaben beginnen und mindestens zwei Buchstaben enthalten, nur Kleinbuchstaben nutzen und mindestens drei Zeichen lang sein. |
| Dienstprinzipal (SPN) | App-Registrierung | Zum Bereitstellen und Konfigurieren der Containerregistrierung muss eine Anwendungsregistrierung (auch Dienstprinzipal (SPN)) erstellt werden. Dieser SPN wird während der Konfiguration der VM und der Registrierung verwendet, um auf Microsoft Azure Key Vault-Ressourcen und Ressourcen in Ihrem Speicherkonto zuzugreifen, die vor der Bereitstellung des Marketplace-Elements erstellt wurden.<br>Der SPN sollte in Azure AD im Mandanten erstellt werden, bei dem Sie sich über das Benutzerportal in Azure Stack Hub anmelden. Wenn AD FS verwendet wird, wird der SPN im lokalen Verzeichnis erstellt.<br>Details zum Erstellen eines SPN für Azure AD- und AD FS-Authentifizierungsmethoden finden Sie im [folgenden Leitfaden](../../operator/azure-stack-create-service-principals.md).<br> **Wichtig**: Sie müssen die ID und das Geheimnis der SPN-App speichern, um Updates bereitstellen zu können.<br> |
| Registrierungsbenutzername und -kennwort | Anmeldeinformationen | Die Open-Source-Containerregistrierung von Docker wird mit aktivierter grundlegender Authentifizierung bereitgestellt und konfiguriert. Für den Zugriff auf die Registrierung mit Docker-Befehlen zum Pushen und Pullen von Images ist ein Benutzername und ein Kennwort erforderlich. Der Benutzername und das Kennwort werden sicher in Key Vault gespeichert.<br>**Wichtig**: Sie müssen den Benutzername und das Kennwort für die Registrierung speichern, um sich bei der Registrierung anzumelden und Images zu pushen/zu pullen. |
| Öffentlicher SSH-Schlüssel/privater Schlüssel | Anmeldeinformationen | Zur Problembehandlung von Problemen bei der Bereitstellung oder von Runtimeproblemen der VM muss ein öffentlicher SSH-Schlüssel für die Bereitstellung bereitgestellt werden und der entsprechende private Schlüssel zugänglich sein. Das OpenSSH-Format (ssh-keygen) sollte verwendet werden, um das private/öffentliche Schlüsselpaar als Diagnoseskripts zu generieren, um Protokolle zu erfassen, für die dieses Format erforderlich ist.<br>**Wichtig**: Sie benötigen Zugriff auf die öffentlichen und privaten Schlüssel, um zu Problembehandlungszwecken auf die bereitgestellte VM zuzugreifen. |
| Zugriff auf Verwaltungs- und Benutzerportale und Verwaltungsendpunkte | Konnektivität | Für diesen Leitfaden wird angenommen, dass Sie die Registrierung in einem System bereitstellen und konfigurieren, das Konnektivität zum Azure Stack Hub-System aufweist. |

Das Skript `Pre-reqs` erstellt die anderen für die Bereitstellung des Marketplace-Elements erforderlichen Eingaben.

## <a name="installation-steps"></a>Installationsschritte

Für die Installation der Containerregistrierungsvorlage müssen vor der Bereitstellung mehrere Ressourcen erstellt werden.

1. Stellen Sie eine Verbindung zu Azure Stack Hub als Benutzer mithilfe von PowerShell her, und wählen Sie mithilfe des Cmdlet `Select-AzureRmSubscription –Subscription <subscription guid>` ein Abonnement aus. Weitere Informationen zum Herstellen einer Verbindung zu PowerShell als Benutzer in Azure Stack finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Benutzer](../../user/azure-stack-powershell-configure-user.md).

2. Führen Sie im `pre-reqs.ps1`-Skript `Import-Modules .\\pre-reqs.ps1` aus, um die Module zu importieren. Das Skript erstellt eine Ressourcengruppe, ein Speicherkonto, einen Blobcontainer, einen Key Vault-Speicher, weist dem SPN Zugriffsberechtigungen zu und kopiert die Zertifikate und den Benutzernamen sowie das Kennwort für die Registrierung in den Key Vault-Speicher.

3. Führen Sie das folgende Cmdlet in einer Eingabeaufforderung mit erhöhten Rechten mit den Werten für Ihre Umgebung für die folgenden Parameter aus:

    ```powershell  
         Set-ContainerRegistryPrerequisites -Location Shanghai `
         -ServicePrincipalId <spn app id> `
         -ResourceGroupName newregreq1 `
         -StorageAccountName newregsa1 `
         -StorageAccountBlobContainer newregct1 `
         -KeyVaultName newregkv1 `
         -CertificateSecretName containersecret2 `
         -CertificateFilePath C:\crinstall\shanghairegcert.pfx `
         -CertificatePassword <cert password> `
         -RegistryUserName admin `
         -RegistryUserPassword <password> 
    ```

    | Parameter | Details |
    | --- | --- |
    | $Location | Dieser Parameter wird manchmal als Regionsname bezeichnet. |
    | $ResourceGroupName | Geben Sie den Namen der Ressourcengruppen an, in der das Speicherkonto und der Key Vault-Speicher erstellt werden sollen. Beim Bereitstellen des Marketplace-Elements geben Sie eine andere Ressourcengruppe an. |
    | $StorageAccountName | Geben Sie den Namen des Speicherkontos an, das für die Containerregistrierung erstellt werden soll, damit darin die Images, die gepusht wurden, gespeichert werden können. |
    | $StorageAccountBlobContainer | Geben Sie den Namen des Blobcontainers an, der erstellt werden soll, der als Imagespeicher verwendet werden soll. |
    | $KeyVaultName | Geben Sie den Namen des Key Vault-Speichers an, der erstellt werden soll, um das Zertifikat und die Werte für Benutzername und Kennwort zu speichern. |
    | $CertificateSecretName | Geben Sie den Namen des Geheimnisses an, das in Key Vault erstellt wurde, um das PFX-Zertifikat zu speichern. |
    | $CertificateFilePath | Geben Sie den Pfad zum PFX-Zertifikat an. |
    | $CertificatePassword | Geben Sie das Kennwort für das PFX-Zertifikat an. |
    | $ServicePrincipalId | Geben Sie die App-ID des SPN an. |
    | $RegistryUserName | Geben Sie den Benutzernamen für den Zugriff auf den Registrierungsdienst mit einfacher Autorisierung an. |
    | $RegistryUserPassword | Geben Sie das Kennwort für den Registrierungsbenutzer an. |

1. Sobald die Ausführung des Skripts abgeschlossen ist, finden Sie am Ende des Skripts Parameter, die in der Vorlagenbereitstellung genutzt werden können. Beim Kopieren und Einfügen dieser Werte werden möglicherweise Leerzeichen eingefügt, wenn die Werte Umbrüche aufweisen.

    ```powershell  
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/<subcription id>/resourceGroups/newr
    egreg1/providers/Microsoft.KeyVault/vaults/newregkv1
    PFX KeyVaultSecretUrl        : https://newregkv1.vault.shanghai.azurestack.corp.microsoft.com:443/secr
    ets/containersecret1/37cc2f7ea1c44ad7b930e2c237a14949
    PFX Certificate Thumbprint   : 64BD5F3BC41DCBC6495998900ED322D8110DE25E
    ----------------------------------------------------------------
    StorageAccountResourceId     : /subscriptions/<subcription id>/resourcegroups/newr
    egreg1/providers/Microsoft.Storage/storageAccounts/newregsa1
    Blob Container               : newregct1
    ----------------------------------------------------------------
    
    Skus : aks-ubuntu-1604-201909
    
    
    Skus : aks-ubuntu-1604-201910
    
    ---------------------------------------------------------------- 
    
    ```

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

2. Klicken Sie auf **Erstellen** > **Compute** > **Containerregistrierungsvorlage**.

    ![Containerregistrierungsvorlage](./media/container-registry-template-install-tzl/template.png)

3. Wählen Sie das Abonnement, die Ressourcengruppe und den Speicherort für die Bereitstellung der Containerregistrierungsvorlage aus.

    ![Auswählen des Abonnements](./media/container-registry-template-install-tzl/subscription.png)

4. Vervollständigen Sie die Konfigurationsdetails für die VM. Der Standardwert für die Image-SKU ist **aks-ubuntu-1604-201909**. Die Ausgabe der `Set-ContainerRegistryPrerequisites`-Funktion enthält jedoch eine Liste verfügbarer SKUs, die für die Bereitstellung verwendet werden können. Wenn mehrere SKUs vorhanden sind, wählen Sie für die Bereitstellung die aktuelle SKU aus.

    ![VM-Konfigurationsdetails](./media/container-registry-template-install-tzl/details.png)

    | Parameter | Details |
    | --- | --- |
    | Username | Geben Sie den Benutzername für die Anmeldung bei der VM an. |
    | Öffentlicher SSH-Schlüssel | Geben Sie den öffentlichen SSH-Schlüssel an, der für die Authentifizierung mit der VM mithilfe des SSH-Protokolls genutzt wird. |
    | Size | Wählen Sie die Größe der bereitzustellenden VM aus. |
    | Öffentliche IP-Adresse | Geben Sie den Namen und den Typ der IP-Adresse (dynamisch oder statisch) für diese VM an. Der Domänenname ist ungültig. Nur Kleinbuchstaben, Zahlen und Bindestriche sind zulässig. Das erste Zeichen muss ein Buchstabe sein. Das letzte Zeichen muss ein Buchstabe oder eine Zahl sein. Der Name muss zwischen drei und 63 Zeichen lang sein.  |
    | Domänennamenbezeichnung | Geben Sie das DNS-Präfix für Ihre Registrierung an. Der gesamte FQDN sollte mit dem CN-Wert des für die Registrierung erstellten PFX-Zertifikats übereinstimmen. |
    | Replikate | Geben Sie zu Beginn die Anzahl der Containerreplikate an. |
    | Image-SKU | Geben Sie die Image-SKU an, die für die Bereitstellung verwendet werden soll. Die für das AKS-Basisimage verfügbaren SKUs werden vom `Set-ContainerRegistryPrerequisites`-Skript aufgeführt. |
    | SPN-Client-ID | Geben Sie die SPN-App-ID an. |
    | SPN-Kennwort/Kennwort bestätigen | Geben Sie das SPN-App-ID-Geheimnis an. |

1. Schließen Sie die Konfiguration für Speicher und Key Vault ab.

    ![Speicher- und Key Vault-Konfiguration](./media/container-registry-template-install-tzl/storage.png)

    | Parameter | Details |
    | --- | --- |
    | Existing extended storage account resource ID (Vorhandene Ressourcen-ID des erweiterten Speicherkontos) | Geben Sie die vom `pre-reqs`-Skript zurückgegebene Ressourcen-ID des Speicherkontos an. |
    | Existing backend blob container (Vorhandener Back-End-Blobcontainer) | Geben Sie den Blobcontainernamen an, der in der pre-reqs-Skriptausgabe aufgeführt ist. |
    | PFX Certificate Key Vault Resource ID (Ressourcen-ID des PFX-Zertifikats in Key Vault) | Geben Sie die Ressourcen-ID für Key Vault wie vom `pre-reqs`-Skript zurückgegeben an. |
    | PFX Certificate Key Vault Secret URL (URL für das Key Vault-Geheimnis des PFX-Zertifikats) | Geben Sie die Zertifikat-URL wie vom pre-reqs-Skript zurückgegeben an. |
    | PFX Certificate Thumbprint (PFX-Zertifikatfingerabdruck) | Geben Sie den Zertifikatfingerabdruck wie vom pre-reqs-Skript zurückgegeben an. |

1. Sobald alle Werte angegeben wurden und die Bereitstellung der Lösungsvorlage begonnen hat, dauert es 10–15 Minuten, bis die VM den Registrierungsdienst bereitgestellt und konfiguriert hat.

    ![VM-Bereitstellung](./media/container-registry-template-install-tzl/deploy.png)

2. Öffnen Sie zum Testen der Registrierung eine Docker-CLI-Instanz auf einem Computer/einer VM mit Zugriff auf die Registrierungs-URL.

    > [!Note]
    >  Wenn Sie ein selbstsigniertes Zertifikat oder ein für die VM unbekanntes Zertifikat verwendet haben, um auf die Registrierung zuzugreifen, müssen Sie dieses Zertifikat auf der VM installieren und Docker neu starten.

## <a name="pushing-and-pulling-images-from-container-registry"></a>Pushen und Pullen von Images aus der Containerregistrierung

1. Melden Sie sich mit `docker login –u \<username> -p \<password>` an.
2. Pullen Sie ein Image aus einer bekannten Registrierung.
3. Versehen Sie das Image mit einem Tag, damit die neu bereitgestellte Docker-Containerregistrierung als Ziel verwendet wird.
4. Pushen Sie das Image in die neue Zielregistrierung.

Beispiel:

```powershell  
PS C:\> docker pull mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
1.0: Pulling from azureiotedge-simulated-temperature-sensor
5d20c808ce19: Already exists
656de8e592c3: Already exists
1e1868d1f676: Already exists
f3fb1b0d620f: Pulling fs layer
26224c4fc11a: Pulling fs layer
c459a69d65b2: Pulling fs layer
c459a69d65b2: Verifying Checksum
c459a69d65b2: Download complete
f3fb1b0d620f: Download complete
f3fb1b0d620f: Pull complete
26224c4fc11a: Verifying Checksum
26224c4fc11a: Pull complete
c459a69d65b2: Pull complete
Digest: sha256:dd64ff0918459184574e840ee97aa9f1bacd40aa37c972984ea10f0ecd719d5f
Status: Downloaded newer image for mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker tag mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0    myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp.microsoft.com
docker : WARNING! Using --password via the CLI is insecure. Use --password-stdin.
At line:1 char:1
+ docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (WARNING! Using ...password-stdin.:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
 
Login Succeeded

PS C:\> docker push myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
The push refers to repository [myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor]
d377c212e567: Preparing
0481685b758f: Preparing
15474c03a0b6: Preparing
8cdec5be5964: Preparing
79116d3fb0bf: Preparing
3fc64803ca2d: Preparing
3fc64803ca2d: Waiting
79116d3fb0bf: Mounted from azureiotedge-agent
8cdec5be5964: Mounted from azureiotedge-agent
15474c03a0b6: Pushed
d377c212e567: Pushed
3fc64803ca2d: Mounted from azureiotedge-agent
0481685b758f: Pushed
1.0: digest: sha256:f5fbc4a5c6806e12cafe1c363fea2b6cbd98a211b8153c5b19aca1386bfa6ecb size: 1576 

```

## <a name="known-issues"></a>Bekannte Probleme

Die von dieser Vorlage bereitgestellte Version des Docker-Containerregistrierungsdiensts ist 2.7. Diese Version weist bekannte Probleme auf, die das Pushen und Pullen von Windows-Containerimages verhindert. Dieses Issue kann mit dem GitHub-Element [https://github.com/docker/distribution-library-image/issues/89](https://github.com/docker/distribution-library-image/issues/89) nachverfolgt werden.

## <a name="next-steps"></a>Nächste Schritte

[Der Azure Stack-Marketplace – Übersicht](../../operator/azure-stack-marketplace.md)
