---
title: Azure Stack 1803-Update | Microsoft-Dokumentation
description: Erfahren Sie, welche integrierten Azure Stack-Systeme Update 1803 enthält, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6f15453ce20988929728a9d326ab5f7bf901beca
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248702"
---
# <a name="azure-stack-1803-update"></a>Azure Stack-Update 1803

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird beschrieben, welche Verbesserungen und Fehlerbehebungen in diesem Updatepaket 1803 enthalten sind, welche bekannten Probleme es für dieses Release gibt und wo das Update heruntergeladen werden kann. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]        
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz    
Die Buildnummer des Azure Stack-Updates 1803 ist **20180329.1**.


## <a name="before-you-begin"></a>Vorbereitung    
> [!IMPORTANT]    
> Versuchen Sie nicht, während der Installation dieses Updates virtuelle Computer zu erstellen. Weitere Informationen zum Verwalten von Updates finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).


### <a name="prerequisites"></a>Voraussetzungen
- Installieren Sie das Azure Stack 1802-Update, bevor Sie das Azure Stack 1803-Update anwenden.   

- Installieren Sie **AzS Hotfix 1.0.180312.1 – Build 20180222.2**, bevor Sie das Azure Stack 1803-Update anwenden. Durch diesen Hotfix wird Windows Defender aktualisiert. Dieser ist beim Herunterladen von Updates für Azure Stack verfügbar.

  Um den Hotfix zu installieren, führen Sie die üblichen Verfahren zum [Installieren von Updates für Azure Stack](../azure-stack-apply-updates.md) durch. Der Name des Updates wird als **AzS Hotfix 1.0.180312.1** angezeigt und enthält die folgenden Dateien: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Nachdem Sie diese Dateien in ein Speicherkonto und in einen Container hochgeladen haben, führen Sie die Installation im Verwaltungsportal über die Kachel „Aktualisieren“ aus. 
  
  Im Gegensatz zu Updates für Azure Stack wird die Version von Azure Stack nicht durch die Installation dieses Updates geändert. Um zu überprüfen, ob dieses Update installiert wurde, sehen Sie sich die Liste **Installierte Updates** an.



### <a name="new-features"></a>Neue Funktionen 
Dieses Update enthält die folgenden Verbesserungen und Fehlerbehebungen für Azure Stack.

- **Aktualisieren von Azure Stack-Geheimnissen** – (Konten und Zertifikate). Weitere Informationen zum Verwalten von Geheimnissen finden Sie unter [Rotieren von Geheimnissen in Azure Stack](../azure-stack-rotate-secrets.md). 

<!-- 1914853 --> 
- **Automatische Umleitung an HTTPS** beim Zugriff auf die Administrator- und Benutzerportale über HTTP. Diese Verbesserung wurde basierend auf dem [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec)-Feedback für Azure Stack vorgenommen. 

<!-- 2202621  --> 
- **Zugriff auf den Marketplace**: Sie können nun den Azure Stack Marketplace öffnen, indem Sie die Option [+ Neu](https://ms.portal.azure.com/#create/hub) in den Administrator- und Benutzerportalen genauso wie in den Azure-Portalen verwenden.
 
<!-- 2202621 --> 
- **Azure Monitor** – Azure Stack fügt [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) zu den Administrator- und Benutzerportalen hinzu. Dazu gehören neue Explorer für Metriken und Aktivitätsprotokolle. Um von externen Netzwerken auf Azure Monitor zugreifen zu können, muss der Port **13012** in Firewallkonfigurationen geöffnet sein. Weitere Informationen zu Ports, die von Azure Stack benötigt werden, finden Sie unter [Azure Stack-Rechenzentrumsintegration – Veröffentlichen von Endpunkten](../azure-stack-integrate-endpoints.md).

   Im Rahmen dieser Änderung wird die Option *Überwachungsprotokolle* unter **Weitere Dienste** zudem jetzt als *Aktivitätsprotokolle* angezeigt. Die Funktionalität entspricht jetzt dem Azure-Portal. 

<!-- 1664791 --> 
- **Sparsedateien** – Wenn Sie ein neues Image zu Azure Stack hinzufügen oder ein Image über die Marketplace-Syndikation hinzufügen, wird das Image in eine Sparsedatei konvertiert. Images, die vor der Verwendung von Azure Stack Version 1803 hinzugefügt wurden, können nicht konvertiert werden. Stattdessen müssen Sie diese Images über die Marketplace-Syndikation erneut übermitteln, um dieses Feature nutzen zu können. 
 
   Sparsedateien stellen ein effizientes Dateiformat dar, das zur Verringerung der Speicherplatznutzung und Verbesserung von E/A-Vorgängen verwendet wird. Weitere Informationen zu Windows Server finden Sie unter [Fsutil-Sparsedatei](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse). 

### <a name="fixed-issues"></a>Behobene Probleme

<!-- 1739988 --> 
-  Der interne Lastenausgleich (Internal Load Balancing, ILB) verarbeitet nun ordnungsgemäß MAC-Adressen für Back-End-VMs, wodurch der ILB bei der Verwendung von Linux-Instanzen im Back-End-Netzwerk Pakete für das Back-End-Netzwerk verwirft. Der ILB funktioniert gut mit Windows-Instanzen im Back-End-Netzwerk. 

<!-- 1805496 --> 
-  Das Problem, bei dem VPN-Verbindungen mit Azure Stack getrennt wurden, weil in Azure Stack andere Einstellungen für die IKE-Richtlinie zum Einsatz kamen als bei Azure, wurde behoben. Die Werte für SALifetime (Zeit) und SALiftetime (Bytes) waren nicht mit Azure kompatibel und wurden in 1803 an die Azure-Einstellungen angepasst. Der Wert für SALifetime (Sekunden) vor 1803 betrug 14.400 und wurde nun in Version 1803 in 27.000 geändert. Der Wert für SALifetime (Bytes) vor 1803 betrug 819.200 und wurde nun in Version 1803 in 33.553.408 geändert.

<!-- 2209262 --> 
-  Das IP-Problem, bei dem VPN-Verbindungen vorher im Portal sichtbar waren, das Aktivieren oder Umschalten der IP-Weiterleitung jedoch keine Auswirkungen hatte, wurde behoben. Das Feature ist standardmäßig aktiviert, und es besteht noch keine Möglichkeit, dies zu ändern.  Das Steuerelement wurde aus dem Portal entfernt. 

<!-- 1766332 --> 
-  Azure Stack unterstützt keine richtlinienbasierte VPN-Gateways, obwohl die Option im Portal angezeigt wird.  Die Option wurde aus dem Portal entfernt. 

<!-- 1868283 --> 
-  Azure Stack verhindert nun die Größenänderung eines virtuellen Computers, der mit dynamischen Datenträgern erstellt werden. 

<!-- 1756324 --> 
-  Verwendungsdaten für virtuelle Computer werden jetzt stündlich getrennt. Dies steht im Einklang mit Azure. 

<!--  2253274 --> 
-  Das Problem, bei dem in den Administrator- und Benutzerportalen das Blatt „Einstellungen“ für VNET-Subnetze nicht geladen werden konnte, wurde behoben. Verwenden Sie zur Problemumgehung PowerShell und das Cmdlet [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true), um diese Informationen anzuzeigen und zu verwalten.

- Bei der Erstellung eines virtuellen Computers wird die Meldung *Preise können nicht angezeigt werden* nicht mehr angezeigt, wenn Sie eine VM-Größe auswählen.

- Es wurden verschiedene Fehlerbehebungen hinsichtlich der Leistung, Stabilität, Sicherheit und des von Azure Stack eingesetzten Betriebssystems vorgenommen.


### <a name="changes"></a>Änderungen
- Die Methode der Änderung des Zustands eines neu erstellten Angebots von *Privat* in *Öffentlich* oder *Außer Betrieb* hat sich geändert. Weitere Informationen finden Sie unter [Erstellen von Angeboten in Azure Stack](../azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess    
<!-- 2328416 -->  Bei der Installation des Updates auf 1803 kann es zu Ausfallzeiten beim Blobdienst sowie den internen Diensten kommen, die den Blobdienst einsetzen. Betroffen sind auch einige Vorgänge in virtuellen Computern. Durch diese Ausfallzeit können Fehler bei Mandantenvorgängen oder Warnungen von Diensten verursacht werden, die nicht auf Daten zugreifen können. Dieses Problem löst sich nach der Installation des Updates von selbst. 



### <a name="post-update-steps"></a>Schritte nach dem Update
- Installieren Sie nach der Installation von 1803 alle entsprechenden Hotfixes. Weitere Informationen finden Sie in den folgenden Knowledge Base-Artikeln sowie in unserer [Wartungsrichtlinie](../azure-stack-servicing-policy.md).

  - [KB 4344115 – Azure Stack-Hotfix 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Überprüfen Sie nach der Installation dieses Updates die Firewallkonfiguration, um sicherzustellen, dass die [erforderlichen Ports](../azure-stack-integrate-endpoints.md) offen sind. Mit diesem Update beispielsweise wird der Dienst *Azure Monitor* eingeführt, der einen Wechsel von Überwachungsprotokollen zu Aktivitätsprotokollen beinhaltet. Durch diese Änderung wird jetzt Port 13012 verwendet, der ebenfalls offen sein muss.  


### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)
Im Folgenden werden bekannte Probleme nach der Installation zum Build **20180323.2** vorgestellt.

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

- Im Verwaltungsportal wird unter Umständen eine kritische Warnung für die Komponente *Microsoft.Update.Admin* angezeigt. Name, Beschreibung und Behebung der Warnung werden wie folgt angezeigt:  
    - *FEHLER: Vorlage für FaultType ResourceProviderTimeout fehlt.*

  Diese Warnung kann ignoriert werden. 


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
- Die Skalierungseinstellungen für Skalierungsgruppen für virtuelle Computer sind im Portal nicht verfügbar. Dieses Problem können Sie umgehen, indem Sie [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) verwenden. Aufgrund der Versionsunterschiede bei PowerShell müssen Sie den `-Name`-Parameter statt des `-VMScaleSetName`-Parameters verwenden.

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



- Azure Stack unterstützt ein einzelnes *Gateway eines lokalen Netzwerks* pro IP-Adresse. Dies gilt für alle Mandantenabonnements. Nach der Herstellung der Verbindung mit dem ersten Gateway eines lokalen Netzwerks werden nachfolgende Versuche zum Erstellen einer Ressource für Gateways lokaler Netzwerke mit der gleichen IP-Adresse blockiert.

- In einem virtuellen Netzwerk, das mit der DNS-Servereinstellung *Automatisch* erstellt wurde, tritt bei der Änderung eines benutzerdefinierten DNS-Servers ein Fehler auf. Die aktualisierten Einstellungen werden nicht per Pushvorgang auf VMs in diesem VNET übertragen.

- Azure Stack unterstützt nicht das Hinzufügen zusätzlicher Netzwerkschnittstellen zu einer VM-Instanz, nachdem die VM bereitgestellt wurde. Wenn die VM mehr als eine Netzwerkschnittstelle benötigt, müssen diese zum Zeitpunkt der Bereitstellung definiert werden.

<!-- 2096388 --> 
- Sie können das Verwaltungsportal nicht verwenden, um Regeln für eine Netzwerksicherheitsgruppe zu aktualisieren. 

    Problemumgehung für App Service: Wenn Sie eine Remotedesktopverbindung mit den Controllerinstanzen herstellen müssen, ändern Sie mit PowerShell die Sicherheitsregeln innerhalb der Netzwerksicherheitsgruppen.  Im Folgenden werden Beispiele zum *Zulassen* und anschließenden Wiederherstellen der Konfiguration mit *Verweigern* vorgestellt:  
    
    - *Zulassen*:
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
> Nachdem Sie ein Update auf Azure Stack 1803 ausgeführt haben, können Sie die zuvor bereitgestellten Ressourcenanbieter SQL und MySQL weiterhin verwenden.  Es wird empfohlen, SQL und MySQL zu aktualisieren, sobald ein neues Release verfügbar ist. Wenden Sie Updates wie bei Azure Stack sequenziell auf die Ressourcenanbieter SQL und MySQL an.  Wenn Sie z.B. Version 1711 verwenden, wenden Sie zuerst Version 1712, dann 1802 und schließlich 1803 an.      
>   
> Die Installation des Updates 1803 wirkt sich nicht auf die aktuelle Verwendung der von Ihren Benutzern verwendeten Ressourcenanbieter SQL oder MySQL aus.
> Unabhängig von der Version der Ressourcenanbieter, die Sie verwenden, bleiben Ihre Benutzerdaten in deren Datenbanken davon unberührt und stehen weiterhin für den Zugriff zur Verfügung.    



#### <a name="app-service"></a>App Service
- Benutzer müssen den Speicherressourcenanbieter vor dem Erstellen seiner ersten Azure-Funktion im Abonnement registrieren.

- Um die Infrastruktur (Worker-, Verwaltungs-, Front-End-Rollen) horizontal hochzuskalieren, müssen Sie PowerShell verwenden, wie in den Anmerkungen zu dieser Version für die Berechnung beschrieben.


#### <a name="usage"></a>Verwendung  
-  Die Ansicht „Verwendung“ für die Nutzungsdaten öffentlicher IP-Adressen zeigt den gleichen *EventDateTime*-Wert für jeden Datensatz anstatt des *TimeDate*-Stempels, der anzeigt, wann dieser jeweils erstellt wurde. Derzeit können Sie diese Daten nicht nutzen, um eine genaue Buchhaltung über die Nutzung öffentlicher IP-Adressen durchzuführen.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Herunterladen von Azure Stack-Tools von GitHub
- Beim Herunterladen der Azure Stack-Tools über GitHub mit dem PowerShell-Cmdlet *invoke-webrequest* ist ein Fehler aufgetreten:     
    -  *invoke-webrequest: Die Anforderung wurde abgebrochen: Es konnte kein sicherer SSL/TLS-Kanal erstellt werden.*     

  Dieser Fehler tritt aufgrund der zurzeit veralteten GitHub-Unterstützung für die kryptografischen Standards TLS v1 und TLS v1.1 auf (der Standardwert für PowerShell). Weitere Informationen finden Sie unter [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Hinweis zur Abschaffung unsicherer kryptografischer Standards).

  Fügen Sie zur Behebung dieses Problems `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` am Anfang des Skripts hinzu, um bei Downloads aus GitHub-Repositorys die Verwendung von TLS v1.2 in der PowerShell-Konsole zu erzwingen.


## <a name="download-the-update"></a>Herunterladen des Updates
Sie können das Paket für das Azure Stack-Update 1803 [hier](https://aka.ms/azurestackupdatedownload) herunterladen.


## <a name="see-also"></a>Siehe auch
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](../azure-stack-monitor-update.md).
- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).