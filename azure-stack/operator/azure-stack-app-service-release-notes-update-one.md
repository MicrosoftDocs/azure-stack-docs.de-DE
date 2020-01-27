---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 1 | Microsoft-Dokumentation'
description: Erfahren Sie mehr über Verbesserungen, Fehlerbehebungen und bekannte Probleme in Update 1 für App Service in Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: f36a94bbc442913826571fdacd1303b010212d4c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880361"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>App Service in Azure Stack Hub: Versionshinweise zu Update 1

In diesen Versionshinweisen werden die Verbesserungen, Fehlerbehebungen und bekannten Probleme in Update 1 für Azure App Service in Azure Stack Hub beschrieben. Die bekannten Probleme sind in drei Abschnitte unterteilt: Probleme in Bezug auf die Bereitstellung, Probleme mit dem Updatevorgang und Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1802 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.2 bereit.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 1 für App Service in Azure Stack Hub lautet **69.0.13698.9**.

### <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Neue Bereitstellungen von Azure App Service in Azure Stack Hub erfordern jetzt aufgrund einer verbesserten SSO-Verarbeitung für Kudu in Azure App Service ein [Platzhalterzertifikat mit drei Antragstellern](azure-stack-app-service-before-you-get-started.md#get-certificates). Der neue Antragsteller lautet **\*.sso.appservice.\<Region\>.\<Domänenname\>.\<Erweiterung\>**

Lesen Sie vor Beginn der Bereitstellung die [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 1 für Azure App Service in Azure Stack Hub enthält folgende Verbesserungen und Fehlerbehebungen:

- **Hochverfügbarkeit von Azure App Service**: Das Azure Stack Hub-Update 1802 ermöglicht die Bereitstellung von Workloads in mehreren Fehlerdomänen. Durch die übergreifende Bereitstellung ist die App Service-Infrastruktur fehlertolerant. Standardmäßig weisen alle neuen Bereitstellungen von Azure App Service diese Funktion auf. Für Bereitstellungen, die vor Anwendung des Azure Stack Hub-Updates 1802 durchgeführt wurden, sollten Sie jedoch die [App Service-Dokumentation für Fehlerdomänen](azure-stack-app-service-before-you-get-started.md) lesen.

- **In vorhandenem virtuellem Netzwerk bereitstellen**: Kunden können jetzt App Service in Azure Stack Hub in einem vorhandenen virtuellen Netzwerk bereitstellen. Die Bereitstellung in einem vorhandenen virtuellen Netzwerk ermöglicht Kunden das Herstellen einer Verbindung mit der SQL Server-Instanz und dem Dateiserver (für Azure App Service erforderlich) über private Ports. Während der Bereitstellung können die Kunden auswählen, ob sie die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchführen möchten. Vor der Bereitstellung [müssen sie jedoch Subnetze für die Verwendung durch App Service erstellen](azure-stack-app-service-before-you-get-started.md#virtual-network).

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Konsistent mit Azure Stack Hub-Portal-SDK-Version.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.11388**.

- **Updates für folgende Anwendungsframeworks und Tools**:
    - Unterstützung für **.NET Core 2.0** hinzugefügt.
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

  - Unterstützung für das **Nur HTTPS**-Feature wurde innerhalb des Features „Benutzerdefinierte Domäne“ im App Service-Benutzerportal hinzugefügt.

  - Überprüfung der Speicherverbindung in der Auswahl von benutzerdefiniertem Speicher für Azure Functions hinzugefügt.

#### <a name="fixes"></a>Fehlerbehebungen

- Beim Erstellen eines Offlinebereitstellungspakets erhalten Kunden keine „Zugriff verweigert“-Fehlermeldung mehr, wenn sie den Ordner vom App Service-Installationsprogramm aus öffnen.

- Probleme beim Arbeiten im Feature „Benutzerdefinierte Domänen“ im App Service-Benutzerportal wurden gelöst.

- Es wird verhindert, dass Kunden reservierte Administratornamen während des Setups verwenden.

- Die App Service-Bereitstellung mit **in die Domäne eingebundenem** Dateiserver wurde ermöglicht.

- Der Abruf des Azure Stack Hub-Stammzertifikats im Skript wurde verbessert, und die Möglichkeit zum Überprüfen des Stammzertifikats im App Service-Installationsprogramm wurde hinzugefügt.

- Der Fehler, dass beim Löschen eines Abonnements, dass Ressourcen im Microsoft.Web-Namespace enthielt, ein falscher Status an den Azure Resource Manager zurückgegeben wurde, wurde behoben.

### <a name="known-issues-with-the-deployment-process"></a>Bekannte Probleme mit dem Bereitsellungsprozess

- Fehler beim Überprüfen des Zertifikats.

    Einige Kunden haben bei der Bereitstellung auf einem integrierten System aufgrund einer zu restriktiven Überprüfung durch das Installationsprogramm Probleme beim Zuteilen von Zertifikaten für das App Service-Installationsprogramm festgestellt. Das App Service-Installationsprogramm wurde neu veröffentlicht. Daher sollten Kunden [das aktualisierte Installationsprogramm herunterladen](https://aka.ms/appsvconmasinstaller). Wenn Sie weiterhin Probleme bei der Überprüfung von Zertifikaten mit dem aktualisierten Installer haben, wenden Sie sich an den Support.

- Problem beim Abrufen von Azure Stack Hub-Stammzertifikaten aus dem integrierten System.

    Ein Fehler in der Datei „Get-AzureStackRootCert.ps1“ führte dazu, dass Kunden das Azure Stack Hub-Stammzertifikat nicht abrufen konnten, wenn das Skript auf einem Computer ausgeführt wurde, auf dem das Stammzertifikat nicht installiert ist. Das Skript wurde nun ebenfalls erneut veröffentlicht. Dadurch wird das Problem gelöst. [Laden Sie hier die aktualisierten Hilfsskripts herunter](https://aka.ms/appsvconmashelpers). Wenn Sie weiterhin Probleme beim Abrufen des Stammzertifikats mit dem aktualisierten Skript haben, wenden Sie sich an den Support.

### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Es sind keine Probleme beim Aktualisieren mit Update 1 für Azure App Service in Azure Stack Hub bekannt.

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Der Austausch von Slots funktioniert nicht.

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

2. Navigieren Sie zur **CN0-VM** unter „Virtuelle Computer“ im Azure Stack Hub-Administratorportal, und klicken Sie auf **Verbinden**, um eine Remotedesktopsitzung mit der Controllerinstanz zu öffnen. Verwenden Sie die bei der Bereitstellung von App Service angegebenen Anmeldeinformationen.
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

Wenn Sie sich für die Bereitstellung in einem vorhandenen virtuellen Netzwerk und zur Verwendung einer internen IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Administratorportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

- Quelle: Any
- Quellportbereich: *
- Ziel: IP-Adressen
- IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
- Zielportbereich: 445
- Protokoll: TCP
- Aktion: Allow
- Priorität: 700
- Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack Hub verwenden

Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub 1802](azure-stack-update-1903.md).

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack Hub](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack Hub finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
