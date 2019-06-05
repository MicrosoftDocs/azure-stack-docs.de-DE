---
title: 'Tutorial: Abonnieren eines Azure Stack-Angebots | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie ein neues Abonnement für Azure Stack-Dienste erstellen und das Angebot mit einem erstellten virtuellen Testcomputer testen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 3f577ebd5a57b271a449b5db262ea70779ebdb3d
ms.sourcegitcommit: a427e72e4f3b6cd6000b1459af9bbf221e049e08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66506366"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Tutorial: Erstellen und Testen eines Abonnements

In diesem Tutorial erfahren Sie, wie Sie ein Abonnement mit einem Angebot erstellen und es anschließend testen. Für den Test melden Sie sich beim Azure Stack-Benutzerportal als Cloudadministrator an, abonnieren das Angebot und erstellen dann einen virtuellen Computer.

> [!TIP]
> Für eine komplexere Evaluierung können Sie [ein Abonnement für einen bestimmten Benutzer erstellen](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) und sich dann als dieser Benutzer beim Benutzerportal anmelden.

In diesem Tutorial erfahren Sie, wie Sie ein Azure Stack-Angebot abonnieren.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Abonnieren von Angeboten 
> * Testen des Angebots

## <a name="subscribe-to-an-offer"></a>Abonnieren von Angeboten

Wenn Sie als Benutzer ein Angebot abonnieren möchten, müssen Sie sich beim Azure Stack-Benutzerportal anmelden, um die Dienste zu ermitteln, die vom Azure Stack-Betreiber angeboten werden.

1. Melden Sie sich beim Benutzerportal an, und wählen Sie **Abonnement erwerben** aus.

   ![Erwerben eines Abonnements](media/azure-stack-subscribe-services/get-subscription.png)

2. Geben Sie im Feld **Anzeigenamen** einen Namen für Ihr Abonnement ein. Klicken Sie auf **Angebot**, und wählen Sie im Abschnitt **Angebot wählen** eines der verfügbaren Angebote aus. Klicken Sie anschließend auf **Erstellen**.

   ![Erstellen von Angeboten](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Aktualisieren Sie nun das Benutzerportal, um mit der Verwendung Ihres Abonnements zu beginnen.

3. Wählen Sie zum Anzeigen des von Ihnen erstellten Abonnements **Alle Dienste** aus. Wählen Sie anschließend unter der Kategorie **ALLGEMEIN** die Option **Abonnements** und dann Ihr neues Abonnement aus. Aktualisieren Sie nach dem Abonnieren eines Angebots das Portal, um zu sehen, ob das neue Abonnement neue Dienste beinhaltet. In diesem Beispiel wurde **Virtuelle Computer** hinzugefügt.

   ![Anzeigen des Abonnements](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testen des Angebots

Wenn Sie beim Benutzerportal angemeldet sind, können Sie das Angebot testen, indem Sie einen virtuellen Computer bereitstellen, der die Funktionen des neuen Abonnements verwendet.

> [!NOTE]
> Dieser Test setzt voraus, dass dem Azure Stack-Marketplace ein virtueller Windows Server 2016 Datacenter-Computer hinzugefügt wurde.

1. Melden Sie sich am Benutzerportal an.

2. Wählen Sie im Benutzerportal **Virtual Machines** > **Hinzufügen** > **Windows Server 2016 Datacenter** aus, und klicken Sie anschließend auf **Erstellen**.

3. Geben Sie im Abschnitt **Grundlagen** einen **Namen**, einen **Benutzernamen** und ein **Kennwort** ein, wählen Sie ein **Abonnement** aus, erstellen Sie eine **Ressourcengruppe** (oder wählen Sie eine vorhandene Ressourcengruppe aus), und klicken Sie dann auf **OK**.

4. Wählen Sie im Abschnitt **Größe auswählen** die Option **A1 Standard** aus, und klicken Sie dann auf **Auswählen**.  

5. Behalten Sie auf dem Blatt **Einstellungen** die Standardwerte bei, und wählen Sie **OK** aus.

6. Klicken Sie im Abschnitt **Zusammenfassung** auf **OK**, um den virtuellen Computer zu erstellen.  

7. Wählen Sie zum Anzeigen Ihres neuen virtuellen Computers **Virtuelle Computer** aus, suchen Sie nach dem neuen virtuellen Computer, und klicken Sie auf seinen Namen.

    ![Alle Ressourcen](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Die Bereitstellung des virtuellen Computers dauert einige Minuten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Abonnieren von Angeboten 
> * Testen des Angebots

> [!div class="nextstepaction"]
> [Tutorial: create a VM using a community template](azure-stack-create-vm-template.md) (Erstellen eines virtuellen Computers über eine Communityvorlage)