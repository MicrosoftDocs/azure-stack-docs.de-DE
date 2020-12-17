---
title: Beheben allgemeiner Probleme mit PKI-Zertifikaten
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie mithilfe von Azure Stack Hub Readiness Checker allgemeine Probleme mit Azure Stack Hub-PKI-Zertifikaten beheben.
author: BryanLa
ms.topic: how-to
ms.date: 11/10/2020
ms.author: bryanla
ms.reviewer: unknown
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: d1d19d79a3a2242ada4e3f7972fa26f61ed600ce
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343205"
---
# <a name="fix-common-issues-with-azure-stack-hub-pki-certificates"></a>Beheben allgemeiner Probleme mit Azure Stack Hub-PKI-Zertifikaten

In diesem Artikel werden allgemeine Probleme mit Azure Stack Hub-PKI-Zertifikaten sowie entsprechende Problemlösungen beschrieben. Sie können Probleme ermitteln, indem Sie das Tool „Azure Stack Hub Readiness Checker“ zur [Überprüfung von Azure Stack Hub-PKI-Zertifikaten](azure-stack-validate-pki-certs.md) verwenden. Das Tool überprüft, ob die Zertifikate die PKI-Anforderungen einer Azure Stack Hub-Bereitstellung und einer Azure Stack Hub-Geheimnisrotation erfüllen, und protokolliert die Ergebnisse in einer Datei vom Typ [report.json](azure-stack-validation-report.md).  

## <a name="http-crl---warning"></a>HTTP-Zertifikatsperrliste: Warnung

**Problem**: Zertifikat enthält keine HTTP-Zertifikatsperrliste in der CDP-Erweiterung.

**Korrektur**: Dies ist kein Blockierproblem. Azure Stack erfordert eine HTTP-Zertifikatsperrliste für die Sperrungsüberprüfung gemäß [Zertifikatanforderungen für Azure Stack Hub-PKI (Public Key-Infrastruktur)](https://aka.ms/azspki).  Eine HTTP-Zertifikatsperrliste wurde im Zertifikat nicht erkannt.  Um sicherzustellen, dass die Zertifikatsperrungsüberprüfung funktioniert, sollte die Zertifizierungsstelle ein Zertifikat mit einer HTTP-Zertifikatsperrliste in der CDP-Erweiterung ausstellen.

## <a name="http-crl---fail"></a>HTTP-Zertifikatsperrliste: Fehler

**Problem**: Keine Verbindung mit der HTTP-Zertifikatsperrliste in der CDP-Erweiterung möglich.

**Korrektur**: Dies ist ein Blockierproblem. Azure Stack erfordert eine Verbindung mit einer HTTP-Zertifikatsperrliste für die Sperrungsüberprüfung gemäß [Veröffentlichen von Azure Stack Hub-Ports und -URLs](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound).

## <a name="pfx-encryption"></a>PFX-Verschlüsselung

**Problem:** Bei der PFX-Verschlüsselung handelt es sich nicht um TripleDES-SHA1.

**Lösung:** Exportieren Sie PFX-Dateien mit der Verschlüsselung **TripleDES-SHA1**. Dies ist die Standardverschlüsselung für alle Windows 10-Clients, wenn Sie den Export über das Zertifikat-Snap-In oder mithilfe von `Export-PFXCertificate` durchführen.

## <a name="read-pfx"></a>Lesen einer PFX-Datei

**Warnung**: Ein Kennwort schützt nur persönliche Informationen im Zertifikat.  

**Lösung:** Exportieren Sie PFX-Dateien mit der optionalen Einstellung **Zertifikatdatenschutz aktivieren**.  

**Problem:** Die PFX-Datei ist ungültig.  

**Lösung:** Exportieren Sie das Zertifikat mithilfe der Schritte unter [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) erneut.

## <a name="signature-algorithm"></a>Signaturalgorithmus

**Problem:** Der Signaturalgorithmus ist SHA1.

**Lösung:** Führen Sie die Schritte unter „Generieren von Zertifikatsignieranforderungen für Azure Stack Hub“ aus, um die Zertifikatsignieranforderung (Certificate Signing Request, CSR) mit dem Signaturalgorithmus SHA256 zu generieren. Senden Sie die CSR dann erneut an die Zertifizierungsstelle, um das Zertifikat neu auszustellen.

## <a name="private-key"></a>Privater Schlüssel

**Problem:** Der private Schlüssel fehlt oder enthält nicht das Attribut des lokalen Computers.  

**Lösung:** Exportieren Sie über den Computer, mit dem die CSR generiert wurde, das Zertifikat mithilfe der Schritte unter [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) erneut. Diese Schritte umfassen das Exportieren aus dem Zertifikatspeicher des lokalen Computers.

## <a name="certificate-chain"></a>Zertifikatkette

**Problem:** Die Vertrauenskette ist nicht vollständig.  

**Lösung:** Zertifikate müssen eine vollständige Vertrauenskette enthalten. Exportieren Sie das Zertifikat wie unter [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) beschrieben erneut, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus.

## <a name="dns-names"></a>DNS-Namen

**Problem:** **DNSNameList** für das Zertifikat enthält nicht den Namen des Azure Stack Hub-Dienstendpunkts oder einen gültigen Platzhalter. Platzhalterübereinstimmungen gelten nur für den Namespace der ganz links vom DNS-Namen steht. `*.region.domain.com` gilt beispielsweise nur für `portal.region.domain.com`, aber nicht für `*.table.region.domain.com`.

**Lösung:** Führen Sie die Schritte unter „Generieren von Zertifikatsignieranforderungen für Azure Stack Hub“ aus, um die CSR zur Unterstützung von Azure Stack Hub-Endpunkten mit den richtigen DNS-Namen erneut zu generieren. Übermitteln Sie die CSR erneut an eine Zertifizierungsstelle. Führen Sie dann die Schritte unter [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) aus, um das Zertifikat über den Computer, mit dem die CSR generiert wurde, zu exportieren.  

## <a name="key-usage"></a>Schlüsselverwendung

**Problem:** Bei der Schlüsselverwendung fehlt die digitale Signatur oder Schlüsselverschlüsselung. Oder: Bei der erweiterten Schlüsselverwendung fehlt die Server- oder Clientauthentifizierung.  

**Lösung:** Führen Sie die Schritte unter [Generieren von Zertifikatsignieranforderungen für Azure Stack Hub](azure-stack-get-pki-certs.md) aus, um die CSR mit den richtigen Schlüsselverwendungsattributen erneut zu generieren. Übermitteln Sie die Zertifikatsignieranforderung erneut an die Zertifizierungsstelle, und vergewissern Sie sich, dass die Schlüsselverwendung in der Anforderung nicht durch eine Zertifikatvorlage überschrieben wird.

## <a name="key-size"></a>Schlüsselgröße

**Problem:** Die Schlüsselgröße ist kleiner als 2.048.

**Lösung**: Führen Sie die Schritte unter [Generieren von Zertifikatsignieranforderungen für Azure Stack Hub](azure-stack-get-pki-certs.md) aus, um die Zertifikatsignieranforderung mit der richtigen Schlüssellänge (2048) erneut zu generieren, und übermitteln Sie die Zertifikatsignieranforderung anschließend erneut an die Zertifizierungsstelle.

## <a name="chain-order"></a>Kettenreihenfolge

**Problem:** Die Reihenfolge der Vertrauenskette ist falsch.  

**Lösung:** Exportieren Sie das Zertifikat wie unter [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) beschrieben erneut, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus. Stellen Sie sicher, dass nur das untergeordnete Zertifikat für den Export ausgewählt ist.

## <a name="other-certificates"></a>Andere Zertifikate

**Problem:** Das PFX-Paket enthält Zertifikate, bei denen es sich nicht um das untergeordnete Zertifikat handelt oder die nicht der Vertrauenskette angehören.  

**Lösung:** Exportieren Sie das Zertifikat wie unter [Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) beschrieben erneut, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus. Stellen Sie sicher, dass nur das untergeordnete Zertifikat für den Export ausgewählt ist.

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
- PowerShell 5.1 oder höher Um Ihre Version zu überprüfen, führen Sie das folgende PowerShell-Cmdlet aus, und überprüfen Sie dann die **Hauptversion** und die **Nebenversionen**:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Konfigurieren Sie [PowerShell für Azure Stack Hub](powershell-install-az-module.md).
- Laden Sie die aktuelle Version des Tools [Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker) herunter.

### <a name="import-and-export-an-existing-pfx-file"></a>Importieren und Exportieren einer vorhandenen PFX-Datei

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Installieren von „Azure Stack Hub Readiness Checker“ aus:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. Führen Sie an der PowerShell-Eingabeaufforderung das folgende Cmdlet aus, um das PFX-Kennwort festzulegen. Geben Sie bei Aufforderung das Kennwort ein:

   ```powershell
   $password = Read-Host -Prompt "Enter password" -AsSecureString
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
   Starting Azure Stack Hub Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur Sicherheit von Azure Stack Hub](azure-stack-rotate-secrets.md)
