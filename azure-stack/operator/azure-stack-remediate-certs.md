---
title: Beheben von Problemen mit Azure Stack-Zertifikaten | Microsoft-Dokumentation
description: Verwenden Sie Azure Stack Readiness Checker, um Probleme mit Zertifikaten zu überprüfen und zu beheben.
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
ms.openlocfilehash: 6e8adbc0d84c7816a081e751473764aab79cfcf2
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961913"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Beheben von häufigen Problemen mit Azure Stack-PKI-Zertifikaten

In diesem Artikel werden häufig auftretende Probleme mit Azure Stack-PKI-Zertifikaten beschrieben, und es wird erläutert, wie diese behoben werden können. Sie können Probleme ermitteln, indem Sie das Tool „Azure Stack Readiness Checker“ zur [Überprüfung von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md) verwenden. Das Tool führt Überprüfungen durch, um sicherzustellen, dass Zertifikate die PKI-Anforderungen einer Azure Stack-Bereitstellung und Azure Stack-Geheimnisrotation erfüllen, und protokolliert die Ergebnisse in einer Datei vom Typ [report.json](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>PFX-Verschlüsselung

**Fehler**: Bei der PFX-Verschlüsselung handelt es sich nicht um TripleDES-SHA1.

**Problembehandlung**: Exportieren Sie PFX-Dateien mit **TripleDES-SHA1**-Verschlüsselung. Dies ist die Standardeinstellung für alle Windows 10-Clients, wenn Sie den Export über das Zertifikat-Snap-In oder mithilfe von `Export-PFXCertificate` durchführen.

## <a name="read-pfx"></a>Lesen einer PFX-Datei

**Warnung**: Ein Kennwort schützt nur persönliche Informationen im Zertifikat.  

**Lösung**: Exportieren Sie die PFX-Dateien mit der optionalen Einstellung **Zertifikatdatenschutz aktivieren**.  

**Fehler**: Die PFX-Datei ist ungültig.  

**Lösung**: Exportieren Sie das Zertifikat erneut mithilfe der Schritte unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Signaturalgorithmus

**Fehler**: Der Signaturalgorithmus lautet SHA1.

**Lösung**: Führen Sie die Schritte unter „Generieren von Signieranforderungen für Azure Stack-Zertifikate“ aus, um die Zertifikatsignieranforderung (Certificate Signing Request, CSR) erneut mit dem Signaturalgorithmus SHA256 zu generieren. Senden Sie die CSR dann erneut an die Zertifizierungsstelle, um das Zertifikat neu auszustellen.

## <a name="private-key"></a>Privater Schlüssel

**Fehler**: Der private Schlüssel fehlt oder enthält nicht das Attribut des lokalen Computers.  

**Lösung**: Exportieren Sie über den Computer, mit dem die CSR generiert wurde, erneut das Zertifikat mithilfe der Schritte unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Diese Schritte umfassen das Exportieren aus dem Zertifikatspeicher des lokalen Computers.

## <a name="certificate-chain"></a>Zertifikatkette

**Fehler**: Die Zertifikatkette ist nicht vollständig.  

**Lösung**: Zertifikate sollten eine vollständige Zertifikatkette enthalten. Exportieren Sie das Zertifikat erneut wie unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) beschrieben, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus.

## <a name="dns-names"></a>DNS-Namen

**Fehler**: **DNSNameList** für das Zertifikat enthält nicht den Namen des Azure Stack-Dienstendpunkts oder ein gültiges Platzhalterzeichen. Platzhalterübereinstimmungen gelten nur für den Namespace der ganz links vom DNS-Namen steht. `*.region.domain.com` gilt beispielsweise nur für `portal.region.domain.com`, aber nicht für `*.table.region.domain.com`.

**Lösung**: Führen Sie die Schritte unter „Generieren von Signieranforderungen für Azure Stack-Zertifikate“ durch, um die CSR erneut mit den richtigen DNS-Namen zur Unterstützung von Azure Stack-Endpunkten zu generieren. Senden Sie die CSR erneut an eine Zertifizierungsstelle, und führen Sie dann die Schritte unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) durch, um das Zertifikat über den Computer, mit dem die CSR generiert wurde, zu exportieren.  

## <a name="key-usage"></a>Schlüsselverwendung

**Fehler**: Bei der Schlüsselverwendung fehlt die digitale Signatur oder Schlüsselverschlüsselung, oder bei der erweiterten Schlüsselverwendung fehlt die Server- oder Clientauthentifizierung.  

**Lösung**: Führen Sie die Schritte unter [Generieren von Signieranforderungen für Azure Stack-Zertifikate](azure-stack-get-pki-certs.md) durch, um die CSR erneut mit den richtigen Schlüsselverwendungsattributen zu generieren. Senden Sie die Zertifikatsignieranforderung erneut an die Zertifizierungsstelle, und vergewissern Sie sich, dass die Schlüsselverwendung in der Anforderung nicht durch eine Zertifikatvorlage überschrieben wird.

## <a name="key-size"></a>Schlüsselgröße

**Fehler**: Die Schlüsselgröße ist kleiner als 2048.

**Lösung**: Führen Sie die Schritte unter [Generieren von Signieranforderungen für Azure Stack-Zertifikate](azure-stack-get-pki-certs.md) durch, um die Zertifikatsignieranforderung erneut mit der richtigen Schlüssellänge (2048) zu generieren, und senden Sie die Zertifikatsignieranforderung erneut an die Zertifizierungsstelle.

## <a name="chain-order"></a>Kettenreihenfolge

**Fehler**: Die Reihenfolge der Zertifikatkette ist falsch.  

**Lösung**: Exportieren Sie das Zertifikat erneut wie unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) beschrieben, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus. Stellen Sie sicher, dass nur das untergeordnete Zertifikat für den Export ausgewählt ist.

## <a name="other-certificates"></a>Andere Zertifikate

**Fehler**: Das PFX-Paket enthält Zertifikate, bei denen es sich nicht um das untergeordnete Zertifikat oder einen Teil der Zertifikatkette handelt.  

**Lösung**: Exportieren Sie das Zertifikat erneut wie unter [Vorbereiten von Azure Stack-PKI-Zertifikaten für die Bereitstellung](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) beschrieben, und wählen Sie die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aus. Stellen Sie sicher, dass nur das untergeordnete Zertifikat für den Export ausgewählt ist.

## <a name="fix-common-packaging-issues"></a>Beheben von häufigen Problemen beim Packen

Das Tool **AzsReadinessChecker** enthält das Hilfs-Cmdlet **Repair-AzsPfxCertificate**, mit dem eine PFX-Datei importiert und dann exportiert werden kann, um häufig auftretende Probleme beim Verpacken zu beheben, z. B.:

- Bei der **PFX-Verschlüsselung** handelt es sich nicht um TripleDES-SHA1.
- Beim **privaten Schlüssel** fehlt das Attribut des lokalen Computers.
- Die **Zertifikatkette** ist unvollständig oder falsch. Der lokale Computer muss die Zertifikatkette enthalten, wenn dies beim PFX-Paket nicht der Fall ist.
- **Andere Zertifikate**

**Repair-AzsPfxCertificate** kann nicht als Hilfe dienen, wenn Sie eine neue Zertifikatsignieranforderung generieren und ein Zertifikat neu ausstellen müssen.

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
