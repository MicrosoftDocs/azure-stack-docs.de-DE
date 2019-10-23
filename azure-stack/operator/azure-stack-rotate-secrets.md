---
title: Rotieren von Geheimnissen in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Geheimnisse in Azure Stack rotieren.
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
ms.date: 10/15/2019
ms.reviewer: ppacent
ms.author: mabrigg
ms.lastreviewed: 09/30/2019
monikerRange: '>=azs-1803'
ms.openlocfilehash: f32a25997e4336a24dfb9b673202882cff1845e9
ms.sourcegitcommit: 70147e858956443bc66b3541ec278c102bb45c07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72381457"
---
# <a name="rotate-secrets-in-azure-stack"></a>Rotieren von Geheimnissen in Azure Stack

*Diese Anweisungen gelten nur für integrierte Azure Stack-Systeme ab Version 1803. Versuchen Sie nicht, die Geheimnisrotation mit älteren Azure Stack-Versionen (vor 1802) zu verwenden.*

Geheimnisse tragen zur Gewährleistung einer sicheren Kommunikation zwischen Azure Stack-Infrastrukturressourcen und -diensten bei.

## <a name="overview-to-rotate-secrets"></a>Übersicht über das Rotieren von Geheimnissen

1. Bereiten Sie die Zertifikate vor, die für die Geheimnisrotation verwendet werden.
2. Sehen Sie sich die [Azure Stack-PKI-Zertifikatanforderungen](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs) an.
3. [Verwenden Sie den privilegierten Endpunkt](azure-stack-privileged-endpoint.md), und führen Sie **Test-azurestack** aus, um zu überprüfen, ob alles in Ordnung ist.  
4. Informieren Sie sich ausführlicher über das [Vorbereiten der Geheimnisrotation](#pre-steps-for-secret-rotation).
5. [Überprüfen Sie die Azure Stack-PKI-Zertifikate.](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs) Stellen Sie sicher, dass das Kennwort keine Sonderzeichen wie `*` oder `)` enthält.
6. Vergewissern Sie sich, dass als PFX-Verschlüsselung **TripleDES-SHA1** verwendet wird. Tritt ein Problem auf, lesen Sie die Informationen unter [Behebung von häufigen Problemen mit Azure Stack-PKI-Zertifikaten](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption).
7. Bereiten Sie die Ordnerstruktur vor.  Ein Beispiel finden Sie im Abschnitt [Rotieren externer Geheimnisse](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets).
8. [Starten Sie die Geheimnisrotation.](#use-powershell-to-rotate-secrets)

## <a name="rotate-secrets"></a>Rotieren von Geheimnissen

Azure Stack verwendet verschiedene Geheimnisse, um eine sichere Kommunikation zwischen Azure Stack-Infrastrukturressourcen und -diensten zu gewährleisten.

- **Interne Geheimnisse**

    Alle Zertifikate, Kennwörter, sicheren Zeichenfolgen und Schlüssel, die von der Azure Stack-Infrastruktur ohne Beteiligung des Azure Stack-Betreibers verwendet werden.

- **Externe Geheimnisse**

    Infrastrukturdienstzertifikate für extern ausgerichtete Dienste, die vom Azure Stack-Betreiber bereitgestellt werden. Zu externen Geheimnissen zählen Zertifikate für folgende Dienste:

    - Administratorportal
    - Öffentliches Portal
    - Azure Resource Manager (Administrator)
    - Azure Resource Manager (global)
    - Key Vault (Administrator)
    - KeyVault
    - Administratorerweiterungshost
    - ACS (einschließlich Blob, Table und Queue Storage)
    - ADFS *
    - Graph *
    
    \* Nur relevant, wenn Active Directory-Verbunddienste (AD FS) als Identitätsanbieter der Umgebung verwendet wird.

> [!Note]
> Alle anderen sicheren Schlüssel und Zeichenfolgen (einschließlich BMC- und Wechselkennwörter sowie Kennwörter für Benutzer- und Administratorkonten) werden weiterhin manuell vom Administrator aktualisiert.

> [!Important]
> Seit Release 1811 von Azure Stack wurde die Geheimnisrotation für interne und externe Zertifikate getrennt.

Um die Integrität der Azure Stack-Infrastruktur zu erhalten, müssen Operatoren die Geheimnisse ihrer Infrastruktur regelmäßig und mit einer Häufigkeit rotieren können, die mit den Sicherheitsanforderungen ihrer Organisation in Einklang steht.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Rotieren von Geheimnissen mit externen Zertifikaten von einer neuen Zertifizierungsstelle

Azure Stack unterstützt Geheimnisrotation mit externen Zertifikaten von einer neuen Zertifizierungsstelle (ZS) in den folgenden Kontexten:

|Installierte Zertifizierungsstelle|ZS, zu der rotiert werden soll|Unterstützt|Unterstützte Versionen von Azure Stack|
|-----|-----|-----|-----|
|Von „Selbstsigniert“|Zu „Enterprise“|Unterstützt|1903 und höher|
|Von „Selbstsigniert“|Zu „Selbstsigniert“|Nicht unterstützt||
|Von „Selbstsigniert“|Zu „Öffentlich“<sup>*</sup>|Unterstützt|1803 und höher|
|Von „Enterprise“|Zu „Enterprise“|Unterstützt. Von 1803 – 1903: Wird unterstützt, solange Kunden die GLEICHE Unternehmens-ZS wie bei der Bereitstellung verwenden.|1803 und höher|
|Von „Enterprise“|Zu „Selbstsigniert“|Nicht unterstützt||
|Von „Enterprise“|Zu „Öffentlich“<sup>*</sup>|Unterstützt|1803 und höher|
|Von „Öffentlich“<sup>*</sup>|Zu „Enterprise“|Unterstützt|1903 und höher|
|Von „Öffentlich“<sup>*</sup>|Zu „Selbstsigniert“|Nicht unterstützt||
|Von „Öffentlich“<sup>*</sup>|Zu „Öffentlich“<sup>*</sup>|Unterstützt|1803 und höher|

<sup>*</sup>Gibt an, dass die Öffentlichen Zertifizierungsstellen diejenigen sind, die Teil des „Vertrauenswürdiger Stamm“-Programms von Windows sind. Sie finden die vollständige Liste im Artikel [Microsoft Trusted Root Certificate Program: Participants (Stand: 27. Juni 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Behandeln von Warnungen

Für Geheimnisse, die in 30 Tagen ablaufen, werden im Administratorportal folgende Warnungen generiert:

- Pending service account password expiration (Bevorstehender Ablauf eines Dienstkontokennworts)
- Pending internal certificate expiration (Bevorstehender Ablauf eines internen Zertifikats)
- Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats)

Diese Warnungen können durch Ausführen der Geheimnisrotation behandelt werden. Führen Sie hierzu die im Anschluss beschriebenen Schritte aus.

> [!Note]
> In Azure Stack-Umgebungen von Versionen vor 1811 treten möglicherweise Warnungen für ausstehende interne Zertifikate oder Geheimnisablaufzeiten auf.
> Diese Warnungen sind ungenau und sollten ohne interne Geheimnisrotation ignoriert werden.
> Ungenaue interne Warnungen zum Geheimnisablauf sind ein bekanntes Problem, das in 1811 gelöst wurde – interne Geheimnisse laufen nicht ab, es sei denn, die Umgebung war für zwei Jahre aktiv.

## <a name="pre-steps-for-secret-rotation"></a>Vorbereiten der Geheimnisrotation

   > [!IMPORTANT]
   > Wenn die Geheimnisrotation bereits in Ihrer Azure Stack-Umgebung durchgeführt wurde, aktualisieren Sie das System auf Version 1811 oder höher, bevor Sie die Geheimnisrotation erneut ausführen.
   > Die Geheimnisrotation muss über den [Privilegierten Endpunkt](azure-stack-privileged-endpoint.md) ausgeführt werden und erfordert Anmeldeinformationen für Azure Stack-Operatoren.
   > Wenn die Azure Stack-Operatoren Ihrer Umgebung nicht wissen, ob die Geheimnisrotation in Ihrer Umgebung ausgeführt wurde, aktualisieren Sie vor dem Ausführen der Geheimnisrotation erneut auf 1811.

1. Sie sollten Ihre Azure Stack-Instanz unbedingt auf Version 1811 aktualisieren.

    > [!Note] 
    > Für Versionen vor 1811 müssen Sie zum Hinzufügen von Erweiterungshostzertifikaten keine Geheimnisse rotieren. Befolgen Sie die Anweisungen im Artikel [Vorbereiten auf den Erweiterungshost für Azure Stack](azure-stack-extension-host-prepare.md), um Erweiterungshostzertifikate hinzuzufügen.

2. Operatoren wird auffallen, dass während der Rotation der Azure Stack-Geheimnisse Warnungen geöffnet und automatisch wieder geschlossen werden.  Dieses Verhalten ist zu erwarten, und die Warnungen können ignoriert werden.  Operatoren können die Gültigkeit dieser Warnungen durch Ausführen von **Test-AzureStack** überprüfen.  Wenn Operatoren Azure Stack-Systeme mithilfe von System Center Operations Manager (SCOM) überwachen, wird durch Versetzen eines Systems in den Wartungsmodus verhindert, dass diese Warnungen ihre ITSM-Systeme erreichen, es werden jedoch weiterhin Warnungen ausgegeben, wenn das Azure Stack-System nicht mehr erreichbar ist.

3. Informieren Sie Ihre Benutzer über sämtliche Wartungsmaßnahmen. Planen Sie normale Wartungszeitfenster möglichst außerhalb der Geschäftszeiten. Wartungsvorgänge können sowohl Benutzerworkloads als auch Portalvorgänge beeinträchtigen.

    > [!Note]
    > Die nächsten Schritte gelten nur für die Rotation externer Azure Stack-Geheimnisse.

4. Führen Sie **[Test-AzureStack](azure-stack-diagnostic-test.md)** aus, und bestätigen Sie, dass alle Testausgaben fehlerfrei sind, bevor Sie Geheimnisse rotieren.
5. Bereiten Sie eine neue Gruppe externer Ersatzzertifikate vor. Die neue Gruppe entspricht den Zertifikatspezifikationen aus den [Azure Stack-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md). Sie können eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) für den Kauf bzw. das Erstellen neuer Zertifikate mit den unter [Generieren von Signieranforderungen für Azure Stack-Zertifikate](azure-stack-get-pki-certs.md) beschriebenen Schritten generieren und sie mit den in [ Vorbereiten von Azure Stack-PKI-Zertifikaten für die Verwendung bei der Bereitstellung oder Rotation](azure-stack-prepare-pki-certs.md) für die Verwendung in Ihrer Azure Stack-Umgebung beschriebenen Schritten vorbereiten. Achten Sie darauf, dass Sie die Zertifikate mit den in [Überprüfen von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md) beschriebenen Schritten überprüfen.
6. Speichern Sie eine Sicherung der für die Rotation verwendeten Zertifikate an einem sicheren Sicherungsspeicherort. Sollte die Rotation nicht erfolgreich sein, ersetzen Sie die Zertifikate in der Dateifreigabe durch die Sicherungskopien, und wiederholen Sie dann die Rotation. Hinweis: Bewahren Sie Sicherungskopien am sicheren Sicherungsspeicherort auf.
7. Erstellen Sie eine Dateifreigabe, auf die Sie über die virtuellen ERCS-Computer zugreifen können. Die Identität **CloudAdmin** muss über Lese- und Schreibzugriff für die Dateifreigabe verfügen.
8. Öffnen Sie eine PowerShell ISE-Konsole auf einem Computer, auf dem Sie Zugriff auf die Dateifreigabe haben. Navigieren Sie zu Ihrer Dateifreigabe.
9. Führen Sie **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** aus, um die erforderlichen Verzeichnisse für Ihre externen Zertifikate zu erstellen.

> [!IMPORTANT]
> Mit dem Skript „CertDirectoryMaker“ wird eine Ordnerstruktur erstellt, die folgendem Muster entspricht:
>
> **.\Certificates\AAD** oder ***.\Certificates\ADFS*** (je nach Identitätsanbieter für Azure Stack)
>
> Es ist von größter Bedeutung, dass die Ordnerstruktur mit **AAD** oder **ADFS** endet und sich alle Unterverzeichnisse innerhalb dieser Struktur befinden, da andernfalls **Start-SecretRotation** zu folgender Ausgabe führt:
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Wie Sie sehen, gibt die Fehlermeldung an, dass ein Problem beim Zugriff auf die Dateifreigabe auftritt, doch ist es in Wirklichkeit die Ordnerstruktur, die hier erzwungen wird.
> Weitere Informationen finden Sie im Microsoft AzureStack Readiness Checker im [PublicCertHelper-Modul](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1).
>
> Es ist auch wichtig, dass die Ordnerstruktur der Dateifreigabe mit **Certificates** beginnt, da sie sonst ebenfalls bei der Überprüfung zu einem Fehler führt.
> Die Einbindung der Dateifreigabe sollte die Form **\\\\\<IPAddress>\\\<ShareName>\\** aufweisen und den Ordner **Certificates\AAD** oder **Certificates\ADFS** enthalten.
>
> Beispiel:
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Rotieren externer Geheimnisse

So rotieren Sie externe Geheimnisse:

1. Platzieren Sie die neue Gruppe externer Ersatzzertifikate in der Verzeichnisstruktur des Verzeichnisses **\Certificates\\\<IdentityProvider>** , das Sie im Rahmen der Vorbereitungsschritte erstellt haben. Berücksichtigen Sie dabei das Format, wie unter [Azure Stack-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md#mandatory-certificates) im Abschnitt „Erforderliche Zertifikate“ erläutert.

    Beispiel der Ordnerstruktur für den Azure AD-Identitätsanbieter:
    ```powershell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. Erstellen Sie unter Verwendung des Kontos **CloudAdmin** eine PowerShell-Sitzung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md), und speichern Sie die Sitzung als Variable. Diese Variable wird im nächsten Schritt als Parameter verwendet.

    > [!IMPORTANT]  
    > Geben Sie die Sitzung nicht ein; speichern Sie sie als Variable.

3. Führen Sie **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** aus. Übergeben Sie die PowerShell-Sitzungsvariable für den privilegierten Endpunkt als Parameter **Session**.

4. Führen Sie **Start-SecretRotation** mit den folgenden Parametern aus:
    - **PfxFilesPath**  
    Geben Sie den Netzwerkpfad des zuvor erstellten Verzeichnisses „Certificates“ an.  
    - **PathAccessCredential**  
    Ein PSCredential-Objekt mit Anmeldeinformationen für die Freigabe.
    - **CertificatePassword**  
    Eine sichere Zeichenfolge des Kennworts, das für alle erstellten PFX-Zertifikatdateien verwendet wird.

5. Warten Sie, bis Ihre Geheimnisse rotiert wurden. Die Rotation externer Geheimnisse dauert in der Regel ungefähr eine Stunde.

    Wenn die Geheimnisrotation erfolgreich abgeschlossen wurde, wird in der Konsole **Overall action status: Success** (Allgemeiner Aktionsstatus: Erfolgreich) angezeigt.

    > [!Note]
    > Wenn bei der Geheimnisrotation ein Fehler auftritt, befolgen Sie die Anweisungen in der Fehlermeldung, und führen Sie **Start-SecretRotation** mit dem Parameter **-ReRun** erneut aus.

    ```powershell
    Start-SecretRotation -ReRun
    ```
    Wenden Sie sich an den Support, wenn wiederholt Fehler bei der Geheimnisrotation auftreten.

6. Entfernen Sie nach erfolgreichem Abschluss der Geheimnisrotation Ihre Zertifikate aus der Freigabe, die Sie im Rahmen der Vorbereitung erstellt haben, und speichern Sie sie an ihrem sicheren Sicherungsspeicherort.

## <a name="use-powershell-to-rotate-secrets"></a>Verwenden von PowerShell zum Rotieren von Geheimnissen

Das folgende PowerShell-Beispiel veranschaulicht die zu verwendenden Cmdlets und Parameter für die Geheimnisrotation.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Nur Rotieren interner Geheimnisse

> [!Note]
> Das Rotieren interner Geheimnisse sollte nur vorgenommen werden, wenn Sie vermuten, dass ein internes Geheimnisse durch eine schädliche Entität kompromittiert wurde, oder Sie eine Warnung erhalten haben (in Build 1811 oder höher), die angibt, dass die internen Zertifikate demnächst ablaufen.
> In Azure Stack-Umgebungen von Versionen vor 1811 treten möglicherweise Warnungen für ausstehende interne Zertifikate oder Geheimnisablaufzeiten auf.
> Diese Warnungen sind ungenau und sollten ohne interne Geheimnisrotation ignoriert werden.
> Ungenaue interne Warnungen zum Geheimnisablauf sind ein bekanntes Problem, das in 1811 gelöst wurde – interne Geheimnisse laufen nicht ab, es sei denn, die Umgebung war für zwei Jahre aktiv.

1. Erstellen Sie eine PowerShell-Sitzung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md).
2. Führen Sie in der Sitzung des privilegierten Endpunkts **Start-SecretRotation-Internal** aus.

    > [!Note]
    > Azure Stack-Umgebungen mit Versionen vor 1811 benötigen das **-Internal**-Flag nicht. **Start-SecretRotation** wird nur für interne Geheimnisse gedreht.

3. Warten Sie, bis Ihre Geheimnisse rotiert wurden.

   Wenn die Geheimnisrotation erfolgreich abgeschlossen wurde, wird in der Konsole **Overall action status: Success** (Allgemeiner Aktionsstatus: Erfolgreich) angezeigt.
    > [!Note]
    > Wenn bei der Geheimnisrotation ein Fehler auftritt, befolgen Sie die Anweisungen in der Fehlermeldung, und führen Sie **Start-SecretRotation** mit den Parametern **–Internal** und **-ReRun** erneut aus.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Wenden Sie sich an den Support, wenn wiederholt Fehler bei der Geheimnisrotation auftreten.

## <a name="start-secretrotation-reference"></a>Referenz für „Start-SecretRotation“

Rotiert die Geheimnisse eines Azure Stack-Systems. Wird nur für den privilegierten Azure Stack-Endpunkt ausgeführt.

### <a name="syntax"></a>Syntax

#### <a name="for-external-secret-rotation"></a>Für Rotation externer Geheimnisse

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Für Rotation interner Geheimnisse

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Für erneute Rotation externer Geheimnisse

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Für erneute Rotation interner Geheimnisse

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>BESCHREIBUNG

Das Cmdlet **Start-SecretRotation** rotiert die Infrastrukturgeheimnisse eines Azure Stack-Systems. Standardmäßig dreht es nur die Zertifikate aller externen Netzwerkinfrastruktur-Endpunkte. Bei Verwendung mit dem -Internal-Flag werden die internen Infrastrukturgeheimnisse gedreht. Beim Rotieren externer Netzwerkinfrastruktur-Endpunkte muss **Start-SecretRotation** über einen **Invoke-Command**-Skriptblock ausgeführt werden, und die Sitzung des privilegierten Endpunkts der Azure Stack-Umgebung muss als **Session**-Parameter übergeben werden.

### <a name="parameters"></a>Parameter

| Parameter | type | Erforderlich | Position | Standard | BESCHREIBUNG |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | Zeichenfolge  | False  | benannt  | Keine  | Der Dateifreigabepfad des Verzeichnisses **\Certificates** mit allen externen Netzwerkendpunkt-Zertifikaten. Nur beim Rotieren externer Geheimnisse erforderlich. Das Endverzeichnis muss **\Certificates** sein. |
| `CertificatePassword` | SecureString | False  | benannt  | Keine  | Das Kennwort für alle Zertifikate in „-PfXFilesPath“. Erforderlich, wenn „PfxFilesPath“ beim Rotieren externer Geheimnisse angegeben wird. |
| `Internal` | Zeichenfolge | False | benannt | Keine | Das Internal-Flag muss immer dann verwendet werden, wenn ein Azure Stack-Operator die internen Infrastrukturgeheimnisse rotieren möchte. |
| `PathAccessCredential` | PSCredential | False  | benannt  | Keine  | Die PowerShell-Anmeldeinformationen für die Dateifreigabe des Verzeichnisses **\Certificates** mit allen externen Netzwerkendpunkt-Zertifikaten. Nur beim Rotieren externer Geheimnisse erforderlich.  |
| `ReRun` | SwitchParameter | False  | benannt  | Keine  | Die erneute Ausführung muss immer dann erfolgen, wenn die Geheimnisrotation nach einem fehlgeschlagenen Versuch erneut versucht wird. |

### <a name="examples"></a>Beispiele

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Nur Rotieren interner Infrastrukturgeheimnisse

Dies muss über den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) Ihrer Azure Stack-Umgebung ausgeführt werden.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Dieser Befehl rotiert alle Infrastrukturgeheimnisse, die für das interne Azure Stack-Netzwerk verfügbar gemacht wurden.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Nur Rotieren externer Infrastrukturgeheimnisse  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Dieser Befehl rotiert die TLS-Zertifikate, die für die externen Netzwerkinfrastruktur-Endpunkte von Azure Stack verwendet werden.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Rotieren interner und externer Infrastrukturgeheimnisse (nur Versionen **vor 1811**)

> [!IMPORTANT]
> Dieser Befehl gilt nur für Azure Stack-Versionen **vor 1811**, da die Rotation für interne und externe Zertifikate aufgeteilt wurde.
>
> **Bei Versionen *ab 1811* können interne und externe Zertifikate nicht mehr zusammen rotiert werden!**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Dieser Befehl rotiert alle Infrastrukturgeheimnisse, die für das interne Azure Stack-Netzwerk verfügbar gemacht wurden, sowie die TLS-Zertifikate, die für die externen Netzwerkinfrastruktur-Endpunkte von Azure Stack verwendet werden. „Start-SecretRotation“ rotiert alle von Stack generierten Geheimnisse, und da bereitgestellte Zertifikate vorhanden sind, werden auch externe Endpunktzertifikate rotiert.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Aktualisieren des Baseboard Management Controller (BMC, Baseboard-Verwaltungscontroller)

Der Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) überwacht den physischen Zustand Ihrer Server. Anweisungen zur Aktualisierung des Benutzerkontonamens und Kennworts des BMC erhalten Sie vom OEM-Hardwareanbieter (Original Equipment Manufacturer, Originalgerätehersteller). 

>[!NOTE]
> Der OEM stellt unter Umständen zusätzliche Verwaltungsanwendungen bereit. Die Aktualisierung des Benutzernamens oder Kennworts für andere Verwaltungsanwendungen wirkt sich nicht auf den BMC-Benutzernamen oder das BMC-Kennwort aus.   

1. Aktualisieren Sie den BMC auf den physischen Azure Stack-Servern gemäß den Anweisungen des OEM. Benutzername und Kennwort für jeden BMC in Ihrer Umgebung müssen identisch sein. BMC-Benutzernamen dürfen nicht länger als 16 Zeichen sein.
2. Öffnen Sie in Azure Stack einen privilegierten Endpunkt. Anweisungen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).
3. Nachdem Sie über Ihre PowerShell-Eingabeaufforderung zu **[IP-Adresse oder ERCS-VM-Name]: PS>** oder zu **[azs-ercs01]: PS>** gewechselt sind, führen Sie (abhängig von der Umgebung) `Set-BmcCredential` durch Ausführen von `Invoke-Command` aus. Übergeben Sie die Sitzungsvariable des privilegierten Endpunkts als Parameter. Beispiel:

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    Sie können auch die statische PowerShell-Version mit den Kennwörtern als Codezeilen verwenden:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Nächste Schritte

[Informieren Sie sich ausführlicher über die Sicherheit von Azure Stack.](azure-stack-security-foundations.md)
