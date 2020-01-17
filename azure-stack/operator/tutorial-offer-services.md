---
title: Bieten Sie Azure Stack Hub-Dienste als Abonnement an.
description: Erfahren Sie, wie Sie mit Angeboten, Plänen und Diensten ein Dienstangebot erstellen.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 331d76a61ec67165473702d47f35c02533dcd0b8
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816563"
---
# <a name="tutorial-offer-a-service-to-users"></a>Tutorial: Anbieten eines Diensts für Benutzer

Dieses Tutorial zeigt einem Operator, wie ein Angebot erstellt wird. Ein Angebot macht Dienste für Benutzer auf Abonnementbasis verfügbar. Nachdem ein Benutzer ein Angebot abonniert hat, ist er berechtigt, Ressourcen innerhalb der Dienste zu erstellen und bereitzustellen, die im Angebot enthalten sind.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Angeboten
> * Erstellen von Plänen
> * Zuweisen von Diensten und Kontingenten zu einem Plan
> * Zuweisen eines Plans zu einem Angebot

## <a name="overview"></a>Übersicht

Ein Angebot besteht aus mindestens einem Plan. Ein Plan berechtigt zum Zugriff auf einen oder mehrere Dienste, indem er den entsprechenden Ressourcenanbieter und ein Kontingent für jeden Dienst festlegt. Pläne können einem Angebot als Basisplan hinzugefügt werden oder das Angebot als Add-On-Plan erweitern. Weitere Informationen finden Sie in der [Übersicht über Dienste, Pläne, Angebote und Abonnements](service-plan-offer-subscription-overview.md).

![Abonnements, Angebote und Pläne](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>Ressourcenanbieter

Ein Ressourcenanbieter unterstützt die Erstellung, Bereitstellung und Verwaltung seiner Ressourcen als Dienste. Ein gängiges Beispiel ist der Ressourcenanbieter „Microsoft.Compute“, der die Möglichkeit bietet, virtuelle Computer (VMs) zu erstellen und bereitzustellen. Eine Übersicht über das Azure-Ressourcenverwaltungsmodell finden Sie unter [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

In Azure Stack Hub gibt es zwei allgemeine Kategorien von Ressourcenanbietern: solche, die Ressourcen als grundlegende Dienste bereitstellen, und solche, die sie als Mehrwertdienste bereitstellen.

### <a name="foundational-services"></a>Grundlegende Dienste

>[!NOTE]
> In diesem Tutorial erfahren Sie, wie Sie ein Angebot erstellen, das auf grundlegenden Diensten basiert. 

Grundlegende Dienste werden von den folgenden Ressourcenanbietern unterstützt, die bei jeder Installation von Azure Stack Hub nativ verfügbar sind:

| Ressourcenanbieter | Beispielressourcen |
| ----------------- | ------------------|
| Microsoft.Compute | Virtuelle Computer, Datenträger und VM-Skalierungsgruppen |
| Microsoft.KeyVault | Schlüsseltresore, Geheimnisse |
| Microsoft.Network | Virtuelle Netzwerke, öffentliche IP-Adressen, Lastenausgleichsmodule |
| Microsoft.Storage | Speicherkonten, Blobs, Warteschlangen, Tabellen |

### <a name="value-add-services"></a>Mehrwertdienste

>[!NOTE]
> Damit ein Mehrwertdienst angeboten werden kann, muss zunächst der entsprechende Ressourcenanbieter im Azure Stack Hub-Marketplace installiert werden. Nachdem der Anbieter installiert wurde, werden seine Ressourcen den Benutzern auf die gleiche Weise wie grundlegende Dienste angeboten. Im Abschnitt **Schrittanleitungen** im Inhaltsverzeichnis finden Sie die aktuellen Ressourcenanbieter, die Angebote für Mehrwertdienste unterstützen.

Mehrwertdienste werden von Ressourcenanbietern unterstützt, die nach der Bereitstellung von Azure Stack Hub installiert wurden. Beispiele:

| Ressourcenanbieter | Beispielressourcen |
| ----------------- | ------------------------- |
| Microsoft.Web | App Service-Funktions-Apps, Web-Apps, API-Apps | 
| Microsoft.MySqlAdapter | MySQL-Hostserver, MySQL-Datenbank | 
| Microsoft.SqlAdapter | SQL Server-Hostserver, SQL Server-Datenbank |

::: moniker range=">=azs-1902"
## <a name="create-an-offer"></a>Erstellen von Angeboten

Bei der Angebotserstellung erstellen Sie sowohl ein Angebot als auch einen Plan. Der Plan wird als Basisplan des Angebots verwendet. Bei der Planerstellung legen Sie fest, welche Dienste im Plan zur Verfügung gestellt werden und welche Kontingente für sie gelten.

1. Melden Sie sich mit einem Cloudadministratorkonto beim Administratorportal an.

   - Bei einem integrierten System variiert die URL je nach Region und dem externen Domänennamen des Betreibers und hat das Format https://adminportal.&lt;*Region*&gt;.&lt;*FQDN*&gt;.
   - Wenn Sie das Azure Stack Development Kit verwenden, lautet die URL https://adminportal.local.azurestack.external.

   Wählen Sie dann **+ Ressource erstellen** > **Angebote + Pläne** > **Angebot** aus.

   ![Neues Angebot](media/tutorial-offer-services/1-create-resource-offer.png)

1. Geben Sie auf der Registerkarte **Grundlagen** in **Neues Angebot** einen **Anzeigenamen** sowie einen **Ressourcennamen** ein, und wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue. Der Anzeigename ist der verständliche Anzeigename des Angebots. Der Ressourcenname ist nur für den Cloudbetreiber sichtbar und wird von Administratoren verwendet, um mit dem Angebot als Azure Resource Manager-Ressource zu arbeiten.

   ![`Display name`](media/tutorial-offer-services/2-create-new-offer.png)

1. Wählen Sie die Registerkarte **Basispläne** und dann die Option **Neuen Plan erstellen** aus, um einen neuen Plan zu erstellen. Der Plan wird dem Angebot auch als Basisplan hinzugefügt.

   ![Hinzufügen eines Plans](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. Geben Sie auf der Registerkarte **Grundlagen** in **Neuer Plan** einen **Anzeigenamen** und einen **Ressourcennamen** ein. Der Anzeigename ist der verständliche Name des Plans, der Benutzern angezeigt wird. Der Ressourcenname ist nur für den Cloudbetreiber sichtbar und wird von Cloudbetreibern verwendet, um mit dem Plan als Azure Resource Manager-Ressource zu arbeiten. Die **Ressourcengruppe** wird auf jene festgelegt, die für das Angebot angegeben ist.

   ![Anzeigename des Plans](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. Wählen Sie die Registerkarte **Dienste** aus, damit eine Liste der Dienste angezeigt wird, die von den installierten Ressourcenanbietern zur Verfügung gestellt werden. Wählen Sie **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage** aus. 

   ![Dienste des Plans](media/tutorial-offer-services/5-create-new-plan-services.png)

1. Wählen Sie die Registerkarte **Kontingente** aus, damit die Liste der Dienste angezeigt wird, die Sie für diesen Plan aktiviert haben. Klicken Sie auf **Neu erstellen**, um ein benutzerdefiniertes Kontingent für **Microsoft.Compute** anzugeben. Für das Kontingent muss ein **Name** angegeben werden. Sie können den Wert für die einzelnen Kontingente übernehmen oder ändern. Wenn Sie fertig sind, wählen Sie **OK** aus, und wiederholen Sie diese Schritte für die verbleibenden Dienste.

   ![Computekontingent erstellen](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. Wählen Sie die Registerkarte **Überprüfen + erstellen** aus. Oben sollte das grüne Banner „Überprüfung erfolgreich“ zu sehen sein, das anzeigt, dass der neue Basisplan für die Erstellung bereit ist. Klicken Sie auf **Erstellen**. Außerdem sollte eine Benachrichtigung mit dem Hinweis angezeigt werden, dass der Plan erstellt wurde.

   ![Erstellen eines neuen Plans](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. Nachdem Sie zur Registerkarte **Basispläne** der Seite **Neues Angebot erstellen** zurückgekehrt sind, sehen Sie, dass der Plan erstellt wurde. Stellen Sie sicher, dass der neue Plan als Basisplan für die Einbindung in das Angebot ausgewählt ist, und wählen Sie dann **Überprüfen + erstellen** aus.

   ![Basisplan hinzufügen](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. Auf der Registerkarte **Überprüfen + erstellen** sollte oben das grüne Banner „Überprüfung erfolgreich“ angezeigt werden. Überprüfen Sie die Informationen zu „Grundlagen“ und „Basispläne“, und wählen Sie **Erstellen** aus, wenn Sie fertig sind. 

   ![Erstellen eines neuen Angebots](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. Zuerst wird die Seite „Ihre Bereitstellung wird ausgeführt.“ gefolgt von „Ihre Bereitstellung wurde abgeschlossen.“ angezeigt, sobald das Angebot bereitgestellt ist. Klicken Sie in der Spalte **Ressource** auf den Namen des Angebots.

   ![Angebotsbereitstellung abgeschlossen](media/tutorial-offer-services/10-offer-deployment-complete.png)


1. Beachten Sie das Banner, das anzeigt, dass das Angebot noch privat ist, d. h. Benutzer können es nicht abonnieren. Machen Sie es öffentlich, indem Sie **Status ändern** und dann den Wert **Öffentlich** auswählen.

    ![Status „Öffentlich“](media/tutorial-offer-services/11-offer-change-state.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Erstellen von Angeboten (bis 1901)

Bei der Angebotserstellung erstellen Sie sowohl ein Angebot als auch einen Plan. Der Plan wird als Basisplan des Angebots verwendet. Bei der Planerstellung legen Sie fest, welche Dienste im Plan zur Verfügung gestellt werden und welche Kontingente für sie gelten.

1. Melden Sie sich mit einem Cloudadministratorkonto beim Administratorportal an.

   - Bei einem integrierten System variiert die URL je nach Region und dem externen Domänennamen des Betreibers und hat das Format https://adminportal.&lt;*Region*&gt;.&lt;*FQDN*&gt;.
   - Wenn Sie das Azure Stack Development Kit verwenden, lautet die URL https://adminportal.local.azurestack.external.
   
   Wählen Sie dann **+ Ressource erstellen** > **Angebote + Pläne** > **Angebot** aus.

   ![Neues Angebot](media/tutorial-offer-services/image01.png)

1. Geben Sie unter **Neues Angebot** einen Wert für **Anzeigename** und **Ressourcenname** an, und wählen Sie anschließend eine neue oder vorhandene **Ressourcengruppe** aus. Der Anzeigename ist der verständliche Anzeigename des Angebots. Der Ressourcenname ist nur für den Cloudbetreiber sichtbar und wird von Administratoren verwendet, um mit dem Angebot als Azure Resource Manager-Ressource zu arbeiten.

   ![`Display name`](media/tutorial-offer-services/image02.png)

1. Klicken Sie auf **Basispläne** und im Abschnitt **Plan** auf **Hinzufügen**, um dem Angebot einen neuen Plan hinzuzufügen.

   ![Hinzufügen eines Plans](media/tutorial-offer-services/image03.png)

1. Geben Sie im Abschnitt **Neuer Plan** Werte für die Optionen **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der verständliche Name des Plans, der Benutzern angezeigt wird. Der Ressourcenname ist nur für den Cloudbetreiber sichtbar und wird von Cloudbetreibern verwendet, um mit dem Plan als Azure Resource Manager-Ressource zu arbeiten.

   ![Anzeigename des Plans](media/tutorial-offer-services/image04.png)

1. Klicken Sie auf **Dienste**. Wählen Sie in der Liste der Dienste **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage** aus. Klicken Sie auf **Auswählen**, um diese Dienste zum Plan hinzuzufügen.

   ![Dienste des Plans](media/tutorial-offer-services/image05.png)

1. Klicken Sie auf **Kontingente**, und wählen Sie dann den ersten Dienst aus, für den Sie ein Kontingent erstellen möchten. Verwenden Sie für ein IaaS-Kontingent das folgende Beispiel als Leitfaden für das Konfigurieren von Kontingenten für den Compute-, den Netzwerk- und den Storage-Dienst.

   - Erstellen Sie zunächst ein Kontingent für den Computedienst. Wählen Sie in der Liste mit den Namespaces **Microsoft.Compute** aus, und klicken Sie anschließend auf **Neues Kontingent erstellen**.

     ![Erstellen eines neuen Kontingents](media/tutorial-offer-services/image06.png)

   - Geben Sie unter **Kontingent erstellen** einen Namen für das Kontingent ein. Sie können die angezeigten Kontingentwerte ändern oder übernehmen. In diesem Beispiel übernehmen Sie die Standardeinstellungen und klicken auf **OK**.

     ![Namen des Kontingents](media/tutorial-offer-services/image07.png)

   - Wählen Sie in der Liste der Namespaces **Microsoft.Compute** und dann das von Ihnen erstellte Kontingent aus. In diesem Schritt wird das Kontingent mit dem Computedienst verknüpft.

     ![Auswählen des Kontingents](media/tutorial-offer-services/image08.png)

      Wiederholen Sie diese Schritte für den Netzwerk- und den Storage-Dienst. Wenn Sie fertig sind, klicken Sie unter **Kontingente** auf **OK**, um alle Kontingente zu speichern.

1. Klicken Sie unter **Neuer Plan** auf **OK**.

1. Wählen Sie unter **Plan** den neuen Plan aus, und klicken Sie auf **Auswählen**.

1. Klicken Sie unter **Neues Angebot** auf **Erstellen**. Sie erhalten eine Benachrichtigung, wenn das Angebot erstellt wird.

1. Klicken Sie im Dashboardmenü auf **Angebote** und dann auf das von Ihnen erstellte Angebot.

1. Klicken Sie auf **Status ändern** und dann auf **Öffentlich**.

    ![Status „Öffentlich“](media/tutorial-offer-services/image09.png)
::: moniker-end
 
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen von Angeboten
> * Erstellen von Plänen
> * Zuweisen von Diensten und Kontingenten zu einem Plan
> * Zuweisen eines Plans zu einem Angebot

Im nächsten Tutorial lernen Sie Folgendes:
> [!div class="nextstepaction"]
> [Testen der in diesem Tutorial angebotenen Dienste](tutorial-test-offer.md)
