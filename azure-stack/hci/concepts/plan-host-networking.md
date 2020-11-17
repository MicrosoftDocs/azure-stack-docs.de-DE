---
title: Planen von Hostnetzwerken für Azure Stack HCI
description: Erfahren Sie, wie Sie Hostnetzwerke für Azure Stack HCI-Cluster planen.
author: v-dasis
ms.topic: how-to
ms.date: 11/09/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b6cfbfcff408483d7086c311dff41fdab59c9524
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414060"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>Planen von Hostnetzwerken für Azure Stack HCI

> Gilt für Azure Stack HCI, Version 20H2

In diesem Thema werden Überlegungen und Anforderungen erläutert, die bei der Hostnetzwerk-Planung in nicht gestreckten und gestreckten Azure Stack HCI-Clusterumgebungen eine Rolle spielen.

## <a name="node-interconnect-requirements"></a>Anforderungen an Verbindungen zwischen Knoten

In diesem Abschnitt sind bestimmte Netzwerkanforderungen beschrieben, die zwischen Servern an einem Standort erfüllt sein müssen und als Verbindungen bezeichnet werden. Knotenverbindungen mit oder ohne Switches können verwendet werden und werden unterstützt:

- **Mit Switches**: Serverknoten sind in der Regel über Ethernet-Netzwerke miteinander verbunden, die Netzwerkswitches verwenden. Die Switches müssen ordnungsgemäß konfiguriert sein, um die Bandbreite und den Netzwerktyp richtig zu verarbeiten. Wenn Sie das RDMA-Feature (Remote Direct Memory Access, Remotezugriff auf den direkten Speicher) verwenden, das das RoCE-Protokoll implementiert, ist die richtige Konfiguration von Netzwerkgerät und Switch von besonderer Bedeutung.
- **Ohne Switches**: Serverknoten können auch über direkte Ethernet-Verbindungen ohne Switch miteinander verbunden werden. In diesem Fall muss jeder Serverknoten über eine direkte Verbindung mit jedem anderen Clusterknoten am selben Standort verfügen.

### <a name="interconnects-for-2-3-node-clusters"></a>Verbindungen für Cluster mit zwei bis drei Knoten

Dies sind die *Mindestanforderungen* für Verbindungen in Clustern an einem Standort mit zwei oder drei Knoten. Diese gelten für jeden einzelnen Serverknoten:

- Mindestens eine Netzwerkadapterkarte mit 1 Gbit/s für Verwaltungsfunktionen.
- Mindestens eine Netzwerkschnittstellenkarte mit 10 Gbit/s (oder schneller) für Speicher- und Workloaddatenverkehr.
- Für bessere Redundanz und Leistung werden mindestens zwei Netzwerkverbindungen zwischen den einzelnen Knoten empfohlen.

### <a name="interconnects-for-4-node-and-greater-clusters"></a>Verbindungen für Cluster mit vier oder mehr Knoten

Dies sind die *Mindestanforderungen* für Verbindungen in Clustern mit vier oder mehr Knoten sowie für Hochleistungscluster. Diese gelten für jeden einzelnen Serverknoten:

- Mindestens eine Netzwerkadapterkarte mit 1 Gbit/s für Verwaltungsfunktionen.
- Mindestens eine Netzwerkschnittstellenkarte mit 25 Gbit/s (oder schneller) für Speicher- und Workloaddatenverkehr. Für bessere Redundanz und Leistung werden mindestens zwei Netzwerkverbindungen empfohlen.
- RDMA-fähige Netzwerkkarten: iWARP (empfohlen) oder RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Anforderungen an Site-to-Site-Cluster (Stretchingcluster)

Bei Verbindungen zwischen Standorten mit Stretchingclustern gelten weiterhin die Verbindungsanforderungen innerhalb jedes Standorts. Darüber hinaus müssen zusätzliche Anforderungen für den Datenverkehr durch Speicherreplikate und die Hyper-V-Livemigration berücksichtigt werden:

- Mindestens eine RDMA- oder Ethernet/TCP-Verbindung mit 1 Gbit/s zwischen den Standorten für die synchrone Replikation. Eine Verbindung mit 25 Gbit/s wird bevorzugt.
- Ein Netzwerk zwischen den Standorten, das genügend Bandbreite für die Workloads von E/A-Schreibvorgängen sowie eine Roundtriplatenz von durchschnittlich 5 ms oder weniger für die synchrone Replikation bietet. Für die asynchrone Replikation gelten keine Empfehlungen in Bezug auf die Latenz.
- Wenn Sie eine einzelne Verbindung zwischen Ihren Standorten verwenden, legen Sie über PowerShell SMB-Bandbreitenlimits für Speicherreplikate fest. Weitere Informationen finden Sie unter [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Wenn Sie mehrere Verbindungen zwischen Standorten verwenden, trennen Sie den Datenverkehr zwischen den Verbindungen. Leiten Sie beispielsweise über PowerShell den Datenverkehr für Speicherreplikate über ein anderes Netzwerk als den Datenverkehr für die Hyper-V-Livemigration. Weitere Informationen finden Sie unter [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

## <a name="rdma-considerations"></a>Überlegungen zu RDMA

Der direkte Remotespeicherzugriff (Remote Direct Memory Access, RDMA) ist ein direkter Speicherzugriff aus dem Arbeitsspeicher eines Computers auf den eines anderen, ohne das Betriebssystem eines der Computer einzubeziehen. Dies ermöglicht Netzwerkbetrieb mit hohem Durchsatz und geringer Latenz und minimiert gleichzeitig die CPU-Auslastung, was in Clustern besonders nützlich ist.

Der gesamte Host-RDMA-Datenverkehr nutzt SMB Direct. SMB Direct ist über RDMA gesendeter SMB 3.0-Datenverkehr und wird im Multiplexverfahren über Port 445 gesendet. Es müssen mindestens zwei für die prioritätsbasierte Flusssteuerung (Priority-based Flow Control, PFC) geeignete Datenverkehrsklassen (Traffic Classes, TCs) für RDMA-Datenverkehr verwendet werden, um die Kompatibilität mit der Mehrzahl der aktuellen und zukünftigen physischen Switches auf dem Markt beizubehalten.

Das Internet Wide Area RDMA Protocol (iWARP) führt RDMA über TCP aus, während RDMA over Converged Ethernet (RoCE) die Verwendung von TCP vermeidet, aber sowohl NICs als auch physische Switches erfordert, die dies unterstützen. Informationen zu Converged Network-Anforderungen für RDMA über RoCE finden Sie im [RDMA-Bereitstellungshandbuch für Windows Server 2016 und 2019 ](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

RDMA ist standardmäßig für den gesamten Ost/West-Datenverkehr zwischen Clusterknoten an einem Standort im gleichen Subnetz aktiviert. RDMA ist deaktiviert und wird für den Nord/Süd-Stretched Cluster-Datenverkehr zwischen Standorten in verschiedenen Subnetzen nicht unterstützt.

Die Anforderungen für RDMA für Azure Stack HCI lauten:

- Für den gesamten Datenverkehr zwischen Subnetzen und Orten (Stretched Clusters) muss Winsock TCP verwendet werden. Alle dazwischen liegenden Netzwerkhops liegen außerhalb der Sicht und Kontrolle von Azure Stack HCI.
- RDMA zwischen Subnetzen und Standorten (Stretched Clusters) wird nicht unterstützt. Dies könnte aufgrund der Verwendung von Uplinks und mehreren Netzwerkgeräten an mehreren Fehlerpunkten instabil und nicht unterstützbar werden.
- Für den Speicherreplikat-Datenverkehr für Stretched Clusters werden keine zusätzlichen virtuellen NICs benötigt. Allerdings kann es für die Problembehandlung hilfreich sein, den standort- und subnetzübergreifenden Datenverkehr vom Ost-West-RDMA-Datenverkehr getrennt zu halten. Wenn SMB Direct nicht nativ pro Flow standort- oder subnetzübergreifend deaktiviert werden kann, gehen Sie wie folgt vor:
    - Mindestens eine zusätzliche vNIC sollte für das Speicherreplikat bereitgestellt werden
    - Für Speicherreplikat-vNICs muss RDMA mithilfe des PowerShell-Cmdlets [Disable-NetAdapterRDMA](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma) deaktiviert werden, da RDMA definitionsgemäß standort- und subnetzübergreifend ist
    - Native RDMA-Adapter benötigen einen vSitch und vNICs zur Unterstützung des Speicherreplikats, um die oben genannten Anforderungen an Standort/Subnetz zu erfüllen
    - Für die standortinternen RDMA-Bandbreitenanforderungen müssen die Bandbreiten-Prozentsätze pro Datenverkehrstyp wie im Abschnitt **Bandbreitenzuordnung für Datenverkehr** beschrieben bekannt sein. Dadurch wird sichergestellt, dass die entsprechenden Bandbreitenreservierungen und -begrenzungen auf den Ost/West-Datenverkehr (Knoten zu Knoten) angewendet werden können.
- Für Livemigrations- und Speicherreplikat-Datenverkehr muss die SMB-Bandbreite begrenzt werden, denn andernfalls könnten sie die gesamte Bandbreite auf Kosten von Speicherdatenverkehr mit hoher Priorität beanspruchen. Weitere Informationen finden Sie in den Artikeln zu den PowerShell-Cmdlets [Set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) und [Set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint).

> [!NOTE]
> Wenn Sie das `Set-SmbBandwidthLimit`-Cmdlet verwenden, müssen Sie Bits in Bytes konvertieren.

## <a name="traffic-bandwidth-allocation"></a>Bandbreitenzuordnung für Datenverkehr

In der folgenden Tabelle sind die Bandbreitenzuordnungen für verschiedene Datenverkehrstypen aufgeführt:

- Alle Einheiten sind Gbit/s.
- Werte gelten sowohl für gestreckte als auch für nicht gestreckte Cluster.
- SMB-Datenverkehr erhält 50 % der gesamten Bandbreitenzuordnung.
- Der SBL/CSV-Datenverkehr erhält 70 % der restlichen 50 %-Zuordnung.
- Der Livemigration-Datenverkehr (LM) erhält 15 % der restlichen 50 %-Zuordnung.
- Der Speicherreplikat-Datenverkehr (SR) erhält 14 % der restlichen 50 %-Zuordnung.
- Der Heartbeat-Datenverkehr (HR) erhält 1 % der restlichen 50 %-Zuordnung.
- *= sollte Komprimierung anstelle von RDMA verwenden, wenn die Bandbreitenzuordnung für LM-Datenverkehr < 5 Gbit/s beträgt

|NIC-Geschwindigkeit|Kombinierte Bandbreite|SMB 50 % Reservierung|SBL/CSV %|SBL/CSV Bandbreite|LM %|LM Bandbreite|SR % |SR Bandbreite|HB %|HB Bandbreite|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70%|7|14 %*|1,4*|14 %|1.4|2 %|0.2|
|25|50|25|70%|17,5|15 %*|3,75*|14 %|3,5|1%|0,25|
|40|80| 40|70%|28|15 %|6|14 %|5.6|1%|0.4|
|50|100|50|70%|35|15 %|7,5|14 %|7|1%|0.5|
|100|200|100|70%|70|15 %|15|14 %|14|1%|1|
|200|400|200|70%|140|15 %|30|14 %|28|1%|2|

## <a name="network-port-requirements"></a>Anforderungen an Netzwerkports

Stellen Sie sicher, dass zwischen allen Serverknoten innerhalb eines Standorts und zwischen Standorten (bei Stretchingclustern) die richtigen Netzwerkports geöffnet sind. Sie benötigen geeignete Firewall- und Routerregeln zum Zulassen von ICMP- und SMB-Datenverkehr (Port 445 plus Port 5445 für SMB Direct) sowie von bidirektionalem WS-MAN-Datenverkehr (Port 5985) zwischen allen Servern im Cluster.

Wenn Sie zum Erstellen des Clusters den Assistenten für die Clustererstellung in Windows Admin Center verwenden, öffnet dieser automatisch auf jedem Server im Cluster die entsprechenden Firewallports für Failoverclustering, Hyper-V und Speicherreplikate. Wenn Sie auf den einzelnen Servern unterschiedliche Firewalls verwenden, öffnen Sie die folgenden Ports:

### <a name="failover-clustering-ports"></a>Ports für Failoverclustering

- ICMPv4 und ICMPv6
- TCP-Port 445
- Dynamische RPC-Ports
- TCP-Port 135
- TCP-Port 137
- TCP-Port 3343
- UDP-Port 3343

### <a name="hyper-v-ports"></a>Hyper-V-Ports

- TCP-Port 135
- TCP-Port 80 (HTTP-Konnektivität)
- TCP-Port 443 (HTTPS-Konnektivität)
- TCP-Port 6600
- TCP-Port 2179
- Dynamische RPC-Ports
- RPC-Endpunktzuordnung
- TCP-Port 445

### <a name="storage-replica-ports-stretched-cluster"></a>Ports für Speicherreplikate (Stretchingcluster)

- TCP-Port 445
- TCP 5445 (bei Verwendung von RDMA über iWARP)
- TCP-Port 5985
- ICMPv4 und ICMPv6 (bei Verwendung des `Test-SRTopology`-PowerShell-Cmdlets)

Möglicherweise müssen weitere Ports geöffnet werden, die in dieser Liste nicht aufgeführt sind. Dies sind die Ports für die grundlegende Azure Stack HCI-Funktionalität.

## <a name="network-switch-requirements"></a>Anforderungen an Netzwerkswitches

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

## <a name="traffic-types-supported"></a>Unterstützte Datenverkehrstypen

Azure Stack HCI verwendet den Server Message Block (SMB). SMB in Azure Stack HCI unterstützt die folgenden Datenverkehrstypen:

- SBL (Storage Bus Layer) – wird von „Direkte Speicherplätze“ verwendet; Datenverkehr mit höchster Priorität
- Freigegebene Clustervolumes (Cluster Shared Volumes, CSV)
- Livemigration (LM)
- Speicherreplikat (SR) – wird in Stretched Clusters verwendet
- Dateifreigaben (File Shares, FS) – herkömmliche Dateifreigaben und Dateiserver mit horizontaler Skalierung (Scale-Out File Server, SOFS)
- Clustertakt (Heartbeat, HB)
- Clusterkommunikation (Knotenbeitritte, Clusterupdates, Registrierungsupdates)

SMB-Datenverkehr kann über die folgenden Protokolle übertragen werden:

- Transmission Control Protocol (TCP) – wird zwischen Standorten verwendet
- Remotezugriff auf den direkten Speicher (Remote Direct Memory Access, RDMA)


## <a name="next-steps"></a>Nächste Schritte

- Frischen Sie Ihre Grundlagenkenntnisse zu Failoverclustering auf. Siehe [Failover Clustering Networking Basics and Fundamentals](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09) (Failoverclustering-Netzwerkgrundlagen).
- Frischen Sie Ihre Grundlagenkenntnisse zur Verwendung von SET auf. Siehe [Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) und SET (Switch Embedded Teaming)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Informationen zur Bereitstellung finden Sie unter [Erstellen eines Clusters mithilfe von Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Informationen zur Bereitstellung finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).