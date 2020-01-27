---
title: Hinzufügen von Azure Stack Hub-Benutzern in AD FS
description: Erfahren Sie, wie Sie Bereitstellungen von Active Directory-Verbunddienste (AD FS) Azure Stack Hub-Benutzer hinzufügen.
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 2c81d8ccceb4fa2f1e05b006232e3b6513ace469
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880956"
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