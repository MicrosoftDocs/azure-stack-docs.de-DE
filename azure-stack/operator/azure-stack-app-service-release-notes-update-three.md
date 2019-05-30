---
title: App Service in Azure Stack – Versionshinweise zu Update 3 | Microsoft-Dokumentation
description: Erfahren Sie, was in Update 3 für App Service in Azure Stack enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: 4397fbbc570972fb7a041f583d81115e28fe70ce
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269098"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>App Service in Azure Stack – Versionshinweise zu Update 3

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und Fehlerbehebungen in Update 3 für Azure App Service in Azure Stack sowie bekannte Probleme beschrieben. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1807 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit vor der Bereitstellung von Azure App Service 1.3 bereit.
>
>

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 3 für App Service in Azure Stack ist **74.0.13698.31**.

### <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack auf 1.3 beginnen, sollten Sie sicherstellen, dass im Azure Stack-Verwaltungsportal für die Azure App Service-Verwaltung alle Rollen „bereit“ sind.

![Status von App Service-Rollen](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 3 für Azure App Service in Azure Stack enthält die folgenden Verbesserungen und Fehlerbehebungen:

- Unterstützung der Verwendung von SQL Server Always On für Azure App Service-Ressourcenanbieterdatenbanken.

- Dem Create-AADIdentityApp-Hilfsskript wurde ein neuer „Environment“-Parameter hinzugefügt, um die Ausrichtung auf unterschiedliche AAD-Regionen zu vereinfachen.

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.11820**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools**:
  - ASP.NET Core 2.1.2 hinzugefügt
  - NodeJS 10.0.0 hinzugefügt
  - Zulu OpenJDK 8.30.0.1 hinzugefügt
  - Tomcat 8.5.31 und 9.0.8 hinzugefügt
  - Hinzugefügte PHP-Versionen:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Wincache 2.0.0.8 hinzugefügt
  - Git für Windows auf v2.17.1.2 aktualisiert
  - Kudu auf 74.10611.3437 aktualisiert
  
- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [Wartungsstapelaktualisierung für Windows Server 2016 für x64-basierte Systeme (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [Kumulatives Update für Windows Server 2016 für x64-basierte Systeme aus Juli 2018 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Schritte nach dem Updatevorgang (optional)

Führen Sie diese Schritte aus, nachdem das Update für Azure App Service in Azure Stack 1.3 abgeschlossen wurde, falls Sie die Migration zu eigenständigen Datenbanken für vorhandene Bereitstellungen von Azure App Service in Azure Stack durchführen möchten:

> [!IMPORTANT]
> Dieser Vorgang dauert ca. 5 bis 10 Minuten.  Der Vorgang umfasst das Beenden der vorhandenen Datenbankanmeldesitzungen.  Planen von Ausfallzeiten für die Migration und Überprüfung von Azure App Service in Azure Stack nach der Migration
>
>

1. Hinzufügen von [App Service-Datenbanken (appservice_hosting und appservice_metering) zu einer Verfügbarkeitsgruppe](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Aktivieren der eigenständigen Datenbank
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Konvertieren einer Datenbank in „teilweise eigenständig“.  Bei diesem Schritt kommt es zu Ausfallzeit, da alle aktiven Sitzungen beendet werden müssen.

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
    '''

1. Migrate Logins to Contained Database Users

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

Überprüfen

1. Überprüfen, ob für SQL Server die Eigenständigkeit aktiviert ist

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Überprüfen des vorhandenen eigenständigen Verhaltens
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn der App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist.  Dies wird auch in der Dokumentation zu Azure App Service in einer Azure Stack-Bereitstellung beschrieben.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie dazu im Admin-Portal zur WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:
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

Lesen Sie die Dokumentation in den Versionshinweisen zum Azure Stack-Update 1807.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
