---
title: Erstellen von SQL-Datenbanken
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie mit dem SQL-Ressourcenanbieteradapter bereitgestellte SQL-Datenbanken erstellen und verwalten.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: f6c030767ba64aa3c8acd47d5b358a4b385785ac
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814540"
---
# <a name="create-sql-databases"></a>Erstellen von SQL-Datenbanken

Self-Service-Datenbanken können im Benutzerportal erstellt und verwaltet werden. Ein Azure Stack Hub-Benutzer benötigt ein Abonnement mit einem Angebot, das den SQL-Datenbankdienst einschließt.

1. Melden Sie sich beim [Azure Stack Hub](azure-stack-overview.md)-Benutzerportal an.

2. Klicken Sie auf **+ Neu** &gt;**Daten + Speicher** &gt; **SQL Server-Datenbank** &gt; **Hinzufügen**.

3. Geben Sie unter **Datenbank erstellen** die erforderlichen Informationen ein, z.B. **Datenbankname** und **Max. Größe in MB**.

   >[!NOTE]
   >Die Datenbankgröße muss mindestens 64 MB betragen. Diesen Wert können Sie nach der Bereitstellung der Datenbank erhöhen.

   Konfigurieren Sie nach Bedarf die anderen Einstellungen für Ihre Umgebung.

4. Klicken Sie unter **Datenbank erstellen** auf **SKU**. Wählen Sie unter **SKU auswählen** die SKU für Ihre Datenbank aus.

   ![Erstellen Sie im Azure Stack Hub-Benutzerportal eine Datenbank.](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Wenn Hostserver zu Azure Stack Hub hinzugefügt werden, wird ihnen eine SKU zugewiesen. Datenbanken werden im Pool von Hostservern in einer SKU erstellt.

5. Wählen Sie **Anmelden**.

6. Wählen Sie unter **Anmeldung auswählen** eine vorhandene Anmeldung aus, oder wählen Sie **+ Neue Anmeldung erstellen**.

7. Geben Sie unter **Neue Anmeldung** einen Namen für **Datenbankanmeldung** und ein **Kennwort** ein.

   >[!NOTE]
   >Bei diesen Einstellungen handelt es sich um die Anmeldeinformationen für die SQL-Authentifizierung, die nur für Ihren Zugriff auf diese Datenbank erstellt werden. Der Benutzername für die Anmeldung muss global eindeutig sein. Sie können die Anmeldeeinstellungen für andere Datenbanken wiederverwenden, die dieselbe SKU verwenden.

   ![Erstellen einer neuen Datenbankanmeldung im Azure Stack Hub-Benutzerportal](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Wählen Sie **OK**, um die Bereitstellung der Datenbank abzuschließen.

Notieren Sie sich die **Verbindungszeichenfolge** aus der **Zusammenfassung**, die nach Bereitstellung der Datenbank angezeigt wird. Sie können diese Zeichenfolge in jeder App verwenden, die auf die SQL Server-Datenbank zugreifen muss.

![Abrufen der Verbindungszeichenfolge für die SQL Server-Datenbank](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On-Datenbanken

Entwurfsbedingt werden Always On-Datenbanken anders behandelt als in einer eigenständigen Serverumgebung. Weitere Informationen finden Sie unter [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Überprüfen von SQL Always On-Datenbanken

Die folgende Bildschirmaufnahme zeigt, wie Sie den Status der Datenbank in SQL Always On mithilfe von SQL Server Management Studio anzeigen können.

![AlwaysOn-Datenbankstatus in SQL Server Management Studio](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On-Datenbanken sollten als **Synchronisiert** und für alle SQL-Instanzen verfügbar dargestellt und in **Verfügbarkeitsgruppen** angezeigt werden. Auf dem vorherigen Screenshot ist „newdb1“ das Beispiel für die Datenbank, und der Status lautet **newdb1 (synchronisiert)** .

### <a name="delete-an-alwayson-database"></a>Löschen einer Always On-Datenbank

Wenn Sie eine SQL AlwaysOn-Datenbank aus dem Ressourcenanbieter löschen, löscht SQL die Datenbank aus dem **primären** Replikat und aus der Verfügbarkeitsgruppe.

Für die anderen Replikate versetzt SQL die Datenbank in den Zustand **Wird wiederhergestellt** und löscht die Datenbank nur, wenn ein entsprechender Löschvorgang ausgelöst wird. Wird die Datenbank nicht gelöscht, wechseln die sekundären Replikate in den Zustand **Synchronisierung wird nicht ausgeführt**.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Anbieten hochverfügbarer SQL-Datenbanken](azure-stack-tutorial-sql.md).
