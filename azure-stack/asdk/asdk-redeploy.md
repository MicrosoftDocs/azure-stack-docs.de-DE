---
title: Erneutes Bereitstellen des ASDK | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Azure Stack Development Kit (ASDK) erneut bereitstellen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 5ff77bbe915a506803a1c06f68579c199439ea73
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025889"
---
# <a name="redeploy-the-asdk"></a>Erneutes Bereitstellen des ASDK
In diesem Artikel erfahren Sie, wie Sie das Azure Stack Development Kit (ASDK) in einer produktionsfremden Umgebung erneut bereitstellen. Da für das ASDK keine Upgrades unterstützt werden, müssen Sie es vollständig neu bereitstellen, um zu einer neueren Version zu wechseln. Sie können das ASDK auch dann erneut bereitstellen, wenn Sie von Grund auf neu beginnen möchten.

> [!IMPORTANT]
> Für das ASDK kann kein Upgrade auf eine neue Version durchgeführt werden. Sie müssen das ASDK immer auf dem ASDK-Hostcomputer neu bereitstellen, wenn Sie eine neuere Version von Azure Stack evaluieren möchten.

## <a name="remove-azure-registration"></a>Entfernen der Azure-Registrierung 
Wenn Sie Ihre ASDK-Installation bei Azure registriert haben, müssen Sie die Registrierungsressource entfernen, bevor Sie das ASDK erneut bereitstellen. Registrieren Sie das ASDK erneut, um die Verfügbarkeit der Elemente im Marketplace zu aktivieren, wenn Sie das ASDK neu bereitstellen. Falls Sie das ASDK nicht bei Ihrem Azure-Abonnement registriert haben, können Sie diesen Abschnitt überspringen.

Verwenden Sie das Cmdlet **Remove-AzsRegistration**, um die Registrierungsressource zu entfernen und die Registrierung von Azure Stack aufzuheben. Verwenden Sie dann das Cmdlet **Remove-AzureRMRsourceGroup**, um die Azure Stack-Ressourcengruppe aus Ihrem Azure-Abonnement zu löschen:

1. Öffnen Sie auf einem Computer, der Zugriff auf den privilegierten Endpunkt hat, eine PowerShell-Konsole als Administrator. Beim ASDK ist das der ASDK-Hostcomputer.

2. Führen Sie die folgenden PowerShell-Befehle aus, um die Registrierung Ihrer ASDK-Installation aufzuheben und die Ressourcengruppe **azurestack** aus Ihrem Azure-Abonnement zu löschen:

   ```powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. Im Verlauf der Skriptausführung werden Sie zur Anmeldung bei Ihrem Azure-Abonnement und bei der lokalen ASDK-Installation aufgefordert.
4. Nach Abschluss der Skriptausführung werden Meldungen angezeigt, die in etwa wie in den folgenden Beispielen aussehen sollten:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).``Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Azure Stack sollte nun nicht mehr bei Ihrem Azure-Abonnement registriert sein. Die Ressourcengruppe „azurestack“ sollte ebenfalls gelöscht werden. Diese Ressourcengruppe wurde erstellt, als Sie das ASDK zum ersten Mal bei Azure registriert haben.

## <a name="deploy-the-asdk"></a>Bereitstellen des ASDK
Um Azure Stack erneut bereitzustellen, müssen Sie wie unten beschrieben noch einmal von Vorne anfangen. Welche Schritte ausgeführt werden müssen, hängt davon ab, ob Sie das ASDK mithilfe des Azure Stack-Installationsskripts (asdk-installer.ps1) installiert haben.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Erneutes Bereitstellen des ASDKs mithilfe des Installationsskripts
1. Öffnen Sie auf dem ASDK-Computer eine PowerShell-Konsole mit erhöhten Rechten, und navigieren Sie zum Skript „asdk-installer.ps1“. Dieses befindet sich auf einem systemfremden Laufwerk im Verzeichnis **AzureStack_Installer**. Führen Sie das Skript aus, und klicken Sie auf **Neu starten**.

   ![Ausführen des Skripts „asdk-installer.ps1“](media/asdk-redeploy/1.png)

2. Wählen Sie das Basisbetriebssystem aus (nicht **Azure Stack**), und klicken Sie dann auf **Weiter**.

   ![Neustarten mit dem Hostbetriebssystem](media/asdk-redeploy/2.png)

3. Melden Sie sich nach dem Neustart des ASDK-Hosts im Basisbetriebssystem als lokaler Administrator an. Suchen und löschen Sie die Datei **C:\CloudBuilder.vhdx**, die in der vorherigen Bereitstellung verwendet wurde.

4. Wiederholen Sie die Schritte, die Sie beim erstmaligen [Bereitstellen des ASDKs](asdk-install.md) ausgeführt haben.

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Erneutes Bereitstellen des ASDKs ohne Installationsprogramm
Wenn Sie das ASDK nicht mithilfe des Skripts „asdk-installer.ps1“ installiert haben, müssen Sie den ASDK-Hostcomputer manuell neu konfigurieren, bevor Sie das ASDK neu bereitstellen.

1. Führen Sie auf dem ASDK-Computer **msconfig.exe** aus, um das Hilfsprogramm „Systemkonfiguration“ zu starten. Wählen Sie auf der Registerkarte **Start** das Hostbetriebssystem (nicht Azure Stack) aus, klicken Sie auf **Als Standard**, und klicken Sie anschließend auf **OK**. Klicken Sie auf **Neu starten**, wenn Sie dazu aufgefordert werden.

      ![Festlegen der Startkonfiguration](media/asdk-redeploy/4.png)

2. Melden Sie sich nach dem Neustart des ASDK-Hosts im Basisbetriebssystem als lokaler Administrator des ASDK-Hostcomputers an. Suchen und löschen Sie die Datei **C:\CloudBuilder.vhdx**, die in der vorherigen Bereitstellung verwendet wurde.

3. Wiederholen Sie die Schritte, die Sie beim erstmaligen [Bereitstellen des ASDKs per PowerShell](asdk-deploy-powershell.md) ausgeführt haben.


## <a name="next-steps"></a>Nächste Schritte
[Konfigurationsaufgaben nach der Installation des ASDK](asdk-post-deploy.md)




