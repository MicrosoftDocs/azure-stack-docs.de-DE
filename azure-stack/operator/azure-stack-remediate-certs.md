---
title: Beheben allgemeiner Probleme mit PKI-Zertifikaten
titleSuffix: Azure Stack
description: Hier erfahren Sie, wie Sie mithilfe von Azure Stack Readiness Checker allgemeine Probleme mit Azure Stack-PKI-Zertifikaten beheben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 3cff638fa242e4d70230062e7d54eef0c9c66802
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465403"
---
# <a name="fix-common-issues-with-azure-stack-pki-certificates"></a>Beheben allgemeiner Probleme mit Azure Stack-PKI-Zertifikaten

In diesem Artikel werden allgemeine Probleme mit Azure Stack-PKI-Zertifikaten sowie entsprechende Problemlösungen beschrieben. Sie können Probleme ermitteln, indem Sie das Tool Azure Stack Readiness Checker zur [Überprüfung von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md) verwenden. Das Tool überprüft, ob die Zertifikate die PKI-Anforderungen einer Azure Stack-Bereitstellung und einer Azure Stack-Geheimnisrotation erfüllen, und protokolliert die Ergebnisse in einer Datei vom Typ [report.json](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>PFX-Verschlüsselung

**Problem:** Bei der PFX-Verschlüsselung handelt es sich nicht um TripleDES-SHA1.

**Lösung:** Exportieren Sie PFX-Dateien mit der Verschlüsselung **TripleDES-SHA1**. Dies ist die Standardverschlüsselung für alle Windows 10-Clients, wenn Sie den Export über das Zertifikat-Snap-In oder mithilfe von `Export-PFXCertificate` durchführen.

## <a name="read-pfx"></a>Lesen einer PFX-Datei

**Warnung**: Ein Kennwort schützt nur persönliche Informationen im Zertifikat.  

**Lösung:** Exportieren Sie PFX-Dateien mit der optionalen Einstellung **Zertifikatdatenschutz aktivieren**.  

**Problem:** Die PFX-Datei ist ungültig.  

**Lösung:** Exportieren Sie das Zertifikat mithilfe der Schritte unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) erneut.

## <a name="signature-algorithm"></a>Signaturalgorithmus

**Problem:** Der Signaturalgorithmus ist SHA1.

**Lösung:** Führen Sie die Schritte unter „Generieren von Zertifikatsignieranforderungen für Azure Stack“ aus, um die Zertifikatsignieranforderung (Certificate Signing Request, CSR) mit dem Signaturalgorithmus SHA256 zu generieren. Senden Sie die CSR dann erneut an die Zertifizierungsstelle, um das Zertifikat neu auszustellen.

## <a name="private-key"></a>Privater Schlüssel

**Problem:** Der private Schlüssel fehlt oder enthält nicht das Attribut des lokalen Computers.  

**Lösung:** Exportieren Sie über den Computer, mit dem die CSR generiert wurde, das Zertifikat mithilfe der Schritte unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) erneut. Diese Schritte umfassen das Exportieren aus dem Zertifikatspeicher des lokalen Computers.

## <a name="certificate-chain"></a>Zertifikatkette

**Problem:** Die Vertrauenskette ist nicht vollständig.  

**Lösung:** Zertifikate müssen eine vollständige Vertrauenskette enthalten. Exportieren Sie das Zertifikat erneut wie unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) beschrieben, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus.

## <a name="dns-names"></a>DNS-Namen

**Problem:** **DNSNameList** für das Zertifikat enthält nicht den Namen des Azure Stack-Dienstendpunkts oder einen gültigen Platzhalter. Platzhalterübereinstimmungen gelten nur für den Namespace der ganz links vom DNS-Namen steht. `*.region.domain.com` gilt beispielsweise nur für `portal.region.domain.com`, aber nicht für `*.table.region.domain.com`.

**Lösung:** Führen Sie die Schritte unter „Generieren von Zertifikatsignieranforderungen für Azure Stack“ aus, um die CSR zur Unterstützung von Azure Stack-Endpunkten mit den richtigen DNS-Namen erneut zu generieren. Übermitteln Sie die CSR erneut an eine Zertifizierungsstelle. Führen Sie dann die Schritte unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) aus, um das Zertifikat über den Computer, mit dem die CSR generiert wurde, zu exportieren.  

## <a name="key-usage"></a>Schlüsselverwendung

**Problem:** Bei der Schlüsselverwendung fehlt die digitale Signatur oder Schlüsselverschlüsselung. Oder: Bei der erweiterten Schlüsselverwendung fehlt die Server- oder Clientauthentifizierung.  

**Lösung:** Führen Sie die Schritte unter [Generieren von Zertifikatsignieranforderungen für Azure Stack](azure-stack-get-pki-certs.md) aus, um die CSR mit den richtigen Schlüsselverwendungsattributen erneut zu generieren. Übermitteln Sie die Zertifikatsignieranforderung erneut an die Zertifizierungsstelle, und vergewissern Sie sich, dass die Schlüsselverwendung in der Anforderung nicht durch eine Zertifikatvorlage überschrieben wird.

## <a name="key-size"></a>Schlüsselgröße

**Problem:** Die Schlüsselgröße ist kleiner als 2.048.

**Lösung**: Führen Sie die Schritte unter [Generieren von Zertifikatsignieranforderungen für Azure Stack](azure-stack-get-pki-certs.md) aus, um die Zertifikatsignieranforderung mit der richtigen Schlüssellänge (2.048) erneut zu generieren, und übermitteln Sie die Zertifikatsignieranforderung anschließend erneut an die Zertifizierungsstelle.

## <a name="chain-order"></a>Kettenreihenfolge

**Problem:** Die Reihenfolge der Vertrauenskette ist falsch.  

**Lösung:** Exportieren Sie das Zertifikat wie unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) beschrieben erneut, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus. Stellen Sie sicher, dass nur das untergeordnete Zertifikat für den Export ausgewählt ist.

## <a name="other-certificates"></a>Andere Zertifikate

**Problem:** Das PFX-Paket enthält Zertifikate, bei denen es sich nicht um das untergeordnete Zertifikat handelt oder die nicht der Vertrauenskette angehören.  

**Lösung:** Exportieren Sie das Zertifikat wie unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) beschrieben erneut, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus. Stellen Sie sicher, dass nur das untergeordnete Zertifikat für den Export ausgewählt ist.

## <a name="fix-common-packaging-issues"></a>Beheben von häufigen Problemen beim Packen

Das Tool **AzsReadinessChecker** enthält das Hilfs-Cmdlet **Repair-AzsPfxCertificate**, mit dem eine PFX-Datei importiert und dann exportiert werden kann, um häufig auftretende Probleme beim Verpacken zu beheben, z. B.:

- Bei der **PFX-Verschlüsselung** handelt es sich nicht um TripleDES-SHA1.
- Beim **privaten Schlüssel** fehlt das Attribut des lokalen Computers.
- Die **Zertifikatkette** ist unvollständig oder falsch. Der lokale Computer muss die Vertrauenskette enthalten, wenn dies beim PFX-Paket nicht der Fall ist.
- **Andere Zertifikate**

**Repair-AzsPfxCertificate** hilft Ihnen nicht weiter, wenn Sie eine neue Zertifikatsignieranforderung generieren und ein Zertifikat neu ausstellen müssen.

### <a name="prerequisites"></a>Voraussetzungen

Auf dem Computer, auf dem das Tool ausgeführt wird, müssen folgende erforderliche Komponenten vorhanden sein:

- Windows 10 oder Windows Server 2016 mit Internetkonnektivität
- PowerShell 5.1 oder höher Um Ihre Version zu überprüfen, führen Sie das folgende PowerShell-Cmdlet aus, und überprüfen Sie dann die *Hauptversion** und die **Nebenversionen**:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Konfigurieren Sie [PowerShell für Azure Stack](azure-stack-powershell-install.md).
- Laden Sie die aktuelle Version des Tools [Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker) herunter.

### <a name="import-and-export-an-existing-pfx-file"></a>Importieren und Exportieren einer vorhandenen PFX-Datei

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Installieren von „Azure Stack Readiness Checker“ aus:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Führen Sie an der PowerShell-Eingabeaufforderung das folgende Cmdlet aus, um das PFX-Kennwort festzulegen. Ersetzen Sie `PFXpassword` durch das tatsächliche Kennwort:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um eine neue PFX-Datei zu exportieren:

   - Geben Sie für `-PfxPath` den Pfad zur PFX-Datei an, mit der Sie arbeiten. Im folgenden Beispiel lautet der Pfad `.\certificates\ssl.pfx`.
   - Geben Sie für `-ExportPFXPath` den Speicherort und Namen der zu exportierenden PFX-Datei an. Im folgenden Beispiel lautet der Pfad `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Überprüfen Sie, ob die erfolgreiche Durchführung in der Ausgabe angegeben wird, nachdem das Tool den Vorgang abgeschlossen hat:

   ```shell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich ausführlicher über die Sicherheit von Azure Stack.](azure-stack-rotate-secrets.md)
