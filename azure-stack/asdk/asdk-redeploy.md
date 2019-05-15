---
title: Erneutes Bereitstellen des Azure Stack Development Kits (ASDK) | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie das ASDK neu installieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
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
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 0658943a36673df4658b6f1951ed7992996aba41
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617478"
---
# <a name="redeploy-the-asdk"></a>Erneutes Bereitstellen des ASDK
In diesem Artikel erfahren Sie, wie Sie das Azure Stack Development Kit (ASDK) in einer produktionsfremden Umgebung neu bereitstellen. Da für das ASDK keine Upgrades unterstützt werden, müssen Sie es vollständig neu bereitstellen, um zu einer neueren Version zu wechseln. Darüber hinaus können Sie das ASDK jederzeit neu bereitstellen, wenn Sie wieder von vorn beginnen möchten.

> [!IMPORTANT]
> Für das ASDK kann kein Upgrade auf eine neue Version durchgeführt werden. Wenn Sie eine neuere Version von Azure Stack evaluieren möchten, müssen Sie das ASDK auf dem Development Kit-Hostcomputer neu bereitstellen.

## <a name="remove-azure-registration"></a>Entfernen der Azure-Registrierung 
Wenn Sie Ihre ASDK-Installation bei Azure registriert haben, müssen Sie die Registrierungsressource entfernen, bevor Sie das ASDK bereitstellen. Registrieren Sie das ASDK erneut, um die Verfügbarkeit der Elemente im Marketplace zu aktivieren, wenn Sie das ASDK neu bereitstellen. Falls Sie das ASDK nicht bei Ihrem Azure-Abonnement registriert haben, können Sie diesen Abschnitt überspringen.

Verwenden Sie das Cmdlet **Remove-AzsRegistration**, um die Registrierungsressource zu entfernen und die Registrierung von Azure Stack aufzuheben. Verwenden Sie dann das Cmdlet **Remove-AzureRMRsourceGroup**, um die Azure Stack-Ressourcengruppe aus Ihrem Azure-Abonnement zu löschen:

1. Öffnen Sie auf einem Computer, der Zugriff auf den privilegierten Endpunkt hat, eine PowerShell-Konsole als Administrator. Beim ASDK ist das der Development Kit-Hostcomputer.

2. Führen Sie die folgenden PowerShell-Befehle aus, um die Registrierung Ihrer ASDK-Installation aufzuheben und die Ressourcengruppe **azurestack** aus Ihrem Azure-Abonnement zu löschen:

   ```Powershell    
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

3. Im Zuge der Skriptausführung werden Sie zur Anmeldung bei Ihrem Azure-Abonnement und bei der lokalen ASDK-Installation aufgefordert.
4. Nach Abschluss der Skriptausführung werden Meldungen angezeigt, die in etwa wie in den folgenden Beispielen aussehen sollten:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).``Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Azure Stack sollte nun nicht mehr bei Ihrem Azure-Abonnement registriert sein. Und auch die Ressourcengruppe „azurestack“, die im Zuge der Registrierung des ASDKs bei Azure erstellt wurde, sollte nicht mehr vorhanden sein.

## <a name="deploy-the-asdk"></a>Bereitstellen des ASDK
Um Azure Stack erneut bereitzustellen, müssen Sie wie unten beschrieben noch einmal von Vorne anfangen. Welche Schritte ausgeführt werden müssen, hängt davon ab, ob Sie das ASDK mithilfe des Azure Stack-Installationsskripts (asdk-installer.ps1) installiert haben.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Erneutes Bereitstellen des ASDKs mithilfe des Installationsskripts
1. Öffnen Sie auf dem ASDK-Computer eine PowerShell-Konsole mit erhöhten Rechten, und navigieren Sie zum Skript „asdk-installer.ps1“. Dieses befindet sich auf einem systemfremden Laufwerk im Verzeichnis **AzureStack_Installer**. Führen Sie das Skript aus, und klicken Sie auf **Neu starten**.

   ![Ausführen des Skripts „asdk-installer.ps1“](media/asdk-redeploy/1.png)

2. Wählen Sie das Basisbetriebssystem aus (nicht **Azure Stack**), und klicken Sie dann auf **Weiter**.

   ![Neustarten mit dem Hostbetriebssystem](media/asdk-redeploy/2.png)

3. Melden Sie sich nach dem Neustart des Development Kit-Hosts beim Basisbetriebssystem als lokaler Administrator an. Suchen und löschen Sie die Datei **C:\CloudBuilder.vhdx**, die in der vorherigen Bereitstellung verwendet wurde. 

4. Wiederholen Sie die Schritte, die Sie beim erstmaligen [Bereitstellen des ASDKs](asdk-install.md) ausgeführt haben.

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Erneutes Bereitstellen des ASDKs ohne Installationsprogramm
Wenn Sie das ASDK nicht mithilfe des Skripts „asdk-installer.ps1“ installiert haben, müssen Sie den Development Kit-Hostcomputer manuell neu konfigurieren, bevor Sie das ASDK neu bereitstellen.

1. Führen Sie auf dem ASDK-Computer **msconfig.exe** aus, um das Hilfsprogramm „Systemkonfiguration“ zu starten. Wählen Sie auf der Registerkarte **Start** das Hostbetriebssystem (nicht Azure Stack) aus, klicken Sie auf **Als Standard**, und klicken Sie anschließend auf **OK**. Klicken Sie auf **Neu starten**, wenn Sie dazu aufgefordert werden.

      ![Festlegen der Startkonfiguration](media/asdk-redeploy/4.png)

2. Melden Sie sich nach dem Neustart des Development Kit-Hosts beim Basisbetriebssystem als lokaler Administrator für den Development Kit-Hostcomputer an. Suchen und löschen Sie die Datei **C:\CloudBuilder.vhdx**, die in der vorherigen Bereitstellung verwendet wurde. 

3. Wiederholen Sie die Schritte, die Sie beim erstmaligen [Bereitstellen des ASDKs per PowerShell](asdk-deploy-powershell.md) ausgeführt haben.


## <a name="next-steps"></a>Nächste Schritte
[Konfigurationsaufgaben nach der Installation des ASDK](asdk-post-deploy.md)




