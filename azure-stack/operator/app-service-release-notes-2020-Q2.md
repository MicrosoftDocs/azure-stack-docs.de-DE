---
title: 'App Service in Azure Stack Hub 2002 Q2: Versionshinweise'
description: Erfahren Sie, was im Release 2002 Q2 für App Service in Azure Stack Hub enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: c5e6ac0a2a500cf43cf94cbc40b2a95c58784d28
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544716"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>App Service on Azure Stack Hub 2020 Q2: Versionshinweise

In diesen Versionshinweisen werden die Verbesserungen und Fehlerbehebungen in Azure App Service in Azure Stack Hub 2020 Q2 sowie bekannte Probleme beschrieben. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer für App Service in Azure Stack Hub 2020 Q2 lautet **87.0.2.10**.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack auf 2020 Q2 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal bereit sind.

- Sichern der App Service-Geheimnisse mithilfe der App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

  > [!Important]
  > Cloudoperatoren sind für die Verwaltung und den Betrieb des Dateiservers und von SQL Server verantwortlich.  Der Ressourcenanbieter verwaltet diese Ressourcen nicht.  Der Cloudoperator ist für das Sichern der App Service-Datenbanken und der Mandanten-Inhaltsdateifreigabe verantwortlich.

- Syndizieren Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.3** ) über den Marketplace.



## <a name="updates"></a>Aktualisierungen

Update Q2 für Azure App Service in Azure Stack enthält folgende Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Aktualisierung der **Azure Functions-Runtime** auf **v1.0.13021**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools** :
  - ASP.NET Framework 4.7.2
  - ASP.NET Core 3.1.3
  - ASP.NET Core-Modul v2 13.1.19331.0
  - PHP 7.4.2
  - Update von Kudu auf 86.20224.4450
  - NodeJS 
    - 8.17.0
    - 10.19.0
    - 12.13.0
    - 12.15.0
  - npm
    - 5.6.0
    - 6.1.0
    - 6.12.0
    - 6.13.4
  
- **Updates des zugrunde liegenden Betriebssystems aller Rollen** :
  - [April 2020: Kumulatives Update für Windows Server 2016 für x64-basierte Systeme (KB4550929)](https://support.microsoft.com/help/4550929)
  - [April 2020: Wartungsstapelaktualisierung für Windows Server 2016 für x64-basierte Systeme (KB4550994)](https://support.microsoft.com/help/4550994)

- **Kumulative Updates für Windows Server werden jetzt im Rahmen der Bereitstellung und Aktualisierung auf Controllerrollen angewendet.**

- **Update der Standard-SKUs für virtuelle Computer und Skalierungsgruppen für neue Bereitstellungen** : Um die Konsistenz mit unserem öffentlichen Clouddienst aufrechtzuerhalten, verwenden neue Bereitstellungen von Azure App Service in Azure Stack Hub die folgenden SKUs für die zugrunde liegenden Computer und Skalierungsgruppen, die für den Betrieb des Ressourcenanbieters verwendet werden.
  
  | Role | Mindest-SKU |
  | --- | --- |
  | Controller | Standard_A4_v2 (4 Kerne, 8.192 MB) |
  | Verwaltung | Standard_D3_v2 (4 Kerne, 14.336 MB) |
  | Herausgeber | Standard_A2_v2 (2 Kerne, 4.096 MB) |
  | FrontEnd | Standard_A4_v2 (4 Kerne, 8.192 MB) |
  | Freigegebener Worker | Standard_A4_v2 (4 Kerne, 8.192 MB) |
  | Kleiner dedizierter Worker | Standard_A1_v2 (1 Kern, 2.048 MB) |
  | Mittlerer dedizierter Worker | Standard_A2_v2 (2 Kerne, 4.096 MB) |
  | Großer dedizierter Worker | Standard_A4_v2 (4 Kerne, 8.192 MB) |

Für ASDK-Bereitstellungen können Sie die Instanzen auf niedrigere SKUs abskalieren, um die Kern- und Arbeitsspeicherbelegung zu reduzieren, aber es treten Leistungseinbußen auf.

## <a name="issues-fixed-in-this-release"></a>In diesem Release behobene Probleme

- Upgrades werden nun abgeschlossen, wenn der SQL Always On-Cluster ein Failover auf den sekundären Knoten ausgeführt hat.
- Für neue Bereitstellungen von Azure App Service in Azure Stack Hub ist es nicht mehr erforderlich, dass Datenbanken manuell in eigenständige Datenbanken konvertiert werden.
- Das Hinzufügen zusätzlicher Worker oder Infrastrukturrolleninstanzen wird ohne manuellen Eingriff ordnungsgemäß durchgeführt.
- Das Hinzufügen benutzerdefinierter Workerebenen wird ohne manuellen Eingriff ordnungsgemäß ausgeführt.
- Das Entfernen von benutzerdefinierten Workerebenen wird jetzt ohne Portalfehler abgeschlossen.
- Worker werden nicht mehr als bereit gekennzeichnet, wenn kein Speicherplatz auf dem lokalen Datenträger verfügbar ist.
- Das Timeout beim Abrufen des Azure Resource Manager-Zertifikats wurde erhöht.
- Die Anzahl der Nachrichten, die aus den Serverprotokollen abgerufen und im Verwaltungsportal angezeigt werden, wurde so eingeschränkt, dass sie unterhalb der maximalen Azure Resource Manager-Anforderungsgröße liegt.
- Timeoutproblem verursacht Startprobleme des Verwendungsdiensts.
- Datenbankbereitstellungsproblem bei der Erstellung von Orchard Sites wurde behoben.
- Controller werden nun im Rahmen der Bereitstellung und Aktualisierung mit kumulativen Windows-Updates aktualisiert.
- App Service sperrt keine Vorgänge mehr, wenn ein Fehler bei der Überprüfung der benutzerdefinierten Domäne auftritt.

## <a name="pre-update-steps"></a>Schritte vor dem Update

Überprüfen Sie die [bekannten Probleme bei Updates](#known-issues-update), und ergreifen Sie alle vorgeschriebenen Maßnahmen.

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie [die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

## <a name="known-issues-update"></a>Bekannte Probleme (Update)

- In Fällen, in denen ein Kunde die appservice_hosting- und appservice_metering-Datenbanken in eine eigenständige Datenbank konvertiert hat, schlägt das Upgrade möglicherweise fehl, wenn Anmeldungen nicht erfolgreich zu eigenständigen Benutzern migriert wurden.

Bei Kunden, die die appservice_hosting- und appservice_metering-Datenbanken nach der Bereitstellung in eine eigenständige Datenbank konvertiert und die Datenbankanmeldungen nicht zu eigenständigen Benutzern migriert haben, können Upgradefehler auftreten.  

Kunden müssen das folgende Skript für den SQL-Server ausführen, der appservice_hosting und appservice_metering hostet, bevor sie die Azure App Service in Azure Stack Hub-Installation auf 2020 Q2 aktualisieren können.  **Dieses Skript ist nicht destruktiv und führt nicht zu Ausfallzeiten**.

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

- Mandanten, die keinen App Service-Plan mithilfe von „Neu“ in der App Service-Planansicht im Mandantenportal erstellen können

Beim Erstellen einer neuen Anwendung können Mandanten während des App-Erstellungsworkflows oder beim Ändern des App Service-Plans für eine aktuelle App oder über das App Service-Plan-Marketplace-Element App Service-Pläne erstellen.

- Benutzerdefinierte Domänen werden in getrennten Umgebungen nicht unterstützt.

App Service führt die Überprüfung des Domänenbesitzes für öffentliche DNS-Endpunkte durch. Daher werden benutzerdefinierte Domänen in getrennten Szenarien nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
