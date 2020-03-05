---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 4'
description: Erfahren Sie mehr über Verbesserungen, Fehlerbehebungen und bekannte Probleme in Update 4 für App Service in Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 93ca14c17613229aea354e96a9e48be41c2ea5e2
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703484"
---
# <a name="app-service-on-azure-stack-hub-update-4-release-notes"></a>App Service in Azure Stack Hub: Versionshinweise zu Update 4

In diesen Versionshinweisen werden die Verbesserungen, Fehlerbehebungen und bekannten Probleme in Update 4 für Azure App Service in Azure Stack Hub beschrieben. Die bekannten Probleme sind in drei Abschnitte unterteilt: Probleme in Bezug auf die Bereitstellung, Probleme mit dem Updatevorgang und Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1809 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.4 bereit.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 4 für App Service in Azure Stack Hub lautet **78.0.13698.5**.

### <a name="prerequisites"></a>Voraussetzungen

Lesen Sie vor Beginn der Bereitstellung die [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack Hub auf 1.4 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack Hub-Administratorportal bereit sind.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

- Beziehen Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9**) über den Azure Marketplace.

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 4 für Azure App Service in Azure Stack Hub enthält folgende Verbesserungen und Fehlerbehebungen:

- Lösung für das Cross-Site-Scripting-Sicherheitsrisiko (XSS) [CVE-2018-8600](https://aka.ms/CVE20188600).

- Unterstützung für App Service, API-Version 2018-02-01 hinzugefügt

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Konsistent mit Azure Stack Hub-Portal-SDK-Version.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.11959**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende App-Frameworks und Tools**:
  - NodeJS 10.6.0 hinzugefügt
  - NPM 6.1.0 hinzugefügt
  - Zulu OpenJDK 8.31.0.2 hinzugefügt
  - Tomcat 8.5.34 und 9.0.12 hinzugefügt
  - Hinzugefügte PHP-Versionen:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Update für Python-Versionen:
    - 2.7.15
    - 3.6.6
  - Git für Windows auf v2.17.1.2 aktualisiert
  - Kudu auf 78.11022.3613 aktualisiert
  
- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [Kumulatives Update für Windows Server 2016 für x64-basierte Systeme aus Oktober 2018 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Das Vorlagenüberprüfungsproblem bei der Bereitstellung von Wordpress-, DNN- und Orchard CMS-Katalogelementen wurde behoben.

- Das Konfigurationsproblem, das aufgetreten ist, wenn Azure Stack Hub das Azure Resource Manager-Clientzertifikat wechselt, wurde behoben.

- Funktionen in den CORS-Einstellungen (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen) im App Service-Benutzerportal wurden wiederhergestellt.

- Jetzt wird im App Service-Administratorportal eine Fehlermeldung angezeigt, wenn die Steuerungsebene des Ressourcenanbieters keine Verbindung mit der konfigurierten SQL Server-Instanz herstellen kann.

- Es wird sichergestellt, dass der Endpunkt in der benutzerdefinierten Speicherverbindungszeichenfolge angegeben wird, wenn er in der neuen Funktions-App angegeben ist.

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]  
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, *müssen* [Sie die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) hinzufügen und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

### <a name="post-update-steps-optional"></a>Schritte nach dem Update (optional)

Führen Sie die folgenden Schritte aus, nachdem das Update für Azure App Service in Azure Stack Hub 1.4 abgeschlossen wurde, falls Sie für bereits vorhandene Bereitstellungen von Azure App Service in Azure Stack Hub eine Migration zu eigenständigen Datenbanken durchführen möchten:

> [!IMPORTANT]
> Das Migrationsverfahren dauert etwa 5 bis 10 Minuten. Das Verfahren umfasst das Beenden der vorhandenen Datenbankanmeldesitzungen. Planen Sie Ausfallzeiten für die Migration und Überprüfung von Azure App Service in Azure Stack Hub nach der Migration ein. Wenn Sie diese Schritte nach dem Upgrade auf Azure App Service in Azure Stack Hub 1.3 ausgeführt haben, müssen sie nicht mehr ausgeführt werden.

1. [Fügen Sie App Service-Datenbanken („appservice_hosting“ und „appservice_metering“) einer Verfügbarkeitsgruppe hinzu](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

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

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack Hub verwenden

Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub 1809](azure-stack-update-1903.md).

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack Hub](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack Hub finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
