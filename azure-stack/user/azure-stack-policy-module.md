---
title: Verwenden des Azure Stack Hub-Richtlinienmoduls
description: Erfahren Sie, wie Sie ein Azure-Abonnement so beschränken, dass es sich wie ein Azure Stack Hub-Abonnement verhält.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 7af2662c52de8085b6b77fa0c9a2b36f401168fc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703824"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Verwalten von Azure-Richtlinien mithilfe des Azure Stack Hub-Richtlinienmoduls

Das Azure Stack Hub-Richtlinienmodul ermöglicht Ihnen die Konfiguration eines Azure-Abonnements mit der gleichen Versionsverwaltung und Dienstverfügbarkeit wie Azure Stack Hub. Das Modul verwendet das PowerShell-Cmdlet [**New-AzureRmPolicyDefinition**](/powershell/module/azurerm.resources/new-azurermpolicydefinition) zum Erstellen einer Azure-Richtlinie, mit der die in einem Abonnement verfügbaren Ressourcentypen und Dienste eingeschränkt werden. Anschließend erstellen Sie eine Richtlinienzuweisung im geeigneten Bereich, indem Sie das Cmdlet [**New-AzureRmPolicyAssignment**](/powershell/module/azurerm.resources/new-azurermpolicyassignment) verwenden. Nach Konfigurieren der Richtlinie können Sie Ihr Azure-Abonnement zum Entwickeln von Apps für Azure Stack Hub verwenden.

## <a name="install-the-module"></a>Installieren des Moduls

1. Installieren Sie die erforderliche Version des AzureRM PowerShell-Moduls, wie in Schritt 1 von [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md) beschrieben.
2. [Laden Sie die Azure Stack Hub-Tools aus GitHub herunter](../operator/azure-stack-powershell-download.md).
3. [Konfigurieren Sie PowerShell für die Verwendung mit Azure Stack Hub](azure-stack-powershell-configure-user.md).
4. Importieren Sie das **AzureStack.Policy.psm1**-Modul:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Übernehmen der Richtlinie für das Azure-Abonnement

Mit den folgenden Befehlen können Sie eine Azure Stack Hub-Standardrichtlinie auf Ihr Azure-Abonnement anwenden. Ersetzen Sie vor der Ausführung dieser Befehle `Azure subscription name` durch den Namen Ihres Azure-Abonnements:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Übernehmen einer Richtlinie für eine Ressourcengruppe

Möglicherweise möchten Sie präzisere Richtlinien anwenden. Beispielsweise könnten andere Ressourcen im selben Abonnement ausgeführt werden. Sie können die Anwendung der Richtlinie auf eine bestimmte Ressourcengruppe beschränken, was es Ihnen ermöglicht, Ihre Apps für Azure Stack Hub mithilfe von Azure-Ressourcen zu testen. Ersetzen Sie vor der Ausführung der folgenden Befehle `Azure subscription name` durch den Namen Ihres Azure-Abonnements:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Ausgeführte Richtlinien

Nach der Bereitstellung der Azure-Richtlinie wird eine Fehlermeldung angezeigt, wenn Sie versuchen, eine durch die Richtlinie untersagte Ressource bereitzustellen:

![Ergebnis eines Ressourcenbereitstellungsfehlers aufgrund einer Richtlinienbeschränkung](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
* [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
* [Bereitstellen von Vorlagen mithilfe von Visual Studio](azure-stack-deploy-template-visual-studio.md)
