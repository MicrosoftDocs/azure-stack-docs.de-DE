---
title: Sichern von Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine bedarfsgesteuerte Sicherung in Azure Stack durchführen.
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
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2018
ms.openlocfilehash: 5feca4195dca1847f89da1e084b77565f4ad5bd7
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941098"
---
# <a name="back-up-azure-stack"></a>Sichern von Azure Stack

*Anwendungsbereich: Modular Data Center, Azure Stack Hub Ruggedized*

In diesem Artikel wird gezeigt, wie Sie eine bedarfsbasierte Sicherung für Azure Stack erstellen.

## <a name="start-backup"></a>Sicherung starten

Sicherungen werden automatisch gemäß einem Zeitplan erstellt. Das Starten einer bedarfsbasierten Sicherung ist nur erforderlich, wenn eine Benachrichtigung angezeigt wird, die Sie dazu auffordert, eine manuelle Sicherung auszulösen. Klicken Sie auf **Jetzt sichern**, um eine bedarfsbasierte Sicherung zu starten. Eine bedarfsgesteuerte Sicherung ändert die Zeit für die nächste geplante Sicherung nicht. Nach Abschluss des Tasks können Sie die Einstellungen auf dem Blatt **Zusammenfassung** überprüfen:

![Sicherungseinstellungen](media/azure-stack-backup-back-up-azure-stack-tzl/on-demand-backup.png)

## <a name="start-azure-stack-backup"></a>Starten der Azure Stack-Sicherung

Sie können auch das PowerShell-Cmdlet **Start-AzsBackup** auf Ihrem Azure Stack-Verwaltungscomputer ausführen.

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Starten einer neuen Sicherung ohne Nachverfolgung des Auftragsstatus

Verwenden Sie **Start-AzSBackup**, um eine neue Sicherung sofort und ohne Nachverfolgung des Auftragsfortschritts zu starten.

```powershell
Start-AzsBackup -Force
```

## <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Starten der Azure Stack-Sicherung mit Nachverfolgung des Auftragsstatus

Verwenden Sie **Start-AzSBackup**, um eine neue Sicherung mit dem Parameter **-AsJob** zu starten und diesen als Variable zu speichern, um den Fortschritt des Sicherungsauftrags nachzuverfolgen.

> [!NOTE]
> Ihr Sicherungsauftrag wird im Portal etwa 10 bis 15 Minuten vor Abschluss des Auftrags als erfolgreich abgeschlossen angezeigt.
>
> Der tatsächliche Status kann besser über den folgenden Code beobachtet werden.

Die anfängliche Verzögerung von 1 Millisekunde wird eingeführt, da der Code zu schnell ist, um den Auftrag richtig zu registrieren. Der Code meldet keine **PSBeginTime** und daher keinen **Status** für den Auftrag.

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
    $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Mohs>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
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

```shell
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

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>Sicherstellen im Administratorportal, dass die Sicherung abgeschlossen wurde

Führen Sie im Azure Stack-Administratorportal diese Schritte aus, um sicherzustellen, dass die Sicherung erfolgreich abgeschlossen wurde:

1. Öffnen Sie das [Azure Stack-Administratorportal](../../operator/azure-stack-manage-portals.md).

2. Klicken Sie auf **Alle Dienste** und dann in der Kategorie **Verwaltung** auf **Infrastructure Backup**. Wählen Sie auf dem Blatt **Infrastructure Backup** die Option **Konfiguration** aus.

3. Suchen Sie in der Liste **Verfügbare Sicherungen** nach **Name** und **Abschlussdatum** der Sicherung.

4. Überprüfen Sie, ob der **Status** als **Erfolgreich** angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Bewährte Methoden für den Infrastructure Backup-Dienst](azure-stack-backup-best-practices-tzl.md).
