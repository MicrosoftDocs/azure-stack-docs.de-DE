---
title: App Service in Azure Stack – Versionshinweise zu Update 6 | Microsoft-Dokumentation
description: Erfahren Sie, was in Update 6 für App Service in Azure Stack enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: db403f68879efa9103e35bb3581801240c0d64d3
ms.sourcegitcommit: 1545e18a31cd715a12c7ddc3bcb173b41eb41730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2019
ms.locfileid: "67348720"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>App Service in Azure Stack: Versionshinweise zu Update 6

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und Fehlerbehebungen in Update 6 für Azure App Service in Azure Stack sowie bekannte Probleme beschrieben. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie das Update 1904 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit bereit, bevor Sie Azure App Service 1.6 bereitstellen.


## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 6 für App Service in Azure Stack lautet **82.0.1.50**.

### <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack auf 1.6 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack-Verwaltungsportal bereit sind.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

- Syndizieren Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.1**) über den Marketplace.

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Das Update 6 für Azure App Service in Azure Stack enthält folgende Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.12299**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools**:
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

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie [die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dies ist in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack dargestellt.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:
 * Quelle: Beliebig
 * Quellportbereich: *
 * Ziel: IP-Adressen
 * IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
 * Zielportbereich: 445
 * Protokoll: TCP
 * Aktion: ZULASSEN
 * Priorität: 700
 * Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

Lesen Sie die Dokumentation in den [Versionshinweisen zum Azure Stack-Update 1904](azure-stack-release-notes-1904.md).

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>Bekannte Probleme für Mandanten, die Anwendungen in Azure App Service in Azure Stack bereitstellen

- Bereitstellungscenter abgeblendet

Mandanten können das Bereitstellungscenter noch nicht nutzen. Dieses Feature wurde Ende 2018 in der Public Cloud veröffentlicht.  Mandanten können weiterhin die standardmäßigen Bereitstellungsmethoden (FTP, Web Deploy, Git usw.) über das Portal, über die Befehlszeilenschnittstelle und über PowerShell verwenden.

- Bereitstellungsoptionen der (klassischen) Benutzeroberfläche sowie Portaloptionen für Bereitstellungsanmeldeinformationen nicht verfügbar

Um zu den Bereitstellungsoptionen und den Anmeldeinformationen für die Bereitstellung zu gelangen, müssen Mandanten unter Verwendung des folgenden URL-Formats auf das Portal zugreifen und anschließend wie gewohnt zu ihren Anwendungen navigieren: https://portal.&lt ;*Region*&gt;.&lt;*FQDN*&gt; /?websitesExtension_oldvsts=true. ([https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) für ASDK).

- Die Azure Functions-Überwachung zeigt ständig „Laden“ im Portal an.

Wenn Sie versuchen, einzelne Funktionen zu überwachen, wird im Mandantenportal kein Aufrufprotokoll, keine Erfolgsanzahl oder keine Fehleranzahl angezeigt.  Um diese Funktion erneut zu aktivieren, wechseln Sie zu Ihrer **Funktions-App**, wechseln Sie zu **Plattformfeatures**, und wechseln Sie zu **Anwendungseinstellungen**.  Fügen Sie eine neue App-Einstellung mit den Namen **AzureWebJobsDashboard** hinzu, und legen Sie den Wert auf denselben Wert fest, wie er in „AzureWebJobsStorage“ festgelegt ist.  Wechseln Sie dann zur Überwachungsansicht für Ihre Funktion, wo dann die Überwachungsinformationen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
