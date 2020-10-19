---
title: Software Load Balancer (SLB) für SDN in Azure Stack HCI
description: In diesem Thema erfahren Sie mehr über den Software Load Balancer für SDN (Software-Defined Networking) in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: 79588b25f76540aed1499123f7d39e4d57cf9ecd
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858979"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>Was ist Software Load Balancer \(SLB\) für SDN?

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Clouddienstanbieter (Cloud Service Providers, CSPs) und Unternehmen, die [Software-Defined Networking (SDN) in Azure Stack HCI](software-defined-networking.md) bereitstellen, können Software Load Balancer (SLB) verwenden, um den Netzwerkdatenverkehr zwischen Mandanten und zwischen Mandanten und Kunden gleichmäßig auf virtuelle Netzwerkressourcen zu verteilen. SLB ermöglicht es Ihnen, mehrere Server zum Hosten derselben Workload zu aktivieren, um hohe Verfügbarkeit und Skalierbarkeit bereitzustellen.

Software Load Balancer umfasst die folgenden Funktionen:

- Layer 4-Load Balancing-Dienste (L4) für TCP/UDP-Datenverkehr in Nord/Süd- und Ost/West-Richtung.

- Lastenausgleich des Datenverkehrs im öffentlichen Netzwerk und im internen Netzwerk.

- Unterstützung dynamischer IP-Adressen (DIPs) in virtuellen lokalen Netzwerken (VLANs) und in virtuellen Netzwerken, die Sie mithilfe der Hyper-V-Netzwerkvirtualisierung erstellen.

- Unterstützung für Integritätstests.

- Bereit für Cloudskalierung, einschließlich Funktionen für horizontales Hochskalieren und zentrales Hochskalieren für Multiplexer und Host-Agents.

- Ein mehrinstanzenfähiger einheitlicher Edge durch nahtlose Integration in SDN-Technologien wie RAS-Gateway, Rechenzentrumsfirewall und Routenreflektor.

Weitere Informationen finden Sie unter [Funktionen des Software Load Balancers](#bkmk_features) in diesem Thema.

> [!NOTE]
> Mehrinstanzenfähigkeit für VLANs wird vom Netzwerkcontroller nicht unterstützt. Allerdings können Sie VLANs mit SLB für vom Dienstanbieter verwaltete Workloads verwenden, z. B. die Rechenzentrumsinfrastruktur und Webserver mit hoher Dichte.

Mithilfe von Software Load Balancer können Sie Ihre Lastenausgleichsfunktionen mithilfe von virtuellen SLB-Computern (VMs) auf denselben Hyper-V-Compute-Servern, die Sie für Ihre anderen VM-Workloads verwenden, horizontal hochskalieren. Aus diesem Grund unterstützt der Software Load Balancer das schnelle Erstellen und Löschen von Lastenausgleichsendpunkten, das für den CSP-Betrieb erforderlich ist. Außerdem unterstützt der Software Load Balancer mehrere zehn Gigabyte pro Cluster, bietet ein einfaches Bereitstellungsmodell und lässt sich problemlos horizontal hoch- und herunterskalieren.

## <a name="how-software-load-balancer-works"></a>Funktionsweise von Software Load Balancer

Software Load Balancer funktioniert, indem virtuelle IP-Adressen (VIPs) zu DIPs zugeordnet werden, die Teil eines Clouddienstsatzes von Ressourcen im Rechenzentrum sind.

VIPs sind einzelne IP-Adressen, die öffentlichen Zugriff auf einen Pool von virtuellen Computern mit Lastenausgleich ermöglichen. VIPs sind z. B. IP-Adressen, die über das Internet zugänglich gemacht werden, sodass Mandanten und Kunden von Mandanten eine Verbindung mit Mandantenressourcen im Cloudrechenzentrum herstellen können.

DIPs sind die IP-Adressen der virtuellen Mitgliedscomputer eines Pools mit Lastenausgleich hinter der VIP. DIPs werden den Mandantenressourcen innerhalb der Cloudinfrastruktur zugewiesen.

VIPs befinden sich im SLB-Multiplexer (MUX).  Der MUX besteht aus einem oder mehreren virtuellen Computern.  Der Netzwerkcontroller stellt jedem MUX jede VIP zur Verfügung, und jeder MUX verwendet wiederum das Border Gateway Protocol (BGP), um den Routern im physischen Netzwerk jede VIP als /32-Route anzukündigen.  BGP ermöglicht den physischen Netzwerkroutern Folgendes:

- Erfahren, dass eine VIP-Adresse an jedem MUX verfügbar ist, auch wenn sich die MUXe in unterschiedlichen Subnetzen in einem Layer 3-Netzwerk befinden.

- Verteilen der Last für jede VIP über alle verfügbaren MUXe mithilfe von ECMP-Routing (Equal Cost Multi-Path).

- Automatisches Erkennen eines MUX-Fehlers oder der Entfernung eines MUX sowie das Beenden des Sendens von Datenverkehr an den fehlgeschlagenen MUX.

- Verteilen der Last vom fehlerhaften oder entfernten MUX auf die fehlerfreien MUXe.

Wenn öffentlicher Datenverkehr aus dem Internet eingeht, untersucht der SLB-MUX den Datenverkehr, der die VIP als Ziel enthält, ordnet den Datenverkehr zu und schreibt ihn so neu, dass er bei einer einzelnen DIP ankommt. Für eingehenden Netzwerkdatenverkehr wird diese Transaktion in einem zweistufigen Prozess durchgeführt, der zwischen den virtuellen MUX-Computern und dem Hyper-V-Host, auf dem sich die Ziel-DIP befindet, aufgeteilt wird:

1. Lastenausgleich: Der MUX verwendet die VIP, um eine DIP auszuwählen, kapselt das Paket und leitet den Datenverkehr an den Hyper-V-Host weiter, auf dem sich die DIP befindet.

2. Netzwerkadressübersetzung (Network Address Translation, NAT): Der Hyper-V-Host entfernt die Kapselung von dem Paket, übersetzt die VIP in eine DIP, ordnet die Ports neu zu und leitet das Paket an den virtuellen DIP-Computer weiter.

Der MUX weiß durch von Ihnen mithilfe des Netzwerkcontrollers definierten Lastenausgleichsrichtlinien, wie VIPs den richtigen DIPs zugeordnet werden. Diese Regeln umfassen Protokoll, Front-End-Port, Back-End-Port und den Verteilungsalgorithmus (5-, 3- oder 2-Tupel).

Wenn Mandanten-VMs antworten und ausgehenden Netzwerkdatenverkehr zurück an das Internet oder die Remotemandantenstandorte senden, weil die NAT vom Hyper-V-Host ausgeführt wird, umgeht der Datenverkehr den MUX und erreicht direkt vom Hyper-V-Host aus den Edgerouter. Dieser MUX-Umgehungsprozess wird als Direct Server Return (DSR) bezeichnet.

Nachdem der anfängliche Netzwerkdatenverkehrsfluss hergestellt wurde, umgeht der eingehende Netzwerkdatenverkehr den SLB-MUX vollständig.

In der folgenden Abbildung führt ein Clientcomputer eine DNS-Abfrage für die IP-Adresse einer SharePoint-Unternehmenssite aus – in diesem Fall ein fiktives Unternehmen mit dem Namen „Contoso“. Der folgende Prozess läuft ab:

1. Der DNS-Server gibt die VIP 107.105.47.60 an den Client zurück.

2. Der Client sendet eine HTTP-Anforderung an die VIP.

3. Das physische Netzwerk verfügt über mehrere Pfade, um die VIP zu erreichen, die sich auf einem beliebigen MUX befindet.  Jeder Router entlang des Wegs verwendet ECMP, um das nächste Segment des Pfads auszuwählen, bis die Anforderung bei einem MUX eintrifft.

4. Der MUX, der die Anforderung empfängt, überprüft konfigurierte Richtlinien und erkennt, dass zwei DIPS, 10.10.10.5 und 10.10.20.5, in einem virtuellen Netzwerk verfügbar sind, um die Anforderung an die VIP 107.105.47.60 zu verarbeiten.

5. Der MUX wählt die DIP 10.10.10.5 aus und kapselt die Pakete mithilfe von VXLAN, damit er sie mithilfe der physischen Netzwerkadresse des Hosts an den Host senden kann, auf dem sich die DIP-Adresse befindet.

6. Der Host empfängt das gekapselte Paket und überprüft es. Er entfernt die Kapselung und schreibt das Paket so neu, dass das Ziel jetzt die DIP 10.10.10.5 anstelle der VIP ist, und sendet dann den Datenverkehr an den virtuellen DIP-Computer.

7. Die Anforderung erreicht die SharePoint-Site „Contoso“ in der Serverfarm 2. Der Server generiert eine Antwort und sendet sie an den Client, wobei seine eigene IP-Adresse als Quelle verwendet wird.

8. Der Host fängt das ausgehende Paket im virtuellen Switch ab, in dem gespeichert ist, dass der Client, der nun das Ziel ist, die ursprüngliche Anforderung an die VIP gestellt hat.  Der Host schreibt die Quelle des Pakets als die VIP neu, sodass der Client die DIP-Adresse nicht sieht.

9. Der Host leitet das Paket direkt an das Standardgateway für das physische Netzwerk weiter, das seine Standardroutingtabelle verwendet, um das Paket weiter an den Client zu leiten, der schließlich die Antwort empfängt.

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="Software Load Balancer-Prozess" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>Lastenausgleich für internen Rechenzentrumsdatenverkehr

Beim Lastenausgleich des internen Netzwerkdatenverkehrs an das Rechenzentrum, z. B. zwischen Mandantenressourcen, die auf unterschiedlichen Servern ausgeführt werden und Mitglieder desselben virtuellen Netzwerks sind, führt der virtuelle Hyper-V-Switch, mit dem die virtuellen Computer verbunden sind, die NAT aus.

Beim Lastenausgleich des internen Datenverkehrs wird die erste Anforderung an den MUX gesendet und von diesem verarbeitet, wobei er die geeignete DIP auswählt und den Datenverkehr dann an die DIP weiterleitet. Ab diesem Zeitpunkt umgeht der hergestellte Datenverkehrsfluss den MUX und fließt direkt zwischen den VMs.

### <a name="health-probes"></a>Integritätstests

Software Load Balancer umfasst Integritätstests zum Überprüfen der Integrität der Netzwerkinfrastruktur, einschließlich der folgenden:

- TCP-Test an Port

- TCP-Test an Port und URL

Anders als bei einem herkömmlichen Lastenausgleichsgerät, bei dem der Test von dem Gerät stammt und über das Kabel an die DIP gesendet wird, stammt der SLB-Test von dem Host, auf dem sich die DIP befindet, und wird direkt vom SLB-Host-Agent zu der DIP gesendet, sodass die Arbeit noch weiter auf die Hosts verteilt wird.

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>Software Load Balancer-Infrastruktur
Bevor Sie Software Load Balancer konfigurieren können, müssen Sie zuerst den Netzwerkcontroller und mindestens einen virtuellen SLB-MUX-Computer bereitstellen.

Außerdem müssen Sie die Azure Stack HCI-Hosts mit dem SDN-fähigen virtuellen Hyper-V-Switch konfigurieren und sicherstellen, dass der SLB-Host-Agent ausgeführt wird. Die Router, die die Hosts versorgen, müssen ECMP-Routing und das Border Gateway Protocol (BGP) unterstützen, und Sie müssen so konfiguriert sein, dass BGP-Peeringanforderungen von den SLB-MUXen akzeptiert werden.

Die folgende Abbildung bietet eine Übersicht über die SLB-Infrastruktur.

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="Software Load Balancer-Prozess" border="false":::

Die folgenden Abschnitte enthalten weitere Informationen zu diesen Elementen der Software Load Balancer-Infrastruktur.

### <a name="network-controller"></a>Netzwerkcontroller
Der Netzwerkcontroller hostet den SLB-Manager und führt die folgenden Aktionen für den Software Load Balancer aus:

- Verarbeitung von SLB-Befehlen, die über die Northbound-API von Windows Admin Center, System Center, Windows PowerShell oder einer anderen Netzwerkverwaltungsanwendung eingehen.

- Berechnung der Richtlinie für die Verteilung an Azure Stack HCI-Hosts und SLB-MUXe.

- Bereitstellung des Integritätsstatus der Software Load Balancer-Infrastruktur.

Sie können das Windows Admin Center oder die Windows PowerShell verwenden, um Netzwerkcontroller und andere SLB-Infrastrukturen zu installieren und zu konfigurieren.

### <a name="slb-mux"></a>SLB-MUX
Der SLB-MUX verarbeitet den eingehenden Netzwerkdatenverkehr, ordnet VIPs zu DIPs zu und leitet den Datenverkehr anschließend an die richtige DIP weiter. Jeder MUX verwendet außerdem BGP zum Veröffentlichen von VIP-Routen an Edgerouter. BGP-Keep-Alive benachrichtigt MUXe, wenn ein MUX ausfällt. Dies ermöglicht es aktiven MUXen, die Last im Falle eines MUX-Ausfalls neu umzuverteilen. Dies bietet im Wesentlichen Lastenausgleich für die Load Balancer.

### <a name="slb-host-agent"></a>SLB-Host-Agent
Beim Bereitstellen des Software Load Balancers müssen Sie Windows Admin Center, System Center, Windows PowerShell oder eine andere Verwaltungsanwendung verwenden, um den SLB-Host-Agent jedem der Hostserver bereitzustellen.

Der SLB-Host-Agent lauscht auf SLB-Richtlinienaktualisierungen vom Netzwerkcontroller. Darüber hinaus programmiert der Host-Agent Regeln für SLB in die SDN-fähigen virtuellen Hyper-V-Switches, die auf dem lokalen Computer konfiguriert sind.

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>SDN-fähiger virtueller Hyper-V-Switch
Damit ein virtueller Switch mit SLB kompatibel ist, muss die VFP-Erweiterung (Virtual Filtering Platform) auf dem virtuellen Switch aktiviert sein. Dies erfolgt automatisch durch die PowerShell-Bereitstellungsskripts für SDN, den Bereitstellungs-Assistenten für Windows Admin Center und die System Center Virtual Machine Manager (SCVMM)-Bereitstellung.

Informationen zum Aktivieren von VFP auf virtuellen Switches finden Sie unter den Windows PowerShell-Befehlen [Get-VMSystemSwitchExtension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) und [Enable-VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps).

Der SDN-fähige virtuelle Hyper-V-Switch führt die folgenden Aktionen für den SLB aus:

- Verarbeitung des Datenpfads für den SLB.

- Empfangen eingehenden Netzwerkdatenverkehrs vom MUX.

- Umgehen des MUX für ausgehenden Netzwerkdatenverkehr und Senden dieses Datenverkehrs mithilfe von DSR an den Router.

### <a name="bgp-router"></a>BGP-Router
Der BGP-Router führt die folgenden Aktionen für den Software Load Balancer aus:

- Routing eingehenden Datenverkehrs mithilfe von ECMP an den MUX.

- Bei ausgehendem Netzwerkdatenverkehr Verwendung der vom Host bereitgestellten Route.

- Lauschen auf Routenaktualisierungen für VIPs vom SLB-MUX.

- Entfernen von SLB-MUXen aus der SLB-Rotation, wenn Keep-Alive fehlschlägt.

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>Funktionen des Software Load Balancers
In den folgenden Abschnitten werden einige der Features und Funktionen des Software Load Balancers beschrieben.

### <a name="core-functionality"></a>Kernfunktionalität

- Der SLB bietet Layer 4-Load Balancing-Dienste für TCP/UDP-Datenverkehr in Nord/Süd- und Ost/West-Richtung.

- Sie können den SLB in einem Netzwerk verwenden, das auf Hyper-V-Netzwerkvirtualisierung basiert.

- Sie können den SLB mit einem VLAN-basierten Netzwerk für virtuelle DIP-Computer verwenden, die mit einem SDN-fähigen virtuellen Hyper-V-Switch verbunden sind.

- Eine SLB-Instanz kann mehrere Mandanten verarbeiten.

- SLB und DIP unterstützen einen skalierbaren Rückgabepfad mit niedriger Latenz, wie er von DSR implementiert wird.

- SLB funktioniert, wenn Sie auch Switch Embedded Teaming (SET) oder Single Root Input/Output Virtualization (SR-IOV) verwenden.

- SLB umfasst Unterstützung für die Internetprotokollversionen 6 (IPv6) und 4 (IPv4).

- Für Site-to-Site-Gatewayszenarien bietet SLB NAT-Funktionen, um allen Site-to-Site-Verbindungen die Verwendung einer einzelnen öffentlichen IP-Adresse zu ermöglichen.

### <a name="scale-and-performance"></a>Skalierung und Leistung

- Bereit für Cloudskalierung, einschließlich Funktionen für horizontales Hochskalieren und zentrales Hochskalieren für MUXe und Host-Agents.

- Ein aktives SLB-Manager-Netzwerkcontrollermodul kann acht MUX-Instanzen unterstützen.

### <a name="high-availability"></a>Hochverfügbarkeit

- Sie können SLB auf mehr als zwei Knoten in einer Aktiv/Aktiv-Konfiguration bereitstellen.

- MUXe können dem MUX-Pool hinzugefügt und daraus entfernt werden, ohne dass sich dies auf den SLB-Dienst auswirkt. Hierdurch wird die SLB-Verfügbarkeit aufrechterhalten, wenn einzelne MUXe gepatcht werden.

- Einzelne MUX-Instanzen haben eine Uptime von 99 %.

- Systemüberwachungsdaten sind für Verwaltungsentitäten verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Netzwerkcontroller – Übersicht](network-controller-overview.md)
- [Bereitstellen des Netzwerkcontrollers mithilfe von Windows PowerShell](../deploy/network-controller-powershell.md)
- [SDN in Azure Stack HCI](software-defined-networking.md)
