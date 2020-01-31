---
title: Starten und Beenden des ASDK
description: Erfahren Sie, wie Sie das Azure Stack Development Kit (ASDK) starten und beenden.
author: justinha
ms.topic: article
ms.date: 07/18/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 68523f429fb49bfc28151f3d92f3bb3cd64a0b71
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873463"
---
# <a name="start-and-stop-the-asdk"></a>Starten und Beenden des ASDK
Es wird nicht empfohlen, den ASDK-Hostcomputer einfach neu zu starten. Befolgen Sie stattdessen die Verfahren in diesem Artikel, um ASDK-Dienste ordnungsgemäß herunterzufahren und neu zu starten.

## <a name="stop-azure-stack"></a>Beenden von Azure Stack 
Um Azure Stack-Dienste und den ASDK-Hostcomputer ordnungsgemäß herunterzufahren, verwenden Sie die folgenden PowerShell-Befehle:

1. Melden Sie sich auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ an.
2. Öffnen Sie PowerShell als Administrator (nicht PowerShell ISE).
3. Führen Sie die folgenden Befehle aus, um eine privilegierte Endpunktsitzung (PEP) einzurichten: 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Verwenden Sie anschließend in der PEP-Sitzung das Cmdlet **Stop-AzureStack**, um die Azure Stack-Dienste zu beenden und den ASDK-Hostcomputer herunterzufahren:

   ```powershell
   Stop-AzureStack
   ```
5. Überprüfen Sie die PowerShell-Ausgabe, um sicherzustellen, dass alle Azure Stack-Dienste erfolgreich heruntergefahren wurden, bevor der ASDK-Hostcomputer heruntergefahren wird. Das Herunterfahren dauert einige Minuten.

## <a name="start-azure-stack"></a>Starten von Azure Stack 
ASDK-Dienste sollten automatisch starten, wenn der Hostcomputer gestartet wird. Die Startzeit der ASDK-Infrastrukturdienste hängt jedoch von der Leistung der Hardwarekonfiguration des ASDK-Hostcomputers ab. Mitunter kann es mehrere Stunden dauern, bis alle Dienste erfolgreich neu gestartet werden.

Unabhängig davon, wie das ASDK heruntergefahren wurde, sollten Sie die folgenden Schritte durchführen, um sicherzustellen, dass alle Azure Stack-Dienste nach Einschalten des Hostcomputers gestartet und voll funktionsfähig sind: 

1. Schalten Sie den ASDK-Hostcomputer ein. 
2. Melden Sie sich auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ an.
3. Öffnen Sie PowerShell als Administrator (nicht PowerShell ISE).
4. Führen Sie die folgenden Befehle aus, um eine privilegierte Endpunktsitzung (PEP) einzurichten:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Führen Sie anschließend in der PEP-Sitzung die folgenden Befehle aus, um den Startstatus der Azure Stack-Dienste zu überprüfen:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Überprüfen Sie die Ausgabe, um sicherzustellen, dass die Azure Stack-Dienste erfolgreich neu gestartet wurden.

Weitere Informationen zu den empfohlenen Verfahren zum ordnungsgemäßen Herunterfahren und Neustarten von Azure Stack-Diensten finden Sie unter [Starten und Beenden von Azure Stack](../operator/azure-stack-start-and-stop.md).

## <a name="troubleshoot-startup-and-shutdown"></a>Behandeln von Problemen beim Starten und Herunterfahren 
Führen Sie diese Schritte aus, wenn die Azure Stack-Dienste nicht innerhalb von zwei Stunden nach Einschalten des ASDK-Hostcomputers erfolgreich gestartet werden:

1. Melden Sie sich auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ an.
2. Öffnen Sie PowerShell als Administrator (nicht PowerShell ISE).
3. Führen Sie die folgenden Befehle aus, um eine privilegierte Endpunktsitzung (PEP) einzurichten:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Führen Sie anschließend in der PEP-Sitzung die folgenden Befehle aus, um den Startstatus der Azure Stack-Dienste zu überprüfen:

   ```powershell
   Test-AzureStack
   ```
5. Überprüfen Sie die Ausgabe, und lösen Sie etwaige Probleme. Weitere Informationen finden Sie unter [Ausführen eines Validierungstests für Azure Stack](../operator/azure-stack-diagnostic-test.md).
6. Starten Sie die Azure Stack-Dienste innerhalb der PEP-Sitzung neu, indem Sie das Cmdlet **Start-AzureStack** ausführen:

   ```powershell
   Start-AzureStack
   ```

Wenn die Ausführung von **Start-AzureStack** zu einem Fehler führt, besuchen Sie das [Azure Stack Support-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack), um Unterstützung bei der ASDK-Problembehandlung zu erhalten. 

## <a name="next-steps"></a>Nächste Schritte 
Weitere Informationen zu Azure Stack-Diagnosetools und zur Protokollierung von Problemen finden Sie unter [Azure Stack-Diagnosetools](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
