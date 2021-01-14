---
title: Wartungsvorgänge von SQL-Ressourcenanbietern
titleSuffix: Azure Stack Hub
description: Hier finden Sie Informationen zu Wartungsvorgängen für den SQL-Ressourcenanbieter in Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 212d6c89bedc6f46eb6db2cbf735fa61ea0ad909
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103098"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Wartungsvorgänge von SQL-Ressourcenanbietern

Der SQL-Ressourcenanbieter wird auf einem gesperrten virtuellen Computer (virtual machine, VM) ausgeführt. Damit Wartungsvorgänge durchgeführt werden können, müssen Sie die Sicherheit der VM aktualisieren. Verwenden Sie hierzu den Endpunkt *DBAdapterMaintenance* mit [PowerShell Just Enough Administration (JEA)](/powershell/scripting/learn/remoting/jea/overview), um dem Prinzip der geringsten Rechte zu entsprechen. Das Ressourcenanbieter-Installationspaket enthält ein Skript für diese Aktion.

## <a name="patching-and-updating"></a>Patchen und Aktualisieren

Der SQL-Ressourcenanbieter wird nicht als Teil von Azure Stack Hub gewartet, da es sich um eine Add-On-Komponente handelt. Microsoft stellt bei Bedarf Updates für den SQL-Ressourcenanbieter bereit. Bei der Veröffentlichung eines aktualisierten SQL-Adapters wird ein Skript zum Anwenden des Updates bereitgestellt. Dieses Skript erstellt eine neue Ressourcenanbieter-VM und migriert den Zustand der älteren Anbieter-VM auf die neue VM. Weitere Informationen finden Sie unter [Aktualisieren des SQL-Ressourcenanbieters](azure-stack-sql-resource-provider-update.md).

### <a name="provider-vm"></a>Anbieter-VM

Da der Ressourcenanbieter auf einer *Benutzer*-VM ausgeführt wird, müssen Sie die erforderlichen Patches und Updates installieren, sobald sie veröffentlicht werden. Verwenden Sie die Windows Update-Pakete, die im Rahmen des Patch- und Updatezyklus bereitgestellt werden, um den virtuellen Computer zu aktualisieren.

## <a name="updating-sql-credentials"></a>Aktualisieren von SQL-Anmeldeinformationen

Sie sind für das Erstellen und Verwalten von Systemadministratorkonten auf Ihren SQL Server-Instanzen verantwortlich. Der Ressourcenanbieter benötigt ein Konto mit diesen Berechtigungen zum Verwalten von Datenbanken für Benutzer – er benötigt keinen Zugriff auf die Daten der Benutzer. Wenn Sie die Systemadministratorkennwörter auf Ihren SQL Server-Instanzen ändern müssen, können Sie die Administratorschnittstelle des Ressourcenanbieters verwenden, um das gespeicherte Kennwort zu ändern. Diese Kennwörter werden in einem Schlüsseltresor in Ihrer Azure Stack Hub-Instanz gespeichert.

Um die Einstellungen zu ändern, wählen Sie **Durchsuchen** &gt; **VERWALTUNGSRESSOURCEN** &gt; **SQL-Hostserver** &gt; **SQL-Anmeldungen**, und wählen Sie einen Benutzernamen aus. Die Änderung muss zuerst auf der SQL-Instanz vorgenommen werden (und ggf. auf allen Replikaten). Wählen Sie unter **Einstellungen** die Option **Kennwort** aus.

![Aktualisieren des SQL-Administratorkennworts](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>Geheimnisrotation

*Diese Anweisungen gelten nur für integrierte Azure Stack Hub-Systeme.*

Bei Verwendung der SQL- und MySQL-Ressourcenanbieter mit integrierten Azure Stack Hub-Systemen ist der Azure Stack Hub-Operator dafür verantwortlich, die folgenden Geheimnisse der Ressourcenanbieterinfrastruktur zu rotieren, um sicherzustellen, dass sie nicht ablaufen:

- Das [während der Bereitstellung angegebene](azure-stack-pki-certs.md) externe SSL-Zertifikat
- Das während der Bereitstellung angegebene Kennwort des lokalen Administratorkontos der Ressourcenanbieter-VM
- Das Kennwort des Diagnosebenutzers (dbadapterdiag) für den Ressourcenanbieter
- (version >= 1.1.47.0) Während der Bereitstellung generiertes Key Vault-Zertifikat.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-Beispiele für die Rotation von Geheimnissen

**Gleichzeitiges Ändern aller Geheimnisse**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

**Ändern des Kennworts für den Diagnosebenutzer**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**Ändern des Kennworts des lokalen Administratorkontos für die VM**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Ändern des SSL-Zertifikatkennworts**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

**Ändern des Kennworts für das Key Vault-Zertifikat**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parameter für „SecretRotationSQLProvider.ps1“

|Parameter|Beschreibung|Kommentar|
|-----|-----|-----|
|AzureEnvironment|Die zum Bereitstellen von Azure Stack Hub verwendete Azure-Umgebung des Dienstadministratorkontos. Nur für Azure AD-Bereitstellungen erforderlich. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für Azure Active Directory für China **AzureChinaCloud**.|Optional|
|AzCredential|Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Beim Skript tritt ein Fehler auf, wenn für das Konto, das Sie mit AzCredential verwenden, mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist.|Obligatorisch.|
|CloudAdminCredential|Anmeldeinformationen für das Domänenkonto des Azure Stack Hub-Cloudadministrators.|Obligatorisch.|
|PrivilegedEndpoint|Privilegierter Endpunkt für den Zugriff auf „Get-AzureStackStampInformation“|Obligatorisch.|
|DiagnosticsUserPassword|Kennwort des Diagnosebenutzerkontos|Optional|
|VMLocalCredential|Lokales Administratorkonto des virtuellen Computers „MySQLAdapter“|Optional|
|DefaultSSLCertificatePassword|Kennwort für das SSL-Standardzertifikat (PFX-Datei)|Optional|
|DependencyFilesLocalPath|Lokaler Pfad der Abhängigkeitsdateien|Optional|
|KeyVaultPfxPassword|Das Kennwort, das zum Generieren des Key Vault-Zertifikats für Datenbankadapter verwendet wird.|Optional|
|     |     |     |

### <a name="known-issues"></a>Bekannte Probleme

**Problem**:<br>
Protokolle für Geheimnisrotation. Die Protokolle für die Geheimnisrotation werden nicht automatisch erfasst, wenn beim Ausführen des benutzerdefinierten Skripts für die Geheimnisrotation ein Fehler auftritt.

**Problemumgehung**:<br>
Verwenden Sie das Cmdlet Get-AzsDBAdapterLogs, um alle Ressourcenanbieterprotokolle, einschließlich „AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log“, gespeichert unter „C:\Logs“, zu erfassen.

## <a name="update-the-vm-operating-system"></a>Aktualisieren des VM-Betriebssystems

Verwenden Sie eine der folgenden Methoden, um das Betriebssystem des virtuellen Computers zu aktualisieren:

- Installieren des neuesten Ressourcenanbieterpakets über ein VM-Image, das über aktuelle Patches verfügt
- Installieren eines Windows Update-Pakets während der Installation oder des Updates des Ressourcenanbieters

## <a name="update-the-vm-windows-defender-definitions"></a>Aktualisieren der Windows Defender-Definitionen für die VM

Aktualisieren der Windows Defender-Definitionen für den virtuellen Computer

1. Laden Sie aktuellen Windows Defender-Definitionen von der Seite [Security Intelligence-Updates für Windows Defender Antivirus und andere Microsoft Antimalware-Lösungen](https://www.microsoft.com/wdsi/definitions) herunter.

   Scrollen Sie auf der Seite mit den Definitionsupdates nach unten zu „Manuelles Herunterladen des Updates“. Laden Sie die 64-Bit-Datei „Windows Defender Antivirus for Windows 10 and Windows 8.1“ herunter.

   Alternativ können Sie auch [diesen Direktlink](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) verwenden, um die Datei „fpam-fe.exe“ herunterzuladen und auszuführen.

2. Erstellen Sie eine PowerShell-Sitzung mit dem Wartungsendpunkt der SQL-Ressourcenanbieteradapter-VM.

3. Kopieren Sie die Datei mit den Definitionsupdates unter Verwendung der Wartungsendpunkt-Sitzung auf den virtuellen Computer.

4. Führen Sie in der PowerShell-Wartungssitzung den Befehl *Update-DBAdapterWindowsDefenderDefinitions* aus.

5. Nach der Installation empfiehlt es sich, die Datei mit den Definitionsupdates mithilfe des Befehls *Remove-ItemOnUserDrive* zu entfernen.

**PowerShell-Skriptbeispiel zum Aktualisieren von Definitionen**

Zum Aktualisieren der Defender-Definitionen können Sie das folgende Skript bearbeiten und ausführen. Ersetzen Sie die Werte im Skript durch Werte aus Ihrer Umgebung.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter VM.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Erfassen von Diagnoseprotokollen

::: moniker range=">= azs-2008"

Azure Stack Hub bietet mehrere Methoden zum Erfasse, Speichern und Senden von Diagnoseprotokollen an den Microsoft-Support. Der SQL-Ressourcenanbieter unterstützt ab Version 1.1.93 das Standardverfahren zum Erfassen von Protokollen aus der Azure Stack Hub-Umgebung. Weitere Informationen finden Sie unter [Sammlung von Diagnoseprotokollen](diagnostic-log-collection.md).

::: moniker-end

::: moniker range="< azs-2008"

Der SQL-Ressourcenanbieter unterstützt ab Version 1.1.93 das Standardverfahren zum Erfassen von Protokollen aus der Azure Stack Hub-Umgebung. Wenn Sie eine ältere Version verwenden, empfiehlt es sich, den SQL-Ressourcenanbieter auf die neueste Version zu aktualisieren.

Zur Erfassung von Protokollen der gesperrten virtuellen Computer können Sie den Endpunkt *DBAdapterDiagnostics* mit PowerShell Just Enough Administration (JEA) verwenden. Dieser Endpunkt bietet die folgenden Befehle:

- **Get-AzsDBAdapterLog**. Dieser Befehl erstellt ein Zip-Paket der Diagnoseprotokolle des Ressourcenanbieters und speichert die Datei auf dem Benutzerlaufwerk der Sitzung. Sie können diesen Befehl ohne Parameter ausführen, und die Protokolle der letzten vier Stunden werden gesammelt.
- **Remove-AzsDBAdapterLog**. Dieser Befehl entfernt vorhandene Protokollpakete vom virtuellen Computer des Ressourcenanbieters.

### <a name="endpoint-requirements-and-process"></a>Endpunktanforderungen und Prozess

Beim Installieren oder Aktualisieren eines Ressourcenanbieters wird das **dbadapterdiag**-Benutzerkonto erstellt. Sie werden dieses Konto verwenden, um Diagnoseprotokolle zu sammeln.

>[!NOTE]
>Das Kennwort des Kontos „dbadapterdiag“ entspricht dem Kennwort des lokalen Administrators der VM, die während der Bereitstellung oder dem Update eines Anbieters erstellt wurde.

Zur Verwendung der *DBAdapterDiagnostics*-Befehle müssen Sie eine PowerShell-Remotesitzung mit der VM des Ressourcenanbieters herstellen und den Befehl **Get-AzsDBAdapterLog** ausführen.

Mit den Parametern **FromDate** und **ToDate** legen Sie die Zeitspanne für die Protokollsammlung fest. Wenn Sie einen oder beide dieser Parameter nicht angeben, werden die folgenden Standardwerte verwendet:

- „FromDate“ sind vier Stunden vor der aktuellen Uhrzeit.
- „ToDate“ ist die aktuelle Uhrzeit.

**PowerShell-Skriptbeispiel für die Protokollerfassung**

Das folgende Skript zeigt, wie Diagnoseprotokolle aus der Ressourcenanbieter-VM gesammelt werden.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Clean up the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession
```

::: moniker-end

## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>Konfigurieren der Azure-Diagnoseerweiterung für den SQL-Ressourcenanbieter
Die Azure-Diagnoseerweiterung wird standardmäßig auf dem virtuellen Computer des SQL-Ressourcenanbieteradapters installiert. In den folgenden Schritten wird gezeigt, wie Sie die Erweiterung zum Sammeln von Betriebsereignisprotokollen des SQL-Ressourcenanbieters und von IIS-Protokollen zur Problembehandlung und Überwachung anpassen.

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.

2. Wählen Sie im linken Bereich **Virtuelle Computer** aus, suchen Sie nach dem virtuellen Computer des SQL-Ressourcenanbieteradapters, und wählen Sie ihn aus.

3. Navigieren Sie in den **Diagnoseeinstellungen** des virtuellen Computers zur Registerkarte **Protokolle**, und wählen Sie **Benutzerdefiniert** aus, um die zu erfassenden Ereignisprotokolle anzupassen.
![Navigieren zu den Diagnoseeinstellungen](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. Fügen Sie **Microsoft-AzureStack-DatabaseAdapter/Operational!\** _ hinzu, um Betriebsereignisprotokolle des SQL-Ressourcenanbieters zu sammeln.
![Hinzufügen von Ereignisprotokollen](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. Wenn Sie die Sammlung von IIS-Protokollen aktivieren möchten, aktivieren Sie die Optionen _ *IIS-Protokolle** und **Protokolle zu fehlerhaften Anforderungen**.
![Hinzufügen von IIS-Protokollen](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. Wählen Sie abschließend **Speichern** aus, um alle Diagnoseeinstellungen zu speichern.

Wenn die Sammlung von Ereignisprotokollen und IIS-Protokollen für den SQL-Ressourcenanbieter konfiguriert wurde, finden Sie die Protokolle in einem Systemspeicherkonto mit dem Namen **sqladapterdiagaccount**.

Weitere Informationen zur Azure-Diagnoseerweiterung finden Sie unter [Was ist die Azure-Diagnoseerweiterung?](/azure/azure-monitor/platform/diagnostics-extension-overview).

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von SQL Server-Hostservern](azure-stack-sql-resource-provider-hosting-servers.md)
