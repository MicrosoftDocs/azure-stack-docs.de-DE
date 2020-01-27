---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 8 | Microsoft-Dokumentation'
description: Erfahren Sie, was in Update 8 für App Service in Azure Stack Hub enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 5da3ec3d838deabebbe70b04ad66d58241a42b5d
ms.sourcegitcommit: e47dc5fe9e59010ea3dbb9cb31abe15cfb821262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124700"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>App Service in Azure Stack Hub: Versionshinweise zu Update 8

In diesen Versionshinweisen werden die Verbesserungen und Fehlerbehebungen in Update 8 für Azure App Service in Azure Stack Hub sowie bekannte Probleme beschrieben. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie das Update 1910 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit bereit, bevor Sie Azure App Service 1.8 bereitstellen.


## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 8 für App Service in Azure Stack Hub lautet **86.0.2.13**.

### <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack auf 1.8 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack-Verwaltungsportal bereit sind.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

- Syndizieren Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.3**) über den Marketplace.

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 8 für Azure App Service in Azure Stack enthält folgende Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.12615**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools**:
  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - ASP.NET Core-Modul v2 13.1.19331.0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3.9
  - Kudu auf 85.11024.4154 aktualisiert
  - 3\.5.80916.15 (MSDeploy)
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git für Windows 2.19.1.0

- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [Dienstag, 10. Dezember 2019 – KB4530689 (BS-Build 14393.3384)](https://support.microsoft.com/help/4530689)

- **Unterstützung von verwalteten Datenträgern für neue Bereitstellungen**

Für alle neuen Bereitstellungen von Azure App Service in Azure Stack Hub werden verwaltete Datenträger für alle Virtual Machines und Virtual Machine Scale Sets verwendet.  Für vorhandene Bereitstellungen werden weiter nicht verwaltete Datenträger verwendet.

- **Durch Front-End-Lastenausgleich erzwungener TLS 1.2**

Ab diesem Update wird **TLS  1.2** für alle Anwendungen erzwungen.

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie [die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dies ist in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack dargestellt.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:
 * Quelle: Any
 * Quellportbereich: *
 * Ziel: IP-Adressen
 * IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
 * Zielportbereich: 445
 * Protokoll: TCP
 * Aktion: Allow
 * Priorität: 700
 * Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

Sehen Sie sich die Dokumentation in den [Versionshinweisen zum Azure Stack-Update 1907](azure-stack-release-notes-1907.md) an.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
