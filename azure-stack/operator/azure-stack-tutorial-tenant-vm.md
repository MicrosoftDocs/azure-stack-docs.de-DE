---
title: Verfügbarmachen von virtuellen Computern für Ihre Azure Stack-Benutzer | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie virtuelle Computer in Azure Stack verfügbar machen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: 949161cd90099d5b9486d7a10524fd915692c897
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618399"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Tutorial: Verfügbarmachen von virtuellen Computern für Ihre Azure Stack-Benutzer

Als Azure Stack-Cloudadministrator können Sie Angebote erstellen, die von Ihren Benutzern (ggf. auch als Mandanten bezeichnet) abonniert werden können. Durch das Abonnieren eines Angebots können Benutzer die Azure Stack-Dienste eines Angebots nutzen.

In diesem Tutorial wird gezeigt, wie Sie ein Angebot für einen virtuellen Computer erstellen und sich dann als Benutzer anmelden, um das Angebot zu testen.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen von Angeboten
> * Hinzufügen eines Image
> * Testen des Angebots

Dienste werden in Azure Stack mithilfe von Abonnements, Angeboten und Plänen für Benutzer bereitgestellt. Die Benutzer können mehrere Angebote abonnieren. Ein Angebot kann einen oder mehrere Pläne enthalten und ein Plan wiederum einen oder mehrere Dienste.

![Abonnements, Angebote und Pläne](media/azure-stack-key-features/image4.png)

Weitere Informationen finden Sie unter [Wichtige Features und Konzepte in Azure Stack](azure-stack-overview.md).

## <a name="create-an-offer"></a>Erstellen von Angeboten

Angebote sind Gruppen mit einem oder mehreren Plänen, die Anbieter Benutzern zum Erwerben oder Abonnieren anbieten. Der Prozess zum Erstellen eines Angebots umfasst mehrere Schritte. Sie werden nacheinander zum Erstellen des Angebots, eines Plans und von Kontingenten aufgefordert.

1. Führen Sie die [Anmeldung](../asdk/asdk-connect.md) beim Portal als Cloudadministrator durch, und klicken Sie anschließend auf **+ Eine Ressource erstellen** > **Angebote + Pläne** > **Angebot**.

   ![Neues Angebot](media/azure-stack-tutorial-tenant-vm/image01.png)

1. Geben Sie unter **Neues Angebot** einen Wert für **Anzeigename** und **Ressourcenname** an, und wählen Sie anschließend eine neue oder vorhandene **Ressourcengruppe** aus. Der Anzeigename ist der verständliche Anzeigename des Angebots. Der Ressourcenname ist nur für den Cloudbetreiber sichtbar und wird von Administratoren verwendet, um mit dem Angebot als Azure Resource Manager-Ressource zu arbeiten.

   ![`Display name`](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Klicken Sie auf **Basispläne** und im Abschnitt **Plan** auf **Hinzufügen**, um dem Angebot einen neuen Plan hinzuzufügen.

   ![Hinzufügen eines Plans](media/azure-stack-tutorial-tenant-vm/image03.png)

1. Geben Sie im Abschnitt **Neuer Plan** Werte für die Optionen **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der verständliche Name des Plans, der Benutzern angezeigt wird. Der Ressourcenname ist nur für den Cloudbetreiber sichtbar und wird von Cloudbetreibern verwendet, um mit dem Plan als Azure Resource Manager-Ressource zu arbeiten.

   ![Anzeigename des Plans](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Klicken Sie auf **Dienste**. Wählen Sie in der Liste der Dienste **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage** aus. Klicken Sie auf **Auswählen**, um diese Dienste zum Plan hinzuzufügen.

   ![Dienste des Plans](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Klicken Sie auf **Kontingente**, und wählen Sie dann den ersten Dienst aus, für den Sie ein Kontingent erstellen möchten. Verwenden Sie für ein IaaS-Kontingent das folgende Beispiel als Leitfaden für das Konfigurieren von Kontingenten für den Compute-, den Netzwerk- und den Storage-Dienst.

   - Erstellen Sie zunächst ein Kontingent für den Computedienst. Wählen Sie in der Liste mit den Namespaces **Microsoft.Compute** aus, und klicken Sie anschließend auf **Neues Kontingent erstellen**.

     ![Erstellen eines neuen Kontingents](media/azure-stack-tutorial-tenant-vm/image06.png)

   - Geben Sie unter **Kontingent erstellen** einen Namen für das Kontingent ein. Sie können die angezeigten Kontingentwerte für das von Ihnen erstellte Kontingent ändern oder übernehmen. In diesem Beispiel übernehmen Sie die Standardeinstellungen und klicken auf **OK**.

     ![Namen des Kontingents](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Wählen Sie in der Liste der Namespaces **Microsoft.Compute** und dann das von Ihnen erstellte Kontingent aus. Dadurch wird das Kontingent mit dem Computedienst verknüpft.

     ![Auswählen des Kontingents](media/azure-stack-tutorial-tenant-vm/image08.png)

      Wiederholen Sie diese Schritte für den Netzwerk- und den Storage-Dienst. Wenn Sie fertig sind, klicken Sie unter **Kontingente** auf **OK**, um alle Kontingente zu speichern.

1. Klicken Sie unter **Neuer Plan** auf **OK**.

1. Wählen Sie unter **Plan** den neuen Plan aus, und klicken Sie auf **Auswählen**.

1. Klicken Sie unter **Neues Angebot** auf **Erstellen**. Sie erhalten eine Benachrichtigung, wenn das Angebot erstellt wird.

1. Klicken Sie im Dashboardmenü auf **Angebote** und dann auf das von Ihnen erstellte Angebot.

1. Klicken Sie auf **Status ändern** und dann auf **Öffentlich**.

    ![Status „Öffentlich“](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Hinzufügen eines Image

Bevor Sie virtuelle Computer bereitstellen können, müssen Sie dem Azure Stack-Marketplace ein Image hinzufügen. Sie können über den Azure Marketplace das Image Ihrer Wahl, z.B. auch Linux-Images, hinzufügen.

Wenn Sie ein verbundenes Szenario nutzen und Ihre Azure Stack-Instanz bei Azure registriert haben, können Sie das Windows Server 2016-VM-Image vom Azure Marketplace herunterladen, indem Sie die Schritte im Thema [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md) ausführen.

Informationen zum Hinzufügen von unterschiedlichen Elementen zum Marketplace finden Sie unter [Der Azure Stack-Marketplace – Übersicht](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testen des Angebots

Nachdem Sie ein Angebot erstellt haben, können Sie es testen. Melden Sie sich als Benutzer an, und abonnieren Sie das Angebot. Fügen Sie anschließend einen virtuellen Computer hinzu.

1. **Abonnieren von Angeboten**

   a. Melden Sie sich beim Benutzerportal mit einem Benutzerkonto an, und klicken Sie auf die Kachel **Abonnement erwerben**.
   - Bei einem integrierten System variiert die URL abhängig von der Region und dem externen Domänenamen des Betreibers und hat das Format https://portal.&lt;*Region*&gt;.&lt;*FQDN*&gt;.
   - Wenn Sie das Azure Stack Development Kit verwenden, lautet die Portaladresse https://portal.local.azurestack.external.

   ![Erwerben eines Abonnements](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. Geben Sie unter **Abonnement erwerben** im Feld **Anzeigename** einen Namen für Ihr Abonnement ein. Klicken Sie auf **Angebot**, und wählen Sie dann eines der Angebote in der Liste **Angebot wählen** aus. Klicken Sie auf **Erstellen**.

   ![Erstellen von Angeboten](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Klicken Sie zum Anzeigen des Abonnements auf **Alle Dienste** und anschließend unter der Kategorie **ALLGEMEIN** auf **Abonnements**. Wählen Sie Ihr neues Abonnement aus, um anzuzeigen, welche Dienste zum Abonnement gehören.

   >[!NOTE]
   >Nach dem Abonnieren eines Angebots müssen Sie das Portal unter Umständen aktualisieren, um anzuzeigen, welche Dienste zum neuen Abonnement gehören.

1. **Bereitstellen von virtuellen Computern**

   Über das Benutzerportal können Sie einen virtuellen Computer mithilfe des neuen Abonnements bereitstellen.

   a. Melden Sie sich mit einem Benutzerkonto beim Benutzerportal an.
   - Bei einem integrierten System variiert die URL abhängig von der Region und dem externen Domänenamen des Betreibers und hat das Format https://portal.&lt;*Region*&gt;.&lt;*FQDN*&gt;.
     - Wenn Sie das Azure Stack Development Kit verwenden, lautet die Portaladresse https://portal.local.azurestack.external.

   b.  Klicken Sie auf dem Dashboard auf **+ Eine Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter Eval** und dann auf **Erstellen**.

   c. Geben Sie unter **Grundlagen** die folgenden Informationen ein:
      - Geben Sie unter **Name** einen Namen ein.
      - Geben Sie unter **Benutzername** einen Benutzernamen ein.
      - Geben Sie ein **Kennwort** ein.
      - Wählen Sie ein **Abonnement**
      - Erstellen Sie eine **Ressourcengruppe** (oder wählen Sie eine vorhandene Ressourcengruppe aus). 
      - Wählen Sie **OK**, um diese Informationen zu speichern.

   d. Wählen Sie unter **Größe auswählen** die Option **A1 Standard** aus, und klicken Sie anschließend auf **Auswählen**.  

   e. Klicken Sie unter **Einstellungen** auf **Virtuelles Netzwerk**.

   f. Wählen Sie unter **Virtuelles Netzwerk auswählen** die Option **Neu erstellen** aus.

   g. Übernehmen Sie unter **Virtuelles Netzwerk erstellen** alle Standardeinstellungen, und klicken Sie auf **OK**.

   h. Klicken Sie unter **Einstellungen** auf **OK**, um die Netzwerkkonfiguration zu speichern.

      i. Klicken Sie unter **Zusammenfassung** auf **OK**, um den virtuellen Computer zu erstellen.  

   j. Klicken Sie zum Anzeigen des neuen virtuellen Computers auf **Alle Ressourcen**. Suchen Sie nach dem virtuellen Computer, und klicken Sie in den Suchergebnissen auf seinen Namen.

   
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen von Angeboten
> * Hinzufügen eines Image
> * Testen des Angebots

Im nächsten Tutorial lernen Sie Folgendes:
> [!div class="nextstepaction"]
> [Verfügbarmachen von SQL-Datenbanken für Ihre Azure Stack-Benutzer](azure-stack-tutorial-sql-server.md)