---
title: Horizontales Hochskalieren von Workerrollen in App Services – Azure Stack | Microsoft-Dokumentation
description: Ausführliche Anleitung zum Skalieren von Azure Stack App Services
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/22/2010
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 839fa7fe8374f1f85b019178d4c3fe53f7137372
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308736"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service in Azure Stack: Hinzufügen von weiterer Infrastruktur oder von Workerrollen

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*  

Dieses Dokument enthält Anleitungen zum Skalieren der Infrastruktur- und Workerrollen für App Service in Azure Stack. Es werden die Schritte zum Erstellen von zusätzlichen Workerrollen aufgeführt, um Anwendungen beliebiger Größe zu unterstützen.

> [!NOTE]
> Wenn Ihre Azure Stack-Umgebung nicht mehr als 96 GB RAM aufweist, können beim Hinzufügen von zusätzlicher Kapazität Schwierigkeiten auftreten.

Standardmäßig unterstützt App Service in Azure Stack kostenlose und gemeinsam genutzte Workerebenen. Um andere Workerebenen hinzufügen zu können, müssen Sie weitere Workerrollen hinzufügen.

Wenn Sie sich nicht sicher sind, welche Elemente mit der App Service-Standardversion bei der Azure Stack-Installation bereitgestellt wurden, helfen Ihnen die zusätzlichen Informationen unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md) weiter.

Azure App Service in Azure Stack stellt alle Rollen mithilfe von VM-Skalierungsgruppen bereit und nutzt somit die Skalierungsfunktionen dieser Workload. Aus diesem Grund erfolgt die gesamte Skalierung der Workerebenen über den App Service-Administrator.

## <a name="add-additional-workers-with-powershell"></a>Hinzufügen von zusätzlichen Workern mit PowerShell

1. [Einrichten der Azure Stack-Administratorumgebung in PowerShell](azure-stack-powershell-configure-admin.md)

2. Verwenden Sie dieses Beispiel, um die Skalierungsgruppe horizontal hochzuskalieren:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Es kann je nach Typ der Rolle und der Anzahl von Instanzen einigen Stunden dauern, bis dieser Schritt abgeschlossen ist.
   >
   >

3. Überwachen Sie den Status der neuen Rolleninstanzen in der App Service-Verwaltung. Klicken Sie in der Liste auf den Rollentyp, um den Status einer einzelnen Rolleninstanz zu überprüfen.

## <a name="add-additional-workers-using-the-administration-portal"></a>Hinzufügen zusätzlicher Worker über das Verwaltungsportal

1. Melden Sie sich am Azure Stack-Verwaltungsportal als Dienstadministrator an.

2. Navigieren Sie zu **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klicken Sie auf **Rollen**. Eine Übersicht mit allen bereitgestellten App Service-Rollen wird angezeigt.

4. Klicken Sie mit der rechten Maustaste auf die Zeile des Typs, den Sie skalieren möchten, und klicken Sie dann auf **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klicken Sie auf **Skalierung**, wählen Sie die Anzahl der Instanzen aus, auf die skaliert werden soll, und klicken Sie dann auf **Speichern**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service in Azure Stack fügt jetzt die zusätzlichen VMs hinzu, konfiguriert sie, installiert die gesamte erforderliche Software und kennzeichnet sie als bereit, wenn dieser Prozess abgeschlossen ist. Dieser Prozess kann ca. 80 Minuten dauern.

7. Sie können den Bereitschaftsstatus der neuen Rollen überwachen, indem Sie die Worker auf dem Blatt **Rollen** anzeigen.

## <a name="result"></a>Ergebnis

Nachdem die Worker vollständig bereitgestellt wurden und einsatzbereit sind, werden sie für die Benutzer verfügbar gemacht, damit diese ihre Workloads darauf bereitstellen können. Unten sehen Sie ein Beispiel für die verschiedenen Tarife, die standardmäßig verfügbar sind. Die Option zum Auswählen des entsprechenden Tarifs ist nicht verfügbar, wenn für eine bestimmte Workerebene keine Worker vorhanden sind.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Um die Rollen „Management“, „Front-End“ oder „Herausgeber“ horizontal hochzuskalieren, müssen Sie den entsprechenden Rollentyp horizontal hochskalieren. 
>
>

Um die Rollen „Management“, „Front-End“ oder „Herausgeber“ horizontal hochzuskalieren, befolgen Sie die gleichen Schritte und wählen dabei den entsprechenden Rollentyp aus. Controller werden nicht als Skalierungsgruppen bereitgestellt. Aus diesem Grund sollten zwei Controller zum Installationszeitpunkt für alle Produktionsbereitstellungen bereitgestellt werden.

### <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Bereitstellungsquellen](azure-stack-app-service-configure-deployment-sources.md)

<!-- Update_Description: wording update -->