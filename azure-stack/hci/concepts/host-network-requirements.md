---
title: Anforderungen an das Hostnetzwerk für Azure Stack HCI
description: Informieren Sie sich über die Anforderungen an das Hostnetzwerk für Azure Stack HCI.
author: v-dasis
ms.topic: how-to
ms.date: 11/25/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 2d31294b2b3a402efcfd4376988d1de6c3dbabd1
ms.sourcegitcommit: 26901a61a44390bc9b7804c22018c213036e680d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355598"
---
# <a name="host-network-requirements-for-azure-stack-hci"></a>Anforderungen an das Hostnetzwerk für Azure Stack HCI

> Gilt für Azure Stack HCI, Version 20H2

In diesem Thema werden Überlegungen und Anforderungen zu Azure Stack HCI-Hostnetzwerken behandelt. Informationen zu Rechenzentrumsarchitekturen und den physischen Verbindungen zwischen Servern finden Sie unter [Anforderungen an das physische Netzwerk](physical-network-requirements.md).

## <a name="network-traffic-types"></a>Typen des Netzwerkdatenverkehrs

Azure Stack HCI-Netzwerkdatenverkehr kann nach seinem beabsichtigten Einsatzzweck klassifiziert werden:

- **Computedatenverkehr**: Datenverkehr von oder zu einem virtuellen Computer (VM)
- **Speicherdatenverkehr**: Datenverkehr für direkte Speicherplätze (S2D) unter Verwendung von Server Message Block (SMB)
- **Verwaltungsdatenverkehr**: Datenverkehr, der für einen Administrator für die Clusterverwaltung relevant ist, z. B. für Active Directory, Remotedesktop, Windows Admin Center und Windows PowerShell

## <a name="selecting-a-network-adapter"></a>Auswählen eines Netzwerkadapters

Für Azure Stack HCI müssen Sie einen Netzwerkadapter auswählen, der mit der Zusatzqualifikation (Additional Qualification, AQ) Standard oder Premium für Windows SDDC (Software Defined Data Center) zertifiziert ist. Diese Adapter unterstützen die fortschrittlichsten Plattformfunktionen und wurden von unseren Hardwarepartnern am intensivsten getestet. In der Regel führt diese Sorgfalt zu weniger Qualitätsproblemen im Zusammenhang mit Hardware und Treibern.

Suchen Sie im [Windows Server-Katalog](https://www.windowsservercatalog.com/) nach dem Eintrag für Ihren Adapter und das betreffende Betriebssystem, um herauszufinden, ob ein Adapter eine Standard- oder Premium-AQ besitzt. Im Folgenden finden Sie ein Beispiel für die Kennzeichnung als Premium-AQ:

:::image type="content" source="media/plan-networking/windows-certified.png" alt-text="Für Windows zertifiziert" lightbox="media/plan-networking/windows-certified.png":::

## <a name="overview-of-key-network-adapter-capabilities"></a>Übersicht über die wichtigsten Funktionen des Netzwerkadapters

Einige wichtige Funktionen von Netzwerkadaptern, die von Azure Stack HCI genutzt werden:

- Dynamic Virtual Machine Multi-Queue (Dynamic VMMQ oder d.VMMQ)
- Remotezugriff auf den direkten Speicher (RDMA)
- Gast-RDMA
- Switch Embedded Teaming (SET)

### <a name="dynamic-vmmq"></a>Dynamic VMMQ

Alle Netzwerkadapter mit dem Premium-AQ unterstützen Dynamic VMMQ. Dynamic VMMQ erfordert die Verwendung von Switch Embedded Teaming.

**Betreffende Datenverkehrstypen**: Compute

**Erforderliche Zertifizierungen**: Premium

Dynamic VMMQ ist eine intelligente empfangsseitige Technologie, die auf den Vorläufern Virtual Machine Queue (VMQ), Virtual Receive Side Scaling (vRSS) und VMMQ aufbaut und im Vergleich zu diesen drei wichtige Verbesserungen bietet:

- Optimiert die Hosteffizienz durch die Verwendung von CPU-Kernen
- Automatische Optimierung der Verarbeitung von Netzwerkdatenverkehr auf CPU-Kernen, sodass VMs den erwarteten Durchsatz erzielen und aufrechterhalten können
- Erlaubt Workloads mit Auslastungsspitzen, um die erwartete Datenverkehrsmenge empfangen zu können

Weitere Informationen zu Dynamic VMMQ finden Sie im Blogbeitrag [Synthetic Accelerations](https://techcommunity.microsoft.com/t5/networking-blog/synthetic-accelerations-in-a-nutshell-windows-server-2019/ba-p/653976) (Synthetische Beschleunigungen).

### <a name="rdma"></a>RDMA

RDMA bezeichnet eine Abladung des Netzwerkstapels auf den Netzwerkadapter, die es dem SMB-Speicherdatenverkehr ermöglicht, bei der Verarbeitung das Betriebssystem zu umgehen.

RDMA ermöglicht Netzwerke mit hohem Durchsatz und geringer Latenz bei möglichst geringer Auslastung der Host-CPU-Ressourcen. Diese Host-CPU-Ressourcen können dann verwendet werden, um zusätzliche VMs oder Container auszuführen.

**Betreffende Datenverkehrstypen**: Hostspeicher

**Erforderliche Zertifizierungen**: Standard

Alle Adapter mit Standard- oder Premium-AQ unterstützen RDMA (Remote Direct Memory Access). RDMA wird für die Bereitstellung von Speicherworkloads in Azure Stack HCI empfohlen und kann optional für Speicherworkloads (unter Verwendung von SMB) für virtuelle Computer aktiviert werden. Weitere Informationen finden Sie im Abschnitt **Gast-RDMA** weiter unten.

Azure Stack HCI unterstützt RDMA mithilfe der Implementierungen des iWARP- oder RoCE-Protokolls.

> [!IMPORTANT]
> RDMA-Adapter können nur mit anderen RDMA-Adaptern verwendet werden, die dasselbe RDMA-Protokoll (iWARP oder RoCE) implementieren.

RDMA wird nicht von allen Netzwerkadaptern der Anbieter unterstützt. In der folgenden Tabelle sind die Anbieter (in alphabetischer Reihenfolge) aufgelistet, die RDMA-Adapter mit Premium-Zertifizierung anbieten. In dieser Liste sind jedoch nicht alle Hardwareanbieter aufgeführt, die RDMA unterstützen. Überprüfen Sie die RDMA-Unterstützung im [Windows Server-Katalog](https://www.windowsservercatalog.com/).

> [!NOTE]
> InfiniBand (IB) wird mit Azure Stack HCI nicht unterstützt.

|Netzwerkkartenhersteller|iWARP|RoCE|
|----|----|----|
|Broadcom|Nein|Ja|
|Chelsio|Ja|Nein|
|Intel|Ja|Ja (bestimmte Modelle)|
|Marvell (Qlogic/Cavium)|Ja|Ja|
|Nvidia (Mellanox)|Nein|Ja|

> [!NOTE]
> RDMA wird nicht von allen Adaptern der Anbieter unterstützt. Lassen Sie sich die RDMA-Unterstützung vom Anbieter Ihrer Netzwerkkarte bestätigen.

Weitere Informationen zum Bereitstellen von RDMA finden Sie in dem Word-Dokument, das Sie aus dem [SDN-GitHub-Repository](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx) herunterladen können.

#### <a name="internet-wide-area-rdma-protocol-iwarp"></a>Internet Wide Area RDMA Protocol (iWARP)

iWARP verwendet TCP (Transmission Control Protocol) und kann optional mit DCB-PFC (Priority-based Flow Control mit Data Center Bridging) und ETS (Enhanced Transmission Service) erweitert werden.

Verwenden Sie iWARP in folgenden Situationen:

- Sie haben nur geringe oder gar keine Erfahrung mit Netzwerken oder sind nicht mit der Verwaltung von Netzwerkswitches vertraut.
- Die ToR-Switches unterliegen nicht Ihrer Kontrolle.
- Die Lösung wird nach der Bereitstellung nicht von Ihnen verwaltet.
- Sie verfügen bereits über Bereitstellungen, die iWARP verwenden.
- Sie wissen nicht, für welche Option Sie sich entscheiden sollen.

#### <a name="rdma-over-converged-ethernet-roce"></a>RDMA over Converged Ethernet (RoCE)

RoCE verwendet UDP (User Datagram Protocol) und setzt aus Gründen der Zuverlässigkeit PFC mit DCB (Data Center Bridging) und ETS voraus.

Verwenden Sie RoCE in folgenden Situationen:

- Sie verfügen bereits über Bereitstellungen mit RoCE in Ihrem Rechenzentrum.
- Sie kennen die Anforderungen an Ihr physisches Netzwerk genau.

### <a name="guest-rdma"></a>Gast-RDMA

Mit Gast-RDMA können Sie bei SMB-Workloads für VMs von den gleichen Vorteilen wie bei Verwendung von RDMA auf Hosts profitieren.

**Betreffende Datenverkehrstypen**: Compute

**Erforderliche Zertifizierungen**: Premium

 Hauptvorteile der Verwendung von Gast-RDMA:

- CPU-Abladung an die Netzwerkkarte für die Verarbeitung des Netzwerkdatenverkehrs
- Äußerst geringe Latenz
- Hoher Durchsatz

Weitere Informationen u. a. zum Bereitstellen von Gast-RDMA finden Sie in dem Word-Dokument, das Sie aus dem [SDN-GitHub-Repository](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx) herunterladen können.

### <a name="switch-embedded-teaming-set"></a>Switch Embedded Teaming (SET)

Switch Embedded Teaming (SET) ist eine softwarebasierte Teamingtechnologie, die ab Windows Server 2016 Bestandteil des Windows Server-Betriebssystems ist. SET ist nicht vom Typ der verwendeten Netzwerkadapter abhängig.

**Betreffende Datenverkehrstypen**: Compute, Speicher und Verwaltung

**Erforderliche Zertifizierungen**: keine (in Betriebssystem integriert)

SET ist die einzige Teamingtechnologie, die von Azure Stack HCI unterstützt wird. Lastenausgleich/Failover (Load Balancing/Failover, LBFO) ist eine weitere Teamingtechnologie, die häufig mit Windows Server verwendet wird, von Azure Stack HCI jedoch nicht unterstützt wird. Weitere Informationen zu LBFO in Azure Stack HCI finden Sie im Blogbeitrag [Teaming in Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642). SET kann gleichermaßen für Compute-, Storage- und Verwaltungsdatenverkehr verwendet werden.

SET ist für Azure Stack HCI von großer Bedeutung, da es die einzige Teamingtechnologie ist, die folgende Funktionen unterstützt:

- Teaming von RDMA-Adaptern (bei Bedarf)
- Gast-RDMA
- Dynamic VMMQ
- Weitere wichtige Azure Stack HCI-Features (siehe [Teaming in Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642))

SET bietet gegenüber LBFO zusätzliche Features, einschließlich Qualitäts- und Leistungsverbesserungen. Hierzu setzt SET die Verwendung von symmetrischen (identischen) Adaptern voraus. Ein Teaming von asymmetrischen Adaptern wird nicht unterstützt. Netzwerkadapter sind symmetrisch, wenn Folgendes identisch ist:

- Marke (Hersteller)
- Modell (Version)
- Geschwindigkeit (Durchsatz)
- Konfiguration

Am einfachsten lässt sich feststellen, ob Adapter symmetrisch sind, wenn die Geschwindigkeiten und die Schnittstellenbeschreibungen übereinstimmen. Eine Abweichung ist nur bei der in der Beschreibung aufgeführten Ziffer zulässig. Verwenden Sie das Cmdlet [`Get-NetAdapterAdvancedProperty`](https://docs.microsoft.com/powershell/module/netadapter/get-netadapteradvancedproperty), um sicherzustellen, dass in der zurückgegebenen Konfiguration die gleichen Eigenschaftswerte aufgeführt sind.

In der folgenden Tabelle finden Sie ein Beispiel für Schnittstellenbeschreibungen, die sich nur durch die Ziffer (#) unterscheiden:

|Name|Schnittstellenbeschreibung|Übertragungsrate|
|----|----|----|
|NIC1|Netzwerkadapter #1|25GBit/s|
|NIC2|Netzwerkadapter #2|25GBit/s|
|NIC3|Netzwerkadapter #3|25GBit/s|
|NIC4|Netzwerkadapter #4|25GBit/s|

### <a name="rdma-traffic-considerations"></a>Überlegungen zum RDMA-Datenverkehr

Wenn Sie Data Center Bridging (DCB) implementieren, müssen Sie sicherstellen, dass die Konfiguration von PFC und ETS für jeden Netzwerkport, einschließlich Netzwerkswitches, ordnungsgemäß implementiert wurde. DCB ist für RoCE erforderlich und bei iWARP optional.

Weitere Informationen zum Bereitstellen von RDMA finden Sie in dem Word-Dokument, das Sie aus dem [SDN-GitHub-Repository](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx) herunterladen können.

RoCE-basierte Azure Stack HCI-Implementierungen erfordern die Konfiguration von drei PFC-Datenverkehrsklassen, einschließlich der Standard-Datenverkehrsklasse, im gesamten Fabric und auf allen Hosts:

#### <a name="cluster-traffic-class"></a>Cluster-Datenverkehrsklasse

Diese Datenverkehrsklasse stellt sicher, dass genügend Bandbreite für den Clustertakt reserviert ist:

- Erforderlich: Ja
- PFC aktiviert: Nein
- Empfohlene Datenverkehrspriorität: Priorität 7
- Empfohlene Bandbreitenreservierung:
    - RDMA-Netzwerke bis 10 GbE = 2 %
    - RDMA-Netzwerke ab 25 GbE = 1 %

#### <a name="rdma-traffic-class"></a>RDMA-Datenverkehrsklasse

Diese Datenverkehrsklasse stellt sicher, dass genügend Bandbreite für die verlustfreie RDA-Kommunikation mithilfe von SMB Direct reserviert ist:

- Erforderlich: Ja
- PFC aktiviert: Ja
- Empfohlene Datenverkehrspriorität: Priorität 3 oder 4
- Empfohlene Bandbreitenreservierung: 50 %

#### <a name="default-traffic-class"></a>Standard-Datenverkehrsklasse

Dieser Datenverkehrsklasse unterliegt der gesamte sonstige Datenverkehr (einschließlich VM- und Verwaltungsdatenverkehr), der nicht in der Cluster- oder RDMA-Datenverkehrsklasse definiert ist:

- Erforderlich: Standard (keine Konfiguration auf dem Host erforderlich)
- Flusssteuerung (PFC) aktiviert: Nein
- Empfohlene Datenverkehrsklasse: Standard (Priorität 0)
- Empfohlene Bandbreitenreservierung: Standard (keine Hostkonfiguration erforderlich)

## <a name="storage-traffic-models"></a>Modelle für den Speicherdatenverkehr

SMB bietet als Speicherprotokoll für Azure Stack HCI viele Vorteile, z. B. SMB Multichannel. SMB Multichannel wird zwar in diesem Thema nicht behandelt, so sollten Sie dennoch wissen, dass der Datenverkehr über alle Links, die von SMB Multichannel verwendet werden können, Multiplexing verwendet.

> [!NOTE]
>Es wird empfohlen, mehrere Subnetze und VLANs zu verwenden, um den Speicherdatenverkehr in Azure Stack HCI abzutrennen.

Betrachten Sie das folgende Beispiel eines Clusters mit vier Knoten. Jeder Server verfügt über zwei Speicherports (links und rechts). Da sich alle Adapter im gleichen Subnetz und VLAN befinden, verteilt SMB Multichannel Verbindungen über alle verfügbaren Links. Daher stellt der linke Port auf dem ersten Server (192.168.1.1) eine Verbindung mit dem linken Port auf dem zweiten Server (192.168.1.2) her. Der rechte Port auf dem ersten Server (192.168.1.12) stellt eine Verbindung mit dem rechten Port auf dem zweiten Server her. Entsprechende Verbindungen werden für den dritten und vierten Server hergestellt.

Dies führt jedoch zu unnötigen Verbindungen und zu einer Überlastung des Interlink (Multi-Chassis Link Aggregation Group, MC-LAG), der die ToR-Switches (Top of Rack) verbindet (mit X gekennzeichnet). Sehen Sie sich die folgende Abbildung an:

:::image type="content" source="media/plan-networking/four-node-cluster-1.png" alt-text="Cluster mit vier Knoten und demselben Subnetz" lightbox="media/plan-networking/four-node-cluster-1.png":::

Die empfohlene Vorgehensweise besteht darin, separate Subnetze und VLANs für jede Gruppe von Adaptern zu verwenden. Im folgenden Diagramm verwenden die rechten Ports nun Subnetz 192.168.2. x/24 und VLAN2. Dadurch kann der Datenverkehr an den linken Ports auf TOR1 und der Datenverkehr an den rechten Ports auf TOR2 bleiben. Sehen Sie sich die folgende Abbildung an:

:::image type="content" source="media/plan-networking/four-node-cluster-2.png" alt-text="Cluster mit vier Knoten und unterschiedlichen Subnetzen" lightbox="media/plan-networking/four-node-cluster-2.png":::

## <a name="traffic-bandwidth-allocation"></a>Bandbreitenzuordnung für Datenverkehr

Die folgende Tabelle enthält Beispiele für Bandbreitenzuordnungen verschiedener Datenverkehrstypen bei üblichen Adaptergeschwindigkeiten in Azure Stack HCI. Beachten Sie, dass es sich hierbei um ein Beispiel für eine zusammengeführte Lösung handelt, bei der alle Datenverkehrstypen (Compute, Speicher und Verwaltung) über die gleichen physischen Adapter geführt werden und ein Teaming mithilfe von SET erfolgt.

Da dieser Anwendungsfall mit den meisten Einschränkungen verbunden ist, ist er gut als Baseline geeignet. Unter Berücksichtigung der Variationen bei der Anzahl von Adaptern und den Geschwindigkeiten sollte dies jedoch als Beispiel und nicht als erforderliche Unterstützung betrachtet werden.

Dieses Beispiel geht von folgenden Voraussetzungen aus:

- Zwei Adapter pro Team
- SBL- (Storage Bus Layer), CSV- (Cluster Shared Volume) und Hyper-V-Datenverkehr (Livemigration):

    - Verwenden derselben physischen Adapter
    - Verwenden von SMB
- SMB erhält eine Bandbreitenreservierung von 50 % mithilfe von Data Center Bridging
    - SBL/CSV ist der Datenverkehr mit der höchsten Priorität und erhält 70 % der SMB-Bandbreitenreservierung
    - Livemigration (LM) wurde mithilfe des Cmdlets `Set-SMBBandwidthLimit` eingeschränkt und erhält 29 % der verbleibenden Bandbreite
        - Wenn die verfügbare Bandbreite für die Livemigration > = 5 Gbit/s beträgt und die Netzwerkadapter dies unterstützen, verwenden Sie RDMA. Verwenden Sie hierzu das folgende Cmdlet:

            ```Powershell
            Set-VMHost VirtualMachineMigrationPerformanceOption SMB
            ```

        - Wenn die verfügbare Bandbreite für die Livemigration < 5 Gbit/s beträgt, verringern Sie die Ausfallzeiten mithilfe von Komprimierung. Verwenden Sie hierzu das folgende Cmdlet:

            ```Powershell
            Set-VMHost -VirtualMachineMigrationPerformanceOption Compression
            ```

 - Wenn Sie RDMA mit Livemigration verwenden, stellen Sie mithilfe einer SMB-Bandbreitenbegrenzung sicher, dass der Datenverkehr für die Livemigration nicht die gesamte Bandbreite belegen kann, die der RDMA-Datenverkehrsklasse zugeordnet ist. Beachten Sie, dass dieses Cmdlet eine Eingabe in Byte pro Sekunde (B/s) erwartet, für die Netzwerkadapter hingegen Bit pro Sekunde (Bit/s) aufgeführt sind. Verwenden Sie das folgende Cmdlet, um eine Bandbreitenbegrenzung von 6 Gbit/s festzulegen. Beispiel:

    ```Powershell
    Set-SMBBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
    ```

    > [!NOTE]
    >750 MB/s in diesem Beispiel entsprechen 6 Gbit/s.

Hier sehen Sie die Tabelle mit der Beispiel-Bandbreitenzuordnung:

|NIC-Geschwindigkeit|Kombinierte Bandbreite|SMB-Bandbreitenreservierung**|SBL/CSV %|SBL/CSV Bandbreite|Livemigration %|Maximale Bandbreite für die Livemigration|Takt %|Taktbandbreite|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10 GBit/s|20GBit/s|10 GBit/s|70 %|7 GBit/s|*|*|2 %|200 MBit/s|
|25GBit/s|50 GBit/s|25GBit/s|70 %|17,5 GBit/s|29 %|7,25 GBit/s|1%|250 MBit/s|
|40 GBit/s|80 GBit/s|40 GBit/s|70 %|28 GBit/s|29 %|11,6 GBit/s|1%|400 MBit/s|
|50 GBit/s|100 GBit/s|50 GBit/s|70 %|35 GBit/s|29 %|14,5 GBit/s|1%|500 MBit/s|
|100 GBit/s|200 GBit/s|100 GBit/s|70 %|70 GBit/s|29 %|29 GBit/s|1%|1 GBit/s|
|200 GBit/s|400 GBit/s|200 GBit/s|70 %|140 GBit/s|29 %|58 GBit/s|1%|2 GBit/s|

* Sollte Komprimierung anstelle von RDMA verwenden, da die Bandbreitenzuordnung für den Datenverkehr für die Livemigration < 5 Gbit/s beträgt.

** 50 % ist ein Beispiel für die Bandbreitenreservierung im Rahmen dieses Beispiel.

## <a name="stretched-cluster-considerations"></a>Überlegungen zu Stretched Cluster

Stretched Cluster bieten eine Notfallwiederherstellung, die mehrere Rechenzentren umfasst. In seiner einfachsten Form sieht ein Azure Stack HCI-Netzwerk mit Stretched Cluster wie folgt aus:

:::image type="content" source="media/plan-networking/stretched-cluster.png" alt-text="Stretched Cluster" lightbox="media/plan-networking/stretched-cluster.png":::

Stretched Cluster weisen die folgenden Anforderungen und Merkmale auf:

- RDMA ist auf einen einzelnen Standort beschränkt und wird nicht über verschiedene Standorte oder Subnetze hinweg unterstützt.
- Server am gleichen Standort müssen sich im gleichen Rack und innerhalb der gleichen Layer-2-Grenzen befinden.
- Die Kommunikation zwischen Standorten überschreitet eine Layer-3-Grenze. Layer-2-Topologien mit Stretched Cluster werden nicht unterstützt.

- Wenn an einem Standort RDMA für die Speicheradapter verwendet wird, müssen sich diese Adapter in einem separaten Subnetz und VLAN befinden, bei dem keine Weiterleitung zwischen Standorten erfolgt. Dadurch wird verhindert, dass das Speicherreplikat RDMA standortübergreifend verwendet.
- Für die Kommunikation zwischen Standorten verwendete Adapter:

    - Können physisch oder virtuell (Host-vNIC) sein. Bei virtuellen Adaptern müssen Sie pro physischer Netzwerkkarte eine vNIC in einem eigenen Subnetz und VLAN bereitstellen.
    - Muss sich in einem eigenen Subnetz und VLAN befinden, das eine Weiterleitung zwischen Standorten erlaubt.
    - RDMA muss mit dem Cmdlet `Disable-NetAdapterRDMA` deaktiviert werden. Es wird empfohlen, dass Sie mithilfe des Cmdlets `Set-SRNetworkConstraint` explizit festlegen, dass das Speicherreplikat bestimmte Schnittstellen verwenden muss.
    - Muss alle zusätzlichen Anforderungen für das Speicherreplikat erfüllen.
-   Wenn ein Failover zu einem anderen Standort ausgeführt wird, müssen Sie sicherstellen, dass genügend Bandbreite zur Verfügung steht, um die Workloads am anderen Standort auszuführen. Ausreichend ist eine Bereitstellung von Standorten mit 50 % der verfügbaren Kapazität. Dies ist keine zwingende Anforderung, wenn während eines Failovers Leistungseinbußen hinnehmbar sind.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die Anforderungen an Netzwerkswitches und physische Netzwerke. Siehe [Anforderungen für physische Netzwerke](physical-network-requirements.md).
- Frischen Sie Ihre Grundlagenkenntnisse zu Failoverclustering auf. Siehe [Failover Clustering Networking Basics and Fundamentals](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09) (Failoverclustering-Netzwerkgrundlagen).
- Frischen Sie Ihre Grundlagenkenntnisse zur Verwendung von SET auf. Siehe [Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) und SET (Switch Embedded Teaming)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Informationen zur Bereitstellung finden Sie unter [Erstellen eines Clusters mithilfe von Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Informationen zur Bereitstellung finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).
