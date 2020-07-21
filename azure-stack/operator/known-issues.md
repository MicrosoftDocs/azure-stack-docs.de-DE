---
title: Azure Stack Hub – Bekannte Probleme
description: Enthält Informationen zu bekannten Problemen in Releases von Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 07/10/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: d0a212b231f187ca9ed2ce5520639b0e08a3d7e2
ms.sourcegitcommit: e433e6f772789ab00c131c24650e700c65e6d73a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86272962"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack Hub – Bekannte Probleme

In diesem Artikel sind die bekannten Probleme in Releases von Azure Stack Hub aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

Um auf bekannte Probleme für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2002"
## <a name="update"></a>Aktualisieren

Nach dem Anwenden von Update 2002 wird im Administratorportal ggf. fälschlicherweise die Warnung „Ungültige Zeitquelle“ angezeigt. Sie können diese Warnung (False Positive) ignorieren. Der Fehler wird mit einem der nächsten Releases behoben. 

Informationen zu weiteren bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Behandeln von Patch- und Updateproblemen bei Azure Stack](azure-stack-troubleshooting.md).

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack Hub-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="storage-account-options"></a>Speicherkontooptionen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird der Name von Speicherkonten als **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** angezeigt. **Datei** wird in Azure Stack Hub jedoch nicht unterstützt.
- Häufigkeit: Allgemein

### <a name="create-managed-disk-snapshot"></a>Erstellen einer Momentaufnahme eines verwalteten Datenträgers

- Geltungsbereich: Dieses Problem betrifft das Release 2002.
- Ursache: Beim Erstellen einer Momentaufnahme eines verwalteten Datenträgers ist im Benutzerportal das Feld **Kontotyp** leer. Wenn Sie die Schaltfläche **Erstellen** auswählen, ohne einen Kontotyp anzugeben, ist die Erstellung der Momentaufnahme nicht erfolgreich.
- Abhilfe: Wählen Sie in der Dropdownliste **Kontotyp** einen Kontotyp aus, und erstellen Sie dann die Momentaufnahme.
- Häufigkeit: Allgemein

### <a name="alert-for-network-interface-disconnected"></a>Warnung für getrennte Netzwerkschnittstelle

- Geltungsbereich: Dieses Problem betrifft das Release 1908 und höhere Releases.
- Ursache: Wenn ein Kabel von einem Netzwerkadapter getrennt wird, wird im Administratorportal keine Warnung angezeigt. Dieses Problem ist darauf zurückzuführen, dass dieser Fehler in Windows Server 2019 standardmäßig deaktiviert ist.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="denyalloutbound-rule-cannot-be-created"></a>Die DenyAllOutbound-Regel kann nicht erstellt werden.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Eine explizite **DenyAllOutbound**-Regel kann nicht in einer Netzwerksicherheitsgruppe (NSG) erstellt werden, da dadurch die gesamte interne Kommunikation mit der Infrastruktur, die für die VM-Bereitstellung erforderlich ist, verhindert wird.
- Häufigkeit: Allgemein

### <a name="icmp-protocol-not-supported-for-nsg-rules"></a>Das ICMP-Protokoll wird für NSG-Regeln nicht unterstützt.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Beim Erstellen einer Eingangs- oder Ausgangssicherheitsregel für das Netzwerk wird unter **Protokoll** die Option **ICMP** angezeigt. Dies wird unter Azure Stack Hub derzeit nicht unterstützt. Dieses Problem wurde behoben und tritt im nächsten Azure Stack Hub-Release nicht mehr auf.
- Häufigkeit: Allgemein

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Eine NSG kann nicht gelöscht werden, wenn NICs nicht an die laufende VM angefügt sind.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Beim Aufheben der Zuordnung einer NSG und einer NIC, die nicht an eine laufende VM angefügt ist, schlägt der Updatevorgang (PUT) für dieses Objekt auf der Ebene des Netzwerkcontrollers fehl. Die NSG wird auf der Ebene des Netzwerkressourcenanbieters aktualisiert, aber nicht auf dem Netzwerkcontroller, sodass die NSG in einen fehlerhaften Zustand wechselt.
- Abhilfe: Fügen Sie die NICs an, die der NSG zugeordnet sind, die mit laufenden VMs entfernt werden muss, und heben Sie die Zuordnung der NSG auf, oder entfernen Sie alle NICs, die der NSG zugeordnet sind.
- Häufigkeit: Allgemein

### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer leitet Datenverkehr in bestimmten Szenarien an eine Back-End-VM weiter 

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Beim Aktivieren der **Sitzungsaffinität** in einem Lastenausgleich verwendet der 2-Tupel-Hash die PA-IP-Adresse (physische IP-Adresse) anstelle der privaten IP-Adressen, die den VMs zugewiesen sind. In Szenarien, in denen Datenverkehr, der an den Lastenausgleich weitergeleitet wird, über ein VPN eingeht oder alle virtuellen Clientcomputer (Quell-IPs) sich im gleichen Knoten befinden und die Sitzungsaffinität aktiviert ist, wird der gesamte Datenverkehr an eine Back-End-VM geleitet.
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

### <a name="public-ip"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Der Wert **IdleTimeoutInMinutes** für eine öffentliche IP-Adresse, die einem Lastenausgleich zugeordnet ist, kann nicht geändert werden. Bei diesem Vorgang wird die öffentliche IP-Adresse in einen Fehlerzustand versetzt.
- Abhilfe: Gehen Sie wie folgt vor, um die öffentliche IP-Adresse wieder in den korrekten Zustand zu versetzen: Ändern Sie den Wert **IdleTimeoutInMinutes** für die Lastenausgleichsregel, in der auf die öffentliche IP-Adresse verwiesen wird, wieder in den ursprünglichen Wert (Standardwert: 4 Minuten).
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute
### <a name="cannot-create-a-vmss-with-standard_ds2_v2-vm-size-on-portal"></a>VMSS kann nicht mit der VM-Größe „Standard_DS2_v2“ im Portal erstellt werden.

- Geltungsbereich: Dieses Problem gilt für Release 2002.
- Ursache: Es gibt einen Portalfehler, der die VMSS-Erstellung mit der VM-Größe „Standard_DS2_v2“ verhindert. Beim Erstellen wird ein Fehler ausgegeben: "{"code":"DeploymentFailed","message":"Mindestens ein Ressourcenbereitstellungsvorgang ist fehlgeschlagen. Listen Sie die Bereitstellungsvorgänge auf, um Details anzuzeigen. Weitere Informationen zur Verwendung finden Sie unter https://aka.ms/arm-debug.","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\": \" NetworkProfileValidationError\" ,\r\n \" message\": \" Die VM-Größe „Standard_DS2_v2“ ist nicht in der Liste der zulässigen VM-Größen für beschleunigten Netzwerkbetrieb, die auf der VM bei Index 0 für die VM-Skalierungs Gruppe „/subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss“ aktiviert werden soll. Zulässige Größen: .\"\r\n }\r\n}"}]}"
- Abhilfe: Erstellen Sie eine VMSS mit PowerShell oder einer Resource Manager-Vorlage.

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Auf dem Blatt mit der VM-Übersicht wird nicht der richtige Computername angezeigt.

- Geltungsbereich: Dieses Problem betrifft alle Releases.
- Ursache: Wenn die Details eines virtuellen Computers auf dem Übersichtsblatt angezeigt werden, wird der Computername als **(nicht verfügbar)** angezeigt. Dies ist für VMs, die aus speziellen Datenträgern bzw. Datenträgermomentaufnahmen erstellt werden, so beabsichtigt.
- Abhilfe: Zeigen Sie das Blatt **Eigenschaften** unter **Einstellungen** an.

### <a name="nvv4-vm-size-on-portal"></a>NVv4-VM-Größe im Portal

- Geltungsbereich: Dieses Problem gilt für Release 2002 und höher.
- Ursache: Bei der Erstellung des virtuellen Computers wird die VM-Größe angezeigt: NV4as_v4. Bei Kunden mit der erforderlichen Hardware für die AMD Mi25-basierte Azure Stack Hub-GPU-Vorschau ist die VM-Bereitstellung erfolgreich. Bei allen anderen Kunden tritt bei der VM-Bereitstellung mit dieser VM-Größe ein Fehler auf.
- Abhilfe: Entwurfsbedingt bei der Vorbereitung für die Azure Stack Hub-GPU-Vorschau

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Computer erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** . Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, einen virtuellen Computer mit dem Status „Beendet (Zuordnung aufgehoben)“ zu starten, wird unter Umständen der folgende Fehler angezeigt: **VM diagnostics Storage account 'diagnosticstorageaccount' not found. Ensure storage account is not deleted** (Das Speicherkonto „diagnosticstorageaccount“ für die VM-Diagnose wurde nicht gefunden. Vergewissern Sie sich, dass das Speicherkonto nicht gelöscht wurde.). Der Fehler tritt auf, wenn Sie versuchen, einen virtuellen Computer mit aktivierter Startdiagnose zu starten, aber das Speicherkonto für die Startdiagnose, auf das verwiesen wird, gelöscht wurde.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="consumed-compute-quota"></a>Verbrauchtes Computekontingent

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Computer erstellen, wird unter Umständen eine Fehlermeldung wie die folgende angezeigt: **Dieses Abonnement hat die Kapazität für „Regionale vCPUs gesamt“ an diesem Standort erreicht. 50 „Regionale vCPUs gesamt“ sind verfügbar, und alle werden verwendet.** Dies deutet darauf hin, dass das Kontingent für die Gesamtanzahl verfügbarer Kerne erreicht wurde.
- Abhilfe: Erkundigen Sie sich bei Ihrem Betreiber nach einem Add-On-Plan mit zusätzlichem Kontingent. Das Kontingent des aktuellen Plans kann nicht bearbeitet werden, oder das höhere Kontingent wird nicht berücksichtigt.
- Häufigkeit: Selten

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Bereitstellung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="sql-vm"></a>SQL-VM

#### <a name="storage-account-creating-failure-when-configuring-auto-backup"></a>Fehler beim Erstellen des Speicherkontos, wenn die automatische Sicherung konfiguriert wird

- Geltungsbereich: Dieses Problem gilt für 2002.
- Ursache: Wenn Sie die automatisierte Sicherung von virtuellen SQL-Computern mit einem neuen Speicherkonto konfigurieren, tritt der folgende Fehler auf: **Deployment template validation failed. The template parameter for 'SqlAutobackupStorageAccountKind' is not found.** (Fehler bei der Bereitstellungsvorlagenvalidierung. Der Vorlagenparameter für „SqlAutobackupStorageAccountKind“ wurde nicht gefunden.).
- Abhilfe: Wenden Sie den aktuellen Hotfix für 2002 an.

#### <a name="auto-backup-cannot-be-configured-with-tls-12-enabled"></a>Die automatische Sicherung kann nicht konfiguriert werden, wenn TLS 1.2. aktiviert ist.

- Geltungsbereich: Dieses Problem gilt für Neuinstallationen von Version 2002 und höheren Versionen und für alle älteren Versionen, in denen TLS 1.2 aktiviert ist.
- Ursache: Wenn Sie die automatisierte Sicherung von virtuellen SQL-Computern mit einem vorhandenen Speicherkonto konfigurieren, tritt der Fehler **SQL Server-IaaS-Agent: Die zugrunde liegende Verbindung wurde geschlossen: Unerwarteter Fehler beim Senden** auf.
- Häufigkeit: Allgemein

## <a name="resource-providers"></a>Ressourcenanbieter

### <a name="sqlmysql"></a>SQL/MySQL

- Geltungsbereich: Dieses Problem betrifft das Release 2002. 
- Ursache: Wenn der Stempel die Version 1.1.33.0 oder eine frühere Version des SQL-Ressourcenanbieters enthält, werden die Blätter für SQL/MySQL bei der Aktualisierung des Stempels nicht geladen.
- Abhilfe: Aktualisieren Sie den Ressourcenanbieter auf die Version 1.1.47.0.

### <a name="app-service"></a>App Service

- Geltungsbereich: Dieses Problem betrifft das Release 2002.
- Ursache: Wenn der Stempel die Version 1.7 oder eine ältere Version des App Service-Ressourcenanbieters enthält, werden die Blätter für App Service bei der Aktualisierung des Stempels nicht geladen.
- Abhilfe: Aktualisieren Sie den Ressourcenanbieter auf die Version [2020 Q2](azure-stack-app-service-update.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1910"
## <a name="update"></a>Aktualisieren

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Behandeln von Patch- und Updateproblemen bei Azure Stack](azure-stack-troubleshooting.md).

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

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Eine NSG kann nicht gelöscht werden, wenn NICs nicht an die laufende VM angefügt sind.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Beim Aufheben der Zuordnung einer NSG und einer NIC, die nicht an eine laufende VM angefügt ist, schlägt der Updatevorgang (PUT) für dieses Objekt auf der Ebene des Netzwerkcontrollers fehl. Die NSG wird auf der Ebene des Netzwerkressourcenanbieters aktualisiert, aber nicht auf dem Netzwerkcontroller, sodass die NSG in einen fehlerhaften Zustand wechselt.
- Abhilfe: Fügen Sie die NICs an, die der NSG zugeordnet sind, die mit laufenden VMs entfernt werden muss, und heben Sie die Zuordnung der NSG auf, oder entfernen Sie alle NICs, die der NSG zugeordnet sind.
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

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Eine NSG kann nicht gelöscht werden, wenn NICs nicht an die laufende VM angefügt sind.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Beim Aufheben der Zuordnung einer NSG und einer NIC, die nicht an eine laufende VM angefügt ist, schlägt der Updatevorgang (PUT) für dieses Objekt auf der Ebene des Netzwerkcontrollers fehl. Die NSG wird auf der Ebene des Netzwerkressourcenanbieters aktualisiert, aber nicht auf dem Netzwerkcontroller, sodass die NSG in einen fehlerhaften Zustand wechselt.
- Abhilfe: Fügen Sie die NICs an, die der NSG zugeordnet sind, die mit laufenden VMs entfernt werden muss, und heben Sie die Zuordnung der NSG auf, oder entfernen Sie alle NICs, die der NSG zugeordnet sind.
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

### <a name="issues-creating-resources"></a>Probleme beim Erstellen von Ressourcen

- Geltungsbereich: Dieses Problem gilt für Release 1906.
- Ursache: 1906 weist ein bekanntes Problem mit der Zuordnung von benutzerdefinierten Rollen und Berechtigungen für die Ressourcenerstellung auf. Beim Erstellen von Ressourcen treten möglicherweise Probleme auf, auch wenn Sie über die richtigen Berechtigungen verfügen.
- Abhilfe: Aktualisieren Sie auf Build 1907, um dieses Problem zu beheben.
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

::: moniker range=">=azs-1907"
## <a name="archive"></a>Archivieren

Um auf archivierte bekannte Probleme für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>1906 – archivierte bekannte Probleme
::: moniker-end
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

::: moniker range="<azs-1907"
Sie können auf [ältere Versionen der bekannten Probleme in Azure Stack Hub im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack Hub-Support finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end
