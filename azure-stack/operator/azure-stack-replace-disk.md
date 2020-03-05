---
title: Ersetzen eines physischen Datenträgers
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie ein physischer Datenträger in Azure Stack Hub ersetzt wird.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/02/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 8a181823f47d62167ca2e2b033e2cbe0bf258ec0
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697891"
---
# <a name="replace-a-physical-disk-in-azure-stack-hub"></a>Ersetzen eines physischen Datenträgers in Azure Stack Hub

Dieser Artikel beschreibt das allgemeine Verfahren, um einen physischen Datenträger in Azure Stack Hub zu ersetzen. Wenn ein physischer Datenträger ausfällt, sollten Sie ihn so bald wie möglich ersetzen.

> [!Note]  
> Beim Austausch eines physischen Datenlaufwerks muss der Skalierungseinheitenknoten vorab **nicht** in den Wartungsmodus (Ausgleichen) versetzt werden. Auch nach dem Austausch des physischen Laufwerks muss der Skalierungseinheitenknoten nicht über das Azure Stack Hub-Administratorportal repariert werden. Im folgenden Artikel finden Sie weitere Informationen dazu, wann eine Reparatur erforderlich ist: [Ersetzen einer Hardwarekomponente auf einem Azure Stack Hub-Skalierungseinheitenknoten](azure-stack-replace-component.md).

Sie können dieses Verfahren für Bereitstellungen verwenden, die über im laufenden Betrieb austauschbare Datenträger verfügen.

Die tatsächlichen Schritte zum Austausch von Datenträgern variieren und hängen von Ihrem Originalgerätehersteller-Hardwareanbieter (Original Equipment Manufacturer, OEM) ab. Die für Ihr System spezifischen ausführlichen Schritte finden Sie in der FRU-Dokumentation (Field Replaceable Unit) des Herstellers.

## <a name="review-disk-alert-information"></a>Überprüfen von Datenträger-Warnungsinformationen
Wenn ein Datenträger ausfällt, teilt Ihnen eine Warnung mit, dass die Konnektivität mit einem physischen Datenträger verloren gegangen ist.

![Warnung, die in der Azure Stack Hub-Verwaltung den Verlust der Konnektivität mit einem physischen Datenträger anzeigt](media/azure-stack-replace-disk/DiskAlert.png)

Wenn Sie die Warnung öffnen, informiert Sie die Warnungsbeschreibung über den Skalierungseinheitsknoten und das exakte physische Einschubfach für den Datenträger, den Sie ersetzen müssen. Azure Stack Hub hilft Ihnen darüber hinaus, den fehlerhaften Datenträger mithilfe von LED-Anzeigefunktionen zu identifizieren.

## <a name="replace-the-physical-disk"></a>Ersetzen des physischen Datenträgers

Befolgen Sie die FRU-Anweisungen des OEM-Hardwareanbieters beim tatsächlichen Austausch des Datenträgers.

> [!note]
> Ersetzen Sie Datenträger immer nur für einen Skalierungseinheitknoten zurzeit. Warten Sie, bis der Auftrag zur Reparatur des virtuellen Datenträgers abgeschlossen ist, bevor Sie mit dem nächsten Skalierungseinheitsknoten fortfahren.

Um die Verwendung eines nicht unterstützten Datenträgers in einem integrierten System zu verhindern, blockiert das System Datenträger, die von Ihrem Anbieter nicht unterstützt werden. Wenn Sie versuchen, einen nicht unterstützten Datenträger zu verwenden, teilt Ihnen eine neue Warnung mit, dass ein Datenträger aufgrund eines nicht unterstützten Modells oder nicht unterstützter Firmware isoliert wurde.

Nachdem Sie den Datenträger ersetzt haben, ermittelt Azure Stack Hub automatisch den neuen Datenträger und startet die Reparatur des virtuellen Datenträgers.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell"></a>Überprüfen des Status der Reparatur von virtuellen Datenträgern mit Azure Stack Hub PowerShell

Nachdem Sie den Datenträger ausgetauscht haben, können Sie den Integritätsstatus des virtuellen Datenträgers und den Verlauf des Reparaturauftrags mit Azure Stack Hub PowerShell überwachen.

1. Überprüfen Sie, ob Sie Azure Stack Hub PowerShell installiert haben. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md).
2. Stellen Sie als Operator eine Verbindung mit Azure Stack Hub über PowerShell her. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Operator](azure-stack-powershell-configure-admin.md).
3. Führen Sie die folgenden Cmdlets aus, um die Integrität des virtuellen Datenträgers und den Reparaturstatus zu überprüfen:

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Integrität von Azure Stack Hub-Volumes in PowerShell](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Überprüfen Sie den Azure Stack Hub-Systemstatus. Eine Anleitung finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md).
5. Optional können Sie den folgenden Befehl ausführen, um den Status des ausgetauschten physischen Datenträgers zu überprüfen.

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

    Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
    ```

    ![Ersetzte physische Datenträger in Azure Stack Hub mit PowerShell](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

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

4. Überprüfen Sie den Status des Azure Stack Hub-Systems. Eine Anleitung finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md).

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Durchführen der Problembehandlung für die Reparatur des virtuellen Datenträgers über den virtuellen Endpunkt

Wenn der Auftrag zur Reparatur des virtuellen Datenträgers hängen bleibt, führen Sie den folgenden Befehl aus, um den Auftrag neu zu starten:

```powershell
Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
```
