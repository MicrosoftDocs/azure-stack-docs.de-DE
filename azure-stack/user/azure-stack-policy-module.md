---
title: Verwenden des Azure Stack Hub-Richtlinienmoduls
description: Erfahren Sie, wie Sie ein Azure-Abonnement so beschränken, dass es sich wie ein Azure Stack Hub-Abonnement verhält.
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: ca96de45f50f48b91dbb2e6e8679df5dedab8d8f
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547057"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Verwalten von Azure-Richtlinien mithilfe des Azure Stack Hub-Richtlinienmoduls

Das Azure Stack Hub-Richtlinienmodul ermöglicht Ihnen die Konfiguration eines Azure-Abonnements mit der gleichen Versionsverwaltung und Dienstverfügbarkeit wie Azure Stack Hub. Das Modul verwendet das PowerShell-Cmdlet [**New-AzPolicyDefinition**](/powershell/module/Az.resources/new-Azpolicydefinition) zum Erstellen einer Azure-Richtlinie, mit der die in einem Abonnement verfügbaren Ressourcentypen und Dienste eingeschränkt werden. Anschließend erstellen Sie eine Richtlinienzuweisung im geeigneten Bereich, indem Sie das Cmdlet [**New-AzPolicyAssignment**](/powershell/module/Az.resources/new-Azpolicyassignment) verwenden. Nach Konfigurieren der Richtlinie können Sie Ihr Azure-Abonnement zum Entwickeln von Apps für Azure Stack Hub verwenden.

## <a name="install-the-module"></a>Installieren des Moduls

1. Installieren Sie die erforderliche Version des PowerShell-Moduls „Az“, wie in Schritt 1 von [Installieren von PowerShell für Azure Stack Hub](../operator/powershell-install-az-module.md) beschrieben.
2. [Laden Sie die Azure Stack Hub-Tools aus GitHub herunter](../operator/azure-stack-powershell-download.md).
3. [Konfigurieren Sie PowerShell für die Verwendung mit Azure Stack Hub](azure-stack-powershell-configure-user.md).
4. Importieren Sie das **AzureStack.Policy.psm1**-Modul:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Übernehmen der Richtlinie für das Azure-Abonnement

Mit den folgenden Befehlen können Sie eine Azure Stack Hub-Standardrichtlinie auf Ihr Azure-Abonnement anwenden. Ersetzen Sie vor der Ausführung dieser Befehle `Azure subscription name` durch den Namen Ihres Azure-Abonnements:

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Übernehmen einer Richtlinie für eine Ressourcengruppe

Möglicherweise möchten Sie präzisere Richtlinien anwenden. Beispielsweise könnten andere Ressourcen im selben Abonnement ausgeführt werden. Sie können die Anwendung der Richtlinie auf eine bestimmte Ressourcengruppe beschränken, was es Ihnen ermöglicht, Ihre Apps für Azure Stack Hub mithilfe von Azure-Ressourcen zu testen. Ersetzen Sie vor der Ausführung der folgenden Befehle `Azure subscription name` durch den Namen Ihres Azure-Abonnements:

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Ausgeführte Richtlinien

Nach der Bereitstellung der Azure-Richtlinie wird eine Fehlermeldung angezeigt, wenn Sie versuchen, eine durch die Richtlinie untersagte Ressource bereitzustellen:

![Ergebnis eines Ressourcenbereitstellungsfehlers aufgrund einer Richtlinienbeschränkung](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
* [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
* [Bereitstellen von Vorlagen mithilfe von Visual Studio](azure-stack-deploy-template-visual-studio.md)
