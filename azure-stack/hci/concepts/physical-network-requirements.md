---
title: Anforderungen an das physische Netzwerk für Azure Stack HCI
description: Informieren Sie sich über die Anforderungen an das physische Netzwerk für Azure Stack HCI.
author: v-dasis
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7452437d7f760a688c2403cb4def735b2daaa105
ms.sourcegitcommit: a7a2ac1b9be926134826dce03e348154fd212bc9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443298"
---
# <a name="physical-network-requirements-for-azure-stack-hci"></a>Anforderungen an das physische Netzwerk für Azure Stack HCI

> Gilt für Azure Stack HCI, Version 20H2

In diesem Thema werden Überlegungen und Anforderungen zum physischen Netzwerk (Fabric-Netzwerk) für Azure Stack HCI behandelt, insbesondere im Hinblick auf Netzwerkswitches.

> [!NOTE]
> Die Anforderungen können sich für zukünftige Azure Stack HCI-Versionen ändern.

## <a name="network-switches-for-azure-stack-hci"></a>Netzwerkswitches für Azure Stack HCI

Microsoft testet die Standards und Protokolle mit Azure Stack HCI, die im Abschnitt **Anforderungen an Netzwerkswitches** weiter unten genannt werden. Obwohl Microsoft keine Netzwerkswitches zertifiziert, arbeiten wir mit Anbietern zusammen, um Geräte zu identifizieren, die Anforderungen von Azure Stack HCI unterstützen.

Diese Anforderungen werden auch unter [Spezifikationen und Richtlinien zum Windows-Hardware-Kompatibilitätsprogramm](https://docs.microsoft.com/windows-hardware/design/compatibility/whcp-specifications-policies) veröffentlicht.  Wählen Sie **Spezifikationen und Richtlinien herunterladen, Version 1809** aus, öffnen Sie die ZIP-Datei und dann die Datei **WHCP-Components-Peripherals-Specification-1809.pdf**. Gehen Sie zum Abschnitt **Device.Network.Switch.SDDC**.

> [!IMPORTANT]
> Möglicherweise funktionieren auch andere Netzwerkswitches, die hier nicht aufgeführte Technologien und Protokolle verwenden, Microsoft kann allerdings nicht gewährleisten, dass diese mit Azure Stack HCI funktionieren, und kann ggf. nicht bei der Behandlung von Problemen behilflich sein.

Wenn Sie Netzwerkswitches kaufen, wenden Sie sich an den Anbieter des Switches, und vergewissern Sie sich, dass die Geräte alle Anforderungen von Azure Stack HCI erfüllen. Die folgenden (in alphabetischer Reihenfolge aufgeführten) Anbieter haben bestätigt, dass ihre Switches die Azure Stack HCI-Anforderungen unterstützen:

|Hersteller|10 GbE|25 GbE|100 GbE|
|-----|-----|-----|-----|
|Dell|[S41xx-Serie](https://www.dell.com/learn/us/en/45/shared-content~data-sheets~en/documents~dell-emc-networking-s4100-series-spec-sheet.pdf)|[S52xx-Serie](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|[S52xx-Serie](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|
|Lenovo|[G8272](https://lenovopress.com/tips1267-lenovo-rackswitch-g8272), [NE1032](https://lenovopress.com/lp0605-thinksystem-ne1032-rackswitch)|[NE2572](https://lenovopress.com/lp0608-lenovo-thinksystem-ne2572-rackswitch) |[NE10032](https://lenovopress.com/lp0609-lenovo-thinksystem-ne10032-rackswitch) |

> [!IMPORTANT]
> Microsoft aktualisiert diese Liste, wenn wir von Anbietern der Netzwerkswitches über Änderungen informiert werden.

Wenn Ihr Switch nicht aufgeführt ist, wenden Sie sich an den Anbieter Ihres Switches, um sicherzustellen, dass das Modell und die Betriebssystemversion des Switches die Anforderungen im nächsten Abschnitt unterstützen.

## <a name="network-switch-requirements"></a>Anforderungen an Netzwerkswitches

In diesem Abschnitt werden die Branchenstandards aufgelistet, die Netzwerkswitches einhalten müssen, wenn sie in Azure Stack HCI-Bereitstellungen verwendet werden. Diese Standards tragen dazu bei, eine zuverlässige Kommunikation zwischen Knoten in Azure Stack HCI-Clusterbereitstellungen zu gewährleisten.

> [!NOTE]
> Für Netzwerkadapter, die für Compute-, Speicher- und Verwaltungsdatenverkehr verwendet werden, ist Ethernet erforderlich. Weitere Informationen finden Sie unter [Anforderungen für Hostnetzwerke](host-network-requirements.md).

Im Folgenden finden Sie die obligatorischen IEEE-Standards und -Spezifikationen:

### <a name="standard-ieee-8021q"></a>Standard: IEEE 802.1Q

Ethernet-Switches müssen der IEEE 802.1Q-Spezifikation entsprechen, die zum Definieren von VLANs dient. VLANs sind für verschiedene Aspekte von Azure Stack HCI erforderlich und werden in allen Szenarien benötigt.

### <a name="standard-ieee-8021qbb"></a>Standard: IEEE 802.1Qbb

Ethernet-Switches müssen der IEEE 802.1Qbb-Spezifikation entsprechen, die zum Definieren der Prioritätsflusssteuerung (Priority Flow Control, PFC) dient. PFC ist bei Verwendung von Data Center Bridging (DCB) erforderlich. Da DCB sowohl in RoCE- als auch in iWARP RDMA-Szenarien verwendet werden kann, ist 802.1Qbb in allen Szenarien erforderlich. Es werden mindestens drei CoS-Prioritäten (Class of Service, Dienstklasse) benötigt, ohne die Switchfunktionen oder Portgeschwindigkeiten zu herabzustufen. Mindestens eine dieser Datenverkehrsklassen muss eine verlustfreie Kommunikation bieten.

### <a name="standard-ieee-8021qaz"></a>Standard: IEEE 802.1Qaz

Ethernet-Switches müssen der IEEE 802.1Qaz-Spezifikation entsprechen, die zum Definieren der erweiterten Übertragungsauswahl (Enhanced Transmission Selection, ETS) dient. ETS ist bei Verwendung von DCB erforderlich. Da DCB sowohl in RoCE- als auch in iWARP RDMA-Szenarien verwendet werden kann, ist 802.1Qaz in allen Szenarien erforderlich. Es werden mindestens drei CoS-Prioritäten benötigt, ohne die Switchfunktionen oder die Portgeschwindigkeit herabzustufen.

> [!NOTE]
> Eine hyperkonvergente Infrastruktur ist stark von Ost-West-Layer-2-Kommunikation im gleichen Rack abhängig und erfordert daher ETS. Microsoft testet Azure Stack HCI nicht mit DSCP (Differentiated Services Code Point). 

### <a name="standard-ieee-8021ab"></a>Standard: IEEE 802.1AB

Ethernet-Switches müssen der IEEE 802.1AB-Spezifikation entsprechen, die zum Definieren des Verbindungsschichterkennungsprotokolls (Link Layer Discovery Protocol, LLDP) dient. LLDP ist für Azure Stack HCI erforderlich und unterstützt das Teaming zum Ermitteln der Switchkonfiguration.

Die Konfiguration der LLDP-TLVs (Type-Length-Values) muss dynamisch aktiviert werden. Diese Switches dürfen abgesehen von der Aktivierung eines bestimmten TLV keine zusätzliche Konfiguration erfordern. Wenn also beispielsweise der Untertyp 3 von 802.1 aktiviert wird, müssen automatisch alle VLANs angekündigt werden, die an Switchports verfügbar sind.

### <a name="custom-tlv-requirements"></a>Anforderungen an benutzerdefinierte TLVs

Mit LLDP können Organisationen eigene benutzerdefinierte TLVs definieren und codieren. Diese werden als organisationsspezifische TLVs bezeichnet. Alle organisationsspezifischen TLVs beginnen mit dem LLDP-TLV-Typwert 127. Der folgenden Tabelle können Sie entnehmen, welche organisationsspezifischen benutzerdefinierten TLV-Untertypen (TLV-Typ 127) erforderlich sind:

|Erforderliche Version|Organization|TLV-Untertyp|
|-----|-----|-----|-----|
|20H2 und höher|IEEE 802.1|VLAN-Name (Untertyp = 3)|
|20H2 und höher|IEEE 802.3|Maximale Framegröße (Untertyp = 4)|

## <a name="network-traffic-and-architecture"></a>Netzwerkdatenverkehr und -architektur

Dieser Abschnitt richtet sich in erster Linie an Netzwerkadministratoren.

Azure Stack HCI kann bei verschiedenen Rechenzentrumsarchitekturen funktionieren, einschließlich solchen mit 2 Ebenen (Spine-Leaf) und 3 Ebenen (Core-Aggregation-Access). Dieser Abschnitt behandelt eher Konzepte der Spine-Leaf-Topologie, die häufig für Workloads in einer hyperkonvergenten Infrastruktur wie Azure Stack HCI verwendet wird.

## <a name="network-models"></a>Netzwerkmodelle

Der Netzwerkdatenverkehr kann anhand seiner Richtung klassifiziert werden. In herkömmlichen SAN-Umgebungen (Storage Area Network) fließt der Datenverkehr vorwiegend in Nord-Süd-Richtung von einer Computeebene über eine Layer-3-Grenze (IP) zu einer Speicherebene. Bei der hyperkonvergenten Infrastruktur herrscht die Ost-West-Richtung vor, wobei ein erheblicher Teil des Datenverkehrs innerhalb von Layer-2-Grenzen (VLAN) erfolgt.

> [!IMPORTANT]
>Es wird dringend empfohlen, dass sich alle Clusterknoten physisch im gleichen Rack befinden und mit denselben ToR-Switches (Top-of-Rack) verbunden sind.

### <a name="north-south-traffic-for-azure-stack-hci"></a>Nord-Süd-Datenverkehr für Azure Stack HCI

Nord-Süd-Datenverkehr weist die folgenden Eigenschaften auf:

- Der Datenverkehr wird von einem ToR-Switch zum Spine oder vom Spine zu einem ToR-Switch übertragen.
- Der Datenverkehr verlässt das physische Rack oder überschreitet eine Layer-3-Grenze (IP).
- Umfasst Verwaltungs- (PowerShell, Windows Admin Center), Compute- (VM) und standortübergreifenden Stretched Cluster-Datenverkehr.
- Verwendet einen Ethernet-Switch für die Konnektivität mit dem physischen Netzwerk.

### <a name="east-west-traffic-for-azure-stack-hci"></a>Ost-West-Datenverkehr für Azure Stack HCI

Ost-West-Datenverkehr weist die folgenden Eigenschaften auf:

- Der Datenverkehr bleibt hinter den ToR-Switches und innerhalb der Layer-2-Grenzen (VLAN).
- Umfasst Speicherdatenverkehr oder Datenverkehr für die Livemigration zwischen Knoten im gleichen Cluster und (bei Verwendung eines Stretched Cluster) innerhalb desselben Standorts.
- Kann einen Ethernet-Switch (mit Switch) oder eine direkte Verbindung (ohne Switch) verwenden, wie in den nächsten beiden Abschnitten beschrieben.

## <a name="using-switches"></a>Verwenden von Switches

Für den Nord-Süd-Datenverkehr ist die Verwendung von Switches erforderlich. Neben der Verwendung eines Ethernet-Switches, der die erforderlichen Protokolle für Azure Stack HCI unterstützt, ist der wichtigste Aspekt die richtige Dimensionierung des Netzwerkfabrics.

Sie müssen unbedingt die „nicht blockierende“ Fabric-Bandbreite kennen, die Ihre Ethernet-Switches unterstützen. Eine Überzeichnung des Netzwerks muss minimiert (oder besser ganz vermieden) werden.

Allgemeine Staufallen und Überzeichnung wie bei einer zur Pfadredundanz verwendeten [Multi-Chassis Link Aggregation Group](https://en.wikipedia.org/wiki/Multi-chassis_link_aggregation_group) können durch eine geeignete Verwendung von Subnetzen und VLANs vermieden werden. Siehe auch [Anforderungen für Hostnetzwerke](host-network-requirements.md).

Arbeiten Sie mit Ihrem Netzwerkanbieter oder Netzwerksupportteam zusammen, um sicherzustellen, dass Ihre Netzwerkswitches für die beabsichtigte Arbeitsauslastung ordnungsgemäß dimensioniert sind.

## <a name="using-switchless"></a>Ohne Switches

Azure Stack HCI unterstützt Verbindungen ohne Switches (direkte Verbindungen) für den Ost-West-Datenverkehr bei Clustern beliebiger Größe, sofern jeder Knoten im Cluster über redundante Verbindungen mit allen Knoten im Cluster verfügt. Dies wird als „Full-Mesh-Verbindung“ bezeichnet.

> [!NOTE]
>Die Vorteile von switchlosen Bereitstellungen werden aufgrund der Anzahl von erforderlichen Netzwerkadaptern bei Clustern eingebüßt, die mehr als drei Knoten umfassen.

### <a name="advantages-of-switchless-connections"></a>Vorteile von switchlosen Verbindungen

- Für den Ost-West-Datenverkehr muss kein Switch erworben werden. Ein Switch ist für den Nord-Süd-Datenverkehr erforderlich. Dies kann zu niedrigeren Investitionskosten führen, hängt jedoch von der Anzahl der Knoten im Cluster ab.
- Da es keinen Switch gibt, ist die Konfiguration auf den Host beschränkt, wodurch die Anzahl potenziell erforderlicher Konfigurationsschritte verringert werden kann. Dieser Wert verringert sich mit zunehmender Clustergröße.

### <a name="disadvantages-of-switchless-connections"></a>Nachteile von switchlosen Verbindungen

- Je höher die Anzahl der Knoten im Cluster, desto eher können die Kosten für die Netzwerkadapter die Kosten bei Verwendung von Netzwerkswitches überschreiten.
- IP- und Subnetzadressierungsschemas sind mit einem höheren Planungsaufwand verbunden.
- Sie bieten nur lokalen Speicherzugriff. Der VM-Datenverkehr, der Verwaltungsdatenverkehr und sonstiger Datenverkehr, der Nord-Süd-Zugriff benötigt, können diese Adapter nicht nutzen.
- Eine Skalierung von Clustern über drei Knoten hinaus ist im Allgemeinen nicht optimal.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die Anforderungen an Netzwerkadapter und Hosts. Weitere Informationen finden Sie unter [Anforderungen für Hostnetzwerke](host-network-requirements.md).
- Frischen Sie Ihre Grundlagenkenntnisse zu Failoverclustering auf. Siehe [Failover Clustering Networking Basics and Fundamentals](https://techcommunity.microsoft.com/t5/failover-clustering/.failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09) (Failoverclustering-Netzwerkgrundlagen).
- Frischen Sie Ihre Grundlagenkenntnisse zur Verwendung von SET auf. Siehe [Direkter Remotezugriff auf den Arbeitsspeicher (Remote Direct Memory Access, RDMA) und SET (Switch Embedded Teaming)](https://docs.microsoft.com/windows-server/virtualization/.hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Informationen zur Bereitstellung finden Sie unter [Erstellen eines Clusters mithilfe von Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Informationen zur Bereitstellung finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).