---
title: Festlegen von Zugriffsberechtigungen mithilfe der rollenbasierten Zugriffssteuerung
description: Hier erfahren Sie, wie Sie mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) Zugriffsberechtigungen in Azure Stack Hub festlegen.
author: BryanLa
ms.topic: article
ms.date: 12/23/2019
ms.author: bryanla
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 5b101330f66396d7e1e2fe93d9168244dc97d97e
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231675"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Festlegen von Zugriffsberechtigungen mithilfe der rollenbasierten Zugriffssteuerung

Ein Benutzer in Azure Stack Hub kann ein Leser, ein Besitzer oder ein Mitwirkender an jeder Instanz eines Abonnements, einer Ressourcengruppe oder eines Diensts sein. Beispielsweise kann Benutzer A Leseberechtigungen für „Abonnement 1“ besitzen, aber auch die Besitzberechtigungen für „Virtueller Computer 7“.

 - Leser: Der Benutzer kann alles anzeigen, jedoch keine Änderungen vornehmen.
 - Mitwirkender: Der Benutzer kann alles verwalten, außer des Zugriffs auf Ressourcen.
 - Besitzer: Der Benutzer kann alles verwalten, einschließlich des Zugriffs auf Ressourcen.
 - Benutzerdefiniert: Der Benutzer hat begrenzten, spezifischen Zugriff auf Ressourcen.

 Weitere Informationen zum Erstellen einer benutzerdefinierten Rolle finden Sie unter [Benutzerdefinierte Rollen für Azure-Ressourcen](/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Festlegen von Zugriffsberechtigungen für Benutzer

1. Melden Sie sich mit einem Konto an, das Besitzerberechtigungen für die Ressource hat, die Sie verwalten möchten.
2. Klicken Sie auf dem Blatt für die Ressource auf das Symbol **Zugriff**. ![Das Zugriffssymbol ist eine Kontur des Kopfes und der Schultern von zwei Personen.](media/azure-stack-manage-permissions/image1.png).
3. Klicken Sie auf dem Blatt **Benutzer** auf **Rollen**.
4. Klicken Sie auf dem Blatt **Rollen** auf **Hinzufügen**, um Berechtigungen für den Benutzer hinzuzufügen.

## <a name="set-access-permissions-for-a-universal-group"></a>Festlegen von Zugriffsberechtigungen für eine universelle Gruppe 

> [!Note]
> Gilt nur für Active Directory-Verbunddienste (AD FS).

1. Melden Sie sich mit einem Konto an, das Besitzerberechtigungen für die Ressource hat, die Sie verwalten möchten.
2. Klicken Sie auf dem Blatt für die Ressource auf das Symbol **Zugriff**. ![Das Zugriffssymbol ist eine Kontur des Kopfes und der Schultern von zwei Personen.](media/azure-stack-manage-permissions/image1.png).
3. Klicken Sie auf dem Blatt **Benutzer** auf **Rollen**.
4. Klicken Sie auf dem Blatt **Rollen** auf **Hinzufügen**, um Berechtigungen für die universelle Active Directory-Gruppe hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen eines Azure Stack Hub-Mandanten](azure-stack-add-new-user-aad.md)
