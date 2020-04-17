---
title: Hinzufügen neuer Azure Stack Hub-Benutzerkonten in Azure Active Directory
description: Erfahren Sie, wie Sie ein Benutzerkonto in Azure Active Directory erstellen, damit Sie das Benutzerportal erkunden können.
author: JustinHall
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 435b2bfdd2de9a232379190a0fe3db0dca642def
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700560"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>Hinzufügen neuer Azure Stack Hub-Benutzerkonten in Azure Active Directory (Azure AD)

Um Angebote und Pläne testen und Ressourcen erstellen zu können, benötigen Sie ein Benutzerkonto. Sie erstellen ein Benutzerkonto in Ihrem Azure AD-Mandanten, entweder mithilfe des Azure-Portals oder mit PowerShell.

## <a name="create-user-account-using-the-azure-portal"></a>Erstellen eines Benutzerkontos im Azure-Portal

Sie benötigen ein Azure-Abonnement, um das Azure-Portal verwenden zu können.

1. Melden Sie sich bei [Azure](https://portal.azure.com) an.
2. Wählen Sie in der linken Navigationsleiste **Active Directory** aus, und wechseln Sie in das Verzeichnis, das Sie für Azure Stack Hub verwenden möchten (oder erstellen Sie ein neues Verzeichnis).
3. Wählen Sie **Azure Active Directory** > **Benutzer** > **Neuer Benutzer** aus.

    ![Seite „Benutzer – Alle Benutzer“ mit Hervorhebung von „Neuer Benutzer“](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Geben Sie auf der Seite **Benutzer** die erforderlichen Informationen ein.

    ![„Neuen Benutzer hinzufügen“, Seite „Benutzer“ mit Benutzerinformationen](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Name (erforderlich)** : Der Vor- und Nachname des neuen Benutzers. Beispielsweise Mary Parker.
   - **Benutzername** (erforderlich): Der Benutzername des neuen Benutzers. Beispiel: mary@contoso.com.
       Der Domänenteil des Benutzernamens muss entweder der anfängliche Standarddomänenname sein, „<_IhrDomänenname_>.onmicrosoft.com“, oder ein benutzerdefinierter Domänennamen, z. B. „contoso.com“. Weitere Informationen zum Erstellen eines benutzerdefinierten Domänennamens finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
   - **Profil:** Optional können Sie weitere Informationen zum Benutzer hinzufügen. Sie können Benutzerinformationen auch zu einem späteren Zeitpunkt hinzufügen. Weitere Informationen zum Hinzufügen von Benutzerinformationen finden Sie unter [Hinzufügen oder Ändern von Benutzerprofilinformationen](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Verzeichnisrolle:** Wählen Sie **Benutzer** aus.

5. Aktivieren Sie **Kennwort anzeigen**, und kopieren Sie das automatisch generierte Kennwort, das im Feld **Kennwort** angegeben wird. Sie benötigen dieses Kennwort bei der ersten Anmeldung.

6. Klicken Sie auf **Erstellen**.

    Der Benutzer wird erstellt und Ihrem Azure AD-Mandanten hinzugefügt.

7. Melden Sie sich mit dem neuen Konto beim Azure-Portal an. Ändern Sie das Kennwort, wenn Sie dazu aufgefordert werden.
8. Melden Sie sich mit dem neuen Konto bei `https://portal.local.azurestack.external` an, um das Benutzerportal anzuzeigen.

## <a name="create-a-user-account-using-powershell"></a>Erstellen eines Benutzerkontos mithilfe von PowerShell

Falls Sie kein Azure-Abonnement besitzen, können Sie ein Mandantenbenutzerkonto nicht mithilfe des Azure-Portals hinzufügen. In diesem Fall können Sie stattdessen das Azure AD-Modul für Windows PowerShell verwenden.

> [!NOTE]
> Wenn Sie ein Microsoft-Konto zum Bereitstellen des ASDK verwenden, können Sie ein Mandantenkonto nicht mithilfe von Azure AD PowerShell erstellen.

1. Installieren Sie die **64-Bit**-Version des [Microsoft Online Services-Anmelde-Assistenten für IT-Experten RTW](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Installieren Sie das Microsoft Azure AD-Modul für Windows PowerShell mit diesen Schritten:

    - Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten (Ausführung von Windows PowerShell als Administrator).
    - Führen Sie den Befehl **Install-Module MSOnline** aus.
    - Wählen Sie **Y** aus, und drücken Sie die **EINGABETASTE**, wenn Sie zum Installieren des NuGet-Anbieters aufgefordert werden.
    - Wählen Sie **Y**, und drücken Sie die **EINGABETASTE**, wenn Sie zum Installieren des Moduls aus PSGallery aufgefordert werden.

3. Führen Sie die folgenden Cmdlets aus:

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Melden Sie sich mit dem neuen Konto bei Azure an. Ändern Sie das Kennwort, wenn Sie dazu aufgefordert werden.
2. Melden Sie sich mit dem neuen Konto bei `https://portal.local.azurestack.external` an, um das Benutzerportal anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Azure Stack Hub-Benutzern in AD FS](azure-stack-add-users-adfs.md)
