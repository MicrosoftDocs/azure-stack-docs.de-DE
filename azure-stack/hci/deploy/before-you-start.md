---
title: Vor dem Bereitstellen von Azure Stack HCI
description: Hier finden Sie Informationen zum Vorbereiten der Bereitstellung von Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/14/2020
ms.openlocfilehash: 4ff495aba1f46824a6ab47c95601687402d24edb
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060105"
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

Informationen zu Überlegungen zur Planung von Hostnetzwerken und zu den Anforderungen finden Sie unter [Planen von Hostnetzwerken für Azure Stack HCI](../concepts/plan-host-networking.md).

### <a name="software-defined-networking-requirements"></a>Softwaredefinierte Netzwerkanforderungen

Wenn Sie einen Azure Stack HCI-Cluster mithilfe von Windows Admin Center erstellen, haben Sie die Möglichkeit, Netzwerkcontroller bereitzustellen, um softwaredefinierte Netzwerke zu aktivieren. Wenn Sie beabsichtigen, softwaredefinierte Netzwerke für Azure Stack HCI zu verwenden:

- Stellen Sie sicher, dass die Hostserver über mindestens 50-100 GB freien Speicherplatz verfügen, um die Netzwerkcontroller-VMs zu erstellen.

- Sie müssen eine virtuelle Festplatte (VHD) des Azure Stack HCI-Betriebssystems auf den ersten Knoten im Cluster kopieren, um die Netzwerkcontroller-VMs zu erstellen. Sie können die VHD mithilfe von [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) oder durch Ausführen des Skripts [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) vorbereiten, um eine .ISO-Datei in eine VHD zu konvertieren.

Weitere Informationen zum Vorbereiten der Verwendung von softwaredefinierten Netzwerken in Azure Stack HCI finden Sie unter [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md) und [Planen der Bereitstellung von Netzwerkcontrollern](../concepts/network-controller.md).

### <a name="domain-requirements"></a>Domänenanforderungen

Für Azure Stack HCI gelten keine speziellen Domänenanforderungen auf Funktionsebene – Sie benötigen einfach nur eine unterstützte Betriebssystemversion für Ihren Domänencontroller. Als allgemeine Best Practice wird empfohlen, das Papierkorbfeature von Active Directory zu aktivieren, falls Sie dies noch nicht getan haben.

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
