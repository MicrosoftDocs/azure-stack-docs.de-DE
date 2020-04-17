---
title: 'Offlineaktualisierung von Azure App Service '
description: Detaillierter Leitfaden zum Offlineaktualisieren von Azure App Service in Azure Stack Hub
author: bryanla
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwe
ms.openlocfilehash: 65fb8a0adebb73fa8740f41d50eb83f869ac534a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700798"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack-hub"></a>Offlineaktualisieren von Azure App Service in Azure Stack Hub

> [!IMPORTANT]
> Wenden Sie das Update 1910 oder ein höheres Update auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit bereit, bevor Sie Azure App Service 1.8 bereitstellen.

Mithilfe der Anweisungen in diesem Artikel können Sie den [Azure App Service-Ressourcenanbieter](azure-stack-app-service-overview.md) in einer Azure Stack Hub-Umgebung aktualisieren, für die Folgendes gilt:

* Sie ist nicht mit dem Internet verbunden.
* Sie wird mit Active Directory Federation Services (AD FS) geschützt.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Ausführen des Upgrades, dass die [Bereitstellung von Azure App Service im Azure Stack Hub-Ressourcenanbieter](azure-stack-app-service-deploy-offline.md) abgeschlossen ist, und informieren Sie sich anhand der [Versionshinweise](azure-stack-app-service-release-notes-update-eight.md) für Version 1.8 über neue Funktionen, Fehlerbehebungen und bekannte Probleme, die ggf. für Ihre Bereitstellung relevant sind.

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

    ![Azure App Service-Installationsprogramm][1]

2. Klicken Sie auf **Erweitert** > **Offlinepaket erstellen**.

    ![Azure App Service-Installationsprogramm – erweitert][2]

3. Das Azure App Service-Installationsprogramm erstellt ein Offlineupgradepaket und zeigt den Pfad zu diesem an.  Klicken Sie auf **Ordner öffnen**, um den Ordner im Datei-Explorer zu öffnen.

4. Kopieren Sie das Installationsprogramm „AppService.exe“ und das Offlineupgradepaket auf den Azure Stack Hub-Hostcomputer.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Abschließen des Upgrades von App Service in Azure Stack Hub

> [!IMPORTANT]
> Das Azure App Service-Installationsprogramm muss auf einem Computer ausgeführt werden, der den Azure Resource Manager-Endpunkt des Azure Stack Hub-Administrators erreichen kann.

1. Führen Sie „appservice.exe“ als Administrator aus.

    ![Azure App Service-Installationsprogramm][1]

2. Klicken Sie auf **Erweitert** > **Vollständige Offline-Installation oder -Aktualisierung**.

    ![Azure App Service-Installationsprogramm – erweitert][2]

3. Navigieren Sie zum Speicherort des zuvor erstellten Offlineupgradepakets, und klicken Sie dann auf **Weiter**.

4. Lesen und akzeptieren Sie die Lizenzbedingungen von Microsoft-Software, und klicken Sie dann auf **Weiter**.

5. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und klicken Sie dann auf **Weiter**.

6. Stellen Sie sicher, dass die Informationen zum Azure Resource Manager-Endpunkt für Azure Stack Hub und zu den Active Directory-Mandanten richtig sind. Wenn Sie während der Bereitstellung mit dem Azure Stack Development Kit die Standardeinstellungen verwendet haben, können Sie hier die Standardwerte übernehmen. Wenn Sie die Optionen bei der Bereitstellung von Azure Stack Hub jedoch angepasst haben, müssen Sie die Werte in diesem Fenster bearbeiten. Wenn Sie beispielsweise das Domänensuffix *mycloud.com* verwenden, muss der Azure Resource Manager-Endpunkt für Azure Stack Hub zu *management.region.mycloud.com* geändert werden. Nachdem Sie Ihre Informationen bestätigt haben, klicken Sie auf **Weiter**.

    ![Azure Stack Hub-Cloudinformationen][3]

7. Auf der nächsten Seite:

   1. Wählen Sie die Verbindungsmethode aus, die Sie verwenden möchten: **Anmeldeinformationen** oder **Dienstprinzipal**.
        - **Credential**
            - Wenn Sie Azure Active Directory (Azure AD) verwenden, geben Sie das Azure AD-Administratorkonto, das Sie bei der Bereitstellung von Azure Stack Hub angegeben haben, und das zugehörige Kennwort ein. Wählen Sie **Verbinden**.
            - Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden, geben Sie Ihr Administratorkonto an. Beispiel: cloudadmin@azurestack.local. Geben Sie Ihr Kennwort ein, und wählen Sie dann **Verbinden** aus.
        - **Dienstprinzipal**
            - Der Dienstprinzipal, den Sie verwenden, **muss** über Rechte als **Besitzer** für das **Standardanbieterabonnement** verfügen.
            - Geben Sie **Dienstprinzipal-ID**, **Zertifikatsdatei** und **Kennwort** ein, und wählen Sie **Verbinden** aus.

   1. Wählen Sie in **Azure Stack Hub-Abonnements** das **Standardanbieterabonnement** aus.  Azure App Service auf Azure Stack Hub **muss** im **Standardanbieterabonnement** bereitgestellt werden.

   1. Wählen Sie in **Azure Stack Hub-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.
   
   1. Wenn eine vorhandene App Service-Bereitstellung erkannt wird, werden Ressourcengruppe und Speicherkonto aufgefüllt und ausgegraut.

      ![Azure App Service-Installation erkannt][4]
8. Auf der Zusammenfassungsseite:
   1. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.
   2. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.
   3. Um das Upgrade zu starten, klicken Sie auf **Weiter**.

       ![Azure App Service-Upgradezusammenfassung][5]

9. Upgradestatusseite:
    1. Verfolgen Sie den Upgradestatus. Die Dauer des Upgrades von App Service in Azure Stack Hub variiert abhängig von der Anzahl der bereitgestellten Rolleninstanzen.
    2. Klicken Sie nach erfolgreichem Abschluss des Upgrades auf **Beenden**.

        ![Azure App Service-Upgradestatus][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Administratorvorgänge für Azure App Service in Azure Stack Hub

* [Planen zusätzlicher Kapazität](azure-stack-app-service-capacity-planning.md)
* [Hinzufügen zusätzlicher Kapazität](azure-stack-app-service-add-worker-roles.md)
