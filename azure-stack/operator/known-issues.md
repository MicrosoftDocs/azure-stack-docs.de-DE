---
title: Azure Stack Hub – Bekannte Probleme
description: Enthält Informationen zu bekannten Problemen in Releases von Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 12/27/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 631a086b86fd7855780aa1cb6dba11dbfa799759
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76879821"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack Hub – Bekannte Probleme

In diesem Artikel sind die bekannten Probleme in Releases von Azure Stack Hub aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

Um auf bekannte Probleme für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1910"
## <a name="update"></a>Aktualisieren

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Behandeln von Patch- und Updateproblemen bei Azure Stack](azure-stack-updates-troubleshoot.md).

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

### <a name="subscriptions-lock-blade"></a>Blatt für Abonnementsperre

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Administratorportal verfügt das Blatt **Sperren** für Benutzerabonnements über zwei Schaltflächen mit der Bezeichnung **Abonnement**.
- Häufigkeit: Allgemein

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack Hub-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="upload-blob-with-oauth-error"></a>Fehler beim Hochladen eines Blobs mit OAuth

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Allgemein

### <a name="upload-blob-option-unsupported"></a>Nicht unterstützte Blob-Uploadoption

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie auf dem Uploadblatt des Benutzerportals ein Blob hochladen möchten, können Sie zwischen **AAD** und **Schlüsselauthentifizierung** wählen. **AAD** wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="load-balancer-backend-pool"></a>Back-End-Pool für den Lastenausgleich

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal einen Back-End-Pool vom Typ **Lastenausgleich** hinzufügen, wird die Fehlermeldung **Fehler beim Speichern des Back-End-Pools des Lastenausgleichs** angezeigt, obwohl der Vorgang erfolgreich war.
- Häufigkeit: Allgemein

### <a name="alert-for-network-interface-disconnected"></a>Warnung für getrennte Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem betrifft die Releases 1908 und 1910.
- Ursache: Wenn ein Kabel von einem Netzwerkadapter getrennt wird, wird im Administratorportal keine Warnung angezeigt. Dieses Problem ist darauf zurückzuführen, dass dieser Fehler in Windows Server 2019 standardmäßig deaktiviert ist.
- Häufigkeit: Allgemein

### <a name="incorrect-tooltip-when-creating-vm"></a>Falsche QuickInfo bei der VM-Erstellung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal einen verwalteten Datenträger vom Typ „SSD Premium“ auswählen, wird in der Dropdownliste **Betriebssystemdatenträger** angezeigt. Die QuickInfo neben dieser Option lautet: **Bestimmte Betriebssystemdatenträger sind mit dem kostenlosen Azure-Konto möglicherweise gebührenfrei verfügbar.** Dies gilt jedoch nicht für Azure Stack Hub. Darüber hinaus enthält die Liste **Free account eligible** (Geeignet für kostenloses Konto), was ebenfalls nicht für Azure Stack Hub gilt.
- Häufigkeit: Allgemein

### <a name="vpn-troubleshoot-and-metrics"></a>VPN-Problembehandlung und Metriken

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal werden das Feature **VPN-Problembehandlung** sowie **Metriken** in einer VPN-Gatewayressource angezeigt. Dies wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="adding-extension-to-vm-scale-set"></a>Hinzufügen einer Erweiterung zu einer VM-Skalierungsgruppe

- Geltungsbereich: Dieses Problem betrifft das Release 1907 und spätere Releases.
- Ursache: Nach Erstellung einer VM-Skalierungsgruppe kann über die Benutzeroberfläche des Benutzerportals keine Erweiterung mehr hinzugefügt werden.
- Häufigkeit: Allgemein

### <a name="delete-a-storage-container"></a>Löschen eines Speichercontainers

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn ein Benutzer im Benutzerportal versucht, einen Speichercontainer zu löschen, ist der Vorgang nicht erfolgreich, wenn der Benutzer **Einstellungen für Azure Policy und RBAC-Rolle außer Kraft setzen** nicht aktiviert.
- Abhilfe: Vergewissern Sie sich, dass das Kontrollkästchen für **Einstellungen für Azure Policy und RBAC-Rolle außer Kraft setzen** aktiviert ist.
- Häufigkeit: Allgemein

### <a name="refresh-button-on-virtual-machines-fails"></a>Aktualisierungsschaltfläche für virtuelle Computer funktioniert nicht

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal zu **Virtuelle Computer** navigieren und die Ansicht mithilfe der entsprechenden Schaltfläche im oberen Bereich aktualisieren, werden die Zustände nicht ordnungsgemäß aktualisiert.
- Abhilfe: Der Status wird automatisch alle fünf Minuten aktualisiert – unabhängig davon, ob auf die Aktualisierungsschaltfläche geklickt wurde. Warten Sie fünf Minuten, und überprüfen Sie den Status.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal eine Routingtabelle erstellen, wird **Gateway für virtuelle Netzwerke** als Option für den Typ des nächsten Hops angezeigt. Dies wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="storage-account-options"></a>Speicherkontooptionen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird der Name von Speicherkonten als **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** angezeigt. **Datei** wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="storage-account-configuration"></a>Speicherkontokonfiguration

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal ein Speicherkonto erstellen und dessen **Konfiguration** anzeigen, können Sie keine Konfigurationsänderungen speichern, da dies einen AJAX-Fehler zur Folge hat.
- Häufigkeit: Allgemein

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>Kapazitätsüberwachung im SQL-Ressourcenanbieter wird weiterhin geladen

- Geltungsbereich: Dieses Problem betrifft das Azure Stack Hub-Update 1910 und höhere Versionen, bei denen maximal die SQL-Ressourcenanbieterversion 1.1.33.0 installiert ist.
- Ursache: Die aktuelle Version des SQL-Ressourcenanbieters ist mit einigen der neuesten Portaländerungen des Updates 1910 nicht kompatibel.
- Abhilfe: Führen Sie im Anschluss an das Azure Stack Hub-Upgrade auf das Update 1910 ([SQL-Ressourcenanbieterversion 1.1.47.0](https://aka.ms/azurestacksqlrp11470)) die Schritte des Ressourcenanbieter-Updateprozesses aus, um den SQL-Ressourcenanbieter-Hotfix 1.1.47.0 anzuwenden. Für den MySQL-Ressourcenanbieter sollte im Anschluss an das Azure Stack Hub-Upgrade auf das Update 1910 ([MySQL-Ressourcenanbieterversion 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)) auch der MySQL-Ressourcenanbieter-Hotfix 1.1.47.0 angewendet werden.
- Häufigkeit: Allgemein

### <a name="access-control-iam"></a>Zugriffssteuerung (IAM)

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die IAM-Erweiterung ist veraltet. Das Ibiza-Portal ist im Lieferumfang von Azure Stack Hub enthalten und führt ein neues Verhalten ein, das einen Fehler der RBAC-Erweiterung verursacht, wenn Benutzer das Blatt **Zugriffssteuerung (IAM)** für ein Abonnement öffnen, das nicht in der Auswahl für globale Abonnements ausgewählt ist (**Verzeichnis + Abonnement** im Benutzerportal). Auf dem Blatt wird **Laden** in einer Schleife angezeigt, und Benutzer können dem Abonnement keine neuen Rollen hinzufügen. Auf dem Blatt **Hinzufügen** wird ebenfalls **Laden** in einer Schleife angezeigt.
- Abhilfe: Stellen Sie sicher, dass das Abonnement im Menü **Verzeichnis + Abonnement** aktiviert ist. Sie können wie folgt auf das Menü zugreifen: Über den oberen Portalbereich (in der Nähe der Schaltfläche **Benachrichtigungen**) oder über die Verknüpfung auf dem Blatt **Alle Ressourcen**, die **Wird kein Abonnement angezeigt? Verzeichnis- und Abonnementeinstellungen öffnen** lautet. Das Abonnement muss in diesem Menü ausgewählt werden.

### <a name="sql-resource-provider"></a>SQL-Ressourcenanbieter

- Geltungsbereich: Dieses Problem gilt für Stempel, auf denen das Update 1908 oder früher ausgeführt wird.
- Ursache: Beim Bereitstellen der SQL-Ressourcenanbieterversion 1.1.47.0 zeigt das Portal nur Ressourcen an, die dem SQL-Ressourcenanbieter zugeordnet sind.
- Abhilfe: Löschen Sie den Ressourcenanbieter, führen Sie ein Upgrade des Stempels durch, und stellen Sie den SQL-Ressourcenanbieter erneut bereit.

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie dem Back-End-Pool eines Lastenausgleichs Verfügbarkeitsgruppen-VMs hinzufügen, wird im Portal die Fehlermeldung **Fehler beim Speichern des Back-End-Pools des Lastenausgleichs** angezeigt. Hierbei handelt es sich lediglich um ein kosmetisches Problem im Portal. Die Funktion wird dadurch nicht beeinträchtigt, und virtuelle Computer werden dem Back-End-Pool intern erfolgreich hinzugefügt.
- Häufigkeit: Allgemein

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Eine explizite **DenyAllOutbound**-Regel kann nicht in einer Netzwerksicherheitsgruppe (NSG) erstellt werden, da dadurch die gesamte interne Kommunikation mit der Infrastruktur, die für die VM-Bereitstellung erforderlich ist, verhindert wird.
- Häufigkeit: Allgemein

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-interface"></a>Netzwerkschnittstelle

#### <a name="addingremoving-network-interface"></a>Hinzufügen/Entfernen einer Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen oder entfernen.
- Häufigkeit: Allgemein

#### <a name="primary-network-interface"></a>Primäre Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Der primäre Netzwerkadapter (Network Interface Card, NIC) einer VM kann nicht geändert werden. Das Löschen oder Trennen der primären NIC führt zu Problemen beim Starten des virtuellen Computers.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** ein Feature namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (virtual machine, VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="consumed-compute-quota"></a>Verbrauchtes Computekontingent

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Computer erstellen, wird unter Umständen eine Fehlermeldung wie die folgende angezeigt: **Dieses Abonnement hat die Kapazität für „Regionale vCPUs gesamt“ an diesem Standort erreicht. 50 „Regionale vCPUs gesamt“ sind verfügbar, und alle werden verwendet.** Dies deutet darauf hin, dass das Kontingent für die Gesamtanzahl verfügbarer Kerne erreicht wurde.
- Abhilfe: Erkundigen Sie sich bei Ihrem Betreiber nach einem Add-On-Plan mit zusätzlichem Kontingent. Das Kontingent des aktuellen Plans kann nicht bearbeitet werden, oder das höhere Kontingent wird nicht berücksichtigt.
- Häufigkeit: Selten

### <a name="privileged-endpoint"></a>Privilegierter Endpunkt

- Geltungsbereich: Dieses Problem betrifft das Release 1910 und ältere Releases.
- Ursache: Von einem Computer, auf dem eine nicht englischsprachige Windows-Version ausgeführt wird, kann keine Verbindung mit dem privilegierten Endpunkt (ERC-VMs) hergestellt werden.
- Abhilfe: Dies ist ein bekanntes Problem, das in neueren Releases als 1910 behoben wurde. Zur Problemumgehung können Sie die PowerShell-Cmdlets **New-PSSession** und **Enter-PSSession** unter Verwendung der Kultur **en-US** ausführen. Legen Sie die Kultur beispielsweise mit diesem Skript fest: https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1.
- Häufigkeit: Selten

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Bereitstellung mit vier Knoten aber immer noch nicht verfügbar.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908 – Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack Hub-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
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

### <a name="subscriptions-lock-blade"></a>Blatt für Abonnementsperre

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Administratorportal verfügt das Blatt **Sperren** für Benutzerabonnements über zwei Schaltflächen mit der Bezeichnung **Abonnement**.
- Häufigkeit: Allgemein

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack Hub-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Allgemein

### <a name="alert-for-network-interface-disconnected"></a>Warnung für getrennte Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für Release 1908.
- Ursache: Wenn ein Kabel von einem Netzwerkadapter getrennt wird, wird im Administratorportal keine Warnung angezeigt. Dieses Problem ist darauf zurückzuführen, dass dieser Fehler in Windows Server 2019 standardmäßig deaktiviert ist.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Wenn Sie dem Back-End-Pool eines Lastenausgleichs Verfügbarkeitsgruppen-VMs hinzufügen, wird im Portal die Fehlermeldung **Fehler beim Speichern des Back-End-Pools des Lastenausgleichs** angezeigt. Hierbei handelt es sich lediglich um ein kosmetisches Problem im Portal. Die Funktion wird dadurch nicht beeinträchtigt, und virtuelle Computer werden dem Back-End-Pool intern erfolgreich hinzugefügt. 
- Häufigkeit: Allgemein

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Eine explizite **DenyAllOutbound**-Regel kann nicht in einer Netzwerksicherheitsgruppe (NSG) erstellt werden, da dadurch die gesamte interne Kommunikation mit der Infrastruktur, die für die VM-Bereitstellung erforderlich ist, verhindert wird.
- Häufigkeit: Allgemein

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-interface"></a>Netzwerkschnittstelle

#### <a name="addingremoving-network-interface"></a>Hinzufügen/Entfernen einer Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Allgemein

#### <a name="primary-network-interface"></a>Primäre Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack Hub unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack Hub-Build nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.    
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack Hub noch nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

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
- Ursache: Wenn Sie versuchen, das Azure Stack Hub-Update 1907 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](azure-stack-apply-updates.md).
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
- Ursache: Sie können mit den Azure Stack Hub-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Wenn Sie dem Back-End-Pool eines Lastenausgleichs Verfügbarkeitsgruppen-VMs hinzufügen, wird im Portal die Fehlermeldung **Fehler beim Speichern des Back-End-Pools des Lastenausgleichs** angezeigt. Hierbei handelt es sich lediglich um ein kosmetisches Problem im Portal. Die Funktion wird dadurch nicht beeinträchtigt, und virtuelle Computer werden dem Back-End-Pool intern erfolgreich hinzugefügt. 
- Häufigkeit: Allgemein

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Eine explizite **DenyAllOutbound**-Regel kann nicht in einer Netzwerksicherheitsgruppe (NSG) erstellt werden, da dadurch die gesamte interne Kommunikation mit der Infrastruktur, die für die VM-Bereitstellung erforderlich ist, verhindert wird.
- Häufigkeit: Allgemein

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-interface"></a>Netzwerkschnittstelle

#### <a name="addingremoving-network-interface"></a>Hinzufügen/Entfernen einer Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Allgemein

#### <a name="primary-network-interface"></a>Primäre Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-connection-type"></a>Netzwerkverbindungstyp

- Geltungsbereich: Dieses Problem betrifft alle 1906- und 1907-Umgebungen. 
- Ursache: Im Benutzerportal wird auf dem Blatt **AddConnection** eine Option für die Verwendung von **VNet-zu-VNet** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt. 
- Häufigkeit: Allgemein 

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack Hub unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack Hub-Build nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem betrifft die Versionen 1906 und 1907.    
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack Hub noch nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

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
- Ursache: Wenn Sie versuchen, das Azure Stack Hub-Update 1906 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. 
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](azure-stack-apply-updates.md).
- Häufigkeit: Allgemein

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

### <a name="subscription-resources"></a>Abonnementressourcen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen.
- Abhilfe: Löschen Sie zuerst die Benutzerressourcen oder die gesamte Ressourcengruppe und anschließend die Benutzerabonnements.
- Häufigkeit: Allgemein

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack Hub-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="subscriptions-properties-blade"></a>Blatt „Abonnementeigenschaften“
- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Verwaltungsportal wird das Blatt **Eigenschaften** für Abonnements nicht ordnungsgemäß geladen
- Abhilfe: Sie können diese Abonnementeigenschaften im Bereich „Essentials“ des Blatts „Abonnements – Übersicht“ anzeigen.
- Häufigkeit: Allgemein

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Konfiguration** des Speicherkontos eine Option zum Ändern des **Sicherheitsübertragungstyps** angezeigt. Die Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Allgemein

### <a name="update"></a>Aktualisieren

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Im Betreiberportal wird für den Hotfix ein fehlerhafter Updatestatus angezeigt. Der Anfangszustand zeigt an, dass das Update nicht installiert werden konnte, obwohl es noch ausgeführt wird.
- Abhilfe: Aktualisieren Sie das Portal. Dann wird der Status in „Wird ausgeführt“ geändert.
- Häufigkeit: Zeitweilig

## <a name="networking"></a>Netzwerk

### <a name="service-endpoints"></a>Dienstendpunkte

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerk** eine Option zum Verwenden von **Dienstendpunkten** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="network-interface"></a>Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Einer VM, die sich im Zustand **Wird ausgeführt** befindet, kann keine neue Netzwerkschnittstelle hinzugefügt werden.
- Abhilfe: Beenden Sie den virtuellen Computer, bevor Sie eine Netzwerkschnittstelle hinzufügen/entfernen.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="alerts"></a>Alerts

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Virtuelles Netzwerkgateway** eine Option zum Verwenden von **Warnungen** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="active-active"></a>Aktiv/Aktiv

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird während der Erstellung und im Ressourcenmenü des **Virtual Networkgateways** eine Option zum Aktivieren von **Aktiv/Aktiv** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird auf dem Blatt **Verbindungen** eine Funktion namens **VPN-Problembehandlung** angezeigt. Diese Funktion wird zurzeit in Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Allgemein

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Regeln für eingehenden Datenverkehr

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Allgemein

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Das Zurücksetzen des Kennworts einer VM-Skalierungsgruppe funktioniert nicht

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Ein neues Blatt zum Zurücksetzen des Kennworts wird in der Benutzeroberfläche der Skalierungsgruppe angezeigt, aber Azure Stack Hub unterstützt das Zurücksetzen von Kennwörtern für eine Skalierungsgruppe noch nicht.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Regenwolke in Skalierungsgruppendiagnose

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Auf der Übersichtsseite für die VM-Skalierungsgruppe wird ein leeres Diagramm angezeigt. Durch Klicken auf das leere Diagramm wird ein Blatt mit einer „Regenwolke“ angezeigt. Dies ist das Diagramm für die Diagnoseinformationen der Skalierungsgruppe, z. B. CPU-Prozentsatz, und dies ist eine Funktion, die im aktuellen Azure Stack Hub-Build nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

### <a name="issues-creating-resources"></a>Probleme beim Erstellen von Ressourcen

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: 1906 weist ein bekanntes Problem mit der Zuordnung von benutzerdefinierten Rollen und Berechtigungen für die Ressourcenerstellung auf. Beim Erstellen von Ressourcen treten möglicherweise Probleme auf, auch wenn Sie über die richtigen Berechtigungen verfügen.
- Abhilfe: Aktualisieren Sie auf Build 1907, um dieses Problem zu beheben.
- Häufigkeit: Allgemein

### <a name="virtual-machine-diagnostic-settings-blade"></a>Blatt mit VM-Diagnoseeinstellungen

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: Das Blatt mit den VM-Diagnoseeinstellungen verfügt über eine Registerkarte **Senke**, auf der ein **Application Insights-Konto** abgefragt wird. Dies resultiert aus einem neuen Blatt, das in Azure Stack Hub noch nicht unterstützt wird.
- Abhilfe: Keine.
- Häufigkeit: Allgemein

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="archive"></a>Archivieren

Um auf archivierte bekannte Probleme für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>1905 – archivierte bekannte Probleme
::: moniker-end
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

::: moniker range="<azs-1906"
Sie können auf [ältere Versionen der bekannten Probleme in Azure Stack Hub im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack Hub-Support finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end
