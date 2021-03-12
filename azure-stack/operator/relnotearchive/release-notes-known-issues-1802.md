---
title: Azure Stack 1802-Update | Microsoft-Dokumentation
description: Erfahren Sie, was in Update 1802 für integrierte Azure Stack-Systeme enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 63a42e4b3cab1273c84c52367178c00442bf2cf2
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187230"
---
# <a name="azure-stack-1802-update"></a>Azure Stack-Update 1802

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird beschrieben, welche Verbesserungen und Fehlerbehebungen in diesem Updatepaket 1802 enthalten sind, welche bekannten Probleme es für dieses Release gibt und wo das Update heruntergeladen werden kann. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]        
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz    
Die Buildnummer des Azure Stack-Updates 1802 ist **20180302.1**.  


## <a name="before-you-begin"></a>Vorbereitung    
> [!IMPORTANT]    
> Versuchen Sie nicht, während der Installation dieses Updates virtuelle Computer zu erstellen. Weitere Informationen zum Verwalten von Updates finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).


### <a name="prerequisites"></a>Voraussetzungen
- Installieren Sie das Azure Stack 1712-Update, bevor Sie das Azure Stack 1802-Update anwenden.    

- Installieren Sie **AzS-Hotfix 1.0.180312.1 – Build 20180222.2**, bevor Sie das Azure Stack 1802-Update anwenden. Durch diesen Hotfix wird Windows Defender aktualisiert. Dieser ist beim Herunterladen von Updates für Azure Stack verfügbar.

  Um den Hotfix zu installieren, führen Sie die üblichen Verfahren zum [Installieren von Updates für Azure Stack](../azure-stack-apply-updates.md) durch. Der Name des Updates wird als **AzS Hotfix 1.0.180312.1** angezeigt und enthält die folgenden Dateien: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Nachdem Sie diese Dateien in ein Speicherkonto und in einen Container hochgeladen haben, führen Sie die Installation im Verwaltungsportal über die Kachel „Aktualisieren“ aus. 
  
  Im Gegensatz zu Updates für Azure Stack wird die Version von Azure Stack nicht durch die Installation dieses Updates geändert.  Um zu überprüfen, ob dieses Update installiert wurde, sehen Sie sich die Liste **Installierte Updates** an.
 


### <a name="post-update-steps"></a>Schritte nach dem Update
Installieren Sie nach der Installation von 1802 alle entsprechenden Hotfixes. Weitere Informationen finden Sie in den folgenden Knowledge Base-Artikeln sowie in unserer [Wartungsrichtlinie](../azure-stack-servicing-policy.md). 
- Azure Stack-Hotfix **1.0.180302.4**. [KB 4131152: Vorhandene Virtual Machine Scale Sets-Instanzen werden möglicherweise unbrauchbar.]( https://support.microsoft.com/help/4131152) 

  Mit diesem Hotfix werden auch die unter [KB 4103348 – Netzwerkcontroller-API-Dienst stürzt ab, wenn versucht wird, ein Azure Stack-Update zu installieren](https://support.microsoft.com/help/4103348) beschriebenen Probleme behoben.


### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen
Dieses Update enthält die folgenden Verbesserungen und Fehlerbehebungen für Azure Stack.

- **Die Unterstützung wurde auf die folgenden Versionen der Azure Storage-Dienst-API erweitert**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Weitere Informationen finden Sie unter [Azure Stack-Speicher: Unterschiede und Überlegungen](../../user/azure-stack-acs-differences.md).

- **Unterstützung für größere [Blockblobs](../../user/azure-stack-acs-differences.md)**:
    - Die maximal zulässige Blockgröße wurde von 4 MB auf 100 MB erhöht.
    - Die maximale Blobgröße wurde von 195 GB auf 4,75 TB erhöht.  

- Die **Infrastruktursicherung** wird ab sofort in der Kachel „Ressourcenanbieter“ angezeigt, und Benachrichtigungen in Bezug auf Sicherungen sind aktiviert. Weitere Informationen zum Infrastructure Backup-Dienst finden Sie unter [Sicherung und Datenwiederherstellung für Azure Stack mit dem Infrastructure Backup-Dienst](../azure-stack-backup-infrastructure-backup.md).

- **Führen Sie ein Update auf das Cmdlet *Test-AzureStack* durch**, um die Diagnose für den Speicher zu verbessern. Weitere Informationen zu diesem Cmdlet finden Sie unter [Ausführen eines Validierungstests für Azure Stack](../azure-stack-diagnostic-test.md).

- **Verbesserungen an der rollenbasierten Zugriffssteuerung (RBAC)** – Ab sofort können Sie mithilfe der RBAC Berechtigungen an universellen Benutzergruppen delegieren, wenn Azure Stack mit AD FS bereitgestellt wird. Weitere Informationen zur RBAC finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung](../azure-stack-manage-permissions.md).

- **Es wurde Unterstützung für mehrere Fehlerdomänen hinzugefügt**.  Weitere Informationen finden Sie unter Hochverfügbarkeit für Azure Stack.

- **Unterstützung von Upgrades für den physischen Speicher:** Sie können jetzt die Speicherkapazität des integrierten Azure Stack-Systems nach der Erstbereitstellung erweitern. Weitere Informationen finden Sie unter [Verwalten der Kapazität des physischen Speichers für Azure Stack](../azure-stack-manage-storage-physical-memory-capacity.md).

- Es wurden **verschiedene Fehlerbehebungen** hinsichtlich der Leistung, Stabilität, Sicherheit und des von Azure Stack eingesetzten Betriebssystems vorgenommen.

<!-- #### New features -->

<!-- #### Fixes -->

### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess    
*Es liegen keine bekannten Probleme für die Installation des Updates 1802 vor.*


### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)
Im Folgenden werden bekannte Probleme nach der Installation zum Build **20180302.1** vorgestellt:

#### <a name="portal"></a>Portal
<!-- 2332636 - IS -->  
-   Wenn Sie AD FS für Ihr Azure Stack-Identitätssystem verwenden und auf diese Version von Azure Stack aktualisieren, wird der Standardbesitzer des Abonnements für Standardanbieter auf den integrierten **CloudAdmin**-Benutzer zurückgesetzt.  
  Problemumgehung: Um dieses Problem nach der Installation dieses Updates zu beheben, führen Sie Schritt 3 unter [Auslösen der Automatisierung zum Konfigurieren der Anspruchsanbieter-Vertrauensstellung in Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) aus, um den Besitzer des Abonnements für Standardanbieter zurückzusetzen.   

- Die Funktion, mit der innerhalb des Administratorportals [eine neue Supportanfrage über die Dropdownliste geöffnet wird](../azure-stack-manage-portals.md#quick-access-to-help-and-support), ist nicht verfügbar. Verwenden Sie stattdessen den folgenden Link:     
    - Für integrierte Azure Stack-Systeme muss https://aka.ms/newsupportrequest verwendet werden.

<!-- 2050709 --> 
-  Es ist nicht möglich, im Verwaltungsportal Speichermetriken für Blob-Dienst, Tabellenspeicherdienst oder Warteschlangendienst zu bearbeiten. Wenn Sie zum Speicher navigieren und dann die Kachel für Blob-Dienst, Tabellenspeicherdienst oder Warteschlangendienst auswählen, wird ein neues Blatt mit dem Metrikdiagramm für diesen Dienst geöffnet. Wenn Sie anschließend oben in der Kachel „Metrikdiagramm“ die Option „Bearbeiten“ auswählen, wird das Blatt "Diagramm bearbeiten" geöffnet, welches jedoch keine Optionen zum Bearbeiten von Metriken anzeigt.

- Es ist es u.U. nicht möglich, Compute- oder Speicherressourcen im Administratorportal anzuzeigen. Grund für dieses Problem ist ein Fehler bei der Installation des Updates, der dazu führt, dass das Update fälschlicherweise als erfolgreich gemeldet wird. Wenn dieses Problem auftritt, wenden Sie sich an Microsoft Customer Support Services, um Unterstützung zu erhalten.

- Es kann sein, dass im Portal ein leeres Dashboard angezeigt wird. Wählen Sie zum Wiederherstellen des Dashboards oben rechts im Portal das Zahnradsymbol und dann die Option **Standardeinstellungen wiederherstellen**.

- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung PowerShell, um Berechtigungen zu überprüfen.

- Im Dashboard des Verwaltungsportals tritt beim Anzeigen von Informationen zu Updates über die Kachel „Aktualisieren“ ein Fehler auf. Klicken Sie zur Behebung dieses Problems auf die Kachel, um sie zu aktualisieren.

-   Im Verwaltungsportal wird möglicherweise eine kritische Benachrichtigung für die Komponente „Microsoft.Update.Admin“ angezeigt. Name, Beschreibung und Behebung der Warnung werden wie folgt angezeigt:  
    - *FEHLER: Vorlage für FaultType ResourceProviderTimeout fehlt.*

    Diese Warnung kann ignoriert werden. 

<!-- 2253274 --> 
- In den Administrator- und Benutzerportalen kann das Blatt „Einstellungen“ für VNET-Subnetze nicht geladen werden. Verwenden Sie zur Problemumgehung PowerShell und das Cmdlet [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?preserve-view=true&view=azurermps-5.5.0), um diese Informationen anzuzeigen und zu verwalten.

- Sowohl im Administrator- als auch im Benutzerportal wird das Blatt „Übersicht“ nicht geladen, wenn Sie auf das Übersichtsblatt für Speicherkonten klicken, die mit einer älteren API-Version (beispielsweise 2015-06-15) erstellt wurden. Das gilt auch für Systemspeicherkonten wie **updateadminaccount**, das im Rahmen von Patch- und Updatevorgängen verwendet wird. 

  Führen Sie zur Umgehung dieses Problems mithilfe von PowerShell das Skript **Start-ResourceSynchronization.ps1** aus, um den Zugriff auf die Speicherkontodetails wiederherzustellen. Das Skript steht auf [GitHub](https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) zur Verfügung und muss mit Dienstadministrator-Anmeldeinformationen auf dem privilegierten Endpunkt ausgeführt werden. 

- Das Blatt **Service Health** wird nicht geladen. Beim Öffnen des Blatts „Service Health“ im Verwaltungs- oder Benutzerportal zeigt Azure Stack eine Fehlermeldung an, und Informationen werden nicht geladen. Dieses Verhalten ist normal. Sie können Service Health zwar auswählen und öffnen, dieses Feature ist jedoch noch nicht verfügbar, sondern wird in einer zukünftigen Version von Azure Stack implementiert.


#### <a name="health-and-monitoring"></a>Integrität und Überwachung
<!-- 1264761 - IS ASDK -->  
-  Möglicherweise werden Warnungen für die *Health Controller*-Komponente mit folgenden Details angezeigt:  

  Warnung 1:
   - NAME: Infrastrukturrolle fehlerhaft
   - SCHWEREGRAD: Warnung
   - KOMPONENTE: Health Controller
   - BESCHREIBUNG: Heartbeat Scanner von Health Controller ist nicht verfügbar. Dies kann sich auf Integritätsberichte und Metriken auswirken.  

  Warnung 2:
   - NAME: Infrastrukturrolle fehlerhaft
   - SCHWEREGRAD: Warnung
   - KOMPONENTE: Health Controller
   - BESCHREIBUNG: Fault Scanner von Health Controller ist nicht verfügbar. Dies kann sich auf Integritätsberichte und Metriken auswirken.

  Beide Warnungen können ignoriert werden. Sie werden nach einem bestimmten Zeitraum automatisch geschlossen.  


#### <a name="marketplace"></a>Marketplace
- Benutzer können den gesamten Marketplace ohne Abonnement durchsuchen, und es werden administrative Elemente wie Pläne und Angebote angezeigt. Diese Elemente sind für Benutzer nicht funktional.

#### <a name="compute"></a>Compute
- Die Skalierungseinstellungen für Skalierungsgruppen für virtuelle Computer sind im Portal nicht verfügbar. Dieses Problem können Sie umgehen, indem Sie [Azure PowerShell](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) verwenden. Aufgrund der Versionsunterschiede bei PowerShell müssen Sie den `-Name`-Parameter statt des `-VMScaleSetName`-Parameters verwenden.

<!-- 2290877  --> 
- Es ist für Sie nicht möglich, eine VM-Skalierungsgruppe (VMSS) hochzuskalieren, die mit einer älteren Version als Azure Stack 1802 erstellt wurde. Der Grund ist eine Änderung bei der Unterstützung der Nutzung von Verfügbarkeitsgruppen mit VM-Skalierungsgruppen. Diese Unterstützung wurde mit Version 1802 hinzugefügt.  Wenn Sie zusätzliche Instanzen zur Skalierung einer VM-Skalierungsgruppe hinzufügen möchten, die vor dem Vorhandensein dieser Unterstützung erstellt wurde, tritt für den Vorgang ein Fehler mit der Meldung *Bereitstellungsstatus „Fehler“* auf. 

  Dieses Problem wurde in Version 1803 behoben. Installieren Sie zum Beheben dieses Problems für Version 1802 das Azure Stack-Hotfix **1.0.180302.4**. Weitere Informationen finden Sie unter [KB 4131152: Vorhandene Virtual Machine Scale Sets-Instanzen werden möglicherweise unbrauchbar](https://support.microsoft.com/help/4131152). 

- Azure Stack unterstützt nur die Verwendung von VHDs mit fester Größe. Einige über den Marketplace in Azure Stack angebotenen Images verwenden dynamische VHDs, die aber entfernt wurden. Größenänderungen an virtuellen Computern (VMs), bei denen ein dynamischer Datenträger angefügt ist, führen dazu, dass die VM in den Zustand „Fehlerhaft“ wechselt.

  Löschen Sie in diesem Fall den virtuellen Computer, ohne aber seinen Datenträger (ein VHD-Blob in einem Speicherkonto) zu löschen. Konvertieren Sie die VHD anschließend von einem dynamischen Datenträger in einen Datenträger mit fester Größe, und erstellen Sie den virtuellen Computer neu.

- Wenn Sie im Portal unter **Neu** > **Berechnen** > **Verfügbarkeitsgruppe** eine Verfügbarkeitsgruppe erstellen, können Sie nur eine Verfügbarkeitsgruppe mit einer Fehlerdomäne und einer Updatedomäne erstellen. Erstellen Sie bei der Erstellung eines neuen virtuellen Computers zur Problemumgehung die Verfügbarkeitsgruppe mithilfe von PowerShell, der CLI oder im Portal.

- Beim Erstellen von virtuellen Computern im Azure Stack-Benutzerportal zeigt das Portal eine falsche Anzahl von Datenträgern an, die an eine VM der Serie D angefügt werden können. Alle unterstützten virtuellen Computer der Serie D können so viele Datenträger wie bei der Azure-Konfiguration aufnehmen.

- Falls bei der Erstellung eines VM-Images ein Fehler auftritt, wird dem Blatt „Berechnung der VM-Images“ ggf. ein fehlerhaftes Element hinzugefügt, das nicht gelöscht werden kann.

  Erstellen Sie zur Problemumgehung ein neues VM-Image mit einer Dummy-VHD, die per Hyper-V erstellt werden kann (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Das Problem, mit dem das Löschen des fehlerhaften Elements verhindert wird, sollte durch diesen Vorgang behoben werden. Anschließend können Sie das Element 15 Minuten nach der Erstellung des Dummy-Images erfolgreich löschen.

  Danach können Sie versuchen, das VM-Image, bei dem zuvor ein Fehler aufgetreten ist, erneut herunterzuladen.

-  Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Benutzer eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.  

<!-- 1662991 --> 
- Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren.  




#### <a name="networking"></a>Netzwerk
- Nachdem Sie eine VM erstellt und einer öffentlichen IP-Adresse zugeordnet haben, können Sie die Zuordnung dieser VM zu dieser IP-Adresse nicht mehr aufheben. Die Aufhebung der Zuordnung war scheinbar erfolgreich, aber die zuvor zugewiesene öffentliche IP-Adresse bleibt der ursprünglichen VM zugeordnet.

  Derzeit müssen Sie ausschließlich neue öffentliche IP-Adressen für die Erstellung neuer VMs verwenden.

  Dieses Verhalten tritt auch dann auf, wenn Sie die IP-Adresse einem neuen virtuellen Computer neu zuordnen (gewöhnlich als *VIP-Austausch* bezeichnet). Alle künftigen Versuche, eine Verbindung über diese IP-Adresse herzustellen, führen dazu, dass eine Verbindung mit dem ursprünglich zugeordneten virtuellen Computer (und nicht mit dem neuen) hergestellt wird.

- Der interne Lastenausgleich (ILB) behandelt MAC-Adressen für Back-End-VMs nicht korrekt, wodurch der ILB bei der Verwendung von Linux-Instanzen im Back-End-Netzwerk abstürzt.  Der ILB funktioniert gut mit Windows-Instanzen im Back-End-Netzwerk.

-   Das Feature zur IP-Weiterleitung wird im Portal angezeigt, aber die Aktivierung der IP-Weiterleitung hat keine Auswirkungen. Dieses Feature wird noch nicht unterstützt.

- Azure Stack unterstützt ein einzelnes *Gateway eines lokalen Netzwerks* pro IP-Adresse. Dies gilt für alle Mandantenabonnements. Nach der Herstellung der Verbindung mit dem ersten Gateway eines lokalen Netzwerks werden nachfolgende Versuche zum Erstellen einer Ressource für Gateways lokaler Netzwerke mit der gleichen IP-Adresse blockiert.

- In einem virtuellen Netzwerk, das mit der DNS-Servereinstellung *Automatisch* erstellt wurde, tritt bei der Änderung eines benutzerdefinierten DNS-Servers ein Fehler auf. Die aktualisierten Einstellungen werden nicht per Pushvorgang auf VMs in diesem VNET übertragen.

- Azure Stack unterstützt nicht das Hinzufügen zusätzlicher Netzwerkschnittstellen zu einer VM-Instanz, nachdem die VM bereitgestellt wurde. Wenn die VM mehr als eine Netzwerkschnittstelle benötigt, müssen diese zum Zeitpunkt der Bereitstellung definiert werden.

<!-- 2096388 --> 
- Sie können das Verwaltungsportal nicht verwenden, um Regeln für eine Netzwerksicherheitsgruppe zu aktualisieren. 

    Problemumgehung für App Service: Wenn Sie eine Remotedesktopverbindung mit den Controllerinstanzen herstellen müssen, ändern Sie mit PowerShell die Sicherheitsregeln innerhalb der Netzwerksicherheitsgruppen.  Im Folgenden werden Beispiele zum *Zulassen* und anschließenden Wiederherstellen der Konfiguration mit *Verweigern* vorgestellt:  
    
    - *Zulassen*:
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn�t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Verweigern*:

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn�t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL und MySQL
 - Bevor Sie fortfahren, überprüfen Sie den wichtigen Hinweis unter [Bevor Sie beginnen](#before-you-begin) bei den Anmerkungen zu dieser Version.
- Es kann bis zu einer Stunde dauern, bis Benutzer Datenbanken in einer neuen SQL- oder MySQL-Bereitstellung erstellen können.

- Auf Servern, die SQL oder MySQL hosten, kann nur der Ressourcenanbieter Elemente erstellen. Die auf einem Hostserver erstellten Elemente, die nicht vom Ressourcenanbieter erstellt wurden, könnten zu einem Zustand ohne Entsprechung führen.  

<!-- IS, ASDK --> 
- Sonderzeichen, einschließlich Leerzeichen und Interpunktion, werden im Namen für die **Familie** nicht unterstützt, wenn Sie die SKU für die SQL- und MySQL-Ressourcenanbieter erstellen.

> [!NOTE]  
> Nachdem Sie ein Update auf Azure Stack 1802 ausgeführt haben, können Sie die zuvor bereitgestellten Ressourcenanbieter SQL und MySQL weiterhin verwenden.  Es wird empfohlen, SQL und MySQL zu aktualisieren, sobald ein neues Release verfügbar ist. Wenden Sie Updates wie bei Azure Stack sequenziell auf die Ressourcenanbieter SQL und MySQL an.  Wenn Sie z.B. Version 1710 verwenden, wenden Sie zuerst Version 1711, dann 1712 und schließlich 1802 an.      
>   
> Die Installation des Updates 1802 wirkt sich nicht auf die aktuelle Verwendung der von Ihren Benutzern verwendeten Ressourcenanbieter SQL oder MySQL aus.
> Unabhängig von der Version der Ressourcenanbieter, die Sie verwenden, bleiben Ihre Benutzerdaten in deren Datenbanken davon unberührt und stehen weiterhin für den Zugriff zur Verfügung.    


#### <a name="app-service"></a>App Service
- Benutzer müssen den Speicherressourcenanbieter vor dem Erstellen seiner ersten Azure-Funktion im Abonnement registrieren.

- Um die Infrastruktur (Worker-, Verwaltungs-, Front-End-Rollen) horizontal hochzuskalieren, müssen Sie PowerShell verwenden, wie in den Anmerkungen zu dieser Version für die Berechnung beschrieben.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Herunterladen von Azure Stack-Tools von GitHub
- Beim Herunterladen der Azure Stack-Tools über GitHub mit dem PowerShell-Cmdlet *invoke-webrequest* ist ein Fehler aufgetreten:     
    -  *invoke-webrequest: Die Anforderung wurde abgebrochen: Es konnte kein sicherer SSL/TLS-Kanal erstellt werden.*     

  Dieser Fehler tritt aufgrund der zurzeit veralteten GitHub-Unterstützung für die kryptografischen Standards TLS v1 und TLS v1.1 auf (der Standardwert für PowerShell). Weitere Informationen finden Sie unter [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Hinweis zur Abschaffung unsicherer kryptografischer Standards).

  Fügen Sie zur Behebung dieses Problems `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` am Anfang des Skripts hinzu, um bei Downloads aus GitHub-Repositorys die Verwendung von TLS v1.2 in der PowerShell-Konsole zu erzwingen.


## <a name="download-the-update"></a>Herunterladen des Updates
Sie können das Paket für das Azure Stack-Update 1802 [hier](https://aka.ms/azurestackupdatedownload) herunterladen.


## <a name="more-information"></a>Weitere Informationen
Microsoft stellt mithilfe des privilegierten Endpunkts (Privileged End Point, PEP), der mit Update 1710 installiert wird, eine Möglichkeit zum Überwachen und Fortsetzen von Updates bereit.

- Weitere Informationen finden Sie in der Dokumentation unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Siehe auch

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).