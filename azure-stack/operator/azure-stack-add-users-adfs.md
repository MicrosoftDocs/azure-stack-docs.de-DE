---
title: Hinzufügen von Azure Stack Hub-Benutzern in AD FS
description: Erfahren Sie, wie Sie Bereitstellungen von Active Directory-Verbunddienste (AD FS) Azure Stack Hub-Benutzer hinzufügen.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: fc60c23f47ea5e1afd68c6e4f6299d0bf83b4a24
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509397"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Hinzufügen eines neuen Azure Stack Hub-Benutzerkontos in Active Directory-Verbunddienste (AD FS)

Sie können das Snap-In **Active Directory-Benutzer und -Computer** zum Hinzufügen weiterer Benutzer zu einer Azure Stack Hub-Umgebung verwenden und dabei AD FS als Identitätsanbieter nutzen.

## <a name="add-windows-server-active-directory-users"></a>Hinzufügen von Windows Server Active Directory-Benutzern

1. Melden Sie sich auf einem Computer mit einem Konto an, das Zugriff auf die Windows-Verwaltungstools bietet, und öffnen Sie eine neue Microsoft Management Console-Instanz (MMC).
2. Wählen Sie **Datei > Snap-Ins hinzufügen bzw. entfernen** aus.

   > [!TIP]
   > Ersetzen Sie *directory-domain* durch die Domäne, die Ihrem Verzeichnis entspricht. 

3. Wählen Sie **Active Directory-Benutzer und -Computer** > *directory-domain* > **Benutzer** aus.
4. Wählen Sie **Aktion** > **Neu** > **Benutzer** aus.
5. Geben Sie unter „Neues Objekt – Benutzer“ Details zum Benutzer an. Wählen Sie **Weiter** aus.
6. Geben Sie ein Kennwort an, und bestätigen Sie es.
7. Wählen Sie **Weiter** aus, um die Eingabe der Werte abzuschließen. Wählen Sie **Fertig stellen** aus, um den Benutzer zu erstellen.


## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer App-Identität für den Zugriff auf Azure Stack Hub-Ressourcen](azure-stack-create-service-principals.md)