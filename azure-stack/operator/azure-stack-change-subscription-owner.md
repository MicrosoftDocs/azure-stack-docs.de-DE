---
title: Aktualisieren des Besitzers des Azure Stack-Benutzerabonnements | Microsoft-Dokumentation
description: Ändern Sie den Abrechnungsbesitzer für Azure Stack-Benutzerabonnements.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: f37eb580dd9a1276587e6ad731952661ad4ddf0b
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986215"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Ändern des Besitzers für ein Azure Stack-Benutzerabonnement

Azure Stack-Bediener können mithilfe von PowerShell den Abrechnungsbesitzer eines Benutzerabonnements ändern. Das Ändern des Besitzers kann beispielsweise erforderlich sein, wenn ein Benutzer aus Ihrer Organisation ausscheidet.

Einem Abonnement sind zwei Arten von *Besitzern* zugewiesen:

- **Abrechnungsbesitzer**: Der Abrechnungsbesitzer ist standardmäßig das Benutzerkonto, das das Abonnement aus einem Angebot erhält und dann für die Abrechnungsbeziehung dieses Abonnements zuständig ist. Dieses Konto ist gleichzeitig ein Administrator des Abonnements. Für ein Abonnement kann jeweils nur ein einzelnes Benutzerkonto als Abrechnungsbesitzer festgelegt sein. Bei einem Abrechnungsbesitzer handelt es sich häufig um eine Führungskraft der Organisation oder um einen Teamleiter.

  Der Abrechnungsbesitzer kann mithilfe des PowerShell-Cmdlets [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) geändert werden.  

- **Über RBAC-Rollen hinzugefügte Besitzer:** Die Rolle **Besitzer** kann weiteren Benutzern über die [rollenbasierte Zugriffssteuerung](azure-stack-manage-permissions.md) (Role Based Access Control, RBAC) erteilt werden. Zur Ergänzung des Abrechnungsbesitzers kann eine beliebige Anzahl zusätzlicher Benutzerkonten hinzugefügt werden. Zusätzliche Besitzer sind ebenfalls Administratoren des Abonnements und verfügen über sämtliche Berechtigungen für das Abonnement (außer zum Löschen des Abrechnungsbesitzers).

  Sie können PowerShell verwenden, um zusätzliche Besitzer zu verwalten. [hier finden Sie weitere Informationen](/azure/role-based-access-control/role-assignments-powershell)

## <a name="change-the-billing-owner"></a>Ändern des Abrechnungsbesitzers

Führen Sie das folgende Skript aus, um den Abrechnungsbesitzer eines Benutzerabonnements zu ändern. Der für die Skriptausführung verwendete Computer muss eine Verbindung mit Azure Stack herstellen und mindestens Version 1.3.0 des Azure Stack PowerShell-Moduls ausführen. Weitere Informationen finden Sie unter [Installieren von Azure Stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>In einer mehrinstanzenfähigen Azure Stack-Instanz muss sich der neue Besitzer im gleichen Verzeichnis befinden wie der aktuelle Besitzer. Wenn Sie die Besitzerrolle für das Abonnement einem Benutzer zuweisen möchten, der sich in einem anderen Verzeichnis befindet, müssen Sie zuerst [den Benutzer als Gast in Ihr Verzeichnis einladen]((/azure/active-directory/b2b/add-users-administrator).

Ersetzen Sie vor der Skriptausführung die folgenden Werte im Skript:

- **$ArmEndpoint**: Der Resource Manager-Endpunkt für Ihre Umgebung
- **$TenantId**: Ihre Mandanten-ID
- **$SubscriptionId**: Ihre Abonnement-ID.
- **$OwnerUpn**: Ein Konto (beispielsweise **Benutzer\@example.com**), das als neuer Abrechnungsbesitzer hinzufügt wird

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md)
