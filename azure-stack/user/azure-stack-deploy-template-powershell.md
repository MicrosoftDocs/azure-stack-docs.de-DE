---
title: Bereitstellen einer Vorlage mithilfe von PowerShell in Azure Stack | Microsoft-Dokumentation
description: Stellen Sie eine Vorlage mithilfe von PowerShell in Azure Stack bereit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: be325c41023c859ee70b8ee932673db75f41ec4b
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691440"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack"></a>Bereitstellen einer Vorlage mithilfe von PowerShell in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können PowerShell zum Bereitstellen von Azure Resource Manager-Vorlagen (ARM) für Azure Stack verwenden. In diesem Artikel wird das Verwenden von PowerShell zum Bereitstellen einer Vorlage beschrieben.

## <a name="run-azurerm-powershell-cmdlets"></a>Ausführen von AzureRM PowerShell-Cmdlets

In diesem Beispiel werden **AzureRM**-PowerShell-Cmdlets und eine auf GitHub gespeicherte Vorlage verwendet. Die Vorlage erstellt einen virtuelle Windows Server 2012 R2 Datacenter-Computer.

>[!NOTE]
> Bevor Sie dieses Beispiel ausprobieren, stellen Sie sicher, dass Sie [PowerShell für einen Azure Stack-Benutzer konfiguriert](azure-stack-powershell-configure-user.md) haben.

1. Durchsuchen Sie das [AzureStackGitHub-Repository](https://aka.ms/AzureStackGitHub) nach der **101-simple-windows-vm**-Vorlage. Speichern Sie die Vorlage an diesem Speicherort: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.
3. Ersetzen Sie `username` und `password` im folgenden Skript durch Ihren Benutzernamen und Ihr Kennwort, und führen Sie das Skript aus:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    > Erhöhen Sie bei jeder Skriptausführung den Wert für den `$myNum`-Parameter, um zu verhindern, dass Ihre Bereitstellung überschrieben wird.

4. Öffnen Sie das Azure Stack-Portal, wählen Sie **Durchsuchen**, und wählen Sie dann **Virtuelle Computer**, um nach dem neuen virtuellen Computer (**myDeployment001**) zu suchen.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer Vorlage mit Visual Studio](azure-stack-deploy-template-visual-studio.md)
