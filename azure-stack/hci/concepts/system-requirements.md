---
title: Systemanforderungen für Azure Stack HCI
description: In diesem Artikel erfahren Sie, wie Sie Server-, Speicher- und Netzwerkkomponenten für Azure Stack HCI auswählen.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/29/2020
ms.openlocfilehash: 68ee5d0635d1ed9dbc62bece417f00430bf61de2
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93065997"
---
# <a name="system-requirements-for-azure-stack-hci"></a>Systemanforderungen für Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2

Anhand dieses Artikels können Sie Systemanforderungen für Server, Speicher und Netzwerke für Azure Stack HCI bewerten.

## <a name="server-requirements"></a>Serveranforderungen

Ein Azure Stack HCI-Standardcluster erfordert mindestens zwei und höchstens 16 Server. Cluster können jedoch mithilfe von Clustergruppen kombiniert werden, um eine HCI-Plattform mit Hunderten von Knoten zu erstellen.

Beachten Sie Folgendes für verschiedene Typen von Azure Stack HCI-Bereitstellungen:

- Für Stretchingcluster müssen Server an zwei separaten Standorten bereitgestellt werden. Die Standorte können sich in verschiedenen Ländern, in verschiedenen Städten, auf verschiedenen Etagen oder in verschiedenen Räumen befinden. Ein Stretchingcluster erfordert mindestens vier Server (zwei pro Standort) und höchstens 16 Server (acht pro Standort).

- Es wird empfohlen, dass alle Server vom selben Hersteller stammen, dasselbe Modell aufweisen und 64-Bit-Prozessoren der Intel Nehalem-Architektur oder der AMD EPYC-Architektur oder kompatible Prozessoren mit höherer Spezifikation und SLAT (Second-Level Address Translation, Adressübersetzung der zweiten Ebene) verwenden. Zur Unterstützung des persistenten Intel Optane DC-Arbeitsspeichers ist ein skalierbarer Intel Xeon-Prozessor der zweiten Generation erforderlich. Die Prozessoren müssen mit mindestens 1,4 GHz getaktet sein und mit dem Anweisungssatz für x64 kompatibel sein.

- Stellen Sie sicher, dass die Server mit mindestens 32 GB RAM pro Knoten ausgestattet sind, um Serverbetriebssystem, VMs und weitere Apps oder Workloads zu unterstützen. Stellen Sie außerdem auf jedem Server 4 GB RAM pro Terabyte (TB) an Cachelaufwerkskapazität für die Metadaten von „Direkte Speicherplätze“ bereit.

- Vergewissern Sie sich, dass die Virtualisierungsunterstützung im BIOS oder UEFI aktiviert ist:
    - Hardwaregestützte Virtualisierung. Diese ist für Prozessoren mit Virtualisierungsoption verfügbar, insbesondere für Prozessoren mit Intel Virtualization Technology (Intel VT) oder AMD Virtualization Technology (AMD-V).
    - Die auf der Hardware erzwungene Datenausführungsverhinderung (Data Execution Protection, DEP) muss verfügbar und aktiviert sein. Bei Intel-Systemen ist dies das XD-Bit (Execute Disable-Bit). Bei AMD-Systemen ist dies das NX-Bit (No Execute-Bit).

- Sie können jedes von Windows Server unterstützte Startgerät verwenden. Dazu gehört jetzt auch [SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). RAID 1-Spiegelung ist **nicht** erforderlich, wird aber für den Systemstart unterstützt. Eine Mindestgröße von 200 GB wird empfohlen.

- Weitere featurespezifische Anforderungen für Hyper-V finden Sie unter [Systemanforderungen für Hyper-V unter Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

## <a name="storage-requirements"></a>Speicheranforderungen

Azure Stack HCI arbeitet mit direkt verbundenen SATA-, SAS- oder NVME-Laufwerken oder Laufwerken für persistenten Speicher, die physisch an nur jeweils einen Server angeschlossen sind.

Halten Sie sich an die folgenden Regeln, um optimale Ergebnisse zu erzielen:

- Jeder Server im Cluster sollte über dieselben Laufwerkstypen und dieselbe Anzahl von Laufwerken des jeweiligen Typs verfügen. Es wird auch empfohlen (aber nicht vorgeschrieben), dass die Laufwerke dieselbe Größe und dasselbe Modell besitzen. Laufwerke können intern im Server installiert sein oder sich in einem externen Gehäuse befinden, das nur mit einem Server verbunden ist. Weitere Informationen finden Sie unter [Überlegungen zur Laufwerkssymmetrie](drive-symmetry-considerations.md).

- Jeder Server im Cluster muss über dedizierte Volumes für Protokolle verfügen, und der Protokollspeicher muss mindestens so schnell sein wie der Datenspeicher. Stretchingcluster erfordern mindestens zwei Volumes: eins für replizierte Daten und eins für Protokolldaten.

- SES (SCSI Enclosure Services) ist für die Zuordnung und Identifikation von Slots erforderlich. Jedes externe Gehäuse muss einen eindeutigen Bezeichner (eindeutige ID) besitzen. 

   > [!IMPORTANT]
   > **NICHT UNTERSTÜTZT** : RAID-Controllerkarten oder SAN-Speicher (Fibre Channel, iSCSI, FCoE), mit mehreren Servern verbundene, gemeinsam genutzte SAS-Gehäuse und jegliche Art von Multipfad-E/A (Multi-Path IO, MPIO), bei dem über mehrere Pfade auf Laufwerke zugegriffen werden kann. Hostbusadapterkarten müssen einen einfachen Pass-Through-Modus implementieren.

## <a name="networking-requirements"></a>Netzwerkanforderungen

Ein Azure Stack HCI-Cluster erfordert eine Netzwerkverbindung mit hoher und zuverlässiger Bandbreite sowie geringer Latenz zwischen den einzelnen Serverknoten.

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

Informationen zu Überlegungen zur Planung von Hostnetzwerken und zu den Anforderungen finden Sie unter [Planen von Hostnetzwerken für Azure Stack HCI](plan-host-networking.md).

## <a name="software-defined-networking-sdn-requirements"></a>SDN-Anforderungen (Softwaredefiniertes Netzwerk)

Wenn Sie einen Azure Stack HCI-Cluster mithilfe von Windows Admin Center erstellen, haben Sie die Möglichkeit, Netzwerkcontroller bereitzustellen, um softwaredefinierte Netzwerke zu ermöglichen. Wenn Sie beabsichtigen, softwaredefinierte Netzwerke für Azure Stack HCI zu verwenden:

- Stellen Sie sicher, dass die Hostserver über mindestens 50-100 GB freien Speicherplatz verfügen, um die Netzwerkcontroller-VMs zu erstellen.

- Sie müssen eine virtuelle Festplatte (VHD) des Azure Stack HCI-Betriebssystems auf den ersten Knoten im Cluster kopieren, um die Netzwerkcontroller-VMs zu erstellen. Sie können die VHD mithilfe von [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) oder durch Ausführen des Skripts [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) vorbereiten, um eine .ISO-Datei in eine VHD zu konvertieren.

Weitere Informationen zum Vorbereiten der Verwendung von softwaredefinierten Netzwerken in Azure Stack HCI finden Sie unter [Planen einer softwaredefinierten Netzwerkinfrastruktur](plan-software-defined-networking-infrastructure.md) und [Planen der Bereitstellung von Netzwerkcontrollern](../concepts/network-controller.md).

   > [!NOTE]
   > SDN wird für Stretched Cluster (an mehreren Standorten) nicht unterstützt.

### <a name="sdn-hardware-requirements"></a>SDN-Hardwareanforderungen

In diesem Abschnitt finden Sie Netzwerkhardwareanforderungen für Netzwerkschnittstellenkarten und physische Switches zur Planung einer SDN-Umgebung.

#### <a name="network-interface-cards-nics"></a>Netzwerkschnittstellenkarten (NICs)

Die NICs, die Sie in Ihren Hyper-V-Hosts und Speicherhosts verwenden, erfordern bestimmte Funktionen, um die bestmögliche Leistung erzielen zu können.

Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) ist eine Methode zur Umgehung des Kernels, mit der große Datenmengen ohne Verwendung der Host-CPU übertragen werden können, wodurch die CPU zur Durchführung anderer Aufgaben freigegeben wird. Switch Embedded Teaming (SET) ist eine alternative NIC-Teaminglösung, die Sie in Umgebungen verwenden können, die Hyper-V und den SDN-Stapel enthalten. SET integriert einige NIC-Teamingfunktionen in den virtuellen Hyper-V-Switch.

Weitere Informationen finden Sie unter [Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) und SET (Switch Embedded Teaming)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Um den Mehraufwand für den Datenverkehr des virtuellen Mandantennetzwerks zu berücksichtigen, der durch VXLAN- oder NVGRE-Kapselungsheader verursacht wird, muss die maximale Übertragungseinheit (MTU) des Layer-2-Fabric-Netzwerks (Switches und Hosts) auf einen Wert größer als oder gleich 1674 Bytes, \(einschließlich Layer-2-Ethernet-Headern\) festgelegt sein.

NICs, die das neue, erweiterte Adapterschlüsselwort *EncapOverhead* unterstützen, legen die MTU automatisch über den Netzwerkcontroller-Host-Agent fest. NICs, die das neue Schlüsselwort *EncapOverhead* nicht unterstützen, müssen die MTU-Größe auf jedem physischen Host mit dem Schlüsselwort *JumboPacket* \(oder einem entsprechenden Schlüsselwort\) festlegen.

#### <a name="switches-and-routers"></a>Switches und Router

Bei der Auswahl eines physischen Switches und Routers für Ihre SDN-Umgebung sollten Sie sicherstellen, dass er die folgenden Funktionen unterstützt:
- Switchport-MTU-Einstellungen \(erforderlich\)
- MTU festgelegt auf >= 1674 Bytes, \(einschließlich L2-Ethernet-Header\)
- L3-Protokolle \(erforderlich\)
- ECMP-Routing (Equal-Cost Multi Path)
- Auf BGP \(IETF RFC 4271\)\- basierendes ECMP

Implementierungen sollten die MUST-Anweisungen in den folgenden IETF-Standards unterstützen:
- RFC 2545: [BGP-4-Multiprotokollerweiterungen für das domänenübergreifende IPv6-Routing](https://tools.ietf.org/html/rfc2545)
- RFC 4760: [Multiprotokollerweiterungen für BGP-4](https://tools.ietf.org/html/rfc4760)
- RFC 4893: [BGP-Unterstützung für 4-Oktett-ASN-Räume](https://tools.ietf.org/html/rfc4893)
- RFC 4456: [BGP-Routenreflektion: Eine Alternative zum Full Mesh Internal BGP (IBGP)](https://tools.ietf.org/html/rfc4456)
- RFC 4724: [Ordnungsgemäßer Neustartmechanismus für BGP](https://tools.ietf.org/html/rfc4724)

Die folgenden Markierungsprotokolle sind erforderlich:
- VLAN – Isolation verschiedener Arten von Datenverkehr
- 802.1q-Trunk

Die folgenden Elemente bieten Verknüpfungskontrolle:
- Quality of Service \(QoS\) \(PFC nur erforderlich bei Verwendung von ROCE\)
- Enhanced Traffic Selection \(802.1Qaz\)
- Priority-Based Flow Control (PFC) \(802.1p/Q und 802.1Qbb\)

Die folgenden Elemente bieten Verfügbarkeit und Redundanz:
- Switchverfügbarkeit (erforderlich)
- Ein hochverfügbarer Router ist erforderlich, um Gatewayfunktionen auszuführen. Diesen können Sie bereitstellen, indem Sie einen Mehrfach-Chassis-Switch/Router verwenden oder Technologien wie das Virtual Router Redundancy Protocol (VRRP).

### <a name="domain-requirements"></a>Domänenanforderungen

Für Azure Stack HCI gelten keine speziellen Domänenanforderungen auf Funktionsebene – Sie benötigen einfach nur eine unterstützte Betriebssystemversion für Ihren Domänencontroller. Als allgemeine Best Practice wird empfohlen, das Papierkorbfeature von Active Directory zu aktivieren, falls Sie dies noch nicht getan haben. Weitere Informationen finden Sie unter [Übersicht über Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="maximum-supported-hardware-specifications"></a>Maximal unterstützte Hardwarespezifikationen

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

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Auswählen von Laufwerken](choose-drives.md)
- [Hardwareanforderungen für „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
