---
title: Verwalten des SQL-Ressourcenanbieters in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den SQL-Ressourcenanbieterdienst in Azure Stack verwalten können.
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1fa83cbbdba011646016e1e0b0fd5bef3b8d0baf
ms.sourcegitcommit: a8379358f11db1e1097709817d21ded0231503eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70377278"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Wartungsvorgänge von SQL-Ressourcenanbietern

Der SQL-Ressourcenanbieter führt einen gesperrten virtuellen Computer aus. Damit Wartungen durchgeführt werden können, müssen Sie die Sicherheit einer VM aktualisieren. Dies kann nach dem Prinzip der geringsten Berechtigung über den [PowerShell JEA-Endpunkt (Just Enough Administration)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) *DBAdapterMaintenance* erfolgen. Das Ressourcenanbieter-Installationspaket enthält ein Skript für diese Vorgänge.

## <a name="patching-and-updating"></a>Patchen und Aktualisieren

Der SQL-Ressourcenanbieter wird nicht als Teil von Azure Stack gewartet, da es sich um eine Add-On-Komponente handelt. Microsoft stellt bei Bedarf Updates für den SQL-Ressourcenanbieter bereit. Bei der Veröffentlichung eines aktualisierten SQL-Adapters wird ein Skript zum Anwenden des Updates bereitgestellt. Dieses Skript erstellt eine neue Ressourcenanbieter-VM und migriert den Zustand der älteren Anbieter-VM auf die neue VM. Weitere Informationen finden Sie unter [Aktualisieren des SQL-Ressourcenanbieters](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Anbieter-VM

Da der Ressourcenanbieter auf einer *Benutzer*-VM ausgeführt wird, müssen Sie die erforderlichen Patches und Updates installieren, sobald sie veröffentlicht werden. Sie können die Windows Update-Pakete verwenden, die als Teil des Patch- und Updatezyklus bereitgestellt werden, um die VM zu aktualisieren.

## <a name="updating-sql-credentials"></a>Aktualisieren von SQL-Anmeldeinformationen

Sie sind für das Erstellen und Verwalten von Systemadministratorkonten auf Ihren SQL Server-Instanzen verantwortlich. Der Ressourcenanbieter benötigt ein Konto mit diesen Berechtigungen zum Verwalten von Datenbanken für Benutzer – er benötigt keinen Zugriff auf die Daten der Benutzer. Wenn Sie die Systemadministratorkennwörter auf Ihren SQL Server-Instanzen ändern müssen, können Sie die Administratorschnittstelle des Ressourcenanbieters verwenden, um das gespeicherte Kennwort zu ändern. Diese Kennwörter werden in einem Schlüsseltresor in Ihrer Azure Stack-Instanz gespeichert.

Um die Einstellungen zu ändern, wählen Sie **Durchsuchen** &gt; **VERWALTUNGSRESSOURCEN** &gt; **SQL-Hostserver** &gt; **SQL-Anmeldungen**, und wählen Sie einen Benutzernamen aus. Die Änderung muss zuerst auf der SQL-Instanz vorgenommen werden (und ggf. auf allen Replikaten). Wählen Sie unter **Einstellungen** die Option **Kennwort** aus.

![Aktualisieren des Administratorkennworts](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Geheimnisrotation

*Diese Anweisungen gelten nur für integrierte Azure Stack-Systeme.*

Bei Verwendung der SQL- und MySQL-Ressourcenanbieter mit integrierten Azure Stack-Systemen ist der Azure Stack-Operator dafür verantwortlich, die folgenden Geheimnisse der Ressourcenanbieterinfrastruktur zu rotieren, um sicherzustellen, dass sie nicht ablaufen:

- Das [während der Bereitstellung angegebene](azure-stack-pki-certs.md) externe SSL-Zertifikat
- Das während der Bereitstellung angegebene Kennwort des lokalen Administratorkontos für den virtuellen Computer des Ressourcenanbieters
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

**Ändern des Kennworts des lokalen Administratorkontos für den virtuellen Computer**

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
|VMLocalCredential|Lokales Administratorkonto des virtuellen MySQLAdapter-Computers|
|DefaultSSLCertificatePassword|Kennwort für das SSL-Standardzertifikat (*.pfx)|
|DependencyFilesLocalPath|Lokaler Pfad der Abhängigkeitsdateien|
|     |     |

### <a name="known-issues"></a>Bekannte Probleme

**Problem**: Protokolle für Geheimnisrotation.<br>
Die Protokolle für die Geheimnisrotation werden nicht automatisch erfasst, wenn beim Ausführen des benutzerdefinierten Skripts für die Geheimnisrotation Fehler auftreten.

**Problemumgehung**:<br>
Verwenden Sie das Cmdlet Get-AzsDBAdapterLogs, um alle Ressourcenanbieterprotokolle, einschließlich „AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log“, gespeichert unter „C:\Logs“, zu erfassen.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualisieren des Betriebssystems für den virtuellen Computer

Es gibt mehrere Möglichkeiten, das Betriebssystem des virtuellen Computers zu aktualisieren.

- Installieren des aktuellen Ressourcenanbieterpakets über ein gepatchtes Windows Server 2016-Core-Image
- Installieren eines Windows Update-Pakets während der Installation oder des Updates des Ressourcenanbieters

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualisieren der Windows Defender-Definitionen für den virtuellen Computer

Aktualisieren der Windows Defender-Definitionen für den virtuellen Computer

1. Laden Sie das Update der Windows Defender-Definitionen von der Seite [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions) herunter.

   Scrollen Sie auf der Seite für Definitionsupdates bis zu „Manually download and install the definitions“ (Definitionen manuell herunterladen und installieren) herunter. Laden Sie die 64-Bit-Datei „Windows Defender Antivirus for Windows 10 and Windows 8.1“ herunter.

   Alternativ verwenden Sie [diesen Direktlink](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64), um die Datei „fpam-fe.exe“ herunterladen und auszuführen.

2. Erstellen Sie eine PowerShell-Sitzung mit dem VM-Wartungsendpunkt des SQL-Ressourcenanbieteradapters.

3. Kopieren Sie die Updatedatei für die Definitionen auf die VM, indem Sie die Wartungsendpunkt-Sitzung verwenden.

4. Führen Sie in der PowerShell-Wartungssitzung den Befehl *Update-DBAdapterWindowsDefenderDefinitions* aus.

5. Es wird empfohlen, nach der Installation die Updatedatei für die Definitionen mithilfe des Befehls *Remove-ItemOnUserDrive* zu entfernen.

**PowerShell-Skriptbeispiel für das Update von Definitionen**

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

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
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

Zum Erfassen von Protokollen vom gesperrten virtuellen Computer können Sie den PowerShell JEA-Endpunkt (Just Enough Administration) mit dem Namen *DBAdapterDiagnostics* verwenden. Dieser Endpunkt bietet die folgenden Befehle:

- **Get-AzsDBAdapterLog**. Dieser Befehl erstellt ein Zip-Paket der Diagnoseprotokolle des Ressourcenanbieters und speichert die Datei auf dem Benutzerlaufwerk der Sitzung. Sie können diesen Befehl ohne Parameter ausführen, und die Protokolle der letzten vier Stunden werden gesammelt.
- **Remove-AzsDBAdapterLog**. Dieser Befehl entfernt vorhandene Protokollpakete vom virtuellen Computer des Ressourcenanbieters.

### <a name="endpoint-requirements-and-process"></a>Endpunktanforderungen und Prozess

Beim Installieren oder Aktualisieren eines Ressourcenanbieters wird das **dbadapterdiag**-Benutzerkonto erstellt. Sie werden dieses Konto verwenden, um Diagnoseprotokolle zu sammeln.

>[!NOTE]
>Das Kennwort des „dbadapterdiag“-Kontos entspricht dem Kennwort des lokalen Administrators der virtuellen Maschine, das während der Bereitstellung oder dem Update eines Anbieters erstellt wurde.

Zum Nutzen der *DBAdapterDiagnostics*-Befehle müssen Sie eine PowerShell-Remotesitzung mit dem virtuellen Computer des Ressourcenanbieters herstellen und den **Get-AzsDBAdapterLog**-Befehl ausführen.

Mit den Parametern **FromDate** und **ToDate** legen Sie die Zeitspanne für die Protokollsammlung fest. Wenn Sie einen oder beide dieser Parameter nicht angeben, werden die folgenden Standardwerte verwendet:

- „FromDate“ sind vier Stunden vor der aktuellen Uhrzeit.
- „ToDate“ ist die aktuelle Uhrzeit.

**PowerShell-Skriptbeispiel für die Sammlung von Protokollen**

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

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von SQL Server-Hostservern](azure-stack-sql-resource-provider-hosting-servers.md)
