---
title: Erstellen von cloudübergreifenden, skalierenden App-Lösungen mit Azure und Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie cloudübergreifende Skalierungslösungen mit Azure erstellen.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: eb5815a55e5e2c60ce61f9c4af96ee58a1aa684b
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138946"
---
# <a name="tutorial-create-cross-cloud-scaling-app-solutions-with-azure-and-azure-stack"></a>Tutorial: Erstellen von cloudübergreifenden, skalierenden App-Lösungen mit Azure und Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Es wird beschrieben, wie Sie eine cloudübergreifende Lösung erstellen, um einen manuell ausgelösten Prozess zum Umschalten von einer unter Azure Stack gehosteten Web-App zu einer unter Azure gehosteten Web-App mit automatischer Skalierung per Traffic Manager bereitzustellen. So wird sichergestellt, dass das Cloudhilfsprogramm auch bei hoher Last flexibel und skalierbar bleibt.

Bei diesem Muster ist Ihr Mandant ggf. noch nicht bereit für die Ausführung Ihrer App in der öffentlichen Cloud. Es ist für das Unternehmen aber unter Umständen nicht wirtschaftlich, die für die lokale Umgebung erforderliche Kapazität beizubehalten, um für die App Auslastungsspitzen verarbeiten zu können. Ihr Mandant kann die Elastizität der öffentlichen Cloud für die lokale Lösung nutzen.

In diesem Tutorial erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Erstellen Sie eine Web-App mit mehreren Knoten.
> - Konfigurieren und verwalten Sie den CD-Prozess (Continuous Deployment).
> - Veröffentlichen Sie die Web-App in Azure Stack.
> - Erstellen Sie ein Release.
> - Es wird beschrieben, wie Sie Ihre Bereitstellungen überwachen und nachverfolgen.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Whitepaper [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Entwurfsüberlegungen für Hybridanwendungen) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybrid-Apps, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.

## <a name="prerequisites"></a>Voraussetzungen

-   Azure-Abonnement. Erstellen Sie bei Bedarf ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

- Ein integriertes Azure Stack-System oder eine Bereitstellung des Azure Stack Development Kit.
    - Anleitungen zur Installation von Azure Stack finden Sie unter [Installieren des Azure Stack Development Kit](../asdk/asdk-install.md).
    - Ein Skript zur Automatisierung der Vorgänge nach der Bereitstellung des ASDK finden Sie hier: [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 
    - Es kann einige Stunden dauern, bis diese Installation abgeschlossen ist.

-   Stellen Sie PaaS-Dienste als [App Service](../operator/azure-stack-app-service-deploy.md) für Azure Stack bereit.

-   [Erstellen Sie Pläne/Angebote](../operator/azure-stack-plan-offer-quota-overview.md) in Ihrer Azure Stack-Umgebung.

-   [Erstellen Sie ein Mandantenabonnement](../operator/azure-stack-subscribe-plan-provision-vm.md) in Ihrer Azure Stack-Umgebung.

-   Erstellen Sie eine Web-App in Ihrem Mandantenabonnement. Notieren Sie sich die URL der neuen Web-App zur späteren Verwendung.

-   Stellen Sie einen virtuellen Azure Pipelines-Computer in Ihrem Mandantenabonnement bereit.

-   Es wird ein virtueller Windows Server 2016-Computer mit .NET 3.5 benötigt. Diese VM wird im Mandantenabonnement unter Azure Stack als privater Build-Agent erstellt.

-   Das [Image „Windows Server 2016 mit SQL 2017-VM“](../operator/azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) ist auf dem Azure Stack Marketplace verfügbar. Falls dieses Image nicht verfügbar sein sollte, können Sie einen Azure Stack-Bediener bitten, es der Umgebung hinzuzufügen.

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

### <a name="scalability"></a>Skalierbarkeit

Die wichtigste Komponente der cloudübergreifenden Skalierung ist die Möglichkeit, sofortige Skalierung bei Bedarf zwischen der öffentlichen und lokalen Cloudinfrastruktur bereitzustellen und so einen konsistenten und zuverlässigen Dienst bereitzustellen.

### <a name="availability"></a>Verfügbarkeit

Stellen Sie sicher, dass lokal bereitgestellte Apps in Bezug auf Hochverfügbarkeit konfiguriert sind, die auf der Konfiguration der lokalen Hardware und der Softwarebereitstellung basiert.

### <a name="manageability"></a>Verwaltbarkeit

Mit der cloudübergreifenden Lösung wird sichergestellt, dass zwischen Umgebungen eine nahtlose Verwaltung möglich ist und eine vertraute Benutzeroberfläche vorhanden ist. PowerShell wird für die plattformübergreifende Verwaltung empfohlen.

## <a name="cross-cloud-scaling"></a>Cloudübergreifende Skalierung

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Abrufen einer benutzerdefinierten Domäne und Konfigurieren des DNS

Aktualisieren Sie die DNS-Zonendatei für die Domäne. Azure AD überprüft die Eigentümerschaft für den Namen der benutzerdefinierten Domäne. Verwenden Sie [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) für Azure-/Office 365-/externe DNS-Einträge in Azure, oder fügen Sie den DNS-Eintrag bei einer [anderen DNS-Registrierungsstelle](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/) hinzu.

1.  Registrieren Sie eine benutzerdefinierte Domäne bei einer öffentlichen Registrierungsstelle.

2.  Melden Sie sich an der Domänennamen-Registrierungsstelle für die Domäne an. Unter Umständen ist ein genehmigter Administrator erforderlich, um die DNS-Updates durchzuführen.

3.  Aktualisieren Sie die DNS-Zonendatei für die Domäne, indem Sie den DNS-Eintrag hinzufügen, der von Azure AD bereitgestellt wurde. (Der DNS-Eintrag hat keinerlei Auswirkung auf das E-Mail-Routing oder das Webhosting-Verhalten.)

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Erstellen einer Standard-Web-App mit mehreren Knoten in Azure Stack

Richten Sie Hybrid-CI/CD (Continuous Integration/Continuous Deployment) ein, um Web-Apps unter Azure und Azure Stack bereitzustellen und Änderungen automatisch per Pushvorgang an beide Clouds zu übertragen.

> [!Note]  
> Azure Stack mit den passenden syndizierten Images für die Ausführung (Windows Server und SQL) und eine App Service-Bereitstellung sind erforderlich. Weitere Informationen finden Sie in der App Service-Dokumentation unter [Vor den ersten Schritten mit App Service in Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md).

### <a name="add-code-to-azure-repos"></a>Hinzufügen von Code zu Azure Repos

Azure Repos

1. Melden Sie sich bei Azure Repos mit einem Konto an, das über Berechtigungen zum Erstellen von Projekten in Azure Repos verfügt.

    Der hybride CI/CD-Ansatz kann sowohl für App-Code als auch für Infrastrukturcode verwendet werden. Verwenden Sie [Azure Resource Manager-Vorlagen](https://azure.microsoft.com/resources/templates/) für die Entwicklung von privaten und gehosteten Clouds.

    ![Herstellen einer Verbindung mit einem Projekt in Azure Repos](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Klonen Sie das Repository**, indem Sie die Standard-Web-App erstellen und öffnen.

    ![Klonen des Repos in der Azure-Web-App](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Erstellen einer eigenständigen Web-App-Bereitstellung für App Services in beiden Clouds

1.  Bearbeiten Sie die Datei **WebApplication.csproj**. Wählen Sie `Runtimeidentifier` aus, und fügen Sie `win10-x64` hinzu. (Weitere Informationen finden Sie in der Dokumentation zur [eigenständigen Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).) 

    ![Bearbeiten Projektdatei einer Web-App](media/azure-stack-solution-cloud-burst/image3.png)

2.  Checken Sie den Code in Azure Repos ein, indem Sie Team Explorer verwenden.

3.  Vergewissern Sie sich, dass der App-Code in Azure Repos eingecheckt wurde.

## <a name="create-the-build-definition"></a>Erstellen der Builddefinition

1. Melden Sie sich bei Azure Pipelines an, um sich zu vergewissern, dass Builddefinitionen erstellt werden können.

2. Fügen Sie den Code **-r win10-x64** hinzu. Diese Hinzufügung ist erforderlich, um eine eigenständige Bereitstellung mit .NET Core auszulösen.

    ![Hinzufügen von Code zur Web-App](media/azure-stack-solution-cloud-burst/image4.png)

3. Führen Sie den Buildvorgang aus. Der Buildvorgang für die [eigenständige Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht Artefakte, die in Azure und Azure Stack ausgeführt werden.

## <a name="use-an-azure-hosted-agent"></a>Verwenden eines gehosteten Azure-Agents

Mit einem gehosteten Build-Agent lassen sich in Azure Pipelines komfortabel Web-Apps erstellen und bereitstellen. Wartungsarbeiten und Upgrades werden automatisch von Microsoft Azure durchgeführt, was einen kontinuierlichen und ununterbrochenen Entwicklungszyklus ermöglicht.

### <a name="manage-and-configure-the-cd-process"></a>Verwalten und Konfigurieren des CD-Prozesses

Azure Pipelines und Azure DevOps Server bieten eine äußerst flexibel konfigurier- und verwaltbare Pipeline für Releases in mehreren Umgebungen (z.B. in Entwicklungs-, Staging-, Qualitätssicherungs- und Produktionsumgebungen) – einschließlich erforderlicher Genehmigungen in bestimmten Phasen.

## <a name="create-release-definition"></a>Erstellen einer Releasedefinition

1.  Klicken Sie auf der VSO-Seite im Abschnitt **Build und Release** auf der Registerkarte **Releases** auf die Schaltfläche mit dem **Pluszeichen**, um ein neues Release hinzuzufügen.

    ![Erstellen einer Releasedefinition](media/azure-stack-solution-cloud-burst/image5.png)

2. Wenden Sie die Vorlage „Azure App Service-Bereitstellung“ an.

   ![Anwenden der Bereitstellungsvorlage für Azure App Service](meDia/azure-stack-solution-cloud-burst/image6.png)

3. Fügen Sie unter **Artefakt hinzufügen** das Artefakt für die Azure Cloud-Build-App hinzu.

   ![Hinzufügen des Artefakts zum Azure Cloud-Build](media/azure-stack-solution-cloud-burst/image7.png)

4. Klicken Sie auf der Registerkarte „Pipeline“ auf den Link **Phase, Aufgabe** der Umgebung, und legen Sie die Azure Cloud-Umgebungswerte fest.

   ![Festlegen der Werte für die Azure Cloud-Umgebung](media/azure-stack-solution-cloud-burst/image8.png)

5. Legen Sie den **Umgebungsnamen** fest, und wählen Sie das **Azure-Abonnement** für den Azure Cloud-Endpunkt aus.

      ![Auswählen des Azure-Abonnements für den Azure Cloud-Endpunkt](media/azure-stack-solution-cloud-burst/image9.png)

6. Legen Sie unter **App Service-Name** den erforderlichen Namen des Azure App Service fest.

      ![Festlegen des Azure App Service-Namens](media/azure-stack-solution-cloud-burst/image10.png)

7. Geben Sie unter **Agent-Warteschlange** für die gehostete Azure Cloud-Umgebung die Zeichenfolge „Hosted VS2017“ ein.

      ![Festlegen der Agent-Warteschlange für die gehostete Azure Cloud-Umgebung](media/azure-stack-solution-cloud-burst/image11.png)

8. Wählen Sie im Menü „Deploy Azure App Service“ (Azure App Service bereitstellen) **das gültige Paket oder den gültigen Ordner** für die Umgebung aus. Klicken Sie für den **Ordnerspeicherort** auf **OK**.
  
      ![Auswählen des Pakets oder Ordners für die Azure App Service-Umgebung](media/azure-stack-solution-cloud-burst/image12.png)

      ![Auswählen des Pakets oder Ordners für die Azure App Service-Umgebung](media/azure-stack-solution-cloud-burst/image13.png)

9. Speichern Sie alle Änderungen, und kehren Sie zur **Releasepipeline** zurück.

    ![Speichern der Änderungen in der Releasepipeline](media/azure-stack-solution-cloud-burst/image14.png)

10. Fügen Sie ein neues Artefakt hinzu, und wählen Sie dabei den Build für die Azure Stack-App aus.
    
    ![Hinzufügen eines neuen Artefakts für die Azure Stack-App](media/azure-stack-solution-cloud-burst/image15.png)


11. Fügen Sie mindestens eine Umgebung hinzu, indem Sie die Azure App Service-Bereitstellung anwenden.
    
    ![Hinzufügen einer Umgebung zur Azure App Service-Bereitstellung](media/azure-stack-solution-cloud-burst/image16.png)

12. Nennen Sie die neue Umgebung „Azure Stack“.
    
    ![Benennen der Umgebung in der Azure App Service-Bereitstellung](media/azure-stack-solution-cloud-burst/image17.png)

13. Suchen Sie auf der Registerkarte **Aufgabe** nach der Umgebung „Azure Stack“.
    
    ![Azure Stack-Umgebung](media/azure-stack-solution-cloud-burst/image18.png)

14. Wählen Sie das Abonnement für den Azure Stack-Endpunkt aus.
    
    ![Auswählen des Abonnements für den Azure Stack-Endpunkt](media/azure-stack-solution-cloud-burst/image19.png)

15. Legen Sie den Namen der Azure Stack-Web-App als App Service-Name fest.

    ![Festlegen des Namens der Azure Stack-Web-App](media/azure-stack-solution-cloud-burst/image20.png)

16. Wählen Sie den Azure Stack-Agent aus.
    
    ![Auswählen des Azure Stack-Agents](media/azure-stack-solution-cloud-burst/image21.png)

17. Wählen Sie im Abschnitt „Deploy Azure App Service“ (Azure App Service bereitstellen) **das gültige Paket oder den gültigen Ordner** für die Umgebung aus. Klicken Sie für den Ordnerspeicherort auf **OK**.

    ![Wählen Sie den Ordner für die Azure App Service-Bereitstellung aus.](media/azure-stack-solution-cloud-burst/image22.png)

    ![Wählen Sie den Ordner für die Azure App Service-Bereitstellung aus.](media/azure-stack-solution-cloud-burst/image23.png)

18. Fügen Sie auf der Registerkarte „Variable“ eine Variable mit dem Namen `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` hinzu, und legen Sie ihren Wert auf **true** und den Bereich auf „Azure Stack“ fest.
    
    ![Hinzufügen einer Variablen zur Azure App-Bereitstellung](media/azure-stack-solution-cloud-burst/image24.png)

19. Klicken Sie bei beiden Artefakten auf das Symbol **Continuous Deployment-Trigger**, und aktivieren Sie den Trigger für **Continuous Deployment**.
    
    ![Festlegen des Continuous Deployment-Triggers](media/azure-stack-solution-cloud-burst/image25.png)

20. Klicken Sie für die Azure Stack-Umgebung auf das Symbol **Bedingungen vor der Bereitstellung**, und legen Sie den Trigger auf **Nach einem Release** fest.
    
    ![Festlegen der Bedingungen vor der Bereitstellung](media/azure-stack-solution-cloud-burst/image26.png)

21. Speichern Sie alle Änderungen.

> [!Note]  
> Bei der vorlagenbasierten Erstellung einer Releasedefinition wurden einige Einstellungen für die Aufgaben unter Umständen automatisch als [Umgebungsvariablen](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) definiert. Diese Einstellungen können in den Aufgabeneinstellungen nicht geändert werden. Zum Bearbeiten dieser Einstellungen müssen Sie das übergeordnete Umgebungselement auswählen.

## <a name="publish-to-azure-stack-via-visual-studio"></a>Veröffentlichen in Azure Stack mit Visual Studio

Durch die Erstellung von Endpunkten kann ein Visual Studio Online-Build Azure Service-Apps in Azure Stack bereitstellen. Azure Pipelines stellt eine Verbindung mit dem Build-Agent her, der wiederum eine Verbindung mit Azure Stack herstellt.

1.  Melden Sie sich bei VSTO an, und navigieren Sie zur Seite mit den App-Einstellungen.

2.  Wählen Sie unter **Einstellungen** die Option **Sicherheit**.

3.  Klicken Sie unter **VSTS-Gruppen** auf **Endpunktersteller**.

4.  Klicken Sie auf der Registerkarte **Mitglieder** auf **Hinzufügen**.

5.  Geben Sie unter **Benutzer und Gruppen hinzufügen** einen Benutzernamen ein, und wählen Sie den Benutzer aus der Liste der Benutzer aus.

6.  Klicken Sie auf **Save changes** (Änderungen speichern).

7.  Wählen Sie in der Liste **VSTS-Gruppen** die Option **Endpunktadministratoren** aus.

8.  Klicken Sie auf der Registerkarte **Mitglieder** auf **Hinzufügen**.

9.  Geben Sie unter **Benutzer und Gruppen hinzufügen** einen Benutzernamen ein, und wählen Sie den Benutzer aus der Liste der Benutzer aus.

10. Klicken Sie auf **Save changes** (Änderungen speichern).

Die Endpunktinformationen sind vorhanden, und die Verbindung zwischen Azure Pipelines und Azure Stack kann nun verwendet werden. Der Build-Agent in Azure Stack erhält Anweisungen von Azure Pipelines. Daraufhin übermittelt der Agent Endpunktinformationen für die Kommunikation mit Azure Stack.

## <a name="develop-the-application-build"></a>Entwickeln des Anwendungsbuilds

> [!Note]  
> Azure Stack mit den passenden syndizierten Images für die Ausführung (Windows Server und SQL) und eine App Service-Bereitstellung sind erforderlich. Weitere Informationen finden Sie in der App Service-Dokumentation unter [Vor den ersten Schritten mit App Service in Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md).

Verwenden Sie [Azure Resource Manager-Vorlagen](https://azure.microsoft.com/resources/templates/), z. B. Web-App-Code aus Azure Repos, für die Bereitstellung in beiden Clouds.

### <a name="add-code-to-an-azure-repos-project"></a>Hinzufügen von Code zu einem Azure Repos-Projekt

1.  Melden Sie sich bei Azure Repos mit einem Konto an, das über Berechtigungen zum Erstellen von Projekten in Azure Stack verfügt. Der nächste Screenshot zeigt, wie Sie eine Verbindung mit dem HybridCICD-Projekt herstellen.

2.  **Klonen Sie das Repository**, indem Sie die Standard-Web-App erstellen und öffnen.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Erstellen einer eigenständigen Web-App-Bereitstellung für App Services in beiden Clouds

1.  Bearbeiten Sie die Datei **WebApplication.csproj**: Wählen Sie `Runtimeidentifier` aus, und fügen Sie dann `win10-x64` hinzu. Weitere Informationen finden Sie in der Dokumentation zur [eigenständigen Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).

2.  Checken Sie den Code über den Team Explorer in Azure Repos ein.

3.  Vergewissern Sie sich, dass der App-Code in Azure Repos eingecheckt wurde.

### <a name="create-the-build-definition"></a>Erstellen der Builddefinition

1.  Melden Sie sich bei Azure Pipelines mit einem Konto an, mit dem eine Builddefinition erstellt werden kann.

2.  Navigieren Sie zur Seite **Build Web Application** (Webanwendung erstellen) für das Projekt.

3.  Fügen Sie unter **Argumente** den Code **-r win10-x64** hinzu. Diese Hinzufügung ist erforderlich, um eine eigenständige Bereitstellung mit .NET Core auszulösen.

4.  Führen Sie den Buildvorgang aus. Der Buildvorgang für die [eigenständige Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht Artefakte, die in Azure und Azure Stack ausgeführt werden können.

#### <a name="use-an-azure-hosted-build-agent"></a>Verwenden eines in Azure gehosteten Build-Agents

Mit einem gehosteten Build-Agent lassen sich in Azure Pipelines komfortabel Web-Apps erstellen und bereitstellen. Wartungsarbeiten und Upgrades werden automatisch von Microsoft Azure durchgeführt, was einen kontinuierlichen und ununterbrochenen Entwicklungszyklus ermöglicht.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurieren des CD-Prozesses (Continuous Deployment)

Azure Pipelines und Azure DevOps Server verfügen über eine äußerst flexibel konfigurier- und verwaltbare Pipeline für Releases in mehreren Umgebungen (z.B. in Entwicklungs-, Staging-, Qualitätssicherungs- und Produktionsumgebungen). Dabei sind ggf. Genehmigungen in bestimmten Phasen des Anwendungslebenszyklus erforderlich.

#### <a name="create-release-definition"></a>Erstellen einer Releasedefinition

Die Erstellung einer Releasedefinition ist der letzte Schritt im Anwendungsbuildprozess. Diese Releasedefinition wird zum Erstellen eines Release und zum Bereitstellen eines Builds verwendet.

1.  Melden Sie sich an Azure Pipelines an, und navigieren Sie für das Projekt zu **Build und Release**.

2.  Klicken Sie auf der Registerkarte **Releases** auf **[ + ]** , und wählen Sie dann **Releasedefinition erstellen**.

3.  Klicken Sie unter **Vorlage auswählen** auf **Azure App Service-Bereitstellung** und dann auf **Anwenden**.

4.  Wählen Sie unter **Artefakt hinzufügen** und **Quelle (Builddefinition)** die Azure Cloud-Build-App aus.

5.  Klicken Sie auf der Registerkarte **Pipeline** auf den Link **1 Phase**, **1 Task** (1 Phase, 1 Aufgabe), um **Umgebungsaufgaben anzuzeigen**.

6.  Geben Sie auf der Registerkarte **Aufgaben** unter **Umgebungsname** den Namen „Azure“ ein, und wählen Sie in der Liste **Azure-Abonnement** den Eintrag „AzureCloud Traders-Web EP“ aus.

7.  Geben Sie den **Azure App Service-Namen** ein. Im nächsten Screenshot ist dies `northwindtraders`.

8.  Wählen Sie unter „Agent-Phase“ in der Liste **Agent-Warteschlange** den Eintrag **Hosted VS2017** aus.

9.  Wählen Sie im Menü **Deploy Azure App Service** (Azure App Service bereitstellen) unter **Paket oder Ordner** den gültigen Eintrag für die Umgebung aus.

10. Klicken Sie unter **Datei oder Ordner auswählen** auf **OK**, um den **Speicherort** anzugeben.

11. Speichern Sie alle Änderungen, und kehren Sie zur **Pipeline** zurück.

12. Klicken Sie auf der Registerkarte **Pipeline** auf **Artefakt hinzufügen**, und wählen Sie in der Liste **Quelle (Builddefinition)** den Eintrag **NorthwindCloud Traders-Vessel** aus.

13. Fügen Sie unter **Vorlage auswählen** eine andere Umgebung aus. Wählen Sie **Azure App Service-Bereitstellung** und dann **Anwenden** aus.

14. Geben Sie `Azure Stack` als **Umgebungsnamen** ein.

15. Suchen Sie auf der Registerkarte **Aufgaben** nach „Azure Stack“.

16. Wählen Sie in der Liste **Azure-Abonnement** als Azure Stack-Endpunkt **AzureStack Traders-Vessel EP** aus.

17. Geben Sie unter **App Service-Name** den Namen der Azure Stack-Web-App ein.

18. Wählen Sie unter **Agent-Auswahl** in der Liste **Agent-Warteschlange** den Eintrag **AzureStack -b Douglas Fir** aus.

19. Wählen Sie für **Deploy Azure App Service** (Azure App Service bereitstellen) unter **Paket oder Ordner** den gültigen Eintrag für die Umgebung aus. Klicken Sie unter **Datei oder Ordner auswählen** für den **Speicherort** des Ordners auf **OK**.

20. Suchen Sie auf der Registerkarte **Variable** nach der Variablen mit dem Namen `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Legen Sie den Wert der Variablen auf **true** und ihren Bereich auf **Azure Stack** fest.

21. Klicken Sie auf der Registerkarte **Pipeline** für das Artefakt „NorthwindCloud Traders-Web“ auf **Continuous Deployment-Trigger**, und legen Sie **Continuous Deployment-Trigger** auf **Aktiviert** fest. Gehen Sie für das Artefakt **NorthwindCloud Traders-Vessel** genauso vor.

22. Klicken Sie für die Azure Stack-Umgebung auf das Symbol **Bedingungen vor der Bereitstellung**, und legen Sie den Trigger auf **Nach einem Release** fest.

23. Speichern Sie alle Änderungen.

> [!Note]  
> Bei der vorlagenbasierten Erstellung einer Releasedefinition werden einige Einstellungen für die Releaseaufgaben unter Umständen automatisch als [Umgebungsvariablen](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) definiert. Diese Einstellungen können nicht in den Aufgabeneinstellungen geändert werden, sondern nur in den übergeordneten Umgebungselementen.

## <a name="create-a-release"></a>Erstellen eines Release

1.  Öffnen Sie auf der Registerkarte **Pipeline** die Liste **Release**, und wählen Sie **Release erstellen**.

2.  Geben Sie eine Beschreibung für das Release ein, vergewissern Sie sich, dass die korrekten Artefakte ausgewählt sind, und wählen Sie anschließend **Erstellen**. Kurz darauf erscheint ein Banner mit dem Hinweis, dass das neue Release erstellt wurde, und der Releasename wird als Link angezeigt. Wählen Sie den Link aus, um die Zusammenfassungsseite des Release anzuzeigen.

3.  Die Zusammenfassungsseite enthält Details zum Release. Im folgenden Screenshot für „Release-2“ wird im Abschnitt **Umgebungen** als **Bereitstellungsstatus** für Azure „IN BEARBEITUNG“ und als Status für Azure Stack „SUCCEEDED“ (ERFOLGREICH) angezeigt. Wenn sich der Bereitstellungsstatus für die Azure-Umgebung in „SUCCEEDED“ (ERFOLGREICH) ändert, wird ein Banner mit dem Hinweis angezeigt, dass das Release nun genehmigt werden kann. Wenn eine Bereitstellung noch aussteht oder nicht erfolgreich war, wird ein blaues Informationssymbol **(i)** angezeigt. Zeigen Sie mit der Maus auf das Symbol, um ein Popupfenster mit dem Grund für die Verzögerung oder den Fehler anzuzeigen.

4.  In anderen Ansichten (etwa in der Liste mit den Releases) wird auch ein Symbol angezeigt, das darauf hinweist, dass die Genehmigung aussteht. Das Popupfenster für dieses Symbol enthält den Umgebungsnamen und weitere Details zur Bereitstellung. So kann sich ein Administrator ganz einfach über den Gesamtstatus aller Releases informieren und erkennen, bei welchen Releases eine Genehmigung aussteht.

## <a name="monitor-and-track-deployments"></a>Überwachen und Nachverfolgen von Bereitstellungen

1.  Klicken Sie auf der Zusammenfassungsseite **Release-2** auf **Protokolle**. Während einer Bereitstellung wird auf dieser Seite das Liveprotokoll des Agents angezeigt. Im linken Bereich wird der Status der einzelnen Vorgänge in der Bereitstellung für die jeweilige Umgebung angezeigt.

2.  Wählen Sie zum Anzeigen von Informationen zur Genehmigung vor oder nach der Bereitstellung das Personensymbol in der Spalte **Aktion**. So können Sie ermitteln, wer die Bereitstellung genehmigt (oder abgelehnt) hat und welche Nachricht vorhanden ist.

3.  Nach Abschluss der Bereitstellung wird im rechten Bereich die gesamte Protokolldatei angezeigt. Wählen Sie einen beliebigen **Schritt** im linken Bereich aus, um die Protokolldatei für einen einzelnen Schritt (z.B. **Auftrag initialisieren**) anzuzeigen. Dank der Möglichkeit zum Anzeigen einzelner Protokolle lassen sich Teile der Gesamtbereitstellung leichter nachverfolgen und debuggen. **Speichern** Sie die Protokolldatei für einen Schritt, oder verwenden Sie die Option **Alle Protokolle als ZIP-Datei herunterladen**.

4.  Öffnen Sie die Registerkarte **Zusammenfassung**, um allgemeine Informationen zum Release anzuzeigen. In dieser Ansicht werden die Details zum Build, die Umgebungen, in denen er bereitgestellt wurde, Bereitstellungsstatus und andere Informationen zum Release angezeigt.

5.  Klicken Sie auf den Link für eine Umgebung (**Azure** oder **Azure Stack**), um Informationen zu vorhandenen und ausstehenden Bereitstellungen für eine bestimmte Umgebung anzuzeigen. Mithilfe dieser Ansichten können Sie sich schnell vergewissern, dass der gleiche Build in beiden Umgebungen bereitgestellt wurde.

6.  Öffnen Sie die **bereitgestellte Produktions-App** in einem Browser. Öffnen Sie für die Azure App Service-Website beispielsweise die URL `https://[your-app-name\].azurewebsites.net`.

**Integration von Azure und Azure Stack ergibt eine skalierbare cloudübergreifende Lösung**

Ein flexibler und stabiler Dienst für mehrere Clouds ermöglicht Datensicherheit, Sicherung und Redundanz, konsistente und schnelle Verfügbarkeit, skalierbare Speicherung und Verteilung sowie geokonformes Routing. Mit diesem manuell ausgelösten Prozess wird das zuverlässige und effiziente Umschalten der Last zwischen gehosteten Web-Apps und die sofortige Verfügbarkeit wichtiger Daten sichergestellt.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
