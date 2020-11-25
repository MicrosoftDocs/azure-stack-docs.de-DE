---
title: 'App Service in Azure Stack Hub 2020 Q3: Versionshinweise'
description: Erfahren Sie, was im Release 2020 Q3 für App Service in Azure Stack Hub enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 11/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: f9d575a91c55379326beaac2d76b3400172c8dd0
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785871"
---
# <a name="app-service-on-azure-stack-hub-2020-q3-release-notes"></a>App Service in Azure Stack Hub 2020 Q3: Versionshinweise

In diesen Versionshinweisen werden die Verbesserungen und Fehlerbehebungen in Azure App Service in Azure Stack Hub 2020 Q3 sowie bekannte Probleme beschrieben. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer für App Service in Azure Stack Hub 2020 Q3 lautet **89.0.2.15**.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack auf 2020 Q3 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal bereit sind.

- Sichern der App Service-Geheimnisse mithilfe der App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

  > [!Important]
  > Cloudoperatoren sind für die Verwaltung und den Betrieb des Dateiservers und von SQL Server verantwortlich.  Der Ressourcenanbieter verwaltet diese Ressourcen nicht.  Der Cloudoperator ist für das Sichern der App Service-Datenbanken und der Mandanten-Inhaltsdateifreigabe verantwortlich.

- Syndizieren Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.3**) über den Marketplace.

## <a name="updates"></a>Aktualisierungen

Update Q3 für Azure App Service in Azure Stack enthält folgende Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Hinzufügung von Vollbilderstellungmöglichkeit für Web- und Funktions-Apps.

- Neue Azure Functions-Portalumgebung, die mit Web-Apps konsistent ist.

- Aktualisierung der **Azure Functions-Runtime** auf **v1.0.13154**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools**:
  - ASP.NET Core 2.1.22
  - ASP.NET Core 2.2.14
  - ASP.NET Core 3.1.8
  - ASP.NET Core-Modul v2 13.1.19331.0
  - Azul OpenJDK
    - 8.42.0.23
    - 8.44.0.11
    - 11.35.15
    - 11.37.17
  - Curl 7.55.1
  - Git für Windows 2.28.0.1
  - 3\.5.90702.36 (MSDeploy)
  - NodeJS
    - 14.10.1
  - npm
    - 6.14.8
  - PHP 7.4.5
  - Tomcat
    - 8.5.47
    - 8.5.51
    - 9.0.273
    - 9.0.31
  - Update von Kudu auf 90.21005.4823

- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [Oktober 2020: Kumulatives Update für Windows Server 2016 für x64-basierte Systeme (KB4580346)](https://support.microsoft.com/help/4580346)
  - [September 2020: Wartungsstapelaktualisierung für Windows Server 2016 für x64-basierte Systeme (KB4576750)](https://support.microsoft.com/help/4576750)
  - Defender-Definition 1.325.755.0

- **Kumulative Updates für Windows Server werden jetzt im Rahmen der Bereitstellung und Aktualisierung auf Controllerrollen angewendet.**

## <a name="issues-fixed-in-this-release"></a>In diesem Release behobene Probleme

- Mandanten können nun einen App Service-Plan mithilfe von „Neu“ in der App Service-Planansicht im Mandantenportal erstellen.

- Mandanten können Zertifikate für ihre Anwendungen im Mandantenportal verwalten.

- Functions-Überwachung kann nun Daten von Speicherendpunkten abrufen, wobei TLS 1.2 erzwungen wird.

- Das Warten auf den Verwaltungsserver-Schritt während der Installation wurde aus dem „Cloud bereitstellen“-Schritt verschoben, um die Zuverlässigkeit von Bereitstellung und Upgrade zu verbessern.

- Problem, bei dem Worker die Integritätsprüfung nicht abschließen können, weil die Größe des für Worker verwendeten Ordners für Laufzeitprotokolldateien nach einem Fehler in der Bereinigungslogik die Kontingentgrenze verletzt.  Die Bereinigungslogik wurde in diesem Update korrigiert.

## <a name="pre-update-steps"></a>Schritte vor dem Update

Überprüfen Sie die [bekannten Probleme bei Updates](#known-issues-update), und ergreifen Sie alle vorgeschriebenen Maßnahmen.

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie [die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

## <a name="known-issues-update"></a>Bekannte Probleme (Update)

- In Fällen, in denen ein Kunde die appservice_hosting- und appservice_metering-Datenbanken in eine eigenständige Datenbank konvertiert hat, schlägt das Upgrade möglicherweise fehl, wenn Anmeldungen nicht erfolgreich zu eigenständigen Benutzern migriert wurden.

Bei Kunden, die die appservice_hosting- und appservice_metering-Datenbanken nach der Bereitstellung in eine eigenständige Datenbank konvertiert und die Datenbankanmeldungen nicht zu eigenständigen Benutzern migriert haben, können Upgradefehler auftreten.  

Kunden müssen das folgende Skript für den SQL-Server ausführen, der appservice_hosting und appservice_metering hostet, bevor sie die Azure App Service in Azure Stack Hub-Installation auf 2020 Q3 aktualisieren können.  **Dieses Skript ist nicht destruktiv und führt nicht zu Ausfallzeiten**.

Dieses Skript muss unter den folgenden Bedingungen ausgeführt werden.

- Es muss von einem Benutzer mit der Berechtigung „Systemadministrator“ ausgeführt werden, z. B. dem SQL-Konto „SA“.
- Wenn Sie SQL Always On verwenden, stellen Sie sicher, dass das Skript von der SQL-Instanz ausgeführt wird, die alle App Service-Anmeldungen im folgenden Format enthält:

    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - Alle WebWorker-Anmeldungen in der Form WebWorker_<instance ip address>

```sql
        USE appservice_hosting
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO

        USE appservice_metering
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
```

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dies ist in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack dargestellt.

  Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:
  - Quelle: Any
  - Quellportbereich: *
  - Ziel: IP-Adressen
  - IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
  - Zielportbereich: 445
  - Protokoll: TCP
  - Aktion: Allow
  - Priorität: 700
  - Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

- Benutzerdefinierte Domänen werden in getrennten Umgebungen nicht unterstützt.

App Service führt die Überprüfung des Domänenbesitzes für öffentliche DNS-Endpunkte durch. Daher werden benutzerdefinierte Domänen in getrennten Szenarien nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
