---
title: Sichern von App Service in Azure Stack | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie App Services in Azure Stack sichern.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: a41943a598545b1a4c5dbe6325307a8fa3594cd5
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975037"
---
# <a name="back-up-app-service-on-azure-stack"></a>Sichern von App Service in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*  

Dieses Dokument enthält Anweisungen zum Sichern von App Service in Azure Stack.

> [!IMPORTANT]
> App Service in Azure Stack wird nicht im Rahmen der [Azure Stack-Infrastruktursicherung](azure-stack-backup-infrastructure-backup.md) gesichert. Als Azure Stack-Bediener müssen Sie bestimmte Schritte ausführen, um sicherzustellen, dass App Service bei Bedarf erfolgreich wiederhergestellt werden kann.

Für Azure App Service in Azure Stack müssen bei der Planung der Notfallwiederherstellung vier Hauptkomponenten berücksichtigt werden:
1. Die Ressourcenanbieterinfrastruktur, Serverrollen, Workerebenen usw. 
2. Die App Service-Geheimnisse.
3. Die SQL Server-Instanz für App Service, die Datenbanken hostet und Messungen ausführt.
4. In der App Service-Dateifreigabe gespeicherte Inhalte der App Service-Benutzerworkload.

## <a name="back-up-app-service-secrets"></a>Sichern von App Service-Geheimnissen
Wenn Sie App Service aus einer Sicherung wiederherstellen, müssen Sie die App Service-Schlüssel angeben, die für die Erstbereitstellung verwendet wurden. Diese Informationen sollten nach der erfolgreichen Bereitstellung von App Service sofort gespeichert und an einem sicheren Ort verwahrt werden. Die Konfiguration der Ressourcenanbieterinfrastruktur wird während der Wiederherstellung mithilfe von PowerShell-Cmdlets für die App Service-Wiederherstellung aus einer Sicherung neu erstellt.

Gehen Sie wie folgt vor, um App Service-Geheimnisse über das Verwaltungsportal zu sichern: 

1. Melden Sie sich am Azure Stack-Administratorportal als Dienstadministrator an.

2. Navigieren Sie zu **App Service** -> **Geheimnisse**. 

3. Wählen Sie **Geheimnisse herunterladen** aus.

   ![Herunterladen von Geheimnissen im Azure Stack-Administratorportal](./media/app-service-back-up/download-secrets.png)

4. Wenn die Geheimnisse zum Herunterladen bereit sind, klicken Sie auf **Speichern**, und speichern Sie die Datei mit den App Service-Geheimnissen (**SystemSecrets.JSON**) an einem sicheren Ort. 

   ![Speichern von Geheimnissen im Azure Stack-Administratorportal](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Wiederholen Sie diese Schritte bei jeder Rotation der App Service-Geheimnisse.

## <a name="back-up-the-app-service-databases"></a>Sichern der App Service-Datenbanken
Zum Wiederherstellen von App Service benötigen Sie die Datenbanksicherungen **Appservice_hosting** und **Appservice_metering**. Es wird empfohlen, SQL Server-Wartungspläne oder Azure Backup Server zu verwenden, um die regelmäßige Sicherung und sichere Speicherung dieser Datenbanken zu gewährleisten. Sie können jedoch eine beliebige Methode verwenden, um die regelmäßige Erstellung von SQL-Sicherungen sicherzustellen.

Sichern Sie mit den folgenden PowerShell-Befehlen die Datenbanken manuell, während Sie bei SQL Server angemeldet sind:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Falls Sie SQL AlwaysOn-Datenbanken sichern müssen, folgen Sie [diesen Anweisungen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Nachdem alle Datenbanken erfolgreich gesichert wurden, kopieren Sie die BAK-Dateien zusammen mit den Informationen zu den App Service-Geheimnissen an einen sicheren Ort.

## <a name="back-up-the-app-service-file-share"></a>Sichern der App Service-Dateifreigabe
App Service speichert Informationen von Mandanten-Apps in der Dateifreigabe. Diese Dateifreigabe muss in regelmäßigen Abständen zusammen mit den App Service-Datenbanken gesichert werden, damit im Fall einer Wiederherstellung so wenig Daten wie möglich verloren gehen.

Verwenden Sie zum Sichern des Inhalts der App Service-Dateifreigabe Azure Backup Server oder eine andere Methode, um den Inhalt der Dateifreigabe regelmäßig an den Speicherort zu kopieren, an dem Sie alle vorherigen Wiederherstellungsinformationen gespeichert haben.

Sie können z. B. die folgenden Schritte ausführen, um Robocopy in einer Windows PowerShell-Konsolensitzung (nicht PowerShell ISE) zu verwenden:

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Nächste Schritte
[Wiederherstellen von App Service in Azure Stack](app-service-recover.md)