---
title: Azure Stack – Bekannte Probleme
description: Enthält Informationen zu bekannten Problemen in Releases von Azure Stack.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 09/13/2019
ms.openlocfilehash: ba5b8358dced378f499c30f9b8d409497319fc86
ms.sourcegitcommit: 2c37ac8e88de19430080128bac1b70e33557d354
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164252"
---
# <a name="azure-stack-known-issues"></a>Azure Stack – Bekannte Probleme

In diesem Artikel sind die bekannten Probleme in Releases von Azure Stack aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

Um auf bekannte Probleme für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908 – Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
- Häufigkeit: Common

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Common

### <a name="subscriptions-properties-blade"></a>Blatt „Abonnementeigenschaften“

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Verwaltungsportal wird das Blatt **Eigenschaften** für Abonnements nicht ordnungsgemäß geladen
- Abhilfe: Sie können diese Abonnementeigenshaften im Bereich **Essentials** des Blatts **Abonnements – Übersicht** anzeigen.
- Häufigkeit: Common

### <a name="subscriptions-lock-blade"></a>Blatt für Abonnementsperre

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Administratorportal verfügt das Blatt **Sperren** für Benutzerabonnements über zwei Schaltflächen, die den Namen **Abonnement** haben.
- Häufigkeit: Common

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Common

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Common

## <a name="networking"></a>Netzwerk

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="network-interface"></a>Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Common

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="local-network-gateway-deletion"></a>Löschung des lokalen Netzwerkgateways

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird beim Löschen des **lokalen Netzwerkgateways** die folgende Fehlermeldung angezeigt: **Ein lokales Netzwerkgateway mit einer aktiven Verbindung kann nicht gelöscht werden**, obwohl keine aktive Verbindung vorhanden ist.
- Lösung: Die Lösung dieses Problems wird in Version 1907 veröffentlicht. Eine Problemumgehung besteht darin, ein neues lokales Netzwerkgateway mit der gleichen IP-Adresse, dem gleichen Adressraum und den gleichen Konfigurationsdetails, aber unter einem anderen Namen zu erstellen. Das alte LNG kann gelöscht werden, nachdem die Umgebung auf 1907 aktualisiert wurde.
- Häufigkeit: Common

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuelle Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack. Verwenden Sie die folgenden Links für die Azure Stack-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: None (Keine):
- Häufigkeit: Common

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack-Build nicht unterstützt wird.
- Abhilfe: None (Keine):
- Häufigkeit: Common

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.    
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack noch nicht unterstützt wird.
- Abhilfe: None (Keine):
- Häufigkeit: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>1907 – Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack-Update 1907 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](azure-stack-apply-updates.md).
- Häufigkeit: Common

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Common

### <a name="subscriptions-properties-blade"></a>Blatt „Abonnementeigenschaften“

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Verwaltungsportal wird das Blatt **Eigenschaften** für Abonnements nicht ordnungsgemäß geladen
- Abhilfe: Sie können diese Abonnementeigenshaften im Bereich **Essentials** des Blatts **Abonnements – Übersicht** anzeigen.
- Häufigkeit: Common

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Common

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Common

## <a name="networking"></a>Netzwerk

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="network-interface"></a>Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Common

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="network-connection-type"></a>Netzwerkverbindungstyp

- Geltungsbereich: Dieses Problem betrifft alle 1906- und 1907-Umgebungen. 
- Ursache: Im Benutzerportal wird auf dem Blatt **AddConnection** eine Option für die Verwendung von **VNet-zu-VNet** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt. 
- Häufigkeit: Common 

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuelle Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack. Verwenden Sie die folgenden Links für die Azure Stack-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: None (Keine):
- Häufigkeit: Common

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack-Build nicht unterstützt wird.
- Abhilfe: None (Keine):
- Häufigkeit: Common

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.    
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack noch nicht unterstützt wird.
- Abhilfe: None (Keine):
- Häufigkeit: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>1906 – Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack-Update 1906 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. 
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](azure-stack-apply-updates.md).
- Häufigkeit: Common

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Common

### <a name="subscription-resources"></a>Abonnementressourcen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen.
- Abhilfe: Löschen Sie zuerst die Benutzerressourcen oder die gesamte Ressourcengruppe und anschließend die Benutzerabonnements.
- Häufigkeit: Common

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Common

### <a name="subscriptions-properties-blade"></a>Blatt „Abonnementeigenschaften“
- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Verwaltungsportal wird das Blatt **Eigenschaften** für Abonnements nicht ordnungsgemäß geladen
- Abhilfe: Sie können diese Abonnementeigenschaften im Bereich „Essentials“ des Blatts „Abonnements – Übersicht“ anzeigen.
- Häufigkeit: Common

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Common

### <a name="update"></a>Aktualisieren

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Im Betreiberportal wird für den Hotfix ein fehlerhafter Updatestatus angezeigt. Der Anfangszustand zeigt an, dass das Update nicht installiert werden konnte, obwohl es noch ausgeführt wird.
- Abhilfe: Aktualisieren Sie das Portal. Dann wird der Status in „Wird ausgeführt“ geändert.
- Häufigkeit: Zeitweilig

## <a name="networking"></a>Netzwerk

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

### <a name="network-interface"></a>Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Common

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Common

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuelle Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack. Verwenden Sie die folgenden Links für die Azure Stack-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Regeln für eingehenden Datenverkehr

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: None (Keine):
- Häufigkeit: Common

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack-Build nicht unterstützt wird.
- Abhilfe: None (Keine):
- Häufigkeit: Common

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack noch nicht unterstützt wird.
- Abhilfe: None (Keine):
- Häufigkeit: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-update-process"></a>1905 – Updateprozess

### <a name="host-node-update-prerequisite-failure"></a>Update von Hostknoten: Fehler bei Voraussetzungen

- Geltungsbereich: Dieses Problem gilt für das Update 1905.
- Ursache: Beim Versuch, das Azure Stack-Update 1905 zu installieren, ist das Update aufgrund eines Fehlers bei den **Voraussetzungen für das Hostknoten-Update** unter Umständen nicht erfolgreich. Dies wird meist dadurch verursacht, dass ein Hostknoten nicht über genügend freien Speicherplatz verfügt.
- Abhilfe: Wenden Sie sich an den Azure Stack-Support, um Hilfe beim Freigeben von Speicherplatz auf dem Hostknoten zu erhalten.
- Häufigkeit: Selten

### <a name="preparation-failed"></a>Fehler bei der Vorbereitung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack-Update 1905 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. Da das Updatepaket 1905 größer als vorherige Updatepakete ist, kann dieses Problem häufiger auftreten.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](azure-stack-apply-updates.md).
- Häufigkeit: Common

## <a name="portal"></a>Portal

### <a name="subscription-resources"></a>Abonnementressourcen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen.
- Abhilfe: Löschen Sie zuerst die Benutzerressourcen oder die gesamte Ressourcengruppe und anschließend die Benutzerabonnements.
- Häufigkeit: Common

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Common

### <a name="marketplace-management"></a>Marketplace-Verwaltung

- Geltungsbereich: Dieses Problem gilt für 1904 und 1905.
- Ursache: Der Marketplace-Verwaltungsbildschirm ist nicht sichtbar, wenn Sie sich beim Administratorportal anmelden.
- Abhilfe: Aktualisieren Sie den Browser, oder navigieren Sie zu **Einstellungen**, und wählen Sie die Option **Auf Standard zurücksetzen**.
- Häufigkeit: Zeitweilig

### <a name="docker-extension"></a>Docker-Erweiterung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Administrator- oder Benutzerportal nach **Docker** suchen, wird das Element nicht richtig zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, es zu erstellen, wird ein Fehler angezeigt.
- Abhilfe: Keine Lösung.
- Häufigkeit: Common

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Common

### <a name="template"></a>Vorlage

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal füllt die Benutzeroberfläche für die Vorlagenbereitstellung Parameter für die Vorlagennamen, die mit „_“ (Unterstrich) beginnen, nicht auf.
- Abhilfe: Entfernen Sie den „_“ (Unterstrich) aus dem Vorlagennamen.
- Häufigkeit: Common

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Regeln für eingehenden Datenverkehr

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Lastenausgleich erstellen** eine Option zum Erstellen einer Lastenausgleichs-SKU vom Typ **Standard** angezeigt. Diese Option wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie stattdessen die Lastenausgleichsoptionen vom Typ **Basic**.
- Häufigkeit: Common

### <a name="public-ip-address"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Öffentliche IP-Adresse erstellen** eine Option zum Erstellen einer SKU vom Typ **Standard** angezeigt. Die SKU **Standard** wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie die SKU **Basic** für die öffentliche IP-Adresse.
- Häufigkeit: Common

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** .
Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Common

### <a name="vm-resize"></a>Ändern der Größe einer VM

- Geltungsbereich: Dieses Problem gilt für Release 1905.
- Ursache: Die Größe einer VM mit verwaltetem Datenträger lässt sich nicht erfolgreich ändern. Der Versuch, die Größe der VM zu ändern, generiert einen Fehler mit dem „Code“: „InternalOperationError“, „Meldung“: „Interner Fehler bei Vorgang.“
- Abhilfe: Wir arbeiten daran, dieses Problem im nächsten Release zu beheben. Zurzeit müssen Sie den virtuellen Computer mit der neuen VM-Größe neu erstellen.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="centos"></a>CentOS

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Auf der Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen wird „CentOS 7.2-basiert“ als Option für die Bereitstellung angeboten. CentOS 7.2 ist im Azure Stack-Marketplace nicht verfügbar, sodass es zu Bereitstellungsfehlern mit dem Hinweis kommt, dass das Image nicht gefunden wurde.
- Abhilfe: Wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Bediener aus dem Marketplace heruntergeladen wurde.
- Häufigkeit: Common

#### <a name="remove-scale-set"></a>Entfernen einer Skalierungsgruppe

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen.
- Abhilfe: Wählen Sie die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.
- Häufigkeit: Common

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>Das Blatt mit der Instanzenansicht einer Skalierungsgruppe wird nicht geladen

- Geltungsbereich: Dieses Problem gilt für das Release 1904 und 1905.
- Ursache: Das Blatt mit der Instanzenansicht einer VM-Skalierungsgruppe im Azure Stack-Portal unter „Dashboard“ -> „VM-Skalierungsgruppen“-> „AnyScaleSet – Instanzen“ -> „AnyScaleSetInstance“ kann nicht geladen werden und zeigt das Bild einer weinenden Wolke an.
- Abhilfe: Es gibt derzeit keine Abhilfe, und wir arbeiten an der Behebung des Fehlers. Verwenden Sie bis dahin den CLI-Befehl `az vmss get-instance-view`, um die Instanzenansicht einer Skalierungsgruppe abzurufen.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="archive"></a>Archivieren

Um auf archivierte bekannte Probleme für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>1904 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>1903 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>1902 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>1901 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>1811 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>1809 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>1808 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>1807 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>1805 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>1804 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>1803 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>1802 – archivierte bekannte Probleme
::: moniker-end

::: moniker range="<azs-1905"
Sie können auf [ältere Versionen der bekannten Probleme in Azure Stack im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack-Support finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end