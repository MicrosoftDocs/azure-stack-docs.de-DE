---
title: App Service in Azure Stack – Versionshinweise zu Update 1 | Microsoft-Dokumentation
description: Erfahren Sie, was in Update 1 für App Service in Azure Stack enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digmobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 86eb23e57516bc108557bbdc4f2d131153f5e855
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306486"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service in Azure Stack – Versionshinweise zu Update 1

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Diese Versionshinweise beschreiben die Verbesserungen und Fehlerbehebungen in Update 1 für Azure App Service in Azure Stack und bekannte Probleme. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1802 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack-Development Kit vor der Bereitstellung von Azure App Service bereit.
>
>

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 1 für App Service in Azure Stack ist **69.0.13698.9**.

### <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Neue Bereitstellungen von Azure App Service in Azure Stack erfordern jetzt aufgrund von Verbesserungen der Art, in der SSO für Kudu jetzt in Azure App Service behandelt wird, ein [Platzhalterzertifikat mit drei Antragstellern](azure-stack-app-service-before-you-get-started.md#get-certificates). Der neue Antragsteller lautet **\*.sso.appservice.\<Region\>.\<Domänenname\>.\<Erweiterung\>**
>
>

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 1 für Azure App Service in Azure Stack enthält die folgenden Verbesserungen und Fehlerbehebungen:

- **Hohe Verfügbarkeit von Azure App Service**: Das Azure Stack-Update 1802 ermöglicht die Fehlerdomänen übergreifende Bereitstellung von Workloads. Daher kann die App Service-Infrastruktur fehlertolerant sein, da sie Fehlerdomänen übergreifend bereitgestellt wird. Standardmäßig haben alle neuen Bereitstellungen von Azure App Service diese Funktion, doch lesen Sie zu Bereitstellungen, die vor Anwendung des Azure Stack-Updates 1802 durchgeführt wurden, die Dokumentation [Fehlerdomänen übergreifendes Neuverteilen von Azure App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md ).

- **In vorhandenem virtuellem Netzwerk bereitstellen**: Kunden können jetzt App Service in Azure Stack in einem vorhandenen virtuellen Netzwerk bereitstellen. Die Bereitstellung in einem vorhandenen virtuellen Netzwerk ermöglicht Kunden, eine Verbindung mit der SQL Server- und Dateiserverinstanz – für Azure App Service erforderlich – über private Ports herzustellen. Während der Bereitstellung können die Kunden wählen, ob sie die Bereitstellung in einem vorhandenen virtuellen Netzwerk wünschen, doch vor der Bereitstellung [müssen sie Subnetze für die Verwendung durch App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) erstellen.

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.11388**.

- **Updates für folgende Anwendungsframeworks und Tools**:
    - Unterstützung für **.NET Core 2.0** hinzugefügt
    - Hinzugefügte **Node.JS**-Versionen:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Hinzugefügte **NPM**-Versionen:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Hinzugefügte **PHP**-Updates:
        - 5.6.32
        - 7.0.26 (x86 und x64)
        - 7.1.12 (x86 und x64)
    - **Git für Windows** wurde auf v2.14.1 aktualisiert.
    - **Mercurial** wurde auf v4.5.0 aktualisiert.

  - Unterstützung für das **Nur HTTPS**-Feature wurde innerhalb des Features „Benutzerdefinierte Domäne“ im App Service-Mandantenportal hinzugefügt. 

  - Überprüfung der Speicherverbindung in der Auswahl von benutzerdefiniertem Speicher für Azure Functions wurde hinzugefügt. 

#### <a name="fixes"></a>Fehlerbehebungen

- Beim Erstellen eines Offlinebereitstellungspakets erhalten Kunden keine „Zugriff verweigert“-Fehlermeldung mehr, wenn sie den Ordner vom App Service-Installationsprogramm aus öffnen.

- Probleme beim Arbeiten im Feature „Benutzerdefinierte Domänen“ im App Service-Mandantenportal wurden gelöst.

- Es wird verhindert, dass Kunden reservierte Administratornamen während des Setups verwenden.

- App Service-Bereitstellung mit **in die Domäne eingebundenem** Dateiserver wurde ermöglicht.

- Der Abruf des Azure Stack-Stammzertifikats im Skript und jetzt das Überprüfen des Stammzertifikats im App Service-Installationsprogramm wurden verbessert.

- Der Fehler, dass beim Löschen eines Abonnements, dass Ressourcen im Microsoft.Web-Namespace enthielt, ein falscher Status an den Azure Resource Manager zurückgegeben wurde, wurde behoben.

### <a name="known-issues-with-the-deployment-process"></a>Bekannte Probleme mit dem Bereitsellungsprozess

- Fehler beim Überprüfen des Zertifikats

Einige Kunden haben Probleme beim Zuteilen von Zertifikaten für den App Service-Installer bei der Bereitstellung auf einem integrierten System aufgrund einer zu restriktiven Überprüfung durch das Installationsprogramm. Der App Service-Installer wurde neu veröffentlicht, daher sollten Kunden [den aktualisierten Installer herunterladen](https://aka.ms/appsvconmasinstaller). Wenn Sie weiterhin Probleme bei der Überprüfung von Zertifikaten mit dem aktualisierten Installer haben, wenden Sie sich an den Support.

- Problem beim Abrufen von Azure Stack-Stammzertifikaten aus dem integrierten System.

Ein Fehler in der Datei „Get-AzureStackRootCert.ps1“ führte dazu, dass Kunden das Azure Stack-Stammzertifikat nicht abrufen konnten, wenn das Skript auf einem Computer ausgeführt wurde, auf dem das Stammzertifikat nicht installiert ist. Das Skript wurde nun ebenfalls neu veröffentlicht, um dieses Problem zu beheben. Kunden werden aufgefordert, [die aktualisierten Hilfsprogrammskripts herunterzuladen](https://aka.ms/appsvconmashelpers). Wenn Sie weiterhin Probleme beim Abrufen des Stammzertifikats mit dem aktualisierten Skript haben, wenden Sie sich an den Support.

### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Es sind keine Probleme beim Aktualisieren mit Update 1 für Azure App Service in Azure Stack bekannt.

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Slottausch funktioniert nicht

Die Funktion zum Austauschen von Slots funktioniert in dieser Version nicht. Um die Funktionalität wiederherzustellen, führen Sie diese Schritte aus:

1. Ändern Sie die ControllerNSG-Netzwerksicherheitsgruppe, um Remotedesktopverbindungen zu den App Service-Controllerinstanzen **zuzulassen**. Ersetzen Sie „AppService.local“ durch den Namen der Ressourcengruppe, in der Sie den App Service bereitgestellt haben.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Navigieren Sie zur **CN0-VM** unter „Virtuelle Computer“ im Azure Stack-Administratorportal, und klicken Sie auf **Verbinden**, um eine Remotedesktopsitzung mit der Controllerinstanz zu öffnen. Verwenden Sie die bei der Bereitstellung von App Service angegebenen Anmeldeinformationen.
3. Starten Sie **PowerShell als Administrator**, und führen Sie folgendes Skript aus:

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Schließen Sie die Remotedesktopsitzung.
5. Setzen Sie die ControllerNSG-Netzwerksicherheitsgruppe zurück, um Remotedesktopverbindungen zu den App Service-Controllerinstanzen **abzulehnen**. Ersetzen Sie „AppService.local“ durch den Namen der Ressourcengruppe, in der Sie den App Service bereitgestellt haben.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Worker können den Dateiserver nicht erreichen, wenn der App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie dazu im Admin-Portal zur WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

- Quelle: Beliebig
- Quellportbereich: *
- Ziel: IP-Adressen
- IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
- Zielportbereich: 445
- Protokoll: TCP
- Aktion: ZULASSEN
- Priorität: 700
- Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

Lesen Sie die Dokumentation in den Versionshinweisen [Azure Stack-Update 1802](azure-stack-update-1903.md).

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).

<!-- Update_Description: wording update -->