---
title: Azure Stack 1804-Update | Microsoft-Dokumentation
description: Erfahren Sie, welche integrierten Azure Stack-Systeme Update 1804 enthält, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 5d871458a7af4e9233e750b07404bc151274033a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248698"
---
# <a name="azure-stack-1804-update"></a>Azure Stack-Update 1804

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird beschrieben, welche Verbesserungen und Fehlerbehebungen in diesem Updatepaket 1804 enthalten sind, welche bekannten Probleme es für dieses Release gibt und wo das Update heruntergeladen werden kann. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]        
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz    
Die Buildnummer des Azure Stack-Updates 1804 ist **20180513.1**.   

### <a name="new-features"></a>Neue Funktionen
Dieses Update enthält die folgenden Verbesserungen für Azure Stack.

<!-- 15028744 - IS -->  
- **Visual Studio-Unterstützung für getrennte Azure Stack-Bereitstellungen mithilfe von AD FS**. In Visual Studio können Sie ab sofort Abonnements hinzufügen und sich mithilfe von AD FS-Anmeldeinformationen für Verbundbenutzer authentifizieren. 
 
<!-- 1779474, 1779458 - IS --> 
- **Möglichkeit zum Einsatz virtueller Computer der Av2- und F-Serie**. In Azure Stack können ab sofort virtuelle Computer basierend auf den Größen virtueller Computer der Av2- und F-Serie eingesetzt werden. Weitere Informationen finden Sie unter [In Azure Stack unterstützte VM-Größen](../../user/azure-stack-vm-sizes.md). 

<!-- 1759172 - IS, ASDK --> 
- **Neue Verwaltungsabonnements**. Mit 1804 gibt es im Portal zwei neue Abonnementtypen. Diese neuen Abonnementtypen sind zusätzlich zum Standardabonnement des Anbieters verfügbar und werden in neuen Azure Stack-Installationen ab Version 1804 angezeigt. *Verwenden Sie diese neuen Abonnementtypen nicht mit dieser Azure Stack-Version*. Die Verfügbarkeit dieser Abonnementtypen wird in einem zukünftigen Update bekanntgegeben. 

  Wenn Sie Azure Stack auf Version 1804 aktualisieren, sind die zwei neuen Abonnementtypen nicht sichtbar. Neue Bereitstellungen von integrierten Azure Stack-Systemen und -Installationen des Azure Stack Development Kit ab Version 1804 haben jedoch Zugriff auf alle drei Abonnementtypen.  

  Diese neuen Abonnementtypen sind Bestandteil einer größeren Änderung, um das Abonnement für Standardanbieter zu sichern und die Bereitstellung von freigegebenen Ressourcen wie SQL-Hostserver zu vereinfachen. Da Azure Stack in zukünftigen Updates weitere Bestandteile dieser größeren Änderung enthalten wird, gehen möglicherweise die unter diesen neuen Abonnementtypen bereitgestellten Ressourcen verloren. 

  Derzeit sind drei Abonnementtypen sichtbar:  
  - Abonnement für Standardanbieter: Diesen Abonnementtyp können Sie weiterhin verwenden. 
  - Abonnement für Messungen: *Verwenden Sie diesen Abonnementtyp nicht.*
  - Abonnement für die Nutzung: *Verwenden Sie diesen Abonnementtyp nicht.*

  



## <a name="fixed-issues"></a>Behobene Probleme

<!-- IS, ASDK -->  
-  Im Verwaltungsportal müssen Sie die Kachel „Aktualisieren“ nicht mehr aktualisieren, bevor diese Informationen anzeigt.
 
<!-- 2050709  -->  
-  Sie können nun über das Verwaltungsportal Speichermetriken für Blob-Dienst, Tabellenspeicherdienst und Warteschlangendienst bearbeiten.
 
<!-- IS, ASDK --> 
-  Wenn Sie unter **Netzwerk** auf **Verbindung** klicken, um eine VPN-Verbindung einzurichten, ist **Site-to-site (IPsec)** nun die einzige verfügbare Option.

- Es wurden **verschiedene Fehlerbehebungen** hinsichtlich der Leistung, Stabilität, Sicherheit und des von Azure Stack eingesetzten Betriebssystems vorgenommen.

## <a name="additional-releases-timed-with-this-update"></a>Zusätzliche, zeitlich auf dieses Update abgestimmte Releases  
Die folgenden Updates sind jetzt verfügbar, benötigen jedoch nicht Azure Stack Update 1804.
- **Update für das Microsoft Azure Stack System Center Operations Manager-Überwachungspaket**. Eine neue Version (1.0.3.0) des Microsoft System Center Operations Manager-Überwachungspakets für Azure Stack steht zum [Herunterladen](https://www.microsoft.com/download/details.aspx?id=55184) zur Verfügung. Mit dieser Version können Sie Dienstprinzipale verwenden, wenn Sie eine verbundene Azure Stack-Bereitstellung hinzufügen. Diese Version bietet auch eine Updateverwaltungsoberfläche, mit der Sie die Wartungsaktion direkt in Operations Manager ausführen können. Es gibt auch neue Dashboards, die Ressourcenanbieter, Skalierungseinheiten und Skalierungseinheitenknoten anzeigen.

- **Neue Azure Stack Admin PowerShell Version 1.3.0**.  Azure Stack PowerShell 1.3.0 ist jetzt für die Installation verfügbar. Diese Version stellt Befehle für alle Administratorressourcenanbieter zum Verwalten von Azure Stack zur Verfügung.  Bei diesem Release werden manche Inhalte aus dem GitHub-[Repository](https://github.com/Azure/AzureStack-Tools) für Azure Stack-Tools eingestellt. 

   Um ausführliche Informationen zur Installation zu erhalten, beachten Sie die [Anweisungen](../azure-stack-powershell-install.md) oder den [Hilfeinhalt](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) für das Azure Stack-Modul 1.3.0. 

- **Erstes Release der Azure Stack-REST-API-Referenz**. Die [API-Referenz für alle Azure Stack-Administratorressourcenanbieter](https://docs.microsoft.com/rest/api/azure-stack/) ist jetzt veröffentlicht. 


## <a name="before-you-begin"></a>Voraussetzungen    

### <a name="prerequisites"></a>Voraussetzungen
- Installieren Sie das Azure Stack 1803-Update, bevor Sie das Azure Stack 1804-Update anwenden.  
  
- Installieren Sie das neueste verfügbare Update oder Hotfix für Version 1803. 


### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess   
- Während der Installation des 1804-Updates werden unter Umständen Warnungen mit der folgenden Meldung angezeigt: *Fehler: Die Vorlage für FaultType UserAccounts.New fehlt.*  Sie können diese Warnungen ignorieren. Diese Warnungen werden automatisch geschlossen, nachdem das Update 1804 durchgeführt wurde.   
 
<!-- TBD - IS --> 
- Versuchen Sie nicht, während der Installation dieses Updates virtuelle Computer zu erstellen. Weitere Informationen zum Verwalten von Updates finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Schritte nach dem Update
Installieren Sie nach der Installation von 1804 alle entsprechenden Hotfixes. Weitere Informationen finden Sie in den folgenden Knowledge Base-Artikeln sowie in unserer [Wartungsrichtlinie](../azure-stack-servicing-policy.md).  
 - [KB 4344114 – Azure Stack-Hotfix 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)
Im Folgenden werden bekannte Probleme nach der Installation zum Build **20180513.1** vorgestellt.

#### <a name="portal"></a>Portal

- Die technische Dokumentation für Azure Stack konzentriert sich auf das neueste Release. Aufgrund von Portaländerungen zwischen den Releases kann das, was bei der Verwendung der Azure Stack-Portale angezeigt wird, von dem abweichen, was in der Dokumentation angezeigt wird. 

<!-- TBD - IS ASDK --> 
- Sie können mit dieser Version von Azure Stack keine Treiberupdates mit einem OEM-Erweiterungspaket durchführen.  Dafür gibt es keine Problemumgehung.

<!-- 1272111 - IS --> 
-  Nach der Installation dieser Azure Stack-Version oder dem Update auf diese können möglicherweise keine Azure Stack-Skalierungseinheiten im Administratorportal angezeigt werden.  
  Problemumgehung: Verwenden Sie PowerShell zum Anzeigen von Informationen zu Skalierungseinheiten. Weitere Informationen finden Sie in der [Hilfe](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) zum Azure Stack-Modul 1.3.0. 

<!-- 2332636 - IS -->  
-   Wenn Sie AD FS für Ihr Azure Stack-Identitätssystem verwenden und auf diese Version von Azure Stack aktualisieren, wird der Standardbesitzer des Abonnements für Standardanbieter auf den integrierten **CloudAdmin**-Benutzer zurückgesetzt.  
  Problemumgehung: Um dieses Problem nach der Installation dieses Updates zu beheben, führen Sie Schritt 3 unter [Auslösen der Automatisierung zum Konfigurieren der Anspruchsanbieter-Vertrauensstellung in Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) aus, um den Besitzer des Abonnements für Standardanbieter zurückzusetzen.   

<!-- TBD - IS ASDK --> 
- Einige administrative Abonnementtypen sind nicht verfügbar.  Wenn Sie Azure Stack auf diese Version aktualisieren, werden die zwei Abonnementtypen, die [mit Version 1804 eingeführt wurden](#new-features), nicht in der Konsole angezeigt. Dies entspricht dem erwarteten Verhalten. Die nicht verfügbaren Abonnementtypen sind *Messungsabonnement* und *Verbrauchsabonnement*. Diese Abonnementtypen sind in neuen Azure Stack-Umgebungen ab Version 1804 sichtbar, aber noch nicht zur Verwendung bereit. Sie sollten den Abonnementtyp *Standardanbieter* weiterhin verwenden.  


<!-- TBD -  IS ASDK -->
- Die Funktion, mit der innerhalb des Administratorportals [eine neue Supportanfrage über die Dropdownliste geöffnet wird](../azure-stack-manage-portals.md#quick-access-to-help-and-support), ist nicht verfügbar. Verwenden Sie stattdessen den folgenden Link:     
    - Für integrierte Azure Stack-Systeme muss https://aka.ms/newsupportrequest verwendet werden.

<!-- 2403291 - IS ASDK --> 
- Möglicherweise haben Sie die horizontale Scrollleiste am unteren Rand des Verwaltungs- und Benutzerportals nicht verwendet. Wenn Sie nicht auf die horizontale Bildlaufleiste zugreifen können, verwenden Sie die Breadcrumb-Leiste, um zu einem vorherigen Blatt im Portal zu navigieren. Wählen Sie hierzu den Namen des Blattes, das Sie anzeigen möchten, aus der Breadcrumb-Liste oben links im Portal aus.

<!-- TBD - IS --> 
- Es ist es u.U. nicht möglich, Compute- oder Speicherressourcen im Administratorportal anzuzeigen. Grund für dieses Problem ist ein Fehler bei der Installation des Updates, der dazu führt, dass das Update fälschlicherweise als erfolgreich gemeldet wird. Wenn dieses Problem auftritt, wenden Sie sich an Microsoft Customer Support Services, um Unterstützung zu erhalten.

<!-- TBD - IS --> 
- Es kann sein, dass im Portal ein leeres Dashboard angezeigt wird. Wählen Sie zum Wiederherstellen des Dashboards oben rechts im Portal das Zahnradsymbol und dann die Option **Standardeinstellungen wiederherstellen**.

<!-- TBD - IS ASDK --> 
- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

<!-- TBD - IS ASDK --> 
- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung PowerShell, um Berechtigungen zu überprüfen.

<!-- TBD - IS ASDK --> 
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
 

#### <a name="compute"></a>Compute
<!-- TBD - IS --> 
- Bei der Auswahl der Größe des virtuellen Computers für die Bereitstellung eines virtuellen Computers sind einige VM-Größen der Serie F beim Erstellen einer VM nicht als Teil der Größenauswahl sichtbar. Folgende VM-Größen werden im Selektor nicht angezeigt: *F8s_v2*, *F16s_v2*, *F32s_v2* und *F64s_v2*.  
  Um das Problem zu umgehen, verwenden Sie eine der folgenden Methoden zum Bereitstellen einer VM. Bei jeder Methode müssen Sie die gewünschte VM-Größe angeben.

  - **Azure Resource Manager-Vorlage**: Wenn Sie eine Vorlage verwenden, legen Sie *vmSize* in der Vorlage entsprechend der gewünschten VM-Größe fest. Der folgende Inhalt z.B. wird zum Bereitstellen einer VM mit der Größe *F32s_v2* verwendet:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI**: Sie können den Befehl [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) verwenden und die VM-Größe als Parameter angeben, ähnlich wie `--size "Standard_F32s_v2"`.

  - **PowerShell**: Mit PowerShell können Sie [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) mit dem Parameter verwenden, der die VM-Größe angibt, ähnlich wie `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Die Skalierungseinstellungen für Skalierungsgruppen für virtuelle Computer sind im Portal nicht verfügbar. Dieses Problem können Sie umgehen, indem Sie [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) verwenden. Aufgrund der Versionsunterschiede bei PowerShell müssen Sie den `-Name`-Parameter statt des `-VMScaleSetName`-Parameters verwenden.

<!-- TBD - IS --> 
- Wenn Sie im Portal unter **Neu** > **Berechnen** > **Verfügbarkeitsgruppe** eine Verfügbarkeitsgruppe erstellen, können Sie nur eine Verfügbarkeitsgruppe mit einer Fehlerdomäne und einer Updatedomäne erstellen. Erstellen Sie bei der Erstellung eines neuen virtuellen Computers zur Problemumgehung die Verfügbarkeitsgruppe mithilfe von PowerShell, der CLI oder im Portal.

<!-- TBD - IS ASDK --> 
- Beim Erstellen von virtuellen Computern im Azure Stack-Benutzerportal zeigt das Portal eine falsche Anzahl von Datenträgern an, die an eine VM der Serie D angefügt werden können. Alle unterstützten virtuellen Computer der Serie D können so viele Datenträger wie bei der Azure-Konfiguration aufnehmen.

<!-- TBD - IS ASDK --> 
- Falls bei der Erstellung eines VM-Images ein Fehler auftritt, wird dem Blatt „Berechnung der VM-Images“ ggf. ein fehlerhaftes Element hinzugefügt, das nicht gelöscht werden kann.

  Erstellen Sie zur Problemumgehung ein neues VM-Image mit einer Dummy-VHD, die per Hyper-V erstellt werden kann (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Das Problem, mit dem das Löschen des fehlerhaften Elements verhindert wird, sollte durch diesen Vorgang behoben werden. Anschließend können Sie das Element 15 Minuten nach der Erstellung des Dummy-Images erfolgreich löschen.

  Danach können Sie versuchen, das VM-Image, bei dem zuvor ein Fehler aufgetreten ist, erneut herunterzuladen.

<!-- TBD - IS ASDK --> 
- Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Benutzer eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.  

<!-- 1662991 IS ASDK --> 
- Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren.  


#### <a name="networking"></a>Netzwerk
<!-- 1766332 - IS ASDK --> 
-  Wenn Sie unter **Netzwerk** auf **VPN-Gateway erstellen** klicken, um eine VPN-Verbindung einzurichten, wird als VPN-Typ **Richtlinienbasiert** aufgeführt. Wählen Sie diese Option nicht aus. Nur die Option **Routenbasiert** wird in Azure Stack unterstützt.

<!-- 2388980 - IS ASDK --> 
- Nachdem Sie eine VM erstellt und einer öffentlichen IP-Adresse zugeordnet haben, können Sie die Zuordnung dieser VM zu dieser IP-Adresse nicht mehr aufheben. Die Aufhebung der Zuordnung war scheinbar erfolgreich, aber die zuvor zugewiesene öffentliche IP-Adresse bleibt der ursprünglichen VM zugeordnet.

  Derzeit müssen Sie ausschließlich neue öffentliche IP-Adressen für die Erstellung neuer VMs verwenden.

  Dieses Verhalten tritt auch dann auf, wenn Sie die IP-Adresse einem neuen virtuellen Computer neu zuordnen (gewöhnlich als *VIP-Austausch* bezeichnet). Alle künftigen Versuche, eine Verbindung über diese IP-Adresse herzustellen, führen dazu, dass eine Verbindung mit dem ursprünglich zugeordneten virtuellen Computer (und nicht mit dem neuen) hergestellt wird.

<!-- 2292271 - IS ASDK --> 
- Wenn Sie eine Kontingentgrenze für eine Netzwerkressource heraufsetzen, die Teil eines Angebots und eines Plans ist, das bzw. der mit einem Mandantenabonnement verbunden ist, wird die neue Grenze nicht auf dieses Abonnement angewendet. Der neue Grenzwert gilt jedoch für neue Abonnements, die nach Heraufsetzen der Kontingentgrenze erstellt werden. 

  Um dieses Problem zu umgehen, verwenden Sie einen Skalierungsplan, um ein Netzwerkkontingent zu erhöhen, wenn der Plan bereits einem Abonnement zugeordnet ist. Weitere Informationen finden Sie in der Beschreibung der Vorgehensweise zum [Bereitstellen eines Skalierungsplans](../azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Ein Abonnement, dem DNS-Zonen- oder Routingtabellenressourcen zugeordnet wurden, kann nicht gelöscht werden. Um das Abonnement erfolgreich zu löschen, müssen Sie zuerst die DNS-Zonen- und Routingtabellenressourcen aus dem Mandantenabonnement löschen. 
  

<!-- 1902460 - IS ASDK --> 
- Azure Stack unterstützt ein einzelnes *Gateway eines lokalen Netzwerks* pro IP-Adresse. Dies gilt für alle Mandantenabonnements. Nach der Herstellung der Verbindung mit dem ersten Gateway eines lokalen Netzwerks werden nachfolgende Versuche zum Erstellen einer Ressource für Gateways lokaler Netzwerke mit der gleichen IP-Adresse blockiert.

<!-- 16309153 - IS ASDK --> 
- In einem virtuellen Netzwerk, das mit der DNS-Servereinstellung *Automatisch* erstellt wurde, tritt bei der Änderung eines benutzerdefinierten DNS-Servers ein Fehler auf. Die aktualisierten Einstellungen werden nicht per Pushvorgang auf VMs in diesem VNET übertragen.

<!-- TBD - IS ASDK --> 
- Azure Stack unterstützt nicht das Hinzufügen zusätzlicher Netzwerkschnittstellen zu einer VM-Instanz, nachdem die VM bereitgestellt wurde. Wenn die VM mehr als eine Netzwerkschnittstelle benötigt, müssen diese zum Zeitpunkt der Bereitstellung definiert werden.

<!-- 2096388 IS --> 
- Sie können das Verwaltungsportal nicht verwenden, um Regeln für eine Netzwerksicherheitsgruppe zu aktualisieren. 

    Problemumgehung für App Service: Wenn Sie eine Remotedesktopverbindung mit den Controllerinstanzen herstellen müssen, ändern Sie mit PowerShell die Sicherheitsregeln innerhalb der Netzwerksicherheitsgruppen.  Im Folgenden werden Beispiele zum *Zulassen* und anschließenden Wiederherstellen der Konfiguration mit *Verweigern* vorgestellt:  
    
    - *Zulassen*:
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

<!-- TBD - IS --> 
- Auf Servern, die SQL oder MySQL hosten, kann nur der Ressourcenanbieter Elemente erstellen. Die auf einem Hostserver erstellten Elemente, die nicht vom Ressourcenanbieter erstellt wurden, könnten zu einem Zustand ohne Entsprechung führen.  

<!-- IS, ASDK --> 
- Sonderzeichen, einschließlich Leerzeichen und Interpunktion, werden in den Namen für die **Familie** oder **Ebene** nicht unterstützt, wenn Sie die SKU für die SQL- und MySQL-Ressourcenanbieter erstellen.

<!-- TBD - IS -->
> [!NOTE]  
>  Nachdem Sie ein Update auf Azure Stack 1804 ausgeführt haben, können Sie die zuvor bereitgestellten Ressourcenanbieter SQL und MySQL weiterhin verwenden.  Es wird empfohlen, SQL und MySQL zu aktualisieren, sobald ein neues Release verfügbar ist. Wenden Sie Updates wie bei Azure Stack sequenziell auf die Ressourcenanbieter SQL und MySQL an.  Wenn Sie z.B. Version 1802 verwenden, wenden Sie zuerst Version 1803 an, und aktualisieren Sie sie dann auf 1804.      
>   
> Die Installation des Updates 1804 wirkt sich nicht auf die aktuelle Verwendung der von Ihren Benutzern verwendeten Ressourcenanbieter SQL oder MySQL aus.
> Unabhängig von der Version der Ressourcenanbieter, die Sie verwenden, bleiben Ihre Benutzerdaten in deren Datenbanken davon unberührt und stehen weiterhin für den Zugriff zur Verfügung.    



#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Benutzer müssen den Speicherressourcenanbieter vor dem Erstellen seiner ersten Azure-Funktion im Abonnement registrieren.

<!-- TBD - IS ASDK --> 
- Um die Infrastruktur (Worker-, Verwaltungs-, Front-End-Rollen) horizontal hochzuskalieren, müssen Sie PowerShell verwenden, wie in den Anmerkungen zu dieser Version für die Berechnung beschrieben.

<!-- TBD - IS ASDK --> 
- App Service kann derzeit nur im **Abonnement für Standardanbieter** bereitgestellt werden.  In einem zukünftigen Update wird App Service im neuen in Azure Stack 1804 eingeführten Abonnement für Messungen bereitgestellt, auch werden alle vorhandenen Bereitstellungen zu diesem neuen Abonnement migriert.

#### <a name="usage"></a>Verwendung  
<!-- TBD - IS ASDK --> 
-  Die Ansicht „Verwendung“ für die Nutzungsdaten öffentlicher IP-Adressen zeigt den gleichen *EventDateTime*-Wert für jeden Datensatz anstatt des *TimeDate*-Stempels, der anzeigt, wann dieser jeweils erstellt wurde. Derzeit können Sie diese Daten nicht nutzen, um eine genaue Buchhaltung über die Nutzung öffentlicher IP-Adressen durchzuführen.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Herunterladen des Updates
Sie können das Paket für das Azure Stack-Update 1804 [hier](https://aka.ms/azurestackupdatedownload) herunterladen.


## <a name="see-also"></a>Siehe auch
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](../azure-stack-monitor-update.md).
- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).