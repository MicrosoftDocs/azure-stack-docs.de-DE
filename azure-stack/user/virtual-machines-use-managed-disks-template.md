---
title: Verwenden von Resource Manager-Vorlagen für verwaltete Datenträger in Azure Stack Hub
description: Hier erhalten Sie Informationen über die Unterschiede zwischen verwalteten und nicht verwalteten Datenträgern bei der Verwendung von Azure Resource Manager-Vorlagen.
author: sethmanheim
ms.author: sethm
ms.date: 8/25/2020
ms.topic: conceptual
ms.reviewer: wellsluo
ms.lastreviewed: 8/25/2020
ms.openlocfilehash: 2d2f274fffd1a07857b79255587b659003a3a1ce
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823271"
---
# <a name="use-vm-managed-disks-templates"></a>Verwenden Sie Vorlagen für von der VM verwaltete Datenträger

In diesem Artikel werden die Unterschiede zwischen verwalteten und nicht verwalteten Datenträgern bei der Verwendung von Azure Resource Manager-Vorlagen zur Bereitstellung virtueller Computer in Azure Stack Hub beschrieben. In diesem Beispiel werden Sie bei der Konvertierung vorhandener Vorlagen, die nicht verwaltete Datenträger verwenden, in verwaltete Datenträger unterstützt.

## <a name="unmanaged-disks-template-formatting"></a>Formatieren von Vorlagen mit nicht verwalteten Datenträgern

Zunächst wird die Bereitstellung nicht verwalteter Datenträger betrachtet. Für das Erstellen nicht verwalteter Datenträger benötigen Sie ein Speicherkonto, um die VHD-Dateien zu speichern. Sie können ein neues Speicherkonto erstellen oder ein bereits vorhandenes verwenden. Erstellen Sie wie folgt eine neue Speicherkontoressource im Ressourcenblock der Vorlage:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2017-10-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Innerhalb eines Objekts für einen virtuellen Computer fügen Sie eine Abhängigkeit vom Speicherkonto hinzu, um sicherzustellen, dass es vor dem virtuellen Computer erstellt wird. Geben Sie anschließend im Bereich `storageProfile` den vollständigen URI des VHD-Speicherorts an, der auf das Speicherkonto verweist und für den Betriebssystem- und andere Datenträger benötigt wird. Das folgende Beispiel erstellt einen Betriebssystemdatenträger aus einem Image und einen leeren Datenträger mit einer Größe von 1023 GB:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formatieren von Vorlagen mit verwalteten Datenträgern

Bei verwalteten Azure-Datenträgern wird der Datenträger zu einer Ressource der obersten Ebene und benötigt kein Speicherkonto mehr, um vom Benutzer erstellt zu werden. Verwaltete Datenträger wurden erstmals in der `2017-03-30`-API-Version eingeführt. Die folgenden Abschnitte bieten eine Einführung in die standardmäßigen Einstellungen und erklären, wie Sie Ihre Datenträger weiter anpassen können.

### <a name="default-managed-disk-settings"></a>Standardeinstellungen für verwaltete Datenträger

Sie müssen zum Erstellen eines virtuellen Computers mit verwalteten Datenträgern keine Speicherkontoressource mehr erstellen. Im unten angegebene Vorlagenbeispiel gibt es einige Unterschiede zu den oben genannten Beispielen für nicht verwaltete Datenträger:

- Die `apiVersion` ist eine Version für einen virtualMachines-Ressourcentyp, der verwaltete Datenträger unterstützt.
- `osDisk` und `dataDisks` verweisen nicht mehr auf einen bestimmten URI für die VHD.
- Bei der Bereitstellung ohne Angabe von zusätzlichen Eigenschaften verwendet der Datenträger einen auf der Größe des virtuellen Computers basierenden Speichertyp. Wenn Sie z. B. eine VM-Größe verwenden, die Storage Premium unterstützt (Größen mit „s“ im Namen, z. B. Standard_DS2_v2), werden standardmäßig Premium-Datenträger konfiguriert. Sie können dies über die SKU-Einstellung des Datenträgers ändern, indem Sie einen Speichertyp angeben.
- Wenn kein Name für den Datenträger angegeben ist, wird das Format `<VMName>_OsDisk_1_<randomstring>` für den Betriebssystemdatenträger und `<VMName>_disk<#>_<randomstring>` für jeden anderen Datenträger übernommen.
  - Wenn ein virtueller Computer aus einem benutzerdefinierten Image erstellt wird, werden die Standardeinstellungen für den Speicherkontotyp und den Datenträgernamen aus den Datenträgereigenschaften abgerufen, die in der Ressource des benutzerdefinierten Image definiert sind. Diese können überschrieben werden, indem Sie in der Vorlage Werte für sie angeben.
- Standardmäßig erfolgt das Zwischenspeichern für den Betriebssystemdatenträger mit **Lese-/Schreibzugriff** und für Datenträger **ohne Zugriff**.
- Im nachstehenden Beispiel besteht immer noch eine Speicherkontoabhängigkeit, die allerdings nur für Diagnosespeicher und nicht für Datenträgerspeicher benötigt wird:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="use-a-top-level-managed-disk-resource"></a>Verwenden einer verwalteten Datenträgerressource auf oberster Ebene

Anstatt die Datenträgerkonfiguration im Objekt des virtuellen Computers anzugeben können Sie eine Datenträgerressource auf oberster Ebene erstellen und sie als Teil des Erstellungsprozesses des virtuellen Computers anfügen. Stellen Sie sicher, dass `2017-03-30` als `disks`-Ressourcen-API-Version verwendet wird. Beispielsweise können Sie eine Datenträgerressource zur Verwendung als Datenträger wie folgt erstellen. In diesem Beispiel wird `vmName` als Teil des Datenträgernamens verwendet:

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2017-03-30",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Innerhalb des VM-Objekts verweisen Sie auf das anzufügende Datenträgerobjekt. Durch Angabe der Ressourcen-ID des verwalteten Datenträgers, der in der Eigenschaft `managedDisk` erstellt wurde, kann der Datenträger hinzugefügt werden, während der virtuelle Computer erstellt wird. Die `apiVersion` für die VM-Ressource wird auf `2017-12-01` festgelegt. Eine Abhängigkeit von der Datenträgerressource wird hinzugefügt, um sicherzustellen, dass sie vor der Erstellung des virtuellen Computers erstellt wird:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Erstellen verwalteter Verfügbarkeitsgruppen mit virtuellen Computern mit verwalteten Datenträgern

Fügen Sie zum Erstellen verwalteter Verfügbarkeitsgruppen mit virtuellen Computern, die verwaltete Datenträger verwenden, das Objekt `sku` zur Ressource der Verfügbarkeitsgruppe hinzu, und legen Sie die Eigenschaft `name` auf `Aligned` fest. Durch diese Eigenschaft wird sichergestellt, dass die Datenträger für jeden virtuellen Computer ausreichend voneinander isoliert sind, um einzelne Fehlerquellen zu vermeiden. Beachten Sie, dass die `apiVersion` für die Verfügbarkeitsgruppenressource auf `2017-12-01` festgelegt ist:

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 1,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

<!--
* For full templates that use managed disks visit the following Azure Quickstart Repo links.
    * [Windows VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
-->

- Weitere Informationen zu verwalteten Datenträgern finden Sie unter [Verwaltete Azure Stack Hub-Datenträger](azure-stack-managed-disk-considerations.md).
- Lesen Sie die Referenzdokumentation zu den Vorlagen für VM-Ressourcen unter [Referenz zur Microsoft.Compute/virtualMachines-Vorlage](/azure/templates/microsoft.compute/2017-12-01/virtualmachines).
- Lesen Sie die Referenzdokumentation zu den Vorlagen für Datenträgerressourcen unter [Referenz zurMicrosoft.Compute/disks-Vorlage](/azure/templates/microsoft.compute/2017-03-30/disks).
