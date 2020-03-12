---
title: Überwachen von Änderungen in Azure Stack Hub mithilfe des privilegierten Endpunkts
description: Erfahren Sie, wie Sie den privilegierten Endpunkt verwenden, um den Updatestatus für in Azure Stack Hub integrierte Systeme zu überwachen.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 943f391d709f772ec3ed5aa0c99bd738f5de679a
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368123"
---
# <a name="monitor-updates-in-azure-stack-hub-using-the-privileged-endpoint"></a>Überwachen von Änderungen in Azure Stack Hub mithilfe des privilegierten Endpunkts

Sie können den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) nutzen, um den Fortschritt einer Updateausführung für Azure Stack Hub zu überwachen. Darüber hinaus können Sie den privilegierten Endpunkt verwenden, um einen nicht erfolgreichen Updatevorgang ab dem letzten erfolgreichen Schritt fortzusetzen, wenn das Azure Stack Hub-Portal nicht mehr verfügbar ist. Das Azure Stack Hub-Portal ist die empfohlene Methode zum Verwalten von Updates in Azure Stack Hub.

Die folgenden neuen PowerShell-Cmdlets für die Updateverwaltung sind im Update 1710 für in Azure Stack Hub integrierte Systeme enthalten.

| Cmdlet  | BESCHREIBUNG  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Gibt den Status des derzeit ausgeführten, abgeschlossenen oder fehlerhaften Updates zurück Stellt den allgemeinen Status des Updatevorgangs sowie ein XML-Dokument bereit, das den aktuellen Schritt und den zugehörigen Status beschreibt. |
| `Resume-AzureStackUpdate` | Nimmt ein fehlerhaftes Update an der Stelle wieder auf, an der der Fehler aufgetreten ist. In bestimmten Szenarien müssen Sie möglicherweise Schritte Risikominderung durchführen, bevor Sie das Update fortsetzen.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Überprüfen der Verfügbarkeit von Cmdlets
Da die Cmdlets im Updatepaket 1710 für Azure Stack Hub neu sind, muss der Updatevorgang für 1710 einen bestimmten Punkt erreichen, bevor die Überwachungsfunktionen zur Verfügung steht. In der Regel sind die Cmdlets verfügbar, wenn der Status im Administratorportal angibt, dass sich das Update 1710 beim Schritt **Speicherhosts neu starten** befindet. Insbesondere kommt das Cmdletupdate vor in **Schritt: Ausführen von Schritt 2.6: Aktualisieren der PrivilegedEndpoint-Whitelist**.

Sie können auch programmgesteuert ermitteln, ob die Cmdlets verfügbar sind, indem Sie die Befehlsliste über den privilegierten Endpunkt abfragen. Führen Sie dazu die folgenden Befehle über den Hardwarelebenszyklushost oder über eine Arbeitsstation mit privilegiertem Zugriff aus. Stellen Sie außerdem sicher, dass es sich bei dem privilegierten Endpunkt um einen vertrauenswürdigen Host handelt. Weitere Informationen finden Sie in Schritt 1 von [Zugreifen auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

1. Erstellen Sie eine PowerShell-Sitzung auf einer der ERCS-VMs in Ihrer Azure Stack Hub-Umgebung (*Präfix*-ERCS01, *Präfix*-ERCS02 oder *Präfix*-ERCS03). Ersetzen Sie *Präfix* durch die genaue VM-Präfixzeichenfolge für Ihre Umgebung.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, verwenden Sie das Konto &lt;*Azure Stack Hub-Domäne*&gt;\cloudadmin oder ein Konto, das Mitglied der Gruppe „CloudAdmins“ ist. Geben Sie für das CloudAdmin-Konto dasselbe Kennwort ein, das während der Installation für das Domänenadministratorkonto AzureStackAdmin bereitgestellt wurde.

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

Führen Sie die folgenden Befehle aus, um eine PowerShell-Sitzung auf einer der ERCS-VMs in Ihrer Azure Stack Hub-Umgebung (*Präfix*-ERCS01, *Präfix*-ERCS02 oder *Präfix*-ERCS03) zu erstellen und eine Sitzungsvariable zuzuweisen.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, verwenden Sie das Konto &lt;*Azure Stack Hub-Domäne*&gt;\cloudadmin oder ein Konto, das Mitglied der Gruppe „CloudAdmins“ ist. Geben Sie für das CloudAdmin-Konto dasselbe Kennwort ein, das während der Installation für das Domänenadministratorkonto AzureStackAdmin bereitgestellt wurde.

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

Sie können diese Befehle mehrfach ausführen, um den aktuellen Status abzurufen. Sie müssen eine Verbindung nicht erneut herstellen, um die Überprüfung zu wiederholen.

### <a name="get-the-full-update-run-status-with-details"></a>Abrufen des vollständigen Ausführungsstatus des Updates mit Details

Sie können die vollständige Zusammenfassung des Updatevorgangs als XML-Zeichenfolge abrufen. Sie können die Zeichenfolge zur Überprüfung in eine Datei schreiben oder als XML-Dokument anlegen, die Sie dann mit PowerShell analysieren. Der folgende Befehl analysiert den XML-Code, um eine hierarchische Liste der aktuell ausgeführten Schritte abzurufen:

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

Der privilegierte Endpunkt ist auf allen ERCS-VMs in der Azure Stack Hub-Umgebung verfügbar. Da die Verbindung nicht an einem Endpunkt mit Hochverfügbarkeit hergestellt wird, können gelegentlich Unterbrechungen, Warnungen oder Fehlermeldungen auftreten. Diese Meldungen weisen möglicherweise darauf hin, dass die Sitzung getrennt wurde oder ein Fehler bei der Kommunikation mit dem ECE-Dienst aufgetreten ist. Dies ist das erwartete Verhalten. Sie können den Vorgang nach einigen Minuten wiederholen oder eine neue Sitzung mit privilegiertem Endpunkt auf einer anderen ERCS-VM erstellen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md)


