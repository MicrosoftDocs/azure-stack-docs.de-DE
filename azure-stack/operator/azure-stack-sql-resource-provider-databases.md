---
title: Verwenden von Datenbanken, die vom SQL-Adapter-Ressourcenanbieter in Azure Stack bereitgestellt werden | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie SQL-Datenbanken erstellen und verwalten, die mit dem SQL-Adapter-Ressourcenanbieter bereitgestellt wurden.
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
ms.openlocfilehash: dfa8e778b21826ff1f589efbf2b49097d5ee3685
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829338"
---
# <a name="create-sql-databases"></a>Erstellen von SQL-Datenbanken

Self-Service-Datenbanken können im Benutzerportal erstellt und verwaltet werden. Ein Azure Stack-Benutzer benötigt ein Abonnement mit einem Angebot, das den SQL-Datenbankdienst einschließt.

1. Melden Sie sich beim [Azure Stack](azure-stack-overview.md)-Benutzerportal an.

2. Klicken Sie auf **+ Neu** &gt; **Daten und Speicher** &gt; **SQL Server-Datenbank** &gt; **Hinzufügen**.

3. Geben Sie unter **Datenbank erstellen** die erforderlichen Informationen ein, z.B. **Datenbankname** und **Max. Größe in MB**.

   >[!NOTE]
   >Die Datenbankgröße muss mindestens 64 MB betragen. Diesen Wert können Sie nach der Bereitstellung der Datenbank erhöhen.

   Konfigurieren Sie nach Bedarf die anderen Einstellungen für Ihre Umgebung.

4. Klicken Sie unter **Datenbank erstellen** auf **SKU**. Wählen Sie unter **SKU auswählen** die SKU für Ihre Datenbank aus.

   ![Erstellen einer Datenbank](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Wenn Hostserver in Azure Stack hinzugefügt werden, wird ihnen eine SKU zugewiesen. Datenbanken werden im Pool von Hostservern in einer SKU erstellt.

5. Wählen Sie **Anmelden**.
6. Wählen Sie unter **Anmeldung auswählen** eine vorhandene Anmeldung aus, oder wählen Sie **+ Neue Anmeldung erstellen**.
7. Geben Sie unter **Neue Anmeldung** einen Namen für **Datenbankanmeldung** und ein **Kennwort** ein.

   >[!NOTE]
   >Bei diesen Einstellungen handelt es sich um Anmeldeinformationen für die SQL-Authentifizierung, die nur für Ihren Zugriff auf diese Datenbank erstellt werden. Der Benutzername für die Anmeldung muss global eindeutig sein. Sie können die Anmeldeeinstellungen für andere Datenbanken wiederverwenden, die dieselbe SKU verwenden.

   ![Erstellen einer neuen Datenbankanmeldung](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Wählen Sie **OK**, um die Bereitstellung der Datenbank abzuschließen.

Notieren Sie sich die **Verbindungszeichenfolge** aus der **Zusammenfassung**, die nach Bereitstellung der Datenbank angezeigt wird. Sie können diese Zeichenfolge in jeder Anwendung verwenden, die auf die SQL Server-Datenbank zugreifen muss.

![Abrufen der Verbindungszeichenfolge](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On-Datenbanken

Entwurfsbedingt werden Always On-Datenbanken anders behandelt als in einer eigenständigen Serverumgebung. Weitere Informationen finden Sie unter [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Überprüfen von SQL Always On-Datenbanken

Die folgende Bildschirmaufnahme zeigt, wie Sie den Status der Datenbank in SQL Always On mithilfe von SQL Server Management Studio anzeigen können.

![Always On-Datenbankstatus](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On-Datenbanken sollten als synchronisiert und für alle SQL-Instanzen verfügbar dargestellt und in den Verfügbarkeitsgruppen angezeigt werden. In der vorherigen Bildschirmaufnahme heißt das Beispiel für die Datenbank „newdb1“ und ihr Status lautet **newdb1 (synchronisiert)** .

### <a name="delete-an-alwayson-database"></a>Löschen einer Always On-Datenbank

Wenn Sie eine SQL Always On-Datenbank aus dem Ressourcenanbieter löschen, löscht SQL die Datenbank vom primären Replikat und aus der Verfügbarkeitsgruppe.

Auf den anderen Replikaten versetzt SQL die Datenbank in den Wiederherstellungsstatus und löscht die Datenbank erst, wenn der Löschvorgang ausgelöst wird. Wenn die Datenbank nicht gelöscht wird, wechseln die sekundären Replikate in den nicht synchronisierten Zustand.

## <a name="next-steps"></a>Nächste Schritte

[Warten des SQL Server-Ressourcenanbieters](azure-stack-sql-resource-provider-maintain.md)
