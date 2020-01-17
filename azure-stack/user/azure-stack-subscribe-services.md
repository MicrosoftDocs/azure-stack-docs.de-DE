---
title: Erstellen eines Abonnements mit einem Angebot in Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein neues Abonnement mit einem Angebot in Azure Stack Hub erstellen und das Angebot dann mit einem virtuellen Testcomputer testen.
services: azure-stack
documentationcenter: ''
author: bryanla
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
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 216fe342bcda090d69452e8623f34f6c43cf70b9
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023193"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack-hub"></a>Tutorial: Erstellen und Testen eines Abonnements in Azure Stack Hub

In diesem Tutorial erfahren Sie, wie Sie ein Abonnement mit einem Angebot erstellen und es anschließend testen. Für den Test melden Sie sich beim Azure Stack Hub-Benutzerportal als Cloudadministrator an, abonnieren das Angebot und erstellen dann einen virtuellen Computer.

> [!TIP]
> Für eine komplexere Evaluierung können Sie [ein Abonnement für einen bestimmten Benutzer erstellen](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) und sich dann als dieser Benutzer beim Benutzerportal anmelden.

In diesem Tutorial erfahren Sie, wie Sie ein Azure Stack Hub-Angebot abonnieren.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Abonnieren von Angeboten 
> * Testen des Angebots

## <a name="subscribe-to-an-offer"></a>Abonnieren von Angeboten

Wenn Sie als Benutzer ein Angebot abonnieren möchten, müssen Sie sich beim Azure Stack Hub-Benutzerportal anmelden, um festzustellen, welche Dienste vom Azure Stack Hub-Betreiber angeboten werden.

1. Melden Sie sich beim Benutzerportal an, und wählen Sie **Abonnement erwerben** aus.

   ![Erwerben eines Abonnements](media/azure-stack-subscribe-services/get-subscription.png)

2. Geben Sie im Feld **Anzeigenamen** einen Namen für Ihr Abonnement ein. Klicken Sie auf **Angebot**, und wählen Sie im Abschnitt **Angebot wählen** eines der verfügbaren Angebote aus. Klicken Sie anschließend auf **Erstellen**.

   ![Erstellen von Angeboten](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Aktualisieren Sie nun das Benutzerportal, um mit der Verwendung Ihres Abonnements zu beginnen.

3. Wählen Sie zum Anzeigen des von Ihnen erstellten Abonnements **Alle Dienste** aus. Wählen Sie anschließend unter der Kategorie **ALLGEMEIN** die Option **Abonnements** und dann Ihr neues Abonnement aus. Aktualisieren Sie nach dem Abonnieren eines Angebots das Portal, um zu sehen, ob das neue Abonnement neue Dienste beinhaltet. In diesem Beispiel wurde **Virtuelle Computer** hinzugefügt.

   ![Anzeigen des Abonnements](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testen des Angebots

Wenn Sie beim Benutzerportal angemeldet sind, testen Sie das Angebot, indem Sie einen virtuellen Computer mit den Funktionen des neuen Abonnements bereitstellen.

> [!NOTE]
> Dieser Test setzt voraus, dass dem Azure Stack Hub-Marketplace ein virtueller Windows Server 2016 Datacenter-Computer hinzugefügt wurde.

1. Melden Sie sich am Benutzerportal an.

2. Wählen Sie im Benutzerportal **Virtual Machines** > **Hinzufügen** > **Windows Server 2016 Datacenter** und anschließend **Erstellen** aus.

3. Geben Sie im Abschnitt **Grundlagen** einen **Namen**, einen **Benutzernamen** und ein **Kennwort** ein, wählen Sie ein **Abonnement** aus, erstellen Sie eine **Ressourcengruppe** (oder wählen Sie eine vorhandene Ressourcengruppe aus), und klicken Sie dann auf **OK**.

4. Wählen Sie im Abschnitt **Größe auswählen** die Option **A1 Standard** und dann **Auswählen** aus.  

5. Behalten Sie auf dem Blatt **Einstellungen** die Standardwerte bei, und wählen Sie **OK** aus.

6. Wählen Sie im Bereich **Zusammenfassung** die Option **OK** aus, um den virtuellen Computer zu erstellen.  

7. Damit Sie den neuen virtuellen Computer sehen können, wählen Sie **Virtuelle Computer** aus, suchen nach dem neuen virtuellen Computer und wählen dann dessen Namen aus.

    ![Alle Ressourcen](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Die Bereitstellung des virtuellen Computers dauert einige Minuten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: create a VM using a community template](azure-stack-create-vm-template.md) (Erstellen eines virtuellen Computers über eine Communityvorlage)
