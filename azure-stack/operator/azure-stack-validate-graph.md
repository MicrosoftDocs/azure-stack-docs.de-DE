---
title: Überprüfen der Azure Graph-Integration für Azure Stack
description: Verwenden Sie Azure Stack Readiness Checker, um eine Graph-Integration für Azure Stack zu überprüfen.
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
ms.openlocfilehash: a34d070e5e26113216fe8a5fd598b0b5e27687f2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984539"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Überprüfen der Graph-Integration für Azure Stack

Verwenden Sie das Tool Azure Stack Readiness Checker (AzsReadinessChecker), um zu überprüfen, ob Ihre Umgebung für die Graph-Integration in Azure Stack verwendet werden kann. Überprüfen Sie die Graph-Integration, bevor Sie mit der Integration von Rechenzentren oder mit einer Azure Stack-Bereitstellung beginnen.

Bei der Überprüfung der Bereitschaft wird Folgendes geprüft:

* Die Anmeldeinformationen für das für die Graph-Integration erstellte Dienstkonto weisen die erforderlichen Rechte zum Abfragen von Active Directory auf.
* Der *globale Katalog* kann aufgelöst und kontaktiert werden.
* Das KDC kann aufgelöst und kontaktiert werden.
* Die erforderlichen Netzwerkverbindungen sind vorhanden.

Weitere Informationen zur Rechenzentrumsintegration mit Azure Stack finden Sie unter [Azure Stack-Rechenzentrumsintegration – Identität](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Beziehen des Tools zur Bereitschaftsüberprüfung

Laden Sie die neueste Version des Tools Azure Stack Readiness Checker (AzsReadinessChecker) aus dem [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) herunter.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt werden:

**Auf dem Computer, auf dem das Tool ausgeführt wird, muss Folgendes installiert sein**:

* Windows 10 oder Windows Server 2016 mit Domänenkonnektivität
* PowerShell 5.1 oder höher Um Ihre Version zu überprüfen, führen Sie den folgenden PowerShell-Befehl aus, und überprüfen Sie dann die *Hauptversion* und die *Nebenversionen*:  
   > `$PSVersionTable.PSVersion`
* Active Directory-PowerShell-Modul.
* Die neueste Version des Tools [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Active Directory-Umgebung:**

* Angabe des Benutzernamens und Kennworts für ein Konto für den Graph-Dienst in der vorhandenen Active Directory-Instanz
* Angabe des FQDN der Stammdomäne der Active Directory-Gesamtstruktur

## <a name="validate-the-graph-service"></a>Überprüfen des Graph-Diensts

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine administrative PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl zum Installieren von AzsReadinessChecker aus:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um die Variable *$graphCredential* im Graph-Konto festzulegen. Ersetzen Sie `contoso\graphservice` durch Ihr Konto im Format `domain\username`.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um die Validierung für den Graph-Dienst zu starten. Geben Sie den Wert für **-ForestFQDN** als den FQDN der Gesamtstruktur-Stammdomäne an.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Wenn das Tool ausgeführt wird, überprüfen Sie die Ausgabe. Vergewissern Sie sich, dass der Status für die Anforderungen an die Graph-Integration „OK“ lautet. Eine erfolgreiche Validierung ähnelt dem folgenden Beispiel:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

In Produktionsumgebungen ist das Testen der Netzwerkverbindung von der Arbeitsstation eines Operators aus nicht ausreichend für die für Azure Stack verfügbare Verbindung. Das Netzwerk mit öffentlicher VIP des Azure Stack-Stempels benötigt die Verbindung, damit die Identitätsintegration durch LDAP-Datenverkehr erfolgen kann.

## <a name="report-and-log-file"></a>Berichts- und Protokolldatei

Bei jeder Ausführung einer Überprüfung werden Ergebnisse in den Dateien **AzsReadinessChecker.log** und **AzsReadinessCheckerReport.json** protokolliert. Der Speicherort dieser Dateien wird mit den Überprüfungsergebnissen in PowerShell angezeigt.

Anhand dieser Validierungsdateien können Sie den Status freigeben, bevor Sie Azure Stack bereitstellen oder Probleme mit der Validierung untersuchen. Die Ergebnisse aller nachfolgenden Überprüfungen werden in beiden Dateien gespeichert. Der Bericht enthält die Bestätigung Ihres Bereitstellungsteams über die Identitätskonfiguration. Mithilfe der Protokolldatei kann Ihr Bereitstellungs- oder Supportteam Probleme bei der Überprüfung untersuchen.

Standardmäßig werden beide Dateien in `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\` geschrieben.

Verwendung:

* **-OutputPath**: Der *path*-Parameter am Ende der Befehlsausführung zum Angeben eines anderen Berichtsspeicherorts.
* **-CleanReport**: Der Parameter am Ende der Befehlszeilenausführung zum Löschen der Datei *AzsReadinessCheckerReport.json* mit früheren Berichtsinformationen. Weitere Informationen finden Sie unter [Azure Stack-Überprüfungsbericht](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Fehler bei der Überprüfung

Wenn bei der Überprüfung Fehler auftreten, werden im PowerShell-Fenster Details zum Fehler angezeigt. Das Tool protokolliert Informationen außerdem in *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Nächste Schritte

[Anzeigen des Bereitschaftsberichts](azure-stack-validation-report.md)  
[Allgemeine Überlegungen zur Azure Stack-Integration](azure-stack-datacenter-integration.md)  
