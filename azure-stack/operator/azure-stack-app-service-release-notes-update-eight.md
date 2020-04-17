---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 8'
description: Erfahren Sie, was in Update 8 für App Service in Azure Stack Hub enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 03/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 82f43028253638f92866bb679a5ccb5478a5a56e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "78935076"
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

### <a name="known-issues-upgrade"></a>Bekannte Probleme (Upgrade):

- Das Upgrade schlägt fehl, wenn das Failover für den SQL Server-Always On-Cluster auf den zweiten Knoten durchgeführt wurde.

Während des Upgrades wird ein Vorgang zum Überprüfen der Datenbankexistenz mithilfe der Masterverbindungszeichenfolge ausgeführt, wobei ein Fehler auftritt, da die Anmeldung auf den vorherigen Masterknoten erfolgte.

Führen Sie eine der folgenden Aktionen aus, und klicken Sie im Installationsprogramm auf „Wiederholen“.

- Kopieren Sie die Adminanmeldung appservice_hosting aus dem jetzt sekundären SQL-Knoten.

**OR**

- Führen Sie ein Failover des SQL-Clusters auf den vorherigen aktiven Knoten aus.

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie [die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

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

- Neue Bereitstellungen von Azure App Service auf Azure Stack Hub 1.8 erfordern, dass Datenbanken in eigenständige Datenbanken konvertiert werden.

Aufgrund einer Regression in diesem Release müssen App Service-Datenbanken (sowohl appservice_hosting als auch appservice_metering) in eigenständige Datenbanken konvertiert werden, wenn **neu** bereitgestellt wird.  Dies wirkt sich **NICHT** auf **aktualisierte** Bereitstellungen aus.

> [!IMPORTANT]
> Dieser Vorgang dauert ca. 5 bis 10 Minuten. Der Vorgang umfasst das Beenden der vorhandenen Datenbankanmeldesitzungen. Planen Sie Ausfallzeiten für die Migration und Überprüfung von Azure App Service in Azure Stack Hub nach der Migration ein.
>
>

1. [Fügen Sie App Service-Datenbanken („appservice_hosting“ und „appservice_metering“) einer Verfügbarkeitsgruppe hinzu](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Aktivieren Sie die eigenständige Datenbank.

    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Eine Datenbank wird in eine teilweise eigenständige Datenbank konvertiert. Bei diesem Schritt kommt es zu Downtime, weil alle aktiven Sitzungen beendet werden müssen.

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

- Worker können nicht aufskaliert werden

  Neue Worker können die erforderliche Datenbankverbindungszeichenfolge nicht abrufen.  Stellen Sie eine Verbindung mit einer Ihrer Controllerinstanzen wie z. B. CN0-VM her, und führen Sie das folgende PowerShell-Skript aus, um dieses Problem zu beheben:

    ```powershell
    
    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager

    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()
            
            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()
            
            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName
            
            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }

    $siteManager.CommitChanges()
        
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

Sehen Sie sich die Dokumentation in den [Versionshinweisen zum Azure Stack-Update 1907](azure-stack-release-notes-1907.md) an.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
