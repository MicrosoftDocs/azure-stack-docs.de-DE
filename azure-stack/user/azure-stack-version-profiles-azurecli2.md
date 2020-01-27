---
title: Verwalten von Azure Stack Hub mit der Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die plattformübergreifende Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwenden, um Ressourcen in Azure Stack Hub zu verwalten und bereitzustellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: d35e254a17c1b79347e7d13f866e1163bf049a08
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883370"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Verwalten und Bereitstellen von Ressourcen in Azure Stack Hub mit der Azure CLI

Mit den Schritten in diesem Artikel können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) zum Verwalten von ASDK-Ressourcen (Azure Stack Development Kit) über Linux-, Mac- und Windows-Clientplattformen einrichten.

## <a name="prepare-for-azure-cli"></a>Vorbereiten für Azure CLI

Wenn Sie das ASDK verwenden, benötigen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Hub, um die Azure CLI auf Ihrem Entwicklungscomputer verwenden zu können. Sie verwenden das Zertifikat, um Ressourcen über die CLI zu verwalten.

 - Das **Azure Stack Hub-Zertifizierungsstellen-Stammzertifikat** ist erforderlich, wenn Sie die CLI über eine Arbeitsstation außerhalb des ASDK nutzen.  

 - **Der Endpunkt der VM-Aliase** stellt einen Alias wie „UbuntuLTS“ oder „Win2012Datacenter“ bereit. Dieser Alias verweist beim Bereitstellen von VMs als einzelner Parameter auf einen Herausgeber, ein Angebot, eine SKU und die Version eines Images.  

In den folgenden Abschnitten wird beschrieben, wie Sie diese Werte abrufen.

### <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exportieren des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub

Wenn Sie ein integriertes System verwenden, müssen Sie das Stammzertifikat der Zertifizierungsstelle nicht exportieren. Wenn Sie das ASDK verwenden, müssen Sie das Zertifizierungsstellen-Stammzertifikat für ein ASDK exportieren.

Exportieren Sie das ASDK-Stammzertifikat wie folgt im PEM-Format:

1. Rufen Sie den Namen Ihres Azure Stack Hub-Stammzertifikats ab:
    - Melden Sie sich beim Benutzer- oder Administratorportal von Azure Stack Hub an.
    - Klicken Sie in der Nähe der Adressleiste auf **Sicher**.
    - Klicken Sie im Popupfenster auf **Gültig**.
    - Klicken Sie im Zertifikatsfenster auf die Registerkarte **Zertifizierungspfad**.
    - Notieren Sie sich den Namen Ihres Azure Stack Hub-Stammzertifikats.

    ![Azure Stack Hub-Stammzertifikat](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Erstellen Sie in Azure Stack Hub eine Windows-VM](azure-stack-quick-windows-portal.md).

3. Melden Sie sich bei dem virtuellen Computer an, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie dann das folgende Skript aus:

    ```powershell  
      $label = "<the name of your Azure Stack Hub root cert from Step 1>"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

4. Kopieren Sie das Zertifikat auf Ihren lokalen Computer.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Einrichten des Endpunkts der VM-Aliase

Sie können einen öffentlich zugänglichen Endpunkt einrichten, der eine VM-Aliasdatei hostet. Bei der VM-Aliasdatei handelt es sich um eine JSON-Datei, die einen allgemeinen Namen für ein Image bereitstellt. Sie verwenden den Namen, wenn Sie eine VM bereitstellen, als Azure CLI-Parameter.

1. Wenn Sie ein benutzerdefiniertes Image veröffentlichen, notieren Sie sich die während der Veröffentlichung angegebenen Informationen zu Herausgeber, Angebot, SKU und Version. Wenn es sich um ein Image aus dem Marketplace handelt, können Sie die Informationen mithilfe des ```Get-AzureVMImage```-Cmdlets anzeigen.  

2. Laden Sie die [Beispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) von GitHub herunter.

3. Erstellen Sie ein Speicherkonto in Azure Stack Hub. Erstellen Sie anschließend einen Blobcontainer. Legen Sie die Zugriffsrichtlinie auf „Öffentlich“ fest.  

4. Laden Sie die JSON-Datei in den neuen Container hoch. Wenn das erledigt ist, können Sie die URL des Blobs anzeigen. Wählen Sie den Blobnamen aus, und wählen Sie dann die URL in den „Blob-Eigenschaften“ aus.

### <a name="install-or-upgrade-cli"></a>Installieren oder Aktualisieren der CLI

Melden Sie sich an Ihrer Entwicklungsarbeitsstation an und installieren die CLI. Für Azure Stack Hub wird Version 2.0 oder höher der Azure CLI benötigt. Für die neueste Version der API-Profile ist eine aktuelle Version der CLI erforderlich. Mit den im Artikel [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) beschriebenen Schritten installieren Sie die CLI. 

1. Öffnen Sie ein Terminal oder ein Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation erfolgreich war:

    ```shell
    az --version
    ```

    Daraufhin sollten die Version der Azure-CLI und die anderen abhängigen Bibliotheken angezeigt werden, die auf Ihrem Computer installiert sind.

    ![Azure CLI am Python-Speicherort von Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Notieren Sie sich den Python-Speicherort der CLI. Wenn Sie das ASDK ausführen, müssen Sie diesen Speicherort zum Hinzufügen Ihres Zertifikats verwenden.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Azure AD als Identitätsverwaltungsdienst und die CLI auf einem Windows-Computer verwenden.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub als vertrauenswürdig

Bei Verwendung des ASDK müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Dieser Schritt ist für integrierten Systeme nicht erforderlich.

Um das Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub als vertrauenswürdig einzustufen, fügen Sie es für die Python-Version, die mit der Azure CLI installiert wurde, an den vorhandenen Python-Zertifikatspeicher an. Möglicherweise führen Sie Ihre eigene Instanz von Python aus. Die Azure CLI enthält eine eigene Version von Python.

1. Suchen Sie auf Ihrem Computer nach dem Speicherort des Zertifikatspeichers.  Sie finden den Speicherort durch Ausführen des Befehls `az --version`.

2. Navigieren Sie zu dem Ordner, der Ihre CLI-Python-App enthält. Diese Version von Python sollten Sie ausführen. Wenn Sie Python in Ihrem Systempfad eingerichtet haben, wird Ihre eigene Version von Python ausgeführt. Stattdessen sollten Sie die von der CLI verwendete Version ausführen und Ihr Zertifikat dieser Version hinzufügen. Beispiel: Angenommen, Ihre CLI-Python-Instanz befindet sich in`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    Verwenden Sie die folgenden Befehle:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Notieren Sie sich den Speicherort des Zertifikats. Beispiel: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Ihr spezifischer Pfad hängt von Ihrem Betriebssystem und Ihrer CLI-Installation ab.

2. Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Hub als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

1. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

2. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | value | Beispiel | Beschreibung |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | https://management.local.azurestack.external | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.
 
1. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Melden Sie sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung an. 

   - Anmelden als *Benutzer*: 

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, verwenden Sie den Befehl `az login`, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   - Melden Sie sich als *Dienstprinzipal* an: 
    
     [Erstellen Sie einen Dienstprinzipal über das Azure-Portal](azure-stack-create-service-principals.md) oder die CLI, und weisen Sie ihm eine Rolle zu, bevor Sie sich anmelden. Melden Sie sich anschließend mit dem folgenden Befehl an:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Active Directory-Verbunddienste (AD FS) als Identitätsverwaltungsdienst und die CLI auf einem Windows-Computer verwenden.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub als vertrauenswürdig

Bei Verwendung des ASDK müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Dieser Schritt ist für integrierten Systeme nicht erforderlich.

1. Suchen Sie den Speicherort des Zertifikats auf Ihrem Computer. Der Speicherort kann je nachdem, wo Sie Python installiert haben, variieren. Öffnen Sie eine Eingabeaufforderung oder eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und geben Sie den folgenden Befehl ein:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Notieren Sie sich den Speicherort des Zertifikats. Beispiel: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Der Pfad hängt davon ab, welches BS Sie verwenden und welche Python-Version installiert ist.

2. Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Hub als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

1. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

2. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | value | Beispiel | Beschreibung |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | https://management.local.azurestack.external | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.

1. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung anmelden. 

   - Anmelden als *Benutzer*:

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, verwenden Sie den Befehl `az login`, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   - Melden Sie sich als *Dienstprinzipal* an: 
    
     Bereiten Sie die PEM-Datei vor, die für die Anmeldung des Dienstprinzipals verwendet werden soll.

     Exportieren Sie auf dem Clientcomputer, auf dem der Prinzipal erstellt wurde, das Dienstprinzipalzertifikat als PFX-Datei mit dem privaten Schlüssel. Sie finden diesen unter `cert:\CurrentUser\My`. Das Zertifikat trägt den gleichen Namen wie der Prinzipal.

     Konvertieren Sie die PFX-Datei in eine PEM-Datei (mit dem OpenSSL-Hilfsprogramm).

     Melden Sie sich bei der Befehlszeilenschnittstelle an:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Azure AD als Identitätsverwaltungsdienst und die CLI auf einem Linux-Computer verwenden.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub als vertrauenswürdig

Bei Verwendung des ASDK müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Dieser Schritt ist für integrierten Systeme nicht erforderlich.

Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Hub als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen.

1. Suchen Sie den Speicherort des Zertifikats auf Ihrem Computer. Der Speicherort kann je nachdem, wo Sie Python installiert haben, variieren. Die Module „pip“ und „certifi“ müssen installiert sein. Verwenden Sie den folgenden Python-Befehl über die Bash-Eingabeaufforderung:

    ```bash  
    az --version
    ```

    Notieren Sie sich den Speicherort des Zertifikats. Beispiel: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Ihr eigener Pfad hängt von Ihrem Betriebssystem und der installierten Python-Version ab.

2. Führen Sie den folgenden Bash-Befehl mit dem Pfad zu Ihrem Zertifikat aus.

   - Für einen Linux-Remotecomputer:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Für einen Linux-Computer in der Azure Stack Hub-Umgebung:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

Führen Sie die folgenden Schritte aus, um eine Verbindung mit Azure Stack Hub herzustellen:

1. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

2. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | value | Beispiel | Beschreibung |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | https://management.local.azurestack.external | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Legen Sie die aktive Umgebung fest. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.

5. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung anmelden. 

   * Anmelden als *Benutzer*:

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, können Sie den Befehl `az login` verwenden, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.
   
   * Anmelden als *Dienstprinzipal*:
    
     [Erstellen Sie einen Dienstprinzipal über das Azure-Portal](azure-stack-create-service-principals.md) oder die CLI, und weisen Sie ihm eine Rolle zu, bevor Sie sich anmelden. Melden Sie sich anschließend mit dem folgenden Befehl an:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
    az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Active Directory-Verbunddienste (AD FS) als Verwaltungsdienst und die CLI auf einem Linux-Computer verwenden.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub als vertrauenswürdig

Bei Verwendung des ASDK müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Dieser Schritt ist für integrierten Systeme nicht erforderlich.

Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Hub als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen.

1. Suchen Sie den Speicherort des Zertifikats auf Ihrem Computer. Der Speicherort kann je nachdem, wo Sie Python installiert haben, variieren. Die Module „pip“ und „certifi“ müssen installiert sein. Verwenden Sie den folgenden Python-Befehl über die Bash-Eingabeaufforderung:

    ```bash  
    az --version 
    ```

    Notieren Sie sich den Speicherort des Zertifikats. Beispiel: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Ihr eigener Pfad hängt von Ihrem Betriebssystem und der installierten Python-Version ab.

2. Führen Sie den folgenden Bash-Befehl mit dem Pfad zu Ihrem Zertifikat aus.

   - Für einen Linux-Remotecomputer:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Für einen Linux-Computer in der Azure Stack Hub-Umgebung:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

Führen Sie die folgenden Schritte aus, um eine Verbindung mit Azure Stack Hub herzustellen:

1. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

2. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter.

    | value | Beispiel | Beschreibung |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | https://management.local.azurestack.external | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Legen Sie die aktive Umgebung fest. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.

5. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung anmelden. 

6. Anmelden: 

   *  Als **Benutzer** mit einem Webbrowser unter Verwendung eines Gerätecodes:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   * Als Dienstprinzipal:
        
     Bereiten Sie die PEM-Datei vor, die für die Anmeldung des Dienstprinzipals verwendet werden soll.

      * Exportieren Sie auf dem Clientcomputer, auf dem der Prinzipal erstellt wurde, das Dienstprinzipalzertifikat als PFX-Datei mit dem privaten Schlüssel. Sie finden diesen unter `cert:\CurrentUser\My`. Das Zertifikat trägt den gleichen Namen wie der Prinzipal.
  
      * Konvertieren Sie die PFX-Datei in eine PEM-Datei (mit dem OpenSSL-Hilfsprogramm).

     Melden Sie sich bei der Befehlszeilenschnittstelle an:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
  az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Bekannte Probleme

Es gibt bekannte Probleme bei der Verwendung der CLI in Azure Stack Hub:

 - Der interaktive CLI-Modus. Beispielsweise wird der Befehl `az interactive` in Azure Stack Hub noch nicht unterstützt.
 - Verwenden Sie zum Abrufen der Liste der in Azure Stack Hub verfügbaren VM-Images anstelle des Befehls `az vm image list` den Befehl `az vm image list --all`. Durch Angabe der Option `--all` wird sichergestellt, dass in der Antwort nur die Images zurückgegeben werden, die in Ihrer Azure Stack Hub-Umgebung verfügbar sind.
 - VM-Imagealiase, die in Azure verfügbar sind, gelten möglicherweise nicht für Azure Stack Hub. Wenn Sie VM-Images verwenden, müssen Sie den gesamten URN-Parameter (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) anstelle des Imagealias verwenden. Dieser URN muss mit den Imagespezifikationen übereinstimmen, die vom Befehl `az vm images list` abgerufen wurden.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
- [Aktivieren der Azure CLI für Azure Stack Hub-Benutzer (Operator)](../operator/azure-stack-cli-admin.md)
- [Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md) 
