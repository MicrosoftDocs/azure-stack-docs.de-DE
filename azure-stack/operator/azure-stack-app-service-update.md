---
title: Aktualisieren von Azure App Service in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure App Service in Azure Stack Hub aktualisieren.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: cf161a3c756042d60a112a7ca2c794e3bb72b297
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75924342"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Aktualisieren von Azure App Service in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme und Azure Stack Hub Development Kit*

> [!IMPORTANT]
> Wenden Sie Update 1910 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie mit der Bereitstellung von Azure App Service 1.8 beginnen.

In diesem Artikel erläutern wir, wie Sie ein Upgrade des [Azure App Service-Ressourcenanbieters](azure-stack-app-service-overview.md) durchführen, der in einer mit dem Internet verbundenen Azure Stack Hub-Umgebung bereitgestellt ist.

> [!IMPORTANT]
> Stellen Sie vor dem Ausführen des Upgrades sicher, dass Sie die [Bereitstellung von Azure App Service in Azure Stack Hub](azure-stack-app-service-deploy.md) bereits abgeschlossen haben. Lesen Sie auch die [Versionshinweise](azure-stack-app-service-release-notes-update-eight.md) für Version 1.8, um weitere Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen zu erhalten, die ggf. für Ihre Bereitstellung relevant sind.

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

1. Laden Sie das [Azure App Service-Installationsprogramm](https://aka.ms/appsvcupdate8installer) herunter.

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
            - Geben Sie **Dienstprinzipal-ID**, **Zertifikatsdatei** und **Kennwort** ein, und wählen Sie **Verbinden** aus.

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

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Verwaltungsvorgänge für Azure App Service in Azure Stack Hub:

* [Planen zusätzlicher Kapazität](azure-stack-app-service-capacity-planning.md)
* [Hinzufügen zusätzlicher Kapazität](azure-stack-app-service-add-worker-roles.md)
