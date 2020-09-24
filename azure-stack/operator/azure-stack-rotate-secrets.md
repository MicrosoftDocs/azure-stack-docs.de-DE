---
title: Rotieren von Geheimnissen
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie Ihre Geheimnisse in Azure Stack Hub rotieren.
author: BryanLa
ms.topic: how-to
ms.date: 06/29/2020
ms.reviewer: ppacent
ms.author: bryanla
ms.lastreviewed: 08/15/2020
monikerRange: '>=azs-1803'
ms.openlocfilehash: 463fc8fbee16aa7eddc78cee7c3868f1526fad21
ms.sourcegitcommit: 849be7ebd02a1e54e8d0ec59736c9917c67e309e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2020
ms.locfileid: "91134745"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Rotieren von Geheimnissen in Azure Stack Hub

*Diese Anweisungen gelten nur für integrierte Azure Stack Hub-Systeme ab Version 1803. Versuchen Sie nicht, die Geheimnisrotation mit älteren Versionen (vor 1803) zu verwenden*

Dieser Artikel enthält Anleitungen und PowerShell-Skripts für die Geheimnisrotation, um die sichere Kommunikation mit Azure Stack Hub-Infrastrukturressourcen und -diensten zu gewährleisten. 

## <a name="overview"></a>Übersicht

Azure Stack Hub verwendet Geheimnisse, um eine sichere Kommunikation mit Infrastrukturressourcen und -diensten zu gewährleisten. Um die Integrität der Azure Stack Hub-Infrastruktur zu erhalten, müssen Operatoren die Geheimnisse mit einer Häufigkeit rotieren können, die mit den Sicherheitsanforderungen ihrer Organisation in Einklang steht.

### <a name="internal-vs-external-secrets"></a>Interne und externe Geheimnisse

Seit Release 1811 ist die Geheimnisrotation für interne und externe Zertifikate getrennt:

- **Interne Geheimnisse**: Zertifikate, Kennwörter, sicheren Zeichenfolgen und Schlüssel, die von der Azure Stack Hub-Infrastruktur ohne Eingriff durch den Azure Stack Hub-Operator verwendet werden.

- **Externe Geheimnisse**: Infrastrukturdienstzertifikate für extern ausgerichtete Dienste, die vom Azure Stack Hub-Operator bereitgestellt werden. Zu externen Geheimnissen zählen Zertifikate für folgende Dienste:

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

> [!Important]
> Alle anderen sicheren Schlüssel und Zeichenfolgen werden vom Administrator manuell aktualisiert. Dies schließt Kennwörter für Benutzer- und Administratorkonten, [Kennwörter und Berechtigungen für Netzwerkswitches](azure-stack-customer-defined.md) sowie Anmeldeinformationen für den Baseboard-Verwaltungscontroller (BMC) ein, auf die [weiter unten in diesem Artikel](#update-the-bmc-credential) eingegangen wird. 
>
>Außerdem wird in diesem Artikel nicht die Geheimnisrotation für Ressourcenanbieter (RPs) behandelt, die Mehrwert schaffen. Wenn Sie diese Geheimnisse rotieren möchten, lesen Sie stattdessen die folgenden Artikel:
>
> - [Rotieren von Geheimnissen und Zertifikaten in App Service in Azure Stack Hub](app-service-rotate-certificates.md)
> - [MySQL-Ressourcenanbieter – Rotieren von Geheimnissen](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
> - [SQL-Ressourcenanbieter – Rotieren von Geheimnissen](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)

### <a name="expiration-alerts"></a>Ablaufwarnungen

Für Geheimnisse, die in 30 Tagen ablaufen, werden im Administratorportal folgende Warnungen generiert:

- Pending service account password expiration (Bevorstehender Ablauf eines Dienstkontokennworts)
- Pending internal certificate expiration (Bevorstehender Ablauf eines internen Zertifikats)
- Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats)

Durch die Durchführung der in den folgenden Abschnitten beschriebenen Schritte zur Geheimnisrotation werden diese Warnungen behoben.

> [!Note]
> In Azure Stack Hub-Umgebungen mit Versionen vor 1811 treten möglicherweise Warnungen zum bevorstehenden Ablauf von internen Zertifikaten oder Geheimnissen auf. Diese Warnungen sind ungenau und sollten ohne interne Geheimnisrotation ignoriert werden. Falsche Warnungen im Zusammenhang mit dem Ablauf interner Geheimnisse sind ein bekanntes Problem, das in 1811 behoben wurde. Interne Geheimnisse laufen erst ab, wenn die Umgebung für zwei Jahre aktiv war.

### <a name="external-certificates-from-a-new-certificate-authority"></a>Externe Zertifikate von einer neuen Zertifizierungsstelle

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

<sup>*</sup>Gibt an, dass die Öffentlichen Zertifizierungsstellen Teil des „Vertrauenswürdiger Stamm“-Programms von Windows sind. Sie finden die vollständige Liste im Artikel [List of Participants - Microsoft Trusted Root Program](/security/trusted-root/participants-list).

## <a name="prerequisites"></a>Voraussetzungen

Für die Rotation interner und externer Geheimnisse:

1. Es wird dringend empfohlen, die Azure Stack Hub-Instanz auf die neueste Version zu aktualisieren.

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > Für Versionen vor 1811:
    > - Wenn die Geheimnisrotation bereits durchgeführt wurde, müssen Sie auf Version 1811 oder höher aktualisieren, bevor Sie die Geheimnisrotation erneut ausführen. Die Geheimnisrotation muss über den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) ausgeführt werden und erfordert Anmeldeinformationen für einen Azure Stack Hub-Operator. Wenn Sie nicht wissen, ob die Geheimnisrotation für Ihre Umgebung ausgeführt wurde, aktualisieren Sie vor dem Ausführen der Geheimnisrotation auf Version 1811.
    > - Zum Hinzufügen von Erweiterungshostzertifikaten muss keine Geheimnisrotation durchgeführt werden. Befolgen Sie die Anweisungen im Artikel [Vorbereiten auf den Erweiterungshost für Azure Stack Hub](azure-stack-extension-host-prepare.md), um Erweiterungshostzertifikate hinzuzufügen.
    ::: moniker-end

2. Informieren Sie Ihre Benutzer über geplante Wartungsmaßnahmen. Planen Sie normale Wartungszeitfenster möglichst außerhalb der Geschäftszeiten. Wartungsvorgänge können sowohl Benutzerworkloads als auch Portalvorgänge beeinträchtigen.

3. Operatoren wird auffallen, dass während der Geheimnisrotation Warnungen geöffnet und automatisch wieder geschlossen werden. Dieses Verhalten ist zu erwarten, und die Warnungen können ignoriert werden. Operatoren können die Gültigkeit dieser Warnungen durch Verwenden des Cmdlets [Test-AzureStack PowerShell](azure-stack-diagnostic-test.md) überprüfen. Wenn Operatoren Azure Stack Hub-Systeme mithilfe von System Center Operations Manager (SCOM) überwachen, wird durch Versetzen eines Systems in den Wartungsmodus verhindert, dass diese Warnungen ihre ITSM-Systeme erreichen. Es werden jedoch weiterhin Warnungen ausgegeben, wenn das Azure Stack Hub-System nicht mehr erreichbar ist.

Zum Rotieren externer Geheimnisse müssen Sie diese zusätzlichen Voraussetzungen erfüllen:

1. Führen Sie **[Test-AzureStack](azure-stack-diagnostic-test.md)** aus, und vergewissern Sie sich, dass alle Testausgaben fehlerfrei sind, bevor Sie Geheimnisse rotieren.
2. Bereiten Sie eine neue Gruppe externer Ersatzzertifikate vor:
    - Die neue Gruppe muss den Zertifikatspezifikationen aus den [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md) entsprechen. 
    - Sie können eine Zertifikatsignieranforderung (Certificate Signing Request, CSR), die an Ihre Zertifizierungsstelle gesendet werden soll, mit den unter [Generieren von Zertifikatsignieranforderungen](azure-stack-get-pki-certs.md) beschriebenen Schritten generieren und sie mit den in [Vorbereiten von PKI-Zertifikaten](azure-stack-prepare-pki-certs.md) beschriebenen Schritten für die Verwendung in Ihrer Azure Stack Hub-Umgebung vorbereiten. 
    - Achten Sie darauf, dass Sie die Zertifikate mit den in [Überprüfen von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md) beschriebenen Schritten überprüfen.
    - Vergewissern Sie sich, dass das Kennwort keine Sonderzeichen wie `*` oder `)` enthält.
    - Vergewissern Sie sich, dass als PFX-Verschlüsselung **TripleDES-SHA1** verwendet wird. Lesen Sie bei Bedarf die Informationen unter [Beheben allgemeiner Probleme mit Azure Stack Hub-PKI-Zertifikaten](azure-stack-remediate-certs.md#pfx-encryption).
3. Speichern Sie eine Sicherung der für die Rotation verwendeten Zertifikate an einem sicheren Sicherungsspeicherort. Sollte die Rotation nicht erfolgreich sein, ersetzen Sie die Zertifikate in der Dateifreigabe durch die Sicherungskopien, und wiederholen Sie dann die Rotation. Bewahren Sie Sicherungskopien am sicheren Sicherungsspeicherort auf.
4. Erstellen Sie eine Dateifreigabe, auf die Sie über die virtuellen ERCS-Computer zugreifen können. Die Identität **CloudAdmin** muss über Lese- und Schreibzugriff für die Dateifreigabe verfügen.
5. Öffnen Sie eine PowerShell ISE-Konsole auf einem Computer, auf dem Sie Zugriff auf die Dateifreigabe haben. Navigieren Sie zu Ihrer Dateifreigabe, wo Sie Verzeichnisse für Ihre externen Zertifikate erstellen.
6. Laden Sie **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** auf eine Netzwerkdateifreigabe herunter, auf die während der Rotation zugegriffen werden kann, und führen Sie das Skript aus. Das Skript erstellt eine Ordnerstruktur entsprechend ***.\Certificates\AAD*** oder ***.\Certificates\ADFS***, abhängig von Ihrem Identitätsanbieter. Ihre Ordnerstruktur muss mit einem **\\Certificates**-Ordner beginnen, gefolgt von NUR einem **\\AAD**- oder **\\ADFS**-Ordner. Alle zusätzlichen Unterverzeichnisse sind in der vorangehenden Struktur enthalten. Zum Beispiel:
    - Dateifreigabe = **\\\\\<IPAddress>\\\<ShareName>**
    - Stammordner des Zertifikats für Azure AD-Anbieter = **\\Certificates\AAD**
    - Vollständiger Pfad = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

    > [!IMPORTANT]
    > Wenn Sie `Start-SecretRotation` später ausführen, wird die Ordnerstruktur überprüft. Bei einer nicht kompatiblen Ordnerstruktur wird der folgende Fehler ausgelöst:
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. Kopieren Sie den neuen Satz der in Schritt 2 erstellten externen Ersatzzertifikate in das in Schritt 6 erstellte **\Certificates\\\<IdentityProvider>** -Verzeichnis. Stellen Sie sicher, dass Sie das `cert.<regionName>.<externalFQDN>`-Format für \<CertName\> befolgen. 

    Hier sehen Sie ein Beispiel einer Ordnerstruktur für den Azure AD-Identitätsanbieter:
    ```powershell
        <ShareName>
            │
            └───Certificates
                  └───AAD
                      ├───ACSBlob
                      │       <CertName>.pfx
                      │
                      ├───ACSQueue
                      │       <CertName>.pfx
                      │
                      ├───ACSTable
                      │       <CertName>.pfx
                      │
                      ├───Admin Extension Host
                      │       <CertName>.pfx
                      │
                      ├───Admin Portal
                      │       <CertName>.pfx
                      │
                      ├───ARM Admin
                      │       <CertName>.pfx
                      │
                      ├───ARM Public
                      │       <CertName>.pfx
                      │
                      ├───KeyVault
                      │       <CertName>.pfx
                      │
                      ├───KeyVaultInternal
                      │       <CertName>.pfx
                      │
                      ├───Public Extension Host
                      │       <CertName>.pfx
                      │
                      └───Public Portal
                              <CertName>.pfx

    ```

## <a name="rotate-external-secrets"></a>Rotieren externer Geheimnisse

Führen Sie die folgenden Schritte aus, um externe Geheimnisse zu rotieren:

1. Verwenden Sie das folgende PowerShell-Skript, um externe Geheimnisse zu rotieren. Das Skript erfordert Zugriff auf eine Sitzung mit dem privilegierten Endpunkt (PEP). Der Zugriff auf den PEP erfolgt über eine PowerShell-Remotesitzung auf dem virtuellen Computer (VM), auf dem der PEP gehostet wird. Wenn Sie ein integriertes System verwenden, gibt es drei Instanzen des PEPs. Diese werden jeweils auf einer VM („Präfix-ERCS01“, „Präfix-ERCS02“ und „Präfix-ERCS03“) auf unterschiedlichen Hosts ausgeführt. Wenn Sie ASDK verwenden, hat diese VM den Namen „AzS-ERCS01“. Aktualisieren Sie die `<placeholder>`-Werte vor der Ausführung:

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    $CertPassword = ConvertTo-SecureString "<Cert_Password>" -AsPlainText -Force
    $CertShareCreds = Get-Credential
    $CertSharePath = "<Network_Path_Of_CertShare>"
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
    }
    Remove-PSSession -Session $PEPSession
    ```

    Mit diesem Skript werden die folgenden Schritte ausgeführt:

    - Erstellt eine PowerShell-Sitzung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) unter Verwendung des Kontos **CloudAdmin** und speichert die Sitzung als Variable. Diese Variable wird im nächsten Schritt als Parameter verwendet. 

    - Führt [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1) aus und übergibt dabei die PEP-Sitzungsvariable als `-Session`-Parameter.

    - Führt `Start-SecretRotation` in der PEP-Sitzung mit den folgenden Parametern aus:
        - `-PfxFilesPath`: Der Netzwerkpfad zu Ihrem zuvor erstellten Verzeichnis „Certificates“.  
        - `-PathAccessCredential`: Das PSCredential-Objekt mit Anmeldeinformationen für die Freigabe.
        - `-CertificatePassword`: Eine sichere Zeichenfolge des Kennworts, das für alle erstellten PFX-Zertifikatdateien verwendet wird.

2. Die Rotation externer Geheimnisse dauert ca. eine Stunde. Nach erfolgreichem Abschluss wird in Ihrer Konsole `ActionPlanInstanceID ... CurrentStatus: Completed`, gefolgt von `DONE` angezeigt. Entfernen Sie Ihre Zertifikate aus der Freigabe, die Sie im Abschnitt „Voraussetzungen“ erstellt haben, und speichern Sie sie an ihrem sicheren Sicherungsspeicherort.

    > [!Note]
    > Sollte bei der Geheimnisrotation ein Fehler auftreten, befolgen Sie die Anweisungen in der Fehlermeldung, und führen Sie `Start-SecretRotation` mit dem Parameter `-ReRun` erneut aus.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Wenden Sie sich an den Support, falls wiederholt Fehler bei der Geheimnisrotation auftreten.

## <a name="rotate-internal-secrets"></a>Rotieren interner Geheimnisse

Eine interne Geheimnisrotation ist nur dann erforderlich, wenn Sie den Verdacht haben, dass eines davon kompromittiert wurde, oder wenn Sie eine Ablaufwarnung erhalten haben. In Versionen vor 1811 treten möglicherweise Warnungen für ausstehende interne Zertifikate oder Geheimnisablaufzeiten auf. Diese Warnungen sind ungenau und sollten ignoriert werden. Es handelt sich um ein bekanntes Problem, das in 1811 behoben wurde. Interne Geheimnisse laufen erst ab, wenn die Umgebung für zwei Jahre aktiv war.

Ziehen Sie das PowerShell-Skript in Schritt 2 von [Rotieren externer Geheimnisse](#rotate-external-secrets) zurate. Das Skript enthält ein Beispiel, das Sie für die interne Geheimnisrotation anpassen können, indem Sie einige Änderungen vornehmen, um die folgenden Schritte auszuführen:

1. Fügen Sie im Abschnitt „Geheimnisrotation ausführen“ z. B. den Parameter `-Internal` zum Cmdlet [Start-SecretRotation](/azure-stack/reference/pep-2002/start-secretrotation) hinzu:

    ```powershell
    # Run Secret Rotation
    ...
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    ...
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Für Versionen vor 1811 ist das `-Internal`-Flag nicht erforderlich. 
    ::: moniker-end

3. Nach erfolgreichem Abschluss wird in Ihrer Konsole `ActionPlanInstanceID ... CurrentStatus: Completed`, gefolgt von `DONE` angezeigt.

    > [!Note]
    > Wenn bei der Geheimnisrotation ein Fehler auftritt, befolgen Sie die Anweisungen in der Fehlermeldung, und führen Sie `Start-SecretRotation` mit den Parametern `-Internal` und `-ReRun` erneut aus.  
    >
    >```powershell
    >Start-SecretRotation -Internal -ReRun
    >```
    >
    > Wenden Sie sich an den Support, falls wiederholt Fehler bei der Geheimnisrotation auftreten.

## <a name="update-the-bmc-credential"></a>Aktualisieren der BMC-Anmeldeinformationen

Der Baseboard-Verwaltungscontroller überwacht den physischen Zustand Ihrer Server. Anweisungen zur Aktualisierung des Benutzerkontonamens und Kennworts des BMC erhalten Sie vom OEM-Hardwareanbieter (Original Equipment Manufacturer, Originalgerätehersteller).

>[!NOTE]
> Der OEM stellt unter Umständen zusätzliche Verwaltungs-Apps bereit. Die Aktualisierung des Benutzernamens oder Kennworts für andere Verwaltungs-Apps wirkt sich nicht auf den BMC-Benutzernamen oder auf das BMC-Kennwort aus. 

::: moniker range="<azs-1910"
1. Aktualisieren Sie den BMC auf den physischen Azure Stack Hub-Servern gemäß den Anweisungen des OEM. Benutzername und Kennwort für jeden BMC in Ihrer Umgebung müssen identisch sein. BMC-Benutzernamen dürfen nicht länger als 16 Zeichen sein.
::: moniker-end

::: moniker range=">=azs-1910"
1. Es ist nicht mehr erforderlich, zuerst die BMC-Anmeldeinformationen für die physischen Azure Stack Hub-Server gemäß den OEM-Anweisungen zu aktualisieren. Benutzername und Kennwort für jeden BMC in Ihrer Umgebung müssen identisch sein und dürfen nicht mehr als 16 Zeichen enthalten. 
::: moniker-end

2. Öffnen Sie in Azure Stack Hub-Sitzungen einen privilegierten Endpunkt. Anweisungen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](azure-stack-privileged-endpoint.md). 

3. Nachdem die PowerShell-Eingabeaufforderung sich je nach Umgebung in `[IP address or ERCS VM name]: PS>` oder in `[azs-ercs01]: PS>` geändert hat, führen Sie `Set-BmcCredential` aus, indem Sie `Invoke-Command` ausführen. Wenn Sie den optionalen Parameter `-BypassBMCUpdate` mit `Set-BMCCredential` verwenden, werden die Anmeldeinformationen im BMC nicht aktualisiert. Nur der interne Azure Stack Hub-Datenspeicher wird aktualisiert. Übergeben Sie die Sitzungsvariable des privilegierten Endpunkts als Parameter. 

    Im folgenden finden Sie ein PowerShell-Beispielskript, mit dem Benutzername und Kennwort angefordert werden: 

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

    Sie können den Benutzernamen und das Kennwort auch in Variablen codieren, was weniger sicher sein kann:

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

## <a name="reference-start-secretrotation-cmdlet"></a>Referenz: Cmdlet „Start-SecretRotation“

Das Cmdlet [Start-SecretRotation](/azure-stack/reference/pep-2002/start-secretrotation) rotiert die Infrastrukturgeheimnisse eines Azure Stack Hub-Systems. Dieses Cmdlet kann nur für den privilegierten Azure Stack Hub-Endpunkt ausgeführt werden, indem ein `Invoke-Command`-Skriptblock verwendet wird, der die PEP-Sitzung im `-Session`-Parameter übergibt. Standardmäßig werden nur die Zertifikate aller externen Netzwerkinfrastruktur-Endpunkte rotiert.

| Parameter | type | Erforderlich | Position | Standard | BESCHREIBUNG |
|--|--|--|--|--|--|
| `PfxFilesPath` | String  | False  | benannt  | Keine  | Der Dateifreigabepfad des Verzeichnisses **\Certificates** mit allen externen Netzwerkendpunkt-Zertifikaten. Nur beim Rotieren externer Geheimnisse erforderlich. Das Endverzeichnis muss **\Certificates** sein. |
| `CertificatePassword` | SecureString | False  | benannt  | Keine  | Das Kennwort für alle Zertifikate in „-PfXFilesPath“. Erforderlich, wenn „PfxFilesPath“ beim Rotieren externer Geheimnisse angegeben wird. |
| `Internal` | String | False | benannt | Keine | Das Internal-Flag muss immer dann verwendet werden, wenn ein Azure Stack Hub-Operator interne Infrastrukturgeheimnisse rotieren möchte. |
| `PathAccessCredential` | PSCredential | False  | benannt  | Keine  | Die PowerShell-Anmeldeinformationen für die Dateifreigabe des Verzeichnisses **\Certificates** mit allen externen Netzwerkendpunkt-Zertifikaten. Nur beim Rotieren externer Geheimnisse erforderlich.  |
| `ReRun` | SwitchParameter | False  | benannt  | Keine  | Muss immer dann erfolgen, wenn die Geheimnisrotation nach einem fehlgeschlagenen Versuch erneut versucht wird. |

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
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

::: moniker range="<azs-1811"
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Rotieren interner und externer Infrastrukturgeheimnisse (nur Versionen **vor 1811**)

> [!IMPORTANT]
> Dieser Befehl gilt nur für Azure Stack Hub-Versionen **vor 1811**, da die Rotation für interne und externe Zertifikate aufgeteilt wurde.
>
> **Ab *1811* können interne und externe Zertifikate nicht mehr gemeinsam rotiert werden.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

Dieser Befehl rotiert die Infrastrukturgeheimnisse, die für das interne Azure Stack Hub-Netzwerk verfügbar gemacht wurden, und die TLS-Zertifikate, die für die externen Netzwerkinfrastruktur-Endpunkte von Azure Stack Hub verwendet werden. „Start-SecretRotation“ rotiert alle von Stack generierten Geheimnisse, und da bereitgestellte Zertifikate vorhanden sind, werden auch externe Endpunktzertifikate rotiert.  
::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zur Sicherheit von Azure Stack Hub](azure-stack-security-foundations.md)
