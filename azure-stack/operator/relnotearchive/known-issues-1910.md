---
title: 'Azure Stack Hub 1910: Bekannte Probleme'
description: Enthält Informationen zu bekannten Problemen in Azure Stack Hub 1910.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 0a23951d53d57b103c442ecf2b5b18f894e0f58b
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248715"
---
# <a name="azure-stack-hub-1910-known-issues"></a>Azure Stack Hub 1910: Bekannte Probleme

In diesem Artikel sind die bekannten Probleme aufgelistet, die es in Azure Stack Hub-Releases gibt. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update"></a>Aktualisieren

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Problembehandlung von Azure Stack Hub-Updates](../azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

### <a name="duplicate-subscription-button-in-lock-blade"></a>Schaltfläche für Duplikatabonnements auf dem Blatt „Sperre“

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

### <a name="alert-for-network-interface-disconnected"></a>Warnung für getrennte Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem betrifft das Release 1908 und höhere Releases.
- Ursache: Wenn ein Kabel von einem Netzwerkadapter getrennt wird, wird im Administratorportal keine Warnung angezeigt. Dieses Problem ist darauf zurückzuführen, dass dieser Fehler in Windows Server 2019 standardmäßig deaktiviert ist.
- Häufigkeit: Allgemein

### <a name="incorrect-tooltip-when-creating-vm"></a>Falsche QuickInfo bei der VM-Erstellung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal einen verwalteten Datenträger vom Typ „SSD Premium“ auswählen, wird in der Dropdownliste **Betriebssystemdatenträger** angezeigt. Die QuickInfo neben dieser Option lautet: **Bestimmte Betriebssystemdatenträger sind mit dem kostenlosen Azure-Konto möglicherweise gebührenfrei verfügbar.** Dies gilt jedoch nicht für Azure Stack Hub. Darüber hinaus enthält die Liste **Free account eligible** (Geeignet für kostenloses Konto), was ebenfalls nicht für Azure Stack Hub gilt.
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

### <a name="activity-log-blade"></a>Blatt „Aktivitätsprotokoll“

- Geltungsbereich: Dieses Problem betrifft Stempel, auf denen das Release 1907 oder ein höheres Release ausgeführt wird. <!-- Note: Applies to 2002 as well -->
- Ursache: Beim Zugriff auf das Aktivitätsprotokoll wird im Portal nur die erste Seite mit Einträgen angezeigt. Mit **Mehr laden** können keine weiteren Einträge geladen werden.
- Abhilfe: Passen Sie den Zeitbereich im Filter an, um Einträge zu erhalten, die über die erste Seite hinausgehen.

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

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Eine NSG kann nicht gelöscht werden, wenn NICs nicht an die laufende VM angefügt sind.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Beim Aufheben der Zuordnung einer NSG und einer NIC, die nicht an eine laufende VM angefügt ist, schlägt der Updatevorgang (PUT) für dieses Objekt auf der Ebene des Netzwerkcontrollers fehl. Die NSG wird auf der Ebene des Netzwerkressourcenanbieters aktualisiert, aber nicht auf dem Netzwerkcontroller, sodass die NSG in einen fehlerhaften Zustand wechselt.
- Abhilfe: Fügen Sie die NICs an, die der NSG zugeordnet sind, die mit laufenden VMs entfernt werden muss, und heben Sie die Zuordnung der NSG auf, oder entfernen Sie alle NICs, die der NSG zugeordnet sind.
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

#### <a name="next-hop-type"></a>Typ des nächsten Hops

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal eine Routingtabelle erstellen, wird **Gateway für virtuelle Netzwerke** als Option für den Typ des nächsten Hops angezeigt. Dies wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

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

#### <a name="vpn-troubleshooter"></a>VPN-Problembehandlung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal werden das Feature **VPN-Problembehandlung** sowie **Metriken** in einer VPN-Gatewayressource angezeigt. Dies wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](../azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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
