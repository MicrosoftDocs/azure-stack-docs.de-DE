---
title: Bereitstellen von App Service in Azure Stack in einer Hochverfügbarkeitskonfiguration | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie App Service unter Verwendung einer Hochverfügbarkeitskonfiguration in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: mabrigg
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 78476afd09d6c85fbe5790568dd46366beaf1991
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618364"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Bereitstellen von App Service in einer Hochverfügbarkeitskonfiguration

In diesem Artikel erfahren Sie, wie Sie App Service unter Verwendung von Elementen aus dem Azure Stack-Marketplace für Azure Stack in einer Hochverfügbarkeitskonfiguration bereitstellen. Neben verfügbaren Marketplace-Elementen wird bei dieser Lösung auch die Azure Stack-Schnellstartvorlage [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) verwendet. Diese Vorlage automatisiert die Erstellung einer hochverfügbaren Infrastruktur zum Hosten des App Service-Ressourcenanbieters. App Service wird dann in dieser hochverfügbaren VM-Infrastruktur installiert. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Bereitstellen der virtuellen Computer für die hochverfügbare App Service-Infrastruktur
Die Azure Stack-Schnellstartvorlage [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) vereinfacht die Bereitstellung von App Service in einer Hochverfügbarkeitskonfiguration. Sie muss im Standardabonnement des Anbieters bereitgestellt werden. 

Wenn die Vorlage zum Erstellen einer benutzerdefinierten Ressource in Azure Stack verwendet wird, erstellt sie Folgendes:
- Ein virtuelles Netzwerk und die erforderlichen Subnetze
- Netzwerksicherheitsgruppen für Dateiserver, SQL Server und AD DS-Subnetze (Active Directory Domain Services)
- Speicherkonten für VM-Datenträger und Clustercloudzeuge
- Interner Lastenausgleich für virtuelle SQL-Computer mit privater IP-Adresse, die an den SQL AlwaysOn-Listener gebunden ist
- Drei Verfügbarkeitsgruppen für AD DS, Dateiservercluster und SQL-Cluster
- SQL-Cluster mit zwei Knoten
- Dateiservercluster mit zwei Knoten
- Zwei Domänencontroller

### <a name="required-azure-stack-marketplace-items"></a>Erforderliche Azure Stack-Marketplace-Elemente
Vergewissern Sie sich vor Verwendung dieser Vorlage, dass die folgenden [Azure Stack-Marketplace-Elemente](azure-stack-marketplace-azure-items.md) in Ihrer Azure Stack-Instanz verfügbar sind:

- Windows Server 2016 Datacenter Core-Image (für AD DS und Dateiserver-VMs)
- SQL Server 2016 SP2 unter Windows Server 2016 (Enterprise)
- Neueste SQL-IaaS-Erweiterung 
- Neueste PowerShell-Erweiterung zum Konfigurieren des gewünschten Zustands (Desired State Configuration, DSC) 

> [!TIP]
> Die [Infodatei der Vorlage](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) auf GitHub enthält weitere Details zu Vorlagenanforderungen und Standardwerten. 

### <a name="deploy-the-app-service-infrastructure"></a>Bereitstellen der App Service-Infrastruktur
Gehen Sie wie in diesem Abschnitt beschrieben vor, um mithilfe der Azure Stack-Schnellstartvorlage **appservice-fileshare-sqlserver-ha** eine benutzerdefinierte Bereitstellung zu erstellen.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Klicken Sie auf **\+** **Ressource erstellen** > **Benutzerdefiniert** und dann auf **Vorlagenbereitstellung**.

   ![Benutzerdefinierte Vorlagenbereitstellung](media/app-service-deploy-ha/1.png)


3. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf **Vorlage bearbeiten** > **Schnellstartvorlage**, und wählen Sie dann aus der Dropdownliste der verfügbaren benutzerdefinierten Vorlagen die Vorlage **appservice-fileshare-sqlserver-ha** aus. Klicken Sie auf **OK** und dann auf **Speichern**.

   ![Auswählen der Schnellstartvorlage „appservice-fileshare-sqlserver-ha“](media/app-service-deploy-ha/2.png)

4. Wählen Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** die Option **Parameter bearbeiten** aus, und scrollen Sie nach unten, um sich die Standardwerte der Vorlage anzusehen. Ändern Sie diese Werte nach Bedarf, um alle erforderlichen Parameterinformationen anzugeben, und klicken Sie anschließend auf **OK**.<br><br> Dabei müssen mindestens für die Parameter „ADMINPASSWORD“, „FILESHAREOWNERPASSWORD“, „FILESHAREUSERPASSWORD“, „SQLSERVERSERVICEACCOUNTPASSWORD“ und „SQLLOGINPASSWORD“ komplexe Kennwörter angegeben werden.
    
   ![Bearbeiten benutzerdefinierter Bereitstellungsparameter](media/app-service-deploy-ha/3.png)

5. Vergewissern Sie sich auf dem Blatt **Benutzerdefinierte Bereitstellung**, dass **Standardabonnement des Anbieters** als zu verwendendes Abonnement ausgewählt ist, und erstellen Sie anschließend eine neue Ressourcengruppe für die benutzerdefinierte Bereitstellung, oder wählen Sie eine vorhandene Ressourcengruppe aus.<br><br> Wählen Sie als Nächstes den Standort der Ressourcengruppe (**lokal** bei ASDK-Installationen), und klicken Sie auf **Erstellen**. Die Einstellungen für die benutzerdefinierte Bereitstellung werden vor Beginn der Bereitstellung überprüft.

    ![Erstellen einer benutzerdefinierten Bereitstellung](media/app-service-deploy-ha/4.png)

6. Klicken Sie im Verwaltungsportal auf **Ressourcengruppen** und dann auf den Namen der Ressourcengruppe, die Sie für die benutzerdefinierte Bereitstellung erstellt haben (in diesem Beispiel: **app-service-ha**). Überprüfen Sie den Status der Bereitstellung, um sicherzustellen, dass alle Bereitstellungen erfolgreich abgeschlossen wurden.

   > [!NOTE]
   > Die Vorlagenbereitstellung dauert etwa eine Stunde.

   [![](media/app-service-deploy-ha/5-sm.png "Überprüfen des Status der Vorlagenbereitstellung")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Erfassen der Vorlagenausgaben
Erfassen Sie nach erfolgreicher Bereitstellung der Vorlage die Ausgaben der Vorlagenbereitstellung. Diese Angaben werden beim Ausführen des App Service-Installationsprogramms benötigt. 

Erfassen Sie jeweils folgende Ausgabewerte:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Die Ausgabewerte der Vorlage finden Sie wie folgt:

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Klicken Sie im Verwaltungsportal auf **Ressourcengruppen** und dann auf den Namen der Ressourcengruppe, die Sie für die benutzerdefinierte Bereitstellung erstellt haben (in diesem Beispiel: **app-service-ha**). 

3. Klicken Sie auf **Bereitstellungen**, und wählen Sie **Microsoft.Template** aus.

    ![Microsoft.Template-Bereitstellung](media/app-service-deploy-ha/6.png)

4. Nachdem Sie die Bereitstellung **Microsoft.Template** ausgewählt haben, wählen Sie **Ausgaben** aus, und erfassen Sie die Parameterausgabe der Vorlage. Diese Angabe wird beim Bereitstellen von App Service benötigt.

    ![Parameterausgabe](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Bereitstellen von App Service in einer Hochverfügbarkeitskonfiguration
Gehen Sie wie in diesem Abschnitt beschrieben vor, um App Service auf der Grundlage der Azure Stack-Schnellstartvorlage [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) für Azure Stack in einer Hochverfügbarkeitskonfiguration bereitzustellen. 

Nach dem Installieren des App Service-Ressourcenanbieters können Sie ihn in Ihren Angeboten und Plänen einfügen. Benutzer können ihn dann abonnieren, um den Dienst abzurufen und mit dem Erstellen von Anwendungen zu beginnen.

> [!IMPORTANT]
> Informieren Sie sich vor dem Ausführen des Installationsprogramms für den Ressourcenanbieter anhand der Versionshinweise für das entsprechende App Service-Release über neue Funktionen, Fehlerbehebungen und bekannte Probleme, die ggf. für Ihre Bereitstellung relevant sind.

### <a name="prerequisites"></a>Voraussetzungen
Vor dem Ausführen des App Service-Installationsprogramms müssen zunächst einige Vorbereitungsschritte ausgeführt werden, wie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md) beschrieben:

> [!TIP]
> Nicht alle in dem Vorbereitungsartikel beschriebenen Schritte sind erforderlich, da die Vorlagenbereitstellung Ihnen die Konfiguration der Infrastruktur-VMs abnimmt. 

- [Laden Sie das App Service-Installationsprogramm und die Hilfsskripts herunter.](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)
- [Laden Sie die neueste benutzerdefinierte Skripterweiterung in den Azure Stack-Marketplace herunter.](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)
- [Generieren Sie die erforderlichen Zertifikate.](azure-stack-app-service-before-you-get-started.md#get-certificates)
- Erstellen Sie die ID-Anwendung auf der Grundlage des Identitätsanbieters, den Sie für Azure Stack ausgewählt haben. Eine ID-Anwendung kann entweder für [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) oder für [Active Directory-Verbunddienste](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) erstellt werden. (Notieren Sie sich die Anwendungs-ID.)
- Vergewissern Sie sich, dass Sie das Windows Server 2016 Datacenter-Image dem Azure Stack-Marketplace hinzugefügt haben. Dies ist für die App Service-Installation erforderlich.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Bereitstellen von App Service in einer Hochverfügbarkeitskonfiguration
Die Installation des App Service-Ressourcenanbieters dauert mindestens eine Stunde. Die erforderliche Zeit hängt davon ab, wie viele Rolleninstanzen bereitgestellt werden. Während der Bereitstellung werden mit dem Installationsprogram folgende Tasks ausgeführt:

- Es erstellt im angegebenen Azure Stack-Speicherkonto einen Blobcontainer.
- Es erstellt eine DNS-Zone und DNS-Einträge für App Service.
- Es registriert den App Service-Ressourcenanbieter.
- Es registriert die App Service-Katalogelemente.

Führen Sie zum Bereitstellen eines App Service-Ressourcenanbieters die folgenden Schritte aus:

1. Führen Sie das zuvor heruntergeladene App Service-Installationsprogramm (**appservice.exe**) als Administrator auf einem Computer aus, der auf den Azure-Ressourcenverwaltungsendpunkt des Azure Stack-Administrators zugreifen kann.

2. Wählen Sie **App Service bereitstellen oder Upgrade auf die neueste Version durchführen** aus.

    ![Bereitstellen oder Upgraden](media/app-service-deploy-ha/01.png)

3. Akzeptieren Sie die Lizenzierungsbedingungen von Microsoft, und klicken Sie auf **Weiter**.

    ![Lizenzierungsbedingungen von Microsoft](media/app-service-deploy-ha/02.png)

4. Akzeptieren Sie die Microsoft-fremden Lizenzierungsbedingungen, und klicken Sie auf **Weiter**.

    ![Microsoft-fremde Lizenzierungsbedingungen](media/app-service-deploy-ha/03.png)

5. Geben Sie die App Service-Cloudendpunktkonfiguration für Ihre Azure Stack-Umgebung an.

    ![App Service-Cloudendpunktkonfiguration](media/app-service-deploy-ha/04.png)

6. **Stellen Sie eine Verbindung mit dem Azure Stack-Abonnement her**, das Sie für die Installation verwenden möchten, und wählen Sie den Speicherort aus. 

    ![Herstellen einer Verbindung mit dem Azure Stack-Abonnement](media/app-service-deploy-ha/05.png)

7. Wählen Sie **Vorhandenes VNET und Subnetze verwenden** und unter **Name der Ressourcengruppe** den Namen für die Ressourcengruppe aus, die zum Bereitstellen der hochverfügbaren Vorlage verwendet wird.<br><br>Wählen Sie als Nächstes das virtuelle Netzwerk aus, das im Rahmen der Vorlagenbereitstellung erstellt wurde, und wählen Sie anschließend in der Dropdownliste die entsprechenden Rollensubnetze aus. 

    ![VNET-Auswahl](media/app-service-deploy-ha/06.png)

8. Geben Sie für die Parameter für Dateifreigabepfad und Dateifreigabebesitzer die zuvor erfassten Informationen aus der Vorlagenausgabe an. Klicken Sie abschließend auf **Weiter**.

    ![Ausgabeinformationen für die Dateifreigabe](media/app-service-deploy-ha/07.png)

9. Da sich der für die App Service-Installation verwendete Computer nicht im gleichen VNET befindet wie der Dateiserver, der zum Hosten der App Service-Dateifreigabe verwendet wird, kann der Name nicht aufgelöst werden. **Dieses Verhalten wird erwartet**.<br><br>Vergewissern Sie sich, dass der Dateiserver-UNC-Pfad und die Kontoinformationen korrekt angegeben sind, und wählen Sie im Warnungsdialogfeld die Option **Ja** aus, um die App Service-Installation fortzusetzen.

    ![Erwarteter Fehler](media/app-service-deploy-ha/08.png)

    Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:
    - Quelle: Beliebig
    - Quellportbereich: *
    - Ziel: IP-Adressen
    - IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
    - Zielportbereich: 445
    - Protokoll: TCP
    - Aktion: ZULASSEN
    - Priorität: 700
    - Name: Outbound_Allow_SMB445

10. Geben Sie die ID der Identitätsanwendung sowie den Pfad und die Kennwörter für die Identitätszertifikate an, und klicken Sie auf **Weiter**:
    - Identitätsanwendungszertifikat (im Format **sso.appservice.local.azurestack.external.pfx**)
    - Azure Resource Manager-Stammzertifikat (**AzureStackCertificationAuthority.cer**)

    ![ID-Anwendungszertifikat und Stammzertifikat](media/app-service-deploy-ha/008.png)

10. Geben Sie als Nächstes die restlichen erforderlichen Informationen für die folgenden Zertifikate an, und klicken Sie auf **Weiter**:
    - Azure Stack-SSL-Standardzertifikat (im Format **_.appservice.local.azurestack.external.pfx**)
    - API-SSL-Zertifikat (im Format **api.appservice.local.azurestack.external.pfx**)
    - Herausgeberzertifikat (im Format **ftp.appservice.local.azurestack.external.pfx**) 

    ![Zusätzliche Konfigurationszertifikate](media/app-service-deploy-ha/09.png)

11. Geben Sie die SQL Server-Verbindungsinformationen unter Verwendung der SQL Server-Verbindungsinformationen aus der Ausgabe der Hochverfügbarkeitsvorlagenbereitstellung an:

    ![SQL Server-Verbindungsinformationen](media/app-service-deploy-ha/10.png)

12. Da sich der für die App Service-Installation verwendete Computer nicht im gleichen VNET befindet wie der SQL-Server, der zum Hosten der App Service-Datenbanken verwendet wird, kann der Name nicht aufgelöst werden.  **Dieses Verhalten wird erwartet**.<br><br>Vergewissern Sie sich, dass der SQL Server-Name und die Kontoinformationen korrekt angegeben sind, und wählen Sie **Ja** aus, um die App Service-Installation fortzusetzen. Klicken Sie auf **Weiter**.

    ![SQL Server-Verbindungsinformationen](media/app-service-deploy-ha/11.png)

13. Übernehmen Sie die Standardwerte der Rollenkonfiguration, oder legen Sie sie auf die empfohlenen Werte fest, und klicken Sie auf **Weiter**.<br><br>Es empfiehlt sich, die Standardwerte für die App Service-Infrastrukturrolleninstanzen wie folgt zu ändern, um eine hochverfügbare Konfiguration zu erhalten:

    |Rolle|Standard|Empfehlung für Hochverfügbarkeit|
    |-----|-----|-----|
    |Controllerrolle|2|2|
    |Verwaltungsrolle|1|3|
    |Herausgeberrolle|1|3|
    |FrontEnd-Rolle|1|3|
    |Freigegebene Workerrolle|1|10|
    |     |     |     |

    ![Werte der Infrastrukturrolleninstanzen](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Wenn Sie anstelle der Standardwerte die in diesem Tutorial empfohlenen Werte verwenden, erhöhen sich die Hardwareanforderungen für die App Service-Installation. Für die empfohlenen 21 virtuellen Computer werden insgesamt 26 Kerne und 46.592 MB RAM benötigt (anstelle der standardmäßigen 18 Kerne und 32.256 MB RAM für 15 virtuelle Computer).

14. Wählen Sie das gewünschte Plattformimage für die Installation der App Service-Infrastruktur-VMs aus, und klicken Sie auf **Weiter**:

    ![Auswahl des Plattformimages](media/app-service-deploy-ha/13.png)

15. Geben Sie die zu verwendenden Anmeldeinformationen für die App Service-Infrastrukturrollen an, und klicken Sie auf **Weiter**:

    ![Anmeldeinformationen für die Infrastrukturrollen](media/app-service-deploy-ha/14.png)

16. Überprüfen Sie die Angaben für die App Service-Bereitstellung, und klicken Sie auf **Weiter**, um die Bereitstellung zu starten. 

    ![Überprüfen der Installationszusammenfassung](media/app-service-deploy-ha/15.png)

17. Überprüfen Sie den Status der App Service-Bereitstellung. Der Vorgang kann abhängig von Ihrer Bereitstellungskonfiguration und Ihrer Hardware über eine Stunde dauern. Wählen Sie nach erfolgreichem Abschluss des Installationsprogramms die Option **Beenden** aus.

    ![Setup abgeschlossen](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Nächste Schritte

[Erweitern Sie App Service.](azure-stack-app-service-add-worker-roles.md) Gegebenenfalls müssen noch weitere App Service-Infrastrukturrollen-Worker hinzugefügt werden, um den zu erwartenden Anwendungsbedarf in Ihrer Umgebung zu decken. Standardmäßig unterstützt App Service in Azure Stack kostenlose und gemeinsam genutzte Workerebenen. Um andere Workerebenen hinzufügen zu können, müssen Sie weitere Workerrollen hinzufügen.

[Konfigurieren Sie Bereitstellungsquellen.](azure-stack-app-service-configure-deployment-sources.md) Zur Unterstützung der bedarfsgesteuerten Bereitstellung von verschiedenen Quellcodeverwaltungsanbietern wie GitHub, BitBucket, OneDrive und Dropbox sind weitere Konfigurationsschritte erforderlich.

[Sichern von App Service](app-service-back-up.md). Nach der erfolgreichen Bereitstellung und Konfiguration von App Service sollten Sie sicherstellen, dass alle für die Notfallwiederherstellung erforderlichen Komponenten gesichert werden, um Datenverlust zu vermeiden und unnötige Ausfallzeiten während der Wiederherstellungsprozesse zu vermeiden.