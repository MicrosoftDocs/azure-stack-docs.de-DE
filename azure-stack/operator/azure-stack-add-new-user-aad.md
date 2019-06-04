---
title: Hinzufügen neuer Azure Stack-Mandantenkonten in Azure Active Directory | Microsoft-Dokumentation
description: Sie müssen nach dem Bereitstellen des Microsoft Azure Stack Development Kits mindestens ein Mandantenbenutzerkonto erstellen, damit Sie das Mandantenportal durchsuchen können.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 83a927cd0198798b4b1beaf8b84ed0b7ad2a11a0
ms.sourcegitcommit: 914daff43ae0f0fc6673a06dfe2d42d9b4fbab48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66043112"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Hinzufügen neuer Azure Stack-Mandantenkonten in Azure Active Directory

Nach dem [Bereitstellen des Azure Stack Development Kits](../asdk/asdk-install.md) benötigen Sie ein Mandantenbenutzerkonto, damit Sie das Portal durchsuchen und Ihre Angebote und Pläne testen können. Sie können ein Mandantenkonto über das [Azure-Portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) oder mithilfe von PowerShell erstellen.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Erstellen eines Azure Stack-Mandantenkontos mithilfe des Azure-Portals

Sie benötigen ein Azure-Abonnement, um das Azure-Portal verwenden zu können.

1. Melden Sie sich bei [Azure](https://portal.azure.com) an.
2. Wählen Sie in der linken Navigationsleiste **Active Directory** aus, und wechseln Sie in das Verzeichnis, das Sie für Azure Stack verwenden möchten, oder erstellen Sie ein neues Verzeichnis.
3. Wählen Sie **Azure Active Directory** > **Benutzer** > **Neuer Benutzer** aus.

    ![Benutzer: Seite „Alle Benutzer“ mit Hervorhebung von „Neuer Benutzer“](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Geben Sie auf der Seite **Benutzer** die erforderlichen Informationen ein.

    ![„Neuen Benutzer hinzufügen“, Seite „Benutzer“ mit Benutzerinformationen](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Name (erforderlich).** Der Vor- und Nachname des neuen Benutzers. Beispielsweise Mary Parker.
   - **Benutzername (erforderlich).** Der Benutzername des neuen Benutzers. Beispiel: mary@contoso.com.
       Der Domänenteil des Benutzernamens muss entweder der anfängliche Standarddomänenname sein, „<_IhrDomänenname_>.onmicrosoft.com“, oder ein benutzerdefinierter Domänennamen, z. B. „contoso.com“. Weitere Informationen zum Erstellen eines benutzerdefinierten Domänennamens finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory]((/azure/active-directory/fundamentals/add-custom-domain).
   - **Profil.** Optional können Sie weitere Informationen zum Benutzer hinzufügen. Sie können Benutzerinformationen auch zu einem späteren Zeitpunkt hinzufügen. Weitere Informationen zum Hinzufügen von Benutzerinformationen finden Sie unter [Hinzufügen oder Ändern von Benutzerprofilinformationen]((/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Verzeichnisrolle.**  Wählen Sie **Benutzer** aus.

5. Aktivieren Sie **Kennwort anzeigen**, und kopieren Sie das automatisch generierte Kennwort, das im Feld **Kennwort** angegeben wird. Sie benötigen dieses Kennwort bei der ersten Anmeldung.

6. Klicken Sie auf **Erstellen**.

    Der Benutzer wird erstellt und Ihrem Azure AD-Mandanten hinzugefügt.

7. Melden Sie sich mit dem neuen Konto am Microsoft Azure-Portal an. Ändern Sie das Kennwort, wenn Sie dazu aufgefordert werden.
8. Melden Sie sich bei `https://portal.local.azurestack.external` mit dem neuen Konto an, um das Mandantenportal anzuzeigen.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Erstellen eines Azure Stack-Benutzerkontos mithilfe von PowerShell

Falls Sie kein Azure-Abonnement besitzen, können Sie ein Mandantenbenutzerkonto nicht mithilfe des Azure-Portals hinzufügen. In diesem Fall können Sie stattdessen das Azure Active Directory-Modul für Windows PowerShell verwenden.

> [!NOTE]
> Wenn Sie ein Microsoft-Konto zum Bereitstellen des Azure Stack Development Kits verwenden, können Sie ein Mandantenkonto nicht mithilfe von Azure AD PowerShell erstellen. 

1. Installieren Sie die **64-Bit**-Version des [Microsoft Online Services-Anmelde-Assistenten für IT-Experten RTW](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Installieren Sie das Microsoft Azure Active Directory-Modul für Windows PowerShell mit diesen Schritten:

    - Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten (Ausführung von Windows PowerShell als Administrator).
    - Führen Sie den Befehl **Install-Module MSOnline** aus.
    - Wählen Sie **Y**, und drücken Sie die **EINGABETASTE**, wenn Sie zum Installieren des NuGet-Anbieters aufgefordert werden.
    - Wählen Sie **Y**, und drücken Sie die **EINGABETASTE**, wenn Sie zum Installieren des Moduls aus PSGallery aufgefordert werden.

3. Führen Sie die folgenden Cmdlets aus:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Melden Sie sich mit dem neuen Konto bei Microsoft Azure an. Ändern Sie das Kennwort, wenn Sie dazu aufgefordert werden.
2. Melden Sie sich bei `https://portal.local.azurestack.external` mit dem neuen Konto an, um das Mandantenportal anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Azure Stack-Benutzern in AD FS](azure-stack-add-users-adfs.md)
