---
title: Hinzufügen von Azure Stack-Benutzern in AD FS | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Bereitstellungen von Active Directory-Verbunddienste (AD FS) Azure Stack-Benutzer hinzufügen.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 4411290b075e105a827de8fb2c8295dfd84e3b50
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118632"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Hinzufügen von Azure Stack-Benutzern in AD FS
Sie können das Snap-In **Active Directory-Benutzer und -Computer** zum Hinzufügen weiterer Benutzer zu einer Azure Stack-Umgebung verwenden und dabei Active Directory-Verbunddienste (AD FS) als Identitätsanbieter nutzen.

## <a name="add-windows-server-active-directory-users"></a>Hinzufügen von Windows Server Active Directory-Benutzern
> [!TIP]
> In diesem Beispiel wird das standardmäßige ASDK-Active Directory „azurestack.local“ verwendet. 

1. Melden Sie sich auf einem Computer mit einem Konto an, das Zugriff auf die Windows-Verwaltungstools bietet, und öffnen Sie eine neue Microsoft Management Console-Instanz (MMC).
2. Wählen Sie **Datei > Snap-Ins hinzufügen bzw. entfernen** aus.
3. Klicken Sie auf **Active Directory-Benutzer und -Computer** > **AzureStack.local** > **Benutzer**.
4. Wählen Sie **Aktion** > **Neu** > **Benutzer** aus.
5. Geben Sie unter „Neues Objekt – Benutzer“ Details zum Benutzer an. Klicken Sie auf **Weiter**.
6. Geben Sie ein Kennwort an, und bestätigen Sie es.
7. Wählen Sie **Weiter** aus, um die Eingabe der Werte abzuschließen. Wählen Sie **Fertig stellen** aus, um den Benutzer zu erstellen.


## <a name="next-steps"></a>Nächste Schritte
[Erstellen von Dienstprinzipalen](azure-stack-create-service-principals.md)