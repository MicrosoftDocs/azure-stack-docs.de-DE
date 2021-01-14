---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 6'
description: Versionshinweise zu Update 6 für App Service in Azure Stack Hub, z. B. zu neuen Features, Fehlerbehebungen und bekannten Problemen.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 689f5eaccb7dc30ce7888e9fffdbbd62ac9b5267
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974369"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>App Service in Azure Stack Hub: Versionshinweise zu Update 6

In diesen Versionshinweisen werden die neuen Features, Fehlerbehebungen und bekannten Probleme in Update 6 für Azure App Service in Azure Stack Hub beschrieben. Die bekannten Probleme sind in zwei Abschnitte unterteilt: Probleme, die sich direkt auf den Upgradeprozess beziehen, und Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1904 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.6 bereit.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 6 für App Service in Azure Stack Hub lautet **82.0.1.50**.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie vor Beginn der Bereitstellung die Informationen unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack Hub auf 1.6 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack Hub-Administratorportal bereit sind.

- Sichern Sie die App Service-Geheimnisse mithilfe der App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

  > [!Important]
  > Cloudoperatoren sind für die Verwaltung und den Betrieb des Dateiservers und von SQL Server verantwortlich.  Der Ressourcenanbieter verwaltet diese Ressourcen nicht.  Der Cloudoperator ist für das Sichern der App Service-Datenbanken und der Mandanten-Inhaltsdateifreigabe verantwortlich.

- Beziehen Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.1**) über den Azure Stack Hub Marketplace.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 6 für Azure App Service in Azure Stack Hub enthält die folgenden Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Konsistent mit Azure Stack Hub-Portal-SDK-Version.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.12299**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende App-Frameworks und Tools**:

  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - Kudu auf 81.10329.3844 aktualisiert

- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [25. April 2019 – KB4493473 (Betriebssystembuild 14393.2941)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie die [Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und synchronisieren, damit es bei einem Datenbankfailover nicht zu Dienstausfällen kommt.

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dies ist in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack Hub dargestellt.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Administratorportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

* Quelle: Any
* Quellportbereich: *
* Ziel: IP-Adressen
* IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
* Zielportbereich: 445
* Protokoll: TCP
* Aktion: Allow
* Priorität: 700
* Name: Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack Hub verwenden

Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub 1908](./release-notes.md?view=azs-1908&preserve-view=true).

## <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Bekannte Probleme für Mandanten, die Anwendungen in Azure App Service in Azure Stack Hub bereitstellen

- Bereitstellungscenter ist abgeblendet bzw. nicht verfügbar.

    Mandanten können das Bereitstellungscenter noch nicht nutzen. Dieses Feature wurde Ende 2018 in der Public Cloud veröffentlicht. Mandanten können weiterhin die standardmäßigen Bereitstellungsmethoden (FTP, Web Deploy, Git usw.) über das Portal, über die Befehlszeilenschnittstelle und über PowerShell verwenden.

- Bereitstellungsoptionen (klassisch) der Benutzeroberfläche sowie Portaloptionen für Bereitstellungsanmeldeinformationen sind nicht verfügbar.

    Um zu den Bereitstellungsoptionen und den Anmeldeinformationen für die Azure Stack Hub-Bereitstellung zu gelangen, müssen Mandanten unter Verwendung des folgenden URL-Formats auf das Portal zugreifen und anschließend wie gewohnt zu ihren Apps navigieren: `https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;/?websitesExtension_oldvsts=true`. Für das ASDK lautet die URL `https://portal.local.azurestack.external/?websitesExtension_oldvsts=true`.

- Die Azure Functions-Überwachung zeigt im Portal ständig „Wird geladen“ an.

    Wenn Sie versuchen, einzelne Funktionen zu überwachen, wird im Benutzerportal kein Aufrufprotokoll, keine Erfolgsanzahl oder keine Fehleranzahl angezeigt. Um diese Funktion erneut zu aktivieren, wechseln Sie zu Ihrer **Funktions-App**, wechseln Sie zu **Plattformfeatures**, und wechseln Sie zu **Anwendungseinstellungen**.  Fügen Sie eine neue App-Einstellung mit dem Namen **AzureWebJobsDashboard** hinzu, und legen Sie den Wert auf denselben Wert wie unter „AzureWebJobsStorage“ fest. Wechseln Sie dann zur Überwachungsansicht für Ihre Funktion, um die Überwachungsinformationen anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über Azure App Service und Azure Functions in Azure Stack Hub](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
