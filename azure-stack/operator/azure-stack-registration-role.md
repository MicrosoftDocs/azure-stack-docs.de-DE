---
title: Erstellen einer benutzerdefinierten Rolle für die Azure Stack Hub-Registrierung
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie eine benutzerdefinierte Rolle erstellen, damit Sie die Azure Stack Hub-Registrierung nicht als globaler Administrator ausführen müssen.
author: BryanLa
ms.topic: how-to
ms.date: 03/27/2020
ms.author: bryanla
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: cb9dd6e3049a4d3287bf60dd939bca4d1a0335da
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82173929"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>Erstellen einer benutzerdefinierten Rolle für die Azure Stack Hub-Registrierung

> [!WARNING]
> Dies ist keine Sicherheitsstatusfunktion. Wenden Sie sie in Szenarien an, in denen Sie Einschränkungen benötigen, um versehentliche Änderungen am Azure-Abonnement zu verhindern. Wenn einem Benutzer die Rechte für diese benutzerdefinierte Rolle delegiert werden, hat der Benutzer die Rechte zum Bearbeiten von Berechtigungen und zum Erhöhen von Rechten. Weisen Sie sie daher nur Benutzern zu, denen Sie für die benutzerdefinierte Rolle vertrauen.

Während der Azure Stack Hub-Registrierung müssen Sie sich mit einem Azure Active Directory-Konto (Azure AD) anmelden. Für das Konto werden die folgenden Berechtigungen für Azure AD und das Azure-Abonnement benötigt:

* **Berechtigungen für die App-Registrierung in Ihrem Azure AD-Mandanten:** Administratoren haben App-Registrierungsberechtigungen. Die Berechtigung für Benutzer ist eine globale Einstellung für alle Benutzer im Mandanten. Informationen zum Anzeigen oder Ändern der Einstellung finden Sie unter [Erstellen einer Azure AD-App und eines Dienstprinzipals mit Ressourcenzugriff](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Die Einstellung *Benutzer kann Anwendungen registrieren* muss auf **Ja** festgelegt sein, damit Sie ein Benutzerkonto zum Registrieren von Azure Stack Hub aktivieren können. Wenn die App-Registrierungseinstellung auf **Nein** festgelegt ist, können Sie kein Benutzerkonto zum Registrieren von Azure Stack Hub verwenden, sondern müssen stattdessen ein globales Administratorkonto verwenden.

* **Ein Satz ausreichender Azure-Abonnementberechtigungen:** Benutzer, die über die Rolle „Besitzer“ verfügen, haben ausreichende Berechtigungen. Für andere Konten können Sie den Berechtigungssatz festlegen, indem Sie eine benutzerdefinierte Rolle zuweisen, wie in den folgenden Abschnitten beschrieben.

Anstatt ein Konto zu verwenden, das unter dem Azure-Abonnement über Berechtigungen vom Typ „Besitzer“ verfügt, können Sie eine benutzerdefinierte Rolle erstellen, um einem weniger privilegierten Benutzerkonto Berechtigungen zuzuweisen. Dieses Konto kann dann verwendet werden, um Ihre Azure Stack Hub-Instanz zu registrieren.

## <a name="create-a-custom-role-using-powershell"></a>Erstellen einer benutzerdefinierten Rolle mithilfe von PowerShell

Um eine benutzerdefinierte Rolle zu erstellen, benötigen Sie die `Microsoft.Authorization/roleDefinitions/write`-Berechtigung für alle `AssignableScopes`, wie z.B. [Besitzer](/azure/role-based-access-control/built-in-roles#owner) oder [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator). Verwenden Sie die folgende JSON-Vorlage, um die Erstellung der benutzerdefinierten Rolle zu vereinfachen. Die Vorlage erstellt eine benutzerdefinierte Rolle, die den erforderlichen Lese- und Schreibzugriff für die Azure Stack Hub-Registrierung ermöglicht.

1. Erstellen Sie eine JSON-Datei. z. B. `C:\CustomRoles\registrationrole.json`.
2. Fügen Sie der Datei den folgenden JSON-Code hinzu. Ersetzen Sie `<SubscriptionID>` durch Ihre Azure-Abonnement-ID.

    ```json
    {
      "Name": "Azure Stack Hub registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack Hub",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read",
        "Microsoft.Authorization/locks/read",
        "Microsoft.Authorization/locks/write
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. Stellen Sie in PowerShell eine Verbindung mit Azure her, damit Sie Azure Resource Manager verwenden können. Authentifizieren Sie sich bei Aufforderung mit einem Konto mit ausreichenden Berechtigungen, z.B. [Besitzer](/azure/role-based-access-control/built-in-roles#owner) oder [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Verwenden Sie **New-AzureRmRoleDefinition** zum Erstellen der benutzerdefinierten Rolle, indem Sie die JSON-Vorlagendatei angeben.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Zuweisen eines Benutzers zur Registrierungsrolle

Nachdem die benutzerdefinierte Rolle für die Registrierung erstellt wurde, können Sie die Rolle dem Benutzerkonto zuweisen, das zum Registrieren von Azure Stack Hub verwendet wird.

1. Melden Sie sich mit einem Konto mit ausreichenden Berechtigungen beim Azure-Abonnement an, und delegieren Sie Rechte wie [Besitzer](/azure/role-based-access-control/built-in-roles#owner) oder [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. Wählen Sie in **Abonnements** die Option **Zugriffssteuerung (IAM) > Rollenzuweisung hinzufügen** aus.
3. Wählen Sie in **Rolle** die von Ihnen erstellte benutzerdefinierte Rolle aus: *Rolle für die Azure Stack Hub-Registrierung*
4. Wählen Sie die Benutzer aus, die Sie der Rolle zuweisen möchten.
5. Wählen Sie **Speichern** aus, um der Rolle die ausgewählten Benutzer zuzuweisen.

    ![Wählen Sie Benutzer aus, die der benutzerdefinierten Rolle im Azure-Portal zugewiesen werden sollen.](media/azure-stack-registration-role/assign-role.png)

Weitere Informationen zur Verwendung benutzerdefinierter Rollen finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und des Azure-Portals](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Nächste Schritte

[Registrieren von Azure Stack Hub in Azure](azure-stack-registration.md)
