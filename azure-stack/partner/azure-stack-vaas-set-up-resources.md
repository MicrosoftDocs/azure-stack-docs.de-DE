---
title: 'Tutorial: Einrichten von Ressourcen für Validation-as-a-Service | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Ressourcen für Validation-as-a-Service (VaaS) einrichten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 33ead43fd8dbe77feae12db58ca7c689030c6f98
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829103"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Tutorial: Einrichten von Ressourcen für Validation-as-a-Service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Validation-as-a-Service (VaaS) ist ein Azure-Dienst, mit dem Azure Stack-Lösungen auf dem Markt validiert und unterstützt werden. Lesen Sie diesen Artikel, bevor Sie mit dem Dienst Ihre Lösung validieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Vorbereitung auf die Verwendung von VaaS durch Einrichten Ihrer Azure Active Directory-Instanz (AD).
> * Erstellen Sie ein Speicherkonto.

## <a name="configure-an-azure-ad-tenant"></a>Konfigurieren eines Azure AD-Mandanten

Ein Azure AD-Mandant wird dazu verwendet, eine Organisation zu registrieren und Benutzer bei VaaS zu authentifizieren. Der Partner verwendet die Funktionen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) des Mandanten, um die zur Verwendung von VaaS berechtigten Benutzer in der Partnerorganisation zu verwalten. Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Erstellen eines Mandanten

Erstellen Sie einen Mandanten, mit dem Ihre Organisation auf VaaS-Dienste zugreift. Verwenden Sie einen aussagekräftigen Namen, z.B. `ContosoVaaS@onmicrosoft.com`.

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) einen Azure AD-Mandanten, oder verwenden Sie einen vorhandenen Mandanten. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Fügen Sie Mitglieder Ihrer Organisation zum Mandanten hinzu. Diese Benutzer sind für das Anzeigen oder Planen von Tests mithilfe des Diensts verantwortlich. Nach Abschluss der Registrierung legen Sie die Zugriffsebene der Benutzer fest.

    Weisen Sie den Benutzern in Ihrem Mandanten eine der folgenden Rollen zu, um die Ausführung von Aktionen in VaaS für sie zu autorisieren:

    | Rollenname | BESCHREIBUNG |
    |---------------------|------------------------------------------|
    | Owner (Besitzer) | Verfügt über Vollzugriff auf alle Ressourcen. |
    | Leser | Kann alle Ressourcen anzeigen, aber nicht erstellen oder verwalten. |
    | Test Contributor (Testmitwirkender) | Kann Testressourcen erstellen und verwalten. |

    So weisen Sie Rollen in der Anwendung **Azure Stack Validation Service** (Azure Stack-Validierungsdienst) zu:

   1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
   2. Wählen Sie im Abschnitt **Identität** die Optionen **Alle Dienste** > **Azure Active Directory** aus.
   3. Klicken Sie auf **Unternehmensanwendungen** > **Azure Stack Validation Service** (Azure Stack-Validierungsdienst).
   4. Wählen Sie **Benutzer und Gruppen**. Auf dem Blatt **Azure Stack Validation Service– Users and group** (Azure Stack-Validierungsdienst – Benutzer und Gruppe) sind die zur Verwendung der Anwendung berechtigten Benutzer aufgeführt.
   5. Wählen Sie **+ Benutzer hinzufügen**, um einen Benutzer aus Ihrem Mandanten hinzuzufügen und eine Rolle zuzuweisen.

      Wenn Sie VaaS-Ressourcen und Aktionen in verschiedenen Gruppen innerhalb einer Organisation isolieren möchten, können Sie mehrere Azure AD-Mandantenverzeichnisse erstellen.

### <a name="register-your-tenant"></a>Registrieren Ihres Mandanten

Dieser Prozess autorisiert Ihren Mandanten in der Azure AD-Anwendung **Azure Stack Validation Service** (Azure Stack-Validierungsdienst).

1. Senden Sie die folgenden Mandanteninformationen unter [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) an Microsoft.

    | Data | BESCHREIBUNG |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Name der Organisation | Offizieller Organisationsname |
    | Name des Azure AD-Mandantenverzeichnisses | Registrierter Name des Azure AD-Mandantenverzeichnisses |
    | ID des Azure AD-Mandantenverzeichnisses | Dem Verzeichnis zugeordnete GUID des Azure AD-Mandantenverzeichnisses. Informationen dazu, wie Sie die ID Ihres Azure AD-Mandantenverzeichnisses ermitteln, finden Sie unter [Abrufen der Mandanten-ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in). |

2. Warten Sie auf die Bestätigung des Teams für die Azure Stack-Überprüfung, um sicherzugehen, dass Ihr Mandant das VaaS-Portal verwenden kann.

### <a name="consent-to-the-vaas-application"></a>Einwilligung zur VaaS-Anwendung

Gewähren Sie als Azure AD-Administrator im Namen Ihres Mandanten der VaaS-Azure AD-Anwendung die erforderlichen Berechtigungen:

1. Melden Sie sich mit den Anmeldeinformationen eines globalen Administrators für den Mandanten beim [VaaS-Portal](https://azurestackvalidation.com/) an. 

2. Klicken Sie auf **Mein Konto**.

3\. Akzeptieren Sie bei entsprechender Aufforderung die Bedingungen, um VaaS die aufgeführten Azure AD-Berechtigungen zu erteilen.

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Während der Testausführung gibt VaaS Diagnoseprotokolle an ein Azure Storage-Konto aus. Das Speicherkonto kann nicht nur für Testprotokolle, sondern auch zum Hochladen der OEM-Erweiterungspakete für den Workflow für die Paketvalidierung verwendet werden.

Das Azure Storage-Konto wird in der öffentlichen Azure-Cloud und nicht in Ihrer Azure Stack-Umgebung gehostet.

1. Wählen Sie im Azure-Portal die Optionen **Alle Dienste** > **Storage** > **Speicherkonten** aus. Wählen Sie auf dem Blatt **Speicherkonten** die Option **Hinzufügen** aus.

2. Wählen Sie das Abonnement aus, in dem das Speicherkonto erstellt werden soll.

3. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus. Geben Sie einen Namen für die neue Ressourcengruppe ein.

4. Informieren Sie sich über die [Benennungskonventionen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#storage) für Azure Storage-Konten. Geben Sie einen Namen für Ihr Speicherkonto ein.

5. Wählen Sie die Region **USA, Westen** für Ihr Speicherkonto aus.

    Damit für die Speicherung von Protokollen keine Netzwerkgebühren anfallen, kann das Azure Storage-Konto für die ausschließliche Nutzung der Region **USA, Westen** konfiguriert werden. Die Features für die Datenreplikation und die heiße Speicherebene sind für diese Daten nicht erforderlich. Wenn Sie eines dieser Features aktivieren, erhöhen sich die Kosten erheblich.

6. Übernehmen Sie mit Ausnahme von **Kontoart** für diese Einstellungen die Standardwerte:

    - Das Feld **Bereitstellungsmodell** ist standardmäßig auf **Resource Manager** festgelegt.
    - Das Feld **Leistung** ist standardmäßig auf **Standard** festgelegt.
    - Wählen Sie für **Kontoart** die Option **Blob Storage**.
    - Das Feld **Replikation** ist standardmäßig auf **Lokal redundanter Speicher (LRS)** festgelegt.
    - Das Feld **Zugriffsebene** ist standardmäßig auf **Heiß** festgelegt.

7. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihre Umgebung keine eingehenden Verbindungen zulässt, führen Sie die Schritte im Tutorial zum Bereitstellen des lokalen Agents zum Ausführen eines Tests für Ihre Hardware aus.

> [!div class="nextstepaction"]
> [Deploy the local agent](azure-stack-vaas-local-agent.md) (Bereitstellen des lokalen Agents)
