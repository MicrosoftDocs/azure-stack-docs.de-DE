---
title: 'Azure Stack 1907: Bekannte Probleme'
description: Enthält Informationen zu bekannten Problemen in Releases von Azure Stack 1907.
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 435f761633b3513be39cd5912cbcee697003e952
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248718"
---
# <a name="azure-stack-1907-known-issues"></a>Azure Stack 1907: Bekannte Probleme

In diesem Artikel sind die bekannten Probleme in Releases von Azure Stack aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

Um auf bekannte Probleme für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.

> [!IMPORTANT]  
> Wenn die Version der Azure Stack-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="1907-update-process"></a>1907 – Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack-Update 1907 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](../azure-stack-apply-updates.md).
- Häufigkeit: Allgemein

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

### <a name="subscriptions-properties-blade"></a>Blatt „Abonnementeigenschaften“

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Verwaltungsportal wird das Blatt **Eigenschaften** für Abonnements nicht ordnungsgemäß geladen
- Abhilfe: Sie können diese Abonnementeigenshaften im Bereich **Essentials** des Blatts **Abonnements – Übersicht** anzeigen.
- Häufigkeit: Allgemein

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-interface"></a>Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-connection-type"></a>Netzwerkverbindungstyp

- Geltungsbereich: Dieses Problem betrifft alle 1906- und 1907-Umgebungen. 
- Ursache: Im Benutzerportal wird auf dem Blatt **AddConnection** eine Option für die Verwendung von **VNet-zu-VNet** angezeigt. Diese Funktion wird zurzeit in Azure Stack nicht unterstützt. 
- Häufigkeit: Allgemein 

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuelle Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack. Verwenden Sie die folgenden Links für die Azure Stack-Dokumentation:

  - [Gateway-SKUs](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](../azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack-Build nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.    
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack noch nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein
