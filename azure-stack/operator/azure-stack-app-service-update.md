---
title: Aktualisieren von Azure App Service in Azure Stack Hub
description: Erfahren Sie, wie Sie Azure App Service in Azure Stack Hub aktualisieren.
author: BryanLa
ms.topic: article
ms.date: 05/05/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 9ab446785d463358b3d04850b5d08ff9410a40d5
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489656"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Aktualisieren von Azure App Service in Azure Stack Hub

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

::: zone pivot="state-connected"
In diesem Artikel erfahren Sie, wie Sie ein Upgrade des [Azure App Service-Ressourcenanbieters](azure-stack-app-service-overview.md) durchführen, der in einer mit dem Internet verbundenen Azure Stack Hub-Umgebung bereitgestellt wird.

> [!IMPORTANT]
> Vor der Durchführung des Upgrades müssen Sie die [Bereitstellung von Azure App Service in Azure Stack Hub](azure-stack-app-service-deploy.md) abschließen. 

## <a name="run-the-azure-app-service-resource-provider-installer"></a>Ausführen des Installationsprogramms für den Azure App Service-Ressourcenanbieter

Während dieses Vorgangs führt das Upgrade Folgendes aus:

* Ermitteln der vorherigen Bereitstellung von Azure App Service
* Vorbereiten aller Updatepakete und neuen Versionen aller bereitzustellenden OSS-Bibliotheken
* Hochladen in den Speicher
* Aktualisieren aller Azure App Service-Rollen (Controller, Verwaltung, Front-End, Herausgeber und Worker)
* Aktualisieren der Azure App Service-Skalierungsgruppendefinitionen
* Aktualisieren des Azure App Service-Ressourcenanbietermanifests

> [!IMPORTANT]
> Das Azure App Service-Installationsprogramm muss auf einem Computer ausgeführt werden, der den Azure Resource Manager-Endpunkt des Azure Stack Hub-Administrators erreichen kann.

Gehen Sie folgendermaßen vor, um ein Upgrade der Bereitstellung von Azure App Service in Azure Stack Hub durchzuführen:

1. Laden Sie das [Azure App Service-Installationsprogramm](https://aka.ms/appsvcupdateQ2installer) herunter.

2. Führen Sie „appservice.exe“ als Administrator aus.

    ![Azure App Service-Installationsprogramm][1]

3. Klicken Sie auf **Azure App Service bereitstellen oder Upgrade auf die neueste Version durchführen**.

4. Lesen und akzeptieren Sie die Lizenzbedingungen von Microsoft-Software, und klicken Sie dann auf **Weiter**.

5. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und klicken Sie dann auf **Weiter**.

6. Stellen Sie sicher, dass die Informationen zum Azure Resource Manager-Endpunkt für Azure Stack Hub und zu den Active Directory-Mandanten richtig sind. Wenn Sie während der ASDK-Bereitstellung die Standardeinstellungen verwendet haben, können Sie hier die Standardwerte übernehmen. Wenn Sie die Optionen bei der Bereitstellung von Azure Stack Hub jedoch angepasst haben, müssen Sie die Werte in diesem Fenster bearbeiten. Wenn Sie beispielsweise das Domänensuffix *mycloud.com* verwenden, muss der Azure Resource Manager-Endpunkt für Azure Stack Hub zu *management.region.mycloud.com* geändert werden. Nachdem Sie Ihre Informationen bestätigt haben, klicken Sie auf **Weiter**.

    ![Azure Stack Hub-Cloudinformationen][2]

7. Auf der nächsten Seite:

    1. Wählen Sie die Verbindungsmethode aus, die Sie verwenden möchten: **Anmeldeinformationen** oder **Dienstprinzipal**.
        - **Credential**
            - Wenn Sie Azure Active Directory (Azure AD) verwenden, geben Sie das Azure AD-Administratorkonto, das Sie bei der Bereitstellung von Azure Stack Hub angegeben haben, und das zugehörige Kennwort ein. Wählen Sie **Verbinden**.
            - Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden, geben Sie Ihr Administratorkonto an. Beispiel: cloudadmin@azurestack.local. Geben Sie Ihr Kennwort ein, und wählen Sie dann **Verbinden** aus.
        - **Dienstprinzipal**
            - Der Dienstprinzipal, den Sie verwenden, **muss** über Rechte als **Besitzer** für das **Standardanbieterabonnement** verfügen.
            - Geben Sie die **Dienstprinzipal-ID**, die **Zertifikatsdatei** und das **Kennwort** ein, und wählen Sie **Verbinden** aus.

    1. Wählen Sie in **Azure Stack Hub-Abonnements** das **Standardanbieterabonnement** aus.    Azure App Service auf Azure Stack Hub **muss** im **Standardanbieterabonnement** bereitgestellt werden.

    1. Wählen Sie in **Azure Stack Hub-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.

    1. Wenn eine vorhandene Azure App Service-Bereitstellung erkannt wird, werden Ressourcengruppe und Speicherkonto aufgefüllt und sind nicht verfügbar.

      ![Azure App Service-Installation erkannt][3]

8. Auf der Zusammenfassungsseite:
   1. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.
   2. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.
   3. Um das Upgrade zu starten, klicken Sie auf **Weiter**.

       ![Azure App Service-Upgradezusammenfassung][4]

9. Upgradestatusseite:
    1. Verfolgen Sie den Upgradestatus. Die Dauer des Upgrades von Azure App Service in Azure Stack Hub variiert abhängig von der Anzahl der bereitgestellten Rolleninstanzen.
    2. Klicken Sie nach erfolgreichem Abschluss des Upgrades auf **Beenden**.

        ![Azure App Service-Upgradestatus][5]
::: zone-end

::: zone pivot="state-disconnected"
In diesem Artikel erfahren Sie, wie Sie ein Upgrade des [Azure App Service-Ressourcenanbieters](azure-stack-app-service-overview.md) durchführen, der in einer Azure Stack Hub-Umgebung bereitgestellt wird, auf die Folgendes zutrifft:

* Sie ist nicht mit dem Internet verbunden.
* Sie wird mit Active Directory Federation Services (AD FS) geschützt.

> [!IMPORTANT]
> Vor der Durchführung des Upgrades müssen Sie die [Bereitstellung von Azure App Service in Azure Stack Hub in einer getrennten Umgebung](./azure-stack-app-service-deploy.md?pivots=state-disconnected&view=azs-2002) abschließen. 

## <a name="run-the-app-service-resource-provider-installer"></a>Ausführen des Installationsprogramms für den App Service-Ressourcenanbieter

Um den App Service-Ressourcenanbieter in einer Azure Stack Hub-Umgebung zu aktualisieren, müssen Sie die folgenden Aufgaben ausführen:

1. Laden Sie das [Azure App Service-Installationsprogramm](https://aka.ms/appsvcupdate8installer) herunter.
2. Erstellen Sie ein Paket für das Offlineupgrade.
3. Führen Sie das App Service-Installationsprogramm („appservice.exe“) aus, und schließen Sie das Upgrade ab.

Während dieses Vorgangs führt das Upgrade Folgendes aus:

* Ermitteln der alten Bereitstellung von App Service
* Hochladen in Storage
* Aktualisieren aller App Service-Rollen (Controller, Verwaltung, Front-End, Herausgeber und Worker)
* Aktualisieren der App Service-Skalierungsgruppendefinitionen
* Aktualisieren des App Service-Ressourcenanbietermanifests

## <a name="create-an-offline-upgrade-package"></a>Erstellen eines Pakets für das Offlineupgrade

Um App Service in einer nicht verbundenen Umgebung zu aktualisieren, müssen Sie auf einem Computer, der über eine Internetverbindung verfügt, zunächst ein Offlineupgradepaket erstellen.

1. Führen Sie „appservice.exe“ als Administrator aus.

    ![Azure App Service-Installationsprogramm][11]

2. Klicken Sie auf **Erweitert** > **Offlinepaket erstellen**.

    ![Azure App Service-Installationsprogramm – erweitert][12]

3. Das Azure App Service-Installationsprogramm erstellt ein Offlineupgradepaket und zeigt den Pfad zu diesem an.  Klicken Sie auf **Ordner öffnen**, um den Ordner im Datei-Explorer zu öffnen.

4. Kopieren Sie das Installationsprogramm („AppService.exe“) und das Offlineinstallationspaket auf einen Computer, der eine Verbindung mit Azure Stack Hub herstellen kann.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Abschließen des Upgrades von App Service in Azure Stack Hub

> [!IMPORTANT]
> Das Azure App Service-Installationsprogramm muss auf einem Computer ausgeführt werden, der den Azure Resource Manager-Endpunkt des Azure Stack Hub-Administrators erreichen kann.

1. Führen Sie „appservice.exe“ als Administrator aus.

    ![Azure App Service-Installationsprogramm][11]

2. Klicken Sie auf **Erweitert** > **Vollständige Offline-Installation oder -Aktualisierung**.

    ![Azure App Service-Installationsprogramm – erweitert][12]

3. Navigieren Sie zum Speicherort des zuvor erstellten Offlineupgradepakets, und klicken Sie dann auf **Weiter**.

4. Lesen und akzeptieren Sie die Lizenzbedingungen von Microsoft-Software, und klicken Sie dann auf **Weiter**.

5. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und klicken Sie dann auf **Weiter**.

6. Stellen Sie sicher, dass die Informationen zum Azure Resource Manager-Endpunkt für Azure Stack Hub und zu den Active Directory-Mandanten richtig sind. Wenn Sie während der Bereitstellung mit dem Azure Stack Development Kit die Standardeinstellungen verwendet haben, können Sie hier die Standardwerte übernehmen. Wenn Sie die Optionen bei der Bereitstellung von Azure Stack Hub jedoch angepasst haben, müssen Sie die Werte in diesem Fenster bearbeiten. Wenn Sie beispielsweise das Domänensuffix *mycloud.com* verwenden, muss der Azure Resource Manager-Endpunkt für Azure Stack Hub zu *management.region.mycloud.com* geändert werden. Nachdem Sie Ihre Informationen bestätigt haben, klicken Sie auf **Weiter**.

    ![Azure Stack Hub-Cloudinformationen][13]

7. Auf der nächsten Seite:

   1. Wählen Sie die Verbindungsmethode aus, die Sie verwenden möchten: **Anmeldeinformationen** oder **Dienstprinzipal**.
        - **Credential**
            - Wenn Sie Azure Active Directory (Azure AD) verwenden, geben Sie das Azure AD-Administratorkonto, das Sie bei der Bereitstellung von Azure Stack Hub angegeben haben, und das zugehörige Kennwort ein. Wählen Sie **Verbinden**.
            - Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden, geben Sie Ihr Administratorkonto an. Beispiel: cloudadmin@azurestack.local. Geben Sie Ihr Kennwort ein, und wählen Sie dann **Verbinden** aus.
        - **Dienstprinzipal**
            - Der Dienstprinzipal, den Sie verwenden, **muss** über Rechte als **Besitzer** für das **Standardanbieterabonnement** verfügen.
            - Geben Sie die **Dienstprinzipal-ID**, die **Zertifikatsdatei** und das **Kennwort** ein, und wählen Sie **Verbinden** aus.

   1. Wählen Sie in **Azure Stack Hub-Abonnements** das **Standardanbieterabonnement** aus.  Azure App Service auf Azure Stack Hub **muss** im **Standardanbieterabonnement** bereitgestellt werden.

   1. Wählen Sie in **Azure Stack Hub-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.
   
   1. Wenn eine vorhandene App Service-Bereitstellung erkannt wird, werden Ressourcengruppe und Speicherkonto aufgefüllt und ausgegraut.

      ![Azure App Service-Installation erkannt][14]
8. Auf der Zusammenfassungsseite:
   1. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.
   2. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.
   3. Um das Upgrade zu starten, klicken Sie auf **Weiter**.

       ![Azure App Service-Upgradezusammenfassung][15]

9. Upgradestatusseite:
    1. Verfolgen Sie den Upgradestatus. Die Dauer des Upgrades von App Service in Azure Stack Hub variiert abhängig von der Anzahl der bereitgestellten Rolleninstanzen.
    2. Klicken Sie nach erfolgreichem Abschluss des Upgrades auf **Beenden**.

        ![Azure App Service-Upgradestatus][16]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Verwaltungsvorgänge für Azure App Service in Azure Stack Hub:

* [Planen zusätzlicher Kapazität](azure-stack-app-service-capacity-planning.md)
* [Hinzufügen zusätzlicher Kapazität](azure-stack-app-service-add-worker-roles.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

[11]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[12]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[13]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[14]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[15]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[16]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png
