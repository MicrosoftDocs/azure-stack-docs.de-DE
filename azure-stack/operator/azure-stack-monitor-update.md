---
title: Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den privilegierten Endpunkt verwenden, um den Updatestatus für in Azure Stack integrierte Systeme zu überwachen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 7cfbba830b91d5dba8935cce20a2cdc0e65e49de
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909159"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Sie können mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) den Status eines Azure Stack-Updatevorgangs überwachen und ein fehlerhaftes Update vom letzten erfolgreichen Schritt aus fortsetzen, falls das Azure Stack-Portal nicht verfügbar sein sollte.  Das Azure Stack-Portal ist die empfohlene Methode zum Verwalten von Updates in Azure Stack.

Die folgenden neuen PowerShell-Cmdlets für die Updateverwaltung sind im Update 1710 für in Azure Stack integrierte Systeme enthalten.

| Cmdlet  | BESCHREIBUNG  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Gibt den Status des derzeit ausgeführten, abgeschlossenen oder fehlerhaften Updates zurück Stellt den allgemeinen Status des Updatevorgangs sowie ein XML-Dokument, das den aktuellen Schritt und den zugehörigen Status beschreibt, bereit |
| `Resume-AzureStackUpdate` | Nimmt ein fehlerhaftes Update an der Stelle wieder auf, an der der Fehler aufgetreten ist. In bestimmten Szenarien müssen Sie möglicherweise Schritte Risikominderung durchführen, bevor Sie das Update fortsetzen.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Überprüfen der Verfügbarkeit von Cmdlets
Da die Cmdlets im Updatepaket 1710 für Azure Stack neu sind, muss der Updatevorgang für 1710 einen bestimmten Punkt erreichen, bevor die Überwachungsfunktionen zur Verfügung steht. In der Regel sind die Cmdlets verfügbar, wenn der Status im Administratorportal angibt, dass sich das Update 1710 beim Schritt **Speicherhosts neu starten** befindet. Insbesondere kommt das Cmdletupdate vor in **Schritt: Ausführen von Schritt 2.6: Aktualisieren der PrivilegedEndpoint-Whitelist**.

Sie können auch programmgesteuert ermitteln, ob die Cmdlets verfügbar sind, indem Sie die Befehlsliste vom privilegierten Endpunkt aus abfragen. Führen Sie dazu die folgenden Befehle über den Hardwarelebenszyklushost oder über eine Arbeitsstation für privilegierten Zugriff aus. Stellen Sie außerdem sicher, dass es sich bei dem privilegierten Endpunkt um einen vertrauenswürdigen Host handelt. Weitere Informationen finden Sie in Schritt 1 von [Zugreifen auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Erstellen Sie eine PowerShell-Sitzung auf einem der virtuellen ERCS-Computer in Ihrer Azure Stack-Umgebung (*Präfix*-ERCS01, *Präfix*-ERCS02 oder *Präfix*-ERCS03). Ersetzen Sie *Präfix* durch die spezifische Präfixzeichenfolge für virtuelle Computer in Ihrer Umgebung.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, verwenden Sie das Konto &lt;*Azure Stack-Domäne*&gt;\cloudadmin oder ein Konto, das Mitglied der Gruppe CloudAdmins ist. Geben Sie für das CloudAdmin-Konto dasselbe Kennwort ein, das während der Installation für das Domänenadministratorkonto AzureStackAdmin bereitgestellt wurde.

2. Rufen Sie die vollständige Liste der Befehle ab, die im privilegierten Endpunkt verfügbar sind. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Ermitteln Sie, ob der privilegierte Endpunkt aktualisiert wurde.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Listen Sie die spezifischen Befehle des Moduls Microsoft.AzureStack.UpdateManagement auf.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Beispiel:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Verwenden der Cmdlets zur Updateverwaltung

> [!NOTE]
> Führen Sie die folgenden Befehle über den Hardwarelebenszyklushost oder über eine Arbeitsstation für privilegierten Zugriff aus. Stellen Sie außerdem sicher, dass es sich bei dem privilegierten Endpunkt um einen vertrauenswürdigen Host handelt. Weitere Informationen finden Sie in Schritt 1 von [Zugreifen auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Herstellen einer Verbindung mit dem privilegierten Endpunkt und Zuweisen einer Sitzungsvariable

Führen Sie die folgenden Befehle aus, um eine PowerShell-Sitzung auf einem der virtuellen ERCS-Computer in Ihrer Azure Stack-Umgebung (*Präfix*-ERCS01, *Präfix*-ERCS02 oder *Präfix*-ERCS03) zu erstellen und eine Sitzungsvariable zuzuweisen.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, verwenden Sie das Konto &lt;*Azure Stack-Domäne*&gt;\cloudadmin oder ein Konto, das Mitglied der Gruppe CloudAdmins ist. Geben Sie für das CloudAdmin-Konto dasselbe Kennwort ein, das während der Installation für das Domänenadministratorkonto AzureStackAdmin bereitgestellt wurde.

### <a name="get-high-level-status-of-the-current-update-run"></a>Abrufen des allgemeinen Status des aktuellen Updatevorgangs 

Um den allgemeinen Status des aktuellen Updatevorgangs zu erhalten, führen Sie die folgenden Befehle aus: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Mögliche Werte sind:

- Wird ausgeführt
- Abgeschlossen
- Fehler 
- Canceled

Sie können diese Befehle wiederholt ausführen, um den aktuellen Status abzurufen. Sie müssen eine Verbindung nicht erneut herstellen, um die Überprüfung zu wiederholen.

### <a name="get-the-full-update-run-status-with-details"></a>Abrufen des vollständigen Ausführungsstatus des Updates mit Details 

Sie können die vollständige Zusammenfassung des Updatevorgangs als XML-Zeichenfolge abrufen. Sie können die Zeichenfolge zur Überprüfung in eine Datei schreiben oder als XML-Dokument anlegen, die Sie dann mit PowerShell analysieren. Der folgende Befehl analysiert den XML-Code, um eine hierarchische Liste der aktuell ausgeführten Schritte abzurufen.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

Im folgenden Beispiel besteht im obersten Schritt (Cloudupdate) ein untergeordneter Plan, die Speicherhosts zu aktualisieren und neu zu starten. Im Beispiel wird gezeigt, dass der Plan zum Aktualisieren der Speicherhosts den Blob Storage-Dienst auf einem der Hosts aktualisiert.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>Fortsetzen eines fehlerhaften Updatevorgangs

Wenn beim Update ein Fehler auftritt, können Sie den Updatevorgang an der Stelle, an der er abgebrochen wurde, wieder aufnehmen.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Problembehandlung

Der privilegierte Endpunkt ist auf allen virtuellen ERCS-Computern in der Azure Stack-Umgebung verfügbar. Da die Verbindung nicht an einem Endpunkt mit hoher Verfügbarkeit hergestellt wird, können gelegentlich Unterbrechungen, Warnungen oder Fehlermeldungen auftreten. Diese Meldungen weisen möglicherweise darauf hin, dass die Sitzung getrennt wurde oder ein Fehler bei der Kommunikation mit dem ECE-Dienst aufgetreten ist. Dies ist das erwartete Verhalten. Sie können den Vorgang nach einigen Minuten wiederholen oder eine neue Sitzung mit privilegiertem Endpunkt auf einem anderen virtuellen ERCS-Computer erstellen. 

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack](azure-stack-updates.md) 


