---
title: Hinzufügen neuer Azure Stack-Mandantenkonten in Azure Active Directory | Microsoft-Dokumentation
description: Sie müssen nach dem Bereitstellen des Microsoft Azure Stack Development Kits mindestens ein Mandantenbenutzerkonto erstellen, damit Sie das Mandantenportal durchsuchen können.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 52fb4074a476cb907f02628933b83d82b6ee4984
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985758"
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
> Wenn Sie ein Microsoft-Konto (Live ID) zum Bereitstellen des Azure Stack Development Kits verwenden, können Sie ein Mandantenkonto nicht mithilfe von AAD PowerShell erstellen. 

1. Installieren Sie den [Microsoft Online Services-Anmelde-Assistenten für IT-Experten RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installieren Sie das [Azure Active Directory-Modul für Windows PowerShell (64-Bit-Version)](https://go.microsoft.com/fwlink/p/?linkid=236297), und öffnen Sie es.
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
