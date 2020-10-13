---
title: Vor dem Bereitstellen von Azure Stack HCI
description: Hier finden Sie Informationen zum Vorbereiten der Bereitstellung von Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/01/2020
ms.openlocfilehash: 8a4c8557fe708535bfdde383ef30dd78395b1c01
ms.sourcegitcommit: 09572e1442c96a5a1c52fac8ee6b0395e42ab77d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91625871"
---
# <a name="before-you-deploy-azure-stack-hci"></a>Vor dem Bereitstellen von Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2

In dieser Schrittanleitung erfahren Sie Folgendes:

- Ermitteln, ob Ihre Hardware die grundlegenden Anforderungen für einen Azure Stack HCI-Standardcluster (ein Standort) oder einen Azure Stack HCI-Stretchingcluster (zwei Standorte) erfüllt
- Sicherstellen, dass die maximal unterstützten Hardwarespezifikationen nicht überschritten werden
- Erfassen der erforderlichen Informationen für eine erfolgreiche Bereitstellung
- Installieren von Windows Admin Center auf einem Verwaltungscomputer oder Server

Informationen zu den Anforderungen an Azure Kubernetes Service in Azure Stack HCI finden Sie unter [Was Sie für die ersten Schritte benötigen](../../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="determine-hardware-requirements"></a>Ermitteln der Hardwareanforderungen

Microsoft empfiehlt den Erwerb einer validierten Azure Stack HCI-Hardware-/Softwarelösung von unseren Partnern. Diese Lösungen wurden anhand unserer Referenzarchitektur entworfen, zusammengestellt und validiert und garantieren Kompatibilität und Zuverlässigkeit, sodass Sie sofort loslegen können. Überprüfen Sie, ob die von Ihnen verwendeten Systeme, Komponenten, Geräte und Treiber gemäß Windows Server Catalog für Windows Server 2019 zertifiziert sind. Auf der Website [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) finden Sie validierte Lösungen.

### <a name="server-requirements"></a>Serveranforderungen

- Ein Azure Stack HCI-Standardcluster erfordert mindestens zwei und höchstens 16 Server. Cluster können jedoch mithilfe von Clustergruppen kombiniert werden, um eine HCI-Plattform mit Hunderten von Knoten zu erstellen.
- Für Stretchingcluster müssen Server an zwei separaten Standorten bereitgestellt werden. Die Standorte können sich in verschiedenen Ländern, in verschiedenen Städten, auf verschiedenen Etagen oder in verschiedenen Räumen befinden. Ein Stretchingcluster erfordert mindestens vier Server (zwei pro Standort) und höchstens 16 Server (acht pro Standort).
- Es wird empfohlen, dass alle Server vom selben Hersteller stammen, dasselbe Modell aufweisen und 64-Bit-Prozessoren der Intel Nehalem-Architektur oder der AMD EPYC-Architektur oder kompatible Prozessoren mit höherer Spezifikation und SLAT (Second-Level Address Translation, Adressübersetzung der zweiten Ebene) verwenden. Zur Unterstützung des persistenten Intel Optane DC-Arbeitsspeichers ist ein skalierbarer Intel Xeon-Prozessor der zweiten Generation erforderlich. Die Prozessoren müssen mit mindestens 1,4 GHz getaktet sein und mit dem Anweisungssatz für x64 kompatibel sein.
- Stellen Sie sicher, dass die Server mit mindestens 32 GB RAM pro Knoten ausgestattet sind, um Serverbetriebssystem, VMs und weitere Apps oder Workloads zu unterstützen. Stellen Sie außerdem auf jedem Server 4 GB RAM pro Terabyte (TB) an Cachelaufwerkskapazität für die Metadaten von „Direkte Speicherplätze“ bereit.
- Vergewissern Sie sich, dass die Virtualisierungsunterstützung im BIOS oder UEFI aktiviert ist:
    - Hardwaregestützte Virtualisierung. Diese ist für Prozessoren mit Virtualisierungsoption verfügbar, insbesondere für Prozessoren mit Intel Virtualization Technology (Intel VT) oder AMD Virtualization Technology (AMD-V).
    - Die auf der Hardware erzwungene Datenausführungsverhinderung (Data Execution Protection, DEP) muss verfügbar und aktiviert sein. Bei Intel-Systemen ist dies das XD-Bit (Execute Disable-Bit). Bei AMD-Systemen ist dies das NX-Bit (No Execute-Bit).
- Sie können jedes von Windows Server unterstützte Startgerät verwenden. Dazu gehört jetzt auch [SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). RAID 1-Spiegelung ist **nicht** erforderlich, wird aber für den Systemstart unterstützt. Eine Mindestgröße von 200 GB wird empfohlen.
- Weitere featurespezifische Anforderungen für Hyper-V finden Sie unter [Systemanforderungen für Hyper-V unter Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

### <a name="networking-requirements"></a>Netzwerkanforderungen

Ein Azure Stack HCI-Cluster erfordert eine Netzwerkverbindung mit hoher und zuverlässiger Bandbreite sowie geringer Latenz zwischen den einzelnen Serverknoten. Überprüfen Sie Folgendes:

- Vergewissern Sie sich, dass mindestens ein Netzwerkadapter verfügbar und für die Clusterverwaltung dediziert ist.
- Vergewissern Sie sich, dass physische Switches in Ihrem Netzwerk so konfiguriert sind, dass sie Datenverkehr für beliebige VLANs zulassen, die Sie verwenden möchten.

Zwischen den Serverknoten finden unterschiedliche Arten von Kommunikation statt:

- Clusterkommunikation (Knotenbeitritte, Clusterupdates, Registrierungsupdates)
- Taktinformationen des Clusters
- Über freigegebenes Clustervolume umgeleiteter Datenverkehr
- Livemigrations-Datenverkehr für virtuelle Computer

Beim Feature „Direkte Speicherplätze“ muss zusätzlicher Datenverkehr eingerechnet werden:

- Speicherbusebene – Erweiterungen oder Daten zwischen den Knoten
- Integrität – Überwachen und Verwalten von Objekten (Knoten, Laufwerke, Netzwerkkarten, Clusterdienst)

Bei Stretchingclustern tritt zwischen den Standorten zusätzlicher Datenverkehr durch Speicherreplikate auf. Datenverkehr für die Speicherbusebene und das freigegebene Clustervolume tritt nicht zwischen Standorten, sondern nur zwischen Serverknoten innerhalb der einzelnen Standorte auf.

### <a name="software-defined-networking-requirements"></a>Softwaredefinierte Netzwerkanforderungen

Wenn Sie einen Azure Stack HCI-Cluster mithilfe von Windows Admin Center erstellen, haben Sie die Möglichkeit, Netzwerkcontroller bereitzustellen, um softwaredefinierte Netzwerke zu aktivieren. Wenn Sie beabsichtigen, softwaredefinierte Netzwerke für Azure Stack HCI zu verwenden:

- Stellen Sie sicher, dass die Hostserver über mindestens 50-100 GB freien Speicherplatz verfügen, um die Netzwerkcontroller-VMs zu erstellen.

- Sie müssen eine virtuelle Festplatte (VHD) des Azure Stack HCI-Betriebssystems auf den ersten Knoten im Cluster kopieren, um die Netzwerkcontroller-VMs zu erstellen. Sie können die VHD mithilfe von [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) oder durch Ausführen des Skripts [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) vorbereiten, um eine .ISO-Datei in eine VHD zu konvertieren.

Weitere Informationen zum Vorbereiten der Verwendung von softwaredefinierten Netzwerken in Azure Stack HCI finden Sie unter [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md) und [Planen der Bereitstellung von Netzwerkcontrollern](../concepts/network-controller.md).

### <a name="domain-requirements"></a>Domänenanforderungen

Für Azure Stack HCI gelten keine speziellen Domänenanforderungen auf Funktionsebene – Sie benötigen einfach nur eine unterstützte Betriebssystemversion für Ihren Domänencontroller. Als allgemeine Best Practice wird empfohlen, das Papierkorbfeature von Active Directory zu aktivieren, falls Sie dies noch nicht getan haben.

### <a name="interconnect-requirements-between-nodes"></a>Anforderung an Verbindungen zwischen Knoten

In diesem Abschnitt werden spezifische Netzwerkanforderungen für Verbindungen zwischen Serverknoten an einem Standort beschrieben. Knotenverbindungen mit oder ohne Switches können verwendet werden und werden unterstützt:

- **Mit Switches**: Serverknoten sind in der Regel über Ethernet-Netzwerke miteinander verbunden, die Netzwerkswitches verwenden. Die Switches müssen ordnungsgemäß konfiguriert sein, um die Bandbreite und den Netzwerktyp richtig zu verarbeiten. Wenn Sie das RDMA-Feature (Remote Direct Memory Access, Remotezugriff auf den direkten Speicher) verwenden, das das RoCE-Protokoll implementiert, ist die richtige Konfiguration von Netzwerkgerät und Switch von besonderer Bedeutung.
- **Ohne Switches**: Serverknoten können auch über direkte Ethernet-Verbindungen ohne Switch miteinander verbunden werden. In diesem Fall muss jeder Serverknoten über eine direkte Verbindung mit jedem anderen Clusterknoten am selben Standort verfügen.

#### <a name="interconnects-for-2-3-node-clusters"></a>Verbindungen für Cluster mit zwei bis drei Knoten

Dies sind die *Mindestanforderungen* für Verbindungen in Clustern an einem Standort mit zwei oder drei Knoten. Diese gelten für jeden einzelnen Serverknoten:

- Mindestens eine Netzwerkadapterkarte mit 1 Gbit/s für Verwaltungsfunktionen.
- Mindestens eine Netzwerkschnittstellenkarte mit 10 Gbit/s (oder schneller) für Speicher- und Workloaddatenverkehr.
- Für bessere Redundanz und Leistung werden mindestens zwei Netzwerkverbindungen zwischen den einzelnen Knoten empfohlen.

#### <a name="interconnects-for-4-node-and-greater-clusters"></a>Verbindungen für Cluster mit vier oder mehr Knoten

Dies sind die *Mindestanforderungen* für Verbindungen in Clustern mit vier oder mehr Knoten sowie für Hochleistungscluster. Diese gelten für jeden einzelnen Serverknoten:

- Mindestens eine Netzwerkadapterkarte mit 1 Gbit/s für Verwaltungsfunktionen.
- Mindestens eine Netzwerkschnittstellenkarte mit 25 Gbit/s (oder schneller) für Speicher- und Workloaddatenverkehr. Für bessere Redundanz und Leistung werden mindestens zwei Netzwerkverbindungen empfohlen.
- RDMA-fähige Netzwerkkarten: iWARP (empfohlen) oder RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Anforderungen an Site-to-Site-Cluster (Stretchingcluster)

Bei Verbindungen zwischen Standorten mit Stretchingclustern gelten weiterhin die Verbindungsanforderungen innerhalb jedes Standorts. Darüber hinaus müssen zusätzliche Anforderungen für den Datenverkehr durch Speicherreplikate und die Hyper-V-Livemigration berücksichtigt werden:

- Mindestens eine RDMA- oder Ethernet/TCP-Verbindung mit 1 Gbit/s zwischen den Standorten für die synchrone Replikation. Eine RDMA-Verbindung mit 25 Gbit/s wird bevorzugt.
- Ein Netzwerk zwischen den Standorten, das genügend Bandbreite für die Workloads von E/A-Schreibvorgängen sowie eine Roundtriplatenz von durchschnittlich 5 ms oder weniger für die synchrone Replikation bietet. Für die asynchrone Replikation gelten keine Empfehlungen in Bezug auf die Latenz.
- Wenn Sie eine einzelne Verbindung zwischen Ihren Standorten verwenden, legen Sie über PowerShell SMB-Bandbreitenlimits für Speicherreplikate fest. Weitere Informationen finden Sie unter [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Wenn Sie mehrere Verbindungen zwischen Standorten verwenden, trennen Sie den Datenverkehr zwischen den Verbindungen. Leiten Sie beispielsweise über PowerShell den Datenverkehr für Speicherreplikate über ein anderes Netzwerk als den Datenverkehr für die Hyper-V-Livemigration. Weitere Informationen finden Sie unter [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

### <a name="network-port-requirements"></a>Anforderungen an Netzwerkports

Stellen Sie sicher, dass zwischen allen Serverknoten innerhalb eines Standorts und zwischen Standorten (bei Stretchingclustern) die richtigen Netzwerkports geöffnet sind. Sie benötigen geeignete Firewall- und Routerregeln zum Zulassen von ICMP- und SMB-Datenverkehr (Port 445 plus Port 5445 für SMB Direct) sowie von bidirektionalem WS-MAN-Datenverkehr (Port 5985) zwischen allen Servern im Cluster.

Wenn Sie zum Erstellen des Clusters den Assistenten für die Clustererstellung in Windows Admin Center verwenden, öffnet dieser automatisch auf jedem Server im Cluster die entsprechenden Firewallports für Failoverclustering, Hyper-V und Speicherreplikate. Wenn Sie auf den einzelnen Servern unterschiedliche Softwarefirewalls verwenden, öffnen Sie die folgenden Ports:

#### <a name="failover-clustering-ports"></a>Ports für Failoverclustering

- ICMPv4 und ICMPv6
- TCP-Port 445
- Dynamische RPC-Ports
- TCP-Port 135
- TCP-Port 137
- TCP-Port 3343
- UDP-Port 3343

#### <a name="hyper-v-ports"></a>Hyper-V-Ports

- TCP-Port 135
- TCP-Port 80 (HTTP-Konnektivität)
- TCP-Port 443 (HTTPS-Konnektivität)
- TCP-Port 6600
- TCP-Port 2179
- Dynamische RPC-Ports
- RPC-Endpunktzuordnung
- TCP-Port 445

#### <a name="storage-replica-ports-stretched-cluster"></a>Ports für Speicherreplikate (Stretchingcluster)

- TCP-Port 445
- TCP 5445 (bei Verwendung von RDMA über iWARP)
- TCP-Port 5985
- ICMPv4 und ICMPv6 (bei Verwendung von Test-SRTopology)

Möglicherweise müssen weitere Ports geöffnet werden, die in dieser Liste nicht aufgeführt sind. Dies sind die Ports für die grundlegende Azure Stack HCI-Funktionalität.

### <a name="network-switch-requirements"></a>Anforderungen an Netzwerkswitches

In diesem Abschnitt werden die Anforderungen für physische Switches definiert, die mit Azure Stack HCI verwendet werden. Die Anforderungen umfassen die Branchenspezifikationen, die Organisationsstandards und die Protokolle, die für alle Azure Stack HCI-Bereitstellungen obligatorisch sind. Sofern nicht anders angegeben, wird die aktuelle aktive (nicht abgelöste) Version des Standards benötigt.

Diese Anforderungen tragen dazu bei, eine zuverlässige Kommunikation zwischen Knoten in Azure Stack HCI-Clusterbereitstellungen zu gewährleisten. Eine zuverlässige Kommunikation zwischen Knoten ist unverzichtbar. Um das erforderliche Maß an Zuverlässigkeit für Azure Stack HCI zu erreichen, müssen Switches folgende Anforderungen erfüllen:

- Erfüllung der relevanten Branchenspezifikationen, Standards und Protokolle
- Transparenz hinsichtlich der unterstützten Spezifikationen, Standards und Protokolle des Switchs
- Bereitstellung von Informationen zu den aktiven Funktionen

Erkundigen Sie sich beim Hersteller des Switchs, ob Ihr Switch Folgendes unterstützt:

#### <a name="standard-ieee-8021q"></a>Standard: IEEE 802.1Q

Ethernet-Switches müssen der IEEE 802.1Q-Spezifikation entsprechen, die zum Definieren von VLANs dient. VLANs sind für verschiedene Aspekte von Azure Stack HCI erforderlich und werden in allen Szenarien benötigt.

#### <a name="standard-ieee-8021qbb"></a>Standard: IEEE 802.1Qbb

Ethernet-Switches müssen der IEEE 802.1Qbb-Spezifikation entsprechen, die zum Definieren der Prioritätsflusssteuerung (Priority Flow Control, PFC) dient. PFC ist bei Verwendung von Data Center Bridging (DCB) erforderlich. Da DCB sowohl in RoCE- als auch in iWARP RDMA-Szenarien verwendet werden kann, ist 802.1Qbb in allen Szenarien erforderlich. Es werden mindestens drei CoS-Prioritäten (Class of Service, Dienstklasse) benötigt, ohne die Switchfunktionen oder die Portgeschwindigkeit herabzustufen.

#### <a name="standard-ieee-8021qaz"></a>Standard: IEEE 802.1Qaz

Ethernet-Switches müssen der IEEE 802.1Qaz-Spezifikation entsprechen, die zum Definieren der erweiterten Übertragungsauswahl (Enhanced Transmission Selection, ETS) dient. ETS ist bei Verwendung von DCB erforderlich. Da DCB sowohl in RoCE- als auch in iWARP RDMA-Szenarien verwendet werden kann, ist 802.1Qaz in allen Szenarien erforderlich. Es werden mindestens drei CoS-Prioritäten benötigt, ohne die Switchfunktionen oder die Portgeschwindigkeit herabzustufen.

#### <a name="standard-ieee-8021ab"></a>Standard: IEEE 802.1AB

Ethernet-Switches müssen der IEEE 802.1AB-Spezifikation entsprechen, die zum Definieren des Verbindungsschichterkennungsprotokolls (Link Layer Discovery Protocol, LLDP) dient. LLDP wird von Windows benötigt, um die Switchkonfiguration zu ermitteln. Die Konfiguration der LLDP-TLVs (Type-Length-Values) muss dynamisch aktiviert werden. Diese Switches dürfen keine zusätzliche Konfiguration erfordern.

Wenn also beispielsweise der Untertyp 3 von 802.1 aktiviert wird, müssen automatisch alle VLANs angekündigt werden, die an Switchports verfügbar sind.

#### <a name="tlv-requirements"></a>TLV-Anforderungen

Mit LLDP können Organisationen eigene benutzerdefinierte TLVs definieren und codieren. Diese werden als organisationsspezifische TLVs bezeichnet. Alle organisationsspezifischen TLVs beginnen mit dem LLDP-TLV-Typwert 127. Der folgenden Tabelle können Sie entnehmen, welche organisationsspezifischen benutzerdefinierten TLV-Untertypen (TLV-Typ 127) erforderlich und welche optional sind:

|Bedingung|Organization|TLV-Untertyp|
|-|-|-|
|Erforderlich|IEEE 802.1|VLAN-Name (Untertyp = 3)|
|Erforderlich|IEEE 802.3|Maximale Framegröße (Untertyp = 4)|
|Optional|IEEE 802.1|Port-VLAN-ID (Untertyp = 1)|
|Optional|IEEE 802.1|Port- und Protokoll-VLAN-ID (Untertyp = 2)|
|Optional|IEEE 802.1|Link-Aggregation (Untertyp = 7)|
|Optional|IEEE 802.1|Überlastungsbenachrichtigung (Untertyp = 8)|
|Optional|IEEE 802.1|ETS-Konfiguration (Untertyp = 9)|
|Optional|IEEE 802.1|ETS-Empfehlung (Untertyp = A)|
|Optional|IEEE 802.1|PFC-Konfiguration (Untertyp = B)|
|Optional|IEEE 802.1|EVB (Untertyp = D)|
|Optional|IEEE 802.3|Link-Aggregation (Untertyp = 3)|

> [!NOTE]
> Einige der aufgeführten optionalen Features werden künftig möglicherweise erforderlich.

### <a name="storage-requirements"></a>Speicheranforderungen

- Azure Stack HCI arbeitet mit direkt verbundenen SATA-, SAS- oder NVME-Laufwerken oder Laufwerken für persistenten Speicher, die physisch an nur jeweils einen Server angeschlossen sind.
- Jeder Server im Cluster sollte über dieselben Laufwerkstypen und dieselbe Anzahl von Laufwerken des jeweiligen Typs verfügen. Es wird auch empfohlen (aber nicht vorgeschrieben), dass die Laufwerke dieselbe Größe und dasselbe Modell besitzen. Laufwerke können intern im Server installiert sein oder sich in einem externen Gehäuse befinden, das nur mit einem Server verbunden ist. Weitere Informationen finden Sie unter [Überlegungen zur Laufwerkssymmetrie](../concepts/drive-symmetry-considerations.md).
- Jeder Server im Cluster muss über dedizierte Volumes für Protokolle verfügen, und der Protokollspeicher muss mindestens so schnell sein wie der Datenspeicher. Stretchingcluster erfordern mindestens zwei Volumes: eins für replizierte Daten und eins für Protokolldaten.
- SES (SCSI Enclosure Services) ist für die Zuordnung und Identifikation von Slots erforderlich. Jedes externe Gehäuse muss einen eindeutigen Bezeichner (eindeutige ID) besitzen. **NICHT UNTERSTÜTZT**: RAID-Controllerkarten oder SAN-Speicher (Fibre Channel, iSCSI, FCoE), mit mehreren Servern verbundene, gemeinsam genutzte SAS-Gehäuse und jegliche Art von Multipfad-E/A (Multi-Path IO, MPIO), bei dem über mehrere Pfade auf Laufwerke zugegriffen werden kann. Hostbusadapterkarten müssen einen einfachen Pass-Through-Modus implementieren.
- Weitere Informationen dazu finden Sie im Thema [Auswählen von Laufwerken](../concepts/choose-drives.md) oder unter [Hardwareanforderungen für „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements).
- Verfügbare Optionen für Volumes und Resilienz finden Sie im Thema [Planen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).
- Wenn Sie auch virtuelle Computer und virtualisierten Speicher bereitstellen, finden Sie weitere Informationen unter [Verwenden von „Direkte Speicherplätze“ in Clustern mit virtuellen Gastcomputern](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm).

## <a name="review-maximum-supported-hardware-specifications"></a>Überprüfen der maximal unterstützten Hardwarespezifikationen

Azure Stack HCI-Bereitstellungen, die folgende Spezifikationen überschreiten, werden nicht unterstützt:

| Ressource                     | Maximum |
| ---------------------------- | --------|
| Physische Server pro Cluster | 16      |
| VMs pro Host                 | 1\.024   |
| Datenträger pro VM (SCSI)          | 256     |
| Speicher pro Cluster          | 4 PB    |
| Speicher pro Server           | 400 TB  |
| Logische Prozessoren pro Host  | 512     |
| RAM pro Host                 | 24 TB   |
| RAM pro VM                   | 12 TB (VM der 2. Generation) oder 1 TB (1. Generation)|
| Virtuelle Prozessoren pro Host  | 2\.048   |
| Virtuelle Prozessoren pro VM    | 240 (VM der 2. Generation) oder 64 (1. Generation)|

## <a name="gather-information"></a>Sammeln von Informationen

Sammeln Sie zur Vorbereitung der Bereitstellung die folgenden Informationen zu Ihrer Umgebung:

- **Servernamen**: Machen Sie sich mit den Benennungsrichtlinien Ihrer Organisation für Computer, Dateien, Pfade und andere Ressourcen vertraut. Sie werden mehrere Server bereitstellen müssen, von denen jeder einen eindeutigen Namen erhalten muss.
- **Domänenname**: Machen Sie sich mit den Benennungsrichtlinien Ihrer Organisation für die Domänenbenennung und den Domänenbeitritt vertraut. Sie werden die Server in Ihre Domäne einbinden und müssen den Domänennamen angeben.
- **Statische IP-Adressen**: Azure Stack HCI erfordert statische IP-Adressen für den Speicher- und Workloaddatenverkehr (VM) und unterstützt keine dynamische Zuweisung von IP-Adressen durch DHCP für dieses Hochgeschwindigkeitsnetzwerk. Sie können DHCP für den Verwaltungsnetzwerkadapter verwenden. Falls Sie jedoch zwei Adapter in einer Teamkonfiguration einsetzen, müssen Sie wieder statische IP-Adressen verwenden. Fragen Sie Ihren Netzwerkadministrator nach den IP-Adressen, die Sie für die einzelnen Server im Cluster verwenden sollten.
- **RDMA-Netzwerkfunktionen**: Es gibt zwei Arten von RDMA-Protokollen: iWARP und RoCE. Informieren Sie sich darüber, welches Protokoll Ihre Netzwerkadapter verwenden. Wenn es sich um RoCE handelt, beachten Sie auch die Version (v1 oder v2). Im Fall von RoCE müssen Sie auch das Modell Ihres Top-of-Rack-Switchs beachten.
- **VLAN-ID**: Informieren Sie sich über die VLAN-ID, die für die Netzwerkserver auf den Servern verwendet werden soll (sofern vorhanden). Diese Informationen sollten Sie von Ihrem Netzwerkadministrator erhalten.
- **Standortnamen:** Bei Stretchingclustern werden zwei Standorte für die Notfallwiederherstellung verwendet. Standorte können mithilfe von Active Directory Domain Services oder automatisch vom Clustererstellungs-Assistenten eingerichtet werden. Wenden Sie sich im Zusammenhang mit der Einrichtung von Standorten an Ihren Domänenadministrator. Weitere Informationen finden Sie unter [Active Directory Domain Services – Übersicht](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="install-windows-admin-center"></a>Installieren von Windows Admin Center

Windows Admin Center ist eine lokal bereitgestellte, browserbasierte App zum Verwalten von Azure Stack HCI. Die einfachste Möglichkeit zum [Installieren von Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) ist die Verwendung eines lokalen Verwaltungscomputers (Desktopmodus). Die Installation auf einem Server (Dienstmodus) ist jedoch ebenfalls möglich.

Bei der Installation von Windows Admin Center auf einem Server muss für Aufgaben, die CredSSP erfordern (etwa Clustererstellung sowie Installation von Updates und Erweiterungen), ein Konto verwendet werden, das der Gatewayadministratorgruppe auf dem Windows Admin Center-Server angehört. Weitere Informationen finden Sie in den ersten beiden Abschnitten des Artikels [Konfigurieren der Benutzerzugriffssteuerung und von Berechtigungen](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um zu erfahren, wie Sie das Azure Stack HCI-Betriebssystem bereitstellen.
> [!div class="nextstepaction"]
> [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md)
