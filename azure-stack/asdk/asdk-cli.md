---
title: Einrichten von Zertifikaten für die Azure CLI im Azure Stack Development Kit (ASDK) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Zertifikate für die Azure CLI im Azure Stack Development Kit einrichten.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 32a09b833e2e5a76abf0c9a6c67540ca124bfe94
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254584"
---
# <a name="setting-up-certificates-for-azure-cli-on-azure-stack-development-kit"></a>Einrichten von Zertifikaten für die Azure CLI im Azure Stack Development Kit (ASDK)

Mit den Schritten in diesem Artikel können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) zum Verwalten von ASDK-Ressourcen (Azure Stack Development Kit) über Linux-, Mac- und Windows-Clientplattformen einrichten.

Dieser Artikel befasst sich mit dem Abrufen Ihrer Zertifikate und dem Einrichten der Vertrauensstellung auf Ihrem Remoteverwaltungscomputer. Informationen zum Installieren der Azure CLI und zum Herstellen einer Verbindung mit Ihrer Umgebung finden Sie unter [Installieren der Azure CLI in Azure Stack Hub](../user/azure-stack-version-profiles-azurecli2.md).

## <a name="prepare-for-azure-cli"></a>Vorbereiten für Azure CLI

Für das ASDK benötigen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Hub, um die Azure CLI auf Ihrem Entwicklungscomputer verwenden zu können. Sie verwenden das Zertifikat, um Ressourcen über die CLI zu verwalten.

 - Das **Azure Stack Hub-Zertifizierungsstellen-Stammzertifikat** ist erforderlich, wenn Sie die CLI über eine Arbeitsstation außerhalb des ASDK nutzen.  

 - **Der Endpunkt der VM-Aliase** stellt einen Alias wie „UbuntuLTS“ oder „Win2012Datacenter“ bereit. Dieser Alias verweist beim Bereitstellen von VMs als einzelner Parameter auf einen Herausgeber, ein Angebot, eine SKU und die Version eines Images.  

In den folgenden Abschnitten wird beschrieben, wie Sie diese Werte abrufen.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exportieren des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub

Exportieren Sie das Zertifizierungsstellen-Stammzertifikat, um die Azure CLI mit dem ASDK zu verwenden.

Exportieren Sie das ASDK-Stammzertifikat wie folgt im PEM-Format:

1. Rufen Sie den Namen Ihres Azure Stack Hub-Stammzertifikats ab:
    1. Melden Sie sich beim Benutzer- oder Administratorportal von Azure Stack Hub an.
    2. Klicken Sie in der Nähe der Adressleiste auf **Sicher**.
    3. Klicken Sie im Popupfenster auf **Gültig**.
    4. Wählen Sie im Fenster „Zertifikat“ die Registerkarte **Zertifizierungspfad** aus.
    5. Notieren Sie sich den Namen Ihres Azure Stack Hub-Stammzertifikats.

    ![Azure Stack Hub-Stammzertifikat](../user/media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Erstellen Sie in Azure Stack Hub eine Windows-VM](../user/azure-stack-quick-windows-portal.md).

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

## <a name="set-up-the-virtual-machine-alias-endpoint"></a>Einrichten des Endpunkts für den VM-Alias

Sie können einen öffentlich zugänglichen Endpunkt einrichten, der eine VM-Aliasdatei hostet. Bei der VM-Aliasdatei handelt es sich um eine JSON-Datei, die einen allgemeinen Namen für ein Image bereitstellt. Sie verwenden den Namen, wenn Sie eine VM bereitstellen, als Azure CLI-Parameter.

1. Wenn Sie ein benutzerdefiniertes Image veröffentlichen, notieren Sie sich die während der Veröffentlichung angegebenen Informationen zu Herausgeber, Angebot, SKU und Version. Wenn es sich um ein Image aus dem Marketplace handelt, können Sie die Informationen mithilfe des ```Get-AzureVMImage```-Cmdlets anzeigen.  

2. Laden Sie die [Beispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) von GitHub herunter.

3. Erstellen Sie ein Speicherkonto in Azure Stack Hub. Erstellen Sie anschließend einen Blobcontainer. Legen Sie die Zugriffsrichtlinie auf „Öffentlich“ fest.  

4. Laden Sie die JSON-Datei in den neuen Container hoch. Wenn das erledigt ist, können Sie die URL des Blobs anzeigen. Wählen Sie den Blobnamen aus, und wählen Sie dann die URL in den „Blob-Eigenschaften“ aus.


## <a name="trust-the-certificate"></a>Vertrauen des Zertifikats

Sie müssen das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig konfigurieren, um die Azure CLI mit dem ASDK zu verwenden.

### <a name="windows"></a>[Windows](#tab/win)

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

Anweisungen zum Installieren und Herstellen einer Verbindung mit der Azure CLI finden Sie unter [Installieren der Azure CLI in Azure Stack Hub](../user/azure-stack-version-profiles-azurecli2.md).

### <a name="linux"></a>[Linux](#tab/lin)

Stufen Sie das Zertifizierungsstellen-Stammzertifikat von Azure Stack Hub beim Einrichten der CLI als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen.

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

Anweisungen zum Installieren und Herstellen einer Verbindung mit der Azure CLI finden Sie unter [Installieren der Azure CLI in Azure Stack Hub](../user/azure-stack-version-profiles-azurecli2.md).

---

## <a name="next-steps"></a>Nächste Schritte

[Installieren der Azure CLI in Azure Stack Hub](../user/azure-stack-version-profiles-azurecli2.md)