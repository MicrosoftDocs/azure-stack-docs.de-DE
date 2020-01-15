---
title: Festlegen von Zugriffsberechtigungen mithilfe der rollenbasierten Zugriffssteuerung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) Zugriffsberechtigungen in Azure Stack festlegen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: ae5b167ca807fc1e28c13c553e13ea89c7e6fe49
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492784"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Festlegen von Zugriffsberechtigungen mithilfe der rollenbasierten Zugriffssteuerung

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Ein Benutzer in Azure Stack kann ein Leser, ein Besitzer oder ein Mitwirkender an jeder Instanz eines Abonnements, einer Ressourcengruppe oder eines Diensts sein. Beispielsweise kann Benutzer A Leseberechtigungen für „Abonnement 1“ besitzen, aber auch die Besitzberechtigungen für „Virtueller Computer 7“.

 - Leser: Der Benutzer kann alles anzeigen, jedoch keine Änderungen vornehmen.
 - Mitwirkender: Der Benutzer kann alles verwalten, außer des Zugriffs auf Ressourcen.
 - Besitzer: Der Benutzer kann alles verwalten, einschließlich des Zugriffs auf Ressourcen.
 - Benutzerdefiniert: Der Benutzer hat begrenzten, spezifischen Zugriff auf Ressourcen.

 Weitere Informationen zum Erstellen einer benutzerdefinierten Rolle finden Sie unter [Benutzerdefinierte Rollen für Azure-Ressourcen](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Festlegen von Zugriffsberechtigungen für Benutzer

1. Melden Sie sich mit einem Konto an, das Besitzerberechtigungen für die Ressource hat, die Sie verwalten möchten.
2. Klicken Sie auf dem Blatt für die Ressource auf das Symbol **Zugriff**![](media/azure-stack-manage-permissions/image1.png).
3. Klicken Sie auf dem Blatt **Benutzer** auf **Rollen**.
4. Klicken Sie auf dem Blatt **Rollen** auf **Hinzufügen**, um Berechtigungen für den Benutzer hinzuzufügen.

## <a name="set-access-permissions-for-a-universal-group"></a>Festlegen von Zugriffsberechtigungen für eine universelle Gruppe 

> [!Note]
> Gilt nur für Active Directory-Verbunddienste (AD FS).

1. Melden Sie sich mit einem Konto an, das Besitzerberechtigungen für die Ressource hat, die Sie verwalten möchten.
2. Klicken Sie auf dem Blatt für die Ressource auf das Symbol **Zugriff**![](media/azure-stack-manage-permissions/image1.png).
3. Klicken Sie auf dem Blatt **Benutzer** auf **Rollen**.
4. Klicken Sie auf dem Blatt **Rollen** auf **Hinzufügen**, um Berechtigungen für die universelle Active Directory-Gruppe hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Azure Stack-Mandanten](azure-stack-add-new-user-aad.md)