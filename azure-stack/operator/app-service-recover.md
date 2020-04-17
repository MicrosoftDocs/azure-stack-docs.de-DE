---
title: App Service-Wiederherstellung in Azure Stack Hub
description: In diesem Artikel wird die Notfallwiederherstellung für App Service in Azure Stack Hub erläutert.
author: bryanla
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: e04fa2f3e9d03f1982ef67d4d19549b7b73a1f1a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701682"
---
# <a name="app-service-recovery-on-azure-stack-hub"></a>App Service-Wiederherstellung in Azure Stack Hub

Dieser Artikel enthält Anweisungen zu den Aktionen, die Sie zur App Service-Notfallwiederherstellung ausführen müssen.

Die folgenden Aktionen müssen ausgeführt werden, um App Service in Azure Stack Hub aus einer Sicherung wiederherzustellen:
1. Wiederherstellen der App Service-Datenbanken
2. Wiederherstellen des Inhalts der Dateiserverfreigabe
3. Wiederherstellen von App Service-Rollen und -Diensten

Wenn Azure Stack Hub-Speicher als Speicher für Funktionen-Apps verwendet wurde, müssen Sie zudem Schritte zum Wiederherstellen der Funktionen-Apps ausführen.

## <a name="restore-the-app-service-databases"></a>Wiederherstellen der App Service-Datenbanken
Die SQL Server-Datenbanken von App Service sollten auf einer produktionsreifen SQL Server-Instanz wiederhergestellt werden. 

Nach dem [Vorbereiten der SQL Server-Instanz](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) zum Hosten der App Service-Datenbanken gehen Sie wie folgt vor, um Datenbanken aus einer Sicherung wiederherzustellen:

1. Melden Sie sich mit Administratorberechtigungen bei der SQL Server-Instanz an, die die wiederhergestellten App Service-Datenbanken hosten soll.
2. Führen Sie an einer Eingabeaufforderung mit Administratorberechtigungen die folgenden Befehle aus, um die App Service-Datenbanken wiederherzustellen:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Stellen Sie sicher, dass beide App Service-Datenbanken erfolgreich wiederhergestellt wurden, und beenden Sie SQL Server Management Studio.

> [!NOTE]
> Informationen zur Wiederherstellung nach einem Fehler einer Failoverclusterinstanz finden Sie unter [Wiederherstellen nach einem Fehler der Failoverclusterinstanz](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Wiederherstellen des Inhalts der App Service-Dateifreigabe
Nach dem [Vorbereiten des Dateiservers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) zum Hosten der App Service-Dateifreigabe müssen Sie den Inhalt der Mandantendateifreigabe aus einer Sicherung wiederherstellen. Sie können die Dateien mit einer beliebigen Methode an den Speicherort der neu erstellten App Service-Dateifreigabe kopieren. Wenn Sie dieses Beispiel auf dem Dateiserver ausführen, werden PowerShell und Robocopy verwendet, um eine Verbindung mit einer Remotefreigabe herzustellen und die Dateien in die Freigabe zu kopieren:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Zusätzlich zum Kopieren des Inhalts der Dateifreigabe müssen Sie auch die Berechtigungen für die Dateifreigabe selbst zurücksetzen. Öffnen Sie zum Zurücksetzen der Berechtigungen auf dem Dateiservercomputer eine Administratoreingabeaufforderung, und führen Sie die Datei **ReACL.cmd** aus. Die Datei **ReACL.cmd** finden Sie in den App Service-Installationsdateien im Verzeichnis **BCDR**.

## <a name="restore-app-service-roles-and-services"></a>Wiederherstellen von App Service-Rollen und -Diensten
Nachdem die App Service-Datenbanken und der Inhalt der Dateifreigabe wiederhergestellt wurden, müssen Sie als Nächstes mithilfe von PowerShell die App Service-Rollen und -Dienste wiederherstellen. Mit den folgenden Schritten werden App Service-Geheimnisse und -Dienstkonfigurationen wiederhergestellt.  

1. Melden Sie sich mit dem Kennwort, das Sie während der Installation von App Service angegeben haben, als Rollenadministrator (**roleadmin**)bei der App Service-Controller-VM **CN0-VM** an. 
    > [!TIP]
    > Sie müssen die Netzwerksicherheitsgruppe der VM ändern, um RDP-Verbindungen zuzulassen. 
2. Kopieren Sie die Datei **SystemSecrets.JSON** lokal auf die Controller-VM. Den Pfad dieser Datei müssen Sie im nächsten Schritt als Parameter `$pathToExportedSecretFile` angeben.
3. Führen Sie die folgenden Befehle in einem PowerShell-Konsolenfenster mit erhöhten Rechten aus, um die App Service-Rollen und -Dienste wiederherzustellen:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Es wird dringend empfohlen, diese PowerShell-Sitzung nach der Ausführung des Befehls zu schließen.

## <a name="restore-function-apps"></a>Wiederherstellen von Funktionen-Apps 
Mit Ausnahme des Inhalts der Dateifreigabe unterstützt App Service für Azure Stack Hub das Wiederherstellen von Mandantenbenutzer-Apps oder -daten nicht. Alle anderen Daten müssen daher außerhalb der App Service-Sicherungs- und Wiederherstellungsvorgänge gesichert und wiederhergestellt werden. Wenn Azure Stack Hub-Speicher als Speicher für Funktionen-Apps verwendet wurde, sollten zum Wiederherstellen verloren gegangener Daten die folgenden Schritte ausgeführt werden:

1. Erstellen Sie ein neues Speicherkonto für die Funktionen-App. Bei diesem Speicher kann es sich um Azure Stack Hub-Speicher, Azure Storage oder beliebigen kompatiblen Speicher handeln.
2. Rufen Sie die Verbindungszeichenfolge für den Speicher ab.
3. Öffnen Sie das Funktionsportal, und navigieren Sie zur Funktions-App.
4. Navigieren Sie zur Registerkarte **Plattformfeatures**, und klicken Sie auf **Anwendungseinstellungen**.
5. Ändern Sie **AzureWebJobsDashboard** und **AzureWebJobsStorage**, indem Sie die neue Verbindungszeichenfolge angeben, und klicken Sie auf **Speichern**.
6. Wechseln Sie zu **Übersicht**.
7. Starten Sie die App neu. Möglicherweise sind mehrere Versuche erforderlich, um alle Fehler zu löschen.

## <a name="next-steps"></a>Nächste Schritte
[App Service in Azure Stack Hub (Übersicht)](azure-stack-app-service-overview.md)