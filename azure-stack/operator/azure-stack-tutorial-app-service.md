---
title: Verfügbarmachen von Web- und API-Apps für Ihre Azure Stack-Benutzer | Microsoft-Dokumentation
description: Tutorial zum Installieren des App Service-Ressourcenanbieters und zum Erstellen von Angeboten, die es Ihren Azure Stack-Benutzern ermöglichen, Web- und API-Apps zu erstellen
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: aa897867c4f9735369cc0dec1c4d8527273e2f7f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "63447406"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tutorial: Verfügbarmachen von Web- und API-Apps für Ihre Azure Stack-Benutzer

Als Azure Stack-Cloudadministrator können Sie Angebote erstellen, die es Ihren Benutzern (Mandanten) ermöglichen, Azure Functions und Web- und API-Anwendungen zu erstellen. Die Bereitstellung des Zugriffs auf diese bedarfsgesteuerten, cloudbasierten Apps für Ihre Benutzer spart diesen Zeit und Ressourcen.

Für die Einrichtung führen Sie folgende Aktionen durch:

> [!div class="checklist"]
> * Bereitstellen des App Service-Ressourcenanbieters
> * Erstellen von Angeboten
> * Testen des Angebots

## <a name="deploy-the-app-service-resource-provider"></a>Bereitstellen des App Service-Ressourcenanbieters

1. [Prepare the Azure Stack Development Kit host (Vorbereiten des Azure Stack Development Kit-Hosts)](azure-stack-app-service-before-you-get-started.md). Dies schließt das Bereitstellen des SQL Server-Ressourcenanbieters ein, der für das Erstellen mancher Apps erforderlich ist.
2. [Download the installer and helper scripts (Herunterladen des Installationsprogramms und der Hilfsskripts)](azure-stack-app-service-deploy.md).
3. [Run the helper script to create required certificates (Ausführen des Hilfsskripts zum Erstellen der erforderlichen Zertifikate)](azure-stack-app-service-deploy.md).
4. [Installieren des App Service-Ressourcenanbieters](azure-stack-app-service-deploy.md). (Es dauert einige Stunden, bis dieser installiert ist und alle Workerrollen angezeigt werden.)
5. [Validate the installation (Überprüfen der Installation)](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Erstellen von Angeboten

Sie können beispielsweise ein Angebot erstellen, das es Benutzern ermöglicht, DNN-Systeme für die Verwaltung von Webinhalten zu erstellen. Dies erfordert einen SQL Server-Dienst, den Sie bei der Installation des SQL Server-Ressourcenanbieters bereits aktiviert haben.

1.  [Legen Sie ein Kontingent fest](azure-stack-plan-offer-quota-overview.md ), und nennen Sie dieses *AppServiceQuota*. Wählen Sie **Microsoft.Web** für das Feld **Namespace** aus.
2.  [Erstellen Sie einen Plan](azure-stack-create-plan.md). Nennen Sie ihn *TestAppServicePlan*, und wählen Sie den Dienst **Microsoft.SQL** und das Kontingent **AppService Quota** aus.

    > [!NOTE]
    > Damit Benutzer andere Apps erstellen können, können andere Dienste im Plan erforderlich sein. Azure Functions erfordert beispielsweise, dass der Plan den Dienst **Microsoft.Storage** enthält, während Wordpress **Microsoft.MySQL** erfordert.

3.  [Erstellen Sie ein Angebot](azure-stack-create-offer.md), nennen Sie dieses **TestAppServiceOffer**, und wählen Sie den Plan **TestAppServicePlan** aus.

## <a name="test-the-offer"></a>Testen des Angebots

Da Sie den App Service-Ressourcenanbieter nun bereitgestellt und ein Angebot erstellt haben, können Sie sich nun als Benutzer anmelden, das Angebot abonnieren und eine App erstellen.

In diesem Beispiel wird ein DNN-System für die Verwaltung von Plattforminhalten erstellt. Sie erstellen zuerst eine SQL-Datenbank und dann die DNN-Web-App.

### <a name="subscribe-to-the-offer"></a>Abonnieren des Angebots

1. Melden Sie sich als Mandant beim Azure Stack-Portal (https://portal.local.azurestack.external)) an.
2. Klicken Sie auf **Abonnement erwerben**, und geben Sie **TestAppServiceSubscription** unter **Anzeigename** > **Angebot auswählen** > **TestAppServiceOffer** > **Erstellen** ein.

### <a name="create-a-sql-database"></a>Erstellen einer SQL-Datenbank

1. Klicken Sie auf **+** > **Daten und Speicher** > **SQL-Datenbank**.
2. Übernehmen Sie mit Ausnahme der folgenden Felder die Standardwerte:

    - **Datenbankname**: DNNdb
    - **Maximale Größe in MB**: 100
    - **Abonnement**: TestAppServiceOffer
    - **Ressourcengruppe**: DNN-RG

3. Klicken Sie auf **Login Settings** (Anmeldeeinstellungen), geben Sie die Anmeldeinformationen für die Datenbank ein, und klicken Sie dann auf **OK**. Diese Anmeldeinformationen werden später in diesem Tutorial verwendet.
4. Wählen Sie unter **SKU** die SQL-SKU aus, die Sie für den SQL-Hostserver erstellt haben, und klicken Sie dann auf **OK**.
5. Klicken Sie auf **Erstellen**.

### <a name="create-a-dnn-app"></a>Erstellen einer DNN-App

1. Klicken Sie auf **+** > **See all (Alle anzeigen)** > **DNN Platform preview (Vorschauversion der DNN-Plattform)** > **Erstellen**.
2. Geben Sie *DNNapp* unter **App-Name** ein, und wählen Sie **TestAppServiceOffer** unter **Abonnement** aus.
3. Klicken Sie auf **Erforderliche Einstellungen konfigurieren** > **Neu erstellen**, und geben Sie einen Namen für den **App Service-Plan** ein.
4. Klicken Sie auf **Tarif** > **F1 Free** > **Auswählen** > **OK**.
5. Klicken Sie auf **Datenbank**, und geben Sie die Anmeldeinformationen für die SQL-Datenbank ein, die Sie zuvor erstellt haben.
6. Klicken Sie auf **Erstellen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen des App Service-Ressourcenanbieters
> * Erstellen von Angeboten
> * Testen des Angebots

Im nächsten Tutorial lernen Sie Folgendes:

> [!div class="nextstepaction"]
> [Deploy apps to Azure and Azure Stack (Bereitstellen von Apps in Azure und Azure Stack)](../user/azure-stack-solution-pipeline.md)
