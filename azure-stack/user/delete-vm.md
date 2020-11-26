---
title: Löschen einer VM mit Abhängigkeiten in Azure Stack Hub
description: 'Gewusst wie: Löschen einer VM (virtueller Computer) mit Abhängigkeiten auf Azure Stack Hub'
author: mattbriggs
ms.topic: how-to
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: f9e32351dbc73b42e51c485c8e2eb39d4226ea27
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518210"
---
# <a name="how-to-delete-a-vm-virtual-machine-with-dependencies-on-azure-stack-hub"></a>Gewusst wie: Löschen einer VM (virtueller Computer) mit Abhängigkeiten auf Azure Stack Hub

In diesem Artikel sind die Schritte zum Entfernen einer VM und ihrer Abhängigkeiten in Azure Stack Hub beschrieben.

Wenn Sie eine VM von Azure Stack Hub entfernen, verbleiben die Komponentenabhängigkeiten, d. h. Datenträger für Daten, virtuelle Netzwerkschnittstellen und Diagnosecontainer in der Ressourcengruppe. Diese Elemente werden nicht automatisch zusammen mit Ihrem Betriebssystemdatenträger gelöscht.

## <a name="delete-a-vm-overview"></a>Löschen einer VM: Übersicht

Wenn Sie eine neue VM erstellen, erstellen Sie in der Regel eine neue Ressourcengruppe und platzieren alle Abhängigkeiten in dieser Ressourcengruppe. Wenn Sie die VM und alle ihre Abhängigkeiten löschen möchten, können Sie die Ressourcengruppe löschen. Der Azure Resource Manager nimmt sich der Abhängigkeiten an, um sie erfolgreich zu löschen. Es gibt aber Situationen, in denen Sie die Ressourcengruppe nicht löschen können, um die VM zu entfernen. Beispielsweise kann die VM Ressourcen enthalten, die keine Abhängigkeiten der VM sind und die Sie behalten möchten.

## <a name="delete-a-vm-with-dependencies"></a>Löschen eines virtuellen Computers mit Abhängigkeiten

### <a name="with-the-portal"></a>[Im Portal](#tab/portal)

Falls Sie die Ressourcengruppe nicht löschen können, befinden sich entweder die Abhängigkeiten nicht in der gleichen Ressourcengruppe, oder es gibt andere Ressourcen. Führen Sie die Schritte unten aus:

1. Öffnen Sie das Azure Stack-Benutzerportal.

2. Wählen Sie **Virtuelle Computer** aus. Suchen Sie Ihren virtuellen Computer, und wählen Sie dann Ihren Computer aus, um das Blatt „Virtueller Computer“ zu öffnen.  
![Löschen einer VM mit Abhängigkeiten](./media/delete-vm/azure-stack-hub-delete-vm-portal.png)  

3. Notieren Sie die Ressourcengruppe, in der die VM und die VM-Abhängigkeiten enthalten sind.

4. Wählen Sie **Netzwerk** aus, und notieren Sie die Netzwerkschnittstelle.

5. Wählen Sie **Datenträger** aus, und notieren Sie den Betriebssystemdatenträger und die Datenträger für Daten.

6. Kehren Sie zum Blatt **Virtueller Computer** zurück, und wählen Sie **Löschen** aus.

7. Geben Sie `yes` ein, um das Löschen zu bestätigen, und wählen Sie **Löschen** aus.

7. Wählen Sie **Ressourcengruppen** und dann die Ressourcengruppe aus.

8. Löschen Sie die Abhängigkeiten, indem Sie sie manuell auswählen und dann **Löschen** auswählen.
    1. Geben Sie `yes` ein, um das Löschen zu bestätigen, und wählen Sie **Löschen** aus.
    2. Warten Sie, bis die Ressource vollständig gelöscht ist.
    3. Anschließend können Sie die nächste Abhängigkeit löschen.

### <a name="with-powershell"></a>[Mit PowerShell](#tab/ps-az)

Falls Sie die Ressourcengruppe nicht löschen können, befinden sich entweder die Abhängigkeiten nicht in der gleichen Ressourcengruppe, oder es gibt andere Ressourcen. Führen Sie diese Schritte aus:

Stellen Sie eine Verbindung mit Ihrer Azure Stack Hub-Umgebung her, und aktualisieren Sie dann die folgenden Variablen mit dem Namen Ihrer VM und Ressourcengruppe. Anweisungen zum Verbinden Ihrer PowerShell-Sitzung mit Azure Stack Hub finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Benutzer](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzVM -Name $machineName -ResourceGroupName $resGroupName
```

Rufen Sie die VM-Informationen und die Namen der Abhängigkeiten ab. Führen Sie in der gleichen Sitzung die folgenden Cmdlets aus:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Löschen Sie den Speichercontainer der Startdiagnose. Wenn der Name Ihres Computers kürzer als 9 Zeichen ist, müssen Sie beim Erstellen der `$diagContainer`-Variable den Index an die Länge Ihrer Zeichenfolge im Substring anpassen. 

Führen Sie in der gleichen Sitzung die folgenden Cmdlets aus:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Entfernen Sie die virtuelle Netzwerkschnittstelle.

```powershell
$machine | Remove-AzNetworkInterface -Force
```

Löschen Sie den Betriebssystemdatenträger.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Entfernen Sie die an Ihre VM angefügten Datenträger für Daten.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Löschen Sie schließlich den virtuellen Computer. Die Ausführung des Cmdlets nimmt einige Zeit in Anspruch. Sie können die an die VM angefügten Komponenten überwachen, indem Sie das VM-Objekt in PowerShell überprüfen. Um das Objekt zu überprüfen, verweisen Sie einfach auf die Variable, die das VM-Objekt enthält. Geben Sie `$machine`ein.

Zum Löschen der VM führen Sie in der gleichen Sitzung die folgenden Cmdlets aus:

```powershell
$machine | Remove-AzVM -Force
```
### <a name="with-powershell"></a>[Mit PowerShell](#tab/ps-azureRM)

Falls Sie die Ressourcengruppe nicht löschen können, befinden sich entweder die Abhängigkeiten nicht in der gleichen Ressourcengruppe, oder es gibt andere Ressourcen. Führen Sie diese Schritte aus:

Stellen Sie eine Verbindung mit Ihrer Azure Stack Hub-Umgebung her, und aktualisieren Sie dann die folgenden Variablen mit dem Namen Ihrer VM und Ressourcengruppe. Anweisungen zum Verbinden Ihrer PowerShell-Sitzung mit Azure Stack Hub finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Benutzer](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzureRmVM -Name $machineName -ResourceGroupName $resGroupName
```

Rufen Sie die VM-Informationen und die Namen der Abhängigkeiten ab. Führen Sie in der gleichen Sitzung die folgenden Cmdlets aus:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzureRmResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Löschen Sie den Speichercontainer der Startdiagnose. Wenn der Name Ihres Computers kürzer als 9 Zeichen ist, müssen Sie beim Erstellen der `$diagContainer`-Variable den Index an die Länge Ihrer Zeichenfolge im Substring anpassen. 

Führen Sie in der gleichen Sitzung die folgenden Cmdlets aus:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzureRmStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Entfernen Sie die virtuelle Netzwerkschnittstelle.

```powershell
$machine | Remove-AzureRmNetworkInterface -Force
```

Löschen Sie den Betriebssystemdatenträger.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Entfernen Sie die an Ihre VM angefügten Datenträger für Daten.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzureRmStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Löschen Sie schließlich den virtuellen Computer. Die Ausführung des Cmdlets nimmt einige Zeit in Anspruch. Sie können die an die VM angefügten Komponenten überwachen, indem Sie das VM-Objekt in PowerShell überprüfen. Um das Objekt zu überprüfen, verweisen Sie einfach auf die Variable, die das VM-Objekt enthält. Geben Sie `$machine`ein.

Zum Löschen der VM führen Sie in der gleichen Sitzung die folgenden Cmdlets aus:

```powershell
$machine | Remove-AzureRmVM -Force
```
---
## <a name="next-steps"></a>Nächste Schritte

[Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md)