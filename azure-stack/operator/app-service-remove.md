---
title: Entfernen von Azure App Service aus Azure Stack Hub
description: Hier erfahren Sie, wie Sie Azure App Service aus Azure Stack Hub entfernen.
author: apwestgarth
ms.topic: article
ms.date: 04/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/17/20207
ms.openlocfilehash: b371b319ae87bb112633b64d77d66f8916a13124
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81636491"
---
# <a name="remove-azure-app-service-from-azure-stack-hub"></a>Entfernen von Azure App Service aus Azure Stack Hub

In diesem Artikel erfahren Sie, wie Sie den Azure App Service-Ressourcenanbieter und die zugehörigen Komponenten aus Azure Stack Hub entfernen.

## <a name="remove-resource-provider"></a>Entfernen des Ressourcenanbieters

> [!Important]
> Durch diesen Vorgang werden alle Mandantenressourcen entfernt. Außerdem werden der Dienst und die Kontingente aus allen Plänen entfernt, und der Azure App Service-Ressourcenanbieter wird vollständig entfernt.  Wenn Sie die App Service-Schnellstartvorlage für hochverfügbare Dateiserver und SQL Server bereitgestellt haben, werden diese Ressourcen ebenfalls entfernt, da sie in der gleichen Ressourcengruppe bereitgestellt wurden wie Azure App Service in Azure Stack Hub.

Wenn Sie Azure App Service aus Azure Stack Hub entfernen möchten, müssen Sie nur diesen einen Schritt ausführen:

1. Löschen Sie die Ressourcengruppe, die Ressourcen von Azure App Service in Azure Stack Hub enthält (beispielsweise „AppService.local“).

## <a name="remove-databases-and-file-share-content"></a>Entfernen von Datenbanken und Dateifreigabeinhalt

Die Schritte in diesem Abschnitt sind nur erforderlich, wenn die SQL Server-Instanz und/oder der Dateiserver außerhalb des Stempels oder in einer anderen Ressourcengruppe bereitgestellt wurde(n). Ist dies nicht der Fall, fahren Sie mit dem nächsten Abschnitt fort.

### <a name="remove-databases-and-logins"></a>Entfernen von Datenbanken und Anmeldungen

1. Entfernen Sie bei Verwendung von **SQL Server Always On** die Datenbanken **AppService_Hosting** und **AppService_Metering** aus der Verfügbarkeitsgruppe:

1. Führen Sie das folgende SQL-Skript aus, um die Datenbanken und Anmeldungen zu entfernen:

   ```sql
   --******************************************************************
   /*
   Script to clean up App Service objects (databases and logins).
   */
   USE [master]
   GO

   DROP DATABASE [appservice_hosting]
   GO

   DROP DATABASE [appservice_metering]
   GO

   DECLARE @sql NVARCHAR(MAX) = N'';    
 
   SELECT @sql += '
   DROP LOGIN [' + name + '];' 
   from master.sys.sql_logins
   WHERE name LIKE  '%_hosting_%' OR 
   name LIKE  '%_metering_%' OR
   name LIKE  '%WebWorker_%';

   PRINT @sql;
   EXEC sp_executesql @sql;
   PRINT 'Completed';

   --******************************************************************
   ```

### <a name="remove-the-application-file-content-from-the-file-server"></a>Entfernen des Inhalts der Anwendungsdatei vom Dateiserver

1. Entfernen Sie die Inhaltsdateifreigabe vom Dateiserver.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Neuinstallation finden Sie im Artikel [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
