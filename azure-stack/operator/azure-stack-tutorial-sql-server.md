---
title: Verfügbarmachen von SQL-Datenbanken für Ihre Azure Stack-Benutzer | Microsoft-Dokumentation
description: Dies ist ein Tutorial zum Installieren des SQL Server-Ressourcenanbieters und zum Erstellen von Angeboten, die es Azure Stack-Benutzern ermöglichen, SQL-Datenbanken zu erstellen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: cc3af256adbcea8e6055b237945dc2286762437e
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267998"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Tutorial: Verfügbarmachen von SQL-Datenbanken für Ihre Azure Stack-Benutzer

Als Azure Stack-Cloudadministrator können Sie Angebote erstellen, die es Ihren Benutzern (Mandanten) ermöglichen, SQL-Datenbanken zu erstellen, die sie mit ihren cloudbasierten Apps, Websites und Workloads verwenden können. Die Bereitstellung dieser benutzerdefinierten, bedarfsgesteuerten, cloudbasierten Datenbanken für Ihre Benutzer spart diesen Zeit und Ressourcen. Für die Einrichtung führen Sie folgende Aktionen durch:

> [!div class="checklist"]
> * Bereitstellen des SQL Server-Ressourcenanbieters
> * Erstellen von Angeboten
> * Testen des Angebots

## <a name="deploy-the-sql-server-resource-provider"></a>Bereitstellen des SQL Server-Ressourcenanbieters

Der Bereitstellungsprozess wird im Artikel [Verwenden von SQL-Datenbanken in Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) ausführlich beschrieben und umfasst im Wesentlichen folgende Schritte:

1. [Bereitstellen des SQL-Ressourcenanbieters](azure-stack-sql-resource-provider-deploy.md)
2. [Überprüfen der Bereitstellung](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)
3. Bereitstellen von Kapazität durch Herstellen einer Verbindung mit einem SQL-Hostserver. Weitere Informationen finden Sie unter [Hinzufügen von Hostservern für den SQL-Ressourcenanbieter](azure-stack-sql-resource-provider-hosting-servers.md).

## <a name="create-an-offer"></a>Erstellen von Angeboten

1.  [Legen Sie ein Kontingent fest](azure-stack-plan-offer-quota-overview.md ), und nennen Sie es *SQLServerQuota*. Wählen Sie **Microsoft.SQLAdapter** für das Feld **Namespace** aus.
2.  [Erstellen Sie einen Plan](azure-stack-create-plan.md). Nennen Sie ihn *TestSQLServerPlan*, und wählen Sie den Dienst **Microsoft.SQLAdapter** und das Kontingent **SQLServerQuota** aus.

    > [!NOTE]
    > Damit Benutzer andere Apps erstellen können, können andere Dienste im Plan erforderlich sein. Azure Functions erfordert beispielsweise, dass der Plan den Dienst **Microsoft.Storage** enthält, während Wordpress **Microsoft.MySQLAdapter** erfordert.

3.  [Erstellen Sie ein Angebot](azure-stack-create-offer.md), nennen Sie es **TestSQLServerOffer**, und wählen Sie den Plan **TestSQLServerPlan** aus.

## <a name="test-the-offer"></a>Testen des Angebots

Da Sie den SQL Server-Ressourcenanbieter nun bereitgestellt und ein Angebot erstellt haben, können Sie sich nun als Benutzer anmelden, das Angebot abonnieren und eine Datenbank erstellen.

### <a name="subscribe-to-the-offer"></a>Abonnieren des Angebots

1. Melden Sie sich als Mandant beim Azure Stack-Portal (https://portal.local.azurestack.external)) an.
2. Klicken Sie auf **Abonnement erwerben**, und geben Sie dann unter **Anzeigename** die Zeichenfolge **TestSQLServerSubscription** ein.
3. Klicken Sie auf **Angebot auswählen** > **TestSQLServerOffer** > **Erstellen**.
4. Klicken Sie auf **Alle Dienste** > **Abonnements** > **TestSQLServerSubscription** > **Ressourcenanbieter**.
5. Klicken Sie neben dem Anbieter **Microsoft.SQLAdapter** auf **Registrieren**.

### <a name="create-a-sql-database"></a>Erstellen einer SQL-Datenbank

1. Klicken Sie auf **+**  > **Daten und Speicher** > **SQL-Datenbank**.
2. Übernehmen Sie die Standardwerte, oder verwenden Sie folgende Beispielwerte:
    - **Datenbankname**: SQLdb
    - **Maximale Größe in MB**: 100
    - **Abonnement**: TestSQLOffer
    - **Ressourcengruppe**: SQL-RG
3. Klicken Sie auf **Login Settings** (Anmeldeeinstellungen), geben Sie die Anmeldeinformationen für die Datenbank ein, und klicken Sie dann auf **OK**.
4. Wählen Sie unter **SKU** die SQL-SKU aus, die Sie für den SQL-Hostserver erstellt haben, und klicken Sie dann auf **OK**.
5. Klicken Sie auf **Erstellen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen des SQL Server-Ressourcenanbieters
> * Erstellen von Angeboten
> * Testen des Angebots

Im nächsten Tutorial lernen Sie Folgendes:

> [!div class="nextstepaction"]
> [Make web, mobile, and API apps available to your users (Verfügbarmachen von Web-, API- und mobilen Apps für Ihre Azure Stack-Benutzer)]( azure-stack-tutorial-app-service.md)