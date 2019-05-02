---
title: Generieren von PKI-Zertifikaten für Azure Stack für die Bereitstellung von in Azure Stack integrierten Systemen | Microsoft-Dokumentation
description: Beschreibt den Bereitstellungsprozess für Azure Stack-PKI-Zertifikate für in Azure Stack integrierte Systeme.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: e0556eb5cc3d0f140067a4e3b4a9054a47b91417
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307128"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Generieren von Signieranforderungen für Azure Stack-Zertifikate

Sie können mit dem Azure Stack Readiness Checker-Tool Zertifikatsignieranforderungen (CSRs) erstellen, die für eine Azure Stack-Bereitstellung geeignet sind. Zertifikate müssen mit genügend Zeit zum Testen vor dem Einsatz angefordert, generiert und validiert werden. Sie können das Tool [aus dem PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) abrufen.

Mit dem Azure Stack Readiness Checker-Tool (AzsReadinessChecker) können Sie die folgenden Zertifikate anfordern:

- **Standardzertifikatanforderungen** gemäß [Generieren von PKI-Zertifikaten für die Azure Stack-Bereitstellung](azure-stack-get-pki-certs.md).
- **Platform-as-a-Service**: Sie können PaaS-Namen (Platform-as-a-Service ) für Zertifikate anfordern, wie unter [Azure Stack-PKI-Zertifikatanforderungen: Optionale PaaS-Zertifikate](azure-stack-pki-certs.md#optional-paas-certificates) angegeben.

## <a name="prerequisites"></a>Voraussetzungen

Ihr System muss die folgenden Voraussetzungen erfüllen, bevor Sie die Zertifikatsignieranforderungen für PKI-Zertifikate für eine Azure Stack-Bereitstellung generieren:

- Microsoft Azure Stack Readiness Checker
- Zertifikatattribute:
  - Regionsname
  - Externer vollqualifizierter Domänenname (FQDN)
  - Subject
- Windows 10 oder Windows Server 2016

  > [!NOTE]  
  > Wenn Sie Ihre Zertifikate von Ihrer Zertifizierungsstelle erhalten haben, müssen Sie auf demselben System die Schritte in [Vorbereiten von Azure Stack-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md) ausführen!

## <a name="generate-certificate-signing-requests"></a>Generieren der Zertifikatsignieranforderungen

Gehen Sie wie folgt vor, um die Azure Stack-PKI-Zertifikate vorzubereiten und zu überprüfen:

1. Führen Sie an einer PowerShell-Eingabeaufforderung (5.1 oder höher) das folgende Cmdlet aus, um AzsReadinessChecker zu installieren:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarieren Sie den **Antragsteller** als sortiertes Wörterbuch. Beispiel: 

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Wenn ein allgemeiner Name (CN) angegeben ist, wird dieser vom ersten DNS-Namen der Zertifikatanforderung überschrieben.

3. Deklarieren Sie ein Ausgabeverzeichnis, das bereits vorhanden ist. Beispiel: 

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Deklarieren des Identitätssystems

    Azure Active Directory

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory-Verbunddienste (AD FS)

    ```powershell
    $IdentitySystem = "ADFS"
    ```

5. Deklarieren Sie einen **Regionsnamen** und **externen FQDN** für die Azure Stack-Bereitstellung.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` bildet die Grundlage für die Erstellung aller externen DNS-Namen in Azure Stack. In diesem Beispiel heißt das Portal `portal.east.azurestack.contoso.com`.  

6. So generieren Sie Zertifikatsignieranforderungen für jeden DNS-Namen

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Um PaaS-Dienste einzuschließen, geben Sie den Schalter ```-IncludePaaS``` an.

7. Um als Alternative für Dev/Test-Umgebungen eine einzelne Zertifikatanforderung mit mehreren alternativen Antragstellernamen zu generieren, fügen Sie den Parameter und Wert **--RequestType SingleCSR** hinzu (wird für Produktionsumgebungen **nicht** empfohlen):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Um PaaS-Dienste einzuschließen, geben Sie den Schalter ```-IncludePaaS``` an.

8. Überprüfen Sie die Ausgabe:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Übermitteln Sie die generierte **REQ**-Datei an Ihre (interne oder öffentliche) Zertifizierungsstelle.  Das Ausgabeverzeichnis von **New-AzsCertificateSigningRequest** enthält die Zertifikatsignieranforderungen, die für die Übermittlung an eine Zertifizierungsstelle erforderlich sind.  Das Verzeichnis enthält zu Ihrer Referenz auch ein Unterverzeichnis, das die bei der Generierung von Zertifikatanforderungen verwendeten INF-Dateien enthält. Vergewissern Sie sich, dass Ihre Zertifizierungsstelle Zertifikate mit Ihrer generierten Anforderung erstellt, die die [Azure Stack-PKI-Voraussetzungen](azure-stack-pki-certs.md) erfüllen.

## <a name="next-steps"></a>Nächste Schritte

[Vorbereiten von Azure Stack-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md)
