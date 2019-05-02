---
title: Erstellen von Plänen in Azure Stack | Microsoft-Dokumentation
description: Erstellen Sie als Cloudadministrator einen Plan, mit dem Abonnenten virtuelle Computer bereitstellen können.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 03/07/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 85c6fd58108653ba4876d8aa7802d5674ae1336c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311238"
---
# <a name="create-a-plan-in-azure-stack"></a>Erstellen von Plänen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

[Pläne](azure-stack-overview.md) sind Gruppen mit mindestens einem Dienst und den dazugehörigen Kontingenten. Als Anbieter können Sie Pläne erstellen und Ihren Benutzern anbieten. Benutzer abonnieren daraufhin Ihre Angebote, um die darin enthaltenen Pläne, Dienste und Kontingente zu verwenden. In diesem Beispiel wird veranschaulicht, wie Sie einen Plan erstellen, der die Compute-, Netzwerk- und Speicherressourcenanbieter enthält. Mit diesem Plan erhalten Abonnenten die Möglichkeit, virtuelle Computer bereitzustellen.

## <a name="create-a-plan-1902-and-later"></a>Erstellen von Plänen (ab 1902)

1. Melden Sie sich beim [Azure Stack-Administratorportal](https://adminportal.local.azurestack.external) an.

2. Klicken Sie zum Erstellen von Plänen und Angeboten, die Benutzer abonnieren können, auf **+ Ressource erstellen** > **Angebote + Pläne** > **Plan**.
  
   ![Auswählen eines Plans](media/azure-stack-create-plan/select-plan.png)

3. Auf den Registerkarten der Benutzeroberfläche können Sie den Namen des Plan angeben, Dienste hinzufügen und Kontingente für die einzelnen ausgewählten Dienste definieren. Außerdem können Sie vor der tatsächlichen Erstellung des Angebots noch einmal die Angebotsdetails überprüfen.

   Geben Sie im Fenster **Neuer Plan** auf der Registerkarte **Grundlagen** Werte für **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der Name des Plans, der Bedienern angezeigt wird. Im Administratorportal sind Plandetails nur für Bediener sichtbar.

   ![Angeben von Details](media/azure-stack-create-plan/plan-name.png)

4. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, die als Container für den Plan dient.

   ![Angeben der Ressourcengruppe](media/azure-stack-create-plan/resource-group.png)

5. Wählen Sie die Registerkarte **Dienste** aus, und aktivieren Sie die Kontrollkästchen für **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage**.
  
   ![Auswählen von Diensten](media/azure-stack-create-plan/services.png)

6. Wählen Sie die Registerkarte **Kontingente** aus. Wählen Sie neben **Microsoft.Storage** entweder das Standardkontingent aus dem Dropdownfeld aus, oder wählen Sie **Neu erstellen** aus, um ein angepasstes Kontingent zu erstellen.
  
   ![Kontingente](media/azure-stack-create-plan/quotas.png)

7. Falls Sie ein neues Kontingent erstellen möchten, geben Sie unter **Name** einen Namen für das Kontingent ein, und geben Sie anschließend die Kontingentwerte an. Wählen Sie **OK** aus, um das Kontingent zu erstellen.

   ![Neues Kontingent](media/azure-stack-create-plan/new-quota.png)

8. Wiederholen Sie die Schritte 6 und 7, um Kontingente für **Microsoft.Network** und **Microsoft.Compute** zu erstellen und zuzuweisen. Wenn alle drei Dienste mit Kontingenten versehen wurden, sehen sie wie im folgenden Beispiel aus.

   ![Durchführen der Kontingentzuweisungen](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Wählen Sie **Überprüfen + erstellen** aus, um den Plan zu überprüfen. Vergewissern Sie sich, dass alle Werte und Kontingente korrekt sind. Beachten Sie die Erweiterungspfeile auf der linken Seite des jeweiligen Dienst/Kontingent-Paars. Dank eines neuen Features können Sie nacheinander die Kontingente in den gewählten Plänen erweitern, um die Details der einzelnen Kontingente in einem Plan anzuzeigen, und zurückwechseln, um ggf. Änderungen vorzunehmen.

   ![Erstellen des Plans](media/azure-stack-create-plan/create.png)

10. Wenn Sie so weit sind, wählen Sie **Erstellen** aus, um den Plan zu erstellen.

11. Wählen Sie zum Anzeigen des neuen Plans die Option **Pläne** aus, suchen Sie nach dem Plan, und wählen Sie seinen Namen aus. Wenn Ihre Liste mit den Ressourcen sehr lang ist, können Sie die Option **Suche** verwenden, um anhand des Namens nach Ihrem Plan zu suchen.

## <a name="create-a-plan-1901-and-earlier"></a>Erstellen von Plänen (bis 1901)

1. Melden Sie sich beim [Azure Stack-Administratorportal](https://adminportal.local.azurestack.external) an.

2. Klicken Sie zum Erstellen von Plänen und Angeboten, die Benutzer abonnieren können, auf **+ Ressource erstellen** > **Angebote + Pläne** > **Plan**.
  
   ![Auswählen eines Plans](media/azure-stack-create-plan/select-plan1901.png)

3. Geben Sie unter **Neuer Plan** einen Wert für **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der Name des Plans, der Benutzern angezeigt wird. Der Ressourcenname ist nur für den Administrator sichtbar und wird von Administratoren genutzt, um den Plan als Azure Resource Manager-Ressource zu verwenden.

   ![Angeben von Details](media/azure-stack-create-plan/plan-name1901.png)

4. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, die als Container für den Plan dient.

   ![Angeben der Ressourcengruppe](media/azure-stack-create-plan/resource-group1901.png)

5. Wählen Sie **Dienste**, und aktivieren Sie anschließend jeweils das Kontrollkästchen für **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage**. Wählen Sie als Nächstes die Option **Auswählen**, um die Konfiguration zu speichern. Die Kontrollkästchen werden angezeigt, wenn Sie mit der Maus auf eine Option zeigen.
  
   ![Auswählen von Diensten](media/azure-stack-create-plan/services1901.png)

6. Klicken Sie auf **Kontingente** > **Microsoft.Storage (lokal)**, und wählen Sie entweder das Standardkontingent aus, oder klicken Sie auf **Neues Kontingent erstellen**, um ein angepasstes Kontingent zu erstellen.
  
   ![Kontingente](media/azure-stack-create-plan/quotas1901.png)

7. Geben Sie bei der Erstellung eines neuen Kontingents einen **Namen** für das Kontingent ein, geben Sie die Kontingentwerte an, und wählen Sie **OK**. Das Dialogfeld **Kontingent erstellen** wird geschlossen.

   ![Neues Kontingent](media/azure-stack-create-plan/new-quota1901.png)

   Anschließend wählen Sie das von Ihnen neu erstellte Kontingent aus. Wenn Sie das Kontingent auswählen, wird es zugewiesen, und das Auswahldialogfeld wird geschlossen.
  
   ![Zuweisen des Kontingents](media/azure-stack-create-plan/assign-quota1901.png)

8. Wiederholen Sie die Schritte 6 und 7, um Kontingente für **Microsoft.Network (local)** und **Microsoft.Compute (local)** zu erstellen und zuzuweisen. Wenn alle drei Dienste mit Kontingenten versehen wurden, sehen sie wie im folgenden Beispiel aus.

   ![Durchführen der Kontingentzuweisungen](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Klicken Sie unter **Kontingente** auf **OK** und dann unter **Neuer Plan** auf **Erstellen**, um den Plan zu erstellen.

    ![Erstellen des Plans](media/azure-stack-create-plan/create1901.png)

10. Wählen Sie zum Anzeigen des neuen Plans die Option **Alle Ressourcen**, suchen Sie nach dem Plan, und wählen Sie seinen Namen aus. Wenn Ihre Liste mit den Ressourcen sehr lang ist, können Sie die Option **Suche** verwenden, um anhand des Namens nach Ihrem Plan zu suchen.

    ![Überprüfen des Plans](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Angeboten](azure-stack-create-offer.md)

<!-- Update_Description: wording update -->