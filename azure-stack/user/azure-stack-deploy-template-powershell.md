---
title: Bereitstellen einer Vorlage mithilfe von PowerShell in Azure Stack Hub | Microsoft-Dokumentation
description: Stellen Sie mithilfe von PowerShell eine Vorlage in Azure Stack Hub bereit.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 010d6a31ec9bd618ea9b4394b505649f2146a80d
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536316"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Bereitstellen einer Vorlage mithilfe von PowerShell in Azure Stack Hub

Sie können PowerShell zum Bereitstellen von Azure Resource Manager-Vorlagen (ARM) für Azure Stack Hub verwenden. In diesem Artikel wird das Verwenden von PowerShell zum Bereitstellen einer Vorlage beschrieben.

## <a name="run-azurerm-powershell-cmdlets"></a>Ausführen von AzureRM PowerShell-Cmdlets

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
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Erhöhen Sie bei jeder Skriptausführung den Wert für den `$myNum`-Parameter, um zu verhindern, dass Ihre Bereitstellung überschrieben wird.

4. Öffnen Sie das Azure Stack Hub-Portal, wählen Sie **Durchsuchen**, und wählen Sie dann **Virtuelle Computer**, um nach dem neuen virtuellen Computer (**myDeployment001**) zu suchen.

## <a name="cancel-a-running-template-deployment"></a>Abbrechen der Bereitstellung einer Vorlage

Verwenden Sie zum Abbrechen der Ausführung einer Vorlagenbereitstellung das PowerShell-Cmdlet `Stop-AzureRmResourceGroupDeployment`.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer Vorlage mit Visual Studio](azure-stack-deploy-template-visual-studio.md)
