---
title: Erstellen von Plänen in Azure Stack Hub
description: Erfahren Sie, wie Sie in Azure Stack Hub einen Plan erstellen, mit dem Abonnenten virtuelle Computer bereitstellen können.
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 12de836d74382f26efd49ec8873aaa99a919ecd5
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91107089"
---
# <a name="create-a-plan-in-azure-stack-hub"></a>Erstellen von Plänen in Azure Stack Hub

[Azure Stack Hub-Pläne](azure-stack-overview.md) sind Gruppen mit mindestens einem Dienst und den zugehörigen Kontingenten. Als Anbieter können Sie Pläne erstellen und Ihren Benutzern anbieten. Benutzer abonnieren daraufhin Ihre Angebote, um die darin enthaltenen Pläne, Dienste und Kontingente zu verwenden. In diesem Beispiel wird veranschaulicht, wie Sie einen Plan erstellen, der die Compute-, Netzwerk- und Speicherressourcenanbieter enthält. Mit diesem Plan erhalten Abonnenten die Möglichkeit, virtuelle Computer bereitzustellen.

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>Erstellen von Plänen (ab 1902)

1. Melden Sie sich beim Azure Stack Hub-Administratorportal `https://adminportal.local.azurestack.external` an.

2. Klicken Sie zum Erstellen von Plänen und Angeboten, die Benutzer abonnieren können, auf **+ Ressource erstellen** > **Angebote + Pläne** > **Plan**.
  
   ![Screenshot, der das Auswählen eines Plans im Azure Stack Hub-Administratorportal zeigt.](media/azure-stack-create-plan/select-plan.png)

3. Auf den Registerkarten der Benutzeroberfläche können Sie den Namen des Plan angeben, Dienste hinzufügen und Kontingente für die einzelnen ausgewählten Dienste definieren. Ganz wichtig: Sie können vor der tatsächlichen Erstellung des Angebots noch einmal die Angebotsdetails überprüfen.

   Geben Sie im Fenster **Neuer Plan** auf der Registerkarte **Grundlagen** Werte für **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der Name des Plans, der Bedienern angezeigt wird. Im Administratorportal sind Plandetails nur für Bediener sichtbar.

   ![Der Screenshot zeigt, wie die Details für den neuen Plan in Azure Stack Hub festgelegt werden.](media/azure-stack-create-plan/plan-name.png)

4. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, die als Container für den Plan dient.

   ![Der Screenshot zeigt, wie die Ressourcengruppe für den neuen Plan in Azure Stack Hub festgelegt wird.](media/azure-stack-create-plan/resource-group.png)

5. Wählen Sie die Registerkarte **Dienste**, oder klicken Sie auf die Schaltfläche **Weiter: Dienste >** , und aktivieren Sie anschließend jeweils das Kontrollkästchen für **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage**.
  
   ![Der Screenshot zeigt, wie Dienste für einen neuen Plan in Azure Stack Hub ausgewählt werden.](media/azure-stack-create-plan/services.png)

6. Wählen Sie die Registerkarte **Kontingente**, oder klicken Sie auf die Schaltfläche **Weiter: Kontingente >** . Wählen Sie neben **Microsoft.Storage** entweder das Standardkontingent aus dem Dropdownfeld aus, oder wählen Sie **Neu erstellen** aus, um ein angepasstes Kontingent zu erstellen.
  
   ![Der Screenshot zeigt, wie Kontingente für einen neuen Plan in Azure Stack Hub angegeben werden.](media/azure-stack-create-plan/quotas.png)

7. Falls Sie ein neues Kontingent erstellen möchten, geben Sie unter **Name** einen Namen für das Kontingent ein, und geben Sie anschließend die Kontingentwerte an. Wählen Sie **OK** aus, um das Kontingent zu erstellen.

   ![Der Screenshot zeigt, wie neue Kontingente für einen neuen Plan in Azure Stack Hub erstellt werden.](media/azure-stack-create-plan/new-quota.png)

8. Wiederholen Sie die Schritte 6 und 7, um Kontingente für **Microsoft.Network** und **Microsoft.Compute** zu erstellen und zuzuweisen. Wenn alle drei Dienste mit Kontingenten versehen wurden, sehen sie wie im folgenden Beispiel aus.

   ![Der Screenshot zeigt, wie Kontingentzuweisungen für einen neuen Plan in Azure Stack Hub abgeschlossen werden.](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Wählen Sie **Überprüfen + erstellen** aus, um den Plan zu überprüfen. Überprüfen Sie alle Werte und Kontingente, um sich zu vergewissern, dass alle richtig sind. Auf der Oberfläche können Sie die Kontingente in den gewählten Plänen erweitern, um die Details jedes einzelnen Kontingents in einem Plan anzuzeigen. Sie können auch zurückkehren, um erforderliche Änderungen vorzunehmen.

   ![Der Screenshot zeigt, wie der Plan in Azure Stack Hub erstellt wird.](media/azure-stack-create-plan/create.png)

10. Wenn Sie bereit sind, wählen Sie **Erstellen** aus, um den Plan zu erstellen.

11. Klicken Sie zum Anzeigen des neuen Plans links auf **Alle Dienste**, und wählen Sie **Pläne**. Suchen Sie dann nach dem Plan, und wählen Sie seinen Namen aus. Wenn Ihre Liste mit den Ressourcen sehr lang ist, können Sie die Option **Suche** verwenden, um anhand des Namens nach Ihrem Plan zu suchen.
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>Erstellen von Plänen (bis 1901)

1. Melden Sie sich beim Azure Stack Hub-Administratorportal `https://adminportal.local.azurestack.external` an.

2. Wählen Sie zum Erstellen von Plänen und Angeboten, die Benutzer abonnieren können, die Optionen **+ Neu**, **Angebote + Pläne** und **Plan**.
  
   ![Auswählen eines Plans im Azure Stack Hub-Administratorportal](media/azure-stack-create-plan/select-plan1901.png)

3. Geben Sie unter **Neuer Plan** einen Wert für **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der Name des Plans, der Benutzern angezeigt wird. Der Ressourcenname ist nur für den Administrator sichtbar und wird von Administratoren genutzt, um den Plan als Azure Resource Manager-Ressource zu verwenden.

   ![Angeben der Details für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/plan-name1901.png)

4. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, die als Container für den Plan dient.

   ![Angeben der Ressourcengruppe für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/resource-group1901.png)

5. Wählen Sie **Dienste**, und aktivieren Sie anschließend jeweils das Kontrollkästchen für **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage**. Wählen Sie als Nächstes die Option **Auswählen**, um die Konfiguration zu speichern. Die Kontrollkästchen werden angezeigt, wenn Sie mit der Maus auf eine Option zeigen.
  
   ![Auswählen der Dienste für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/services1901.png)

6. Klicken Sie auf **Kontingente** > **Microsoft.Storage (lokal)** , und wählen Sie entweder das Standardkontingent aus, oder klicken Sie auf **Neues Kontingent erstellen**, um ein angepasstes Kontingent zu erstellen.
  
   ![Angeben der Kontingente für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/quotas1901.png)

7. Geben Sie bei der Erstellung eines neuen Kontingents einen **Namen** für das Kontingent ein, geben Sie die Kontingentwerte an, und wählen Sie **OK**. Das Dialogfeld **Kontingent erstellen** wird geschlossen.

   ![Erstellen eines neuen Kontingents für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/new-quota1901.png)

   Anschließend wählen Sie das von Ihnen neu erstellte Kontingent aus. Wenn Sie das Kontingent auswählen, wird es zugewiesen, und das Auswahldialogfeld wird geschlossen.
  
   ![Zuweisen des Kontingents für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/assign-quota1901.png)

8. Wiederholen Sie die Schritte 6 und 7, um Kontingente für **Microsoft.Network (local)** und **Microsoft.Compute (local)** zu erstellen und zuzuweisen. Wenn alle drei Dienste mit Kontingenten versehen wurden, sehen sie wie im folgenden Beispiel aus.

   ![Finalisieren der Kontingentzuweisungen für einen neuen Plan in Azure Stack Hub](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Klicken Sie unter **Kontingente** auf **OK** und dann unter **Neuer Plan** auf **Erstellen**, um den Plan zu erstellen.

    ![Erstellen des Plans in Azure Stack Hub](media/azure-stack-create-plan/create1901.png)

10. Wählen Sie zum Anzeigen des neuen Plans die Option **Alle Ressourcen**, suchen Sie nach dem Plan, und wählen Sie seinen Namen aus. Wenn Ihre Liste mit den Ressourcen sehr lang ist, können Sie die Option **Suche** verwenden, um anhand des Namens nach Ihrem Plan zu suchen.

    ![Überprüfen des neuen Plans in Azure Stack Hub](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Angeboten](azure-stack-create-offer.md)
