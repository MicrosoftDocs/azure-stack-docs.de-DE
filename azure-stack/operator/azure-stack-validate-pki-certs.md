---
title: Überprüfen von Azure Stack Hub-PKI-Zertifikaten
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie mit dem Azure Stack Hub Readiness Checker-Tool PKI-Zertifikate für integrierte Azure Stack Hub-Systeme überprüfen.
services: azure-stack
documentationcenter: ''
author: PatAltimore
ms.topic: how-to
ms.date: 10/19/2020
ms.author: patricka
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 30a0c94f52a46ae92b1d76e111fae64d5599aba7
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868828"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Überprüfen von Azure Stack Hub-PKI-Zertifikaten

Das in diesem Artikel beschriebene Tool „Azure Stack Hub Readiness Checker“ steht im [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) zur Verfügung. Mit diesem Tool können Sie sich vergewissern, dass die [generierten PKI-Zertifikate (Public Key-Infrastruktur)](azure-stack-get-pki-certs.md) für die Vorabbereitstellung geeignet sind. Planen Sie bei der Zertifikatüberprüfung genügend Zeit ein, um die Zertifikate testen und ggf. neu ausstellen zu können.

Das Readiness Checker-Tool führt folgende Zertifikatüberprüfungen durch:

- **PFX analysieren**  
    Sucht nach einer gültigen PFX-Datei und einem korrekten Kennwort und überprüft, ob öffentliche Informationen durch das Kennwort geschützt sind.
- **Ablaufdatum**  
    Überprüft die Mindestgültigkeit von sieben Tagen.
- **Signaturalgorithmus**  
    Überprüft, ob SHA1 als Signaturalgorithmus verwendet wird (was nicht der Fall sein darf).
- **Privater Schlüssel**  
    Überprüft, ob der private Schlüssel vorhanden ist und mit dem Attribut „Lokaler Computer“ exportiert wird. 
- **Vertrauenskette**  
    Überprüft, ob die Vertrauenskette intakt ist (auch für selbstsignierte Zertifikate).
- **DNS-Namen**  
    Überprüft, ob das SAN relevante DNS-Namen für die einzelnen Endpunkte enthält oder ob ein unterstützender Platzhalter vorhanden ist.
- **Schlüsselverwendung**  
    Überprüft, ob die Schlüsselverwendung eine digitale Signatur und eine Schlüsselchiffrierung enthält und ob die erweiterte Schlüsselverwendung Serverauthentifizierung und Clientauthentifizierung enthält.
- **Schlüsselgröße**  
    Überprüft, ob die Schlüsselgröße mindestens 2.048 beträgt.
- **Kettenreihenfolge**  
    Überprüft, ob die Reihenfolge der anderen Zertifikate, aus denen sich die Kette zusammensetzt, korrekt ist.
- **Andere Zertifikate**  
    Stellt sicher, dass die PFX-Datei nur das relevante Blattzertifikat und dessen Kette enthält und keine anderen Zertifikate in die Datei gepackt wurden.

> [!IMPORTANT]  
> Das PKI-Zertifikat ist eine PFX-Datei, und das Kennwort muss vertraulich behandelt werden.

## <a name="prerequisites"></a>Voraussetzungen

Zur Überprüfung von PKI-Zertifikaten für eine Azure Stack Hub-Bereitstellung muss Ihr System folgende Voraussetzungen erfüllen:

- Microsoft Azure Stack Hub Readiness Checker
- Mindestens ein SSL-Zertifikat, das gemäß den [Vorbereitungsanweisungen](azure-stack-prepare-pki-certs.md) exportiert wurde
- DeploymentData.json
- Windows 10 oder Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Durchführen der Zertifikatüberprüfung für Kerndienste

Gehen Sie wie folgt vor, um die Azure Stack Hub-PKI-Zertifikate für die Bereitstellung und Geheimnisrotation zu überprüfen:

1. Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um **AzsReadinessChecker** zu installieren:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Erstellen Sie die Zertifikatverzeichnisstruktur. Im folgenden Beispiel können Sie für `<C:\Certificates\Deployment>` einen neuen Verzeichnispfad Ihrer Wahl angeben.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Wenn Sie AD FS als Identitätssystem verwenden, sind AD FS und Graph erforderlich. Beispiel:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Platzieren Sie Ihre Zertifikate in den entsprechenden Verzeichnissen aus dem vorherigen Schritt. Beispiel:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. Ändern Sie im PowerShell-Fenster die Werte von `RegionName`, `FQDN` und `IdentitySystem` gemäß der Azure Stack Hub-Umgebung, und führen Sie das folgende Cmdlet aus:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsHubDeploymentCertificateValidation -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Überprüfen Sie die Ausgabe, und vergewissern Sie sich, dass alle Tests für alle Zertifikate erfolgreich waren. Beispiel:

    ```powershell
    Invoke-AzsHubDeploymentCertificateValidation v1.2005.1286.272 started.
    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ARM Public\ARMPublic.pfx
    Thumbprint: 8DC4D9****************************69DBAA
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: Admin Portal\AdminPortal.pfx
    Thumbprint: 6F9055****************************4AC0EA
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: Public Portal\PublicPortal.pfx


    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsHubDeploymentCertificateValidation Completed

    ```

    Um Zertifikate für andere Azure Stack Hub-Dienste zu überprüfen, ändern Sie den Wert für ```-CertificateType```. Beispiel:

    ```powershell  
    # App Services
    Invoke-AzsHubAppServicesCertificateValidation -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsHubDBAdapterCertificateValidation -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHubs
    Invoke-AzsHubEventHubsCertificateValidation -CertificatePath C:\Certificates\EventHubs -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsHubIoTHubCertificateValidation -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
    Jeder Ordner sollte eine einzelne PFX-Datei für den Zertifikattyp enthalten. Wenn für einen Zertifikattyp mehrere Zertifikatanforderungen vorliegen, werden für jedes einzelne Zertifikat geschachtelte Ordner mit eindeutigen Namen erwartet. Der folgende Code zeigt ein Beispiel für eine Ordner-/Zertifikatstruktur für alle Zertifikattypen und den entsprechenden Wert für ```-CertificateType``` und ```-CertificatePath```.
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHubs           # Invoke-AzsCertificateValidation `
                |       eventhubs.pfx   #   -CertificateType EventHubs `
                |                       #   -CertificatePath C:\Certificates\EventHubs
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```

### <a name="known-issues"></a>Bekannte Probleme

**Symptom**: Tests werden übersprungen.

**Ursache:** AzsReadinessChecker überspringt bestimmte Tests, wenn eine Abhängigkeit nicht erfüllt ist:

 - „Andere Zertifikate“ wird übersprungen, wenn „Vertrauenskette“ nicht erfolgreich ist.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Lösung:** Folgen Sie den Toolanweisungen im Detailabschnitt unter den Tests des jeweiligen Zertifikats.

**Symptom**: Die HTTP-CRL-Prüfung schlägt fehl, obwohl ein HTTP-CDP in x509-Erweiterungen geschrieben wurde.

**Ursache:** Zurzeit kann mit AzsReadinessChecker in einigen Sprachen nicht auf einen HTTP-CDP überprüft werden.

**Lösung:** Führen Sie die Validierung mit der Betriebssystemsprache „EN-US“ aus.

## <a name="certificates"></a>Zertifikate

| Verzeichnis | Zertifikat |
| ---    | ----        |
| ACSBlob | `wildcard_blob_<region>_<externalFQDN>` |
| ACSQueue  |  `wildcard_queue_<region>_<externalFQDN>` |
| ACSTable  |  `wildcard_table_<region>_<externalFQDN>` |
| Administratorerweiterungshost  |  `wildcard_adminhosting_<region>_<externalFQDN>` |
| Verwaltungsportal  |  `adminportal_<region>_<externalFQDN>` |
| ARM Admin  |  `adminmanagement_<region>_<externalFQDN>` |
| ARM Public  |  `management_<region>_<externalFQDN>` |
| KeyVault  |  `wildcard_vault_<region>_<externalFQDN>` |
| KeyVaultInternal  |  `wildcard_adminvault_<region>_<externalFQDN>` |
| Öffentlicher Erweiterungshost  |  `wildcard_hosting_<region>_<externalFQDN>` |
| Öffentliches Portal  |  `portal_<region>_<externalFQDN>` |

## <a name="using-validated-certificates"></a>Verwenden überprüfter Zertifikate

Nach der Überprüfung durch AzsReadinessChecker können Sie die Zertifikate in Ihrer Azure Stack Hub-Bereitstellung oder für die Geheimnisrotation von Azure Stack Hub verwenden.

 - Zur Bereitstellung übertragen Sie Ihre Zertifikate auf sichere Weise an Ihren Bereitstellungstechniker, damit dieser sie auf den Bereitstellungshost kopieren kann (wie unter [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md) beschrieben).
 - Zur Geheimnisrotation können Sie die Zertifikate verwenden, um ältere Zertifikate für die öffentlichen Endpunkte der Infrastruktur Ihrer Azure Stack Hub-Umgebung zu aktualisieren. Eine entsprechende Anleitung finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md).
 - Für PaaS-Dienste können Sie die Zertifikate verwenden, um SQL-, MySQL- und App Services-Ressourcenanbieter in Azure Stack Hub zu installieren. Entsprechende Anweisungen finden Sie unter [Übersicht: Azure Stack Hub-Dienste, -Pläne, -Angebote und Abonnements](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Datencenterintegration: Identität](azure-stack-integrate-identity.md)
