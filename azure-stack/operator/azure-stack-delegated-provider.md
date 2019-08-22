---
title: Delegieren von Angeboten in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie es anderen Personen ermöglichen, in Ihrem Auftrag Angebote zu erstellen und Benutzer zu registrieren.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: b33c96ad10d5e4269f96741a5d4aba10d782896c
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959515"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegieren von Angeboten in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Als Azure Stack-Bediener kann es sein, dass Sie andere Personen in die Lage versetzen möchten, Benutzer zu registrieren und Abonnements zu erstellen. Als Dienstanbieter z.B. müssen Sie Ihren Vertriebspartnern ermöglichen können, in Ihrem Auftrag Kunden zu registrieren und diese zu verwalten. Wenn Sie einer zentralen IT-Gruppe in einem Unternehmen angehören, möchten Sie vielleicht die Benutzerregistrierung an einen anderen IT-Mitarbeiter delegieren.

Durch die Delegierung ist es einfacher, mehr Benutzer zu erreichen und zu verwalten, als Sie selbst handhaben können, wie in der folgenden Abbildung dargestellt:

![Delegierungsebenen](media/azure-stack-delegated-provider/image1.png)

Der delegierte Anbieter verwaltet ein Angebot (ein sogenanntes *delegiertes Angebot*), und Endkunden erhalten Abonnements im Rahmen dieses Angebots ohne Beteiligung des Systemadministrators.

## <a name="delegation-roles"></a>Delegierungsrollen

Die folgenden Rollen sind Teil der Delegierung:

* Der *Azure Stack-Bediener* verwaltet die Azure Stack-Infrastruktur und erstellt eine Angebotsvorlage. Der Bediener delegiert die Aufgabe, Benutzern Angebote zu unterbreiten, an seinen Mandanten.

* Die delegierten Azure Stack-Betreiber sind Benutzer mit den Rechten *Besitzer* oder *Mitwirkender* in den Abonnements namens *delegierte Anbieter*. Sie können zu anderen Organisationen gehören, beispielsweise zu anderen Azure Active Directory-Mandanten (Azure AD).

* *Benutzer* registrieren sich für die Angebote und verwenden sie, um ihre Workloads zu verwalten, virtuelle Computer zu erstellen, Daten zu speichern usw.

## <a name="delegation-steps"></a>Delegierungsschritte

Das Einrichten einer Delegierung umfasst zwei grundlegende Schritte:

1. **Erstellen eines Abonnements für einen delegierten Anbieter:** Erstellen Sie ein Abonnement für einen Benutzer bei einem Angebot, das nur den Abonnementdienst enthält. Benutzer, die dieses Angebot abonnieren, können die delegierten Angebote dann auf andere Benutzer erweitern, indem Sie sie für diese Angebote registrieren.

2. **Delegieren eines Angebots an den delegierten Anbieter:** Dieses Angebot ermöglicht dem delegierten Anbieter das Erstellen von Abonnements oder das Erweitern des Angebots auf seine Benutzer. Der delegierte Anbieter kann nun das Angebot anderen Benutzern anbieten.

Die nachstehende Abbildung zeigt die Schritte zum Einrichten der Delegierung:

![Erstellen des delegierten Anbieters und seine Aktivierung zum Registrieren von Benutzern](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>Anforderungen an delegierte Anbieter

Um als delegierter Anbieter zu fungieren, stellt ein Benutzer eine Beziehung zum Hauptanbieter her, indem er ein Abonnement erstellt. Der delegierte Anbieter wird durch das Abonnement als der Anbieter identifiziert, der das Recht besitzt, im Auftrag des Hauptanbieters delegierte Angebote zu präsentieren.

Nachdem diese Beziehung hergestellt wurde, kann der Azure Stack-Bediener ein Angebot an den delegierten Anbieter delegieren. Der delegierte Anbieter kann jetzt das Angebot annehmen, es umbenennen (nicht jedoch den Inhalt ändern) und es seinen Kunden unterbreiten.

## <a name="delegation-walkthrough"></a>Exemplarische Vorgehensweise einer Delegierung

Die folgenden Abschnitte enthalten eine exemplarische Vorgehensweise für das Einrichten eines delegierten Anbieters, das Delegieren eines Angebots und das Überprüfen, ob sich Benutzer für das delegierte Angebot registrieren können.

### <a name="set-up-roles"></a>Einrichten von Rollen

Für diese exemplarische Vorgehensweise benötigen Sie zusätzlich zu Ihrem Azure Stack-Bedienerkonto zwei Azure AD-Konten. Wenn Sie nicht über diese beiden Konten verfügen, müssen Sie sie erstellen. Die Konten können einem beliebigen Azure AD-Benutzer gehören und werden als delegierter Anbieter und Benutzer bezeichnet.

| **Rolle** | **Organisatorische Rechte** |
| --- | --- |
| Delegierter Anbieter |Benutzer |
| Benutzer |Benutzer |

 > [!NOTE]
 > Im Falle eines CSP-Handelspartners müssen sich diese Benutzer zum Erstellen des delegierten Anbieters im Mandantenverzeichnis (Benutzer-AAD) befinden. Der Azure Stack-Bediener muss [zuerst ein Onboarding](azure-stack-enable-multitenancy.md) dieses Mandanten-AAD durchführen und dann die Nutzung und Abrechnung wie [hier](azure-stack-csp-howto-register-tenants.md) beschrieben konfigurieren.

### <a name="identify-the-delegated-provider"></a>Identifizieren des delegierten Anbieters

1. Melden Sie sich beim Administratorportal als Azure Stack-Bediener an.

1. So erstellen Sie ein Angebot, mit dem ein Benutzer ein delegierter Anbieter werden kann

   a.  [Erstellen Sie einen Plan](azure-stack-create-plan.md).
       Dieser Plan sollte nur den Abonnementdienst enthalten. In diesem Artikel wird ein Plan namens **PlanForDelegation** als Beispiel verwendet.

   b.  [Erstellen Sie ein Angebot](azure-stack-create-offer.md), das auf diesem Plan basiert. In diesem Artikel wird ein Angebot mit dem Namen **OfferToDP** als Beispiel verwendet.

   c.  Fügen Sie den delegierten Anbieter diesem Angebot als Abonnenten hinzu, indem Sie **Abonnements**, **Hinzufügen** und dann **Neues Mandantenabonnement** auswählen.

   ![Hinzufügen des delegierten Anbieters als Abonnent](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Wie bei allen Azure Stack-Angeboten haben Sie die Option, das Angebot öffentlich zu machen und Benutzern zu ermöglichen, sich dafür zu registrieren. Sie können auch ein privates Angebot einrichten und es dem Azure Stack-Bediener überlassen, die Registrierung zu verwalten. Delegierte Anbieter sind in der Regel eine kleine Gruppe. Sie sollten kontrollieren können, wer in diese Gruppe aufgenommen wird. Ein privates Angebot ist also in den meisten Fällen sinnvoll.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Erstellen des delegierten Angebots durch den Azure Stack-Bediener

Der nächste Schritt besteht darin, den Plan und das Angebot zu erstellen, den/das Sie delegieren möchten und den/das Ihre Benutzer verwenden werden. Das Angebot sollte genau so zu definiert werden, wie es den Benutzern angezeigt werden soll, da der delegierte Anbieter die darin enthaltenen Pläne und Kontingente nicht ändern kann.

1. Melden Sie sich als Azure Stack-Bediener an, und [erstellen Sie einen Plan](azure-stack-create-plan.md) sowie ein auf dem Plan basierendes [Angebot](azure-stack-create-offer.md). In diesem Artikel wird ein Angebot mit dem Namen **DelegatedOffer** als Beispiel verwendet.

   > [!NOTE]
   > Dieses Angebot muss nicht öffentlich sein, Sie können es bei Bedarf jedoch öffentlich machen. In den meisten Fällen empfiehlt sich jedoch, nur delegierte Anbieter auf das Angebot zugreifen zu lassen. Sobald Sie, wie in den folgenden Schritten beschrieben, ein privates Angebot delegieren, kann der delegierte Anbieter darauf zugreifen.

2. Delegieren Sie das Angebot. Wechseln Sie zu **DelegatedOffer**. Wählen Sie unter **Einstellungen** die Option **Delegierte Anbieter** und dann **Hinzufügen** aus.

3. Wählen Sie in der Dropdownliste das Abonnement für den delegierten Anbieter aus, und wählen Sie dann **Delegieren** aus.

   ![Hinzufügen eines delegierten Anbieters](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Anpassen des Angebots durch den delegierten Anbieter

Melden Sie sich als delegierter Anbieter beim Benutzerportal an, und verwenden Sie das delegierte Angebot dann als Vorlage, um ein neues Angebot zu erstellen.

1. Wählen Sie **+ Ressource erstellen**, **Mandantenangebote + Pläne** und dann **Angebot** aus.

    ![Erstellen eines neuen Angebots](media/azure-stack-delegated-provider/image5.png)

2. Weisen Sie dem Angebot einen Namen zu. In diesem Beispiel wird **ResellerOffer** verwendet. Wählen Sie das delegierte Angebot aus, auf dem dieses Angebot basieren soll, und klicken Sie auf **Erstellen**.

   ![Zuweisen eines Namens](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Es ist wichtig, zu verstehen, dass delegierte Anbieter nur Angebote auswählen können, die an sie delegiert wurden. Sie können diese Angebote nicht ändern. Nur ein Azure Stack-Betreiber kann diese Angebote ändern, z.B. durch Ändern der Pläne und Kontingente. Ein delegierter Anbieter erstellt kein Angebot aufgrund von Basis- und Add-On-Plänen.

3. Der delegierte Anbieter kann diese Angebote über die URL zu seinem eigenen Portal veröffentlichen. Machen Sie das Angebot durch Auswahl von **Durchsuchen** und **Angebote** öffentlich. Wählen Sie das Angebot aus, und wählen Sie dann **Status ändern**.

4. Die öffentlichen delegierten Angebote werden jetzt nur über das delegierte Portal angezeigt. So suchen und ändern Sie diese URL:

    a.  Wählen Sie **Durchsuchen**, **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **ALLGEMEIN** die Option **Abonnements** aus. Wählen Sie das Abonnement des delegierten Anbieters, z.B. **DPSubscription**, und dann **Eigenschaften** aus.

    b.  Kopieren Sie die Portal-URL an einen anderen Ort, beispielsweise in Text-Editor.

    ![Auswählen des Abonnements des delegierten Anbieters](media/azure-stack-delegated-provider/dpportaluri.png)  

   Sie haben nun das Erstellen eines delegierten Angebots als delegierter Anbieter abgeschlossen. Melden Sie sich als delegierter Anbieter ab, und schließen Sie das Browserfenster.

### <a name="sign-up-for-the-offer"></a>Anmelden für das Angebot

1. Wechseln Sie in einem neuen Browserfenster zu der URL des Portals mit delegierten Angeboten, die Sie im vorherigen Schritt gespeichert haben. Melden Sie sich als Benutzer beim Portal an.

   >[!NOTE]
   >Die delegierten Angebote sind nur dann sichtbar, wenn Sie das delegierte Portal verwenden.

1. Wählen Sie auf dem Dashboard **Abonnement erwerben**. Sie sehen, dass dem Benutzer nur die delegierten Angebote angezeigt werden, die vom delegierten Anbieter erstellt wurden.

   ![Anzeigen und Auswählen von Angeboten](media/azure-stack-delegated-provider/image8.png)

Das Delegieren eines Angebots ist abgeschlossen. Ein Benutzer kann sich jetzt für dieses Angebot registrieren, indem er ein Abonnement dafür bezieht.

## <a name="move-subscriptions-between-delegated-providers"></a>Verschieben von Abonnements zwischen delegierten Anbietern

Abonnements können bei Bedarf zwischen neuen oder vorhandenen Abonnements delegierter Anbieter verschoben werden, die demselben Verzeichnismandanten angehören. Dazu wird das PowerShell-Cmdlet [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin) verwendet.

Diese Möglichkeit ist in folgenden Situationen nützlich:

* Sie integrieren ein neues Teammitglied, das die Rolle des delegierten Anbieters übernimmt, und möchten diesem Teammitglied Benutzerabonnements zuweisen, die zuvor im Standardabonnement des Anbieters erstellt wurden.
* Es sind mehrere Abonnements von delegierten Anbietern im gleichen Verzeichnismandanten (Azure Active Directory) vorhanden, und Sie müssen Benutzerabonnements zwischen diesen verschieben. Ein solches Szenario kann vorliegen, wenn ein Teammitglied zu einem anderen Team wechselt und sein Abonnement dem neuen Team zugewiesen werden muss.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer VM](../user/azure-stack-create-vm-template.md)
