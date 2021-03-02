---
title: Azure Stack Hub – Bekannte Probleme
description: Enthält Informationen zu bekannten Problemen in Releases von Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/28/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 220569fd3763654f69809cc8316aca2a54e3f7ef
ms.sourcegitcommit: af2bec84471795c0f3ac62dcaf1347a64e529906
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554146"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack Hub – Bekannte Probleme

In diesem Artikel sind die bekannten Probleme aufgelistet, die es in Azure Stack Hub-Releases gibt. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

Um auf bekannte Probleme für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2008"
## <a name="update"></a>Aktualisieren

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Problembehandlung von Azure Stack Hub-Updates](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Fehler bei der Installation des Pakets „Microsoft.AzureStack.Compute.Installer“ auf der Zertifizierungsstellen-VM durch das Update

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Während des Updates richtet ein Prozess eine Sperre für neue Inhalte ein, die auf die Zertifizierungsstellen-VM kopiert werden müssen. Die Sperre wird aufgehoben, wenn das Update fehlschlägt.
- Abhilfe: Fahren Sie mit dem Update fort.
- Häufigkeit: Selten

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Die DenyAllOutbound-Regel kann nicht erstellt werden.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der VM-Erstellung kann in einer NSG keine explizite Regel vom Typ **DenyAllOutbound** für das Internet erstellt werden, da dadurch die erforderliche Kommunikation für den Abschluss der VM-Bereitstellung verhindert wird. Außerdem werden die beiden wichtigen IP-Adressen verweigert, die für die Bereitstellung von VMs erforderlich sind: DHCP-IP: 169.254.169.254 und DNS-IP: 168.63.129.16

- Abhilfe: Lassen Sie bei der VM-Erstellung ausgehenden Datenverkehr für das Internet zu, und ändern Sie die NSG nach Abschluss der VM-Erstellung, um den erforderlichen Datenverkehr zu blockieren.
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

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer leitet Datenverkehr in bestimmten Szenarien an eine Back-End-VM weiter

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Beim Aktivieren der **Sitzungsaffinität** in einem Lastenausgleich verwendet der 2-Tupel-Hash die PA-IP-Adresse (physische IP-Adresse) anstelle der privaten IP-Adressen, die den VMs zugewiesen sind. In Szenarien, in denen Datenverkehr, der an den Lastenausgleich weitergeleitet wird, über ein VPN eingeht oder alle virtuellen Clientcomputer (Quell-IPs) sich im gleichen Knoten befinden und die Sitzungsaffinität aktiviert ist, wird der gesamte Datenverkehr an eine Back-End-VM geleitet.
- Häufigkeit: Allgemein

#### <a name="ipv6-button-visible-in-frontend-ip-configuration"></a>IPv6-Schaltfläche sichtbar in Front-End-IP-Konfiguration

- Geltungsbereich: Dieses Problem gilt für Release 2008.
- Ursache: Die IPv6-Schaltfläche ist sichtbar und aktiviert, wenn die Front-End-IP-Konfiguration eines öffentlichen Lastenausgleichsmoduls erstellt wird. Hierbei handelt es sich lediglich um ein kosmetisches Problem im Portal. IPv6 wird unter Azure Stack Hub nicht unterstützt.
- Häufigkeit: Allgemein

#### <a name="backend-port-and-frontend-port-need-to-be-the-same-when-floating-ip-is-enabled"></a>Der Back-End- und der Front-End-Port müssen identisch sein, wenn Floating IP aktiviert ist.

- Geltungsbereich: Dieses Problem betrifft alle Releases. 
- Ursache: Wenn Floating IP aktiviert ist, müssen der Front-End- und der Back-End-Port in der Lastenausgleichsregel identisch sein. Dies ist beabsichtigt.
- Häufigkeit: Allgemein

<!-- ## Compute -->

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2005"
## <a name="update"></a>Aktualisieren

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Problembehandlung von Azure Stack Hub-Updates](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Fehler bei der Installation des Pakets „Microsoft.AzureStack.Compute.Installer“ auf der Zertifizierungsstellen-VM durch das Update

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Während des Updates richtet ein Prozess eine Sperre für neue Inhalte ein, die auf die Zertifizierungsstellen-VM kopiert werden müssen. Die Sperre wird aufgehoben, wenn das Update fehlschlägt.
- Abhilfe: Fahren Sie mit dem Update fort.
- Häufigkeit: Selten

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

#### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Eine NSG kann nicht gelöscht werden, wenn NICs nicht an die laufende VM angefügt sind.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Beim Aufheben der Zuordnung einer NSG und einer NIC, die nicht an eine laufende VM angefügt ist, schlägt der Updatevorgang (PUT) für dieses Objekt auf der Ebene des Netzwerkcontrollers fehl. Die NSG wird auf der Ebene des Netzwerkressourcenanbieters aktualisiert, aber nicht auf dem Netzwerkcontroller, sodass die NSG in einen fehlerhaften Zustand wechselt.
- Abhilfe: Fügen Sie die NICs an, die der NSG zugeordnet sind, die mit laufenden VMs entfernt werden muss, und heben Sie die Zuordnung der NSG auf, oder entfernen Sie alle NICs, die der NSG zugeordnet sind.
- Häufigkeit: Allgemein

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Die DenyAllOutbound-Regel kann nicht erstellt werden.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der VM-Erstellung kann in einer NSG keine explizite Regel vom Typ **DenyAllOutbound** für das Internet erstellt werden, da dadurch die erforderliche Kommunikation für den Abschluss der VM-Bereitstellung verhindert wird.
- Abhilfe: Lassen Sie bei der VM-Erstellung ausgehenden Datenverkehr für das Internet zu, und ändern Sie die NSG nach Abschluss der VM-Erstellung, um den erforderlichen Datenverkehr zu blockieren.
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
  
### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer leitet Datenverkehr in bestimmten Szenarien an eine Back-End-VM weiter

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Beim Aktivieren der **Sitzungsaffinität** in einem Lastenausgleich verwendet der 2-Tupel-Hash die PA-IP-Adresse (physische IP-Adresse) anstelle der privaten IP-Adressen, die den VMs zugewiesen sind. In Szenarien, in denen Datenverkehr, der an den Lastenausgleich weitergeleitet wird, über ein VPN eingeht oder alle virtuellen Clientcomputer (Quell-IPs) sich im gleichen Knoten befinden und die Sitzungsaffinität aktiviert ist, wird der gesamte Datenverkehr an eine Back-End-VM geleitet.
- Häufigkeit: Allgemein

#### <a name="public-ip"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Der Wert **IdleTimeoutInMinutes** für eine öffentliche IP-Adresse, die einem Lastenausgleich zugeordnet ist, kann nicht geändert werden. Bei diesem Vorgang wird die öffentliche IP-Adresse in einen Fehlerzustand versetzt.
- Abhilfe: Gehen Sie wie folgt vor, um die öffentliche IP-Adresse wieder in den korrekten Zustand zu versetzen: Legen Sie den Wert **IdleTimeoutInMinutes** für die Lastenausgleichsregel, in der auf die öffentliche IP-Adresse verwiesen wird, wieder auf den ursprünglichen Wert fest (Standardwert: vier Minuten).
- Häufigkeit: Allgemein

## <a name="compute"></a>Compute

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>Probleme bei der Bereitstellung von VM-Skalierungsgruppen mit der Größe „Standard_DS2_v2“ über das Portal

- Geltungsbereich: Dieses Problem gilt für Release 2005.
- Ursache: Ein Problem im Portal führt dazu, dass bei der Erstellung einer Skalierungsgruppe mit der Größe „Standard_DS2_v2“ ein Fehler auftritt.
- Abhilfe: Verwenden Sie PowerShell oder die CLI zur Bereitstellung der VM-Größe dieser VM-Skalierungsgruppe.

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>Probleme bei Verwendung von VM-Erweiterungen in Ubuntu Server 20.04

- Geltungsbereich: Dieses Problem gilt für **Ubuntu Server 20.04 LTS**.
- Ursache: Einige Linux-Distributionen wurden auf Python 3.8 umgestellt, und der Legacy-Einstiegspunkt `/usr/bin/python` für Python wurde vollständig entfernt. Benutzer von Linux-Distributionen, die auf Python 3.x umgestellt haben, müssen sicherstellen, dass der Legacy-Einstiegspunkt `/usr/bin/python` vorhanden ist, bevor sie diese Erweiterungen auf ihren VMs bereitstellen. Andernfalls tritt bei der Bereitstellung der Erweiterung möglicherweise ein Fehler auf.
- Abhilfe: Befolgen Sie die Lösungsschritte in [Probleme bei Verwendung von VM-Erweiterungen in Python 3-fähigen Linux Azure Virtual Machines-Systemen](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3), aber überspringen Sie Schritt 2, da Azure Stack Hub nicht über die Funktion **Befehl ausführen** verfügt.

### <a name="nvv4-vm-size-on-portal"></a>NVv4-VM-Größe im Portal

- Geltungsbereich: Dieses Problem betrifft das Release 2002 und höhere Releases.
- Ursache: Bei der Erstellung des virtuellen Computers wird die VM-Größe angezeigt: NV4as_v4. Bei Kunden mit der erforderlichen Hardware für die AMD MI25-basierte Azure Stack Hub-GPU-Vorschau ist die VM-Bereitstellung erfolgreich. Bei allen anderen Kunden tritt bei der VM-Bereitstellung mit dieser VM-Größe ein Fehler auf.
- Abhilfe: Entwurfsbedingt bei der Vorbereitung für die Azure Stack Hub-GPU-Vorschau

### <a name="consumed-compute-quota"></a>Verbrauchtes Computekontingent

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Computer erstellen, wird unter Umständen eine Fehlermeldung wie die folgende angezeigt: **Dieses Abonnement hat die Kapazität für „Regionale vCPUs gesamt“ an diesem Standort erreicht. 50 „Regionale vCPUs gesamt“ sind verfügbar, und alle werden verwendet.** Dies deutet darauf hin, dass das Kontingent für die Gesamtanzahl verfügbarer Kerne erreicht wurde.
- Abhilfe: Erkundigen Sie sich bei Ihrem Betreiber nach einem Add-On-Plan mit zusätzlichem Kontingent. Das Kontingent des aktuellen Plans kann nicht bearbeitet werden, oder das höhere Kontingent wird nicht berücksichtigt.
- Häufigkeit: Selten

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Auf dem Blatt mit der VM-Übersicht wird nicht der richtige Computername angezeigt.

- Geltungsbereich: Dieses Problem betrifft alle Releases.
- Ursache: Wenn die Details eines virtuellen Computers auf dem Übersichtsblatt angezeigt werden, wird der Computername als **(nicht verfügbar)** angezeigt. Dies gilt dediziert für VMs, die anhand spezieller Datenträger bzw. Momentaufnahmen von Datenträgern erstellt werden, und wird auch für Marketplace-Images angezeigt.
- Abhilfe: Zeigen Sie das Blatt **Eigenschaften** unter **Einstellungen** an.

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Bereitstellung mit vier Knoten aber immer noch nicht verfügbar.

## <a name="storage"></a>Storage

### <a name="retention-period-reverts-to-0"></a>Aufbewahrungszeitraum wird auf „0“ zurückgesetzt

- Geltungsbereich: Dieses Problem betrifft die Releases 2002 und 2005.
- Ursache: Wenn Sie in der Einstellung für den Aufbewahrungszeitraum einen anderen Zeitraum als 0 angeben, wird der Wert beim Update 2002 oder 2005 auf „0“ (Standardwert für diese Einstellung) zurückgesetzt. Die Einstellung „0 Tage“ wird unmittelbar nach Abschluss des Updates wirksam, was dazu führt, dass alle vorhandenen gelöschten Speicherkonten und anstehende neu gelöschten Speicherkonten ab sofort nicht mehr aufbewahrt und für die regelmäßige automatische Speicherbereinigung gekennzeichnet werden, die stündlich ausgeführt wird.
- Abhilfe: Legen Sie manuell einen korrekten Aufbewahrungszeitraum fest. Alle Speicherkonten, die bereits vor der Festlegung des neuen Aufbewahrungszeitraums für die automatische Speicherbereinigung gekennzeichnet wurden, sind nicht wiederherstellbar.  

## <a name="resource-providers"></a>Ressourcenanbieter

### <a name="sqlmysql"></a>SQL/MySQL

- Geltungsbereich: Dieses Problem betrifft das Release 2002.
- Ursache: Wenn der Stempel die Version 1.1.33.0 oder eine frühere Version des SQL-Ressourcenanbieters enthält, werden die Blätter für SQL/MySQL bei der Aktualisierung des Stempels nicht geladen.
- Abhilfe: Aktualisieren Sie den Ressourcenanbieter auf die Version 1.1.47.0.

### <a name="app-service"></a>App Service

- Geltungsbereich: Dieses Problem betrifft das Release 2002.
- Ursache: Wenn der Stempel die Version 1.7 oder eine ältere Version des App Service-Ressourcenanbieters enthält, werden die Blätter für App Service bei der Aktualisierung des Stempels nicht geladen.
- Abhilfe: Aktualisieren Sie den Ressourcenanbieter auf die Version 2002 Q2.

## <a name="powershell"></a>PowerShell

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2002"
## <a name="update"></a>Aktualisieren

Nach dem Anwenden von Update 2002 wird im Administratorportal ggf. fälschlicherweise die Warnung „Ungültige Zeitquelle“ angezeigt. Sie können diese Warnung (False Positive) ignorieren. Der Fehler wird mit einem der nächsten Releases behoben. 

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Problembehandlung von Azure Stack Hub-Updates](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Fehler bei der Installation des Pakets „Microsoft.AzureStack.Compute.Installer“ auf der Zertifizierungsstellen-VM durch das Update

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Während des Updates richtet ein Prozess eine Sperre für neue Inhalte ein, die auf die Zertifizierungsstellen-VM kopiert werden müssen. Die Sperre wird aufgehoben, wenn das Update fehlschlägt.
- Abhilfe: Fahren Sie mit dem Update fort.
- Häufigkeit: Selten

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
- Ursache: Bei der VM-Erstellung kann in einer NSG keine explizite Regel vom Typ **DenyAllOutbound** für das Internet erstellt werden, da dadurch die erforderliche Kommunikation für den Abschluss der VM-Bereitstellung verhindert wird.
- Abhilfe: Lassen Sie bei der VM-Erstellung ausgehenden Datenverkehr für das Internet zu, und ändern Sie die NSG nach Abschluss der VM-Erstellung, um den erforderlichen Datenverkehr zu blockieren.
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

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>Es ist nicht möglich, eine VM-Skalierungsgruppe mit der VM-Größe „Standard_DS2_v2“ im Portal zu erstellen.

- Geltungsbereich: Dieses Problem gilt für Release 2002.
- Ursache: Es gibt einen Portalfehler, der die Erstellung der VM-Skalierungsgruppe mit der VM-Größe „Standard_DS2_v2“ verhindert. Beim Erstellen wird ein Fehler ausgegeben: "{"code":"DeploymentFailed","message":"Mindestens ein Ressourcenbereitstellungsvorgang ist fehlgeschlagen. Listen Sie die Bereitstellungsvorgänge auf, um Details anzuzeigen. Weitere Informationen zur Verwendung finden Sie unter https://aka.ms/arm-debug.","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\": \" NetworkProfileValidationError\" ,\r\n \" message\": \" Die VM-Größe "Standard_DS2_v2" ist nicht in der Liste zulässiger VM-Größen zur Aktivierung des beschleunigten Netzwerkbetriebs für die VM bei Index "0" für die VM-Skalierungsgruppe "/subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss" enthalten. Zulässige Größen: .\"\r\n }\r\n}"}]}"
- Abhilfe: Erstellen Sie eine VM-Skalierungsgruppe mit PowerShell oder einer Resource Manager-Vorlage.

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Auf dem Blatt mit der VM-Übersicht wird nicht der richtige Computername angezeigt.

- Geltungsbereich: Dieses Problem betrifft alle Releases.
- Ursache: Wenn die Details eines virtuellen Computers auf dem Übersichtsblatt angezeigt werden, wird der Computername als **(nicht verfügbar)** angezeigt. Dies gilt dediziert für VMs, die anhand spezieller Datenträger bzw. Momentaufnahmen von Datenträgern erstellt werden, und wird auch für Marketplace-Images angezeigt.
- Abhilfe: Zeigen Sie das Blatt **Eigenschaften** unter **Einstellungen** an.

### <a name="nvv4-vm-size-on-portal"></a>NVv4-VM-Größe im Portal

- Geltungsbereich: Dieses Problem gilt für Release 2002 und höher.
- Ursache: Bei der Erstellung des virtuellen Computers wird die VM-Größe angezeigt: NV4as_v4. Bei Kunden mit der erforderlichen Hardware für die AMD MI25-basierte Azure Stack Hub-GPU-Vorschau ist die VM-Bereitstellung erfolgreich. Bei allen anderen Kunden tritt bei der VM-Bereitstellung mit dieser VM-Größe ein Fehler auf.
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

## <a name="storage"></a>Storage

### <a name="retention-period-revert-to-0"></a>Aufbewahrungszeitraum wird auf „0“ zurückgesetzt

- Geltungsbereich: Dieses Problem betrifft die Releases 2002 und 2005.
- Ursache: Wenn Sie in der Einstellung für den Aufbewahrungszeitraum zuvor einen anderen Zeitraum als 0 angegeben haben, wird der Wert bei den Updates 2002 und 2005 auf „0“ (Standardwert für diese Einstellung) zurückgesetzt. Die Einstellung „0 Tage“ wird unmittelbar nach Abschluss des Updates wirksam, was dazu führt, dass alle vorhandenen gelöschten Speicherkonten und anstehende neu gelöschten Speicherkonten ab sofort nicht mehr aufbewahrt und für die regelmäßige automatische Speicherbereinigung gekennzeichnet werden, die stündlich ausgeführt wird. 
- Abhilfe: Legen Sie manuell einen geeigneten Aufbewahrungszeitraum fest. Alle Speicherkonten, die bereits vor der Festlegung des neuen Aufbewahrungszeitraums für die automatische Speicherbereinigung gekennzeichnet wurden, sind jedoch nicht wiederherstellbar.  

## <a name="resource-providers"></a>Ressourcenanbieter

### <a name="sqlmysql"></a>SQL/MySQL

- Geltungsbereich: Dieses Problem betrifft das Release 2002. 
- Ursache: Wenn der Stempel die Version 1.1.33.0 oder eine frühere Version des SQL-Ressourcenanbieters enthält, werden die Blätter für SQL/MySQL bei der Aktualisierung des Stempels nicht geladen.
- Abhilfe: Aktualisieren Sie den Ressourcenanbieter auf die Version 1.1.47.0.

### <a name="app-service"></a>App Service

- Geltungsbereich: Dieses Problem betrifft das Release 2002.
- Ursache: Wenn der Stempel die Version 1.7 oder eine ältere Version des App Service-Ressourcenanbieters enthält, werden die Blätter für App Service bei der Aktualisierung des Stempels nicht geladen.
- Abhilfe: Aktualisieren Sie den Ressourcenanbieter auf die Version [2020 Q2](azure-stack-app-service-update.md).

## <a name="powershell"></a>PowerShell

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-2002"
## <a name="archive"></a>Archivieren

Um auf archivierte bekannte Probleme für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-known-issues"></a>1910 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-known-issues"></a>1908 – archivierte bekannte Probleme
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-known-issues"></a>1907 – archivierte bekannte Probleme
::: moniker-end
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

::: moniker range="<azs-2002"
Sie können links im Inhaltsverzeichnis unter [**Ressourcen > Archiv der Versionshinweise**](./relnotearchive/known-issues-1910.md) auf ältere Versionen der bekannten Probleme für Azure Stack Hub zugreifen. Diese archivierten Artikel werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack Hub-Support finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end
