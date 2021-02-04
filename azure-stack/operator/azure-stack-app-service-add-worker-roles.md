---
title: Hinzufügen von Workern und Infrastruktur in App Service unter Azure Stack Hub
description: Detaillierter Leitfaden zum Aktualisieren von Azure App Service in Azure Stack Hub
author: bryanla
ms.topic: article
ms.date: 1/29/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 1/29/2020
ms.openlocfilehash: 442831a05950342718c0ee0fc0ced2395627eca6
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534027"
---
# <a name="add-workers-and-infrastructure-in-azure-app-service-on-azure-stack-hub"></a>Hinzufügen von Workern und Infrastruktur in App Service unter Azure Stack Hub

Dieses Dokument enthält Anleitungen zum Skalieren der Infrastruktur- und Workerrollen in Azure App Service unter Azure Stack Hub. Es werden alle Schritte beschrieben, die zum Erstellen von weiteren Workerrollen zur Unterstützung von Apps aller Größen erforderlich sind.

> [!NOTE]
> Wenn Ihre Azure Stack Hub-Umgebung nicht mehr als 96 GB RAM aufweist, können beim Hinzufügen von zusätzlicher Kapazität Schwierigkeiten auftreten.

Standardmäßig unterstützt Azure App Service in Azure Stack Hub kostenlose und gemeinsam genutzte Workerebenen. Um andere Workerebenen hinzufügen zu können, müssen Sie weitere Workerrollen hinzufügen.

Wenn Sie sich nicht sicher sind, welche Elemente mit der Azure App Service-Standardversion bei der Azure Stack Hub-Installation bereitgestellt wurden, helfen Ihnen die zusätzlichen Informationen unter [Übersicht über App Service in Azure Stack Hub](azure-stack-app-service-overview.md) weiter.

Azure App Service in Azure Stack Hub stellt alle Rollen mithilfe von VM-Skalierungsgruppen bereit und nutzt somit die Skalierungsfunktionen dieser Workload. Aus diesem Grund erfolgt die gesamte Skalierung der Workerebenen über den App Service-Administrator.

## <a name="add-additional-workers-with-powershell"></a>Hinzufügen von zusätzlichen Workern mit PowerShell



### <a name="az-modules"></a>[Az-Module](#tab/az)

1. [Einrichten der Azure Stack Hub-Administratorumgebung in PowerShell](azure-stack-powershell-configure-admin.md)

2. Verwenden Sie dieses Beispiel, um die Skalierungsgruppe aufzuskalieren.

    ```powershell
    
    ##### Scale out the AppService Role instances ######
    
    # Set context to AzureStack admin.
    Connect-AzAccount -EnvironmentName AzureStackAdmin
                                                    
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"
    
    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"
    
    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  
    
    # Get current scale set
    $vmss = Get-AzVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName
    
    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
    ```    

    > [!NOTE]
    > Es kann je nach Typ der Rolle und der Anzahl von Instanzen einigen Stunden dauern, bis dieser Schritt abgeschlossen ist.

3. Überwachen Sie über die Option für die App Service-Verwaltung den Status der neuen Rolleninstanzen. Klicken Sie in der Liste auf den jeweiligen Rollentyp, um den Status einer einzelnen Rolleninstanz zu überprüfen.
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

1. [Einrichten der Azure Stack Hub-Administratorumgebung in PowerShell](azure-stack-powershell-configure-admin.md)

2. Verwenden Sie dieses Beispiel, um die Skalierungsgruppe aufzuskalieren.

    ```powershell
    
    ##### Scale out the AppService Role instances ######
    
    # Set context to AzureStackAdmin admin.
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                    
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"
    
    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"
    
    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  
    
    # Get current scale set
    $vmss = Get-AzureRMVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName
    
    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRMVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
    ```   

    > [!NOTE]
    > Es kann je nach Typ der Rolle und der Anzahl von Instanzen einigen Stunden dauern, bis dieser Schritt abgeschlossen ist.

3. Überwachen Sie über die Option für die App Service-Verwaltung den Status der neuen Rolleninstanzen. Klicken Sie in der Liste auf den jeweiligen Rollentyp, um den Status einer einzelnen Rolleninstanz zu überprüfen.
---

## <a name="add-additional-workers-using-the-administrator-portal"></a>Hinzufügen zusätzlicher Worker über das Administratorportal

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Dienstadministrator an.

2. Navigieren Sie zu **App Services**.

    ![App Service im Azure Stack Hub-Administratorportal](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klicken Sie auf **Rollen**. Eine Übersicht mit allen bereitgestellten App Service-Rollen wird angezeigt.

4. Klicken Sie mit der rechten Maustaste auf die Zeile des Typs, den Sie skalieren möchten, und klicken Sie dann auf **ScaleSet**.

    ![ScaleSet-App Service-Rollen im Azure Stack Hub-Administratorportal](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klicken Sie auf **Skalierung**, wählen Sie die Anzahl der Instanzen aus, auf die skaliert werden soll, und klicken Sie dann auf **Speichern**.

    ![Festlegen von Instanzen für die Skalierung in App Service-Rollen im Azure Stack Hub-Administratorportal](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure App Service in Azure Stack Hub fügt jetzt die zusätzlichen VMs hinzu, konfiguriert sie, installiert die gesamte erforderliche Software und kennzeichnet sie als bereit, wenn dieser Prozess abgeschlossen ist. Dieser Prozess kann ca. 80 Minuten dauern.

7. Sie können den Bereitschaftsstatus der neuen Rollen überwachen, indem Sie die Worker auf dem Blatt **Rollen** anzeigen.

## <a name="result"></a>Ergebnis

Nachdem die Worker vollständig bereitgestellt wurden und einsatzbereit sind, werden sie für die Benutzer verfügbar gemacht, damit diese ihre Workloads darauf bereitstellen können. Der folgende Screenshot zeigt ein Beispiel für die verschiedenen Tarife, die standardmäßig verfügbar sind. Die Option zum Auswählen des entsprechenden Tarifs ist nicht verfügbar, wenn für eine bestimmte Workerebene keine Worker vorhanden sind.

![Tarife für den neuen App Service-Plan im Azure Stack Hub-Administratorportal](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Um die Rollen „Management“, „Front-End“ oder „Herausgeber“ aufzuskalieren, befolgen Sie die gleichen Schritte und wählen dabei den entsprechenden Rollentyp aus. Controller werden nicht als Skalierungsgruppen bereitgestellt. Aus diesem Grund sollten zwei Controller zum Installationszeitpunkt für alle Produktionsbereitstellungen bereitgestellt werden.

### <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Bereitstellungsquellen](azure-stack-app-service-configure-deployment-sources.md)
