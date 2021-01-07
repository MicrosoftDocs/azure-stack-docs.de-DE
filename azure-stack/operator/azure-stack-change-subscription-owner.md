---
title: Ändern des Abrechnungsbesitzers für ein Azure Stack Hub-Benutzerabonnement
description: Erfahren Sie, wie Sie den Abrechnungsbesitzer für ein Azure Stack Hub-Benutzerabonnement ändern.
author: PatAltimore
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: patricka
ms.reviewer: shnatara
ms.lastreviewed: 11/16/2020
ms.openlocfilehash: c5438aaf928efeee0b71470e0965c6b18b5a7c0a
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871289"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Ändern des Abrechnungsbesitzers für ein Azure Stack Hub-Benutzerabonnement

Azure Stack Hub-Operatoren können mithilfe von PowerShell den Abrechnungsbesitzer eines Benutzerabonnements ändern. Das Ändern des Besitzers kann beispielsweise erforderlich sein, wenn ein Benutzer aus Ihrer Organisation ausscheidet.

Einem Abonnement sind zwei Arten von *Besitzern* zugewiesen:

- **Abrechnungsbesitzer**: Der Abrechnungsbesitzer ist standardmäßig das Benutzerkonto, das das Abonnement aus einem Angebot erhält und dann für die Abrechnungsbeziehung dieses Abonnements zuständig ist. Dieses Konto ist gleichzeitig ein Administrator des Abonnements. Für ein Abonnement kann jeweils nur ein einzelnes Benutzerkonto als Abrechnungsbesitzer festgelegt sein. Bei einem Abrechnungsbesitzer handelt es sich häufig um eine Führungskraft der Organisation oder um einen Teamleiter.

  Der Abrechnungsbesitzer kann mithilfe des PowerShell-Cmdlets [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) geändert werden.  

- **Über RBAC-Rollen hinzugefügte Besitzer:** Die Rolle **Besitzer** kann weiteren Benutzern über die [rollenbasierte Zugriffssteuerung](azure-stack-manage-permissions.md) (Role Based Access Control, RBAC) erteilt werden. Zur Ergänzung des Abrechnungsbesitzers kann eine beliebige Anzahl zusätzlicher Benutzerkonten hinzugefügt werden. Zusätzliche Besitzer sind ebenfalls Administratoren des Abonnements und verfügen über sämtliche Berechtigungen für das Abonnement (außer zum Löschen des Abrechnungsbesitzers).

  Sie können PowerShell verwenden, um zusätzliche Besitzer zu verwalten. [hier finden Sie weitere Informationen](/azure/role-based-access-control/role-assignments-powershell)

## <a name="change-the-billing-owner"></a>Ändern des Abrechnungsbesitzers

Führen Sie das folgende Skript aus, um den Abrechnungsbesitzer eines Benutzerabonnements zu ändern. Der für die Skriptausführung verwendete Computer muss eine Verbindung mit Azure Stack Hub herstellen und mindestens Version 1.3.0 des Azure Stack Hub PowerShell-Moduls ausführen. Weitere Informationen finden Sie unter [Installieren von Azure Stack Hub PowerShell](powershell-install-az-module.md).

>[!NOTE]
>In einer mehrinstanzenfähigen Azure Stack Hub-Instanz muss sich der neue Besitzer in demselben Verzeichnis wie der aktuelle Besitzer befinden. Wenn Sie die Besitzerrolle für das Abonnement einem Benutzer zuweisen möchten, der sich in einem anderen Verzeichnis befindet, müssen Sie zuerst den [Benutzer als Gast in Ihr Verzeichnis einladen](/azure/active-directory/b2b/add-users-administrator).

Ersetzen Sie vor der Skriptausführung die folgenden Werte im Skript:

- **$ArmEndpoint**: Der Resource Manager-Endpunkt für Ihre Umgebung
- **$TenantId**: Ihre Mandanten-ID
- **$SubscriptionId**: Ihre Abonnement-ID.
- **$OwnerUpn**: Ein Konto (beispielsweise **Benutzer\@example.com**), das als neuer Abrechnungsbesitzer hinzufügt wird

### <a name="az-modules"></a>[Az-Module](#tab/az)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../includes/remove-account-az.md)]

### <a name="az-modules"></a>[Az-Module](#tab/azurerm)

```powershell
# Set up AzureRMure Stack Hub admin environment
Add-AzureRMEnvironment -ARMEndpoint $ArmEndpoint -Name AzureRMureStack-admin
Add-AzureRMAccount -Environment AzureRMureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRMSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRMContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzureRMsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzureRMsUserSubscription -InputObject $subscription
```
[!include[Remove Account](../includes/remove-account-azurerm.md)]
---




## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md)
