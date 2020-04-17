---
title: Bereitstellen von App Service in Azure Stack Hub
description: Es wird beschrieben, wie Sie App Service in Azure Stack Hub bereitstellen.
author: bryanla
ms.topic: article
ms.date: 01/13/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: a1939049c3eb8c4440e37e58b6acfafa91881406
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77688966"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>Bereitstellen von App Service in Azure Stack Hub

In diesem Artikel wird beschrieben, wie Sie App Service in Azure Stack Hub bereitstellen.

> [!IMPORTANT]
> Wenden Sie das Update 1910 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie Azure App Service 1.8 bereitstellen.

Sie können Ihren Benutzern ermöglichen, Web- und API-Anwendungen zu erstellen. Damit Benutzer diese Apps erstellen können, ist Folgendes erforderlich:

- Sie müssen Ihrer Azure Stack Hub-Bereitstellung anhand der in diesem Artikel beschriebenen Schritte den [App Service-Ressourcenanbieter](azure-stack-app-service-overview.md) hinzufügen.
- Nach dem Installieren des App Service-Ressourcenanbieters können Sie ihn in Ihren Angeboten und Plänen einfügen. Benutzer können ihn dann abonnieren, um den Dienst abzurufen und mit dem Erstellen von Anwendungen zu beginnen.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie vor dem Ausführen des Installationsprogramms für den Ressourcenanbieter die Anleitung unter [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) befolgt und die [Versionshinweise](azure-stack-app-service-release-notes-update-eight.md) zu diesem Release 1.8 gelesen haben. Unter diesen Links erhalten Sie Informationen zu neuer Funktionalität, Fehlerbehebungen und den bekannten Problemen, die für die Bereitstellung auftreten können.

## <a name="run-the-app-service-resource-provider-installer"></a>Ausführen des Installationsprogramms für den App Service-Ressourcenanbieter

Die Installation des App Service-Ressourcenanbieters dauert mindestens eine Stunde. Die erforderliche Zeit hängt davon ab, wie viele Rolleninstanzen bereitgestellt werden. Während der Bereitstellung werden mit dem Installationsprogram folgende Tasks ausgeführt:

- Es erstellt im angegebenen Azure Stack Hub-Speicherkonto einen Blobcontainer.
- Es erstellt eine DNS-Zone und DNS-Einträge für App Service.
- Es registriert den App Service-Ressourcenanbieter.
- Es registriert die App Service-Katalogelemente.

Führen Sie zum Bereitstellen eines App Service-Ressourcenanbieters die folgenden Schritte aus:

1. Führen Sie „appservice.exe“ als Administrator auf einem Computer aus, der auf den Azure-Ressourcenverwaltungsendpunkt des Azure Stack Hub-Administrators zugreifen kann.

2. Wählen Sie **App Service bereitstellen oder Upgrade auf die neueste Version durchführen** aus.

    ![App Service-Installationsprogramm][1]

3. Lesen und akzeptieren Sie die Microsoft-Software-Lizenzbedingungen, und wählen Sie dann **Weiter** aus.

4. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und wählen Sie dann **Weiter** aus.

5. Stellen Sie sicher, dass die App Service-Cloudkonfigurationsinformationen stimmen. Wenn Sie während der ASDK-Bereitstellung die Standardeinstellungen verwendet haben, können Sie die Standardwerte übernehmen. Wenn Sie die Optionen bei der Bereitstellung des ASDK jedoch angepasst haben oder in einem integrierten Azure Stack Hub-System bereitstellen, müssen Sie die Werte in diesem Fenster entsprechend bearbeiten.

   Wenn Sie beispielsweise das Domänensuffix „mycloud.com“ verwenden, muss der Azure Resource Manager-Endpunkt des Azure Stack Hub-Mandanten in „management.&lt;region&gt;.mycloud.com“ geändert werden. Überprüfen Sie diese Einstellungen, und wählen Sie anschließend **Weiter** aus.

   ![App Service-Installationsprogramm][2]

6. Auf der nächsten Seite des App Service-Installationsprogramms stellen Sie eine Verbindung mit Ihrer Azure Stack Hub-Instanz her:

    1. Wählen Sie die Verbindungsmethode aus, die Sie verwenden möchten: **Anmeldeinformationen** oder **Dienstprinzipal**.
 
        - **Credential**
            - Wenn Sie Azure Active Directory (Azure AD) verwenden, geben Sie das Azure AD-Administratorkonto, das Sie bei der Bereitstellung von Azure Stack Hub angegeben haben, und das zugehörige Kennwort ein. Wählen Sie **Verbinden**.
            - Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden, geben Sie Ihr Administratorkonto an. Beispiel: cloudadmin@azurestack.local. Geben Sie Ihr Kennwort ein, und wählen Sie dann **Verbinden** aus.

        - **Dienstprinzipal**
            - Der Dienstprinzipal, den Sie verwenden, **muss** über Rechte als **Besitzer** für das **Standardanbieterabonnement** verfügen.
            - Geben Sie **Dienstprinzipal-ID**, **Zertifikatsdatei** und **Kennwort** ein, und wählen Sie **Verbinden** aus.

    1. Wählen Sie in **Azure Stack Hub-Abonnements** das **Standardanbieterabonnement** aus.  Azure App Service auf Azure Stack Hub **muss** im **Standardanbieterabonnement** bereitgestellt werden.

    1. Wählen Sie in **Azure Stack Hub-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.

    ![App Service-Installationsprogramm][3]

7. Nun können Sie die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchführen, das Sie [mit diesen Schritten](azure-stack-app-service-before-you-get-started.md#virtual-network) erstellt haben, oder per App Service-Installationsprogramm ein neues virtuelles Netzwerk mit Subnetzen erstellen lassen. Führen Sie die folgenden Schritte aus, um ein VNET zu erstellen:

   a. Wählen Sie **VNET mit Standardeinstellungen erstellen** aus, übernehmen Sie die Standardwerte, und wählen Sie dann **Weiter** aus.

   b. Wählen Sie alternativ **Vorhandenes VNET und Subnetze verwenden** aus. Führen Sie folgende Aktionen durch:

     - Wählen Sie die **Ressourcengruppe** aus, die Ihr virtuelles Netzwerk enthält.
     - Wählen Sie den **Namen des virtuellen Netzwerks** aus, in dem die Bereitstellung durchgeführt werden soll.
     - Wählen Sie die richtigen **Subnetzwerte** für die einzelnen erforderlichen Rollensubnetze aus.
     - Wählen Sie **Weiter** aus.

   ![App Service-Installationsprogramm][4]

8. Geben Sie die Informationen für die Dateifreigabe ein, und wählen Sie dann **Weiter** aus. Die Adresse der Dateifreigabe muss den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse Ihres Dateiservers enthalten. Beispiel: \\„\appservicefileserver.local.cloudapp.azurestack.external\websites“ oder \\„\10.0.0.1\websites“.  Wenn Sie einen Dateiserver verwenden, der in eine Domäne eingebunden ist, müssen Sie den vollständigen Benutzernamen mit der Domäne eingeben. Beispiel: myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >Das Installationsprogramm versucht, die Konnektivität mit der Dateifreigabe zu testen, bevor der Vorgang fortgesetzt wird. Wenn Sie die Bereitstellung aber in einem vorhandenen virtuellen Netzwerk durchführen, können bei diesem Konnektivitätstest ggf. Fehler auftreten. Es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Wenn die Dateifreigabeinformationen richtig sind, können Sie mit der Bereitstellung fortfahren.

   ![App Service-Installationsprogramm][7]

9. Gehen Sie auf der nächsten Seite des App Service-Installationsprogramms folgendermaßen vor:

   a. Geben Sie im Feld **Identitätsanwendungs-ID** die GUID der App ein, die Sie für die Identität verwenden (aus Azure AD).

   b. Geben Sie im Feld **Identitätsanwendungs-Zertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).

   c. Geben Sie im Feld **Kennwort für das Identitätsanwendungszertifikat** das Kennwort für das Zertifikat ein. Dieses Kennwort haben Sie erstellt, als Sie das Skript zum Erstellen des Zertifikats verwendet haben.

   d. Geben Sie im Feld **Azure Resource Manager-Stammzertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).

   e. Wählen Sie **Weiter** aus.

   ![App Service-Installationsprogramm][9]

10. Wählen Sie für jede der drei Zertifikatsdateien die Option **Durchsuchen** aus, und navigieren Sie zu der entsprechenden Zertifikatsdatei. Sie müssen das Kennwort für jedes Zertifikat angeben. Diese Zertifikate haben Sie im Schritt [Erstellen der erforderlichen Zertifikate](azure-stack-app-service-before-you-get-started.md#get-certificates) erstellt. Wählen Sie **Weiter** aus, nachdem Sie alle Informationen eingegeben haben.

    | Feld | Beispielname für eine Zertifikatsdatei |
    | --- | --- |
    | **Standard-SSL-Zertifikatsdatei für App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-API** | api.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-Herausgeber** | ftp.appservice.local.AzureStack.external.pfx |

    Wenn Sie beim Erstellen der Zertifikate ein anderes Domänensuffix verwendet haben, verwenden die Zertifikatsdateinamen nicht *local.AzureStack.external*. Verwenden Sie stattdessen Ihre benutzerdefinierten Domäneninformationen.

    ![App Service-Installationsprogramm][10]

11. Geben Sie die SQL Server-Informationen für die Serverinstanz ein, auf der die Datenbank des App Service-Ressourcenanbieters gehostet werden soll, und wählen Sie dann **Weiter** aus. Das Installationsprogramm überprüft die SQL-Verbindungseigenschaften.<br><br>Das App Service-Installationsprogramm versucht zunächst, die Konnektivität mit SQL Server zu testen. Wenn Sie die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchführen, ist dieser Konnektivitätstest ggf. nicht erfolgreich. Es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Wenn die SQL Server-Informationen korrekt sind, können Sie mit der Bereitstellung fortfahren.

    ![App Service-Installationsprogramm][11]

12. Überprüfen Sie die Optionen für Rolleninstanz und SKU. Als Standardwerte werden die Mindestzahl der Instanzen und die Mindest-SKU für jede Rolle in einer ASDK-Bereitstellung verwendet. Eine Übersicht über die vCPU- und Arbeitsspeichervoraussetzungen wird angezeigt, um Sie bei der Bereitstellung zu unterstützen. Nachdem Sie Ihre Auswahl getroffen haben, wählen Sie **Weiter** aus.

    >[!NOTE]
    >Befolgen Sie bei Produktionsbereitstellungen die Anweisungen unter [Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack Hub](azure-stack-app-service-capacity-planning.md).

    | Role | Mindestanzahl der Instanzen | Mindest-SKU | Notizen |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Verwaltet und wartet die Integrität der App Service-Cloud |
    | Verwaltung | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Verwaltet die Azure Resource Manager- und API-Endpunkte, die Portalerweiterungen (Administrator-, Mandanten, Functions-Portal) und den Datendienst von App Service. Zur Unterstützung eines Failovers erhöhen Sie die empfohlenen Instanzen auf 2. |
    | Herausgeber | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Veröffentlicht Inhalte per FTP oder Webbereitstellung |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Leitet Anforderungen an App Service-Apps weiter. |
    | Freigegebener Worker | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hostet Web- oder API-Apps und Azure Functions-Apps. Sie können ggf. weitere Instanzen hinzufügen. Als Operator können Sie Ihr Angebot definieren und eine SKU-Ebene auswählen. Die Ebenen müssen mindestens eine vCPU aufweisen. |

    ![App Service-Installationsprogramm][13]

    >[!NOTE]
    >**Windows Server 2016 Core ist kein unterstütztes Plattformimage für die Verwendung mit Azure App Service in Azure Stack Hub.  Verwenden Sie Evaluierungsimages nicht für Produktionsbereitstellungen.**

13. Wählen Sie im Feld **Plattformimage auswählen** aus den VM-Images, die im Computeressourcenanbieter für die App Service-Cloud verfügbar sind, Ihr VM-Bereitstellungsimage für Windows Server 2016 aus. Wählen Sie **Weiter** aus.

14. Gehen Sie auf der nächsten Seite des App Service-Installationsprogramms folgendermaßen vor:

     a. Geben Sie den Administratorbenutzernamen und das Kennwort für die VM mit der Workerrolle ein.

     b. Geben Sie den Administratorbenutzernamen und das Kennwort für die VM mit den anderen Rollen ein.

     c. Wählen Sie **Weiter** aus.

    ![App Service-Installationsprogramm][15]

15. Gehen Sie auf der Zusammenfassungsseite des App Service-Installationsprogramms folgendermaßen vor:

    a. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.

    b. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.

    c. Wählen Sie zum Starten der Bereitstellung die Option **Weiter** aus.

    ![App Service-Installationsprogramm][16]

16. Gehen Sie auf der nächsten Seite des App Service-Installationsprogramms folgendermaßen vor:

    a. Verfolgen Sie den Installationsstatus nach. App Service auf Azure Stack Hub kann je nach der Standardauswahl und dem Alter des zugrundeliegenden Windows 2016 Datacenter-Images bis zu 240 Minuten für die Bereitstellung in Anspruch nehmen.

    b. Wählen Sie nach erfolgreichem Abschluss des Installationsprogramms die Option **Beenden** aus.

    ![App Service-Installationsprogramm][17]

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, **müssen** Sie die [Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

Wenn Sie die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchführen und für die Verbindung mit Ihrem Dateiserver eine interne IP-Adresse verwenden, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen. Diese Regel ermöglicht SMB-Datenverkehr zwischen dem Workersubnetz und dem Dateiserver. Wechseln Sie im Administratorportal zur Netzwerksicherheitsgruppe „WorkersNsg“, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

- Quelle: Any
- Quellportbereich: *
- Ziel: IP-Adressen
- IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
- Zielportbereich: 445
- Protokoll: TCP
- Aktion: Allow
- Priorität: 700
- Name: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-hub-installation"></a>Überprüfen der Installation von App Service in Azure Stack Hub

1. Navigieren Sie im Azure Stack Hub-Administratorportal zu **Verwaltung – App Service**.

2. Überprüfen Sie in der Übersicht unter „Status“, ob für **Status** die Option **Alle Rollen sind bereit** angezeigt wird.

    ![App Service-Verwaltung](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack-hub"></a>Testen von App Service in Azure Stack Hub

Nachdem Sie den App Service-Ressourcenanbieter bereitgestellt und registriert haben, testen Sie ihn, um sicherzustellen, dass Benutzer Web- und API-Apps bereitstellen können.

>[!NOTE]
>Sie müssen ein Angebot erstellen, das den Namespace „Microsoft.Web“ im Plan enthält. Sie benötigen außerdem ein Mandantenabonnement, das das Angebot abonniert. Weitere Informationen finden Sie unter [Erstellen eines Angebots](azure-stack-create-offer.md) und [Erstellen eines Plans](azure-stack-create-plan.md).
>
>Sie *müssen* über ein Mandantenabonnement verfügen, um Apps zu erstellen, die App Service in Azure Stack Hub verwenden. Ein Dienstadministrator kann nur Aufgaben im Administratorportal ausführen, die mit der Ressourcenanbieterverwaltung von App Service zu tun haben. Hierzu gehören das Hinzufügen von Kapazitäten, das Konfigurieren von Bereitstellungsquellen und das Hinzufügen von Workerebenen und SKUs.
>
>Sie müssen das Benutzerportal verwenden und über ein Mandantenabonnement verfügen, um Web-, API- und Azure Functions-Apps zu erstellen.
>

Führen Sie die folgenden Schritte aus, um eine Test-Web-App zu erstellen:

1. Wählen Sie im Azure Stack Hub-Benutzerportal **Ressource erstellen** > **Web + Mobil** > **Web-App** aus.

2. Geben Sie unter **Web-App**, einen Namen in **Web-App** ein.

3. Wählen Sie unter **Ressourcengruppe** die Option **Neu** aus. Geben Sie einen Namen für die **Ressourcengruppe** ein.

4. Wählen Sie **App Service-Plan/Standort** > **Neu erstellen** aus.

5. Geben Sie unter **App Service-Plan**, einen Namen für den **App Service-Plan** ein.

6. Wählen Sie **Tarif** > **Free-Shared** oder **Shared-Shared** > **Auswählen** > **OK** > **Erstellen** aus.

7. Auf dem Dashboard wird eine Kachel für die neue Web-App angezeigt. Wählen Sie die Kachel aus.

8. Wählen Sie in **Web-App** die Option **Durchsuchen** aus, um die Standardwebsite für diese App anzuzeigen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Bereitstellen einer WordPress-, DNN- oder Django-Website (optional)

1. Wählen Sie im Azure Stack Hub-Benutzerportal das Pluszeichen ( **+** ) aus, wechseln Sie zum Azure Marketplace, stellen Sie eine Django-Website bereit, und warten Sie dann, bis die Bereitstellung abgeschlossen ist. Die Django-Webplattform verwendet eine dateisystembasierte Datenbank. Sie erfordert keine zusätzlichen Ressourcenanbieter wie SQL oder MySQL.

2. Wenn Sie auch einen MySQL-Ressourcenanbieter bereitgestellt haben, können Sie über den Marketplace eine WordPress-Website bereitstellen. Wenn Sie zur Angabe der Datenbankparameter aufgefordert werden, geben Sie den Benutzernamen im Format *Benutzer1\@Server1* ein. Sie können einen Benutzer- und Servernamen Ihrer Wahl eingeben.

3. Wenn Sie auch einen SQL Server-Ressourcenanbieter bereitgestellt haben, können Sie über den Marketplace eine DNN-Website bereitstellen. Wenn Sie zur Angabe der Datenbankparameter aufgefordert werden, wählen Sie auf dem SQL Server-Computer, der mit Ihrem Ressourcenanbieter verbunden ist, eine Datenbank aus.

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Verwaltungsvorgänge für App Service unter Azure Stack Hub:

- [Kapazitätsplanung](azure-stack-app-service-capacity-planning.md)
- [Konfigurieren von Bereitstellungsquellen](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
