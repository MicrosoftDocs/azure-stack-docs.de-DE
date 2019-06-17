---
title: Erstellen einer Registrierungsrolle für Azure Stack
description: Erfahren Sie, wie Sie eine benutzerdefinierte Rolle erstellen, damit Sie die Registrierung nicht als globaler Administrator ausführen müssen.
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
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 50c15403ec4bf41f7513af30d2dca53310d45298
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828258"
---
# <a name="create-a-custom-role-for-azure-stack-registration"></a>Erstellen einer benutzerdefinierten Rolle für die Azure Stack-Registrierung

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!WARNING]
> Dies ist keine Sicherheitsstatusfunktion. Wenden Sie sie in Szenarien an, in denen Sie Einschränkungen benötigen, um versehentliche Änderungen am Azure-Abonnement zu verhindern. Wenn einem Benutzer die Rechte für diese benutzerdefinierte Rolle delegiert werden, hat der Benutzer die Rechte zum Bearbeiten von Berechtigungen und zum Erhöhen von Rechten. Weisen Sie sie daher nur Benutzern zu, denen Sie für die benutzerdefinierte Rolle vertrauen.

Während der Azure Stack-Registrierung müssen Sie sich mit einem Azure Active Directory-Konto anmelden. Für das Konto werden die folgenden Berechtigungen für Azure Active Directory und das Azure-Abonnement benötigt:

* **Berechtigungen zur Anwendungsregistrierung in Ihrem Azure Active Directory-Mandanten:** Administratoren haben Anwendungsregistrierungsberechtigungen. Die Berechtigung für Benutzer ist eine globale Einstellung für alle Benutzer im Mandanten. Informationen zum Anzeigen oder Ändern der Einstellung finden Sie unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Die Einstellung *Benutzer kann Anwendungen registrieren* muss auf **Ja** festgelegt sein, damit Sie ein Benutzerkonto zum Registrieren von Azure Stack aktivieren können. Wenn die App-Registrierungseinstellung auf **Nein** festgelegt ist, können Sie kein Benutzerkonto verwenden, sondern müssen stattdessen ein globales Administratorkonto zum Registrieren von Azure Stack verwenden.

* **Ein Satz ausreichender Azure-Abonnementberechtigungen:** Benutzer, die über die Rolle „Besitzer“ verfügen, haben ausreichende Berechtigungen. Für andere Konten können Sie den Berechtigungssatz festlegen, indem Sie eine benutzerdefinierte Rolle zuweisen, wie in den folgenden Abschnitten beschrieben.

Anstatt ein Konto zu verwenden, das unter dem Azure-Abonnement über Berechtigungen vom Typ „Besitzer“ verfügt, können Sie eine benutzerdefinierte Rolle erstellen, um einem weniger privilegierten Benutzerkonto Berechtigungen zuzuweisen. Dieses Konto kann dann verwendet werden, um Ihre Azure Stack-Instanz zu registrieren.

## <a name="create-a-custom-role-using-powershell"></a>Erstellen einer benutzerdefinierten Rolle mithilfe von PowerShell

Um eine benutzerdefinierte Rolle zu erstellen, benötigen Sie die `Microsoft.Authorization/roleDefinitions/write`-Berechtigung für alle `AssignableScopes`, wie z.B. [Besitzer](/azure/role-based-access-control/built-in-roles#owner) oder [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator). Verwenden Sie die folgende JSON-Vorlage, um die Erstellung der benutzerdefinierten Rolle zu vereinfachen. Die Vorlage erstellt eine benutzerdefinierte Rolle, die den erforderlichen Lese- und Schreibzugriff für die Azure Stack-Registrierung ermöglicht.

1. Erstellen Sie eine JSON-Datei. Beispiel: `C:\CustomRoles\registrationrole.json`
2. Fügen Sie der Datei den folgenden JSON-Code hinzu. Ersetzen Sie `<SubscriptionID>` durch Ihre Azure-Abonnement-ID.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
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

Nachdem die benutzerdefinierte Rolle für die Registrierung erstellt wurde, können Sie die Rolle dem Benutzerkonto zuweisen, das zum Registrieren von Azure Stack verwendet wird.

1. Melden Sie sich mit einem Konto mit ausreichenden Berechtigungen beim Azure-Abonnement an, und delegieren Sie Rechte wie [Besitzer](/azure/role-based-access-control/built-in-roles#owner) oder [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. Wählen Sie in **Abonnements** die Option **Zugriffssteuerung (IAM) > Rollenzuweisung hinzufügen** aus.
3. Wählen Sie in **Rolle** die benutzerdefinierte Rolle *Azure Stack-Registrierungsrolle* aus, die Sie erstellt haben.
4. Wählen Sie die Benutzer aus, die Sie der Rolle zuweisen möchten.
5. Wählen Sie **Speichern** aus, um der Rolle die ausgewählten Benutzer zuzuweisen.

    ![Auswählen von Benutzern für die Rollenzuweisung](media/azure-stack-registration-role/assign-role.png)

Weitere Informationen zur Verwendung benutzerdefinierter Rollen finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und des Azure-Portals](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Nächste Schritte

[Registrieren von Azure Stack in Azure](azure-stack-registration.md)
