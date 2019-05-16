---
title: Abonnieren von Angeboten in Azure Stack | Microsoft-Dokumentation
description: Erstellen von Abonnements für Angebote in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: b0dba609acd7b12cdb95c1ec3e40ad906674244d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984773"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Erstellen von Abonnements für Angebote in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Nachdem Sie ein [Angebot erstellt](azure-stack-create-offer.md) haben, benötigen Benutzer ein Abonnement für dieses Angebot, damit sie es nutzen können. Ein Benutzer kann auf zwei Arten ein Abonnement für ein Angebot erhalten:

- Als Cloudbediener können Sie im Verwaltungsportal ein Abonnement für Benutzer erstellen. Von Ihnen erstellte Abonnements können für öffentliche und private Angebote verwendet werden.
- Als Mandantenbenutzer können Sie über das Benutzerportal ein öffentliches Angebot abonnieren.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Erstellen eines Abonnements als Cloudbediener

Cloudbediener können im Verwaltungsportal ein Angebotsabonnement für Benutzer erstellen. Sie können Abonnements für Mitglieder Ihres eigenen Verzeichnismandanten erstellen. Wenn die [Mehrinstanzenfähigkeit](azure-stack-enable-multitenancy.md) aktiviert ist, können Sie auch Abonnements für Benutzer in zusätzlichen Verzeichnismandanten erstellen.

Wenn Ihre Mandanten keine eigenen Abonnements erstellen sollen, legen Sie Ihre Angebote als privat fest, und erstellen Sie anschließend Abonnements für Ihre Mandanten. Dieser Ansatz kommt häufig beim Integrieren von Azure Stack in externe Abrechnungs- oder Dienstkatalogsysteme zum Einsatz.

Nachdem Sie ein Abonnement für einen Benutzer erstellt haben, kann er sich beim Benutzerportal anmelden. Dort sieht er, dass er über ein Abonnement für das Angebot verfügt.  

### <a name="to-create-a-subscription-for-a-user"></a>So erstellen Sie ein Abonnement für einen Benutzer

1. Navigieren Sie im Verwaltungsportal zu **Benutzerabonnements**.
2. Wählen Sie **Hinzufügen**. Geben Sie unter **Neues Benutzerabonnement** folgende Informationen ein:  

   - **Anzeigename**: Ein Anzeigename zum Identifizieren des Abonnements, der als *Name des Benutzerabonnements* angezeigt wird.
   - **Benutzer**: Geben Sie einen Benutzer aus einem verfügbaren Verzeichnismandanten für dieses Abonnement an. Der Benutzername wird als *Besitzer* angezeigt.  Das Format des Benutzernamens hängt von Ihrer Identitätslösung ab. Beispiel: 

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Verzeichnismandant**: Wählen Sie den Verzeichnismandanten aus, zu dem das Benutzerkonto gehört. Wenn Sie keine Mehrinstanzenfähigkeit aktiviert haben, ist nur der lokale Verzeichnismandant verfügbar.

3. Wählen Sie **Angebot** aus. Wählen Sie unter **Angebote** ein **Angebot** für dieses Abonnement aus. Da Sie das Abonnement für einen Benutzer erstellen, wählen Sie als Status der Barrierefreiheit die Option **Privat** aus.

4. Wählen Sie **Erstellen**, um das Abonnement zu erstellen. Das neue Abonnement wird unter **Benutzerabonnement** angezeigt. Wenn sich der Benutzer beim Benutzerportal anmeldet, kann er sich die Abonnementdetails ansehen.

### <a name="to-make-an-add-on-plan-available"></a>So stellen Sie einen Add-On-Plan zur Verfügung

Ein Cloudbediener kann einem zuvor erstellten Abonnement jederzeit einen Plan hinzufügen:

1. Klicken Sie im Verwaltungsportal auf **Alle Dienste** und anschließend unter der Kategorie **VERWALTUNGSRESSOURCEN** auf **Benutzerabonnements**. Wählen Sie das Abonnement aus, das Sie ändern möchten.

2. Klicken Sie auf **Add-Ons** und anschließend auf **+Hinzufügen**.  

3. Wählen Sie unter **Plan hinzufügen** den Plan aus, den Sie als Add-On verwenden möchten.

## <a name="create-a-subscription-as-a-user"></a>Erstellen eines Abonnements als Benutzer

Als Benutzer können Sie sich beim Benutzerportal anmelden, um öffentliche Angebote und Add-On-Pläne für Ihren Verzeichnismandanten (Organisation) zu suchen und zu abonnieren.

>[!NOTE]
>Wenn Ihre Azure Stack-Umgebung [Mehrinstanzenfähigkeit](azure-stack-enable-multitenancy.md) unterstützt, können Sie auch Angebote aus einem Remoteverzeichnismandanten abonnieren.

### <a name="to-subscribe-to-an-offer"></a>So abonnieren Sie ein Angebot

1. [Melden Sie sich](../asdk/asdk-connect.md) beim [Azure Stack-Benutzerportal](https://portal.local.azurestack.external) an, und wählen Sie **Abonnement erwerben** aus.

   ![Erwerben eines Abonnements](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Geben Sie unter **Abonnement erwerben** den Anzeigenamen des Abonnements in das Feld **Anzeigename** ein. Klicken Sie auf **Angebot**, und wählen Sie unter **Angebot wählen** ein Angebot aus. Wählen Sie **Erstellen**, um das Abonnement zu erstellen.

   ![Erstellen von Angeboten](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Aktualisieren Sie nach dem Abonnieren eines Angebots das Portal, um anzuzeigen, welche Dienste zum neuen Abonnement gehören.

4. Klicken Sie zum Anzeigen des erstellten Abonnements auf **Alle Dienste** und anschließend unter der Kategorie **ALLGEMEIN** auf **Abonnements**. Klicken Sie auf das Abonnement, um die Abonnementdetails anzuzeigen.  

### <a name="to-subscribe-to-an-add-on-plan"></a>So abonnieren Sie einen Add-On-Plan

Wenn ein Angebot einen Add-On-Plan enthält, können Sie diesen Plan jederzeit zu Ihrem Abonnement hinzufügen.  

1. Klicken Sie im Benutzerportal auf **Alle Dienste**. Klicken Sie als Nächstes unter der Kategorie **ALLGEMEIN** auf **Abonnements**, und wählen Sie dann das zu ändernde Abonnement aus. Sofern Add-On-Pläne verfügbar sind, ist **+Plan hinzufügen** aktiv, und es ist eine Kachel für **Add-On-Pläne** vorhanden. 

   Ist **+Plan hinzufügen** nicht aktiv, gibt es keine Add-On-Pläne für das mit diesem Abonnement verknüpfte Angebot.

1. Klicken Sie auf **+Plan hinzufügen** oder auf die Kachel **Add-On-Pläne**. Wählen Sie unter **Add-On-Pläne** den Plan aus, den Sie hinzufügen möchten.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)
