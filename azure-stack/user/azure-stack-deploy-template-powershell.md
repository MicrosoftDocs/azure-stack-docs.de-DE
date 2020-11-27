---
title: Bereitstellen einer Vorlage mithilfe von PowerShell in Azure Stack Hub
description: Stellen Sie mithilfe von PowerShell eine Vorlage in Azure Stack Hub bereit.
author: mattbriggs
ms.topic: article
ms.date: 11/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 5bda04678c672c3951cc7644b3dc89f5d9ad25cf
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518329"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Bereitstellen einer Vorlage mithilfe von PowerShell in Azure Stack Hub

Sie können PowerShell zum Bereitstellen von Azure Resource Manager-Vorlagen (ARM) für Azure Stack Hub verwenden. In diesem Artikel wird das Verwenden von PowerShell zum Bereitstellen einer Vorlage beschrieben.

## <a name="run-powershell-cmdlets"></a>Ausführen von PowerShell-Cmdlets

### <a name="az-modules"></a>[Az-Module](#tab/az)

In diesem Beispiel werden **Az** PowerShell-Cmdlets und eine auf GitHub gespeicherte Vorlage verwendet. Die Vorlage erstellt einen virtuelle Windows Server 2012 R2 Datacenter-Computer.

>[!NOTE]
> Bevor Sie dieses Beispiel ausprobieren, stellen Sie sicher, dass Sie [PowerShell für einen Azure Stack Hub-Benutzer konfiguriert](azure-stack-powershell-configure-user.md) haben.

1. Durchsuchen Sie das [Repository „AzureStack-QuickStart-Templates“](https://aka.ms/AzureStackGitHub) nach der Vorlage **101-simple-windows-vm**. Speichern Sie die Vorlage an diesem Speicherort: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.
3. Ersetzen Sie `username` und `password` im folgenden Skript durch Ihren Benutzernamen und Ihr Kennwort, und führen Sie dann das Skript aus:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Erhöhen Sie bei jeder Skriptausführung den Wert für den `$myNum`-Parameter, um zu verhindern, dass Ihre Bereitstellung überschrieben wird.

4. Öffnen Sie das Azure Stack Hub-Portal, wählen Sie **Durchsuchen**, und wählen Sie dann **Virtuelle Computer**, um nach dem neuen virtuellen Computer (**myDeployment001**) zu suchen.

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

In diesem Beispiel werden **AzureRM**-PowerShell-Cmdlets und eine auf GitHub gespeicherte Vorlage verwendet. Die Vorlage erstellt einen virtuelle Windows Server 2012 R2 Datacenter-Computer.

>[!NOTE]
> Bevor Sie dieses Beispiel ausprobieren, stellen Sie sicher, dass Sie [PowerShell für einen Azure Stack Hub-Benutzer konfiguriert](azure-stack-powershell-configure-user.md) haben.

1. Durchsuchen Sie das [Repository „AzureStack-QuickStart-Templates“](https://aka.ms/AzureStackGitHub) nach der Vorlage **101-simple-windows-vm**. Speichern Sie die Vorlage an diesem Speicherort: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.
3. Ersetzen Sie `username` und `password` im folgenden Skript durch Ihren Benutzernamen und Ihr Kennwort, und führen Sie dann das Skript aus:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRMResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRMResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Erhöhen Sie bei jeder Skriptausführung den Wert für den `$myNum`-Parameter, um zu verhindern, dass Ihre Bereitstellung überschrieben wird.

4. Öffnen Sie das Azure Stack Hub-Portal, wählen Sie **Durchsuchen**, und wählen Sie dann **Virtuelle Computer**, um nach dem neuen virtuellen Computer (**myDeployment001**) zu suchen.

---
## <a name="cancel-a-running-template-deployment"></a>Abbrechen der Bereitstellung einer Vorlage

Verwenden Sie zum Abbrechen der Ausführung einer Vorlagenbereitstellung das PowerShell-Cmdlet `Stop-AzResourceGroupDeployment`.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer Vorlage mit Visual Studio](azure-stack-deploy-template-visual-studio.md)
