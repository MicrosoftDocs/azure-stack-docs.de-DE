---
title: Erstellen von Angeboten in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Cloudadministrator ein Angebot für Ihre Benutzer in Azure Stack erstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 1492c779144eac235e5c32e767e966b3a6cfb9fd
ms.sourcegitcommit: 8ddd70ba5ce05c591d3fa62597981859af107c06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70936141"
---
# <a name="create-an-offer-in-azure-stack"></a>Erstellen von Angeboten in Azure Stack

[Angebote](azure-stack-overview.md) sind Gruppen mit mindestens einem Plan, die Anbieter Benutzern zum Kauf oder in Form von Abonnements anbieten. In diesem Artikel wird beschrieben, wie Sie ein Angebot erstellen, das den [von Ihnen erstellten Plan](azure-stack-create-plan.md) enthält. Durch dieses Angebot erhalten Abonnenten die Möglichkeit, virtuelle Computer (VMs) einzurichten.

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>Erstellen von Angeboten (ab 1902)

1. Melden Sie sich beim [Azure Stack-Administratorportal](https://adminportal.local.azurestack.external) an, und wählen Sie **+ Ressource erstellen** > **Angebote + Pläne** > **Angebot** aus.

   ![Erstellen von Angeboten](media/azure-stack-create-offer/offers.png)

2. Auf den Registerkarten der Benutzeroberfläche können Sie den Angebotsnamen definieren und vorhandene Basispläne und Add-On-Pläne hinzufügen oder neue erstellen. Außerdem können Sie vor der tatsächlichen Erstellung des Angebots noch einmal die Angebotsdetails überprüfen.

   Geben Sie auf der Registerkarte **Grundlagen** einen Wert für **Anzeigename** und **Ressourcenname** ein, und wählen Sie anschließend unter **Ressourcenname** die Option **Neu erstellen** oder **Vorhandenes Element verwenden**. Der Anzeigename ist der verständliche Anzeigename des Angebots. Dieser Anzeigename ist die einzige Angebotsinformation, die Benutzern beim Abonnieren eines Angebots im Benutzerportal angezeigt wird. Verwenden Sie einen aussagekräftigen Namen, der Aufschluss darüber gibt, was das Angebot umfasst. Nur der Administrator kann den Ressourcennamen anzeigen. Es handelt sich um den Namen, mit dem Administratoren das Angebot als Azure-Ressourcen-Manager-Ressource bearbeiten. Auf dieser Registerkarte können Sie das Angebot veröffentlichen oder die Standardkonfiguration als privates Angebot beibehalten. Sie können den [Öffentlichkeitsstatus des Angebots auch später noch ändern](#change-the-state-of-an-offer).

   ![Neues Angebot](media/azure-stack-create-offer/new-offer.png)
  
3. Wählen Sie die Registerkarte **Basispläne**, oder klicken Sie auf die Schaltfläche **Weiter: Basispläne >** . Wählen Sie die Pläne aus, die Sie in das Angebot einschließen möchten.

   ![Auswählen des Plans](media/azure-stack-create-offer/select-plan.png)

4. An dieser Stelle können Sie optional einen Add-On-Plan erstellen, um den Basisplan zu ändern. Informationen zum Erstellen eines Add-On-Plans finden Sie im Artikel [Add-On-Pläne für Azure Stack](create-add-on-plan.md).

5. Wählen Sie die Registerkarte **Überprüfen + erstellen** aus. Überprüfen Sie die Angebotszusammenfassung, und vergewissern Sie sich, dass alle Werte korrekt sind. Auf der Oberfläche können Sie nacheinander die Kontingente in den gewählten Plänen erweitern, um die Details der einzelnen Kontingente in einem Plan anzuzeigen, und zurückwechseln, um ggf. Änderungen vorzunehmen.

6. Wählen Sie **Erstellen** aus, um das Angebot zu erstellen.

   ![Überprüfen und Erstellen](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Ändern des Zustands eines Angebots

Nach dem Erstellen des Angebots können Sie dessen Zustand ändern. Angebote müssen **öffentlich** gemacht werden, damit Benutzer beim Abonnieren die vollständige Ansicht erhalten. Angebote können Folgendes sein:

- **Öffentlich**: Für Benutzer sichtbar.
- **Privat**: Nur für Cloudadministratoren sichtbar. Diese Einstellung ist hilfreich beim Entwerfen des Plans oder Angebots oder wenn der Cloudadministrator [jedes Abonnement für Benutzer erstellen](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) möchte.
- **Außer Betrieb**: Für neue Abonnenten geschlossen. Der Cloudadministrator kann Angebote außer Betrieb nehmen, um zukünftige Abonnements zu verhindern, ohne aktuelle Abonnenten zu beeinträchtigen.

  > [!TIP]  
  > Änderungen am Angebot sind für den Benutzer nicht sofort sichtbar. Um die Änderungen zu sehen, müssen sich Benutzer möglicherweise ab- und erneut beim Benutzerportal anmelden, damit das neue Angebot angezeigt wird.

Der Zustand eines Angebots kann auf zwei Arten geändert werden:

1. Wählen Sie unter **Alle Ressourcen** den Namen des Angebots aus. Wählen Sie in der **Übersicht** für das Angebot die Option **Status ändern** aus. Wählen Sie den gewünschten Zustand aus (beispielsweise **Öffentlich**).

   ![Ändern des Status](media/azure-stack-create-offer/change-state.png)

2. Wählen Sie die Option **Angebotseinstellungen**. Wählen Sie den gewünschten Zustand (beispielsweise **Öffentlich**) und anschließend **Speichern** aus.

   ![Auswählen von „Status der Barrierefreiheit“](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Erstellen von Angeboten (bis 1901)

1. Melden Sie sich beim [Azure Stack-Administratorportal](https://adminportal.local.azurestack.external) an, und wählen Sie die Optionen **+ Ressource erstellen**, **Mandantenangebote + Pläne** und dann **Angebot**.

   ![Erstellen von Angeboten](media/azure-stack-create-offer/image01.png)
  
2. Geben Sie unter **Neues Angebot** einen Wert für **Anzeigename** und **Ressourcenname** ein, und klicken Sie anschließend unter **Ressourcenname** auf **Neu erstellen** oder **Vorhandenes Element verwenden**. Der Anzeigename ist der verständliche Anzeigename des Angebots. Dieser Anzeigename ist die einzige Information zum Angebot, die Benutzern beim Abonnieren eines Angebots angezeigt wird. Verwenden Sie einen aussagekräftigen Namen, der Aufschluss darüber gibt, was das Angebot umfasst. Nur der Administrator kann den Ressourcennamen anzeigen. Es handelt sich um den Namen, mit dem Administratoren das Angebot als Azure-Ressourcen-Manager-Ressource bearbeiten.

   ![Neues Angebot](media/azure-stack-create-offer/image01a.png)
  
3. Klicken Sie auf **Basispläne**, um den **Plan** zu öffnen. Wählen Sie die Pläne aus, die Sie in das Angebot einschließen möchten, und klicken Sie anschließend auf **Auswählen**. Klicken Sie zum Erstellen des Angebots auf **Erstellen**.

   ![Auswählen des Plans](media/azure-stack-create-offer/image02.png)
  
4. Nach dem Erstellen des Angebots können Sie dessen Zustand ändern. Angebote müssen **öffentlich** gemacht werden, damit Benutzer beim Abonnieren die vollständige Ansicht erhalten. Angebote können Folgendes sein:

   - **Öffentlich**: Für Benutzer sichtbar.
   - **Privat**: Nur für Cloudadministratoren sichtbar. Diese Einstellung ist hilfreich beim Entwerfen des Plans oder Angebots oder wenn der Cloudadministrator [jedes Abonnement für Benutzer erstellen](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) möchte.
   - **Außer Betrieb**: Für neue Abonnenten geschlossen. Der Cloudadministrator kann Angebote außer Betrieb nehmen, um zukünftige Abonnements zu verhindern, ohne aktuelle Abonnenten zu beeinträchtigen.

   > [!TIP]  
   > Änderungen am Angebot sind für den Benutzer nicht sofort sichtbar. Um die Änderungen zu sehen, müssen sich Benutzer möglicherweise ab- und erneut beim Benutzerportal anmelden, damit das neue Angebot angezeigt wird.

   Klicken Sie im Übersichtsfenster für das Angebot auf **Status der Barrierefreiheit**. Wählen Sie den gewünschten Status aus (beispielsweise **Öffentlich**), und klicken Sie anschließend auf **Speichern**.

     ![Ändern des Status](media/azure-stack-create-offer/change-stage-1807.png)

     Klicken Sie alternativ auf **Status ändern**, und wählen Sie dann einen Status aus.

    ![Auswählen von „Status der Barrierefreiheit“](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Sie können auch PowerShell verwenden, um Standardangebote, Pläne und Kontingente zu erstellen. Weitere Informationen finden Sie unter [Azure Stack-Modul 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).
::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Abonnements](azure-stack-subscribe-plan-provision-vm.md)
- [Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)
