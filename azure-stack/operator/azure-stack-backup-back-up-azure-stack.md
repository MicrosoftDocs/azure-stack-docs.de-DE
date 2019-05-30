---
title: Sichern von Azure Stack | Microsoft-Dokumentation
description: Führen Sie eine bedarfsgesteuerte Sicherung von Azure Stack durch, indem Sie den integrierten Sicherungsdienst nutzen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: a572275ff81918d1b8f739a99fbe7a57784d0651
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269016"
---
# <a name="back-up-azure-stack"></a>Sichern von Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Führen Sie eine bedarfsgesteuerte Sicherung von Azure Stack durch. Anweisungen zum Konfigurieren der PowerShell-Umgebung finden Sie unter [Installieren von PowerShell für Azure Stack](azure-stack-powershell-install.md). Informationen zur Anmeldung bei Azure Stack finden Sie unter [Verwenden des Administratorportals in Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Starten der Azure Stack-Sicherung

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Starten einer neuen Sicherung ohne Nachverfolgung des Auftragsstatus
Verwenden Sie „Start-AzSBackup“, um eine neue Sicherung sofort und ohne Nachverfolgung des Auftragsstatus zu starten.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Starten der Azure Stack-Sicherung mit Nachverfolgung des Auftragsstatus
Verwenden Sie „Start-AzSBackup“, um eine neue Sicherung mit dem Parameter **-AsJob** zu starten und als Variable zu speichern, um den Fortschritt des Sicherungsauftrags zu verfolgen.

> [!NOTE]
> Der Sicherungsauftrag wird im Portal etwa 10 bis 15 Minuten vor Abschluss des Auftrags als erfolgreich abgeschlossen angezeigt.
>
> Daher wird der tatsächliche Status besser über den folgenden Code beobachtet.

> [!IMPORTANT]
> Die anfängliche Verzögerung von 1 Millisekunde wird eingeführt, weil der Code zu schnell ist, um den Auftrag ordnungsgemäß zu registrieren, und er kehrt ohne **PSBeginTime** und damit auch ohne **Zustand** des Auftrags zurück.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
```

## <a name="confirm-backup-has-completed"></a>Sicherstellen, dass die Sicherung abgeschlossen wurde

### <a name="confirm-backup-has-completed-using-powershell"></a>Mithilfe von PowerShell sicherstellen, dass die Sicherung abgeschlossen wurde
Verwenden Sie die folgenden PowerShell-Befehle, um sicherzustellen, dass die Sicherung erfolgreich abgeschlossen wurde:

```powershell
   Get-AzsBackup
```

Das Ergebnis sollte der folgenden Ausgabe ähneln:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Im Verwaltungsportal sicherstellen, dass die Sicherung abgeschlossen wurde
Führen Sie im Azure Stack-Verwaltungsportal diese Schritte aus, um sicherzustellen, dass die Sicherung erfolgreich abgeschlossen wurde:

1. Öffnen Sie das [Azure Stack-Verwaltungsportal](azure-stack-manage-portals.md).
2. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Infrastruktursicherung** aus. Wählen Sie auf dem Blatt **Infrastructure Backup** die Option **Konfiguration** aus.
3. Suchen Sie in der Liste **Verfügbare Sicherungen** nach **Name** und **Abschlussdatum** der Sicherung.
4. Überprüfen Sie, ob der **Status** als **Erfolgreich** angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den Workflow zur [Wiederherstellung nach einem Datenverlust](azure-stack-backup-recover-data.md).
