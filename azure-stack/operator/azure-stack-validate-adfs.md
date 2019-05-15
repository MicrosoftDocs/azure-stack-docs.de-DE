---
title: Überprüfen der AD FS-Integration für Azure Stack
description: Verwenden Sie Azure Stack Readiness Checker, um eine AD FS-Integration für Azure Stack zu überprüfen.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 09aa51c329f7f4fd7004312092ff09c9ceb091d7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984086"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Überprüfen der AD FS-Integration für Azure Stack

Verwenden Sie das Tool Azure Stack Readiness Checker (AzsReadinessChecker), um zu überprüfen, ob Ihre Umgebung für die AD FS-Integration (Active Directory-Verbunddienste) in Azure Stack verwendet werden kann. Überprüfen Sie die AD FS-Integration, bevor Sie mit der Integration von Rechenzentren oder mit einer Azure Stack-Bereitstellung beginnen.

Bei der Überprüfung der Bereitschaft wird Folgendes geprüft:

* Die *Verbundmetadaten* enthalten die gültigen XML-Elemente für den Verbund.
* Das *AD FS-SSL-Zertifikat* kann abgerufen werden, und es kann eine Zertifikatvertrauenskette erstellt werden. Beim Stempel muss AD FS der SSL-Zertifikatkette vertrauen. Das Zertifikat muss von derselben *Zertifizierungsstelle* signiert werden wie die Azure Stack-Bereitstellungszertifikate oder von einem vertrauenswürdigen Stammzertifizierungsstellen-Partner. Die vollständige Liste der vertrauenswürdigen Stammzertifizierungsstellenpartner finden Sie bei [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* Das *AD FS-Signaturzertifikat* ist vertrauenswürdig und läuft nicht in Kürze ab.

Weitere Informationen zur Rechenzentrumsintegration mit Azure Stack finden Sie unter [Azure Stack-Rechenzentrumsintegration – Identität](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Beziehen des Tools zur Bereitschaftsüberprüfung

Laden Sie die neueste Version des Tools Azure Stack Readiness Checker (AzsReadinessChecker) aus dem [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) herunter.  

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt werden:

**Auf dem Computer, auf dem das Tool ausgeführt wird, muss Folgendes installiert sein**:

* Windows 10 oder Windows Server 2016 mit Domänenkonnektivität
* PowerShell 5.1 oder höher Um Ihre Version zu überprüfen, führen Sie den folgenden PowerShell-Befehl aus, und überprüfen Sie dann die *Hauptversion* und die *Nebenversionen*:  
   > `$PSVersionTable.PSVersion`
* Die neueste Version des Tools [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Active Directory-Verbunddienste-Umgebung:**

Sie benötigen mindestens eine der folgenden Formen von Metadaten:

* Die URL für AD FS-Verbundmetadaten. Ein Beispiel ist `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Die Verbundmetadaten-XML-Datei. Beispiel: „FederationMetadata.xml“.

## <a name="validate-ad-fs-integration"></a>Überprüfen der AD FS-Integration

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine administrative PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl zum Installieren von AzsReadinessChecker aus:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um die Validierung zu starten. Geben Sie den Wert **-CustomADFSFederationMetadataEndpointUri** als URI für die Verbundmetadaten an.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Wenn das Tool ausgeführt wird, überprüfen Sie die Ausgabe. Vergewissern Sie sich, dass der Status für die Anforderungen an die AD FS-Integration „OK“ lautet. Eine erfolgreiche Validierung ähnelt dem folgenden Beispiel:

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

In Produktionsumgebungen ist das Testen der Zertifikatvertrauenskette von der Arbeitsstation eines Operators aus nicht ausreichend für die PKI-Vertrauenssituation in der Azure Stack-Infrastruktur. Das Netzwerk mit öffentlicher VIP des Azure Stack-Stempels benötigt die Verbindung mit der CRL für die PKI-Infrastruktur.

## <a name="report-and-log-file"></a>Berichts- und Protokolldatei

Bei jeder Ausführung einer Überprüfung werden Ergebnisse in den Dateien **AzsReadinessChecker.log** und **AzsReadinessCheckerReport.json** protokolliert. Der Speicherort dieser Dateien wird mit den Überprüfungsergebnissen in PowerShell angezeigt.

Anhand dieser Validierungsdateien können Sie den Status freigeben, bevor Sie Azure Stack bereitstellen oder Probleme mit der Validierung untersuchen. Die Ergebnisse aller nachfolgenden Überprüfungen werden in beiden Dateien gespeichert. Der Bericht enthält die Bestätigung Ihres Bereitstellungsteams über die Identitätskonfiguration. Mithilfe der Protokolldatei kann Ihr Bereitstellungs- oder Supportteam Probleme bei der Überprüfung untersuchen.

Standardmäßig werden beide Dateien in `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\` geschrieben.

Verwendung:

* **-OutputPath**: Der *path*-Parameter am Ende der Befehlsausführung zum Angeben eines anderen Berichtsspeicherorts.
* **-CleanReport**: Der Parameter am Ende der Befehlszeilenausführung zum Löschen der Datei „AzsReadinessCheckerReport.json“ mit früheren Berichtsinformationen. Weitere Informationen finden Sie unter [Azure Stack-Überprüfungsbericht](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Fehler bei der Überprüfung

Wenn bei der Überprüfung Fehler auftreten, werden im PowerShell-Fenster Details zum Fehler angezeigt. Das Tool protokolliert Informationen außerdem in *AzsReadinessChecker.log*.

Die folgenden Beispiele veranschaulichen, wie häufige Fehler bei der Überprüfung behoben werden.

### <a name="command-not-found"></a>Der Befehl wurde nicht gefunden.

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Ursache:** PowerShell Autoload konnte das Readiness Checker-Modul nicht ordnungsgemäß laden.

**Lösung:** Importieren Sie das Readiness Checker-Modul explizit. Kopieren Sie den folgenden Code in PowerShell, und ersetzen Sie \<version\> durch die Versionsnummer der derzeit installierten Version.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Nächste Schritte

[Anzeigen des Bereitschaftsberichts](azure-stack-validation-report.md)  
[Allgemeine Überlegungen zur Azure Stack-Integration](azure-stack-datacenter-integration.md)  
