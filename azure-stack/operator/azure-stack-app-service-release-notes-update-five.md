---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 5'
description: Erfahren Sie mehr über Verbesserungen, Fehlerbehebungen und bekannte Probleme in Update 5 für App Service in Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 3a4b2ae49e052731e372f4bfdaf8ff30c9b5490e
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974403"
---
# <a name="app-service-on-azure-stack-hub-update-5-release-notes"></a>App Service in Azure Stack Hub: Versionshinweise zu Update 5

In diesen Versionshinweisen werden die Verbesserungen, Fehlerbehebungen und bekannten Probleme in Update 5 für Azure App Service in Azure Stack Hub beschrieben. Die bekannten Probleme sind in drei Abschnitte unterteilt: Probleme in Bezug auf die Bereitstellung, Probleme mit dem Updatevorgang und Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1901 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.5 bereit.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 5 für App Service in Azure Stack Hub lautet **80.0.2.15**.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie vor Beginn der Bereitstellung die [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack Hub auf 1.5 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack Hub-Administratorportal bereit sind.

- Sichern Sie die App Service-Geheimnisse mithilfe der App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

  > [!Important]
  > Cloudoperatoren sind für die Verwaltung und den Betrieb des Dateiservers und von SQL Server verantwortlich.  Der Ressourcenanbieter verwaltet diese Ressourcen nicht.  Der Cloudoperator ist für das Sichern der App Service-Datenbanken und der Mandanten-Inhaltsdateifreigabe verantwortlich.

- Beziehen Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.1**) über den Azure Marketplace.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 5 für Azure App Service in Azure Stack Hub enthält folgende Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Konsistent mit Azure Stack Hub-Portal-SDK-Version.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.12205**.

- Updates für **Kudu-Tools**, um Gestaltungs- und Funktionsprobleme für Kunden zu beheben, die Azure Stack Hub **ohne Verbindung** verwenden. 

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende App-Frameworks und Tools**:
  - ASP.NET Core 2.1.6 und 2.2.0 hinzugefügt
  - NodeJS 10.14.1 hinzugefügt
  - NPM 6.4.1 hinzugefügt
  - Kudu auf 79.20129.3767 aktualisiert
  
- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [Februar 2019: Kumulatives Update für Windows Server 2016 für x64-basierte Systeme (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]  
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, *müssen* [Sie die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) hinzufügen und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

## <a name="post-update-steps"></a>Schritte nach dem Update

Führen Sie die folgenden Schritte aus, nachdem das Update für Azure App Service in Azure Stack Hub 1.5 abgeschlossen wurde, falls Sie für bereits vorhandene Bereitstellungen von Azure App Service in Azure Stack Hub eine Migration zu eigenständigen Datenbanken durchführen möchten:

> [!IMPORTANT]
> Das Migrationsverfahren dauert etwa 5 bis 10 Minuten. Das Verfahren umfasst das Beenden der vorhandenen Datenbankanmeldesitzungen. Planen Sie Ausfallzeiten für die Migration und Überprüfung von Azure App Service in Azure Stack Hub nach der Migration ein. Wenn Sie diese Schritte nach dem Upgrade auf Azure App Service in Azure Stack Hub 1.3 ausgeführt haben, müssen sie nicht mehr ausgeführt werden.

1. [Fügen Sie App Service-Datenbanken („appservice_hosting“ und „appservice_metering“) einer Verfügbarkeitsgruppe hinzu](/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Aktivieren Sie die eigenständige Datenbank.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Beim Konvertieren einer Datenbank in eine teilweise eigenständige Datenbank entstehen durch die Konvertierung Ausfallzeiten, da alle aktiven Sitzungen beendet werden müssen.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    ```

1. Migrieren Sie Anmeldungen zu Benutzern eigenständiger Datenbanken.

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

**Überprüfen**

1. Überprüfen Sie, ob die Eigenständigkeit für SQL Server aktiviert ist.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Überprüfen Sie das vorhandene Verhalten in Bezug auf die Eigenständigkeit.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn der App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dieses Problem wird in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack Hub beschrieben.

Wenn Sie sich für die Bereitstellung in einem vorhandenen virtuellen Netzwerk und zur Verwendung einer internen IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Administratorportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

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

Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub 1809](./release-notes.md).

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack Hub](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack Hub finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
