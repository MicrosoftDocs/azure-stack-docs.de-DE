---
title: Erstellen von Add-On-Plänen
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Add-On-Pläne in Azure Stack Hub erstellen.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2fd22acfe6f47d3a12502d5528e1f3fd96294460
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696480"
---
# <a name="create-add-on-plans-in-azure-stack-hub"></a>Erstellen von Add-On-Plänen in Azure Stack Hub

Als Azure Stack Hub-Operator erstellen Sie Add-On-Pläne, um einen [Basisplan](azure-stack-create-plan.md) zu ändern, wenn Sie zusätzliche Dienste anbieten oder die *Computer*-, *Speicher*- oder *Netzwerk*kontingente das ursprüngliche Angebot des Basisplans überschreitend ändern möchten. Add-On-Pläne verändern den Basisplan und sind optionale Erweiterungen, die Benutzer in ihrem Abonnement aktivieren können.

Manchmal besteht die optimale Lösung darin, alles in einem einzelnen Plan zu kombinieren. In anderen Fällen bietet es sich an, einen Basisplan zu verwenden und zusätzliche Dienste mithilfe von Add-On-Plänen anzubieten. Beispielsweise können Sie die Entscheidung treffen, IaaS-Dienste im Rahmen eines Basisplans anzubieten und alle PaaS-Dienste als Add-On-Pläne zu behandeln.

Add-On-Pläne können auch bei der Überwachung der Ressourcennutzung hilfreich sein. Hierfür können Sie mit einem Basisplan beginnen, der relativ kleine Kontingente enthält (je nach den erforderlichen Diensten). Wenn die Benutzer die Kapazitätsgrenze erreichen, werden sie darüber benachrichtigt, dass sie die Zuordnung von Ressourcen ausgehend von ihrem zugewiesenen Plan erreicht haben. Davon ausgehend können die Benutzer einen Add-On-Plan auswählen, der die zusätzlichen Ressourcen bereitstellt.

> [!NOTE]
> Wenn Sie keinen Add-On-Plan verwenden möchten, um ein Kontingent zu erweitern, können Sie [die ursprüngliche Konfiguration des Kontingents bearbeiten](azure-stack-quota-types.md#edit-a-quota).

Add-On-Pläne werden [auf die gleiche Weise erstellt](azure-stack-create-plan.md) wie ein Basisplan. Die Art des Plans wird bestimmt, wenn der Plan einem Angebot hinzugefügt wird. Der Plan wird entweder als Basisplan oder als Add-On-Plan festgelegt. Wenn Sie einen Add-On-Plan einem vorhandenen Angebot hinzufügen, kann es bis zu einer Stunde dauern, bis die zusätzlichen Ressourcen angezeigt werden.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Erstellen eines Add-On-Plans (ab 1902)

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Cloudadministrator an.
2. Führen Sie die gleichen Schritte wie beim [Erstellen eines neuen Basisplans](azure-stack-create-plan.md) aus, um einen neuen Plan mit Diensten zu erstellen, die zuvor noch nicht angeboten wurden.
3. Klicken Sie im Administratorportal auf **Angebote**, und wählen Sie anschließend das Angebot aus, das mit einem Add-On-Plan aktualisiert werden soll.

   ![Auswählen der Option zum Aktualisieren mit dem Add-On-Plan im Azure Stack-Administratorportal](media/create-add-on-plan/add-on1.png)

4. Wählen Sie am Ende der Angebotseigenschaften die Option **Add-On-Pläne** aus. Wählen Sie **Hinzufügen**.

    ![Auswählen von Add-On-Plänen im Azure Stack-Administratorportal](media/create-add-on-plan/add-on2.png)

5. Wählen Sie den hinzuzufügenden Plan aus. Der Plan in diesem Beispiel heißt **20-storageaccounts**. Wenn Sie den Plan ausgewählt haben, klicken Sie auf **Auswählen**, um den Plan zum Angebot hinzuzufügen. Sie sollten eine Benachrichtigung erhalten haben, dass der Plan dem Angebot erfolgreich hinzugefügt wurde.

    ![Auswählen des Add-On-Plans im Azure Stack-Administratorportal, der hinzugefügt werden soll](media/create-add-on-plan/add-on3.png)

6. Sehen Sie sich die Liste mit den Add-On-Plänen an, die im Angebot enthalten sind, um sicherzustellen, dass der neue Add-On-Plan aufgeführt ist.

    ![[Überprüfen der Liste der Add-On-Pläne im Azure Stack-Administratorportal](media/create-add-on-plan/add-on4.png „Erstellen eines Add-On-Plans“)](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Erstellen eines Add-On-Plans (bis 1901)

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Cloudadministrator an.
2. Führen Sie die gleichen Schritte wie beim [Erstellen eines neuen Basisplans](azure-stack-create-plan.md) aus, um einen neuen Plan mit Diensten zu erstellen, die zuvor noch nicht angeboten wurden. In diesem Beispiel werden Key Vault-Dienste (**Microsoft.KeyVault**) in den neuen Plan einbezogen.
3. Klicken Sie im Administratorportal auf **Angebote**, und wählen Sie anschließend das Angebot aus, das mit einem Add-On-Plan aktualisiert werden soll.

   ![Auswählen der Option zum Aktualisieren mit dem Add-On-Plan im Azure Stack-Administratorportal](media/create-add-on-plan/1.PNG)

4. Scrollen Sie ans Ende der Angebotseigenschaften, und wählen Sie die Option **Add-On-Pläne**. Wählen Sie **Hinzufügen**.

    ![Auswählen von Add-On-Plänen im Azure Stack-Administratorportal](media/create-add-on-plan/2.PNG)

5. Wählen Sie den hinzuzufügenden Plan aus. Der Plan in diesem Beispiel heißt **Key vault plan** (Key Vault-Plan). Wenn Sie den Plan ausgewählt haben, klicken Sie auf **Auswählen**, um den Plan zum Angebot hinzuzufügen. Sie sollten eine Benachrichtigung erhalten haben, dass der Plan dem Angebot erfolgreich hinzugefügt wurde.

    ![Auswählen des Add-On-Plans im Azure Stack-Administratorportal, der hinzugefügt werden soll](media/create-add-on-plan/3.PNG)

6. Sehen Sie sich die Liste mit den Add-On-Plänen an, die im Angebot enthalten sind, um sicherzustellen, dass der neue Add-On-Plan aufgeführt ist.

    ![Überprüfen der Liste der Add-On-Pläne im Azure Stack-Administratorportal](media/create-add-on-plan/4.PNG)

::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Angeboten](azure-stack-create-offer.md)
