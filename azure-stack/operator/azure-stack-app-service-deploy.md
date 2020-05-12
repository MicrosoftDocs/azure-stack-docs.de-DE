---
title: Bereitstellen von App Service in Azure Stack Hub
description: Es wird beschrieben, wie Sie App Service in Azure Stack Hub bereitstellen.
author: bryanla
ms.topic: article
ms.date: 05/05/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 4f9bdf84f73bf06c42f3b6910e0faad83a9bbd80
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847985"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>Bereitstellen von App Service in Azure Stack Hub

[!INCLUDE [Azure Stack hub update reminder](../includes/app-service-hub-update-banner.md)]

> [!IMPORTANT]
> Bevor Sie das Installationsprogramm des Ressourcenanbieters ausführen, müssen Sie die Schritte unter [Bevor Sie beginnen](azure-stack-app-service-before-you-get-started.md) ausführen.

::: zone pivot="state-connected"
In diesem Artikel erfahren Sie, wie Sie App Service in Azure Stack Hub bereitstellen, damit Benutzer Web-, API- und Azure Functions-Anwendungen erstellen können. Sie müssen folgende Schritte durchführen:

- Sie müssen Ihrer Azure Stack Hub-Bereitstellung anhand der in diesem Artikel beschriebenen Schritte den [App Service-Ressourcenanbieter](azure-stack-app-service-overview.md) hinzufügen.
- Nach dem Installieren des App Service-Ressourcenanbieters können Sie ihn in Ihren Angeboten und Plänen einfügen. Benutzer können ihn dann abonnieren, um den Dienst abzurufen und mit dem Erstellen von Anwendungen zu beginnen.

## <a name="run-the-app-service-resource-provider-installer"></a>Ausführen des Installationsprogramms für den App Service-Ressourcenanbieter

Die Installation des App Service-Ressourcenanbieters dauert mindestens eine Stunde. Die erforderliche Zeit hängt davon ab, wie viele Rolleninstanzen bereitgestellt werden. Während der Bereitstellung werden mit dem Installationsprogram folgende Tasks ausgeführt:

- Registriert die erforderlichen Ressourcenanbieter im Standardabonnement des Anbieters.
- Gewährt Mitwirkendenzugriff auf die App Service-Identitätsanwendung.
- Erstellen einer Ressourcengruppe und eines virtuellen Netzwerks (wenn erforderlich)
- Erstellen von Speicherkonten und Containern für App Service-Installationsartefakte, den Verwendungsdienst und Ressourcenaktivierung
- Herunterladen von App Service-Artefakten und Hochladen dieser in das App Service-Speicherkonto
- Bereitstellen von App Service
- Registrieren des Nutzungsdiensts
- Erstellen von DNS-Einträgen für App Service
- Registrieren der App Service Administrator- und Mandantenressourcenanbieter
- Registrieren von Katalogelementen: Web-, API-, Funktionen-App-, App Service-Plan-, WordPress-, DNN-, Orchard- und Django-Anwendungen

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
            - Geben Sie die **Dienstprinzipal-ID**, die **Zertifikatsdatei** und das **Kennwort** ein, und wählen Sie **Verbinden** aus.

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

   a. Geben Sie im Feld **Identitätsanwendungs-ID** die GUID für die Identitätsanwendung ein, die Sie als eine der [Voraussetzungen](azure-stack-app-service-before-you-get-started.md) erstellt haben.

   b. Geben Sie im Feld **Identitätsanwendungs-Zertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).

   c. Geben Sie im Feld **Kennwort für das Identitätsanwendungszertifikat** das Kennwort für das Zertifikat ein. Dieses Kennwort haben Sie erstellt, als Sie das Skript zum Erstellen des Zertifikats verwendet haben.

   d. Geben Sie im Feld **Azure Resource Manager-Stammzertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).

   e. Wählen Sie **Weiter** aus.

   ![App Service-Installationsprogramm][9]

10. Wählen Sie für jede der drei Zertifikatsdateien die Option **Durchsuchen** aus, und navigieren Sie zu der entsprechenden Zertifikatsdatei. Sie müssen das Kennwort für jedes Zertifikat angeben. Dabei handelt es sich um die Zertifikate, die Sie in [Voraussetzungen für die Bereitstellung von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) erstellt haben. Wählen Sie **Weiter** aus, nachdem Sie alle Informationen eingegeben haben.

    | Feld | Beispielname für eine Zertifikatsdatei |
    | --- | --- |
    | **Standard-SSL-Zertifikatsdatei für App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-API** | api.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-Herausgeber** | ftp.appservice.local.AzureStack.external.pfx |

    Wenn Sie beim Erstellen der Zertifikate ein anderes Domänensuffix verwendet haben, verwenden die Zertifikatsdateinamen nicht *local.AzureStack.external*. Verwenden Sie stattdessen Ihre benutzerdefinierten Domäneninformationen.

    ![App Service-Installationsprogramm][10]

11. Geben Sie die SQL Server-Informationen für die Serverinstanz ein, auf der die Datenbank des App Service-Ressourcenanbieters gehostet werden soll, und wählen Sie dann **Weiter** aus. Das Installationsprogramm überprüft die SQL-Verbindungseigenschaften.<br><br>Das App Service-Installationsprogramm versucht zunächst, die Konnektivität mit SQL Server zu testen. Wenn Sie die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchführen, ist dieser Konnektivitätstest ggf. nicht erfolgreich. Es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Wenn die SQL Server-Informationen korrekt sind, können Sie mit der Bereitstellung fortfahren.

    ![App Service-Installationsprogramm][11]

12. Überprüfen Sie die Optionen für Rolleninstanz und SKU. Als Standardwerte werden die Mindestzahl der Instanzen und die Mindest-SKU für jede Rolle in einer Produktionsbereitstellung verwendet.  Für eine ASDK-Bereitstellung können Sie die Instanzen auf niedrigere SKUs abskalieren, um die Kern- und Arbeitsspeicherbelegung zu reduzieren, aber es treten Leistungseinbußen auf. Eine Übersicht über die vCPU- und Arbeitsspeichervoraussetzungen wird angezeigt, um Sie bei der Bereitstellung zu unterstützen. Nachdem Sie Ihre Auswahl getroffen haben, wählen Sie **Weiter** aus.

    >[!NOTE]
    >Befolgen Sie bei Produktionsbereitstellungen die Anweisungen unter [Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack Hub](azure-stack-app-service-capacity-planning.md).

    | Role | Mindestanzahl der Instanzen | Mindest-SKU | Notizen |
    | --- | --- | --- | --- |
    | Controller | 2 | Standard_A4_v2 (4 Kerne, 8.192 MB) | Verwaltet und wartet die Integrität der App Service-Cloud |
    | Verwaltung | 1 | Standard_D3_v2 (4 Kerne, 14.336 MB) | Verwaltet die Azure Resource Manager- und API-Endpunkte, die Portalerweiterungen (Administrator-, Mandanten, Functions-Portal) und den Datendienst von App Service. Zur Unterstützung eines Failovers erhöhen Sie die empfohlenen Instanzen auf 2. |
    | Herausgeber | 1 | Standard_A2_v2 (2 Kerne, 4.096 MB) | Veröffentlicht Inhalte per FTP oder Webbereitstellung |
    | FrontEnd | 1 | Standard_A4_v2 (4 Kerne, 8.192 MB) | Leitet Anforderungen an App Service-Apps weiter. |
    | Freigegebener Worker | 1 | Standard_A4_v2 (4 Kerne, 8.192 MB) | Hostet Web- oder API-Apps und Azure Functions-Apps. Sie können ggf. weitere Instanzen hinzufügen. Als Operator können Sie Ihr Angebot definieren und eine SKU-Ebene auswählen. Die Ebenen müssen mindestens eine vCPU aufweisen. |

    ![App Service-Installationsprogramm][13]

    > [!NOTE]
    > **Windows Server 2016 Core ist kein unterstütztes Plattformimage für die Verwendung mit Azure App Service in Azure Stack Hub.  Verwenden Sie Evaluierungsimages nicht für Produktionsbereitstellungen.**

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
::: zone-end



<!----------------------------------------- DISCONNECTED PIVOT -------------------------------------------->
::: zone pivot="state-disconnected"
In diesem Artikel erfahren Sie, wie Sie den [Azure App Service-Ressourcenanbieter](azure-stack-app-service-overview.md) in einer Azure Stack Hub-Umgebung bereitstellen, für die Folgendes gilt:
- Sie ist nicht mit dem Internet verbunden.
- Sie ist durch Active Directory-Verbunddienste (AD FS) geschützt.

Um Ihrer Azure Stack Hub-Offlinebereitstellung den Azure App Service-Ressourcenanbieter hinzuzufügen, müssen Sie die folgenden allgemeinen Aufgaben ausführen:

1. Führen Sie die [vorausgesetzten Schritte](azure-stack-app-service-before-you-get-started.md) aus (z.B. den Erwerb von Zertifikaten, der einige Tage dauern kann).
2. [Laden Sie die Installations- und Hilfsdateien herunter, und extrahieren Sie sie](azure-stack-app-service-before-you-get-started.md) auf einem Computer, der mit dem Internet verbunden ist.
3. Erstellen Sie ein Offlineinstallationspaket.
4. Führen Sie das Installationsprogramm „appservice.exe“ aus.

## <a name="create-an-offline-installation-package"></a>Erstellen eines Offlineinstallationspakets

Erstellen Sie zum Bereitstellen von Azure App Service in einer Offlineumgebung zunächst ein Offlineinstallationspaket auf einem Computer, der über eine Internetverbindung verfügt.

1. Führen Sie das Installationsprogramm „AppService.exe“ auf einem Computer aus, der mit dem Internet verbunden ist. 

2. Wählen Sie **Erweitert** > **Offlineinstallationspaket erstellen** aus. Dieser Schritt dauert einige Minuten.

    ![Erstellen eines Offlinepakets im Azure App Service-Installationsprogramm][31]

3. Das Azure App Service-Installationsprogramm erstellt ein Offlineinstallationspaket und zeigt den Pfad zu diesem an. Sie können **Ordner öffnen** auswählen, um den Ordner im Datei-Explorer zu öffnen.

    ![Das Offlineinstallationspaket wurde im Azure App Service-Installationsprogramm erfolgreich generiert.](media/azure-stack-app-service-deploy-offline/image02.png)

4. Kopieren Sie das Installationsprogramm („AppService.exe“) und das Offlineinstallationspaket auf einen Computer, der eine Verbindung mit Azure Stack Hub herstellen kann.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Abschließen der Offlineinstallation von Azure App Service in Azure Stack Hub

1. Führen Sie „AppService.exe“ als Administrator auf einem Computer aus, der den Azure Resource Manager-Endpunkt des Azure Stack Hub-Administrators erreichen kann.

1. Wählen Sie **Erweitert** > **Offlineinstallation abschließen** aus.

    ![Abschließen der Offlineinstallation im Azure App Service-Installationsprogramm][32]

1. Navigieren Sie zum Speicherort des zuvor erstellten Offlineinstallationspakets, und wählen Sie dann **Weiter** aus.

    ![Angeben des Pfads zum Offlineinstallationspaket im Azure App Service-Installationsprogramm](media/azure-stack-app-service-deploy-offline/image04.png)

1. Lesen und akzeptieren Sie die Microsoft-Software-Lizenzbedingungen, und wählen Sie dann **Weiter** aus.

1. Lesen und akzeptieren Sie die Drittanbieter-Lizenzbedingungen, und wählen Sie dann **Weiter** aus.


1. Stellen Sie sicher, dass die Informationen für die Azure App Service-Cloudkonfiguration korrekt sind. Wenn Sie während der ASDK-Bereitstellung die Standardeinstellungen verwendet haben, können Sie hier die Standardwerte übernehmen. Falls Sie die Optionen bei der Bereitstellung von Azure Stack Hub angepasst haben oder die Bereitstellung in einem integrierten System durchführen, müssen Sie die Werte in diesem Fenster entsprechend anpassen. Wenn Sie beispielsweise das Domänensuffix „mycloud.com“ verwenden, muss der Azure Resource Manager-Endpunkt des Azure Stack Hub-Mandanten zu `management.<region>.mycloud.com` geändert werden. Nachdem Sie Ihre Informationen bestätigt haben, wählen Sie **Weiter** aus.

    ![Konfigurieren der Azure App Service-Cloud im Azure App Service-Installationsprogramm][33]

1. Auf der nächsten Seite des App Service-Installationsprogramms stellen Sie eine Verbindung mit Ihrer Azure Stack Hub-Instanz her:

    1. Wählen Sie die Verbindungsmethode aus, die Sie verwenden möchten: **Anmeldeinformationen** oder **Dienstprinzipal**.
        - **Credential**
            - Wenn Sie Azure Active Directory (Azure AD) verwenden, geben Sie das Azure AD-Administratorkonto, das Sie bei der Bereitstellung von Azure Stack Hub angegeben haben, und das zugehörige Kennwort ein. Wählen Sie **Verbinden**.
            - Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden, geben Sie Ihr Administratorkonto an. Beispiel: cloudadmin@azurestack.local. Geben Sie Ihr Kennwort ein, und wählen Sie dann **Verbinden** aus.
        - **Dienstprinzipal**
            - Der Dienstprinzipal, den Sie verwenden, **muss** über Rechte als **Besitzer** für das **Standardanbieterabonnement** verfügen.
            - Geben Sie die **Dienstprinzipal-ID**, die **Zertifikatsdatei** und das **Kennwort** ein, und wählen Sie **Verbinden** aus.

    1. Wählen Sie in **Azure Stack Hub-Abonnements** das **Standardanbieterabonnement** aus.  Azure App Service auf Azure Stack Hub **muss** im **Standardanbieterabonnement** bereitgestellt werden.

    1. Wählen Sie in **Azure Stack Hub-Standorte** den Standort aus, der der Region entspricht, in der die Bereitstellung erfolgen soll. Wählen Sie beispielsweise **Lokal**, wenn Sie die Bereitstellung für das ASDK durchführen.

1. Sie können dem Azure App Service-Installationsprogramm erlauben, ein virtuelles Netzwerk und die zugehörigen Subnetze zu erstellen. Alternativ können Sie auch die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchführen, indem Sie für die Konfiguration [diese Schritte](azure-stack-app-service-before-you-get-started.md#virtual-network) ausführen.
   - Wählen Sie zum Verwenden des Azure App Service-Installationsprogramms die Option **VNET mit Standardeinstellungen erstellen**, übernehmen Sie die Standardeinstellungen, und wählen Sie anschließend **Weiter** aus.
   - Wählen Sie für die Bereitstellung in einem vorhandenen Netzwerk die Option **Vorhandenes VNET und Subnetze verwenden** aus, und gehen Sie dann wie folgt vor:
       1. Wählen Sie die Option für die **Ressourcengruppe** aus, die Ihr virtuelles Netzwerk enthält.
       2. Wählen Sie den Namen des **virtuellen Netzwerks** aus, in dem die Bereitstellung durchgeführt werden soll.
       3. Wählen Sie die richtigen **Subnetzwerte** für die einzelnen erforderlichen Rollensubnetze aus.
       4. Wählen Sie **Weiter** aus.

      ![Informationen zum virtuellen Netzwerk und zum Subnetz im Azure App Service-Installationsprogramm][35]

1. Geben Sie die Informationen für die Dateifreigabe ein, und wählen Sie dann **Weiter** aus. Für die Adresse der Dateifreigabe muss der vollqualifizierte Domänenname (FQDN) oder die IP-Adresse Ihres Dateiservers verwendet werden. Beispiel: „\\\appservicefileserver.local.cloudapp.azurestack.external\websites“ oder „\\\10.0.0.1\websites“.  Wenn Sie einen Dateiserver verwenden, der in eine Domäne eingebunden ist, müssen Sie den vollständigen Benutzernamen mit der Domäne eingeben. Beispiel: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > Das Installationsprogramm versucht, die Konnektivität mit der Dateifreigabe zu testen, bevor der Vorgang fortgesetzt wird. Wenn Sie sich dagegen für die Bereitstellung in einem vorhandenen virtuellen Netzwerk entscheiden, kann das Installationsprogramm unter Umständen keine Verbindung mit der Dateifreigabe herstellen. Es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Überprüfen Sie die Dateifreigabeinformationen, und fahren Sie fort, wenn diese korrekt sind.

   ![Dateifreigabeinformationen im Azure App Service-Installationsprogramm][38]

1. Auf der nächsten Seite:
    1. Geben Sie im Feld **Identitätsanwendungs-ID** die GUID für die Identitätsanwendung ein, die Sie als eine der [Voraussetzungen](azure-stack-app-service-before-you-get-started.md) erstellt haben.
    1. Geben Sie im Feld **Identitätsanwendungs-Zertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).
    1. Geben Sie im Feld **Kennwort für das Identitätsanwendungszertifikat** das Kennwort für das Zertifikat ein. Dieses Kennwort haben Sie erstellt, als Sie das Skript zum Erstellen des Zertifikats verwendet haben.
    1. Geben Sie im Feld **Azure Resource Manager-Stammzertifikatsdatei** den Speicherort der Zertifikatsdatei ein (oder navigieren Sie zu diesem).
    1. Wählen Sie **Weiter** aus.

    ![Eingeben der App-ID und der Zertifikatinformationen im Azure App Service-Installationsprogramm][40]

1. Wählen Sie für jede der drei Zertifikatsdateien die Option **Durchsuchen** aus, und navigieren Sie zu der entsprechenden Zertifikatsdatei. Sie müssen das Kennwort für jedes Zertifikat angeben. Dabei handelt es sich um die Zertifikate, die Sie in [Voraussetzungen für die Bereitstellung von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) erstellt haben. Wählen Sie **Weiter** aus, nachdem Sie alle Informationen eingegeben haben.

    | Feld | Beispielname für eine Zertifikatsdatei |
    | --- | --- |
    | **Standard-SSL-Zertifikatsdatei für App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-API** | api.appservice.local.AzureStack.external.pfx |
    | **SSL-Zertifikatsdatei für App Service-Herausgeber** | ftp.appservice.local.AzureStack.external.pfx |

    Wenn Sie beim Erstellen der Zertifikate ein anderes Domänensuffix verwendet haben, verwenden die Zertifikatsdateinamen nicht *local.AzureStack.external*. Verwenden Sie stattdessen Ihre benutzerdefinierten Domäneninformationen.

    ![Eingeben der SSL-Zertifikatinformationen im Azure App Service-Installationsprogramm][41]

1. Geben Sie die SQL Server-Informationen für die Serverinstanz ein, auf der die Datenbanken des Azure App Service-Ressourcenanbieters gehostet werden sollen, und wählen Sie dann **Weiter** aus. Das Installationsprogramm überprüft die SQL-Verbindungseigenschaften. Sie **müssen** entweder die interne IP-Adresse oder den FQDN für den SQL Server-Namen eingeben.

    > [!NOTE]
    > Vor dem Fortfahren versucht das Installationsprogramm, die Konnektivität für den Computer zu testen, auf dem SQL Server ausgeführt wird. Wenn Sie sich dagegen für die Bereitstellung in einem vorhandenen virtuellen Netzwerk entschieden haben, kann das Installationsprogramm unter Umständen keine Verbindung mit dem Computer herstellen, auf dem SQL Server ausgeführt wird. Es wird eine Warnung mit der Frage angezeigt, ob Sie den Vorgang fortsetzen möchten. Überprüfen Sie die SQL Server-Informationen, und fahren Sie fort, wenn diese richtig sind.
    >
    > Ab Azure App Service in Azure Stack Hub 1.3 überprüft das Installationsprogramm, ob für den Computer, auf dem SQL Server ausgeführt wird, die Datenbankeigenständigkeit auf SQL Server-Ebene aktiviert ist. Wenn dies nicht der Fall ist, wird die folgende Ausnahme angezeigt:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Weitere Informationen finden Sie in den [Versionshinweisen für Azure App Service in Azure Stack Hub 1.3](azure-stack-app-service-release-notes-update-three.md).

    ![Eingeben von SQL Server-Informationen im Azure App Service-Installationsprogramm][42]

1. Überprüfen Sie die Optionen für Rolleninstanz und SKU. Als Standardwerte werden die Mindestzahl der Instanzen und die Mindest-SKU für jede Rolle in einer Produktionsbereitstellung verwendet.  Für eine ASDK-Bereitstellung können Sie die Instanzen auf niedrigere SKUs abskalieren, um die Kern- und Arbeitsspeicherbelegung zu reduzieren, aber es treten Leistungseinbußen auf.  Eine Übersicht über die vCPU- und Arbeitsspeichervoraussetzungen wird angezeigt, um Sie bei der Bereitstellung zu unterstützen. Nachdem Sie Ihre Auswahl getroffen haben, wählen Sie **Weiter** aus.

     > [!NOTE]
     > Befolgen Sie bei Produktionsbereitstellungen die Anweisungen unter [Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack Hub](azure-stack-app-service-capacity-planning.md).
     >
     >

    
    | Role | Mindestanzahl der Instanzen | Mindest-SKU | Notizen |
    | --- | --- | --- | --- |
    | Controller | 2 | Standard_A4_v2 (4 Kerne, 8.192 MB) | Verwaltet und wartet die Integrität der App Service-Cloud |
    | Verwaltung | 1 | Standard_D3_v2 (4 Kerne, 14.336 MB) | Verwaltet die Azure Resource Manager- und API-Endpunkte, die Portalerweiterungen (Administrator-, Mandanten, Functions-Portal) und den Datendienst von App Service. Zur Unterstützung eines Failovers erhöhen Sie die empfohlenen Instanzen auf 2. |
    | Herausgeber | 1 | Standard_A2_v2 (2 Kerne, 4.096 MB) | Veröffentlicht Inhalte per FTP oder Webbereitstellung |
    | FrontEnd | 1 | Standard_A4_v2 (4 Kerne, 8.192 MB) | Leitet Anforderungen an App Service-Apps weiter. |
    | Freigegebener Worker | 1 | Standard_A4_v2 (4 Kerne, 8.192 MB) | Hostet Web- oder API-Apps und Azure Functions-Apps. Sie können ggf. weitere Instanzen hinzufügen. Als Operator können Sie Ihr Angebot definieren und eine SKU-Ebene auswählen. Die Ebenen müssen mindestens eine vCPU aufweisen. |

    ![Festlegen von Rollenebenen und SKU-Optionen im Azure App Service-Installationsprogramm][44]

1. Wählen Sie im Feld **Plattformimage auswählen** aus den Images, die im Computeressourcenanbieter für die Azure App Service-Cloud verfügbar sind, Ihr VM-Bereitstellungsimage für Windows Server 2016 aus. Wählen Sie **Weiter** aus.

    > [!NOTE]
    > Windows Server 2016 Core ist *kein* unterstütztes Plattformimage für die Verwendung mit Azure App Service in Azure Stack Hub.  Verwenden Sie Auswertungsimages nicht für Produktionsbereitstellungen. Für Azure App Service in Azure Stack Hub wird vorausgesetzt, dass im Image für die Bereitstellung Microsoft .NET 3.5.1 SP1 aktiviert ist. Bei syndizierten Marketplace-Images von Windows Server 2016 ist dieses Feature nicht aktiviert. Daher müssen Sie ein Windows Server 2016-Image erstellen und verwenden, für das dieses Feature vorab aktiviert ist.
    >
    > Ausführliche Informationen zum Erstellen eines benutzerdefinierten Images und zum Hinzufügen zum Marketplace finden Sie unter [Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub](azure-stack-add-vm-image.md). Geben Sie beim Hinzufügen des Images zum Marketplace unbedingt Folgendes an:
    >
    >- Herausgeber: MicrosoftWindowsServer
    >- Angebot: WindowsServer
    >- SKU: 2016-Datacenter
    >- Version: Geben Sie die aktuelle Version an.

1. Auf der nächsten Seite:
     1. Geben Sie den Administratorbenutzernamen und das Kennwort für die VM mit der Workerrolle ein.
     2. Geben Sie den Administratorbenutzernamen und das Kennwort für VMs mit anderen Rollen ein.
     3. Wählen Sie **Weiter** aus.

    ![Eingeben der Administratordaten für Rollen-VMs im Azure App Service-Installationsprogramm][46]

1. Auf der Zusammenfassungsseite:
    1. Überprüfen Sie Ihre Auswahl. Um Änderungen vorzunehmen, verwenden Sie die Schaltfläche **Zurück**, um auf die vorherigen Seiten zu gelangen.
    2. Wenn die Konfigurationen richtig sind, aktivieren Sie das Kontrollkästchen.
    3. Wählen Sie zum Starten der Bereitstellung die Option **Weiter** aus.

    ![Zusammenfassung der im Azure App Service-Installationsprogramm getroffenen Auswahloptionen][47]

1. Auf der nächsten Seite:
    1. Verfolgen Sie den Installationsstatus nach. Die Bereitstellung von App Service in Azure Stack Hub kann je nach Standardauswahl und Alter des zugrunde liegenden Windows 2016 Datacenter-Images bis zu 240 Minuten in Anspruch nehmen.

    2. Wählen Sie **Beenden** aus, nachdem die Ausführung des Installationsprogramms abgeschlossen ist.

    ![Nachverfolgen des Installationsvorgangs im Azure App Service-Installationsprogramm][48]

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie für den Azure App Service-Ressourcenanbieter eine SQL-Always On-Instanz bereitgestellt haben, **müssen** Sie die [Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Außerdem müssen Sie die Datenbanken synchronisieren, um bei einem Datenbankfailover einen Dienstverlust zu verhindern.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Administratorportal zur Netzwerksicherheitsgruppe „WorkersNsg“, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

- Quelle: Any
- Quellportbereich: *
- Ziel: IP-Adressen
- IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
- Zielportbereich: 445
- Protokoll: TCP
- Aktion: Allow
- Priorität: 700
- Name: Outbound_Allow_SMB445

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>Überprüfen der Installation von Azure App Service in Azure Stack Hub

1. Navigieren Sie im Azure Stack Hub-Administratorportal zu **Verwaltung – App Service**.

1. Überprüfen Sie in der Übersicht unter „Status“, ob für **Status** die Option **Alle Rollen sind bereit** angezeigt wird.

    ![Übersicht in der Azure App Service-Verwaltung](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Testen von Azure App Service in Azure Stack Hub

Nachdem Sie den Azure App Service-Ressourcenanbieter bereitgestellt und registriert haben, testen Sie ihn, um sicherzustellen, dass Benutzer Web- und API-Apps bereitstellen können.

> [!NOTE]
> Sie müssen ein Angebot erstellen, das den Namespace „Microsoft.Web“ im Plan enthält. Anschließend benötigen Sie ein Mandantenabonnement, das dieses Angebot abonniert. Weitere Informationen finden Sie unter [Erstellen eines Angebots](azure-stack-create-offer.md) und [Erstellen eines Plans](azure-stack-create-plan.md).
>
> Sie *müssen* über ein Mandantenabonnement verfügen, um Apps zu erstellen, die Azure App Service in Azure Stack Hub verwenden. Ein Dienstadministrator kann nur solche Funktionen im Administratorportal ausführen, die mit der Ressourcenanbieterverwaltung von Azure App Service zu tun haben. Hierzu gehören das Hinzufügen von Kapazitäten, das Konfigurieren von Bereitstellungsquellen und das Hinzufügen von Workerebenen und SKUs.
>
> Seit der dritten Technical Preview müssen Sie das Benutzerportal verwenden und über ein Mandantenabonnement verfügen, um Web-, API- und Azure Functions-Apps zu erstellen.

1. Wählen Sie im Azure Stack Hub-Benutzerportal **+ Ressource erstellen** > **Web + Mobil** > **Web-App** aus.

1. Geben Sie auf dem Blatt **Web-App** einen Namen in das Feld **Web-App** ein.

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu** aus. Geben Sie einen Namen in das Feld **Ressourcengruppe** ein.

1. Wählen Sie **App Service-Plan/Standort** > **Neu erstellen** aus.

1. Geben Sie auf dem Blatt **App Service-Plan** einen Namen in das Feld **App Service-Plan** ein.

1. Wählen Sie **Tarif** > **Free-Shared** oder **Shared-Shared** > **Auswählen** > **OK** > **Erstellen** aus.

1. Nach weniger als einer Minute wird auf dem Dashboard eine Kachel für die neue Web-App angezeigt. Wählen Sie die Kachel aus.

1. Wählen Sie auf dem Blatt **Web-App** die Option **Durchsuchen** aus, um die Standardwebsite für diese App anzuzeigen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Bereitstellen einer WordPress-, DNN- oder Django-Website (optional)

1. Wählen Sie im Azure Stack Hub-Benutzerportal **+** aus, wechseln Sie zu Azure Marketplace, stellen Sie eine Django-Website bereit, und warten Sie, bis der Vorgang erfolgreich abgeschlossen wurde. Die Django-Webplattform verwendet eine dateisystembasierte Datenbank. Sie erfordert keine zusätzlichen Ressourcenanbieter wie SQL oder MySQL.

1. Wenn Sie auch einen MySQL-Ressourcenanbieter bereitgestellt haben, können Sie über Azure Marketplace eine WordPress-Website bereitstellen. Wenn Sie zur Angabe der Datenbankparameter aufgefordert werden, geben Sie den Benutzernamen im Format *Benutzer1\@Server1* ein. Sie können einen Benutzer- und Servernamen Ihrer Wahl eingeben.

1. Falls Sie auch einen SQL Server-Ressourcenanbieter bereitgestellt haben, können Sie über Azure Marketplace eine DNN-Website bereitstellen. Wenn Sie zur Angabe der Datenbankparameter aufgefordert werden, wählen Sie auf dem SQL Server-Computer, der mit Ihrem Ressourcenanbieter verbunden ist, eine Datenbank aus.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Vorbereiten auf weitere Verwaltungsvorgänge für App Service unter Azure Stack Hub:

- [Kapazitätsplanung](azure-stack-app-service-capacity-planning.md)
- [Konfigurieren von Bereitstellungsquellen](azure-stack-app-service-configure-deployment-sources.md)

<!-- Connected image references-->
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

<!-- Disconnected image references-->
[31]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[32]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[33]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[34]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[35]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[36]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[37]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[38]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[39]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[40]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[41]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[42]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[43]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[44]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[45]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[46]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[47]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[48]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
