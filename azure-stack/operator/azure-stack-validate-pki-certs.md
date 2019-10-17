---
title: Überprüfen von PKI-Zertifikaten für Azure Stack für die Bereitstellung von in Azure Stack integrierten Systemen | Microsoft-Dokumentation
description: Beschreibt die Vorgehensweise zum Überprüfen von Azure Stack-PKI-Zertifikaten für in Azure Stack integrierte Systeme. Enthält Informationen zur Verwendung des Zertifikatüberprüfungstools von Azure Stack.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 3823aa73d58af48c662690aa0d8e8a21180b4ed6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283239"
---
# <a name="validate-azure-stack-pki-certificates"></a>Überprüfen von Azure Stack-PKI-Zertifikaten

Das in diesem Artikel beschriebene Tool Azure Stack Readiness Checker steht im [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) zur Verfügung. Mit diesem Tool können Sie sich vergewissern, dass die [generierten PKI-Zertifikate](azure-stack-get-pki-certs.md) für die Vorabbereitstellung geeignet sind. Planen Sie bei der Zertifikatüberprüfung genügend Zeit ein, um die Zertifikate testen und ggf. neu ausstellen zu können.

Das Readiness Checker-Tool führt folgende Zertifikatüberprüfungen durch:

- **Lesen der PFX-Datei**  
    Sucht nach einer gültigen PFX-Datei und einem korrekten Kennwort und prüft, ob öffentliche Informationen durch das Kennwort geschützt sind. 
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
- **Kein Profil**  
    Überprüft, ob ein neuer Benutzer die PFX-Daten ohne geladenes Benutzerprofil laden kann. Dadurch wird das Verhalten der gMSA-Konten während der Zertifikatwartung simuliert.

> [!IMPORTANT]  
> Das PKI-Zertifikat ist eine PFX-Datei, und das Kennwort muss vertraulich behandelt werden.

## <a name="prerequisites"></a>Voraussetzungen

Zur Überprüfung von PKI-Zertifikaten für eine Azure Stack-Bereitstellung muss Ihr System über Folgendes verfügen:

- Microsoft Azure Stack Readiness Checker
- Mindestens ein SSL-Zertifikat, das gemäß den [Vorbereitungsanweisungen](azure-stack-prepare-pki-certs.md) exportiert wurde
- DeploymentData.json
- Windows 10 oder Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Durchführen der Zertifikatüberprüfung für Kerndienste

Gehen Sie wie folgt vor, um die Azure Stack-PKI-Zertifikate für die Bereitstellung und Geheimnisrotation vorzubereiten und zu überprüfen:

1. Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um **AzsReadinessChecker** zu installieren:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Erstellen Sie die Zertifikatverzeichnisstruktur. Im folgenden Beispiel können Sie für `<c:\certificates>` einen neuen Verzeichnispfad Ihrer Wahl angeben.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Wenn Sie AD FS als Identitätssystem verwenden, sind AD FS und Graph erforderlich. Beispiel:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Platzieren Sie Ihre Zertifikate in den entsprechenden Verzeichnissen aus dem vorherigen Schritt. Beispiel:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. Ändern Sie im PowerShell-Fenster die Werte von **RegionName** und **FQDN** entsprechend der Azure Stack-Umgebung, und führen Sie Folgendes aus:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Überprüfen Sie die Ausgabe und ob alle Tests für alle Zertifikate erfolgreich waren. Beispiel:

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
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
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Lösung:** Folgen Sie den Toolanweisungen im Detailabschnitt unter den Tests des jeweiligen Zertifikats.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Durchführen der Zertifikatüberprüfung für Platform-as-a-Service-Zertifikate

Gehen Sie folgendermaßen vor, um die Azure Stack-PKI-Zertifikate für Platform-as-a-Service-Zertifikate (PaaS-Zertifikate) vorzubereiten und zu überprüfen, wenn SQL/MySQL- oder App Services-Bereitstellungen geplant sind.

1.  Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um **AzsReadinessChecker** zu installieren:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Erstellen Sie eine geschachtelte Hashtabelle, die Pfade und ein Kennwort für jedes PaaS-Zertifikat enthält, das überprüft werden muss. Führen Sie im PowerShell-Fenster Folgendes aus:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Ändern Sie die Werte von **RegionName** und **FQDN** entsprechend Ihrer Azure Stack-Umgebung, um die Überprüfung zu starten. Führen Sie anschließend Folgendes aus:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Überprüfen Sie die Ausgabe und ob alle Tests für alle Zertifikate erfolgreich waren.

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>Zertifikate

| Verzeichnis | Zertifikat |
| ---    | ----        |
| acsBlob | wildcard_blob_\<Region>_\<externer FQDN> |
| ACSQueue  |  wildcard_queue_\<Region>_\<externer FQDN> |
| ACSTable  |  wildcard_table_\<Region>_\<externer FQDN> |
| Administratorerweiterungshost  |  wildcard_adminhosting_\<Region>_\<externer FQDN> |
| Verwaltungsportal  |  adminportal_\<Region>_\<externer FQDN> |
| ARM Admin  |  adminmanagement_\<Region>_\<externer FQDN> |
| ARM Public  |  management_\<Region>_\<externer FQDN> |
| KeyVault  |  wildcard_vault_\<Region>_\<externer FQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<Region>_\<externer FQDN> |
| Öffentlicher Erweiterungshost  |  wildcard_hosting_\<Region>_\<externer FQDN> |
| Öffentliches Portal  |  portal_\<Region>_\<externer FQDN> |

## <a name="using-validated-certificates"></a>Verwenden überprüfter Zertifikate

Nach der Überprüfung durch AzsReadinessChecker können Sie die Zertifikate in Ihrer Azure Stack-Bereitstellung oder für die Geheimnisrotation von Azure Stack verwenden. 

 - Bereitstellung: Übertragen Sie Ihre Zertifikate auf sichere Weise an Ihren Bereitstellungstechniker, damit dieser sie auf den Bereitstellungshost kopieren kann (wie unter [Azure Stack-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md) beschrieben).
 - Geheimnisrotation: Sie können die Zertifikate verwenden, um ältere Zertifikate für die Endpunkte der öffentlichen Infrastruktur von Azure Stack zu aktualisieren. Eine entsprechende Anleitung finden Sie unter [Rotieren von Geheimnissen in Azure Stack](azure-stack-rotate-secrets.md).
 - PaaS-Dienste: Sie können die Zertifikate verwenden, um SQL-, MySQL- und App Services-Ressourcenanbieter in Azure Stack zu installieren. Entsprechende Anweisungen finden Sie unter [Übersicht über das Anbieten von Diensten in Azure Stack](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Datencenterintegration: Identität](azure-stack-integrate-identity.md)
