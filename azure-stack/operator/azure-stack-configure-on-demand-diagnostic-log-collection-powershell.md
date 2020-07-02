---
title: Senden von Azure Stack Hub-Diagnoseprotokollen an Azure mithilfe des privilegierten Endpunkts (PEP)
description: Hier erfahren Sie, wie Sie mithilfe des privilegierten Endpunkts (PEP) Azure Stack Hub-Diagnoseprotokolle an Azure senden.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: b2b0fe629d840ca8d5b6353a90f1fcb392a73bd5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85377153"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>Senden von Azure Stack Hub-Diagnoseprotokollen an Azure mithilfe des privilegierten Endpunkts (PEP)

Bei dieser Methode müssen Sie keinen Zielspeicherort angeben. Ohne Angabe der Parameter **fromDate** und **toDate** werden standardmäßig die letzten vier Stunden nach der Ausführung verwendet. 

Sie können optional den Parameter **FilterByRole** oder **FilterByResourceProvider** angeben, um nur Protokolle für bestimmte Rollen oder Ressourcenanbieter einzuschließen, die Mehrwert schaffen. 

Das folgende Beispielskript können Sie mit dem PEP ausführen, um Protokolle in einem integrierten System zu erfassen: 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

## <a name="parameter-considerations"></a>Überlegungen zu Parametern 

* Die Parameter **FromDate** und **ToDate** können zum Sammeln von Protokollen für einen bestimmten Zeitraum verwendet werden. Ohne Angabe dieser Parameter werden standardmäßig Protokolle für die letzten vier Stunden gesammelt.

* Verwenden Sie den Parameter **FilterByNode**, um Protokolle nach Computername zu filtern. Beispiel:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Verwenden Sie den Parameter **FilterByLogType**, um Protokolle nach Typ zu filtern. Sie können nach Datei (File), Freigabe (Share) oder Windows-Ereignis (WindowsEvent) filtern. Beispiel:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Verwenden Sie den Parameter **FilterByResourceProvider**, um Diagnoseprotokolle für Ressourcenanbieter zu senden, die Mehrwert schaffen. Die allgemeine Syntax sieht wie folgt aus:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  So senden Sie Diagnoseprotokolle für IoT Hub: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  So senden Sie Diagnoseprotokolle für Event Hubs:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  So senden Sie Diagnoseprotokolle für Azure Stack Edge:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* Verwenden Sie den Parameter **FilterByRole**, um Diagnoseprotokolle für die Rollen „VirtualMachines“ und „BareMetal“ zu senden:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* So senden Sie Diagnoseprotokolle für die Rollen „VirtualMachines“ und „BareMetal“ mit Datumsfilterung für Protokolldateien der letzten acht Stunden:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* So senden Sie Diagnoseprotokolle für die Rollen „VirtualMachines“ und „BareMetal“ mit Datumsfilterung für Protokolldateien für den Zeitraum zwischen acht und zwei Stunden vor dem aktuellen Zeitpunkt:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```


## <a name="next-steps"></a>Nächste Schritte

[Senden von Azure Stack Hub-Diagnoseprotokollen mithilfe des privilegierten Endpunkts (PEP)](azure-stack-get-azurestacklog.md)
