---
title: Bereitstellen von App Service in Azure Stack Hub in einer Hochverfügbarkeitskonfiguration
description: Hier erfahren Sie, wie Sie App Service unter Verwendung einer Hochverfügbarkeitskonfiguration in Azure Stack Hub bereitstellen.
author: BryanLa
ms.topic: article
ms.date: 01/02/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/02/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: e6d8708b15d5402d16b17a722640c9bf0c2251c4
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848029"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Bereitstellen von App Service in einer Hochverfügbarkeitskonfiguration

In diesem Artikel erfahren Sie, wie Sie App Service unter Verwendung von Elementen aus dem Azure Stack Hub-Marketplace für Azure Stack Hub in einer Hochverfügbarkeitskonfiguration bereitstellen. Neben verfügbaren Marketplace-Elementen wird bei dieser Lösung auch die Azure Stack Hub-Schnellstartvorlage [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) verwendet. Diese Vorlage automatisiert die Erstellung einer hochverfügbaren Infrastruktur zum Hosten des App Service-Ressourcenanbieters. App Service wird dann in dieser hochverfügbaren VM-Infrastruktur installiert. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Bereitstellen der virtuellen Computer für die hochverfügbare App Service-Infrastruktur
Die Azure Stack Hub-Schnellstartvorlage [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) vereinfacht die Bereitstellung von App Service in einer Hochverfügbarkeitskonfiguration. Sie muss im Standardabonnement des Anbieters bereitgestellt werden. 

Wenn die Vorlage zum Erstellen einer benutzerdefinierten Ressource in Azure Stack Hub verwendet wird, erstellt sie Folgendes:
- Ein virtuelles Netzwerk und die erforderlichen Subnetze
- Netzwerksicherheitsgruppen für Dateiserver, SQL Server und AD DS-Subnetze (Active Directory Domain Services)
- Speicherkonten für VM-Datenträger und Clustercloudzeuge
- Interner Lastenausgleich für virtuelle SQL-Computer mit privater IP-Adresse, die an den SQL AlwaysOn-Listener gebunden ist
- Drei Verfügbarkeitsgruppen für AD DS, Dateiservercluster und SQL-Cluster
- SQL-Cluster mit zwei Knoten
- Dateiservercluster mit zwei Knoten
- Zwei Domänencontroller

### <a name="required-azure-stack-hub-marketplace-items"></a>Erforderliche Azure Stack Hub-Marketplace-Elemente:
Vergewissern Sie sich vor Verwendung dieser Vorlage, dass die folgenden [Azure Stack Hub-Marketplace-Elemente](azure-stack-marketplace-azure-items.md) in Ihrer Azure Stack Hub-Instanz verfügbar sind:

- Windows Server 2016 Datacenter Core-Image (für AD DS und Dateiserver-VMs)
- SQL Server 2016 SP2 unter Windows Server 2016 (Enterprise)
- Neueste SQL-IaaS-Erweiterung 
- Neueste PowerShell-Erweiterung zum Konfigurieren des gewünschten Zustands (Desired State Configuration, DSC) 

> [!TIP]
> Die [Infodatei der Vorlage](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) auf GitHub enthält weitere Details zu Vorlagenanforderungen und Standardwerten. 

### <a name="deploy-the-app-service-infrastructure"></a>Bereitstellen der App Service-Infrastruktur
Gehen Sie wie in diesem Abschnitt beschrieben vor, um mithilfe der Azure Stack Hub-Schnellstartvorlage **appservice-fileshare-sqlserver-ha** eine benutzerdefinierte Bereitstellung zu erstellen.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Klicken Sie auf **\+** **Ressource erstellen** > **Benutzerdefiniert** und dann auf **Vorlagenbereitstellung**.

   ![Benutzerdefinierte Vorlagenbereitstellung](media/app-service-deploy-ha/1.png)


3. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf **Vorlage bearbeiten** > **Schnellstartvorlage**, und wählen Sie dann aus der Dropdownliste der verfügbaren Vorlagen die Vorlage **appservice-fileshare-sqlserver-ha** aus. Klicken Sie auf **OK** und dann auf **Speichern**.

   ![Auswählen der Schnellstartvorlage „appservice-fileshare-sqlserver-ha“](media/app-service-deploy-ha/2.png)

4. Wählen Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** die Option **Parameter bearbeiten** aus, und scrollen Sie nach unten, um sich die Standardwerte der Vorlage anzusehen. Ändern Sie diese Werte nach Bedarf, um alle erforderlichen Parameterinformationen anzugeben, und klicken Sie anschließend auf **OK**.<br><br> Geben Sie mindestens komplexe Kennwörter für die Parameter `ADMINPASSWORD`, `FILESHAREOWNERPASSWORD`, `FILESHAREUSERPASSWORD`, `SQLSERVERSERVICEACCOUNTPASSWORD` und `SQLLOGINPASSWORD` an.
    
   ![Bearbeiten benutzerdefinierter Bereitstellungsparameter](media/app-service-deploy-ha/3.png)

5. Vergewissern Sie sich auf dem Blatt **Benutzerdefinierte Bereitstellung**, dass **Standardabonnement des Anbieters** als zu verwendendes Abonnement ausgewählt ist, und erstellen Sie anschließend eine neue Ressourcengruppe für die benutzerdefinierte Bereitstellung, oder wählen Sie eine vorhandene Ressourcengruppe aus.<br><br> Wählen Sie als Nächstes den Standort der Ressourcengruppe (**lokal** bei ASDK-Installationen), und klicken Sie auf **Erstellen**. Die Einstellungen für die benutzerdefinierte Bereitstellung werden vor Beginn der Bereitstellung überprüft.

    ![Erstellen einer benutzerdefinierten Bereitstellung](media/app-service-deploy-ha/4.png)

6. Wählen Sie im Administratorportal **Ressourcengruppen** und dann den Namen der Ressourcengruppe aus, die Sie für die benutzerdefinierte Bereitstellung erstellt haben (in diesem Beispiel **app-service-ha**). Überprüfen Sie den Status der Bereitstellung, um sicherzustellen, dass alle Bereitstellungen erfolgreich abgeschlossen wurden.

   > [!NOTE]
   > Die Vorlagenbereitstellung dauert etwa eine Stunde.

   [![](media/app-service-deploy-ha/5-sm.png "Review template deployment status")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Erfassen der Vorlagenausgaben
Erfassen Sie nach erfolgreicher Bereitstellung der Vorlage die Ausgaben der Vorlagenbereitstellung. Sie benötigen diese Informationen während der Ausführung des App Service-Installationsprogramms.

Erfassen Sie jeweils folgende Ausgabewerte:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Die Ausgabewerte der Vorlage finden Sie wie folgt:

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Wählen Sie im Administratorportal **Ressourcengruppen** und dann den Namen der Ressourcengruppe aus, die Sie für die benutzerdefinierte Bereitstellung erstellt haben (in diesem Beispiel **app-service-ha**). 

3. Klicken Sie auf **Bereitstellungen**, und wählen Sie **Microsoft.Template** aus.

    ![Microsoft. Bereitstellung von Vorlagen](media/app-service-deploy-ha/6.png)

4. Nachdem Sie die Bereitstellung **Microsoft.Template** ausgewählt haben, wählen Sie **Ausgaben** aus, und erfassen Sie die Parameterausgabe der Vorlage. Diese Informationen werden beim Bereitstellen von App Service benötigt.

    ![Parameterausgabe](media/app-service-deploy-ha/7.png)

## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Bereitstellen von App Service in einer Hochverfügbarkeitskonfiguration
Gehen Sie wie in diesem Abschnitt beschrieben vor, um App Service auf der Grundlage der Azure Stack Hub-Schnellstartvorlage [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) für Azure Stack Hub in einer Hochverfügbarkeitskonfiguration bereitzustellen. 

Nach dem Installieren des App Service-Ressourcenanbieters können Sie ihn in Ihren Angeboten und Plänen einfügen. Benutzer können ihn dann abonnieren, um den Dienst abzurufen und mit dem Erstellen von Anwendungen zu beginnen.

> [!IMPORTANT]
> Informieren Sie sich vor dem Ausführen des Installationsprogramms für den Ressourcenanbieter anhand der Versionshinweise für das entsprechende App Service-Release über neue Funktionen, Fehlerbehebungen und bekannte Probleme, die ggf. für Ihre Bereitstellung relevant sind.

### <a name="prerequisites"></a>Voraussetzungen
Vor dem Ausführen des App Service-Installationsprogramms müssen zunächst einige Vorbereitungsschritte ausgeführt werden, wie unter [Vor den ersten Schritten mit App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) beschrieben:

> [!TIP]
> Nicht alle im Artikel [Vor den ersten Schritten mit App Service](azure-stack-app-service-before-you-get-started.md) beschriebenen Schritte sind erforderlich, da die Vorlagenbereitstellung Ihnen die Konfiguration der Infrastruktur-VMs abnimmt.

- [Laden Sie das App Service-Installationsprogramm und die Hilfsskripts herunter.](azure-stack-app-service-before-you-get-started.md#installer-and-helper-scripts)
- [Laden Sie Elemente aus dem Azure Stack Hub-Marketplace herunter](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace).
- [Generieren Sie erforderliche Zertifikate für Ihren Bereitstellungstyp](azure-stack-app-service-before-you-get-started.md).
- Erstellen Sie die ID-Anwendung auf der Grundlage des Identitätsanbieters, den Sie für Azure Stack Hub ausgewählt haben. Eine ID-Anwendung kann entweder für [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app) oder für [Active Directory-Verbunddienste](azure-stack-app-service-before-you-get-started.md#create-an-adfs-app) erstellt werden. (Notieren Sie sich die Anwendungs-ID.)
<!-- Connected --->
::: zone pivot="state-connected"
- Vergewissern Sie sich, dass Sie das Windows Server 2016 Datacenter-Image dem Azure Stack Hub-Marketplace hinzugefügt haben. Dieses Image ist für die App Service-Installation erforderlich.
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
- **Vollständiges VM-Image für Windows Server 2016 Datacenter mit aktiviertem Microsoft.Net 3.5.1 SP1**.  Für Azure App Service in Azure Stack Hub wird vorausgesetzt, dass im Image für die Bereitstellung Microsoft .NET 3.5.1 SP1 aktiviert ist. In über Marketplace syndizierten Windows Server 2016-Images ist diese Funktion nicht aktiviert, und sie können in nicht verbundenen Umgebungen Microsoft Update nicht erreichen, um die über DSIM zu installierenden Pakete herunterzuladen. Daher müssen Sie bei getrennten Bereitstellungen ein Windows Server 2016-Image erstellen und verwenden, für das dieses Feature vorab aktiviert ist.

   Ausführliche Informationen zum Erstellen eines benutzerdefinierten Images und zum Hinzufügen zum Marketplace finden Sie unter [Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub](azure-stack-add-vm-image.md). Geben Sie beim Hinzufügen des Images zum Marketplace unbedingt die folgenden Eigenschaften an:

   - Herausgeber: MicrosoftWindowsServer
   - Angebot: WindowsServer
   - SKU: 2016-Datacenter
   - Version: Geben Sie die aktuelle Version an.

::: zone-end

<!-- For All --> 

### <a name="steps-for-app-service-deployment"></a>Schritte für die App Service-Bereitstellung
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

<!-- Connected --->
::: zone pivot="state-connected"
Führen Sie zum Bereitstellen des App Service-Ressourcenanbieters die folgenden Schritte aus:

1. Führen Sie das zuvor heruntergeladene App Service-Installationsprogramm (**appservice.exe**) als Administrator auf einem Computer aus, der auf den Azure-Ressourcenverwaltungsendpunkt des Azure Stack Hub-Administrators zugreifen kann.

2. Wählen Sie **App Service bereitstellen oder Upgrade auf die neueste Version durchführen** aus.

    ![Bereitstellen oder Ausführen eines Upgrades von App Service](media/app-service-deploy-ha/01.png)

3. Akzeptieren Sie die Lizenzierungsbedingungen von Microsoft, und klicken Sie auf **Weiter**.

    ![Lizenzierungsbedingungen von Microsoft für App Service](media/app-service-deploy-ha/02.png)

4. Akzeptieren Sie die Microsoft-fremden Lizenzierungsbedingungen, und klicken Sie auf **Weiter**.

    ![Microsoft-fremde Lizenzierungsbedingungen für App Service](media/app-service-deploy-ha/03.png)

5. Geben Sie die App Service-Cloudendpunktkonfiguration für Ihre Azure Stack Hub-Umgebung an.

    ![App Service-Cloudendpunktkonfiguration für App Service](media/app-service-deploy-ha/04.png)

6. **Stellen Sie eine Verbindung mit dem Azure Stack Hub-Abonnement her**, das Sie für die Installation verwenden möchten, und wählen Sie den Speicherort aus. 

    ![Herstellen einer Verbindung mit dem Azure Stack Hub-Abonnement in App Service](media/app-service-deploy-ha/05.png)

7. Wählen Sie **Vorhandenes VNET und Subnetze verwenden** und unter **Name der Ressourcengruppe** den Namen für die Ressourcengruppe aus, die zum Bereitstellen der hochverfügbaren Vorlage verwendet wird.<br><br>Wählen Sie als Nächstes das virtuelle Netzwerk aus, das im Rahmen der Vorlagenbereitstellung erstellt wurde, und wählen Sie anschließend in der Dropdownliste die entsprechenden Rollensubnetze aus. 

    ![VNet-Auswahl in App Service](media/app-service-deploy-ha/06.png)

8. Geben Sie für die Parameter für Dateifreigabepfad und Dateifreigabebesitzer die zuvor erfassten Informationen aus der Vorlagenausgabe an. Klicken Sie abschließend auf **Weiter**.

    ![Ausgabeinformationen für die Dateifreigabe in App Service](media/app-service-deploy-ha/07.png)

9. Da sich der für die App Service-Installation verwendete Computer nicht im gleichen VNet befindet wie der Dateiserver, der die App Service-Dateifreigabe hostet, kann der Name nicht aufgelöst werden. **Dies ist ein erwarteter Fehler**.<br><br>Vergewissern Sie sich, dass die für den Dateifreigabe-UNC-Pfad eingegebenen Informationen und die Kontoinformationen korrekt sind. Wählen Sie anschließend im Dialogfeld mit der Benachrichtigung **Ja** aus, um mit der App Service-Installation fortzufahren.

    ![Dialogfeld mit erwartetem Fehler in App Service](media/app-service-deploy-ha/08.png)

10. Geben Sie die ID der Identitätsanwendung sowie den Pfad und die Kennwörter für die Identitätszertifikate an, und klicken Sie auf **Weiter**:
    - Identitätsanwendungszertifikat (im Format **sso.appservice.local.azurestack.external.pfx**)
    - Azure Resource Manager-Stammzertifikat (**AzureStackCertificationAuthority.cer**)

    ![ID-Anwendungszertifikat und Stammzertifikat in App Service](media/app-service-deploy-ha/008.png)

11. Geben Sie als Nächstes die restlichen erforderlichen Informationen für die folgenden Zertifikate an, und klicken Sie auf **Weiter**:
    - Azure Stack Hub-SSL-Standardzertifikat (im Format **_.appservice.local.azurestack.external.pfx**)
    - API-SSL-Zertifikat (im Format **api.appservice.local.azurestack.external.pfx**)
    - Herausgeberzertifikat (im Format **ftp.appservice.local.azurestack.external.pfx**) 

    ![Zusätzliche Konfigurationszertifikate für App Service](media/app-service-deploy-ha/09.png)

12. Geben Sie die SQL Server-Verbindungsinformationen unter Verwendung der SQL Server-Verbindungsinformationen aus der Ausgabe der Hochverfügbarkeitsvorlagenbereitstellung an:

    ![SQL Server-Verbindungsinformationen für App Service](media/app-service-deploy-ha/10.png)

13. Da sich der für die App Service-Installation verwendete Computer nicht im gleichen VNet befindet wie der SQL Server, der die App Service-Datenbanken hostet, kann der Name nicht aufgelöst werden.  **Dieses Verhalten wird erwartet**.<br><br>Vergewissern Sie sich, dass der SQL Server-Name und die Kontoinformationen korrekt angegeben sind, und wählen Sie **Ja** aus, um die App Service-Installation fortzusetzen. Klicken Sie auf **Weiter**.

    ![SQL Server-Verbindungsinformationen für App Service](media/app-service-deploy-ha/11.png)

14. Übernehmen Sie die Standardwerte der Rollenkonfiguration, oder legen Sie sie auf die empfohlenen Werte fest, und klicken Sie auf **Weiter**.<br><br>Es empfiehlt sich, die Standardwerte für die App Service-Infrastrukturrolleninstanzen wie folgt zu ändern, um eine hochverfügbare Konfiguration zu erhalten:

    |Role|Standard|Empfehlung für Hochverfügbarkeit|
    |-----|-----|-----|
    |Controllerrolle|2|2|
    |Verwaltungsrolle|1|3|
    |Herausgeberrolle|1|3|
    |FrontEnd-Rolle|1|3|
    |Freigegebene Workerrolle|1|2|
    |     |     |     |

    ![Werte der Infrastrukturrolleninstanzen für App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Wenn Sie anstelle der Standardwerte die in diesem Tutorial empfohlenen Werte verwenden, erhöhen sich die Hardwareanforderungen für die App Service-Installation. Für die empfohlenen 13 virtuellen Computer werden insgesamt 18 Kerne und 32.256 MB RAM benötigt (anstelle der standardmäßigen 9 Kerne und 16.128 MB RAM für 6 virtuelle Computer).

15. Wählen Sie das gewünschte Plattformimage für die Installation der App Service-Infrastruktur-VMs aus, und klicken Sie auf **Weiter**:

    ![Auswahl des Plattformimages für App Service](media/app-service-deploy-ha/13.png)

16. Geben Sie die zu verwendenden Anmeldeinformationen für die App Service-Infrastrukturrollen an, und klicken Sie auf **Weiter**:

    ![Anmeldeinformationen für die Infrastrukturrollen für App Service](media/app-service-deploy-ha/14.png)

17. Überprüfen Sie die Angaben für die App Service-Bereitstellung, und klicken Sie auf **Weiter**, um die Bereitstellung zu starten.

    ![Überprüfen der Installationszusammenfassung für App Service](media/app-service-deploy-ha/15.png)

18. Überprüfen Sie den Status der App Service-Bereitstellung. Diese Bereitstellung kann abhängig von Ihrer Bereitstellungskonfiguration und Ihrer Hardware über eine Stunde dauern. Wählen Sie nach erfolgreichem Abschluss des Installationsprogramms die Option **Beenden** aus.

    ![Setup abgeschlossen für App Service](media/app-service-deploy-ha/16.png)

::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->

## <a name="create-an-offline-installation-package"></a>Erstellen eines Offlineinstallationspakets

Erstellen Sie zum Bereitstellen von Azure App Service in einer Offlineumgebung zunächst ein Offlineinstallationspaket auf einem Computer, der über eine Internetverbindung verfügt.

1. Führen Sie das Installationsprogramm „AppService.exe“ auf einem Computer aus, der mit dem Internet verbunden ist. 

1. Wählen Sie **Erweitert** > **Offlineinstallationspaket erstellen** aus. Dieser Schritt dauert einige Minuten.

    ![Erstellen eines Offlinepakets im Azure App Service-Installationsprogramm][31]

1. Das Azure App Service-Installationsprogramm erstellt ein Offlineinstallationspaket und zeigt den Pfad zu diesem an. Sie können **Ordner öffnen** auswählen, um den Ordner im Datei-Explorer zu öffnen.

    ![Das Offlineinstallationspaket wurde im Azure App Service-Installationsprogramm erfolgreich generiert.](media/azure-stack-app-service-deploy-offline/image02.png)

1. Kopieren Sie das Installationsprogramm („AppService.exe“) und das Offlineinstallationspaket auf einen Computer, der eine Verbindung mit Azure Stack Hub herstellen kann.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Abschließen der Offlineinstallation von Azure App Service in Azure Stack Hub

1. Führen Sie „AppService.exe“ als Administrator auf einem Computer aus, der den Azure Resource Manager-Endpunkt des Azure Stack Hub-Administrators erreichen kann.

1. Wählen Sie **Erweitert** > **Offlineinstallation abschließen** aus.

    ![Abschließen der Offlineinstallation im Azure App Service-Installationsprogramm][32]

1. Navigieren Sie zum Speicherort des zuvor erstellten Offlineinstallationspakets, und wählen Sie dann **Weiter** aus.

    ![Angeben des Pfads zum Offlineinstallationspaket im Azure App Service-Installationsprogramm](media/azure-stack-app-service-deploy-offline/image04.png)

1. Akzeptieren Sie die Lizenzierungsbedingungen von Microsoft, und klicken Sie auf **Weiter**.

    ![Lizenzierungsbedingungen von Microsoft für App Service](media/app-service-deploy-ha/02.png)

1. Akzeptieren Sie die Microsoft-fremden Lizenzierungsbedingungen, und klicken Sie auf **Weiter**.

    ![Microsoft-fremde Lizenzierungsbedingungen für App Service](media/app-service-deploy-ha/03.png)

1. Geben Sie die App Service-Cloudendpunktkonfiguration für Ihre Azure Stack Hub-Umgebung an.

    ![App Service-Cloudendpunktkonfiguration für App Service](media/app-service-deploy-ha/04.png)

1. **Stellen Sie eine Verbindung mit dem Azure Stack Hub-Abonnement her**, das Sie für die Installation verwenden möchten, und wählen Sie den Speicherort aus. 

    ![Herstellen einer Verbindung mit dem Azure Stack Hub-Abonnement in App Service](media/app-service-deploy-ha/05.png)

1. Wählen Sie **Vorhandenes VNET und Subnetze verwenden** und unter **Name der Ressourcengruppe** den Namen für die Ressourcengruppe aus, die zum Bereitstellen der hochverfügbaren Vorlage verwendet wird.  Wählen Sie als Nächstes das virtuelle Netzwerk aus, das im Rahmen der Vorlagenbereitstellung erstellt wurde, und wählen Sie anschließend in der Dropdownliste die entsprechenden Rollensubnetze aus. 

    ![VNet-Auswahl in App Service](media/app-service-deploy-ha/06.png)

1. Geben Sie für die Parameter für Dateifreigabepfad und Dateifreigabebesitzer die zuvor erfassten Informationen aus der Vorlagenausgabe an. Klicken Sie abschließend auf **Weiter**.

    ![Ausgabeinformationen für die Dateifreigabe in App Service](media/app-service-deploy-ha/07.png)

1. Da sich der für die App Service-Installation verwendete Computer nicht im gleichen VNet befindet wie der Dateiserver, der die App Service-Dateifreigabe hostet, kann der Name nicht aufgelöst werden. **Dies ist ein erwarteter Fehler**.  Vergewissern Sie sich, dass die für den Dateifreigabe-UNC-Pfad eingegebenen Informationen und die Kontoinformationen korrekt sind. Wählen Sie anschließend im Dialogfeld mit der Benachrichtigung **Ja** aus, um mit der App Service-Installation fortzufahren.

    ![Dialogfeld mit erwartetem Fehler in App Service](media/app-service-deploy-ha/08.png)

1. Geben Sie die ID der Identitätsanwendung sowie den Pfad und die Kennwörter für die Identitätszertifikate an, und klicken Sie auf **Weiter**:
    - Identitätsanwendungszertifikat (im Format **sso.appservice.local.azurestack.external.pfx**)
    - Azure Resource Manager-Stammzertifikat (**AzureStackCertificationAuthority.cer**)

    ![ID-Anwendungszertifikat und Stammzertifikat in App Service](media/app-service-deploy-ha/008.png)

1. Geben Sie als Nächstes die restlichen erforderlichen Informationen für die folgenden Zertifikate an, und klicken Sie auf **Weiter**:
    - Azure Stack Hub-SSL-Standardzertifikat (im Format **_.appservice.local.azurestack.external.pfx**)
    - API-SSL-Zertifikat (im Format **api.appservice.local.azurestack.external.pfx**)
    - Herausgeberzertifikat (im Format **ftp.appservice.local.azurestack.external.pfx**) 

    ![Zusätzliche Konfigurationszertifikate für App Service](media/app-service-deploy-ha/09.png)

1. Geben Sie die SQL Server-Verbindungsinformationen unter Verwendung der SQL Server-Verbindungsinformationen aus der Ausgabe der Hochverfügbarkeitsvorlagenbereitstellung an:

    ![SQL Server-Verbindungsinformationen für App Service](media/app-service-deploy-ha/10.png)

1. Da sich der für die App Service-Installation verwendete Computer nicht im gleichen VNet befindet wie der SQL Server, der die App Service-Datenbanken hostet, kann der Name nicht aufgelöst werden.  **Dieses Verhalten wird erwartet**.  Vergewissern Sie sich, dass der SQL Server-Name und die Kontoinformationen korrekt angegeben sind, und wählen Sie **Ja** aus, um die App Service-Installation fortzusetzen. Klicken Sie auf **Weiter**.

    ![SQL Server-Verbindungsinformationen für App Service](media/app-service-deploy-ha/11.png)

1. Übernehmen Sie die Standardwerte der Rollenkonfiguration, oder legen Sie sie auf die empfohlenen Werte fest, und klicken Sie auf **Weiter**.  Es empfiehlt sich, die Standardwerte für die App Service-Infrastrukturrolleninstanzen wie folgt zu ändern, um eine hochverfügbare Konfiguration zu erhalten:

    |Role|Standard|Empfehlung für Hochverfügbarkeit|
    |-----|-----|-----|
    |Controllerrolle|2|2|
    |Verwaltungsrolle|1|3|
    |Herausgeberrolle|1|3|
    |FrontEnd-Rolle|1|3|
    |Freigegebene Workerrolle|1|2|
    |     |     |     |

    ![Werte der Infrastrukturrolleninstanzen für App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Wenn Sie anstelle der Standardwerte die in diesem Tutorial empfohlenen Werte verwenden, erhöhen sich die Hardwareanforderungen für die App Service-Installation. Für die empfohlenen 13 virtuellen Computer werden insgesamt 18 Kerne und 32.256 MB RAM benötigt (anstelle der standardmäßigen 9 Kerne und 16.128 MB RAM für 6 virtuelle Computer).

1. Wählen Sie das gewünschte Plattformimage für die Installation der App Service-Infrastruktur-VMs aus, und klicken Sie auf **Weiter**:

    ![Auswahl des Plattformimages für App Service](media/app-service-deploy-ha/13.png)

1. Geben Sie die zu verwendenden Anmeldeinformationen für die App Service-Infrastrukturrollen an, und klicken Sie auf **Weiter**:

    ![Anmeldeinformationen für die Infrastrukturrollen für App Service](media/app-service-deploy-ha/14.png)

1. Überprüfen Sie die Angaben für die App Service-Bereitstellung, und klicken Sie auf **Weiter**, um die Bereitstellung zu starten.

    ![Überprüfen der Installationszusammenfassung für App Service](media/app-service-deploy-ha/15.png)

1. Überprüfen Sie den Status der App Service-Bereitstellung. Diese Bereitstellung kann abhängig von Ihrer Bereitstellungskonfiguration und Ihrer Hardware über eine Stunde dauern. Wählen Sie nach erfolgreichem Abschluss des Installationsprogramms die Option **Beenden** aus.

    ![Setup abgeschlossen für App Service](media/app-service-deploy-ha/16.png)

::: zone-end

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

1. Sie **müssen** [die appservice_hosting-und appservice_metering-Datenbanken einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Außerdem müssen Sie die Datenbanken synchronisieren, um bei einem Datenbankfailover einen Dienstverlust zu verhindern.

2. Sie haben App Service in einem vorhandenen virtuellen Netzwerk bereitgestellt und verwenden eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver. Sie **müssen** eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Administratorportal zur Netzwerksicherheitsgruppe „WorkersNsg“, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

- Quelle: Any
- Quellportbereich: *
- Ziel: IP-Adressen
- IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
- Zielportbereich: 445
- Protokoll: TCP
- Aktion: Allow
- Priorität: 700
- Name: Outbound_Allow_SMB445

## <a name="next-steps"></a>Nächste Schritte

[Fügen Sie die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzu](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), wenn Sie für den App Service-Ressourcenanbieter eine SQL Always On-Instanz bereitgestellt haben. Synchronisieren Sie die Datenbanken, um bei einem Datenbankfailover einen Dienstverlust zu verhindern. Sie können auch ein [Skript](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/) ausführen, um die AppServices-Anmeldungen vom ursprünglichen primären Server auf einen Failoverserver zu importieren.

[Skalieren Sie App Service auf.](azure-stack-app-service-add-worker-roles.md) Gegebenenfalls müssen noch weitere App Service-Infrastrukturrollen-Worker hinzugefügt werden, um den zu erwartenden Anwendungsbedarf in Ihrer Umgebung zu decken. Standardmäßig unterstützt App Service in Azure Stack Hub kostenlose und gemeinsam genutzte Workerebenen. Um andere Workerebenen hinzufügen zu können, müssen Sie weitere Workerrollen hinzufügen.

[Konfigurieren Sie Bereitstellungsquellen.](azure-stack-app-service-configure-deployment-sources.md) Zur Unterstützung der bedarfsgesteuerten Bereitstellung von verschiedenen Quellcodeverwaltungsanbietern wie GitHub, BitBucket, OneDrive und Dropbox sind weitere Konfigurationsschritte erforderlich.

[Sichern von App Service](app-service-back-up.md). Nach der erfolgreichen Bereitstellung und Konfiguration von App Service sollten Sie sicherstellen, dass alle für die Notfallwiederherstellung erforderlichen Komponenten gesichert werden. Das Sichern wichtiger Komponenten trägt dazu bei, Datenverlust zu vermeiden und unnötige Ausfallzeiten während der Wiederherstellungsprozesse zu vermeiden.
