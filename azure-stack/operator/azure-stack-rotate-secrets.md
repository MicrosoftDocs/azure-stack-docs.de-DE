---
title: Rotieren von Geheimnissen
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie Ihre Geheimnisse in Azure Stack Hub rotieren.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 04/03/2020
ms.reviewer: ppacent
ms.author: inhenkel
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: d66f4c6a83dbac71b407990f65922354ee353dc3
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488109"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Rotieren von Geheimnissen in Azure Stack Hub

*Diese Anweisungen gelten nur für integrierte Azure Stack Hub-Systeme ab Version 1803. Versuchen Sie nicht, die Geheimnisrotation mit älteren Azure Stack Hub-Versionen vor 1802 zu verwenden.*

Geheimnisse tragen zur Gewährleistung einer sicheren Kommunikation zwischen Azure Stack Hub-Infrastrukturressourcen und -diensten bei.

## <a name="rotate-secrets-overview"></a>Übersicht über die Geheimnisrotation

1. Bereiten Sie die Zertifikate vor, die für die Geheimnisrotation verwendet werden.
2. Sehen Sie sich die [Azure Stack Hub-PKI-Zertifikatanforderungen](./azure-stack-pki-certs.md) an.
3. [Verwenden Sie den privilegierten Endpunkt](azure-stack-privileged-endpoint.md), und führen Sie **Test-azurestack** aus, um zu überprüfen, ob alles in Ordnung ist.  
4. Machen Sie sich mit der [Vorbereitung der Geheimnisrotation](#pre-steps-for-secret-rotation) vertraut.
5. [Überprüfen Sie Azure Stack Hub-PKI-Zertifikate](./azure-stack-validate-pki-certs.md). Vergewissern Sie sich, dass das Kennwort keine Sonderzeichen wie `*` oder `)` enthält.
6. Vergewissern Sie sich, dass als PFX-Verschlüsselung **TripleDES-SHA1** verwendet wird. Lesen Sie bei Bedarf die Informationen unter [Beheben allgemeiner Probleme mit Azure Stack Hub-PKI-Zertifikaten](./azure-stack-remediate-certs.md#pfx-encryption).
7. Bereiten Sie die Ordnerstruktur vor.  Ein Beispiel finden Sie im Abschnitt [Rotieren externer Geheimnisse](#rotating-external-secrets).
8. [Starten Sie die Geheimnisrotation.](#use-powershell-to-rotate-secrets)

## <a name="rotate-secrets"></a>Rotieren von Geheimnissen

Azure Stack Hub verwendet verschiedene Geheimnisse, um eine sichere Kommunikation zwischen den Infrastrukturressourcen und -diensten von Azure Stack Hub zu gewährleisten.

- **Interne Geheimnisse**

    Alle Zertifikate, Kennwörter, sicheren Zeichenfolgen und Schlüssel, die von der Azure Stack Hub-Infrastruktur ohne Eingriff durch den Azure Stack Hub-Operator verwendet werden.

- **Externe Geheimnisse**

    Infrastrukturdienstzertifikate für extern ausgerichtete Dienste, die vom Azure Stack Hub-Operator bereitgestellt werden. Zu externen Geheimnissen zählen Zertifikate für folgende Dienste:

    - Administratorportal
    - Öffentliches Portal
    - Azure Resource Manager (Administrator)
    - Azure Resource Manager (global)
    - Key Vault-Administrator
    - Key Vault
    - Administratorerweiterungshost
    - ACS (einschließlich Blob, Table und Queue Storage)
    - AD FS*
    - Graph*
    
    \* Nur relevant, wenn Active Directory-Verbunddienste (AD FS) als Identitätsanbieter der Umgebung verwendet wird.

> [!Note]
> Alle anderen sicheren Schlüssel und Zeichenfolgen (einschließlich BMC- und Wechselkennwörter sowie Kennwörter für Benutzer- und Administratorkonten) werden weiterhin manuell vom Administrator aktualisiert.

> [!Important]
> Mit diesen Prozeduren werden die Zertifikate, Geheimnisse und Anmeldeinformationen für den Ressourcenanbieter für Azure App Service in Azure Stack Hub nicht rotiert.  Führen Sie zum Rotieren dieser Elemente die Schritte im Artikel [Rotieren von Geheimnissen und Zertifikaten in App Service in Azure Stack Hub](app-service-rotate-certificates.md) aus.

> [!Important]
> Seit Release 1811 von Azure Stack Hub ist die Geheimnisrotation für interne und externe Zertifikate getrennt.

Um die Integrität der Azure Stack Hub-Infrastruktur zu erhalten, müssen Operatoren die Geheimnisse ihrer Infrastruktur regelmäßig und mit einer Häufigkeit rotieren können, die mit den Sicherheitsanforderungen ihrer Organisation in Einklang steht.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Rotieren von Geheimnissen mit externen Zertifikaten von einer neuen Zertifizierungsstelle

Azure Stack Hub unterstützt die Geheimnisrotation mit externen Zertifikaten von einer neuen Zertifizierungsstelle (ZS) in den folgenden Kontexten:

|Installierte Zertifizierungsstelle|ZS, zu der rotiert werden soll|Unterstützt|Unterstützte Azure Stack Hub-Versionen|
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

## <a name="fixing-alerts"></a>Behandeln von Warnungen

Für Geheimnisse, die in 30 Tagen ablaufen, werden im Administratorportal folgende Warnungen generiert:

- Pending service account password expiration (Bevorstehender Ablauf eines Dienstkontokennworts)
- Pending internal certificate expiration (Bevorstehender Ablauf eines internen Zertifikats)
- Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats)

Diese Warnungen können durch Ausführen der Geheimnisrotation behandelt werden. Führen Sie hierzu die im Anschluss beschriebenen Schritte aus.

> [!Note]
> In Azure Stack Hub-Umgebungen mit Versionen vor 1811 treten möglicherweise Warnungen zum bevorstehenden Ablauf von internen Zertifikaten oder Geheimnissen auf. Diese Warnungen sind ungenau und sollten ohne interne Geheimnisrotation ignoriert werden. Falsche Warnungen im Zusammenhang mit dem Ablauf interner Geheimnisse sind ein bekanntes Problem, das in 1811 behoben wurde. Interne Geheimnisse laufen erst ab, wenn die Umgebung für zwei Jahre aktiv war.

## <a name="pre-steps-for-secret-rotation"></a>Vorbereiten der Geheimnisrotation

   > [!IMPORTANT]
   > Wenn die Geheimnisrotation in Ihrer Azure Stack Hub-Umgebung bereits durchgeführt wurde, aktualisieren Sie das System auf Version 1811 oder höher, bevor Sie die Geheimnisrotation erneut ausführen. Die Geheimnisrotation muss über den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) ausgeführt werden und erfordert Anmeldeinformationen für einen Azure Stack Hub-Operator. Wenn die Azure Stack Hub-Operatoren in Ihrer Umgebung nicht wissen, ob die Geheimnisrotation in Ihrer Umgebung bereits ausgeführt wurde, aktualisieren Sie vor dem erneuten Ausführen der Geheimnisrotation auf 1811.

1. Es wird dringend empfohlen, die Azure Stack Hub-Instanz auf Version 1811 zu aktualisieren.

    > [!Note]
    > Bei Versionen vor 1811 muss zum Hinzufügen von Erweiterungshostzertifikaten keine Geheimnisrotation durchgeführt werden. Befolgen Sie die Anweisungen im Artikel [Vorbereiten auf den Erweiterungshost für Azure Stack Hub](azure-stack-extension-host-prepare.md), um Erweiterungshostzertifikate hinzuzufügen.

2. Operatoren werden bemerken, dass während der Rotation der Azure Stack Hub-Geheimnisse Warnungen geöffnet und automatisch wieder geschlossen werden.  Dieses Verhalten ist zu erwarten, und die Warnungen können ignoriert werden.  Operatoren können die Gültigkeit dieser Warnungen durch Ausführen von **Test-AzureStack** überprüfen.  Wenn Operatoren Azure Stack Hub-Systeme mithilfe von System Center Operations Manager (SCOM) überwachen, wird durch Versetzen eines Systems in den Wartungsmodus verhindert, dass diese Warnungen ihre ITSM-Systeme erreichen. Es werden jedoch weiterhin Warnungen ausgegeben, wenn das Azure Stack Hub-System nicht mehr erreichbar ist.

3. Informieren Sie Ihre Benutzer über sämtliche Wartungsmaßnahmen. Planen Sie normale Wartungszeitfenster möglichst außerhalb der Geschäftszeiten. Wartungsvorgänge können sowohl Benutzerworkloads als auch Portalvorgänge beeinträchtigen.

    > [!Note]
    > Die nächsten Schritte gelten nur für die Rotation externer Azure Stack Hub-Geheimnisse.

4. Führen Sie **[Test-AzureStack](azure-stack-diagnostic-test.md)** aus, und vergewissern Sie sich, dass alle Testausgaben fehlerfrei sind, bevor Sie Geheimnisse rotieren.
5. Bereiten Sie eine neue Gruppe externer Ersatzzertifikate vor. Die neue Gruppe entspricht den Zertifikatspezifikationen aus den [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md). Sie können eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) für den Kauf bzw. das Erstellen neuer Zertifikate mit den unter [Generieren von PKI-Zertifikaten](azure-stack-get-pki-certs.md) beschriebenen Schritten generieren und sie mit den in [ Vorbereiten von Azure Stack Hub-PKI-Zertifikaten](azure-stack-prepare-pki-certs.md) für die Verwendung in Ihrer Azure Stack Hub-Umgebung beschriebenen Schritten vorbereiten. Achten Sie darauf, dass Sie die Zertifikate mit den in [Überprüfen von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md) beschriebenen Schritten überprüfen.
6. Speichern Sie eine Sicherung der für die Rotation verwendeten Zertifikate an einem sicheren Sicherungsspeicherort. Sollte die Rotation nicht erfolgreich sein, ersetzen Sie die Zertifikate in der Dateifreigabe durch die Sicherungskopien, und wiederholen Sie dann die Rotation. Bewahren Sie Sicherungskopien am sicheren Sicherungsspeicherort auf.
7. Erstellen Sie eine Dateifreigabe, auf die Sie über die virtuellen ERCS-Computer zugreifen können. Die Identität **CloudAdmin** muss über Lese- und Schreibzugriff für die Dateifreigabe verfügen.
8. Öffnen Sie eine PowerShell ISE-Konsole auf einem Computer, auf dem Sie Zugriff auf die Dateifreigabe haben. Navigieren Sie zu Ihrer Dateifreigabe.
9. Führen Sie **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** aus, um die erforderlichen Verzeichnisse für Ihre externen Zertifikate zu erstellen.

> [!IMPORTANT]
> Mit dem Skript „CertDirectoryMaker“ wird eine Ordnerstruktur erstellt, die folgendem Muster entspricht:
>
> **.\Certificates\AAD** oder ***.\Certificates\ADFS***, je nach Identitätsanbieter für Azure Stack Hub.
>
> Es ist von größter Bedeutung, dass die Ordnerstruktur mit **AAD** oder **ADFS** endet und sich alle Unterverzeichnisse innerhalb dieser Struktur befinden, da andernfalls **Start-SecretRotation** zu folgender Ausgabe führt:
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Die Fehlermeldung deutet darauf hin, dass ein Problem mit dem Zugriff auf Ihre Dateifreigabe vorliegt. Tatsächlich wird hier jedoch die Ordnerstruktur erzwungen. Weitere Informationen finden Sie im Microsoft Azure Stack Readiness Checker im [PublicCertHelper-Modul](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1).
>
> Darüber hinaus muss die Ordnerstruktur der Dateifreigabe mit **Certificates** beginnen, da sonst ebenfalls ein Überprüfungsfehler auftritt.
> Die Einbindung der Dateifreigabe muss die Form **\\\\\<IPAddress>\\\<ShareName>\\** aufweisen und den Ordner **Certificates\AAD** oder **Certificates\ADFS** enthalten.
>
> Beispiel:
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Rotieren externer Geheimnisse

So rotieren Sie externe Geheimnisse:

1. Platzieren Sie die neue Gruppe externer Ersatzzertifikate in der Verzeichnisstruktur des Verzeichnisses **/Certificates\\\<IdentityProvider>** , das Sie im Rahmen der Vorbereitungsschritte erstellt haben. Berücksichtigen Sie dabei das Format, wie unter **Azure Stack Hub-PKI-Zertifikatanforderungen** im Abschnitt [Erforderliche Zertifikate](azure-stack-pki-certs.md) erläutert.

    Beispiel der Ordnerstruktur für den Azure AD-Identitätsanbieter:
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. Erstellen Sie unter Verwendung des Kontos **CloudAdmin** eine PowerShell-Sitzung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md), und speichern Sie die Sitzung als Variable. Diese Variable wird im nächsten Schritt als Parameter verwendet.

    > [!IMPORTANT]  
    > Geben Sie die Sitzung nicht ein. Speichern Sie die Sitzung als Variable.

3. Führen Sie **[Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)** aus. Übergeben Sie die PowerShell-Sitzungsvariable für den privilegierten Endpunkt als Parameter **Session**.

4. Führen Sie **Start-SecretRotation** mit den folgenden Parametern aus:
    - **PfxFilesPath**  
    Geben Sie den Netzwerkpfad des zuvor erstellten Verzeichnisses „Certificates“ an.  
    - **PathAccessCredential**  
    Ein PSCredential-Objekt mit Anmeldeinformationen für die Freigabe.
    - **CertificatePassword**  
    Eine sichere Zeichenfolge des Kennworts, das für alle erstellten PFX-Zertifikatdateien verwendet wird.

5. Warten Sie, bis Ihre Geheimnisse rotiert wurden. Die Rotation externer Geheimnisse dauert ca. eine Stunde.

    Wenn die Geheimnisrotation erfolgreich abgeschlossen wurde, wird in der Konsole **ActionPlanInstanceID ... CurrentStatus: Completed** gefolgt von **DONE** angezeigt.

    > [!Note]
    > Sollte bei der Geheimnisrotation ein Fehler auftreten, befolgen Sie die Anweisungen in der Fehlermeldung, und führen Sie **Start-SecretRotation** mit dem Parameter **-ReRun** erneut aus.

    ```powershell
    Start-SecretRotation -ReRun
    ```

    Wenden Sie sich an den Support, falls wiederholt Fehler bei der Geheimnisrotation auftreten.

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
> Interne Geheimnisse sollten nur rotiert werden, wenn Sie vermuten, dass ein internes Geheimnisse durch eine schädliche Entität kompromittiert wurde, oder wenn Sie eine Warnung (ab Build 1811) mit dem Hinweis erhalten haben, dass die internen Zertifikate demnächst ablaufen. In Azure Stack Hub-Umgebungen mit Versionen vor 1811 treten möglicherweise Warnungen zum bevorstehenden Ablauf von internen Zertifikaten oder Geheimnissen auf. Diese Warnungen sind ungenau und sollten ohne interne Geheimnisrotation ignoriert werden. Falsche Warnungen im Zusammenhang mit dem Ablauf interner Geheimnisse sind ein bekanntes Problem, das in 1811 behoben wurde. Interne Geheimnisse laufen erst ab, wenn die Umgebung für zwei Jahre aktiv war.

1. Erstellen Sie eine PowerShell-Sitzung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md).
2. Führen Sie in der Sitzung des privilegierten Endpunkts **Start-SecretRotation-Internal** aus.

    > [!Note]
    > Bei Azure Stack Hub-Umgebungen mit einer Version vor 1811 ist das Flag **-Internal** nicht erforderlich. **Start-SecretRotation** wird nur für interne Geheimnisse gedreht.

3. Warten Sie, bis Ihre Geheimnisse rotiert wurden.

   Wenn die Geheimnisrotation erfolgreich abgeschlossen wurde, wird in der Konsole **ActionPlanInstanceID ... CurrentStatus: Completed** gefolgt von **DONE** angezeigt.
    > [!Note]
    > Wenn bei der Geheimnisrotation ein Fehler auftritt, befolgen Sie die Anweisungen in der Fehlermeldung, und führen Sie **Start-SecretRotation** mit den Parametern **–Internal** und **-ReRun** erneut aus.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Wenden Sie sich an den Support, falls wiederholt Fehler bei der Geheimnisrotation auftreten.

## <a name="start-secretrotation-reference"></a>Referenz für „Start-SecretRotation“

Rotiert die Geheimnisse eines Azure Stack Hub-Systems. Wird nur für den privilegierten Azure Stack Hub-Endpunkt ausgeführt.

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

Das Cmdlet **Start-SecretRotation** rotiert die Infrastrukturgeheimnisse eines Azure Stack Hub-Systems. Standardmäßig werden nur die Zertifikate aller externen Netzwerkinfrastruktur-Endpunkte rotiert. Bei Verwendung mit dem Flag „-Internal“ werden die internen Infrastrukturgeheimnisse rotiert. Beim Rotieren externer Netzwerkinfrastruktur-Endpunkte muss **Start-SecretRotation** über einen **Invoke-Command**-Skriptblock ausgeführt werden, und die Sitzung des privilegierten Endpunkts der Azure Stack Hub-Umgebung muss als **Session**-Parameter übergeben werden.

### <a name="parameters"></a>Parameter

| Parameter | type | Erforderlich | Position | Standard | BESCHREIBUNG |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | String  | False  | benannt  | Keine  | Der Dateifreigabepfad des Verzeichnisses **\Certificates** mit allen externen Netzwerkendpunkt-Zertifikaten. Nur beim Rotieren externer Geheimnisse erforderlich. Das Endverzeichnis muss **\Certificates** sein. |
| `CertificatePassword` | SecureString | False  | benannt  | Keine  | Das Kennwort für alle Zertifikate in „-PfXFilesPath“. Erforderlich, wenn „PfxFilesPath“ beim Rotieren externer Geheimnisse angegeben wird. |
| `Internal` | String | False | benannt | Keine | Das Internal-Flag muss immer dann verwendet werden, wenn ein Azure Stack Hub-Operator interne Infrastrukturgeheimnisse rotieren möchte. |
| `PathAccessCredential` | PSCredential | False  | benannt  | Keine  | Die PowerShell-Anmeldeinformationen für die Dateifreigabe des Verzeichnisses **\Certificates** mit allen externen Netzwerkendpunkt-Zertifikaten. Nur beim Rotieren externer Geheimnisse erforderlich.  |
| `ReRun` | SwitchParameter | False  | benannt  | Keine  | Die erneute Ausführung muss immer dann erfolgen, wenn die Geheimnisrotation nach einem fehlgeschlagenen Versuch erneut versucht wird. |

### <a name="examples"></a>Beispiele

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Nur Rotieren interner Infrastrukturgeheimnisse

Dieser Befehl muss über den [privilegierten Endpunkt Ihrer Azure Stack Hub-Umgebung](azure-stack-privileged-endpoint.md) ausgeführt werden.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Dieser Befehl rotiert alle Infrastrukturgeheimnisse, die für das interne Azure Stack Hub-Netzwerk verfügbar gemacht wurden.

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

Dieser Befehl rotiert die TLS-Zertifikate, die für die externen Netzwerkinfrastruktur-Endpunkte von Azure Stack Hub verwendet werden.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Rotieren interner und externer Infrastrukturgeheimnisse (nur Versionen **vor 1811**)

> [!IMPORTANT]
> Dieser Befehl gilt nur für Azure Stack Hub-Versionen **vor 1811**, da die Rotation für interne und externe Zertifikate aufgeteilt wurde.
>
> **Ab *1811* können interne und externe Zertifikate nicht mehr gemeinsam rotiert werden.**

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

Dieser Befehl rotiert alle Infrastrukturgeheimnisse, die für das interne Azure Stack Hub-Netzwerk verfügbar gemacht wurden, sowie die TLS-Zertifikate, die für die externen Netzwerkinfrastruktur-Endpunkte von Azure Stack Hub verwendet werden. „Start-SecretRotation“ rotiert alle von Stack generierten Geheimnisse, und da bereitgestellte Zertifikate vorhanden sind, werden auch externe Endpunktzertifikate rotiert.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Aktualisieren des Baseboard Management Controller (BMC, Baseboard-Verwaltungscontroller)

Der Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) überwacht den physischen Zustand Ihrer Server. Anweisungen zur Aktualisierung des Benutzerkontonamens und Kennworts des BMC erhalten Sie vom OEM-Hardwareanbieter (Original Equipment Manufacturer, Originalgerätehersteller).

>[!NOTE]
> Der OEM stellt unter Umständen zusätzliche Verwaltungs-Apps bereit. Die Aktualisierung des Benutzernamens oder Kennworts für andere Verwaltungs-Apps wirkt sich nicht auf den BMC-Benutzernamen oder auf das BMC-Kennwort aus.

1. **Versionen vor 1910:** Aktualisieren Sie den BMC auf den physischen Azure Stack Hub-Servern gemäß den Anweisungen des OEM. Benutzername und Kennwort für jeden BMC in Ihrer Umgebung müssen identisch sein. BMC-Benutzernamen dürfen nicht länger als 16 Zeichen sein.

   **Ab Version 1910:** Es ist nicht mehr erforderlich, zuerst die BMC-Anmeldeinformationen für die physischen Azure Stack Hub-Server gemäß den OEM-Anweisungen zu aktualisieren. Benutzername und Kennwort für jeden BMC in Ihrer Umgebung müssen identisch sein. BMC-Benutzernamen dürfen nicht länger als 16 Zeichen sein.

    | Parameter | BESCHREIBUNG | State |
    | --- | --- | --- |
    | BypassBMCUpdate | Wenn Sie diesen Parameter verwenden, werden die Anmeldeinformationen im BMC nicht aktualisiert. Nur der interne Azure Stack Hub-Datenspeicher wird aktualisiert. | Optional |

2. Öffnen Sie in Azure Stack Hub-Sitzungen einen privilegierten Endpunkt. Anweisungen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](azure-stack-privileged-endpoint.md).

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

[Weitere Informationen zur Sicherheit von Azure Stack Hub](azure-stack-security-foundations.md)
