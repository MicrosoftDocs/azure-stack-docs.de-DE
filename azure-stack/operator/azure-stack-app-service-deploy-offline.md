---
title: Bereitstellen von App Service in einer Offlineumgebung in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie App Service in einer durch AD FS gesicherten Offlineumgebung in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 9190a8cb0b42b10710a4abc2671fe8ead3094d9a
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808295"
---
# <a name="deploy-app-service-in-an-offline-environment-in-azure-stack"></a>Bereitstellen von App Service in einer Offlineumgebung in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!IMPORTANT]
> Wenden Sie Update 1907 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.7 bereit.

Mithilfe der Anweisungen in diesem Artikel können Sie den [App Service-Ressourcenanbieter](azure-stack-app-service-overview.md) in einer Azure Stack-Umgebung bereitstellen, für die Folgendes gilt:

- Sie ist nicht mit dem Internet verbunden.
- Sie wird mit Active Directory Federation Services (AD FS) geschützt.

> [!IMPORTANT]
> Stellen Sie vor dem Ausführen des Installationsprogramms für den Ressourcenanbieter sicher, dass Sie die Schritte unter [Voraussetzungen für die Bereitstellung von App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md) abgeschlossen haben. Lesen Sie auch die [Versionshinweise](azure-stack-app-service-release-notes-update-seven.md) zur Version 1.7, um weitere Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen zu erhalten, die sich auf die Bereitstellung auswirken können.

Um Ihrer Azure Stack-Offlinebereitstellung den App Service-Ressourcenanbieter hinzuzufügen, müssen Sie die folgenden wichtigen Aufgaben ausführen:

1. Führen Sie die [vorausgesetzten Schritte](azure-stack-app-service-before-you-get-started.md) aus (z.B. den Erwerb von Zertifikaten, der einige Tage dauern kann).
2. [Laden Sie die Installations- und Hilfsdateien herunter, und extrahieren Sie sie](azure-stack-app-service-before-you-get-started.md) auf einem Computer, der mit dem Internet verbunden ist.
3. Erstellen Sie ein Offlineinstallationspaket.
4. Führen Sie das Installationsprogramm „appservice.exe“ aus.

## <a name="create-an-offline-installation-package"></a>Erstellen eines Offlineinstallationspakets

Um App Service in einer Offlineumgebung bereitzustellen, müssen Sie zunächst ein Offlineinstallationspaket auf einem Computer erstellen, der über eine Internetverbindung verfügt.

1. Führen Sie das Installationsprogramm „AppService.exe“ auf einem Computer aus, der mit dem Internet verbunden ist.

2. Klicken Sie auf **Erweitert** > **Offlineinstallationspaket erstellen**.

    ![Erstellen eines Offlinepakets im App Service-Installationsprogramm][1]

3. Das App Service-Installationsprogramm erstellt ein Offlineinstallationspaket und zeigt den Pfad zu diesem an. Klicken Sie auf **Ordner öffnen**, um den Ordner im Datei-Explorer zu öffnen.

    ![Das Offlineinstallationspaket wurde im App Service-Installationsprogramm erfolgreich generiert.](media/azure-stack-app-service-deploy-offline/image02.png)

4. Kopieren Sie das Installationsprogramm „AppService.exe“ und das Offlineinstallationspaket auf den Azure Stack-Hostcomputer.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Abschließen der Offlineinstallation von App Service in Azure Stack

1. Führen Sie „appservice.exe“ als Administrator auf einem Computer aus, der den Azure-Ressourcenverwaltungsendpunkt der Azure Stack-Verwaltung erreichen kann.

2. Klicken Sie auf **Erweitert** > **Offlineinstallation abschließen**.

    ![Abschließen der Offlineinstallation im App Service-Installationsprogramm][2]

3. Navigieren Sie zum Speicherort des zuvor erstellten Offlineinstallationspakets, und klicken Sie dann auf **Weiter**.

    ![Angeben des Pfads für das Offlineinstallationspaket im App Service-Installationsprogramm](media/azure-stack-app-service-deploy-offline/image04.png)

4. Lesen und akzeptieren Sie die Lizenzbedingungen von Microsoft-Software, und klicken Sie dann auf **Weiter**.

5. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und klicken Sie dann auf **Weiter**.

6. Stellen Sie sicher, dass die Informationen für die App Service-Cloudkonfiguration korrekt sind. Wenn Sie während der ASDK-Bereitstellung die Standardeinstellungen verwendet haben, können Sie hier die Standardwerte übernehmen. Wenn Sie die Optionen bei der Bereitstellung von Azure Stack jedoch angepasst haben oder in einem integrierten System bereitstellen, müssen Sie die Werte in diesem Fenster entsprechend anpassen. Wenn Sie beispielsweise das Domänensuffix „mycloud.com“ verwenden, muss der Azure Resource Manager-Endpunkt des Azure Stack-Mandanten in `management.<region>.mycloud.com` geändert werden. Nachdem Sie Ihre Informationen bestätigt haben, klicken Sie auf **Weiter**.

    ![Konfigurieren der Azure App Service-Cloud im App Service-Installationsprogramm][3]

7. Auf der nächsten Seite:

   1. Klicken Sie neben dem Feld **Azure Stack-Abonnements** auf die Schaltfläche **Verbinden**. 

   2. Geben Sie Ihr Administratorkonto an. Beispiel: cloudadmin@azurestack.local. Geben Sie Ihr Kennwort ein, und klicken Sie dann auf **Anmelden**.

   3. Wählen Sie im Feld **Azure Stack-Abonnements** das **Standardabonnement des Anbieters** aus.

      > [!NOTE]
      > App Service kann nur im **Abonnement für Standardanbieter** bereitgestellt werden.

   4. Wählen Sie im Feld **Azure Stack-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.

   5. Klicken Sie auf **Weiter**.

      ![Azure Stack-Abonnements und -Speicherorte im App Service-Installationsprogramm][4]

8. Sie können die Bereitstellung in einem vorhandenen virtuellen Netzwerk ausführen, das mit den [hier](azure-stack-app-service-before-you-get-started.md#virtual-network) aufgeführten Schritten konfiguriert wurde, oder Sie können zulassen, dass das App Service-Installationsprogramm ein virtuelles Netzwerk und zugehörige Subnetze erstellt.
   - Wählen Sie **Create VNet with default settings** (VNET mit Standardeinstellungen erstellen), übernehmen Sie die Standardwerte, und klicken Sie dann auf **Weiter**, oder;
   - Wählen Sie **Use existing VNet and Subnets** (Vorhandenes VNET und zugehörige Subnetze verwenden).
       1. Wählen Sie die **Ressourcengruppe** aus, die Ihr virtuelles Netzwerk enthält;
       2. Wählen Sie den richtigen Namen des **virtuellen Netzwerks** aus, in dem Sie bereitstellen möchten;
       3. Wählen Sie die richtigen **Subnetz**werte für die einzelnen erforderlichen Rollensubnetze aus;
       4. Klicken Sie auf **Weiter**.

      ![Informationen zum virtuellen Netzwerk und zum Subnetz im App Service-Installationsprogramm][5]

9. Geben Sie die Informationen für die Dateifreigabe ein, und klicken Sie dann auf **Weiter**. Die Adresse der Dateifreigabe muss den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse Ihres Dateiservers enthalten. Beispiel: \\„\appservicefileserver.local.cloudapp.azurestack.external\websites“ oder \\„\10.0.0.1\websites“.  Wenn Sie einen Dateiserver verwenden, der in eine Domäne eingebunden ist, müssen Sie den vollständigen Benutzernamen mit der Domäne eingeben. Beispiel: myfileserverdomain\FileShareOwner.

    > [!NOTE]
    > Das Installationsprogramm versucht, die Konnektivität mit der Dateifreigabe zu testen, bevor es fortgesetzt wird. Wenn Sie sich jedoch für die Bereitstellung in einem vorhandenen virtuellen Netzwerk entschieden haben, kann das Installationsprogramm möglicherweise keine Verbindung mit der Dateifreigabe herstellen, und es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Überprüfen Sie die Dateifreigabeinformationen, und fahren Sie fort, wenn diese richtig sind.

   ![Dateifreigabeinformationen im App Service-Installationsprogramm][8]

10. Auf der nächsten Seite:
    1. Geben Sie im Feld **Identitätsanwendungs-ID** die GUID der App ein, die Sie für die Identität verwenden (aus Azure AD).
    2. Geben Sie im Feld **Identitätsanwendungs-Zertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).
    3. Geben Sie im Feld **Kennwort für das Identitätsanwendungszertifikat** das Kennwort für das Zertifikat ein. Dieses Kennwort haben Sie erstellt, als Sie das Skript zum Erstellen des Zertifikats verwendet haben.
    4. Geben Sie im Feld **Azure Resource Manager-Stammzertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).
    5. Klicken Sie auf **Weiter**.

    ![Eingeben der App-ID und der Zertifikatinformationen im App Service-Installationsprogramm][10]

11. Klicken Sie für jede der drei Zertifikatsdateien auf **Durchsuchen**, und navigieren Sie dann zu der entsprechenden Zertifikatsdatei. Sie müssen das Kennwort für jedes Zertifikat angeben. Diese Zertifikate haben Sie im Schritt [Erstellen der erforderlichen Zertifikate](azure-stack-app-service-before-you-get-started.md#get-certificates) erstellt. Klicken Sie auf **Weiter**, nachdem Sie alle Informationen eingegeben haben.

    | Box | Beispielname für eine Zertifikatsdatei |
    | --- | --- |
    | **Standard-SSL-Zertifikatsdatei für App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-API** | api.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-Herausgeber** | ftp.appservice.local.AzureStack.external.pfx |

    Wenn Sie beim Erstellen der Zertifikate ein anderes Domänensuffix verwendet haben, verwenden die Zertifikatsdateinamen nicht *local.AzureStack.external*. Verwenden Sie stattdessen Ihre benutzerdefinierten Domäneninformationen.

    ![Eingeben der SSL-Zertifikatinformationen im App Service-Installationsprogramm][11]

12. Geben Sie die SQL Server-Informationen für die Serverinstanz ein, auf der die Datenbanken des App Service-Ressourcenanbieters gehostet werden sollen, und klicken Sie dann auf **Weiter**. Das Installationsprogramm überprüft die SQL-Verbindungseigenschaften. Sie **müssen** entweder die interne IP-Adresse oder den FQDN für den SQL Server-Namen eingeben.

    > [!NOTE]
    > Das Installationsprogramm versucht, die Konnektivität mit SQL Server zu testen, bevor es fortgesetzt wird. Wenn Sie sich jedoch für die Bereitstellung in einem vorhandenen virtuellen Netzwerk entschieden haben, kann das Installationsprogramm möglicherweise keine Verbindung mit SQL Server herstellen, und es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Überprüfen Sie die SQL Server-Informationen, und fahren Sie fort, wenn diese richtig sind.
    >
    > Ab Azure App Service in Azure Stack 1.3 überprüft das Installationsprogramm, ob die Datenbankeigenständigkeit für SQL Server auf SQL Server-Ebene aktiviert ist. Wenn dies nicht der Fall ist, wird Ihnen die folgende Ausnahme angezeigt:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Weitere Informationen finden Sie in den [Versionshinweisen für Azure App Service in Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md).

    ![Eingeben von SQL Server-Informationen im App Service-Installationsprogramm][12]

13. Überprüfen Sie die Optionen für Rolleninstanz und SKU. Als Standardwerte werden die Mindestzahl der Instanzen und die Mindest-SKU für jede Rolle in einer ASDK-Bereitstellung verwendet. Eine Übersicht über die vCPU- und Arbeitsspeichervoraussetzungen wird angezeigt, um Sie bei der Bereitstellung zu unterstützen. Nachdem Sie Ihre Auswahl getroffen haben, klicken Sie auf **Weiter**.

     > [!NOTE]
     > Befolgen Sie bei Produktionsbereitstellungen die Anweisungen unter [Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Mindestanzahl der Instanzen | Mindest-SKU | Notizen |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Verwaltet und wartet die Integrität der App Service-Cloud |
    | Verwaltung | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Verwaltet die Azure Resource Manager- und API-Endpunkte, die Portalerweiterungen (Administrator-, Mandanten, Functions-Portal) und den Datendienst von App Service. Zur Unterstützung eines Failovers erhöhen Sie die empfohlenen Instanzen auf 2. |
    | Herausgeber | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Veröffentlicht Inhalte per FTP oder Webbereitstellung |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Leitet Anforderungen an App Service-Apps weiter. |
    | Freigegebener Worker | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hostet Web- oder API-Apps und Azure Functions-Apps. Sie können ggf. weitere Instanzen hinzufügen. Als Operator können Sie Ihr Angebot definieren und eine SKU-Ebene auswählen. Die Ebenen müssen mindestens eine vCPU aufweisen. |

    ![Festlegen von Rollenebenen und SKU-Optionen im App Service-Installationsprogramm][14]

    > [!NOTE]
    > Windows Server 2016 Core ist *kein* unterstütztes Plattformimage für die Verwendung mit Azure App Service in Azure Stack.  Verwenden Sie Auswertungsimages nicht für Produktionsbereitstellungen. Für Azure App Service in Azure Stack wird vorausgesetzt, dass im für die Bereitstellung verwendeten Image Microsoft .NET 3.5.1 SP1 aktiviert ist.  Bei über den Marketplace bezogenen Windows Server 2016-Images ist dieses Feature nicht aktiviert. Daher müssen Sie ein Windows Server 2016-Image erstellen und verwenden, bei dem es bereits aktiviert ist.

14. Wählen Sie im Feld **Plattformimage auswählen** aus den VM-Images, die im Computeressourcenanbieter für die App Service-Cloud verfügbar sind, Ihr VM-Bereitstellungsimage für Windows Server 2016 aus. Klicken Sie auf **Weiter**.

15. Auf der nächsten Seite:
     1. Geben Sie den Administratorbenutzernamen und das Kennwort für die VM mit der Workerrolle ein.
     2. Geben Sie den Administratorbenutzernamen und das Kennwort für VMs mit anderen Rollen ein.
     3. Klicken Sie auf **Weiter**.

    ![Eingeben der Administratordaten für Rollen-VMs im App Service-Installationsprogramm][16]

16. Auf der Zusammenfassungsseite:
    1. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.
    2. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.
    3. Um die Bereitstellung zu starten, klicken Sie auf **Weiter**.

    ![Zusammenfassung der im App Service-Installationsprogramm getroffenen Auswahl][17]

17. Auf der nächsten Seite:
    1. Verfolgen Sie den Installationsstatus nach. Bei Auswahl der Standardoptionen dauert die Bereitstellung von App Service in Azure Stack ca. 60 Minuten.
    2. Klicken Sie nach erfolgreichem Abschluss des Installationsprogramms auf **Beenden**.

    ![Nachverfolgen des Installationsvorgangs im App Service-Installationsprogramm][18]

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, *müssen* Sie die [Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zur Netzwerksicherheitsgruppe „WorkersNsg“, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

- Quelle: Any
- Quellportbereich: *
- Ziel: IP-Adressen
- IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
- Zielportbereich: 445
- Protokoll: TCP
- Aktion: ZULASSEN
- Priorität: 700
- Name: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Überprüfen der Installation von App Service in Azure Stack

1. Navigieren Sie im Azure Stack-Verwaltungsportal zu **Verwaltung – App Service**.

2. Überprüfen Sie in der Übersicht unter „Status“, ob für **Status** die Option **Alle Rollen sind bereit** angezeigt wird.

    ![Übersicht in der App Service-Verwaltung](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>Testen von App Service in Azure Stack

Nachdem Sie den App Service-Ressourcenanbieter bereitgestellt und registriert haben, testen Sie ihn, um sicherzustellen, dass Benutzer Web- und API-Apps bereitstellen können.

> [!NOTE]
> Sie müssen ein Angebot erstellen, das den Namespace „Microsoft.Web“ im Plan enthält. Danach benötigen Sie ein Mandantenabonnement, das dieses Angebot abonniert. Weitere Informationen finden Sie unter [Erstellen eines Angebots](azure-stack-create-offer.md) und [Erstellen eines Plans](azure-stack-create-plan.md).
>
> Sie *müssen* über ein Mandantenabonnement verfügen, um Apps zu erstellen, die App Service in Azure Stack verwenden. Ein Dienstadministrator kann nur solche Funktionen im Administratorportal ausführen, die mit der Ressourcenanbieterverwaltung von App Service zu tun haben. Hierzu gehören das Hinzufügen von Kapazitäten, das Konfigurieren von Bereitstellungsquellen und das Hinzufügen von Workerebenen und SKUs.
>
> Seit der dritten Technical Preview müssen Sie das Mandantenportal verwenden und über ein Mandantenabonnement verfügen, um Web-, API- und Azure Functions-Apps zu erstellen.

1. Klicken Sie im Azure Stack-Mandantenportal auf **+Ressource erstellen** > **Web + Mobil** > **Web-App**.

2. Geben Sie auf dem Blatt **Web-App** einen Namen in das Feld **Web-App** ein.

3. Klicken Sie unter **Ressourcengruppe** auf **Neu**. Geben Sie einen Namen in das Feld **Ressourcengruppe** ein.

4. Klicken Sie auf **App Service-Plan/Standort** > **Neu erstellen**.

5. Geben Sie auf dem Blatt **App Service-Plan** einen Namen in das Feld **App Service-Plan** ein.

6. Klicken Sie auf **Tarif** > **Free-Shared** oder **Shared-Shared** > **Auswählen** > **OK** > **Erstellen**.

7. Nach weniger als einer Minute wird auf dem Dashboard eine Kachel für die neue Web-App angezeigt. Klicken Sie auf die Kachel.

8. Klicken Sie auf dem Blatt **Web-App** auf **Durchsuchen**, um die Standardwebsite für diese App anzuzeigen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Bereitstellen einer WordPress-, DNN- oder Django-Website (optional)

1. Klicken Sie im Azure Stack-Mandantenportal auf **+** , wechseln Sie zum Azure Marketplace, stellen Sie eine Django-Website bereit, und warten Sie, bis der Vorgang erfolgreich abgeschlossen ist. Die Django-Webplattform verwendet eine dateisystembasierte Datenbank. Sie erfordert keine zusätzlichen Ressourcenanbieter wie SQL oder MySQL.

2. Wenn Sie auch einen MySQL-Ressourcenanbieter bereitgestellt haben, können Sie über den Marketplace eine WordPress-Website bereitstellen. Wenn Sie zur Angabe der Datenbankparameter aufgefordert werden, geben Sie den Benutzernamen im Format *Benutzer1\@Server1* ein. Sie können einen Benutzer- und Servernamen Ihrer Wahl eingeben.

3. Wenn Sie auch einen SQL Server-Ressourcenanbieter bereitgestellt haben, können Sie über den Marketplace eine DNN-Website bereitstellen. Wenn Sie zur Angabe der Datenbankparameter aufgefordert werden, wählen Sie auf dem SQL Server-Computer, der mit Ihrem Ressourcenanbieter verbunden ist, eine Datenbank aus.

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Verwaltungsvorgänge für App Service unter Azure Stack:

- [Kapazitätsplanung](azure-stack-app-service-capacity-planning.md)
- [Konfigurieren von Bereitstellungsquellen](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
