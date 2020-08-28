---
title: Generieren von Zertifikatsignieranforderungen für Azure Stack Hub
description: Es wird beschrieben, wie Sie Zertifikatsignieranforderungen für Azure Stack Hub-PKI-Zertifikate in integrierten Azure Stack Hub-Systemen generieren.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/10/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 6bcdc7aacfadb37d348eaa33449065b9fb345446
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818350"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Generieren von Zertifikatsignieranforderungen für Azure Stack Hub

Sie können mit dem Azure Stack Hub Readiness Checker-Tool Zertifikatsignieranforderungen (CSRs) erstellen, die für eine Azure Stack Hub-Bereitstellung geeignet sind. Zertifikate müssen mit genügend Zeit zum Testen vor dem Einsatz angefordert, generiert und validiert werden. Sie können das Tool [aus dem PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) abrufen.

Mit dem Azure Stack Hub Readiness Checker-Tool (AzsReadinessChecker) können Sie die folgenden Zertifikate anfordern:

- **Standardzertifikatanforderungen** gemäß [Generieren der Zertifikatsignieranforderung für neue Bereitstellungen](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments).
- **Erneuerungszertifikatanforderungen** gemäß [Generieren der Zertifikatsignieranforderung für die Zertifikaterneuerung](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal).
- **Platform-as-a-Service**: Sie können PaaS-Namen (Platform-as-a-Service ) für Zertifikate anfordern, wie unter [Azure Stack Hub-PKI-Zertifikatanforderungen: Optionale PaaS-Zertifikate](azure-stack-pki-certs.md#optional-paas-certificates) angegeben.

## <a name="prerequisites"></a>Voraussetzungen

Ihr System muss die folgenden Voraussetzungen erfüllen, bevor Sie Zertifikatsignieranforderungen für PKI-Zertifikate für eine Azure Stack Hub-Bereitstellung generieren:

- Microsoft Azure Stack Hub Readiness Checker
- Zertifikatattribute:
  - Regionsname
  - Externer vollqualifizierter Domänenname (FQDN)
  - Subject
- Windows 10 oder Windows Server 2016 oder höher

  > [!NOTE]  
  > Wenn Sie die Zertifikate von Ihrer Zertifizierungsstelle erhalten haben, müssen Sie auf demselben System die Schritte in [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md) ausführen!

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>Generieren von Zertifikatsignieranforderungen für neue Bereitstellungen

Gehen Sie wie folgt vor, um Zertifikatsignieranforderungen für neue Azure Stack Hub-PKI-Zertifikate vorzubereiten:

1. Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um AzsReadinessChecker zu installieren:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarieren Sie den **Antragsteller**. Beispiel:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Wenn ein allgemeiner Name (Common Name, CN) angegeben wird, wird er für jede Zertifikatanforderung konfiguriert. Wenn kein CN angegeben wird, wird für die Zertifikatanforderung der erste DNS-Name des Azure Stack Hub-Diensts konfiguriert.

3. Deklarieren Sie ein Ausgabeverzeichnis, das bereits vorhanden ist. Beispiel:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Deklarieren des Identitätssystems

    Azure Active Directory (Azure AD):

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory-Verbunddienste (AD FS):

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > Der Parameter ist nur für die CertificateType-Bereitstellung erforderlich.

5. Deklarieren Sie einen **Regionsnamen** und **externen FQDN** für die Azure Stack Hub-Bereitstellung.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` bildet die Grundlage für die Erstellung aller externen DNS-Namen in Azure Stack Hub. In diesem Beispiel heißt das Portal `portal.east.azurestack.contoso.com`.  

6. So generieren Sie Zertifikatsignieranforderungen für die Bereitstellung:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Um Zertifikatanforderungen für andere Azure Stack Hub-Dienste zu generieren, ändern Sie den Wert für `-CertificateType`. Beispiel:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDbAdapterCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIoTHubCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Um als Alternative für Dev/Test-Umgebungen eine einzelne Zertifikatanforderung mit mehreren alternativen Antragstellernamen zu generieren, fügen Sie den Parameter und Wert **--RequestType SingleCSR** hinzu (wird für Produktionsumgebungen **nicht** empfohlen):

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Überprüfen Sie die Ausgabe:

    ```powershell  
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710165538.req
    Certreq.exe output: CertReq: Request Created
    ```

9.  Übermitteln Sie die generierte **REQ**-Datei an Ihre (interne oder öffentliche) Zertifizierungsstelle. Das Ausgabeverzeichnis von **New-AzsCertificateSigningRequest** enthält die Zertifikatsignieranforderungen, die für die Übermittlung an eine Zertifizierungsstelle erforderlich sind. Das Verzeichnis enthält zu Ihrer Referenz auch ein Unterverzeichnis, das die bei der Generierung von Zertifikatanforderungen verwendeten INF-Dateien enthält. Vergewissern Sie sich, dass Ihre Zertifizierungsstelle Zertifikate mit Ihrer generierten Anforderung erstellt, die die [Azure Stack Hub-PKI-Voraussetzungen](azure-stack-pki-certs.md) erfüllen.

## <a name="generate-certificate-signing-requests-for-certificate-renewal"></a>Generieren von Zertifikatsignieranforderungen für die Zertifikaterneuerung

Gehen Sie wie folgt vor, um Zertifikatsignieranforderungen für die Erneuerung vorhandener Azure Stack Hub-PKI-Zertifikate vorzubereiten:

1. Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um AzsReadinessChecker zu installieren:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarieren Sie **stampEndpoint** im Format „regionname.domain.com“ des Azure Stack Hub-Systems. Beispiel (wenn die Adresse des Azure Stack Hub-Mandantenportals https://portal.east.azurestack.contoso.com): lautet):

    ```powershell  
    $stampEndpoint = 'east.azurestack.contoso.com'
    ```

    > [!NOTE]  
    > Für das oben genannte Azure Stack Hub-System ist HTTPS-Konnektivität erforderlich.
    > Die Bereitschaftsüberprüfung verwendet den stampEndpoint (Region und Domäne), um einen Zeiger auf vorhandene Zertifikate zu erstellen, die für den Zertifikattyp erforderlich sind, z. B. wird bei Bereitstellungszertifikaten vom Tool „portal“ vorangestellt, sodass „portal.east.azurestack.contoso.com“ beim Klonen von Zertifikaten, für AppServices „sso.appservices.east.azurestack.contoso.com“ usw. verwendet wird. Das an den berechneten Endpunkt gebundene Zertifikat wird zum Klonen von Attributen wie Betreff, Schlüssellänge, Signaturalgorithmus verwendet.  Wenn Sie eines dieser Attribute ändern möchten, sollten Sie stattdessen die Schritte zum [Generieren der Zertifikatsignieranforderung für neue Bereitstellungen](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments) befolgen.

3. Deklarieren Sie ein Ausgabeverzeichnis, das bereits vorhanden ist. Beispiel:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. So generieren Sie Zertifikatsignieranforderungen für die Bereitstellung:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

    Um Zertifikatanforderungen für andere Azure Stack Hub-Dienste zu generieren, verwenden Sie:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDBAdapterCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIotHubCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

5. Um als Alternative für Dev/Test-Umgebungen eine einzelne Zertifikatanforderung mit mehreren alternativen Antragstellernamen zu generieren, fügen Sie den Parameter und Wert **--RequestType SingleCSR** hinzu (wird für Produktionsumgebungen **nicht** empfohlen):

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampendpoint -OutputRequestPath $OutputDirectory -RequestType SingleCSR
    ```

6.  Überprüfen Sie die Ausgabe:

    ```powershell  
    Querying StampEndpoint portal.east.azurestack.contoso.com for existing certificate
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710122723.req
    Certreq.exe output: CertReq: Request Created
    ```

7.  Übermitteln Sie die generierte **REQ**-Datei an Ihre (interne oder öffentliche) Zertifizierungsstelle. Das Ausgabeverzeichnis von **New-AzsCertificateSigningRequest** enthält die Zertifikatsignieranforderungen, die für die Übermittlung an eine Zertifizierungsstelle erforderlich sind. Das Verzeichnis enthält zu Ihrer Referenz auch ein Unterverzeichnis, das die bei der Generierung von Zertifikatanforderungen verwendeten INF-Dateien enthält. Vergewissern Sie sich, dass Ihre Zertifizierungsstelle Zertifikate mit Ihrer generierten Anforderung erstellt, die die [Azure Stack Hub-PKI-Voraussetzungen](azure-stack-pki-certs.md) erfüllen.

## <a name="next-steps"></a>Nächste Schritte

[Vorbereiten von Azure Stack Hub-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md)
