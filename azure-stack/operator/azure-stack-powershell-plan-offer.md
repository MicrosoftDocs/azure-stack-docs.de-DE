---
title: Verwenden von PowerShell zum Verwalten von Abonnements, Plänen und Angeboten in Azure Stack Hub
description: Es wird beschrieben, wie Sie PowerShell zum Verwalten von Abonnements, Plänen und Angeboten in Azure Stack Hub verwenden.
author: PatAltimore
ms.topic: how-to
ms.date: 12/18/2020
ms.author: patricka
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: 3dcc3eae565f2e2a41078c2696a09f60ffa618b9
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697866"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>Verwenden von PowerShell zum Verwalten von Abonnements, Plänen und Angeboten in Azure Stack Hub

Sie können PowerShell zum Konfigurieren und Bereitstellen von Diensten nutzen, indem Sie Angebote, Pläne und Abonnements verwenden. Verwenden von PowerShell Eine Anleitung zum Einrichten von PowerShell in Azure Stack Hub finden Sie unter [Installieren des PowerShell Az-Moduls für Azure Stack Hub](powershell-install-az-module.md). Informationen zum Herstellen einer Verbindung mit Azure Stack Hub über PowerShell finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell](azure-stack-powershell-configure-admin.md).

Vergewissern Sie sich zunächst, dass das Azure Stack Hub-PowerShell-Modul geladen wurde. Geben Sie in einer PowerShell-Konsole `Import-Module AzureStack` ein.

## <a name="create-a-plan"></a>Erstellen von Plänen

Beim Erstellen eines Plans werden Kontingente benötigt. Sie können vorhandene Kontingente verwenden oder neue Kontingente erstellen. Für die Erstellung eines Speicher-, Compute- bzw. Netzwerkkontingents können Sie beispielsweise die Cmdlets [New-AzsStorageQuota](/powershell/module/azs.storage.admin/new-azsstoragequota), [New-AzsComputeQuota](/powershell/module/azs.compute.admin/new-azscomputequota) und [New-AzsNetworkQuota](/powershell/module/azs.network.admin/new-azsnetworkquota) verwenden:

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

Verwenden Sie [New-AzsPlan](/powershell/module/azs.subscriptions.admin/new-azsplan), um einen Basis- oder Add-On-Plan zu erstellen oder zu aktualisieren.

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>Erstellen von Angeboten

Verwenden Sie [New-AzsOffer](/powershell/module/azs.subscriptions.admin/new-azsoffer), um ein Angebot zu erstellen.

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

Wenn Sie dann über ein Angebot verfügen, können Sie dafür Pläne hinzufügen. Verwenden Sie hierfür [Add-AzsPlanToOffer](/powershell/module/azs.subscriptions.admin/add-azsplantooffer). Der Parameter **-PlanLinkType** dient zum Angeben des Plantyps.

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

Verwenden Sie das Cmdlet [Set-AzsOffer](/powershell/module/azs.subscriptions.admin/set-azsoffer), wenn Sie den Status eines Angebots ändern möchten.

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>Erstellen eines Abonnements für ein Angebot

Nachdem Sie ein Angebot erstellt haben, benötigen Benutzer ein Abonnement für dieses Angebot, damit sie es nutzen können. Ein Benutzer kann auf zwei Arten ein Abonnement für ein Angebot erhalten:

* Als Cloudbetreiber können Sie ein Abonnement für einen Benutzer erstellen. Von Ihnen erstellte Abonnements können für öffentliche und private Angebote verwendet werden.
* Als Benutzer können Sie ein öffentliches Angebot abonnieren.

Verwenden Sie [New-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/new-azsusersubscription), um als Cloudbetreiber ein Abonnement für einen Benutzer zu erstellen.

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

Verwenden Sie [New-AzsSubscription](/powershell/module/azs.subscriptions/new-azssubscription), um als Benutzer ein öffentliches Angebot zu abonnieren. Für *New-AzsSubscription* wird eine Verbindung mit der Azure Resource Manager-Umgebung des Benutzers benötigt. Führen Sie die Schritte unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell](azure-stack-powershell-configure-admin.md) aus, aber verwenden Sie dabei den Azure Resource Manager-Endpunkt des Benutzers. Beispiel: `Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`.

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>Löschen von Kontingenten, Plänen, Angeboten und Abonnements

Es sind zusätzliche PowerShell-Cmdlets zum Löschen von Azure Stack Hub-Kontingenten, -Plänen, -Angeboten und -Abonnements vorhanden. Unten sind hierfür jeweils Beispiele angegeben.

Verwenden Sie [Remove-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/remove-azsusersubscription), um ein Abonnement für ein Angebot zu entfernen.

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

Verwenden Sie [Remove-AzsPlanFromOffer](/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer), um einen Plan für ein Angebot zu entfernen.

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

Verwenden Sie [Remove-AzsPlan](/powershell/module/azs.subscriptions.admin/remove-azsplan), um einen Plan zu entfernen.

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

Verwenden Sie [Remove-AzsOffer](/powershell/module/azs.subscriptions.admin/remove-azsoffer), um ein Angebot zu entfernen.

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

Verwenden Sie [Remove-AzsStorageQuota](/powershell/module/azs.storage.admin/remove-azsstoragequota), [Remove-AzsComputeQuota](/powershell/module/azs.compute.admin/remove-azscomputequota) bzw. [Remove-AzsNetworkQuota](/powershell/module/azs.network.admin/remove-azsnetworkquota), um Kontingente zu entfernen.

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack Hub](./azure-stack-updates.md)
