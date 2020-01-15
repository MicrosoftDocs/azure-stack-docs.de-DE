---
title: Generieren von Zertifikatsignieranforderungen für Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Zertifikatsignieranforderungen für Azure Stack-PKI-Zertifikate in integrierten Azure Stack-Systemen generieren.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 9796bec883d69a910b25895b326ed66cb9e8522b
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727376"
---
# <a name="generate-certificate-signing-requests-for-azure-stack"></a>Generieren von Zertifikatsignieranforderungen für Azure Stack

Sie können mit dem Azure Stack Readiness Checker-Tool Zertifikatsignieranforderungen (CSRs) erstellen, die für eine Azure Stack-Bereitstellung geeignet sind. Zertifikate müssen mit genügend Zeit zum Testen vor dem Einsatz angefordert, generiert und validiert werden. Sie können das Tool [aus dem PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) abrufen.

Mit dem Azure Stack Readiness Checker-Tool (AzsReadinessChecker) können Sie die folgenden Zertifikate anfordern:

- **Standardzertifikatanforderungen** gemäß [Generieren der Zertifikatsignieranforderung](azure-stack-get-pki-certs.md#generate-certificate-signing-requests).
- **Platform-as-a-Service**: Sie können PaaS-Namen (Platform-as-a-Service ) für Zertifikate anfordern, wie unter [Azure Stack-PKI-Zertifikatanforderungen: Optionale PaaS-Zertifikate](azure-stack-pki-certs.md#optional-paas-certificates) angegeben.

## <a name="prerequisites"></a>Voraussetzungen

Ihr System muss die folgenden Voraussetzungen erfüllen, bevor Sie Zertifikatsignieranforderungen für PKI-Zertifikate für eine Azure Stack-Bereitstellung generieren:

- Microsoft Azure Stack Readiness Checker
- Zertifikatattribute:
  - Regionsname
  - Externer vollqualifizierter Domänenname (FQDN)
  - Subject
- Windows 10 oder Windows Server 2016 oder höher

  > [!NOTE]  
  > Wenn Sie die Zertifikate von Ihrer Zertifizierungsstelle erhalten haben, müssen Sie auf demselben System die Schritte in [Vorbereiten von Azure Stack-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md) ausführen!

## <a name="generate-certificate-signing-requests"></a>Generieren von Zertifikatsignieranforderungen

Gehen Sie wie folgt vor, um die Azure Stack-PKI-Zertifikate vorzubereiten und zu überprüfen:

1. Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um AzsReadinessChecker zu installieren:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarieren Sie den **Antragsteller**. Beispiel:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack"
    ```

    > [!NOTE]  
    > Wenn ein allgemeiner Name (Common Name, CN) angegeben wird, wird er für jede Zertifikatanforderung konfiguriert. Wenn kein CN angegeben wird, wird für die Zertifikatanforderung der erste DNS-Name des Azure Stack-Diensts konfiguriert.

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

5. Deklarieren Sie einen **Regionsnamen** und **externen FQDN** für die Azure Stack-Bereitstellung.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` bildet die Grundlage für die Erstellung aller externen DNS-Namen in Azure Stack. In diesem Beispiel heißt das Portal `portal.east.azurestack.contoso.com`.  

6. So generieren Sie Zertifikatsignieranforderungen für die Bereitstellung:

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Um Zertifikatanforderungen für andere Azure Stack-Dienste zu generieren, ändern Sie den Wert für `-CertificateType`. Beispiel:

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHub
    New-AzsCertificateSigningRequest -certificateType EventHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Um als Alternative für Dev/Test-Umgebungen eine einzelne Zertifikatanforderung mit mehreren alternativen Antragstellernamen zu generieren, fügen Sie den Parameter und Wert **--RequestType SingleCSR** hinzu (wird für Produktionsumgebungen **nicht** empfohlen):

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Überprüfen Sie die Ausgabe:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Übermitteln Sie die generierte **REQ**-Datei an Ihre (interne oder öffentliche) Zertifizierungsstelle. Das Ausgabeverzeichnis von **New-AzsCertificateSigningRequest** enthält die Zertifikatsignieranforderungen, die für die Übermittlung an eine Zertifizierungsstelle erforderlich sind. Das Verzeichnis enthält zu Ihrer Referenz auch ein Unterverzeichnis, das die bei der Generierung von Zertifikatanforderungen verwendeten INF-Dateien enthält. Vergewissern Sie sich, dass Ihre Zertifizierungsstelle Zertifikate mit Ihrer generierten Anforderung erstellt, die die [Azure Stack-PKI-Voraussetzungen](azure-stack-pki-certs.md) erfüllen.

## <a name="next-steps"></a>Nächste Schritte

[Vorbereiten von Azure Stack-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md)
