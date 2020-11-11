---
title: Aktivieren der Sicherung für Azure Stack mit PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Infrastructure Backup-Dienst mit PowerShell aktivieren, sodass Azure Stack bei einem Fehler wiederhergestellt werden kann.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: 24ffc027ffbcaaf3b04c806af76e9010f0982386
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330223"
---
# <a name="configure-backup-for-azure-stack-with-powershell"></a>Konfigurieren der Sicherung für Azure Stack mit PowerShell

*Anwendungsbereich: Modular Data Center, Azure Stack Hub Ruggedized*

Sie können den Infrastructure Backup-Dienst so konfigurieren, dass Infrastruktursicherungen an einen externen Speicherort exportiert werden. Dazu wird PowerShell genutzt. Infrastruktursicherungen können vom Support verwendet werden, um Probleme bei beeinträchtigten Diensten zu beheben.

## <a name="prepare-powershell-environment"></a>Vorbereiten der PowerShell-Umgebung

Anweisungen zum Konfigurieren der PowerShell-Umgebung finden Sie unter [Installieren von PowerShell für Azure Stack](../../operator/azure-stack-powershell-install.md). Weitere Informationen zur Anmeldung bei Azure Stack finden Sie unter [Konfigurieren der Betreiberumgebung und Anmelden bei Azure Stack](../../operator/azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Angeben der Sicherungsfreigabe, der Anmeldeinformationen und des Verschlüsselungsschlüssels zum Aktivieren der Sicherung

Bearbeiten Sie in der gleichen PowerShell-Sitzung das folgende PowerShell-Skript durch Hinzufügen der Variablen für Ihre Umgebung. Führen Sie das aktualisierte Skript aus, um die Sicherungsfreigabe, die Anmeldeinformationen und den Verschlüsselungsschlüssel für den Infrastructure Backup-Dienst anzugeben.

|Variable  |BESCHREIBUNG  |
|---------|---------|
|$username     | Geben Sie den **Benutzernamen** ein, und verwenden Sie dabei die Domäne und den Benutzernamen für den Speicherort des freigegebenen Laufwerks mit ausreichendem Zugriff zum Lesen und Schreiben von Dateien. Beispiel: Contoso\\backupshareuser.        |
|$password     | Geben Sie das **Kennwort** für den Benutzer ein.        |
|$path     | Geben Sie den Pfad zu dem **Sicherungsspeicherort** ein. Für den Pfad zu einer auf einem separaten Gerät gehosteten Dateifreigabe müssen Sie eine UNC-Zeichenfolge (Universal Naming Convention) verwenden. Eine UNC-Zeichenfolge gibt den Speicherort von Ressourcen (z.B. freigegebene Dateien oder Geräte) an. Zur Sicherstellung der Verfügbarkeit der Sicherungsdaten sollte sich das Gerät an einem gesonderten Speicherort befinden.        |
|$backupfrequencyinhours     | Die Häufigkeit in Stunden bestimmt, wie oft Sicherungen erstellt werden. Der Standardwert ist 12. Scheduler unterstützt mindestens 4 und maximal 12.        |
|$backupretentionperiodindays     | Die Vermerkdauer in Tagen bestimmt, wie viele Tage Sicherungen am externen Speicherort beibehalten werden. Der Standardwert ist 7. Scheduler unterstützt mindestens 2 und maximal 14. Nach Ablauf ihrer Vermerkdauer werden Sicherungen automatisch aus dem externen Speicherort gelöscht.        |
|$encryptioncertpath     | Hierbei handelt es sich um das während der Bereitstellung bereitgestellte Zertifikat. Sie müssen kein neues Zertifikat angeben, wenn Sie den externen Speicherort konfigurieren. Der Verschlüsselungszertifikatpfad gibt den Dateipfad zu der CER-Datei mit dem öffentlichen Schlüssel an, die für die Datenverschlüsselung verwendet wird.        |
|$isbackupschedulerenabled     | Hiermit werden automatische Sicherungen aktiviert/deaktiviert. Automatische Sicherungen sind standardmäßig aktiviert, nachdem die Freigabe und Anmeldeinformationen bereitgestellt wurden.        |

## <a name="configure-backup"></a>Konfigurieren der Sicherung

```powershell
# Example username
$username = "domain\backupadmin"

# Example share path
$sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

$password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

# Set the backup settings with the name, password, share, and CER certificate file.
Set-AzsBackupConfiguration -BackupShare path -Username $username -Password $password
```

## <a name="confirm-backup-settings"></a>Überprüfen der Sicherungseinstellungen

Führen Sie in der gleichen PowerShell-Sitzung die folgenden Befehle aus:

```powershell
Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
```

Das Ergebnis sollte der folgenden Beispielausgabe ähneln:

```shell
Path : \\serverIP\AzsBackupStore\contoso.com\seattle
UserName : domain\backupadmin
```

## <a name="update-backup-settings"></a>Aktualisieren der Sicherungseinstellungen

In der gleichen PowerShell-Sitzung können Sie die Standardwerte für den Aufbewahrungszeitraum und die Häufigkeit von Sicherungen aktualisieren:

```powershell
# Set the backup frequency and retention period values.
$frequencyInHours = 10
$retentionPeriodInDays = 5

Set-AzsBackupConfiguration -BackupFrequencyInHours $backupfrequencyInHours -BackupRetentionPeriodInDays $backupretentionPeriodInDays

Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
```

## <a name="update-encryption-certificate"></a>Aktualisieren des Verschlüsselungszertifikats

In der gleichen PowerShell-Sitzung können Sie das Verschlüsselungszertifikat aktualisieren, das zum Verschlüsseln der Sicherungsdaten genutzt wird. Nur für neue Sicherungen wird das neue Verschlüsselungszertifikat verwendet. Alle vorhandenen Sicherungen bleiben mit dem vorherigen Zertifikat verschlüsselt. Denken Sie daran, eine Kopie des vorherigen Zertifikats mindestens einen Monat lang aufzubewahren, um sicherzustellen, dass mit dem alten Zertifikat verschlüsselte Sicherungen endgültig gelöscht werden:

```powershell
#Set the new encryption certificate by providing local path to CER file.
Set-AzsBackupConfiguration -EncryptionCertPath "c:\temp\cert.cer"
```

## <a name="enable-or-disable-automatic-backups"></a>Aktivieren oder Deaktivieren von automatischen Sicherungen

In der gleichen PowerShell-Sitzung können Sie automatische Sicherungen aktivieren oder deaktivieren. Automatische Sicherungen sind standardmäßig aktiviert.

```powershell
#Disable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $false

#Enable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $true
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Überprüfen, ob eine Sicherung durchgeführt wurde, finden Sie unter [Sichern von Azure Stack Hub](../../operator/azure-stack-backup-back-up-azure-stack.md).
