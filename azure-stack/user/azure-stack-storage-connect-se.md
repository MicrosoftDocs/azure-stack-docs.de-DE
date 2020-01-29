---
title: Verbinden des Storage-Explorers mit einem Azure Stack Hub-Abonnement oder -Speicherkonto | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Verbindung zwischen dem Storage-Explorer und einem Azure Stack Hub-Abonnement herstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 7c01e509dc77e2a036ad640ee5376ffda37f903f
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259833"
---
# <a name="connect-storage-explorer-to-an-azure-stack-hub-subscription-or-a-storage-account"></a>Herstellen einer Verbindung zwischen dem Storage-Explorer und einem Azure Stack Hub-Abonnement oder -Speicherkonto

In diesem Artikel erfahren Sie, wie Sie über den [Azure Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) eine Verbindung mit Ihren Azure Stack Hub-Abonnements und -Speicherkonten herstellen. Der Storage-Explorer ist eine eigenständige App, über die Sie unter Windows, macOS und Linux komfortabel mit Azure Stack Hub-Speicherdaten arbeiten können.

> [!NOTE]  
> Es stehen mehrere Tools zum Verschieben von Daten in und aus Azure Stack Hub-Speicher zur Verfügung. Weitere Informationen finden Sie unter [Datenübertragungstools für Azure Stack Hub-Speicher](azure-stack-storage-transfer.md).

[Laden Sie den Storage-Explorer herunter](https://www.storageexplorer.com/), und installieren Sie ihn, sofern er noch nicht installiert ist.

Nachdem Sie eine Verbindung mit einem Azure Stack Hub-Abonnement oder -Speicherkonto hergestellt haben, können Sie wie in den [Artikeln zum Azure Storage-Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) beschrieben mit Ihren Azure Stack Hub-Daten arbeiten. 

## <a name="prepare-for-connecting-to-azure-stack-hub"></a>Vorbereiten für die Verbindung mit Azure Stack Hub

Damit der Storage-Explorer auf das Azure Stack Hub-Abonnement zugreifen kann, benötigen Sie direkten Zugriff auf Azure Stack Hub oder eine VPN-Verbindung. Weitere Informationen zur Einrichtung einer VPN-Verbindung mit Azure Stack Hub finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub per VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

> [!Note]  
> Verwenden Sie für das ASDK nicht das Stammzertifikat (CA.cer), das während des Setupprozesses erstellt wurde, wenn Sie über ein VPN eine Verbindung mit dem ASDK herstellen.  Dies ist ein DER-codiertes (Distinguished Encoding Rules) Zertifikat, das es dem Storage-Explorer nicht erlaubt, Ihre Azure Stack Hub-Abonnements abzurufen. Gehen Sie wie folgt vor, um ein Base64-codiertes Zertifikat zur Verwendung mit Storage-Explorer zu exportieren.

Für nicht verbundene integrierte Systeme und für das ASDK empfiehlt es sich, eine interne Unternehmenszertifizierungsstelle zu verwenden, um das Stammzertifikat im Base64-Format zu exportieren und anschließend in Azure Storage-Explorer zu importieren.  

### <a name="export-and-then-import-the-azure-stack-hub-certificate"></a>Exportieren und anschließendes Importieren des Azure Stack Hub-Zertifikats

Exportieren und importieren Sie das Azure Stack Hub-Zertifikat für nicht verbundene integrierte Systeme und für das ASDK. Bei verbundenen integrierten Systemen ist das Zertifikat öffentlich signiert, sodass dieser Schritt nicht erforderlich ist.

1. Öffnen Sie `mmc.exe` auf einem Azure Stack Hub-Hostcomputer oder einem lokalen Computer, der über eine VPN-Verbindung mit Azure Stack Hub verfügt. 

2. Wählen Sie unter **Datei** die Option **Snap-In hinzufügen/entfernen** aus. Wählen Sie **Zertifikate** unter „Verfügbare Snap-Ins“ aus. 

3. Wählen Sie **Computerkonto** und dann **Weiter** aus. Wählen Sie **Lokaler Computer** aus, und wählen Sie dann **Fertig stellen** aus.

4.  Suchen Sie unter **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** (Konsolenstamm\Zertifiziert (Lokaler Computer)\Vertrauenswürdige Stammzertifizierungsstellen\Zertifikate) nach **AzureStackSelfSignedRootCert**.

    ![Laden des Azure Stack Hub-Stammzertifikats per „mmc.exe“](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Klicken Sie mit der rechten Maustaste auf das Zertifikat, wählen Sie **Alle Aufgaben** > **Exportieren** aus, und führen Sie dann die Anweisungen aus, um das Zertifikat mit **Base-64-codiert X.509 (.CER)** zu exportieren.

    Das exportierte Zertifikat wird im nächsten Schritt verwendet.

6. Starten Sie den Storage-Explorer. Wenn das Dialogfeld **Verbindung mit Azure-Speicher herstellen** angezeigt wird, brechen Sie den Vorgang ab.

7. Wählen Sie im Menü **Bearbeiten** die Option **SSL-Zertifikate** und dann **Zertifikate importieren** aus. Suchen Sie im Dialogfeld für die Dateiauswahl nach dem Zertifikat, das Sie im vorherigen Schritt exportiert haben, und öffnen Sie es.

    Nach dem Importieren des Zertifikats werden Sie zum Neustarten des Storage-Explorers aufgefordert.

    ![Importieren des Zertifikats in den Storage-Explorer](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Wählen Sie nach dem Neustart des Storage-Explorers das Menü **Bearbeiten** aus, und vergewissern Sie sich, dass die Option **Azure Stack Hub-APIs als Ziel** aktiviert ist. Wenn dies nicht der Fall ist, aktivieren Sie **Azure Stack Hub als Ziel**, und starten Sie den Storage-Explorer anschließend neu, damit die Änderung wirksam wird. Diese Konfiguration ist erforderlich, um die Kompatibilität mit Ihrer Azure Stack Hub-Umgebung zu gewährleisten.

    ![Sicherstellen der Aktivierung von „Azure Stack Hub als Ziel“](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-azure-ad"></a>Herstellen einer Verbindung mit einem Azure Stack Hub-Abonnement mit Azure AD

Gehen Sie wie folgt vor, um den Storage-Explorer mit einem Azure Stack Hub-Abonnement zu verbinden, das zu einem Azure AD-Konto (Azure Active Directory) gehört.

1. Wählen Sie im linken Bereich des Storage-Explorers **Konten verwalten** aus.  
    Alle von Ihnen angemeldeten Microsoft-Abonnements werden angezeigt.

2. Wählen Sie zum Herstellen einer Verbindung mit dem Azure Stack Hub-Abonnement die Option **Konto hinzufügen** aus.

    ![Hinzufügen eines Azure Stack Hub-Kontos](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Wählen Sie im Dialogfeld „Verbindung mit Azure Storage herstellen“ unter **Azure-Umgebung** die Option **Azure**, **Azure China 21Vianet**, **Azure Deutschland**, **Azure US-Regierung** oder **Neue Umgebung hinzufügen** aus. Die Auswahl hängt vom verwendeten Azure Stack Hub-Konto ab. Klicken Sie auf **Anmelden**, um sich mit dem Azure Stack Hub-Konto anzumelden, das mindestens einem aktiven Azure Stack Hub-Abonnement zugeordnet ist.

    ![Herstellen einer Verbindung mit Azure-Speicher](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Nach erfolgreicher Anmeldung mit einem Azure Stack Hub-Konto werden im linken Bereich die Azure Stack Hub-Abonnements angezeigt, die dem Konto zugeordnet sind. Wählen Sie die Azure Stack Hub-Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **Übernehmen**. (Durch Aktivieren oder Deaktivieren des Kontrollkästchens **Alle Abonnements** können Sie alle bzw. keine Azure Stack Hub-Abonnements in der Liste auswählen.)

    ![Auswählen der Azure Stack Hub-Abonnements nach dem Ausfüllen der Felder im Dialogfeld für die benutzerdefinierte Cloudumgebung](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    Im linken Bereich werden die Speicherkonten angezeigt, die den ausgewählten Azure Stack Hub-Abonnements zugeordnet sind.

    ![Liste mit Speicherkonten, einschließlich Azure Stack Hub-Abonnementkonten](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-ad-fs-account"></a>Herstellen einer Verbindung mit einem Azure Stack Hub-Abonnement mit einem AD FS-Konto

> [!Note]  
> Der Bereich für die AD FS-Anmeldung (Active Directory-Verbunddienste) unterstützt Storage-Explorer 1.2.0 oder neuere Versionen mit Azure Stack Hub 1804 oder einem neueren Update.
Gehen Sie wie folgt vor, um den Storage-Explorer mit einem Azure Stack Hub-Abonnement zu verbinden, das zu einem AD FS-Konto gehört.

1. Wählen Sie **Konten verwalten**. Im Explorer werden die Microsoft-Abonnements aufgeführt, an denen Sie sich angemeldet haben.
2. Wählen Sie zum Herstellen einer Verbindung mit dem Azure Stack Hub-Abonnement die Option **Konto hinzufügen** aus.

    ![Hinzufügen eines Kontos: Storage-Explorer](media/azure-stack-storage-connect-se/add-an-account.png)

3. Wählen Sie **Weiter** aus. Wählen Sie im Dialogfeld „Verbindung mit Azure-Speicher herstellen“ unter **Azure-Umgebung** die Option **Use Custom Environment** (Benutzerdefinierte Umgebung verwenden), und klicken Sie anschließend auf **Weiter**.

    ![Verbinden mit Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Geben Sie die erforderlichen Informationen zur benutzerdefinierten Azure Stack Hub-Umgebung ein. 

    | Feld | Notizen |
    | ---   | ---   |
    | Umgebungsname | Dieses Feld kann vom Benutzer angepasst werden. |
    | Azure Resource Manager-Endpunkt | Die Beispiele von Azure Resource Manager-Ressourcenendpunkten des Azure Stack Development Kit.<br>Für Bediener: https://adminmanagement.local.azurestack.external <br> Für Benutzer: https://management.local.azurestack.external |

    Wenn Sie in einem integrierten Azure Stack Hub-System arbeiten und den Verwaltungsendpunkt nicht kennen, wenden Sie sich an den zuständigen Operator.

    ![Hinzufügen eines Kontos: benutzerdefinierte Umgebungen](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Klicken Sie auf **Anmelden**, um eine Verbindung mit dem Azure Stack Hub-Konto herzustellen, das mindestens einem aktiven Azure Stack Hub-Abonnement zugeordnet ist.



6. Wählen Sie die Azure Stack Hub-Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **Übernehmen**.

    ![Kontenverwaltung](./media/azure-stack-storage-connect-se/account-management.png)

    Im linken Bereich werden die Speicherkonten angezeigt, die den ausgewählten Azure Stack Hub-Abonnements zugeordnet sind.

    ![Liste mit den zugeordneten Abonnements](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-hub-storage-account"></a>Herstellen einer Verbindung mit einem Azure Stack Hub-Speicherkonto

Sie können eine Verbindung mit einem Azure Stack Hub-Speicherkonto auch herstellen, indem Sie den Namen und das Schlüsselpaar des Speicherkontos verwenden.

1. Wählen Sie im linken Bereich des Storage-Explorers „Konten verwalten“ aus. Alle Microsoft-Konten, bei denen Sie angemeldet sind, werden angezeigt.

    ![Hinzufügen eines Kontos: Storage-Explorer](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Wählen Sie zum Herstellen einer Verbindung mit dem Azure Stack Hub-Abonnement die Option **Konto hinzufügen** aus.

    ![Hinzufügen eines Kontos: Verbinden mit Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Wählen Sie im Fenster „Verbindung mit Azure-Speicher herstellen“ die Option **Speicherkontoname und -schlüssel verwenden**.

4. Geben Sie Ihren Kontonamen in das Feld **Kontoname** ein, und fügen Sie den Kontoschlüssel in das Textfeld **Kontoschlüssel** ein. Wählen Sie dann unter **Domäne der Speicherendpunkte** die Option **Andere (unten eingeben)** aus, und geben Sie den Azure Stack Hub-Endpunkt ein.

    Ein Azure Stack Hub-Endpunkt besteht aus zwei Teilen: dem Namen einer Region und der Azure Stack Hub-Domäne. Im Azure Stack Development Kit ist der Standardendpunkt **local.azurestack.external**. Wenden Sie sich an den Cloudadministrator, falls Sie nicht sicher sind, wie Ihr Endpunkt lautet.

    ![Anfügen von Name und Schlüssel](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Wählen Sie **Verbinden**.
6. Nachdem das Speicherkonto erfolgreich angefügt wurde, wird es angezeigt. (Dabei wird **(Extern, Anderes)** an den Speicherkontonamen angehängt.)

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit dem Storage-Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack Hub-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md)
* Weitere Informationen zu Azure Storage finden Sie unter [Einführung in Microsoft Azure Storage](/azure/storage/common/storage-introduction).
