---
title: Verwalten des Zugriffs auf Ressourcen mit der rollenbasierten Zugriffssteuerung in Azure Stack Hub
description: Hier erfahren Sie, wie Sie als Dienstadministrator oder Mandant in Azure Stack Hub Berechtigungen für die rollenbasierte Zugriffssteuerung (RBAC) verwalten.
author: bryanla
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 2f633e44b4646659a888df85e51450147104710e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703977"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>Verwalten des Zugriffs auf Ressourcen mit der rollenbasierten Zugriffssteuerung in Azure Stack Hub

Azure Stack Hub unterstützt die rollenbasierte Zugriffssteuerung (RBAC), das gleiche von Microsoft Azure verwendete [Sicherheitsmodell für die Zugriffsverwaltung](/azure/role-based-access-control/overview). Anhand der RBAC können Sie den Benutzer-, Gruppen- oder App-Zugriff auf Abonnements, Ressourcen und Dienste verwalten.

## <a name="basics-of-access-management"></a>Grundlagen zur Zugriffsverwaltung

Die rollenbasierte Zugriffssteuerung (RBAC) bietet eine präzise Zugriffssteuerung, mit der Sie Ihre Umgebung schützen können. Dabei erteilen Sie Benutzern genau die benötigten Berechtigungen, indem Sie eine RBAC-Rolle in einem bestimmten Bereich zuweisen. Der Bereich der Rollenzuweisung kann ein Abonnement, eine Ressourcengruppe oder eine einzelne Ressource sein. Ausführlichere Informationen zur Zugriffsverwaltung finden Sie im Artikel [Rollenbasierte Zugriffssteuerung im Azure-Portal](/azure/role-based-access-control/overview).

> [!NOTE]
> Wenn Azure Stack Hub mit Active Directory-Verbunddiensten (Active Directory Federation Services, AD FS) als Identitätsanbieter bereitgestellt wird, werden nur universelle Gruppen für RBAC-Szenarien unterstützt.

### <a name="built-in-roles"></a>Integrierte Rollen

Azure Stack Hub verfügt über drei grundlegende Rollen, die für alle Ressourcentypen gelten:

* **Besitzer** können alles verwalten, einschließlich des Zugriffs auf Ressourcen.
* **Mitwirkender** können alles mit Ausnahme des Zugriffs auf Ressourcen verwalten.
* **Leser** können alles anzeigen, jedoch keine Änderungen vornehmen.

### <a name="resource-hierarchy-and-inheritance"></a>Ressourcenhierarchie und Vererbung

Azure Stack Hub verfügt über die folgende Ressourcenhierarchie:

* Jedes Abonnement gehört einem Verzeichnis.
* Jede Ressourcengruppe gehört einem Abonnement.
* Jede Ressource gehört einer Ressourcengruppe.

Der Zugriff, den Sie auf übergeordneter Ebene gewähren, wird auf untergeordneter Ebene geerbt. Beispiel:

* Sie weisen die Rolle **Leser** einer Azure AD-Gruppe im Abonnementbereich zu. Die Mitglieder dieser Gruppe können alle Ressourcengruppen und Ressourcen im Abonnement anzeigen.
* Sie weisen die Rolle **Mitwirkender** einer App im Ressourcengruppenbereich zu. Damit kann die App Ressourcen aller Typen in dieser Ressourcengruppe verwalten, aber keine anderen Ressourcengruppen des Abonnements.

### <a name="assigning-roles"></a>Zuweisen von Rollen

Sie können einem Benutzer mehrere Rollen zuweisen, und jede Rolle kann einem anderen Bereich zugeordnet werden. Beispiel:

* Sie weisen TestUser-A die Rolle **Leser** für Subscription-1 zu.
* Sie weisen TestUser-A die Rolle **Besitzer** für TestVM-1 zu.

Der Artikel zu [Rollenzuweisungen](/azure/role-based-access-control/role-assignments-portal) in Azure enthält ausführliche Informationen zum Anzeigen, Zuweisen und Löschen von Rollen.

## <a name="set-access-permissions-for-a-user"></a>Festlegen von Zugriffsberechtigungen für Benutzer

In den folgenden Schritte wird beschrieben, wie Berechtigungen für einen Benutzer konfiguriert werden.

1. Melden Sie sich mit einem Konto an, das Besitzerberechtigungen für die Ressource hat, die Sie verwalten möchten.
2. Wählen Sie im linken Navigationsbereich die Option **Ressourcengruppen** aus.
3. Wählen Sie den Namen der Ressourcengruppe, der Sie Berechtigungen erteilen möchten.
4. Wählen Sie im Navigationsbereich der Ressourcengruppe **Zugriffssteuerung (IAM)** .<BR> Die Ansicht **Rollenzuweisungen** enthält die Elemente, die über Zugriff auf die Ressourcengruppe verfügen. Sie können die Ergebnisse filtern und gruppieren.
5. Wählen Sie auf der Menüleiste **Zugriffssteuerung** die Option **Hinzufügen** aus.
6. Gehen Sie im Bereich **Berechtigungen hinzufügen** wie folgt vor:

   * Wählen Sie aus der Dropdownliste **Rolle** die Rolle aus, die Sie zugewiesen werden soll.
   * Wählen Sie aus der Dropdownliste **Zugriff zuweisen an** die Ressource aus, die Sie zugewiesen werden soll.
   * Wählen Sie den Benutzer, die Gruppe oder die App als das Element in Ihrem Verzeichnis aus, für das Sie Zugriff gewähren möchten. Sie können das Verzeichnis mit Anzeigenamen, E-Mail-Adressen und Objektbezeichnern durchsuchen.

7. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen von Dienstprinzipalen](../operator/azure-stack-create-service-principals.md)
