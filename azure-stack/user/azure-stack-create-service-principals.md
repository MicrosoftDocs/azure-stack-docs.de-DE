---
title: Erstellen eines Dienstprinzipals für Azure Stack | Microsoft-Dokumentation
description: Beschreibt das Erstellen eines Dienstprinzipals, der mit der rollenbasierten Zugriffssteuerung in Azure Resource Manager zum Verwalten des Zugriffs auf Ressourcen verwendet werden kann.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3860f8dcc99f3505fe2f838e75f5e8f09b78aefe
ms.sourcegitcommit: 8cb2b567e9914d4d07e754d95c0864aa55868579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65855377"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Gewähren des Anwendungszugriffs auf Azure Stack-Ressourcen durch Erstellen von Dienstprinzipalen

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einer Anwendung Zugriff auf Azure Stack-Ressourcen gewähren, indem Sie einen Dienstprinzipal erstellen, der Azure Resource Manager verwendet. Mit einem Dienstprinzipal können Sie mithilfe der [rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md) bestimmte Berechtigungen delegieren.

Es wird empfohlen, Dienstprinzipale für Ihre Anwendungen zu verwenden. Der Ansatz mit Dienstprinzipalen ist dem Ausführen einer App mit Ihren Anmeldeinformationen aus folgenden Gründen vorzuziehen:

* Sie können dem Dienstprinzipal Berechtigungen zuweisen, die sich von Ihren eigenen Kontoberechtigungen unterscheiden. In der Regel sind die Berechtigungen eines Dienstprinzipals genau auf die Aufgaben der App beschränkt.
* Sie müssen keine Anmeldeinformationen für die App ändern, wenn sich Ihre Rolle oder Zuständigkeiten ändern.
* Sie können ein Zertifikat verwenden, um die Authentifizierung beim Ausführen eines unbeaufsichtigten Skripts zu automatisieren.

## <a name="example-scenario"></a>Beispielszenario

Sie verfügen über eine App für die Konfigurationsverwaltung, die Azure-Ressourcen mithilfe von Azure Resource Manager inventarisieren muss. Sie können einen Dienstprinzipal erstellen und ihm die Rolle „Leser“ zuweisen. Mit dieser Rolle erhält die App schreibgeschützten Zugriff auf Azure-Ressourcen.

## <a name="getting-started"></a>Erste Schritte

Führen Sie die Schritte in diesem Artikel für folgende Aufgaben aus:

* Erstellen eines Dienstprinzipals für Ihre App
* Registrieren Ihrer App und Erstellen eines Authentifizierungsschlüssels
* Zuweisen Ihrer App zu einer Rolle

Die Konfiguration von Active Directory für Azure Stack bestimmt die Vorgehensweise zur Erstellung des Dienstprinzipals. Wählen Sie eine der folgenden Optionen:

* Erstellen eines Dienstprinzipals für [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad)
* Erstellen eines Dienstprinzipals für [Active Directory-Verbunddienste (Azure Directory Federation Services, AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs)

Die Schritte zum Zuweisen eines Dienstprinzipals zu einer Rolle sind für Azure AD und AD FS identisch. Nach der Erstellung des Dienstprinzipals können Sie durch Zuweisung zu einer Rolle [Berechtigungen delegieren](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-azure-ad"></a>Erstellen des Dienstprinzipals für Azure AD

Wenn Azure Stack Azure AD als Identitätsspeicher nutzt, können Sie mit dem Azure-Portal anhand der gleichen Schritte wie in Azure einen Dienstprinzipal erstellen.

> [!NOTE]
> Stellen Sie vor dem Erstellen eines Dienstprinzipals sicher, dass Sie über die [erforderlichen Azure AD-Berechtigungen](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) verfügen.

### <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

So erstellen Sie einen Dienstprinzipal für Ihre Anwendung

1. Melden Sie sich über das [Azure-Portal](https://portal.azure.com) bei Ihrem Azure-Konto an.
2. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.
3. Geben Sie einen Namen ein.
4. Wählen Sie die **unterstützten Kontotypen** aus.
5.  Fügen Sie einen URI für die Anwendung hinzu. Wählen Sie als Typ für die zu erstellende Anwendung **Web** aus. Wählen Sie nach dem Festlegen der Werte **Registrieren** aus.

Sie haben einen Dienstprinzipal für Ihre Anwendung erstellt.

### <a name="get-credentials"></a>Abrufen von Anmeldeinformationen

Beim programmgesteuerten Anmelden verwenden Sie die ID für Ihre Anwendung und einen Authentifizierungsschlüssel. Diese Werte erhalten Sie wie folgt:

1. Wählen Sie in Active Directory unter **App Registrierungen** Ihre Anwendung aus.

2. Kopieren Sie die **Anwendungs-ID**, und speichern Sie sie in Ihrem Anwendungscode. In den Beispielanwendungen wird für die **Anwendungs-ID** der Begriff **Client-ID** verwendet.

     ![Anwendungs-ID für die Anwendung](./media/azure-stack-create-service-principals/image12.png)
3. Klicken Sie zum Generieren eines Authentifizierungsschlüssels auf **Zertifikate und geheime Schlüssel**.

4. Wählen Sie **Neuer geheimer Clientschlüssel**.

5. Geben Sie eine Beschreibung des Schlüssels ein, wählen Sie eine Dauer für den Schlüssel aus, und wählen Sie dann **Hinzufügen** aus. 

6. Abschließend wird der Wert des geheimen Schlüssels angezeigt. Notieren Sie diesen Wert, da Sie ihn später nicht mehr abrufen können. Speichern Sie die Schlüsselwert an einem Ort, von dem Ihre Anwendung ihn abrufen kann.

![Schlüsselwertwarnung für den gespeicherten Schlüssel](./media/azure-stack-create-service-principals/image15.png)

Der letzte Schritt besteht im [Zuweisen einer Rolle zu Ihrer Anwendung](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Erstellen eines Dienstprinzipals für AD FS

Wenn Sie Azure Stack mit AD FS als Identitätsspeicher bereitgestellt haben, können Sie PowerShell für die folgenden Aufgaben verwenden:

* Erstellen eines Dienstprinzipals
* Zuweisen eines Dienstprinzipals zu einer Rolle
* Anmelden mithilfe der Identität des Dienstprinzipals

Einzelheiten zur Erstellung des Dienstprinzipals finden Sie unter [Erstellen eines Dienstprinzipals für AD FS](../operator/azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Zuweisen des Dienstprinzipals zu einer Rolle

Um auf Ressourcen in Ihrem Abonnement zuzugreifen, müssen Sie die Anwendung einer Rolle zuweisen. Entscheiden Sie, welche Rolle die geeigneten Berechtigungen für die Anwendung darstellt. Informationen zu verfügbaren Rollen finden Sie unter [RBAC: Integrierte Rollen](/azure/role-based-access-control/built-in-roles).

> [!NOTE]
> Sie können den Bereich der Rolle auf Abonnement-, Ressourcengruppen- oder Ressourcenebene festlegen. Berechtigungen werden von niedrigeren Ebenen mit geringerem Umfang geerbt. Beispiel: Eine App mit der Rolle „Leser“ für eine Ressourcengruppe kann alle in einer Ressourcengruppe enthaltenen Ressourcen lesen.

Verwenden Sie die folgenden Schritte als Leitfaden für das Zuweisen einer Rolle zu einem Dienstprinzipal.

1. Navigieren Sie im Azure Stack-Portal zur Bereichsebene, der Sie die Anwendung zuweisen möchten. Um z.B. einer Gruppe im Abonnementkontext eine Rolle zuzuweisen, wählen Sie **Abonnements** aus.

2. Wählen Sie das Abonnement aus, dem die Anwendung zugewiesen werden soll. In diesem Beispiel wird das Abonnement für Visual Studio Enterprise verwendet.

     ![Auswählen des Visual Studio Enterprise-Abonnements für die Zuweisung](./media/azure-stack-create-service-principals/image16.png)

3. Wählen Sie für das Abonnement die Option **Zugriffssteuerung (IAM)**.

4. Wählen Sie **Rollenzuweisung hinzufügen** aus.

5. Wählen Sie die Rolle aus, die Sie der Anwendung zuweisen möchten.

6. Suchen Sie nach Ihrer Anwendung, und wählen Sie sie aus.

7. Wählen Sie **OK** aus, um das Zuweisen der Rolle abzuschließen. Ihre Anwendung wird in der Liste der Benutzer angezeigt, die einer Rolle für diesen Bereich zugewiesen sind.

Nachdem Sie nun einen Dienstprinzipal erstellt und eine Rolle zugewiesen haben, kann Ihre Anwendung auf Azure Stack-Ressourcen zugreifen.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md)
