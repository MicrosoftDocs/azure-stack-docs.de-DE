---
title: Ersetzen eines physischen Datenträgers in Azure Stack | Microsoft-Dokumentation
description: Skizziert das Ersetzen eines physischen Datenträgers in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 5d0c3bdff2684c90f118e26ac62b8219802fa25b
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008649"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Ersetzen eines physischen Datenträgers in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Dieser Artikel beschreibt das allgemeine Verfahren, um einen physischen Datenträger in Azure Stack zu ersetzen. Wenn ein physischer Datenträger ausfällt, sollten Sie ihn so bald wie möglich ersetzen.

Sie können dieses Verfahren für integrierte Systeme verwenden, und für Development Kit-Bereitstellungen, die über im laufenden Betrieb austauschbare Datenträger verfügen.

Die tatsächlichen Schritte zum Austausch von Datenträgern variieren und hängen von Ihrem Originalgerätehersteller-Hardwareanbieter (Original Equipment Manufacturer, OEM) ab. Die für Ihr System spezifischen ausführlichen Schritte finden Sie in der FRU-Dokumentation (Field Replaceable Unit) des Herstellers.

## <a name="review-disk-alert-information"></a>Überprüfen von Datenträger-Warnungsinformationen
Wenn ein Datenträger ausfällt, teilt Ihnen eine Warnung mit, dass die Konnektivität mit einem physischen Datenträger verloren gegangen ist.

![Warnung, die den Verlust der Konnektivität mit einem physischen Datenträger anzeigt](media/azure-stack-replace-disk/DiskAlert.png)

Wenn Sie die Warnung öffnen, informiert Sie die Warnungsbeschreibung über den Skalierungseinheitsknoten und das exakte physische Einschubfach für den Datenträger, den Sie ersetzen müssen. Azure Stack hilft Ihnen darüber hinaus, den fehlerhaften Datenträger mithilfe von LED-Anzeigefunktionen zu identifizieren.

## <a name="replace-the-disk"></a>Austausch des Datenträgers

Befolgen Sie die FRU-Anweisungen des OEM-Hardwareanbieters beim tatsächlichen Austausch des Datenträgers.

> [!note]
> Ersetzen Sie Datenträger immer nur für einen Skalierungseinheitknoten zurzeit. Warten Sie, bis der Auftrag zur Reparatur des virtuellen Datenträgers abgeschlossen ist, bevor Sie mit dem nächsten Skalierungseinheitknoten fortfahren.

Um die Verwendung eines nicht unterstützten Datenträgers in einem integrierten System zu verhindern, blockiert das System Datenträger, die von Ihrem Anbieter nicht unterstützt werden. Wenn Sie versuchen, einen nicht unterstützten Datenträger zu verwenden, teilt Ihnen eine neue Warnung mit, dass ein Datenträger aufgrund eines nicht unterstützten Modells oder nicht unterstützter Firmware isoliert wurde.

Nachdem Sie den Datenträger ersetzt haben, ermittelt Azure Stack automatisch den neuen Datenträger und startet die Reparatur des virtuellen Datenträgers.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>Überprüfen des Status der Reparatur von virtuellen Datenträgern mit Azure Stack PowerShell

Nachdem Sie den Datenträger ausgetauscht haben, können Sie den Integritätsstatus des virtuellen Datenträgers und den Verlauf des Reparaturauftrags mit Azure Stack PowerShell überwachen.

1. Überprüfen Sie, ob Sie Azure Stack PowerShell installiert haben. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack](azure-stack-powershell-install.md).
2. Stellen Sie als Operator eine Verbindung mit Azure Stack über PowerShell her. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Stack über PowerShell als Operator](azure-stack-powershell-configure-admin.md).
3. Führen Sie die folgenden Cmdlets aus, um die Integrität des virtuellen Datenträgers und den Reparaturstatus zu überprüfen:
    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Integrität von Azure Stack-Volumes](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Überprüfen Sie den Status des Azure Stack-Systems. Eine Anleitung finden Sie unter [Überprüfen des Azure Stack-Systemstatus](azure-stack-diagnostic-test.md).
5. Optional können Sie den folgenden Befehl ausführen, um den Status des ausgetauschten physischen Datenträgers zu überprüfen.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Ausgetauschter physischer Datenträger in Azure Stack](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>Überprüfen des Reparaturstatus für den virtuellen Datenträger über den privilegierten Endpunkt
 
Nachdem Sie den Datenträger ersetzt haben, können Sie den Integritätsstatus des virtuellen Datenträgers und den Verlauf des Reparaturauftrags mithilfe des privilegierten Endpunkts überwachen. Führen Sie diese Schritte auf einem beliebigen Computer aus, der über Netzwerkkonnektivität mit dem privilegierten Endpunkt verfügt.

1. Öffnen Sie eine Windows PowerShell-Sitzung, und stellen Sie eine Verbindung mit dem privilegierten Endpunkt her.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Führen Sie den folgenden Befehl zum Anzeigen der Integrität des virtuellen Datenträgers aus:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![PowerShell-Ausgabe des Get-VirtualDisk-Befehls](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Führen Sie den folgenden Befehl zum Anzeigen des aktuellen Speicherauftragsstatus aus:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![PowerShell-Ausgabe des Get-StorageJob-Befehls](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Überprüfen Sie den Status des Azure Stack-Systems. Eine Anleitung finden Sie unter [Überprüfen des Azure Stack-Systemstatus](azure-stack-diagnostic-test.md).


## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Durchführen der Problembehandlung für die Reparatur des virtuellen Datenträgers über den virtuellen Endpunkt

Wenn der Auftrag zur Reparatur des virtuellen Datenträgers hängen bleibt, führen Sie den folgenden Befehl aus, um den Auftrag neu zu starten:
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
