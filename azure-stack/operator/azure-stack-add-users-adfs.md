---
title: Hinzufügen von Benutzern für ADFS-Bereitstellungen von Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Benutzer für ADFS-Bereitstellungen von Azure Stack hinzufügen.
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
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 93541cd02c3d4110e008e5f3f7011f53be897475
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986235"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Hinzufügen von Azure Stack-Benutzern in AD FS
Sie können das Snap-In **Active Directory-Benutzer und -Computer** zum Hinzufügen weiterer Benutzer zu einer Azure Stack-Umgebung verwenden und dabei AD FS als Identitätsanbieter nutzen.

## <a name="add-windows-server-active-directory-users"></a>Hinzufügen von Windows Server Active Directory-Benutzern
> [!TIP]
> In diesem Beispiel wird das standardmäßige ASDK-Active Directory „azurestack.local“ verwendet. 

1. Melden Sie sich bei einem Computer mit einem Konto an, das Zugriff auf die Windows-Verwaltungstools bietet, und öffnen Sie eine neue Microsoft Management Console (MMC).
2. Wählen Sie **Datei > Snap-Ins hinzufügen bzw. entfernen** aus.
3. Klicken Sie auf **Active Directory-Benutzer und -Computer** > **AzureStack.local** > **Benutzer**.
4. Wählen Sie **Aktion** > **Neu** > **Benutzer** aus.
5. Geben Sie unter „Neues Objekt – Benutzer“ Detailinformationen für den Benutzer an. Klicken Sie auf **Weiter**.
6. Geben Sie ein Kennwort an, und bestätigen Sie es.
7. Wählen Sie **Weiter** aus, um die Eingabe der Werte abzuschließen. Wählen Sie **Fertig stellen** aus, um den Benutzer zu erstellen.


## <a name="next-steps"></a>Nächste Schritte
[Erstellen von Dienstprinzipalen](azure-stack-create-service-principals.md)