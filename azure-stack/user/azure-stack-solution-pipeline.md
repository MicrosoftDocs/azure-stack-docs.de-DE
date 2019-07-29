---
title: Bereitstellen von Apps in Azure und Azure Stack
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
ms.date: 07/23/2019
ms.topic: solution
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 86b7fca6b9d2b9aaa322849f2490f83851a80940
ms.sourcegitcommit: d8981947a4bf7752d608e21e6fe0bf2ccd4825d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68462890"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Bereitstellen von Apps in Azure und Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Anhand dieser Lösung erfahren Sie, wie Sie mithilfe der hybriden Continuous Integration/Continuous Delivery (CI/CD) von Azure Pipelines Apps in Azure und Azure Stack bereitstellen.

Nach der Einrichtung von Azure Stack, Azure DevOps und Web-Apps gemäß den [Voraussetzungen](#prerequisites) haben Sie folgende Möglichkeiten:

> [!div class="checklist"]
> - Registrieren Sie Ihre Web-App, und richten Sie den Zugriff für Azure Pipelines in Azure Active Directory (Azure AD) ein. 
> - Erstellen Sie Azure Pipelines-Endpunkte für Azure AD und Active Directory Federation Services (AD FS). 
> - Installieren Sie den Azure Pipelines-Build-Agent auf dem Azure Stack-Buildserver. 
> - Konfigurieren Sie die Bereitstellung eigenständiger Apps auf Azure und Azure Stack.
> - Erstellen Sie eine Buildpipeline, die bei Codecommits in Ihrem Projekt automatisch Builds ausführt.
> - Erstellen Sie eine Buildpipeline, die die Builds automatisch auf Azure AD und Azure Stack bereitstellt. 
> - Erstellen Sie Releases manuell, stellen Sie sie bereit, und öffnen Sie Releasezusammenfassungen und -protokolle. 

Weitere Informationen zu CI/CD finden Sie in den folgenden Artikeln:

* [What is Continuous Integration?](https://www.visualstudio.com/learn/what-is-continuous-integration/) (Was ist Continuous Integration?)
* [What is Continuous Delivery?](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (Was ist Continuous Delivery?)

## <a name="azure-stack-and-hybrid-cicd"></a>Azure Stack und Hybrid-CI/CD

Microsoft Azure Stack ist eine Erweiterung von Azure, mit der Ihre lokale Umgebung von der Flexibilität und dem Innovationspotenzial des Cloud Computing profitieren kann. Dabei handelt es sich um die einzige Hybrid Cloud, mit der Sie hybride Apps sowohl lokal als auch in öffentlichen Cloudumgebungen erstellen und bereitstellen können. Sie können eine Anwendung in Azure Stack erstellen und anschließend in Azure Stack, in Azure oder in Ihrer Azure Hybrid Cloud bereitstellen. 

Bei der App-Bereitstellung sind Kontinuität, Sicherheit und Zuverlässigkeit für Ihre Organisation und Ihr Entwicklungsteam unverzichtbar. Mit dem hybriden CI/CD-Bereitstellungsmodell von Azure Pipelines können Sie Ihre Buildpipelines in der gesamten lokalen Umgebung und der Public Cloud konsolidieren und ohne Änderungen an der App Bereitstellungsorte ändern. Weitere Vorteile bei Verwendung des Hybridansatzes:

- Sie können einen konsistenten Satz von Entwicklungstools für Ihre lokale Azure Stack-Umgebung und die öffentliche Azure-Cloud nutzen.  Ein gängiges Toolset erleichtert die Implementierung von CI/CD-Mustern und -Methoden.
- Apps und Dienste, die in Azure und Azure Stack bereitgestellt werden, sind austauschbar, und der gleiche Code kann in beiden ausgeführt werden. Sie können die Features und Funktionen der lokalen Umgebung und der öffentlichen Cloud nutzen.

> [!TIP]
> ![hybrid-pillars.png](./media/azure-stack-solution-pipeline/hybrid-pillars.png)  
> Im Artikel [Hybrid Cloud-Entwurfsmuster für Azure Stack](azure-stack-edge-pattern-overview.md) werden die Säulen der Softwarequalität für Konzeption, Bereitstellung und Betrieb von hybriden Anwendungen erläutert. Die Qualitätskriterien umfassen Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit. Diese Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybrid-Apps, um das Auftreten von Problemen in Produktionsumgebungen zu minimieren.

## <a name="prerequisites"></a>Voraussetzungen

- Grundkenntnisse in Azure und Azure Stack. Lesen Sie die folgenden Artikel, um vor dem Bereitstellen der Lösung weitere Informationen zu erhalten:
  
  - [Einführung in Azure](https://azure.microsoft.com/overview/what-is-azure/)
  - [Übersicht über Azure Stack](../operator/azure-stack-overview.md)
  
- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
  
- Eine in Azure erstellte Web-App. Erstellen Sie mithilfe einer [Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/) eine Web-App, die Sie lokal und in der Public Cloud bereitstellen können. Notieren Sie sich den App-URI für später. 
  
- Visual Studio 2019 ist [installiert](/visualstudio/install/install-visual-studio).
  
- Administratorzugriff auf eine [Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)-Organisation, in der Pipelines erstellt werden können, und ein DevOps-[Projekt](/azure/devops/organizations/projects/create-project) oder -[Arbeitsbereich](/azure/devops/repos/tfvc/create-work-workspaces). 
  
- Ein Windows Server 2016-Image mit .NET 3.5 für die Azure Pipelines-Builds auf dem virtuellen Computer für den privaten Build-Agent im Azure Stack.
  
- Ein System mit integriertem Azure Stack oder das Azure Stack Development Kit (ASDK), das entsprechend den folgenden Anweisungen bereitgestellt und konfiguriert wurde. 
  
  
  Das Azure Stack Development Kit (ASDK) ist eine Azure Stack-Bereitstellung mit einem einzelnen Knoten, die Sie kostenlos herunterladen und verwenden können. Mit dem ASDK können Sie Azure Stack evaluieren und Azure-APIs und -Tools in Nichtproduktionsumgebungen verwenden.
  
  Das ASDK kann von jedem Benutzer mit Administratoranmeldeinformationen für Azure AD oder Active Directory Federation Services (AD FS) bereitgestellt werden. Ein Azure-OEM/-Hardwarepartner kann eine Azure Stack-Produktionsumgebung bereitstellen. Nur Azure Stack-Bediener können die folgenden Konfigurationsaufgaben für Azure Stack ausführen: 
  
  - Bereitstellen von Azure App Service
  - Erstellen von Plänen und Angeboten
  - Erstellen eines Mandantenabonnements
  - Anwenden eines Windows Server 2016-Images
  
  > [!Note]
  > Der ASDK-Installationsvorgang dauert etwa sieben Stunden. Berücksichtigen Sie dies bei Ihrer Planung.
    
  So stellen Sie das ASDK bereit und konfigurieren dieses
  
  1. Befolgen Sie die ausführlichen Anweisungen zur Bereitstellung unter [Bereitstellen des ASDKs mithilfe des Installationsprogramms](../asdk/asdk-install.md).
     
  1. Mit dem PowerShell-Skript [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) lassen sich die Schritte nach der ASDK-Bereitstellung automatisieren.
     
  1. Stellen Sie PaaS-Dienste als [Azure App Service](../operator/azure-stack-app-service-deploy.md) für Azure Stack bereit.
     
  1. Erstellen Sie [einen Plan und ein Angebot](../operator/azure-stack-plan-offer-quota-overview.md) in Azure Stack.
     
  1. Erstellen Sie ein [Mandantenabonnement](../operator/azure-stack-subscribe-plan-provision-vm.md) für das Angebot in Azure Stack. 
     
  1. Erstellen Sie eine Web-App im Mandantenabonnement. Notieren Sie sich die URL der neuen Web-App zur späteren Verwendung.
     
  1. Stellen Sie einen virtuellen Computer mit Windows Server 2016 und .NET 3.5 im Mandantenabonnement bereit, der als Buildserver dient, auf dem Azure Pipelines ausgeführt werden.
  
  > [!Note]
  > Für die Ausführung von Windows Server und SQL Server benötigen Sie eine Azure Stack-Umgebung mit den richtigen Images. Darüber hinaus muss App Service bereitgestellt sein.

## <a name="register-your-web-app-and-give-it-access-to-resources"></a>Registrieren der Web-App und Gewähren des Ressourcenzugriffs 

In Azure Active Directory (Azure AD) wird Azure Pipelines mit einem *Dienstprinzipal* bei Azure Resource Manager authentifiziert. Zum Bereitstellen von Ressourcen für Azure Pipelines muss der Dienstprinzipal im Azure-Abonnement über die Rolle **Mitwirkender** verfügen. 

Zum Konfigurieren der Authentifizierung für Ihre App können Sie das Azure-Portal verwenden. 

1. Registrieren Sie Ihre App, um einen Dienstprinzipal zu erstellen.
1. Verwenden Sie die *rollenbasierte Zugriffssteuerung (RBAC)* , um dem Dienstprinzipalnamen (SPN) die Rolle **Mitwirkender** zu verleihen.
1. Kopieren und speichern Sie die Werte für die Anwendungs-ID und die Mandanten-ID, die Sie zum Erstellen von Endpunkten für Azure Pipelines benötigen. 
1. Erstellen und speichern Sie einen Wert für den geheimen Anwendungsschlüssel.

Sie können auch ein [PowerShell-Skript](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) verwenden, um einen Dienstprinzipal und die Endpunkte zu erstellen. Dieser Prozess wird im Artikel [Create an Azure Resource Manager service connection with an existing service principal](/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) (Erstellen einer Azure Resource Manager-Dienstverbindung mit einem vorhandenen Dienstprinzipal) erläutert.

 > [!Note]  
 > Wenn Sie das PowerShell-Skript zum Erstellen eines Azure Resource Manager-Endpunkts für Azure Stack verwenden, müssen Sie die Parameter **-azureStackManagementURL** und **-environmentName** übergeben. Beispiel:  
 > `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="register-your-app-in-azure-ad"></a>Registrieren der App in Azure AD 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich **Azure Active Directory** und dann **App-Registrierungen** aus.
   
1. Wählen Sie **Neue Registrierung** aus.
   
1. Gehen Sie auf der Seite **Registrieren einer Anwendung** folgendermaßen vor:
   1. Geben Sie den Namen Ihrer Web-App ein.
   1. Wählen Sie einen unterstützten Kontotyp aus. 
   1. Wählen Sie unter **Umleitungs-URI** als Typ der Anwendung, die Sie erstellen möchten, **Web** aus, und geben Sie den URI der Web-App ein. 
   1. Wählen Sie **Registrieren**.
      
      ![Anwendung registrieren](./media/azure-stack-solution-pipeline/create-app.png) 

### <a name="assign-the-app-to-a-role"></a>Zuweisen der App zu einer Rolle

Sie müssen die Anwendung einer Rolle zuweisen, damit sie auf Ressourcen in Ihrem Abonnement zugreifen kann. Mithilfe von Azure RBAC können Sie die Zugriffsebene steuern, die Benutzer für ihre Arbeit benötigen. Weitere Informationen zu RBAC finden Sie unter [Verwalten des Zugriffs auf Azure-Abonnementressourcen](/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json). Informationen zu verfügbaren Rollen finden Sie unter [RBAC: Integrierte Rollen](/azure/role-based-access-control/built-in-roles).

Azure Pipelines muss die Rolle **Mitwirkender** besitzen, um Ressourcen in einem Azure Stack-Abonnement bereitstellen zu können. 

Sie können den Rollenbereich auf Ebene des Abonnements, der Ressourcengruppe oder der Ressource festlegen. Berechtigungen werden von niedrigeren Ebenen mit geringerem Umfang geerbt. Wenn beispielsweise der Rolle **Leser** für eine Ressourcengruppe eine App hinzugefügt wird, kann diese Rolle die Ressourcengruppe und alle darin enthaltenen Ressourcen lesen.

So weisen Sie die Anwendung der Rolle **Mitwirkender** zu:

1. Navigieren Sie im Azure-Portal zum gewünschten Bereich. Um z. B. einer Gruppe im Abonnementkontext eine Rolle zuzuweisen, wählen Sie **Alle Dienste** und **Abonnements** aus.
   
   ![Zuweisen einer Rolle im Abonnementbereich](./media/azure-stack-solution-pipeline/select-subscription.png)
   
1. Wählen Sie das Abonnement aus, dem die Anwendung zugewiesen werden soll.
   
1. Klicken Sie im linken Navigationsbereich auf **Zugriffssteuerung (IAM)** .
   
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
   
1. Wählen Sie im Dialogfeld **Rollenzuweisung hinzufügen** die Rolle **Mitwirkender** aus. Azure AD-Anwendungen werden standardmäßig nicht in den verfügbaren Optionen angezeigt. Suchen Sie nach dem Namen Ihrer Anwendung, und wählen Sie ihn aus.
   
   ![Auswählen der Rolle und der Anwendung](./media/azure-stack-solution-pipeline/select-role.png)
   
1. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen. Ihre Anwendung wird in der Liste der Benutzer angezeigt, die einer Rolle für diesen Bereich zugewiesen sind.

Ihr Dienstprinzipal wird eingerichtet. Im nächsten Abschnitt erfahren Sie, wie Sie die Werte abrufen, die Azure Pipelines für die programmgesteuerte Anmeldung benötigt.

### <a name="get-values-for-signing-in"></a>Abrufen von Werten für die Anmeldung

Beim Erstellen von Endpunkten für Azure Pipelines müssen Sie die Mandanten-ID und die Anwendungs-ID eingeben. So rufen Sie diese Werte ab

1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
   
1. Wählen Sie im linken Navigationsbereich **App-Registrierungen** und dann Ihre Anwendung aus.
   
1. Kopieren und speichern Sie die **Verzeichnis-(Mandanten-)ID** und die **Anwendungs-(Client-)ID** für die Erstellung der Endpunkte.
   
   ![Kopieren Sie die Verzeichnis-(Mandanten-)ID und die Anwendungs-(Client-)ID.](./media/azure-stack-solution-pipeline/copy-app-id.png)

### <a name="create-a-new-application-secret"></a>Erstellen eines neuen Anwendungsgeheimnisses

Beim Erstellen von Endpunkten für Azure Pipelines muss sich die App authentifizieren. Dazu kann ein [Zertifikat verwendet](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) werden oder ein Anwendungsgeheimnis. Wenn Sie Azure Stack mit AD FS als Identitätsanbieter bereitgestellt haben, müssen Sie ein Zertifikat verwenden.

Befolgen Sie die Schritte unter [Zertifikate und Geheimnisse](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets), um ein neues Zertifikat zu erstellen und hochzuladen. 

Sie können auch ein neues Anwendungsgeheimnis erstellen:

1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
   
1. Wählen Sie im linken Navigationsbereich **App-Registrierungen** und dann Ihre Anwendung aus.
   
1. Klicken Sie im linken Navigationsbereich auf **Zertifikate & Geheimnisse**.
   
1. Wählen Sie unter **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel**.
   
1. Geben Sie unter **Geheimen Clientschlüssel hinzufügen** eine Beschreibung ein, wählen Sie eine Ablauffrist aus, und klicken Sie auf **Hinzufügen**.
   
1. Kopieren Sie den **WERT** des neuen Geheimnisses. Sie müssen den Wert angeben, um sich als App anzumelden. Speichern Sie diesen Wert unbedingt jetzt, da er nicht mehr angezeigt wird, nachdem Sie diese Seite einmal verlassen haben.
   
   ![Kopieren Sie den geheimen Wert, da Sie ihn später nicht mehr abrufen können.](./media/azure-stack-solution-pipeline/copy-secret.png)

## <a name="create-endpoints"></a>Erstellen von Endpunkten

Durch Erstellen von Endpunkten kann ein Azure Pipelines-Build Azure AD-Apps auf Azure Stack bereitstellen. Azure Pipelines stellt eine Verbindung mit dem Build-Agent her, der wiederum eine Verbindung mit Azure Stack herstellt.

Nach dem Festlegen der Berechtigungen für die Endpunkterstellung können Sie Endpunkte für Azure AD oder AD FS erstellen. 

- Wenn Sie Azure Stack mit Azure AD als Identitätsanbieter bereitgestellt haben, können Sie ein Zertifikat oder ein Anwendungsgeheimnis verwenden, um eine Azure Resource Manager-Dienstverbindung für Azure-Bereitstellungen zu erstellen. 
  
- Wenn Sie Active Directory-Verbunddienste (AD FS) als Identitätsanbieter für Azure Stack verwendet haben, müssen Sie die Dienstverbindung mithilfe eines Zertifikats anstelle eines geheimen Clientschlüssels für die Authentifizierung erstellen. 

### <a name="set-endpoint-creation-permissions"></a>Festlegen der Berechtigungen für die Endpunkterstellung

1. Wählen Sie Ihrer Organisation und Ihrem Projekt für Azure DevOps **Projekteinstellungen** aus. 
   
1. Wählen Sie **Sicherheit** und unter **Azure DevOps-Gruppen** die Option **Endpunktadministratoren** aus.
   
1. Klicken Sie auf der Registerkarte **Mitglieder** auf **Hinzufügen**.
   
1. Wählen Sie unter **Benutzer und Gruppen hinzufügen** Benutzernamen aus der Liste aus, einschließlich Ihres eigenen, und wählen Sie dann **Änderungen speichern** aus.
   
   ![Hinzufügen eines Mitglieds](./media/azure-stack-solution-pipeline/endpoint-permissions.png)
   
1. Wählen Sie in der Liste **Azure DevOps-Gruppen** die Option **Endpunktersteller** aus und wiederholen Sie die vorherigen Schritte zum Hinzufügen von Benutzern zur Gruppe **Endpunktersteller**. 

### <a name="create-an-endpoint-for-azure-ad-or-ad-fs-deployments"></a>Erstellen eines Endpunkts für Azure AD- oder AD FS-Bereitstellungen

Befolgen Sie die Anweisungen unter [Create an Azure Resource Manager service connection with an existing service principal](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) (Erstellen einer Azure Resource Manager-Dienstverbindung mit einem vorhandenen Dienstprinzipal), um den Dienstverbindungsprinzipal zu erstellen.  

Füllen Sie das Formular mit den folgenden Werten aus: 

- **Verbindungsname**: Geben Sie einen benutzerfreundlichen Namen ein, der als Verweis auf diese Dienstverbindung verwendet werden soll.
  
- **Umgebung**: Wählen Sie den Umgebungsnamen aus, z. B. **AzureCloud** oder **AzureStack**. Wenn AzureStack in der Dropdownliste nicht angezeigt wird, finden Sie weitere Informationen unter [Herstellen einer Verbindung mit Azure Stack](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#connect-to-azure-stack).
  
- **Umgebungs-URL**: Wenn Sie **AzureCloud** nicht ausgewählt haben, geben Sie die Umgebungs-URL ein, z. B. *https:\//management.local.azurestack.external*.
  
- **Bereichsebene**: Wählen Sie die erforderliche Bereichsebene aus, z. B. **Abonnement**. 
  
- **Abonnement-ID**: Geben Sie Ihre Abonnement-ID ein.
  
- **Abonnementname**: Geben Sie Ihren Benutzernamen aus Azure Stack ein.
  
- **Client-ID des Dienstprinzipals**: Geben Sie die zuvor gespeicherte **Anwendungs-(Client-)ID** ein. 
  
- **Dienstprinzipalschlüssel** oder **Zertifikat**: Wählen Sie eine der beiden Optionen aus. 
  
  > [!NOTE]
  > Zum Erstellen eines AD FS-Endpunkts müssen Sie ein Zertifikat für die Authentifizierung verwenden. 
  
  - Geben Sie als **Dienstprinzipalschlüssel** den zuvor gespeicherten Wert für den geheimen Clientschlüssel ein.
  - Wenn Sie **Zertifikat** auswählen, geben Sie den Inhalt der Abschnitte für das Zertifikat und den privaten Schlüssel aus der *.pem*-Zertifikatsdatei ein. 
    
    > [!NOTE]
    > Um eine *.pfx*-Datei in eine *.pem*-Zertifikatsdatei zu konvertieren, führen Sie `openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>` aus.
  
- **Mandanten-ID**: Geben Sie die zuvor gespeicherte **Verzeichnis-(Mandanten-)ID** ein.
  
- **Verbindung: Nicht überprüft**: Wählen Sie **Verbindung überprüfen** aus, um Ihre Verbindungseinstellungen mit dem Dienstprinzipal zu überprüfen.
  
  > [!NOTE]
  > Wenn Ihr Azure Resource Manager-Endpunkt nicht über das Internet verfügbar gemacht wurde, ist die Verbindungsüberprüfung nicht erfolgreich. Dieses Verhalten wird erwartet, und Sie können die Verbindung überprüfen, indem Sie eine Releasepipeline mit einer einfachen Aufgabe erstellen.

## <a name="install-and-configure-the-build-agent"></a>Installieren und Konfigurieren des Build-Agents 

Mit einem gehosteten Build-Agent lassen sich in Azure Pipelines komfortabel Web-Apps erstellen und bereitstellen. Azure führt automatisch Wartungsarbeiten und Upgrades für den Agent durch, was einen kontinuierlichen und ungestörten Entwicklungszyklus ermöglicht.

Erstellen Sie in Azure DevOps ein persönliches Zugriffstoken (Pat), das für Azure Stack verwendet werden soll. Verwenden Sie dann das Pat zum Bereitstellen und Konfigurieren des Build-Agents auf dem virtuellen Azure Stack-Buildservercomputer. 
   
### <a name="create-a-personal-access-token"></a>Erstellen eines persönlichen Zugriffstokens

1. Melden Sie sich bei Azure DevOps an, und wählen Sie rechts oben **Mein Profil** aus. 
   
1. Erweitern Sie auf Ihrer Profilseite das Dropdownmenü neben dem Namen Ihrer Azure Stack-Organisation, und wählen Sie **Sicherheit verwalten** aus. 
   
   ![Sicherheit verwalten](media/azure-stack-solution-pipeline/managesecurity.png)
   
1. Wählen Sie auf der Seite **Persönliche Zugriffstoken** die Option **Neues Token** aus.
   
   ![Persönliche Zugriffstoken](media/azure-stack-solution-pipeline/pats.png)
   
1. Füllen Sie auf der Seite **Neues persönliches Zugriffstoken erstellen** die Tokeninformationen aus, und klicken Sie auf **Erstellen**. 
   
   ![PAT erstellen](media/azure-stack-solution-pipeline/createpat.png)
   
1. Kopieren und speichern Sie das Token. Dieses wird nach dem Verlassen der Webseite nicht mehr angezeigt.
   
   ![PAT-Warnung](media/azure-stack-solution-pipeline/patwarning.png)
   
### <a name="install-and-configure-the-agent-on-the-build-server"></a>Installieren und Konfigurieren des Agents auf dem Buildserver

1. Stellen Sie eine Verbindung mit dem virtuellen Buildservercomputer her, den Sie auf dem Azure Stack-Host bereitgestellt haben.
   
1. Laden Sie das Image des Build-Agents herunter. 
   
1. Stellen Sie den Agent an einer Administrator Eingabeaufforderung mithilfe von PAT als Dienst bereit, und führen Sie ihn aus.
   
1. Navigieren Sie zum Ordner des extrahierten Build-Agents, und führen Sie die Datei *config.cmd* aus. Mit der Datei *config.cmd* werden zusätzliche Dateien im Build-Agent-Ordner aktualisiert.
   
   ![Installieren und Konfigurieren des Build-Agents](media/azure-stack-solution-pipeline/buildagent.png)

Nachdem Sie den Endpunkt erstellt und den Azure Pipelines-Build-Agent auf dem Buildserver installiert haben, ist die Verbindung von Azure Pipelines mit Azure Stack einsatzbereit. Der Build-Agent in Azure Stack erhält Anweisungen von Azure Pipelines. Daraufhin übermittelt der Agent Endpunktinformationen für die Kommunikation mit Azure Stack.

‎‎Agents können zu *Agentpools* zusammengefasst werden, um sie nicht alle separat verwalten zu müssen. Ein Agent-Pool definiert die Freigabegrenze für alle Agents in diesem Pool. Agentpools gelten für die Azure DevOps Services-Organisation. Dies bedeutet, dass mehrere Projekte gemeinsam einen Agentpool verwenden können. Weitere Informationen zu Agentpools finden Sie unter [Create agent pools and queues](/azure/devops/pipelines/agents/pools-queues) (Erstellen von Agentpools und Warteschlangen).

## <a name="create-build-and-release-pipelines"></a>Einrichten von Build- und Releasepipelines 

Azure Pipelines verfügt über eine äußerst flexibel konfigurier- und verwaltbare Pipeline für Releases in mehreren Umgebungen (z. B. in Entwicklungs-, Staging-, Qualitätssicherungs- und Produktionsumgebungen). Bei diesem Releaseprozess sind ggf. Genehmigungen in bestimmten Phasen des Anwendungslebenszyklus erforderlich.

In diesem Teil der Lösung führen Sie folgende Schritte aus:

- Klonen, Verbinden und Hinzufügen von Code zum Azure DevOps-Projekt in Visual Studio.
- Erstellen einer eigenständigen Web-App-Bereitstellung.
- Konfigurieren der CI/CD-Build-und Releasepipelines.

Der hybride CI/CD-Ansatz kann sowohl für App-Code als auch für Infrastrukturcode verwendet werden. Sie können Azure Resource Manager-[Hybridvorlagen](https://azure.microsoft.com/resources/templates/) verwenden, um Ihren Azure-Web-App-Code für lokale und öffentliche Clouds bereitzustellen.

### <a name="clone-your-project"></a>Klonen des Projekts

1. Wählen Sie im Visual Studio-**Team Explorer** das Symbol **Verbinden** aus, und melden Sie sich bei ihrer Azure DevOps-Organisation an. 
   
1. Wählen Sie **Verbindungen verwalten** > **Verbindung mit einem Projekt herstellen** aus. 
   
   ![Herstellen einer Projektverbindung über Team Explorer](media/azure-stack-solution-pipeline/connecttoprojectteamexp.png)

1. Wählen Sie im Dialogfeld **Verbindung mit einem Projekt herstellen** das Web-App-Projekt aus, legen Sie einen lokalen Pfad fest, und wählen Sie dann **Klonen** aus, um das Repository lokal zu klonen.
   
   ![Repository in „Verbindung mit einem Projekt herstellen“ klonen](media/azure-stack-solution-pipeline/cloneproject.png)

### <a name="create-a-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Erstellen einer eigenständigen Web-App-Bereitstellung für App Services in beiden Clouds

1. Öffnen Sie im Visual Studio-**Projektmappen-Explorer** die Datei *WebApplication.csproj*, und fügen Sie `<RuntimeIdentifier>win10-x64</RuntimeIdentifier>` hinzu. Weitere Informationen zu diesem Schritt finden Sie unter [Eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd).
   
   ![Konfigurieren von RuntimeIdentifier](media/azure-stack-solution-pipeline/runtimeidentifier.png)
   
1. Speichern Sie Ihre Arbeit, und verwenden Sie **Team Explorer**, um den Code in Ihr Projekt einzuchecken.

### <a name="create-a-build-pipeline-and-run-a-build"></a>Erstellen einer Buildpipeline und Ausführen eines Builds

1. Öffnen Sie Ihre Azure DevOps-Organisation und Ihr Projekt im Webbrowser.
   
1. Wählen Sie linken Navigationsbereich **Pipelines** > **Builds** und dann **Neue Pipeline**. 
   
1. Wählen Sie unter **Vorlage auswählen** die Vorlage **ASP.NET Core** und dann **Übernehmen** aus. 
   
1. Wählen Sie auf der Seite „Konfiguration“ im linken Bereich **Veröffentlichen** aus.
   
1. Fügen Sie `-r win10-x64` im rechten Bereich unter **Argumente** der Konfiguration hinzu. 
   
   ![Hinzufügen eines Arguments für die Buildpipeline](media/azure-stack-solution-pipeline/buildargument.png)
   
1. Wählen Sie oben auf der Seite **Speichern und in die Warteschlange einreihen** aus.
   
1. Wählen Sie im Dialogfeld **Build ausführen** die Option **Speichern und ausführen** aus. 
   
Der [Buildvorgang für die eigenständige Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht Artefakte, die in Azure und Azure Stack ausgeführt werden können.

### <a name="create-a-release-pipeline"></a>Erstellen einer Releasepipeline

Die Erstellung einer Releasepipeline ist der letzte Schritt im Konfigurationsprozess für den hybriden CI/CD-Ansatz. Verwenden Sie die Releasepipeline zum Erstellen eines Release und zum Bereitstellen des Builds.

1. Wählen Sie in Ihrem Azure DevOps-Projekt im linken Navigationsbereich **Pipelines** > **Releases** und dann **Neue Pipeline** aus. 
   
1. Wählen Sie auf der Seite **Vorlage auswählen** die Option **Azure App Service-Bereitstellung** und dann **Anwenden** aus.
   
   ![Auswählen der Releasevorlage](media/azure-stack-solution-pipeline/releasetemplate.png)
   
1. Wählen Sie auf der Registerkarte **Pipeline** im linken Bereich **Artefakt hinzufügen** aus. Wählen Sie im rechten Bereich den soeben über das Dropdownmenü **Quelle (Buildpipeline)** erstellten Web-App-Build und dann **Hinzufügen** aus.
   
   ![Hinzufügen eines Buildartefakts](media/azure-stack-solution-pipeline/addartifact.png)
   
1. Wählen Sie auf der Registerkarte **Pipeline** unter **Stufen** den Link in **Stufe 1** aus, um **Stufenaufgaben anzeigen** zu lassen.
   
   ![Stufenaufgaben anzeigen](media/azure-stack-solution-pipeline/viewstagetasks.png)
   
1. Geben Sie auf der Registerkarte **Aufgaben** als **Stufennamen** *Azure* ein. 
   
1. Wählen Sie unter **Parameter** in der Dropdownliste **Azure-Abonnement** Ihr Abonnement aus, und geben Sie einen Namen für **App Service-Name** ein.
   
   ![Abonnement auswählen und App Service-Namen eingeben](media/azure-stack-solution-pipeline/stage1.png)
   
1. Wählen Sie im linken Bereich **Ausführung mit Agent** aus. Wählen Sie im rechten Bereich in der Dropdownliste **Agentpool** die Option **Hosted VS2017** aus, sofern diese nicht bereits ausgewählt ist.
   
   ![Gehosteten Agent auswählen](media/azure-stack-solution-pipeline/agentjob.png)
   
1. Wählen Sie im linken Bereich **Azure App Service bereitstellen** aus, und navigieren Sie im rechten Bereich zum **Paket oder Ordner** für den Azure-Web-App-Build.
   
   ![Auswählen des Pakets oder Ordners](media/azure-stack-solution-pipeline/packageorfolder.png)
   
1. Wählen Sie im Dialogfeld **Datei oder Ordner suchen** die Option **OK** aus.
   
1. Wählen Sie rechts oben auf der Seite **Neue Releasepipeline** die Option **Speichern** aus.
   
   ![Speichern der Änderungen](media/azure-stack-solution-pipeline/save-devops-icon.png)
   
1. Wählen Sie auf der Registerkarte **Pipeline** die Option **Artefakt hinzufügen** aus. Wählen Sie Ihr Projekt und dann im Dropdownmenü **Quelle (Buildpipeline)** den Azure Stack-Build aus. Wählen Sie **Hinzufügen**. 
   
1. Wählen Sie auf der Registerkarte **Pipeline** unter **Stufen** die Option **Hinzufügen** aus.
   
1. Wählen Sie in der neuen Stufe den Link **Stufenaufgaben anzeigen** aus. Geben Sie als Stufennamen den Namen *Azure Stack* ein. 
   
   ![Neue Stufe anzeigen](media/azure-stack-solution-pipeline/newstage.png)
   
1. Wählen Sie unter **Parameter** ihren Azure Stack-Endpunkt aus, und geben Sie den Namen der Azure Stack-Web-App unter **App Service-Name** ein.
   
1. Wählen Sie unter **Ausführung mit Agent** in der Dropdownliste **Agentpool** den Azure Stack-Buildserver-Agent aus.
   
1. Wählen Sie unter **Azure App Service bereitstellen** das **Paket oder den Ordner** für den Azure Stack-Build und dann im Dialogfeld **Datei oder Ordner suchen** die Option **OK** aus.
   
1. Suchen Sie auf der Registerkarte **Variablen** nach der Variablen **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Legen Sie den Wert der Variablen auf **true** und ihren Bereich auf **Azure Stack** fest.
   
   ![Konfigurieren von Variablen](media/azure-stack-solution-pipeline/setvariable.png)
   
1. Wählen Sie auf der Registerkarte **Pipeline** für jedes Artefakt das Symbol **Continuous Deployment-Trigger** aus, und legen Sie es auf **Aktiviert** fest.  
   
   ![Festlegen des Continuous Deployment-Triggers](media/azure-stack-solution-pipeline/contindeploymentenabled.png)
   
1. Klicken Sie für die Azure Stack-Stufe auf das Symbol **Bedingungen vor der Bereitstellung**, und legen Sie den Trigger auf **Nach einem Release** fest.
   
   ![Festlegen des Triggers für Bedingungen vor der Bereitstellung](media/azure-stack-solution-pipeline/predeployafterrelease.png)
   
1. Wählen Sie rechts oben auf der Seite **Neue Releasepipeline** die Option **Speichern** aus, um die Releasepipeline zu speichern.

> [!Note]
> Bei der vorlagenbasierten Erstellung einer Releasepipeline wurden einige Einstellungen für die Releaseaufgaben unter Umständen automatisch als [benutzerdefinierte Variablen](/azure/devops/pipelines/release/variables?view=vsts#custom-variables) definiert. Diese Einstellungen können nicht in den Aufgabeneinstellungen geändert werden, sondern nur in den übergeordneten Stufenelementen.

## <a name="release-the-app"></a>Veröffentlichen der App

Nachdem Sie nun über eine Releasepipeline verfügen, können Sie sie zum Erstellen eines Release und zum Bereitstellen Ihrer App verwenden. 

Da der Continuous Deployment-Trigger in der Releasepipeline festgelegt ist, wird durch Ändern des Quellcodes ein neuer Build gestartet und automatisch ein neues Release erstellt. Die Erstellung und Ausführung dieses neuen Release soll hier jedoch manuell erfolgen.

So erstellen Sie ein Release und stellen dieses bereit

1. Wählen Sie auf der Seite „Neue Releasepipeline“ rechts oben **Release erstellen** aus. 
   
   ![Erstellen eines Release ](media/azure-stack-solution-pipeline/createreleaseicon.png)
   
1. Gehen Sie auf der Seite **Neues Release erstellen** wie folgt vor:
   1. Wählen Sie unter **Pipeline** die Stufe **Azure** aus, um den zugehörigen Trigger von automatisiert in manuell zu ändern. 
   1. Stellen Sie sicher, dass unter **Artefakte** die richtigen Artefakte ausgewählt sind.
   1. Geben Sie eine **Releasebeschreibung** ein, und wählen Sie dann **Erstellen** aus. 
   
   Auf einem Banner wird angezeigt, dass das neue Release erstellt wird. Sie können den Link „Releasename“ auswählen, um eine Seite mit der Releasezusammenfassung anzuzeigen, die Details zum Release enthält, z. B. den Bereitstellungsstatus.
   
1. Wählen Sie zum Bereitstellen des manuellen Release die Stufe **Azure**, dann **Bereitstellen** und anschließend im Dialogfeld „Stufe“ die Option **Bereitstellen** aus. 
   
1. Wenn die Bereitstellung erfolgreich abgeschlossen wurde, öffnen Sie die bereitgestellte App in Ihrem Browser. Öffnen Sie für die Azure App Service-Website beispielsweise die URL `https://<your-app-name>.azurewebsites.net`.

### <a name="monitor-and-track-releases"></a>Überwachen und Nachverfolgen von Releases

Sie können den Link für den Status in einer Releasestufe auswählen, um weitere Informationen zur Bereitstellung anzuzeigen. 

![Seite „Releasezusammenfassung“](media/azure-stack-solution-pipeline/releasesummary.png)

So kann ein Administrator ganz einfach den Gesamtstatus aller Releases nachverfolgen und erkennen, bei welchen Releases eine Genehmigung aussteht.

![Seite mit der Releasezusammenfassung und ausstehenden Genehmigungen](media/azure-stack-solution-pipeline/releasepending.png)

Es werden die Releaseprotokolle aller Bereitstellungen angezeigt: 

1. Wählen Sie in Ihrem Azure DevOps-Projekt links **Pipelines** > **Releases** und dann ein Release aus. 
   
1. Zeigen Sie auf der Seite mit der Releasezusammenfassung auf eine Stufe, oder wählen Sie eine Stufe aus, und wählen Sie dann **Protokolle** aus. 
   
   Im Releaseprotokoll wird dann im linken Bereich der Status der jeweiligen Vorgänge für die einzelnen Stufen angezeigt. Während einer Bereitstellung wird im rechten Bereich das Liveprotokoll des Agents angezeigt. Nach Abschluss der Bereitstellung wird im rechten Bereich die gesamte Protokolldatei aufgeführt. 
   
1. Wählen Sie einen Schritt im linken Bereich aus, um die Protokolldatei für diesen Schritt (z. B. **Genehmigungen vor der Bereitstellung**) anzuzeigen. 
   
1. Um Genehmigungen anzuzeigen, wählen Sie im rechten Bereich **Genehmigung anzeigen** aus. Zusammen mit weiteren Details wird dann aufgeführt, wer das Release genehmigt oder abgelehnt hat. 
   
Dank der Möglichkeit zum Anzeigen der Protokolle für einzelne Schritte lassen sich Teile der Gesamtbereitstellung leichter nachverfolgen und debuggen. Sie können auch alle Protokolle als *ZIP*-Datei **herunterladen**.
   
![Releaseprotokoll](media/azure-stack-solution-pipeline/releaselog.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](/azure/architecture/patterns).
