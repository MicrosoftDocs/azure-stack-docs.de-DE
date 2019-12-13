---
title: Wartungsvorgänge von SQL-Ressourcenanbietern
titleSuffix: Azure Stack
description: Hier finden Sie Informationen zu Wartungsvorgängen für den SQL-Ressourcenanbieter in Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 8d8464c35b2aaa48c5611f7eac84ed6f9d80e866
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954501"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Wartungsvorgänge von SQL-Ressourcenanbietern

Der SQL-Ressourcenanbieter wird auf einem gesperrten virtuellen Computer (virtual machine, VM) ausgeführt. Damit Wartungsvorgänge durchgeführt werden können, müssen Sie die Sicherheit der VM aktualisieren. Verwenden Sie hierzu den Endpunkt *DBAdapterMaintenance* mit [PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview), um dem Prinzip der geringsten Rechte zu entsprechen. Das Ressourcenanbieter-Installationspaket enthält ein Skript für diese Aktion.

## <a name="patching-and-updating"></a>Patchen und Aktualisieren

Der SQL-Ressourcenanbieter wird nicht als Teil von Azure Stack gewartet, da es sich um eine Add-On-Komponente handelt. Microsoft stellt bei Bedarf Updates für den SQL-Ressourcenanbieter bereit. Bei der Veröffentlichung eines aktualisierten SQL-Adapters wird ein Skript zum Anwenden des Updates bereitgestellt. Dieses Skript erstellt eine neue Ressourcenanbieter-VM und migriert den Zustand der älteren Anbieter-VM auf die neue VM. Weitere Informationen finden Sie unter [Aktualisieren des SQL-Ressourcenanbieters](azure-stack-sql-resource-provider-update.md).

### <a name="provider-vm"></a>Anbieter-VM

Da der Ressourcenanbieter auf einer *Benutzer*-VM ausgeführt wird, müssen Sie die erforderlichen Patches und Updates installieren, sobald sie veröffentlicht werden. Verwenden Sie die Windows Update-Pakete, die im Rahmen des Patch- und Updatezyklus bereitgestellt werden, um den virtuellen Computer zu aktualisieren.

## <a name="updating-sql-credentials"></a>Aktualisieren von SQL-Anmeldeinformationen

Sie sind für das Erstellen und Verwalten von Systemadministratorkonten auf Ihren SQL Server-Instanzen verantwortlich. Der Ressourcenanbieter benötigt ein Konto mit diesen Berechtigungen zum Verwalten von Datenbanken für Benutzer – er benötigt keinen Zugriff auf die Daten der Benutzer. Wenn Sie die Systemadministratorkennwörter auf Ihren SQL Server-Instanzen ändern müssen, können Sie die Administratorschnittstelle des Ressourcenanbieters verwenden, um das gespeicherte Kennwort zu ändern. Diese Kennwörter werden in einem Schlüsseltresor in Ihrer Azure Stack-Instanz gespeichert.

Um die Einstellungen zu ändern, wählen Sie **Durchsuchen** &gt; **VERWALTUNGSRESSOURCEN** &gt; **SQL-Hostserver** &gt; **SQL-Anmeldungen**, und wählen Sie einen Benutzernamen aus. Die Änderung muss zuerst auf der SQL-Instanz vorgenommen werden (und ggf. auf allen Replikaten). Wählen Sie unter **Einstellungen** die Option **Kennwort** aus.

![Aktualisieren des SQL-Administratorkennworts](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Geheimnisrotation

*Diese Anweisungen gelten nur für integrierte Azure Stack-Systeme.*

Bei Verwendung der SQL- und MySQL-Ressourcenanbieter mit integrierten Azure Stack-Systemen ist der Azure Stack-Operator dafür verantwortlich, die folgenden Geheimnisse der Ressourcenanbieterinfrastruktur zu rotieren, um sicherzustellen, dass sie nicht ablaufen:

- Das [während der Bereitstellung angegebene](azure-stack-pki-certs.md) externe SSL-Zertifikat
- Das während der Bereitstellung angegebene Kennwort des lokalen Administratorkontos der Ressourcenanbieter-VM
- Das Kennwort des Diagnosebenutzers (dbadapterdiag) für den Ressourcenanbieter

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
    -VMLocalCredential $localCreds
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

### <a name="secretrotationsqlproviderps1-parameters"></a>Parameter für „SecretRotationSQLProvider.ps1“

|Parameter|BESCHREIBUNG|
|-----|-----|
|AzCredential|Anmeldeinformationen für das Azure Stack-Dienstadministratorkonto|
|CloudAdminCredential|Anmeldeinformationen für das Domänenkonto des Azure Stack-Cloudadministrators|
|PrivilegedEndpoint|Privilegierter Endpunkt für den Zugriff auf „Get-AzureStackStampInformation“|
|DiagnosticsUserPassword|Kennwort des Diagnosebenutzerkontos|
|VMLocalCredential|Lokales Administratorkonto des virtuellen Computers „MySQLAdapter“|
|DefaultSSLCertificatePassword|Kennwort für das SSL-Standardzertifikat (PFX-Datei)|
|DependencyFilesLocalPath|Lokaler Pfad der Abhängigkeitsdateien|
|     |     |

### <a name="known-issues"></a>Bekannte Probleme

**Problem**:<br>
Protokolle für Geheimnisrotation. Die Protokolle für die Geheimnisrotation werden nicht automatisch erfasst, wenn beim Ausführen des benutzerdefinierten Skripts für die Geheimnisrotation ein Fehler auftritt.

**Problemumgehung**:<br>
Verwenden Sie das Cmdlet Get-AzsDBAdapterLogs, um alle Ressourcenanbieterprotokolle, einschließlich „AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log“, gespeichert unter „C:\Logs“, zu erfassen.

## <a name="update-the-vm-operating-system"></a>Aktualisieren des VM-Betriebssystems

Verwenden Sie eine der folgenden Methoden, um das Betriebssystem des virtuellen Computers zu aktualisieren:

- Installieren des aktuellen Ressourcenanbieterpakets über ein gepatchtes Windows Server 2016-Core-Image
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

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von SQL Server-Hostservern](azure-stack-sql-resource-provider-hosting-servers.md)
