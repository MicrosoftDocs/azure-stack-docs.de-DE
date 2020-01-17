---
title: Testen Sie ein Azure Stack Hub-Dienstangebot.
description: Erfahren Sie, wie Sie ein Dienstangebot testen, indem Sie ein Abonnement erstellen und Ressourcen bereitstellen.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: fd14a6099c9906a0b37c8227164b213235dcfee3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820932"
---
# <a name="tutorial-test-a-service-offering"></a>Tutorial: Testen eines Dienstangebots

Im vorherigen Tutorial haben Sie ein Angebot für Benutzer erstellt. In diesem Tutorial erfahren Sie, wie Sie dieses Angebot testen, indem Sie ein Abonnement damit erstellen. Anschließend können Sie Ressourcen für die grundlegenden Dienste erstellen und bereitstellen, für die das Abonnement berechtigt ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Abonnements
> * Erstellen und Bereitstellen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, müssen folgende Schritte ausgeführt werden:

- Schließen Sie das Tutorial [Anbieten eines Diensts für Benutzer](tutorial-offer-services.md) ab. Darin erfahren Sie, wie Sie das von diesem Tutorial verwendete Angebot erstellen.

- Das Angebot, das Sie in diesem Tutorial abonnieren, ermöglicht die Bereitstellung einer Ressource eines virtuellen Computers (VM). Wenn Sie die VM-Bereitstellung testen möchten, müssen Sie zunächst ein VM-Image in Azure Stack Hub Marketplace bereitstellen, indem Sie es von Azure Marketplace herunterladen. Die entsprechenden Anweisungen finden Sie unter [Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). 

## <a name="subscribe-to-the-offer"></a>Abonnieren des Angebots

1. Melden Sie sich mit einem Benutzerkonto beim Benutzerportal an. 

   - Bei einem integrierten System variiert die URL je nach Region und dem externen Domänennamen des Betreibers und hat das Format https://portal.&lt;*Region*&gt;.&lt;*FQDN*&gt;.
   - Wenn Sie das Azure Stack Development Kit verwenden, lautet die Portaladresse https://portal.local.azurestack.external.

1. Wählen Sie die Kachel **Erwerben eines Abonnements** aus.

   ![Erwerben eines Abonnements](media/tutorial-test-offer/1-get-subscription.png)

1. Geben Sie unter **Abonnement erwerben** im Feld **Anzeigename** einen Namen für Ihr neues Abonnement ein. Wählen Sie **Angebot** aus, und wählen Sie dann in der Liste **Angebot wählen** das Angebot aus, das Sie im vorherigen Tutorial erstellt haben. Klicken Sie auf **Erstellen**.

   ![Erstellen von Angeboten](media/tutorial-test-offer/2-create-subscription.png)

1. Klicken Sie zum Anzeigen des Abonnements auf **Alle Dienste** und anschließend unter der Kategorie **ALLGEMEIN** auf **Abonnements**. Wählen Sie Ihr neues Abonnement aus, um das Angebot anzuzeigen, dem es zugeordnet ist, und seine Eigenschaften.

   >[!NOTE]
   >Nach dem Abonnieren eines Angebots müssen Sie das Portal unter Umständen aktualisieren, um anzuzeigen, welche Dienste zum neuen Abonnement gehören.

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>Bereitstellen einer Speicherkontoressource

Über das Benutzerportal stellen Sie ein Speicherkonto mit dem Abonnement bereit, das Sie im vorherigen Abschnitt erstellt haben.

1. Melden Sie sich mit einem Benutzerkonto beim Benutzerportal an.

1. Wählen Sie **+Ressource erstellen** > **Daten und Speicher** > **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** aus.

1. Geben Sie in **Speicherkonto erstellen** die folgenden Informationen an:
  
   - Geben Sie unter **Name** einen Namen ein.
   - Wählen Sie Ihr neues **Abonnement** aus.
   - Wählen Sie eine **Ressourcengruppe** aus (oder erstellen Sie eine). 
   - Wählen Sie **Erstellen**, um das Speicherkonto zu erstellen.

1. Sobald die Bereitstellung gestartet wurde, kehren Sie zum Dashboard zurück. Wählen Sie zum Anzeigen des neuen Speicherkontos **Alle Ressourcen** aus. Suchen Sie nach dem Speicherkonto, und wählen Sie in den Suchergebnissen seinen Namen aus. Hier können Sie das Speicherkonto und seinen Inhalt verwalten.

## <a name="deploy-a-virtual-machine-resource"></a>Bereitstellen einer Ressource eines virtuellen Computers

Über das Benutzerportal stellen Sie einen virtuellen Computer mit dem Abonnement bereit, das Sie im vorherigen Abschnitt erstellt haben.

1. Melden Sie sich mit einem Benutzerkonto beim Benutzerportal an.

1. Wählen Sie **+Ressource erstellen** > **Compute** > **\<image-name\>** aus, wobei „image-name“ der Name des virtuellen Computers ist, den Sie unter „Voraussetzungen“ heruntergeladen haben.
1. Geben Sie unter **Virtuellen Computer erstellen** / **Grundlagen** die folgenden Informationen ein:
  
   - Geben Sie einen **Namen** für den virtuellen Computer ein.
   - Geben Sie einen **Benutzernamen** für das Administratorkonto ein.
   - Wählen Sie für virtuelle Linux-Computer die Option „Kennwort“ als **Authentifizierungstyp** aus.
   - Geben Sie für das Administratorkonto unter **Kennwort** und **Kennwort bestätigen** denselben Wert ein.
   - Wählen Sie Ihr neues **Abonnement** aus.
   - Wählen Sie eine **Ressourcengruppe** aus (oder erstellen Sie eine). 
   - Wählen Sie **OK** aus, um diese Informationen zu überprüfen und fortzufahren.

1. In **Größe auswählen** filtern Sie ggf. die Liste, wählen Sie eine VM-SKU aus, und wählen Sie **Auswählen** aus.  
1. Geben Sie in **Einstellungen** unter **Öffentliche Eingangsports hinzufügen** die zu öffnenden Ports an, und wählen Sie **OK** aus.
   > [!NOTE]
   > Wenn Sie z. B. „RDP(3389)“ auswählen, können Sie eine Remoteverbindung mit dem virtuellen Computer herstellen, wenn er ausgeführt wird.
1. Überprüfen Sie Ihre Auswahl unter **Zusammenfassung**, und wählen Sie dann **OK** aus, um den virtuellen Computer zu erstellen.  
1. Sobald die Bereitstellung gestartet wurde, kehren Sie zum Dashboard zurück. Klicken Sie zum Anzeigen des neuen virtuellen Computers auf **Alle Ressourcen**. Suchen Sie nach dem virtuellen Computer, und klicken Sie in den Suchergebnissen auf seinen Namen. Hier können Sie auf den virtuellen Computer zugreifen und ihn verwalten.
   > [!NOTE]
   > Die vollständige Bereitstellung und das Starten des virtuellen Computers können einige Minuten dauern. Sobald der virtuelle Computer einsatzbereit ist, wird der [Status](/azure/virtual-machines/windows/states-lifecycle) in „Wird ausgeführt“ geändert.

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>Bereitstellen einer Ressource eines virtuellen Computers (bis 1901)

Über das Benutzerportal stellen Sie einen virtuellen Computer mithilfe des neuen Abonnements bereit.

1. Melden Sie sich mit einem Benutzerkonto beim Benutzerportal an.

1. Wählen Sie auf dem Dashboard **+ Eine Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter Eval** und dann **Erstellen** aus.

1. Geben Sie unter **Grundlagen** die folgenden Informationen ein:
  
   - Geben Sie unter **Name** einen Namen ein.
   - Geben Sie unter **Benutzername** einen Benutzernamen ein.
   - Geben Sie ein **Kennwort** ein.
   - Auswählen Ihres neuen **Abonnements**
   - Erstellen Sie eine **Ressourcengruppe** (oder wählen Sie eine vorhandene Ressourcengruppe aus). 
   - Wählen Sie **OK**, um diese Informationen zu speichern.

1. Wählen Sie unter **Größe auswählen** die Option **A1 Standard** aus, und klicken Sie anschließend auf **Auswählen**.  
1. Klicken Sie unter **Einstellungen** auf **Virtuelles Netzwerk**.

1. Wählen Sie unter **Virtuelles Netzwerk auswählen** die Option **Neu erstellen** aus.

1. Übernehmen Sie unter **Virtuelles Netzwerk erstellen** alle Standardeinstellungen, und klicken Sie auf **OK**.

1. Klicken Sie unter **Einstellungen** auf **OK**, um die Netzwerkkonfiguration zu speichern.

1. Klicken Sie unter **Zusammenfassung** auf **OK**, um den virtuellen Computer zu erstellen.  

1. Klicken Sie zum Anzeigen des neuen virtuellen Computers auf **Alle Ressourcen**. Suchen Sie nach dem virtuellen Computer, und klicken Sie in den Suchergebnissen auf seinen Namen.
::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Abonnements
> * Erstellen und Bereitstellen von Ressourcen 

Als Nächstes erfahren Sie mehr über das Bereitstellen von Ressourcenanbietern für Mehrwertdienste. Mit diesen können Sie Benutzern in ihren Plänen sogar noch mehr Dienste anbieten:

- [Anbieten von SQL in Azure Stack Hub](azure-stack-sql-resource-provider.md)
- [Anbieten von MySQL in Azure Stack Hub](azure-stack-mysql-resource-provider.md)
- [Anbieten von App Service in Azure Stack Hub](azure-stack-app-service-overview.md)
