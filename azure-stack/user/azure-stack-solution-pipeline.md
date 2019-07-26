---
title: Bereitstellen von Apps in Azure und Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Bereitstellen von Apps in Azure und Azure Stack mit einer hybriden CI/CD-Pipeline.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 03/11/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 9fbadb923452fc2420d1f8626a69d377c4d72e12
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286963"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Bereitstellen von Apps in Azure und Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Hier erfahren Sie, wie Sie mithilfe einer hybriden Pipeline für Continuous Integration/Continuous Delivery (CI/CD) Apps in Azure und Azure Stack bereitstellen.

In dieser Lösung erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> * Initiieren Sie einen neuen Build basierend auf Codecommits für Ihr Azure DevOps Services-Repository.
> * Automatisches Bereitstellen Ihrer App in der globalen Azure-Instanz für Benutzerakzeptanztests
> * Sobald Ihr Code den Test bestanden hat, stellen Sie die App automatisch in Azure Stack bereit.

## <a name="benefits-of-the-hybrid-delivery-build-pipeline"></a>Vorteile der Hybrid Delivery-Buildpipeline

Kontinuität, Sicherheit und Zuverlässigkeit sind wichtige Elemente der App-Bereitstellung. Diese Punkte sind für Ihre Organisation und Ihr Entwicklungsteam unverzichtbar. Mit einer hybriden CI/CD-Pipeline können Sie Ihre Buildpipelines für Ihre lokale Umgebung und für die öffentliche Cloud konsolidieren. Mit einem Hybrid Delivery-Modell können Sie auch die Entwicklungsorte wechseln, ohne dabei die App wechseln zu müssen.

Weitere Vorteile bei Verwendung des Hybridansatzes:

* Sie können einen konsistenten Satz von Entwicklungstools für Ihre lokale Azure Stack-Umgebung und die öffentliche Azure-Cloud nutzen.  Ein gängiges Toolset erleichtert die Implementierung von CI/CD-Mustern und -Methoden.
* Apps und Dienste, die in Azure und Azure Stack bereitgestellt werden, sind austauschbar, und der gleiche Code kann an jedem Standort ausgeführt werden. Sie können die Features und Funktionen der lokalen Umgebung und der öffentlichen Cloud nutzen.

Weitere Informationen zu CI und CD:

* [What is Continuous Integration?](https://www.visualstudio.com/learn/what-is-continuous-integration/) (Was ist Continuous Integration?)
* [What is Continuous Delivery?](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (Was ist Continuous Delivery?)

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Artikel [Entwurfsüberlegungen für Hybridanwendungen](azure-stack-edge-pattern-overview.md) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybrid-Apps, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.

## <a name="prerequisites"></a>Voraussetzungen

Für die Erstellung einer hybriden CI/CD-Pipeline sind einige Komponenten erforderlich. Folgende Komponenten benötigen eine gewisse Vorbereitungszeit:

* Ein Azure-OEM/-Hardwarepartner kann eine Azure Stack-Produktionsumgebung bereitstellen. Das ASDK (Azure Stack Development Kit) kann von allen Benutzern bereitgestellt werden.
* Ein Azure Stack-Bediener muss die folgenden Schritte ausführen: App Service bereitstellen, Pläne und Angebote sowie ein Mandantenabonnement erstellen und das Windows Server 2016-Image hinzufügen.

>[!NOTE]
>Wenn Sie bereits einige dieser Komponenten bereitgestellt haben, sollten Sie vor Beginn dieser Lösung sicherstellen, dass diese alle Anforderungen erfüllen.

In dieser Lösung wird davon ausgegangen, dass Sie bereits über Grundkenntnisse in Bezug auf Azure und Azure Stack verfügen. Lesen Sie die folgenden Artikel, um vor dem Starten der Lösung weitere Informationen zu erhalten:

* [Einführung in Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure Stack Key Concepts (Wichtige Begriffe für Azure Stack)](../operator/azure-stack-overview.md)

### <a name="azure-requirements"></a>Anforderungen für Azure

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* Erstellen Sie eine [Web-App](https://docs.microsoft.com/azure/app-service/overview) in Azure. Notieren Sie sich die Web-App-URL, die Sie in der Lösung benötigen.

### <a name="azure-stack-requirements"></a>Anforderungen für Azure Stack

* Verwenden Sie ein integriertes Azure Stack-System, oder stellen Sie das Azure Stack Development Kit (ASDK) bereit. So stellen Sie das ASDK bereit
  * Die [Lösung: Installieren des Azure Stack Development Kits (ASDK)](../asdk/asdk-install.md) enthält ausführliche Informationen zur Bereitstellung.
  * Mit dem PowerShell-Skript [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) lassen sich die Schritte nach der ASDK-Bereitstellung automatisieren.

    > [!Note]
    > Der ASDK-Installationsvorgang dauert etwa sieben Stunden. Berücksichtigen Sie dies bei Ihrer Planung.

  * Stellen Sie PaaS-Dienste als [App Service](../operator/azure-stack-app-service-deploy.md) für Azure Stack bereit.
  * Erstellen Sie [Pläne/Angebote](../operator/azure-stack-plan-offer-quota-overview.md) in Azure Stack.
  * Erstellen Sie ein [Mandantenabonnement](../operator/azure-stack-subscribe-plan-provision-vm.md) in Azure Stack.
  * Erstellen Sie eine Web-App im Mandantenabonnement. Notieren Sie sich die URL der neuen Web-App zur späteren Verwendung.
  * Stellen Sie einen virtuellen Windows Server 2012-Computer im Mandantenabonnement bereit. Sie verwenden diesen Server als Buildserver und für die Ausführung von Azure DevOps Services.
* Stellen Sie ein Windows Server 2016-Image mit .NET 3.5 für einen virtuellen Computer bereit. Dieser virtuelle Computer wird in Ihrer Azure Stack-Instanz als privater Build-Agent erstellt.

### <a name="developer-tool-requirements"></a>Anforderung an Entwicklertools

* Erstellen Sie einen [Azure DevOps Services-Arbeitsbereich](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). Bei der Anmeldung wird ein Projekt namens **MyFirstProject** erstellt.
* [Installieren Sie Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio), und [melden Sie sich bei Azure DevOps Services an](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Stellen Sie eine Verbindung mit Ihrem Projekt her, und [klonen Sie es lokal](https://www.visualstudio.com/docs/git/gitquickstart).

  > [!Note]
  > Für die Ausführung von Windows Server und SQL Server benötigen Sie eine Azure Stack-Umgebung mit den passenden syndizierten Images. Darüber hinaus muss App Service bereitgestellt sein.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Vorbereiten des privaten Azure Pipelines-Agents für die Azure DevOps Services-Integration

### <a name="prerequisites"></a>Voraussetzungen

Azure DevOps Services führt die Authentifizierung gegenüber Azure Resource Manager mit einem Dienstprinzipal durch. Azure DevOps Services muss die Rolle **Mitwirkender** besitzen, um Ressourcen in einem Azure Stack-Abonnement bereitstellen zu können.

Zum Konfigurieren der Authentifizierung müssen die folgenden Schritte ausgeführt werden:

1. Erstellen Sie einen Dienstprinzipal, oder verwenden Sie einen vorhandenen.
2. Erstellen Sie Authentifizierungsschlüssel für den Dienstprinzipal.
3. Überprüfen Sie das Azure Stack-Abonnement über die rollenbasierte Zugriffssteuerung, damit der Dienstprinzipalname (Service Principal Name, SPN) Teil der Rolle „Mitwirkender“ werden kann.
4. Erstellen Sie unter Verwendung der Azure Stack-Endpunkte und der SPN-Informationen eine neue Dienstdefinition in Azure DevOps Services.

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Eine Anleitung zum Erstellen eines Dienstprinzipals finden Sie im Artikel zur [Erstellung eines Dienstprinzipals](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Wählen Sie **Web-App/API** als Anwendungstyp aus, oder [verwenden Sie das PowerShell-Skript](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) wie unter [Create an Azure Resource Manager service connection with an existing service principal](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) (Erstellen einer Azure Resource Manager-Dienstverbindung mit einem vorhandenen Dienstprinzipal) beschrieben.

 > [!Note]  
 > Wenn Sie das Skript zum Erstellen eines Azure Resource Manager-Endpunkts für Azure Stack verwenden, müssen Sie die Parameter **-azureStackManagementURL** und **-environmentName** übergeben. Beispiel:  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Erstellen eines Zugriffsschlüssels

Ein Dienstprinzipal erfordert einen Schlüssel für die Authentifizierung. Führen Sie zum Generieren eines Schlüssels die folgenden Schritte aus:

1. Wählen Sie in Azure Active Directory unter **App-Registrierungen** Ihre App aus.

    ![Auswählen der Anwendung: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_01.png)

2. Notieren Sie den Wert von **Anwendungs-ID**. Sie benötigen diesen Wert beim Konfigurieren des Dienstendpunkts in Azure DevOps Services.

    ![Anwendungs-ID: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_02.png)

3. Klicken Sie zum Generieren eines Authentifizierungsschlüssels auf **Einstellungen**.

    ![Bearbeiten von App-Einstellungen: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_03.png)

4. Wählen Sie zum Generieren eines Authentifizierungsschlüssels die Option **Schlüssel** aus.

    ![Konfigurieren von Schlüsseleinstellungen: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_04.png)

5. Geben Sie eine Beschreibung für den Schlüssel ein, und legen Sie eine Dauer für ihn fest. Wählen Sie dann die Option **Schließen**.

    ![Schlüsselbeschreibung und -dauer: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_05.png)

    Wenn Sie den Schlüssel gespeichert haben, wird unter **WERT** der Schlüssel angezeigt. Kopieren Sie diesen Wert, da Sie ihn später nicht abrufen können. Sie geben den **Schlüsselwert** zusammen mit der **Anwendungs-ID** an, um die Anmeldung als App durchzuführen. Speichern Sie den Schlüsselwert an einem Ort, an dem er von Ihrer App abgerufen werden kann.

    ![Schlüsselwert: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_06.png)

### <a name="get-the-tenant-id"></a>Abrufen der Mandanten-ID

Im Rahmen der Dienstendpunkt-Konfiguration muss in Azure DevOps Services die **Mandanten-ID** angegeben werden, die dem AAD-Verzeichnis entspricht, in dem Ihr Azure Stack-Stempel bereitgestellt wird. Mit den folgenden Schritten wird die Mandanten-ID abgerufen.

1. Wählen Sie **Azure Active Directory**.

    ![Azure Active Directory für den Mandanten](media/azure-stack-solution-hybrid-pipeline/000_07.png)

2. Wählen Sie zum Abrufen der Mandanten-ID die Option **Eigenschaften** für Ihren Azure AD-Mandanten aus.

    ![Anzeigen der Mandanteneigenschaften: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_08.png)

3. Kopieren Sie die **-Verzeichnis-ID**. Dieser Wert ist Ihre Mandanten-ID.

    ![Verzeichnis-ID: Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Gewähren von Rechten für den Dienstprinzipal zum Bereitstellen von Ressourcen im Azure Stack-Abonnement

Um auf Ressourcen in Ihrem Abonnement zuzugreifen, müssen Sie die App einer Rolle zuweisen. Entscheiden Sie, welche Rolle über die besten Berechtigungen für die App verfügt. Informationen zu verfügbaren Rollen finden Sie unter [RBAC: Integrierte Rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Sie können den Umfang auf Abonnement-, Ressourcengruppen- oder Ressourcenebene festlegen. Berechtigungen werden von niedrigeren Ebenen mit geringerem Umfang geerbt. Wenn beispielsweise der Leserolle für eine Ressourcengruppe eine App hinzugefügt wird, kann diese Rolle die Ressourcengruppe und alle darin enthaltenen Ressourcen lesen.

1. Navigieren Sie zur Bereichsebene, der Sie die Anwendung zuweisen möchten. Um z.B. einer Gruppe im Abonnementkontext eine Rolle zuzuweisen, wählen Sie **Abonnements** aus.

    ![Auswählen von Abonnements: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_10.png)

2. Wählen Sie unter **Abonnements** die Option „Visual Studio Enterprise“.

    ![Visual Studio Enterprise: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_11.png)

3. Wählen Sie in Visual Studio Enterprise die Option **Zugriffssteuerung (IAM)** .

4. Wählen Sie **Rollenzuweisung hinzufügen** aus.

    ![Hinzufügen einer Rollenzuweisung: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_13.png)

5. Wählen Sie unter **Berechtigungen hinzufügen** die Rolle aus, die Sie der App zuweisen möchten. In diesem Beispiel ist dies die Rolle **Besitzer**.

    ![Berechtigungen für Rolle „Besitzer“: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_14.png)

6. Azure Active Directory-Apps werden standardmäßig nicht unter den verfügbaren Optionen angezeigt. Für die Suche nach Ihrer App müssen Sie den Namen in das Feld **Auswählen** eingeben. Wählen Sie die App aus.

    ![App-Suchergebnis: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_16.png)

7. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen. Ihre App wird in der Liste mit den Benutzern angezeigt, die einer Rolle für diesen Bereich zugewiesen sind.

### <a name="role-based-access-control"></a>Rollenbasierte Access Control

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe der RBAC können Sie die Zugriffsebene steuern, die Benutzer für ihre Arbeit benötigen. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Azure DevOps-Services-Agentpools

Agents können in Agentpools zusammengefasst werden, um sie nicht alle separat verwalten zu müssen. Ein Agent-Pool definiert die Freigabegrenze für alle Agents in diesem Pool. In Azure DevOps Services gelten Agentpools für die Azure DevOps Services-Organisation. Dies bedeutet, dass Sie einen Agentpool projektübergreifend freigeben können. Weitere Informationen zu Agentpool finden Sie unter [Agent pools and queues](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts) (Agentpools und Warteschlangen).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Hinzufügen eines persönlichen Zugriffstokens (Personal Access Token, PAT) für Azure Stack

Erstellen Sie ein persönliches Zugriffstoken für den Zugriff auf Azure DevOps Services.

1. Melden Sie sich an Ihrer Azure DevOps Services-Organisation an, und wählen Sie den Profilnamen Ihrer Organisation aus.

2. Klicken Sie auf **Sicherheit verwalten**, um die Seite für die Tokenerstellung zu öffnen. 

    ![Verwalten der Sicherheit: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_18.png)

3. Klicken Sie auf **Hinzufügen**, um ein neues persönliches Zugriffstoken zu erstellen.

    ![Hinzufügen eines persönlichen Zugriffstokens: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_18a.png)

    ![Erstellen eines Tokens: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_18b.png)

4. Kopieren Sie das Token.

    > [!Note]
    > Speichern Sie die Tokeninformationen. Diese Informationen werden nicht gespeichert und werden nach Verlassen der Seite nicht erneut angezeigt.

    ![Persönliches Zugriffstoken: Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Installieren des Azure DevOps Services-Build-Agents auf dem von Azure Stack gehosteten Buildserver

1. Stellen Sie eine Verbindung mit dem Buildserver her, den Sie auf dem Azure Stack-Host bereitgestellt haben.
2. Laden Sie den Build-Agent herunter, stellen Sie ihn als Dienst unter Verwendung Ihres persönlichen Zugriffstokens (Personal Access Token, PAT) bereit, und führen Sie ihn als VM-Administrator aus.

    ![Herunterladen des Build-Agents](media/azure-stack-solution-hybrid-pipeline/010_downloadagent.png)

3. Navigieren Sie zum Ordner des extrahierten Build-Agents. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten die Datei **config.cmd** aus.

    ![Extrahierter Build-Agent](media/azure-stack-solution-hybrid-pipeline/000_20.png)

    ![Registrieren des Build-Agents](media/azure-stack-solution-hybrid-pipeline/000_21.png)

4. Wenn die Ausführung von „config.cmd“ abgeschlossen ist, wird der Ordner des Build-Agents mit zusätzlichen Dateien aktualisiert. Der Ordner mit den extrahierten Inhalten sollte wie im folgenden Beispiel aussehen:

    ![Aktualisieren des Ordners für den Build-Agent](media/azure-stack-solution-hybrid-pipeline/009_token_file.png)

    Sie können den Agent im Azure DevOps Services-Ordner anzeigen.

## <a name="endpoint-creation-permissions"></a>Berechtigungen für die Endpunkterstellung

Durch die Erstellung von Endpunkten kann ein Visual Studio Online-Build Azure Service-Apps in Azure Stack bereitstellen. Azure DevOps Services stellt eine Verbindung mit dem Build-Agent her, der wiederum eine Verbindung mit Azure Stack herstellt.

![Beispiel-App „NorthwindCloud“ in VSTO](media/azure-stack-solution-hybrid-pipeline/012_securityendpoints.png)

1. Melden Sie sich bei VSTO an, und navigieren Sie zur Seite mit den App-Einstellungen.
2. Wählen Sie unter **Einstellungen** die Option **Sicherheit**.
3. Wählen Sie unter **Azure DevOps Services Groups** (Azure DevOps Services-Gruppen) die Option **Endpunktersteller**.

    ![NorthwindCloud-Endpunktersteller](media/azure-stack-solution-hybrid-pipeline/013_endpoint_creators.png)

4. Klicken Sie auf der Registerkarte **Mitglieder** auf **Hinzufügen**.

    ![Hinzufügen eines Mitglieds](media/azure-stack-solution-hybrid-pipeline/014_members_tab.png)

5. Geben Sie auf der Seite **Benutzer und Gruppen hinzufügen** einen Benutzernamen ein, und wählen Sie den Benutzer in der Benutzerliste aus.
6. Klicken Sie auf **Save changes** (Änderungen speichern).
7. Wählen Sie in der Liste **Azure DevOps Services Groups** (Azure DevOps Services-Gruppen) die Option **Endpunktadministratoren**.

    ![NorthwindCloud-Endpunktadministratoren](media/azure-stack-solution-hybrid-pipeline/015_save_endpoint.png)

8. Klicken Sie auf der Registerkarte **Mitglieder** auf **Hinzufügen**.
9. Geben Sie auf der Seite **Benutzer und Gruppen hinzufügen** einen Benutzernamen ein, und wählen Sie den Benutzer in der Benutzerliste aus.
10. Klicken Sie auf **Save changes** (Änderungen speichern).

Die Endpunktinformationen sind vorhanden, und die Verbindung zwischen Azure DevOps Services und Azure Stack kann nun verwendet werden. Der Build-Agent in Azure Stack erhält Anweisungen von Azure DevOps Services. Daraufhin übermittelt der Agent Endpunktinformationen für die Kommunikation mit der Azure Stack-Instanz.

## <a name="create-an-azure-stack-endpoint"></a>Erstellen eines Azure Stack-Endpunkts

### <a name="create-an-endpoint-for-azure-ad-deployments"></a>Erstellen eines Endpunkts für Azure AD-Bereitstellungen

Sie können die Anleitung unter [Create an Azure Resource Manager service connection with an existing service principal ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) (Erstellen einer Azure Resource Manager-Dienstverbindung mit einem vorhandenen Dienstprinzipal) befolgen, um eine Dienstverbindung mit einem vorhandenen Dienstprinzipal zu erstellen und die folgende Zuordnung zu verwenden:

| NAME | Beispiel | BESCHREIBUNG |
| --- | --- | --- |
| Verbindungsname | Azure Stack Azure AD | Der Name der Verbindung. |
| Environment | AzureStack | Der Name Ihrer Umgebung. |
| Umgebungs-URL | `https://management.local.azurestack.external` | Ihr Verwaltungsendpunkt. |
| Bereichsebene | Subscription | Der Bereich der Verbindung. |
| Abonnement-ID | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | Benutzerabonnement-ID aus Azure Stack. |
| Abonnementname | name@contoso.com | Benutzerabonnementname aus Azure Stack. |
| Client-ID des Dienstprinzipals | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | Die Prinzipal-ID aus [diesem Abschnitt](azure-stack-solution-pipeline.md#create-a-service-principal) des Artikels. |
| Dienstprinzipalschlüssel | THESCRETGOESHERE= | Der Schlüssel aus dem gleichen Artikel (bzw. das Kennwort, wenn Sie das Skript verwendet haben). |
| Mandanten-ID | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | Die Mandanten-ID, die Sie gemäß der Anleitung unter [Abrufen der Mandanten-ID](azure-stack-solution-pipeline.md#get-the-tenant-id) abrufen.  |
| Verbindung: | Nicht überprüft | Überprüfen Sie die Einstellungen für die Verbindung mit dem Dienstprinzipal. |

Nachdem der Endpunkt erstellt wurde, kann die Verbindung zwischen DevOps und Azure Stack verwendet werden. Der Build-Agent in Azure Stack erhält Anweisungen von DevOps. Daraufhin übermittelt der Agent Endpunktinformationen für die Kommunikation mit Azure Stack.

![Build-Agent – Azure AD](media/azure-stack-solution-hybrid-pipeline/016_save_changes.png)

### <a name="create-an-endpoint-for-ad-fs"></a>Erstellen eines Endpunkts für AD FS

Das neueste Update für Azure DevOps ermöglicht Ihnen die Erstellung einer Dienstverbindung unter Verwendung eines Dienstprinzipals mit einem Zertifikat für die Authentifizierung. Diese Verbindung ist erforderlich, wenn Azure Stack mit AD FS als Identitätsanbieter bereitgestellt wird. 

![Build-Agent – AD FS](media/azure-stack-solution-hybrid-pipeline/image06.png)

Eine Dienstverbindung kann mit der folgenden Zuordnung erstellt werden:

| NAME | Beispiel | BESCHREIBUNG |
| --- | --- | --- |
| Verbindungsname | Azure Stack ADFS | Der Name der Verbindung. |
| Environment | AzureStack | Der Name Ihrer Umgebung. |
| Umgebungs-URL | `https://management.local.azurestack.external` | Ihr Verwaltungsendpunkt. |
| Bereichsebene | Subscription | Der Bereich der Verbindung. |
| Abonnement-ID | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | Benutzerabonnement-ID aus Azure Stack. |
| Abonnementname | name@contoso.com | Benutzerabonnementname aus Azure Stack. |
| Client-ID des Dienstprinzipals | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | Die Client-ID des Dienstprinzipals, den Sie für AD FS erstellt haben. |
| Zertifikat | `<certificate>` |  Konvertieren Sie die Zertifikatdatei von PFX in PEM. Fügen Sie den Inhalt der PEM-Zertifikatdatei in dieses Feld ein. <br> So konvertieren Sie PFX in PEM:<br>`openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>` |
| Mandanten-ID | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | Die Mandanten-ID, die Sie gemäß der Anleitung unter [Abrufen der Mandanten-ID](azure-stack-solution-pipeline.md#get-the-tenant-id) abrufen. |
| Verbindung: | Nicht überprüft | Überprüfen Sie die Einstellungen für die Verbindung mit dem Dienstprinzipal. |

Nachdem der Endpunkt erstellt wurde, kann die Verbindung zwischen Azure DevOps und Azure Stack verwendet werden. Der Build-Agent in Azure Stack erhält Anweisungen von Azure DevOps. Daraufhin übermittelt der Agent Endpunktinformationen für die Kommunikation mit Azure Stack.

> [!Note]
> Wenn Ihr Azure Resource Manager-Endpunkt nicht über das Internet verfügbar gemacht wurde, ist die Verbindungsüberprüfung nicht erfolgreich. Dieses Verhalten wird erwartet, und Sie können die Verbindung überprüfen, indem Sie eine Releasepipeline mit einer einfachen Aufgabe erstellen. 

## <a name="develop-your-application-build"></a>Entwickeln Ihres Anwendungsbuilds

In diesem Teil der Lösung führen Sie folgende Schritte aus:

* Fügen Sie einem Azure DevOps Services-Projekt Code hinzu.
* Erstellen einer eigenständigen Web-App-Bereitstellung
* Konfigurieren des CD-Prozesses (Continuous Deployment)

> [!Note]
 > Für die Ausführung von Windows Server und SQL Server benötigen Sie eine Azure Stack-Umgebung mit den passenden syndizierten Images. Darüber hinaus muss App Service bereitgestellt sein. Anforderungen für Azure Stack-Bediener finden Sie im Abschnitt „Voraussetzungen“ der App Service-Dokumentation.

Der hybride CI/CD-Ansatz kann für Anwendungscode und Infrastrukturcode verwendet werden. Verwenden Sie [Azure Resource Manager-Vorlagen](https://azure.microsoft.com/resources/templates/), z. B. Web-App-Code aus Azure DevOps Services, für die Bereitstellung in beiden Clouds.

### <a name="add-code-to-an-azure-devops-services-project"></a>Hinzufügen von Code zu einem Azure DevOps Services-Projekt

1. Melden Sie sich an Azure DevOps Services mit einer Organisation an, die über Rechte zur Erstellung von Projekten in Azure Stack verfügt. Der nächste Screenshot zeigt, wie Sie eine Verbindung mit dem HybridCICD-Projekt herstellen.

    ![Herstellen einer Verbindung mit einem Projekt: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/017_connect_to_project.png)

2. **Klonen Sie das Repository**, indem Sie die Standard-Web-App erstellen und öffnen.

    ![Klonen von Repositorys: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Erstellen einer eigenständigen Web-App-Bereitstellung für App Services in beiden Clouds

1. Bearbeiten Sie die Datei **WebApplication.csproj**: Wählen Sie `Runtimeidentifier` aus, und fügen Sie dann Folgendes hinzu: `win10-x64.` Weitere Informationen finden Sie in der Dokumentation [Eigenständige Bereitstellungen (Self-Contained Deployments, SCD)](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).

    ![Konfigurieren von „Runtimeidentifier“](media/azure-stack-solution-hybrid-pipeline/019_runtimeidentifer.png)

2. Verwenden Sie Team Explorer, um den Code in Azure DevOps Services einzuchecken.

3. Vergewissern Sie sich, dass der Anwendungscode in Azure DevOps Services eingecheckt wurde.

### <a name="create-the-build-pipeline"></a>Erstellen der Buildpipeline

1. Melden Sie sich an Azure DevOps Services mit einer Organisation an, die eine Buildpipeline erstellen kann.

2. Navigieren Sie zur Seite **Build Web Application** (Webanwendung erstellen) für das Projekt.

3. Fügen Sie unter **Argumente** den Code **-r win10-x64** hinzu. Dieser Schritt ist erforderlich, um eine eigenständige Bereitstellung mit .NET Core auszulösen.

    ![Hinzufügen eines Arguments zur Buildpipeline](media/azure-stack-solution-hybrid-pipeline/020_publish_additions.png)

4. Führen Sie den Buildvorgang aus. Der Buildvorgang für die [eigenständige Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht Artefakte, die in Azure und Azure Stack ausgeführt werden können.

### <a name="use-an-azure-hosted-build-agent"></a>Verwenden eines in Azure gehosteten Build-Agents

Mit einem gehosteten Build-Agent lassen sich in Azure DevOps Services komfortabel Web-Apps erstellen und bereitstellen. Wartungsarbeiten und Upgrades für den Agent werden automatisch von Microsoft Azure durchgeführt, was einen kontinuierlichen und ungestörten Entwicklungszyklus ermöglicht.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurieren des CD-Prozesses (Continuous Deployment)

Azure DevOps Services und Team Foundation Server (TFS) verfügen über eine äußerst flexibel konfigurier- und verwaltbare Pipeline für Releases in mehreren Umgebungen (z.B. in Entwicklungs-, Staging-, Qualitätssicherungs- und Produktionsumgebungen). Dabei sind ggf. Genehmigungen in bestimmten Phasen des Anwendungslebenszyklus erforderlich.

### <a name="create-release-pipeline"></a>Erstellen einer Releasepipeline

Die Erstellung einer Releasepipeline ist der letzte Schritt des App-Buildprozesses. Diese Releasepipeline wird zum Erstellen eines Release und zum Bereitstellen eines Builds verwendet.

1. Melden Sie sich an Azure DevOps Services an, und navigieren Sie für Ihr Projekt zu **Azure Pipelines**.
2. Klicken Sie auf der Registerkarte **Releases** auf **\[ + ]** und wählen Sie dann **Releasedefinition erstellen**.

   ![Erstellen einer Releasepipeline: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/021a_releasedef.png)

3. Klicken Sie auf der Seite **Vorlage auswählen** auf **Azure App Service-Bereitstellung** und dann auf **Anwenden**.

    ![Anwenden der Vorlage: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/102.png)

4. Wählen Sie auf der Seite **Artefakt hinzufügen** im Pulldownmenü **Quelle (Builddefinition)** die Azure Cloud-Build-App aus.

    ![Hinzufügen eines Artefakts: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/103.png)

5. Klicken Sie auf der Registerkarte **Pipeline** auf den Link **1 Phase**, **1 Task** (1 Phase, 1 Aufgabe), um **Umgebungsaufgaben anzuzeigen**.

    ![Pipeline: Anzeigen von Aufgaben: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/104.png)

6. Geben Sie auf der Registerkarte **Aufgaben** unter **Umgebungsname** den Namen „Azure“ ein, und wählen Sie in der Dropdownliste **Azure-Abonnement** den Eintrag „AzureCloud Traders-Web EP“ aus.

    ![Festlegen von Umgebungsvariablen: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/105.png)

7. Geben Sie den **Azure App Service-Namen** ein. Im nächsten Screenshot ist das „northwindtraders“.

    ![App Service-Name: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/106.png)

8. Wählen Sie unter „Agent-Phase“ in der Dropdownliste **Agent-Warteschlange** den Eintrag **Hosted VS2017** aus.

    ![Gehosteter Agent: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/107.png)

9. Wählen Sie im Menü **Deploy Azure App Service** (Azure App Service bereitstellen) unter **Paket oder Ordner** den gültigen Eintrag für die Umgebung aus.

    ![Auswählen des Pakets oder Ordners: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/108.png)

10. Wählen Sie auf der Seite **Datei oder Ordner auswählen** für den Speicherort des Ordners **OK**.

    ![Auswählen der Datei oder des Ordners: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/109.png)

11. Speichern Sie alle Änderungen, und kehren Sie zur **Pipeline** zurück.

    ![Speichern der Änderungen: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/110.png)

12. Klicken Sie auf der Registerkarte **Pipeline** auf **Artefakt hinzufügen**, und wählen Sie in der Dropdownliste **Quelle (Builddefinition)** den Eintrag **NorthwindCloud Traders-Vessel** aus.

    ![Hinzufügen eines neuen Artefakts: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/111.png)

13. Fügen Sie auf der Seite **Vorlage auswählen** eine weitere Umgebung hinzu. Wählen Sie **Azure App Service-Bereitstellung** und dann **Anwenden** aus.

    ![Auswählen der Vorlage: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/112.png)

14. Geben Sie unter **Umgebungsname** den Namen „Azure Stack“ ein.

    ![Umgebungsname: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/113.png)

15. Suchen Sie auf der Registerkarte **Aufgaben** nach „Azure Stack“.

    ![Azure Stack-Umgebung: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/114.png)

16. Wählen Sie in der Dropdownliste **Azure-Abonnement** als Azure Stack-Endpunkt „AzureStack Traders-Vessel EP“ aus.

    ![Dropdownliste „Azure-Abonnement“: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/115.png)

17. Geben Sie unter **App Service-Name** den Namen der Azure Stack-Web-App ein.

    ![App Service-Name: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/116.png)

18. Wählen Sie unter **Agent-Auswahl** in der Dropdownliste **Agent-Warteschlange** den Eintrag „AzureStack -bDouglas Fir“ aus.

    ![Auswählen des Agents: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/117.png)

19. Wählen Sie für **Deploy Azure App Service** (Azure App Service bereitstellen) unter **Paket oder Ordner** den gültigen Eintrag für die Umgebung aus. Klicken Sie unter **Datei oder Ordner auswählen** für den **Speicherort** des Ordners auf **OK**.

    ![Auswählen des Pakets oder Ordners: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/118.png)

    ![Bestätigen des Speicherorts: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/119.png)

20. Suchen Sie auf der Registerkarte **Variablen** nach der Variablen **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Legen Sie den Wert der Variablen auf **true** und ihren Bereich auf **Azure Stack** fest.

    ![Konfigurieren der Variablen: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/120.png)

21. Klicken Sie auf der Registerkarte **Pipeline** für das Artefakt „NorthwindCloud Traders-Web“ auf **Continuous Deployment-Trigger**, und legen Sie **Continuous Deployment-Trigger** auf **Aktiviert** fest.  Führen Sie dieselben Schritte für das Artefakt „NorthwindCloud Traders-Vessel“ aus.

    ![Festlegen des Continuous Deployment-Triggers: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/121.png)

22. Klicken Sie für die Azure Stack-Umgebung auf das Symbol **Bedingungen vor der Bereitstellung**, und legen Sie den Trigger auf **Nach einem Release** fest.

    ![Festlegen des Triggers für Bedingungen vor der Bereitstellung: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/122.png)

23. Speichern Sie alle Änderungen.

> [!Note]
> Bei der vorlagenbasierten Erstellung einer Releasepipeline wurden einige Einstellungen für die Aufgaben unter Umständen automatisch als [Umgebungsvariablen](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) definiert. Diese Einstellungen können in den Taskeinstellungen nicht geändert werden. Allerdings können Sie diese Einstellungen in den übergeordneten Umgebungselementen bearbeiten.

## <a name="create-a-release"></a>Erstellen eines Release

Nachdem Sie die Releasepipeline geändert haben, können Sie die Bereitstellung starten. Erstellen Sie über die Releasepipeline ein Release, um mit der Bereitstellung zu beginnen. Ein Release kann automatisch erstellt werden, z. B. wenn in der Releasepipeline der Continuous Deployment-Trigger festgelegt ist. Das Festlegen dieses Triggers bedeutet, dass nach Änderungen am Quellcode ein neuer Buildvorgang gestartet und anschließend ein neues Release erstellt wird. In diesem Abschnitt erstellen Sie aber manuell ein neues Release.

1. Öffnen Sie auf der Registerkarte **Pipeline** die Dropdownliste **Release**, und wählen Sie die Option **Release erstellen**.

    ![Erstellen eines Release: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/200.png)

2. Geben Sie eine Beschreibung für das Release ein, vergewissern Sie sich, dass die korrekten Artefakte ausgewählt sind, und wählen Sie anschließend **Erstellen**. Kurz darauf erscheint ein Banner mit dem Hinweis, dass das neue Release erstellt wurde, und der Releasename wird als Link angezeigt. Wählen Sie den Link aus, um die Zusammenfassungsseite des Release anzuzeigen.

    ![Banner zur Releaseerstellung: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/201.png)

3. Die Zusammenfassungsseite enthält Details zum Release. Im folgenden Screenshot für „Release-2“ wird im Abschnitt **Umgebungen** als **Bereitstellungsstatus** für Azure „IN BEARBEITUNG“ und als Status für Azure Stack „SUCCEEDED“ (ERFOLGREICH) angezeigt. Wenn sich der Bereitstellungsstatus für die Azure-Umgebung in „SUCCEEDED“ (ERFOLGREICH) ändert, wird ein Banner mit dem Hinweis angezeigt, dass das Release nun genehmigt werden kann. Wenn eine Bereitstellung noch aussteht oder nicht erfolgreich war, wird ein blaues Informationssymbol **(i)** angezeigt. Zeigen Sie mit der Maus auf das Symbol, um ein Popupfenster mit dem Grund für die Verzögerung oder den Fehler anzuzeigen.

    ![Seite „Releasezusammenfassung“: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/202.png)

In anderen Ansichten (etwa in der Liste mit den Releases) wird ebenfalls ein Symbol angezeigt, das darauf hinweist, dass die Genehmigung aussteht. Das Popupfenster für dieses Symbol enthält den Umgebungsnamen und weitere Details zur Bereitstellung. So kann sich ein Administrator ganz einfach über den Gesamtstatus aller Releases informieren und erkennen, bei welchen Releases eine Genehmigung aussteht.

### <a name="monitor-and-track-deployments"></a>Überwachen und Nachverfolgen von Bereitstellungen

In diesem Abschnitt wird gezeigt, wie Sie alle Ihre Bereitstellungen überwachen und nachverfolgen. Das Release für die Bereitstellung der beiden Azure App Service-Websites ist ein gutes Beispiel.

1. Klicken Sie auf der Zusammenfassungsseite „Release-2“ auf **Protokolle**. Während einer Bereitstellung wird auf dieser Seite das Liveprotokoll des Agents angezeigt. Im linken Bereich wird der Status der einzelnen Vorgänge in der Bereitstellung für die jeweilige Umgebung angezeigt.

    Wählen Sie zum Anzeigen von Informationen zur Genehmigung vor oder nach der Bereitstellung das Personensymbol in der Spalte **Aktion**. So können Sie ermitteln, wer die Bereitstellung genehmigt (oder abgelehnt) hat und welche Nachricht vorhanden ist.

2. Nach Abschluss der Bereitstellung wird im rechten Bereich die gesamte Protokolldatei angezeigt. Wählen Sie einen beliebigen **Schritt** im linken Bereich aus, um die Protokolldatei für einen einzelnen Schritt (z. B. „Auftrag initialisieren“) anzuzeigen. Dank der Möglichkeit zum Anzeigen einzelner Protokolle lassen sich Teile der Gesamtbereitstellung leichter nachverfolgen und debuggen. Sie können die Protokolldatei für einen Schritt auch **speichern** oder **alle Protokolle als ZIP-Datei herunterladen**.

    ![Release-Protokolle: Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/203.png)

3. Öffnen Sie die Registerkarte **Zusammenfassung**, um allgemeine Informationen zum Release anzuzeigen. In dieser Ansicht werden die Details zum Build, die Umgebungen, in denen er bereitgestellt wurde, Bereitstellungsstatus und andere Informationen zum Release angezeigt.

4. Klicken Sie auf den Link für eine Umgebung (**Azure** oder **Azure Stack**), um Informationen zu vorhandenen und ausstehenden Bereitstellungen für eine bestimmte Umgebung anzuzeigen. Mithilfe dieser Ansichten können Sie sich schnell vergewissern, dass der gleiche Build in beiden Umgebungen bereitgestellt wurde.

5. Öffnen Sie die **bereitgestellte Produktions-App** in Ihrem Browser. Öffnen Sie für die Azure App Service-Website beispielsweise die URL `https://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
