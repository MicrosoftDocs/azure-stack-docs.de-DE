---
title: Aktualisieren von Azure Stack-Angeboten und -Plänen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie vorhandene Azure Stack-Angebote und -Pläne anzeigen und ändern.
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
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 54c6c44d6ab5d03819ba07ca1d34269e7a87ee77
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836793"
---
# <a name="azure-stack-add-on-plans"></a>Add-On-Pläne für Azure Stack

Als Azure Stack-Bediener erstellen Sie Add-On-Pläne, um einen [Basisplan](azure-stack-create-plan.md) zu ändern, wenn Sie zusätzliche Dienste anbieten oder die *Computer*-, *Speicher*- oder *Netzwerk*kontingente ändern möchten, die diesen Basisplan ursprünglich enthalten. Add-On-Pläne verändern den Basisplan und sind optionale Erweiterungen, die Benutzer abonnieren können.

Manchmal besteht die optimale Lösung darin, alles in einem einzelnen Plan zu kombinieren. In anderen Fällen bietet es sich an, einen Basisplan zu verwenden und zusätzliche Dienste mithilfe von Add-On-Plänen anzubieten. Beispielsweise können Sie die Entscheidung treffen, IaaS-Dienste im Rahmen eines Basisplans anzubieten und alle PaaS-Dienste als Add-On-Pläne zu behandeln.

Add-On-Pläne können auch bei der Überwachung der Ressourcennutzung hilfreich sein. Hierfür können Sie mit einem Basisplan beginnen, der relativ kleine Kontingente enthält (je nach den erforderlichen Diensten). Wenn die Benutzer die Kapazitätsgrenze erreichen, werden sie darüber benachrichtigt, dass sie die Zuordnung von Ressourcen ausgehend von ihrem zugewiesenen Plan erreicht haben. Davon ausgehend können die Benutzer einen Add-On-Plan auswählen, der die zusätzlichen Ressourcen bereitstellt.

> [!NOTE]
> Wenn Sie keinen Add-On-Plan verwenden möchten, um ein Kontingent zu erweitern, können Sie [die ursprüngliche Konfiguration des Kontingents bearbeiten](azure-stack-quota-types.md#edit-a-quota).

Wenn Sie einen Add-On-Plan einem vorhandenen Angebotsabonnement hinzufügen, kann es bis zu einer Stunde dauern, bis die zusätzlichen Ressourcen angezeigt werden.

Add-On-Pläne werden durch Ändern eines vorhandenen Angebots erstellt.

## <a name="create-an-add-on-plan-1902-and-later"></a>Erstellen eines Add-On-Plans (ab 1902)

1. Melden Sie sich beim Azure Stack-Administratorportal als Cloudadministrator an.
2. Führen Sie die gleichen Schritte wie beim [Erstellen eines neuen Basisplans](azure-stack-create-plan.md) aus, um einen neuen Plan mit Diensten zu erstellen, die zuvor noch nicht angeboten wurden.
3. Klicken Sie im Administratorportal auf **Angebote**, und wählen Sie anschließend das Angebot aus, das mit einem Add-On-Plan aktualisiert werden soll.

   ![Add-On-Plan erstellen](media/create-add-on-plan/add-on1.png)

4. Wählen Sie am Ende der Angebotseigenschaften die Option **Add-On-Pläne** aus. Klicken Sie auf **Hinzufügen**.

    ![Add-On-Plan erstellen](media/create-add-on-plan/add-on2.png)

5. Wählen Sie den hinzuzufügenden Plan aus. Der Plan in diesem Beispiel heißt **20-storageaccounts**. Wenn Sie den Plan ausgewählt haben, klicken Sie auf **Auswählen**, um den Plan zum Angebot hinzuzufügen. Sie sollten eine Benachrichtigung erhalten haben, dass der Plan dem Angebot erfolgreich hinzugefügt wurde.

    ![Add-On-Plan erstellen](media/create-add-on-plan/add-on3.png)

6. Sehen Sie sich die Liste mit den Add-On-Plänen an, die im Angebot enthalten sind, um sicherzustellen, dass der neue Add-On-Plan aufgeführt ist.

    [![Erstellen eines Add-On-Plans](media/create-add-on-plan/add-on4.png "Erstellen eines Add-On-Plans")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Erstellen eines Add-On-Plans (bis 1901)

1. Melden Sie sich beim Azure Stack-Administratorportal als Cloudadministrator an.
2. Führen Sie die gleichen Schritte wie beim [Erstellen eines neuen Basisplans](azure-stack-create-plan.md) aus, um einen neuen Plan mit Diensten zu erstellen, die zuvor noch nicht angeboten wurden. In diesem Beispiel werden Key Vault-Dienste (**Microsoft.KeyVault**) in den neuen Plan einbezogen.
3. Klicken Sie im Administratorportal auf **Angebote**, und wählen Sie anschließend das Angebot aus, das mit einem Add-On-Plan aktualisiert werden soll.

   ![Add-On-Plan erstellen](media/create-add-on-plan/1.PNG)

4. Scrollen Sie ans Ende der Angebotseigenschaften, und wählen Sie die Option **Add-On-Pläne**. Klicken Sie auf **Hinzufügen**.

    ![Add-On-Plan erstellen](media/create-add-on-plan/2.PNG)

5. Wählen Sie den hinzuzufügenden Plan aus. Der Plan in diesem Beispiel heißt **Key vault plan** (Key Vault-Plan). Wenn Sie den Plan ausgewählt haben, klicken Sie auf **Auswählen**, um den Plan zum Angebot hinzuzufügen. Sie sollten eine Benachrichtigung erhalten haben, dass der Plan dem Angebot erfolgreich hinzugefügt wurde.

    ![Add-On-Plan erstellen](media/create-add-on-plan/3.PNG)

6. Sehen Sie sich die Liste mit den Add-On-Plänen an, die im Angebot enthalten sind, um sicherzustellen, dass der neue Add-On-Plan aufgeführt ist.

    ![Add-On-Plan erstellen](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Angeboten](azure-stack-create-offer.md)