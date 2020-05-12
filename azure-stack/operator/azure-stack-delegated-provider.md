---
title: Delegieren von Angeboten in Azure Stack Hub
description: Es wird beschrieben, wie Sie Aufgaben delegieren, z. B. das Erstellen von Angeboten und das Durchführen der Registrierung für Benutzer.
author: sethmanheim
ms.topic: article
ms.date: 05/01/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 4f32bc3e12689f75ad5c7e5b04e7fb36049271b3
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742517"
---
# <a name="delegate-offers-in-azure-stack-hub"></a>Delegieren von Angeboten in Azure Stack Hub

Azure Stack Hub-Operatoren müssen möglicherweise andere Personen in die Lage versetzen, Benutzer zu registrieren und Abonnements zu erstellen. Als Dienstanbieter z.B. müssen Sie Ihren Vertriebspartnern ermöglichen können, in Ihrem Auftrag Kunden zu registrieren und diese zu verwalten. Wenn Sie einer zentralen IT-Gruppe in einem Unternehmen angehören, möchten Sie vielleicht die Benutzerregistrierung an einen anderen IT-Mitarbeiter delegieren.

Durch die Delegierung ist es einfacher, mehr Benutzer zu erreichen und zu verwalten, als Sie selbst handhaben können, wie in der folgenden Abbildung dargestellt:

![Delegierungsebenen in Azure Stack Hub](media/azure-stack-delegated-provider/image1.png)

Der delegierte Anbieter verwaltet ein Angebot (ein so genanntes *delegiertes Angebot*), und Endkunden erhalten Abonnements im Rahmen dieses Angebots ohne Beteiligung des Systemadministrators.

## <a name="delegation-roles"></a>Delegierungsrollen

Die folgenden Rollen sind Teil der Delegierung:

* Der *Azure Stack Hub-Operator* verwaltet die Azure Stack Hub-Infrastruktur und erstellt eine Angebotsvorlage. Der Bediener delegiert die Aufgabe, Benutzern Angebote zu unterbreiten, an seinen Mandanten.

* Die delegierten Azure Stack Hub-Operatoren sind Benutzer mit den Rechten *Besitzer* oder *Mitwirkender* in den Abonnements und werden als *delegierte Anbieter* bezeichnet. Sie können zu anderen Organisationen gehören, beispielsweise zu anderen Azure Active Directory-Mandanten (Azure AD).

* *Benutzer* registrieren sich für die Angebote und verwenden sie, um ihre Workloads zu verwalten, virtuelle Computer zu erstellen, Daten zu speichern usw.

## <a name="delegation-steps"></a>Delegierungsschritte

Das Einrichten von Delegierung umfasst zwei Schritte:

1. **Erstellen eines Abonnements für einen delegierten Anbieter:** Erstellen Sie ein Abonnement für einen Benutzer bei einem Angebot, das nur den Abonnementdienst enthält. Benutzer, die dieses Angebot abonnieren, können die delegierten Angebote dann auf andere Benutzer erweitern, indem Sie sie für diese Angebote registrieren.

2. **Delegieren eines Angebots an den delegierten Anbieter:** Dieses Angebot ermöglicht dem delegierten Anbieter das Erstellen von Abonnements oder das Erweitern des Angebots auf seine Benutzer. Der delegierte Anbieter kann nun das Angebot annehmen und auf andere Benutzer erweitern.

Die nachstehende Abbildung zeigt die Schritte zum Einrichten der Delegierung:

![Schritte in Azure Stack Hub, mit denen delegierten Anbieter erstellt und ihnen die Registrierung von Benutzern ermöglicht wird](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>Anforderungen an delegierte Anbieter

Um als delegierter Anbieter zu fungieren, stellt ein Benutzer eine Beziehung zum Hauptanbieter her, indem er ein Abonnement erstellt. Der delegierte Anbieter wird durch das Abonnement als der Anbieter identifiziert, der das Recht besitzt, im Auftrag des Hauptanbieters delegierte Angebote zu präsentieren.

Nachdem diese Beziehung hergestellt wurde, kann der Azure Stack Hub-Operator ein Angebot an den delegierten Anbieter delegieren. Der delegierte Anbieter kann jetzt das Angebot annehmen, es umbenennen (nicht jedoch den Inhalt ändern) und es seinen Kunden unterbreiten.

## <a name="delegation-walkthrough"></a>Exemplarische Vorgehensweise einer Delegierung

Die folgenden Abschnitte beschreiben die Schritte zum Einrichten eines delegierten Anbieters, zum Delegieren eines Angebots und zum Überprüfen, ob Benutzer sich für das delegierte Angebot registrieren können.

### <a name="set-up-roles"></a>Einrichten von Rollen

Für diese exemplarische Vorgehensweise benötigen Sie zusätzlich zu Ihrem Azure Stack Hub-Operatorkonto zwei Azure AD-Konten. Wenn Sie nicht über diese beiden Konten verfügen, müssen Sie sie erstellen. Die Konten können einem beliebigen Azure AD-Benutzer gehören und werden als *delegierter Anbieter* und *Benutzer* bezeichnet.

| **Rolle** | **Organisatorische Rechte** |
| --- | --- |
| Delegierter Anbieter |Benutzer |
| Benutzer |Benutzer |

 > [!NOTE]
 > Im Falle eines CSP-Handelspartners müssen sich diese Benutzer im Mandantenverzeichnis (Azure AD für Benutzer) befinden, damit dieser delegierte Anbieter erstellt werden kann. Der Azure Stack Hub-Operator muss [zuerst ein Onboarding](azure-stack-enable-multitenancy.md) dieser Azure AD-Instanz für den Mandanten durchführen und dann die Nutzung und Abrechnung wie [hier](azure-stack-csp-howto-register-tenants.md) beschrieben einrichten.

### <a name="identify-the-delegated-provider"></a>Identifizieren des delegierten Anbieters

1. Melden Sie sich beim Administratorportal als Azure Stack Hub-Operator an.

1. So erstellen Sie ein Angebot, mit dem ein Benutzer ein delegierter Anbieter werden kann

   a.  [Erstellen Sie einen Plan](azure-stack-create-plan.md).
       Dieser Plan sollte nur den Abonnementdienst enthalten. In diesem Artikel wird ein Plan namens **PlanForDelegation** als Beispiel verwendet.

   b.  [Erstellen Sie ein Angebot](azure-stack-create-offer.md), das auf diesem Plan basiert. In diesem Artikel wird ein Angebot mit dem Namen **OfferToDP** als Beispiel verwendet.

   c.  Fügen Sie den delegierten Anbieter diesem Angebot als Abonnenten hinzu, indem Sie **Abonnements**, **Hinzufügen** und dann **Neues Mandantenabonnement** auswählen.

   ![Hinzufügen des delegierten Anbieters als Abonnent im Azure Stack Hub-Administratorportal](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Wie bei allen Azure Stack Hub-Angeboten haben Sie die Option, das Angebot öffentlich zu machen und es Benutzern zu ermöglichen, sich dafür zu registrieren. Sie können auch ein privates Angebot einrichten und es dem Azure Stack Hub-Operator überlassen, die Registrierung durchzuführen. Delegierte Anbieter sind in der Regel eine kleine Gruppe. Sie sollten kontrollieren können, wer in diese Gruppe aufgenommen wird. Ein privates Angebot ist also in den meisten Fällen sinnvoll.

### <a name="azure-stack-hub-operator-creates-the-delegated-offer"></a>Erstellen des delegierten Angebots durch den Azure Stack Hub-Operator

Der nächste Schritt besteht darin, den Plan und das Angebot zu erstellen, den/das Sie delegieren möchten und den/das Ihre Benutzer verwenden werden. Es ist ratsam, das Angebot so zu definieren, wie es Ihren Benutzern angezeigt werden soll, da der delegierte Anbieter die Pläne und darin enthaltenen Kontingente nicht ändern kann.

1. Melden Sie sich als Azure Stack Hub-Operator an, und [erstellen Sie einen Plan](azure-stack-create-plan.md) sowie ein auf dem Plan basierendes [Angebot](azure-stack-create-offer.md). In diesem Artikel wird ein Angebot mit dem Namen **DelegatedOffer** als Beispiel verwendet.

   > [!NOTE]
   > Dieses Angebot muss nicht öffentlich sein, aber Sie können es bei Bedarf öffentlich verfügbar machen. In den meisten Fällen empfiehlt es sich aber, nur delegierte Anbieter auf das Angebot zugreifen zu lassen. Sobald Sie, wie in den folgenden Schritten beschrieben, ein privates Angebot delegieren, kann der delegierte Anbieter darauf zugreifen.

2. Delegieren Sie das Angebot. Wechseln Sie zu **DelegatedOffer**. Wählen Sie unter **Einstellungen** die Option **Delegierte Anbieter** und dann **Hinzufügen** aus.

3. Wählen Sie in der Dropdownliste das Abonnement für den delegierten Anbieter aus, und wählen Sie dann **Delegieren** aus.

   ![Hinzufügen eines delegierten Anbieters im Azure Stack Hub-Administratorportal](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Anpassen des Angebots durch den delegierten Anbieter

Melden Sie sich als delegierter Anbieter beim Benutzerportal an, und verwenden Sie das delegierte Angebot dann als Vorlage, um ein neues Angebot zu erstellen.

1. Wählen Sie **+ Ressource erstellen**, **Mandantenangebote + Pläne** und dann **Angebot** aus.

    ![Erstellen eines neuen Angebots im Azure Stack Hub-Benutzerportal](media/azure-stack-delegated-provider/image5.png)

2. Weisen Sie dem Angebot einen Namen zu. In diesem Beispiel wird **ResellerOffer** verwendet. Wählen Sie das delegierte Angebot aus, auf dem dieses Angebot basieren soll, und klicken Sie auf **Erstellen**.

   ![Zuweisen eines Namens im Azure Stack Hub-Benutzerportal](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Es ist wichtig, zu verstehen, dass delegierte Anbieter nur Angebote auswählen können, die an sie delegiert wurden. Sie können diese Angebote nicht änder. Diese Angebote können nur von einem Azure Stack Hub-Operator geändert werden. Beispielsweise können die Pläne und Kontingente nur von einem Betreiber geändert werden. Ein delegierter Anbieter erstellt kein Angebot aufgrund von Basis- und Add-On-Plänen.

3. Der delegierte Anbieter kann diese Angebote über die URL zu seinem eigenen Portal veröffentlichen. Machen Sie das Angebot durch Auswahl von **Durchsuchen** und **Angebote** öffentlich. Wählen Sie das Angebot aus, und wählen Sie dann **Status ändern**.

4. Die öffentlichen delegierten Angebote werden jetzt nur über das delegierte Portal angezeigt. So suchen und ändern Sie diese URL:

    a.  Wählen Sie **Durchsuchen**, **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **ALLGEMEIN** die Option **Abonnements** aus. Wählen Sie das Abonnement des delegierten Anbieters (z. B. **DPSubscription**) und dann **Eigenschaften** aus.

    b.  Kopieren Sie die Portal-URL an einen anderen Ort, beispielsweise in Text-Editor.

    ![Auswählen des Abonnements des delegierten Anbieters im Azure Stack Hub-Benutzerportal](media/azure-stack-delegated-provider/dpportaluri.png)  

   Sie haben nun das Erstellen eines delegierten Angebots als delegierter Anbieter abgeschlossen. Melden Sie sich als delegierter Anbieter ab, und schließen Sie das Browserfenster.

### <a name="sign-up-for-the-offer"></a>Anmelden für das Angebot

1. Wechseln Sie in einem neuen Browserfenster zu der URL des Portals mit delegierten Angeboten, die Sie im vorherigen Schritt gespeichert haben. Melden Sie sich als Benutzer beim Portal an.

   >[!NOTE]
   >Die delegierten Angebote sind nur dann sichtbar, wenn Sie das delegierte Portal verwenden.

1. Wählen Sie auf dem Dashboard **Abonnement erwerben**. Sie sehen, dass dem Benutzer nur die delegierten Angebote angezeigt werden, die vom delegierten Anbieter erstellt wurden.

   ![Anzeigen und Auswählen von Angeboten im Azure Stack Hub-Benutzerportal](media/azure-stack-delegated-provider/image8.png)

Das Delegieren eines Angebots ist abgeschlossen. Ein Benutzer kann sich jetzt für dieses Angebot registrieren, indem er ein Abonnement dafür bezieht.

## <a name="move-subscriptions-between-delegated-providers"></a>Verschieben von Abonnements zwischen delegierten Anbietern

Abonnements können bei Bedarf zwischen neuen oder vorhandenen Abonnements delegierter Anbieter verschoben werden, die demselben Verzeichnismandanten angehören. Sie können sie mit dem PowerShell-Cmdlet [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin) verschieben.

Das Verschieben von Abonnements ist in den folgenden Fällen hilfreich:

* Sie integrieren ein neues Teammitglied, das die Rolle des delegierten Anbieters übernimmt, und möchten diesem Teammitglied Benutzerabonnements zuweisen, die zuvor im Standardabonnement des Anbieters erstellt wurden.
* Es sind mehrere Abonnements von delegierten Anbietern im gleichen Verzeichnismandanten (Azure AD) vorhanden, und Sie müssen dafür Benutzerabonnements verschieben. Ein solches Szenario kann vorliegen, wenn ein Teammitglied zu einem anderen Team wechselt und sein Abonnement dem neuen Team zugewiesen werden muss.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer VM](../user/azure-stack-create-vm-template.md)
