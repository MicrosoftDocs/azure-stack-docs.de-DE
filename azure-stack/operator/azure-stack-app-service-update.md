---
title: Aktualisieren von Azure App Service in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure App Service in Azure Stack aktualisieren.
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
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/28/2019
ms.openlocfilehash: 768a6270021d6a87be3d2d28508288836ffadd29
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808260"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Aktualisieren von Azure App Service in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!IMPORTANT]
> Wenden Sie Update 1904 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.7 bereit.

In diesem Artikel erläutern wir, wie Sie den in einer mit dem Internet verbundenen Azure Stack-Umgebung bereitgestellten [App Service-Ressourcenanbieter](azure-stack-app-service-overview.md) aktualisieren.

> [!IMPORTANT]
> Stellen Sie vor dem Ausführen des Upgrades sicher, dass Sie die [Bereitstellung von Azure App Service in Azure Stack](azure-stack-app-service-deploy.md) bereits abgeschlossen haben. Lesen Sie auch die [Versionshinweise](azure-stack-app-service-release-notes-update-seven.md) zur Version 1.7, um weitere Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen zu erhalten, die sich auf die Bereitstellung auswirken können.

## <a name="run-the-app-service-resource-provider-installer"></a>Ausführen des Installationsprogramms für den App Service-Ressourcenanbieter

Während dieses Vorgangs führt das Upgrade Folgendes aus:

* Ermitteln der vorherigen Bereitstellung von App Service
* Vorbereiten aller Updatepakete und neuen Versionen aller bereitzustellenden OSS-Bibliotheken
* Hochladen in den Speicher
* Aktualisieren aller App Service-Rollen (Controller, Verwaltung, Front-End, Herausgeber und Worker)
* Aktualisieren der App Service-Skalierungsgruppendefinitionen
* Aktualisieren des App Service-Ressourcenanbietermanifests

> [!IMPORTANT]
> Das App Service-Installationsprogramm muss auf einem Computer ausgeführt werden, der den Azure Resource Manager-Endpunkt des Azure Stack-Administrators erreichen kann.

Gehen Sie folgendermaßen vor, um die Bereitstellung von App Service in Azure Stack zu aktualisieren:

1. Laden Sie das [App Service-Installationsprogramm](https://aka.ms/appsvcupdate7installer) herunter.

2. Führen Sie „appservice.exe“ als Administrator aus.

    ![App Service-Installationsprogramm][1]

3. Klicken Sie auf **Stellen Sie App Service bereit, oder führen Sie ein Upgrade auf die neueste Version durch.**

4. Lesen und akzeptieren Sie die Lizenzbedingungen von Microsoft-Software, und klicken Sie dann auf **Weiter**.

5. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und klicken Sie dann auf **Weiter**.

6. Stellen Sie sicher, dass die Informationen über den Azure Resource Manager-Endpunkt für Azure Stack und den Active Directory-Mandanten richtig sind. Wenn Sie während der ASDK-Bereitstellung die Standardeinstellungen verwendet haben, können Sie hier die Standardwerte übernehmen. Wenn Sie die Optionen bei der Bereitstellung von Azure Stack jedoch angepasst haben, müssen Sie die Werte in diesem Fenster anpassen. Wenn Sie beispielsweise das Domänensuffix *mycloud.com* verwenden, muss der Azure Resource Manager-Endpunkt für Azure Stack in *management.region.mycloud.com* geändert werden. Nachdem Sie Ihre Informationen bestätigt haben, klicken Sie auf **Weiter**.

    ![Azure Stack-Cloudinformationen][2]

7. Auf der nächsten Seite:

   1. Klicken Sie neben dem Feld **Azure Stack-Abonnements** auf die Schaltfläche **Verbinden**.
        * Wenn Sie Azure Active Directory (Azure AD) verwenden, geben Sie das Azure AD-Administratorkonto, das Sie bei der Bereitstellung von Azure Stack angegeben haben, und das zugehörige Kennwort ein. Klicken Sie auf **Anmelden**.
        * Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden, geben Sie Ihr Administratorkonto an. Beispiel: *cloudadmin\@azurestack.local*. Geben Sie Ihr Kennwort ein, und klicken Sie auf **Anmelden**.
   2. Wählen Sie im Feld **Azure Stack-Abonnements** das **Standardabonnement des Anbieters** aus.
   3. Wählen Sie im Feld **Azure Stack-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.
   4. Wenn eine vorhandene App Service-Bereitstellung erkannt wird, werden Ressourcengruppe und Speicherkonto aufgefüllt und sind nicht verfügbar.
   5. Klicken Sie auf **Weiter**, um die Zusammenfassung des Upgrades zu überprüfen.

      ![App Service-Installation erkannt][3]

8. Auf der Zusammenfassungsseite:
   1. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.
   2. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.
   3. Um das Upgrade zu starten, klicken Sie auf **Weiter**.

       ![App Service-Upgradezusammenfassung][4]

9. Upgradestatusseite:
    1. Verfolgen Sie den Upgradestatus. Die Dauer des Upgrades von App Service in Azure Stack variiert abhängig von der Anzahl der bereitgestellten Rolleninstanzen.
    2. Klicken Sie nach erfolgreichem Abschluss des Upgrades auf **Beenden**.

        ![App Service-Upgradestatus][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Verwaltungsvorgänge für App Service unter Azure Stack:

* [Planen zusätzlicher Kapazität](azure-stack-app-service-capacity-planning.md)
* [Hinzufügen zusätzlicher Kapazität](azure-stack-app-service-add-worker-roles.md)
