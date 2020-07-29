---
title: Registrieren von Windows Admin Center bei Azure
description: 'Gewusst wie: Registrieren von Windows Admin Center bei Azure.'
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: ee6794c0798bc388bc3d9313665eb0b7917cf8eb
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868002"
---
# <a name="register-windows-admin-center-with-azure"></a>Registrieren von Windows Admin Center bei Azure

> Gilt für Azure Stack HCI v20H2; Windows Server 2019

Um Azure-Dienste mit Windows Admin Center zu verwenden, müssen Sie zunächst Windows Admin Center auf einem Verwaltungscomputer installieren und eine einmalige Registrierung durchführen.

## <a name="complete-the-registration-process-in-windows-admin-center"></a>Abschließen des Registrierungsvorgangs in Windows Admin Center

1. Starten Sie Windows Admin Center, und klicken Sie auf das Zahnradsymbol **Einstellungen** in der oberen rechten Ecke, über das Sie zur Seite „Konten“ gelangen. Wählen Sie dann im **Gateway**-Menü links **Azure** aus, und klicken Sie auf **Registrieren**.
1. Sie erhalten einen eindeutigen Code. Klicken Sie auf die Schaltfläche **Kopieren** rechts neben dem Code.
1. Klicken Sie auf **Code eingeben**, wodurch ein neues Browserfenster geöffnet wird, in das Sie den in Ihrer App oder auf Ihrem Gerät angezeigten Code einfügen können.
1. Nach dem Einfügen des Codes werden Sie benachrichtigt, dass Ihre Anmeldung bei Windows Admin Center auf einem Remotegerät oder in einem Dienst bevorsteht. 
1. Geben Sie Ihre E-Mail-Adresse oder Telefonnummer ein. Wenn es sich bei Ihrem Gerät um ein verwaltetes Gerät handelt, werden Sie zur Authentifizierung zur Anmeldeseite Ihrer Organisation weitergeleitet. Befolgen Sie die Anweisungen, und geben Sie die entsprechenden Anmeldeinformationen ein.
1. Die folgende Meldung sollte angezeigt werden: „Sie haben sich bei der Windows Admin Center-Anwendung auf Ihrem Gerät angemeldet“. Schließen Sie das Browserfenster, um zur ursprünglichen Registrierungsseite zurückzukehren.
1. Stellen Sie eine Verbindung mit Azure Active Directory her, indem Sie Ihre Azure Active Directory-ID (Mandanten-ID) eingeben. Wenn Sie die vorstehenden Schritte ausgeführt haben, ist das ID-Feld voraufgefüllt. Wenn Ihre Organisation Ihnen keine vorhandene ID angegeben hat, belassen Sie die **Azure Active Directory-Anwendung** auf der Einstellung **Neue erstellen**. Wenn Sie bereits über eine ID verfügen, klicken Sie auf **Vorhandene verwenden**, dann wird ein leeres Feld angezeigt, in dem Sie die ID eingeben können, die Ihnen von Ihrem Administrator zur Verfügung gestellt wurde. Nachdem Sie Ihre ID eingegeben haben, bestätigt Windows Admin Center, dass ein Konto mit dieser ID gefunden wurde. Wenn Sie über eine vorhandene ID verfügen, aber nicht wissen, wozu sie dient, führen Sie [diese Schritte](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) aus, um sie abzurufen.
1. Klicken Sie auf die Schaltfläche **Verbinden**, um die Verbindung zu Azure herzustellen. Es sollte eine Bestätigung angezeigt werden, dass Sie jetzt mit Azure AD verbunden sind.
1. Erteilen Sie Berechtigungen in Azure, indem Sie im Azure-Portal zu **App-Berechtigungen** navigieren. Wählen Sie unter **Einwilligung erteilen** die Option **Administratoreinwilligung erteilen** aus.
1. Schließen Sie das Fenster, und melden Sie sich mit Ihrem Azure-Konto bei Windows Admin Center an.

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun zu diesen Schritten bereit:

- [Verwenden von Azure Stack HCI mit Windows Admin Center](../get-started.md)
- [Herstellen einer Verbindung mit Azure-Hybriddiensten](/windows-server/manage/windows-admin-center/azure/)