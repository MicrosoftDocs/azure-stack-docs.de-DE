---
title: Konzepte – Netzwerke in Azure Kubernetes Service (AKS) auf Azure Stack HCI
description: Erfahren Sie mehr über Netzwerke in Azure Kubernetes Service (AKS) auf Azure Stack HCI, einschließlich statischer IP- und DHCP-Netzwerke und Lastenausgleichsmodule.
author: abha
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: abha
ms.custom: fasttrack-edit
ms.openlocfilehash: a8e96783be3228aae525027855c1498b0269bd57
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874443"
---
# <a name="network-concepts-for-deploying-azure-kubernetes-service-aks-on-azure-stack-hci"></a>Netzwerkkonzepte für das Bereitstellen von Azure Kubernetes Service (AKS) auf Azure Stack HCI

In einem containergestützten Microservice-Ansatz für die Anwendungsentwicklung müssen die Anwendungskomponenten zusammenarbeiten, um ihre Aufgaben zu verarbeiten. Kubernetes stellt verschiedene Ressourcen bereit, die diese Anwendungskommunikation ermöglichen. Sie können eine Verbindung mit Anwendungen herstellen und diese intern oder extern verfügbar machen. Zum Erstellen hochverfügbarer Anwendungen können Sie für Ihre Anwendungen einen Lastenausgleich vornehmen. 

In diesem Artikel werden die wichtigsten Konzepte vorgestellt, mit denen Sie Netzwerke für Ihre Anwendungen in AKS auf Azure Stack HCI bereitstellen. Anschließend werden die verschiedenen Bereitstellungsmodelle und Beispiele für die Einrichtung der optimalen Netzwerkinfrastruktur für Ihre lokale AKS-Bereitstellung erläutert.

## <a name="kubernetes-on-azure-stack-hci-basics"></a>Grundlagen von Kubernetes auf Azure Stack HCI

Um den Zugriff auf Ihre Anwendungen oder die gegenseitige Kommunikation von Anwendungskomponenten zu erlauben, stellt Kubernetes eine Abstraktionsschicht für virtuelle Netzwerke bereit. Kubernetes-Knoten sind mit einem virtuellen Netzwerk verbunden und können eingehende und ausgehende Konnektivität für Pods bereitstellen. Die Komponente *kube-proxy*, die auf jedem Knoten ausgeführt wird, stellt diese Netzwerkfunktionen bereit.

In Kubernetes werden Pods von *Diensten* logisch gruppiert, um den direkten Zugriff über eine IP-Adresse oder einen DNS-Namen und an einem bestimmten Port zu erlauben. Sie können Datenverkehr auch über einen *Lastenausgleich* verteilen. 

Die Azure Stack HCI-Plattform hilft auch, virtuelle Netzwerke für AKS in Azure Stack HCI-Clustern zu vereinfachen. Wenn Sie einen Cluster in AKS auf Azure Stack HCI erstellen, wird eine zugrunde liegende `HAProxy`-Lastenausgleichsressource erstellt und konfiguriert. Beim Erstellen von Anwendungen auf der Grundlage Ihrer Kubernetes-Cluster werden IP-Adressen für Ihre Kubernetes-Dienste konfiguriert.

## <a name="aks-on-azure-stack-hci-resources"></a>AKS auf Azure Stack HCI-Ressourcen 

Zur Vereinfachung der Netzwerkkonfiguration für Anwendungsworkloads weist AKS auf Azure Stack HCI den folgenden Objekten in einer Bereitstellung IP-Adressen zu: 

- **Kubernetes-Cluster-API-Server**: Der API-Server ist eine Komponente der Kubernetes-Steuerungsebene, die die Kubernetes-API verfügbar macht. Der API-Server ist das Front-End für die Kubernetes-Steuerungsebene. API-Servern werden immer statische IP-Adressen zugewiesen, unabhängig vom zugrunde liegenden Netzwerkmodell.

- **Kubernetes-Knoten (auf virtuellen Computern)** : Ein Kubernetes-Cluster besteht aus einer Reihe von Workercomputern, die als Knoten bezeichnet werden, auf denen containerisierte Anwendungen ausgeführt werden. Jeder Cluster verfügt über mindestens einen Workerknoten. Für einen AKS auf Azure Stack HCI werden Kubernetes-Knoten in virtuellen Computern ausgeführt. Diese virtuellen Computer werden auf der Grundlage Ihres Azure Stack HCI-Clusters erstellt. 

- **Kubernetes-Dienste**: In Kubernetes werden Pods von *Diensten* logisch gruppiert, um den direkten Zugriff über eine IP-Adresse oder einen DNS-Namen und an einem bestimmten Port zu erlauben. Sie können Datenverkehr auch über einen *Lastenausgleich* verteilen. Kubernetes-Diensten werden immer statische IP-Adressen zugewiesen, unabhängig vom zugrunde liegenden Netzwerkmodell.

- **HAProxy-Lastenausgleichsmodule**: HAProxy (High Availability Proxy, Hochverfügbarkeitsproxy) ist ein TCP/HTTP-Lastenausgleichsmodul und Proxyserver, mit dem ein Webserver eingehende Anforderungen auf mehrere Endpunkte verteilen kann. Jeder Workloadcluster in AKS HCI verfügt über einen HAProxy-Lastenausgleich, der in einem eigenen spezialisierten virtuellen Computer ausgeführt wird.

- **Microsoft On-Premise Cloud-Dienst**: Dies ist der Azure Stack HCI-Cloudanbieter zum Ausführen von Kubernetes auf einem lokalen Azure Stack HCI-Cluster. Das Netzwerkmodell, das Ihr Azure Stack HCI-Cluster verwendet, bestimmt die Zuordnungsmethode IP-Adressen für dieses Objekt.


## <a name="virtual-networks"></a>Virtuelle Netzwerke

In AKS auf Azure Stack HCI werden virtuelle Netzwerke verwendet, um den Kubernetes-Ressourcen, die diese benötigen, wie beschrieben IP-Adressen zuzuordnen. Es gibt zwei Netzwerkmodelle, unter denen Sie wählen können, je nach der gewünschten AKS auf Azure Stack HCI-Netzwerkarchitektur. Beachten Sie, dass sich die hier definierte virtuelle Netzwerkarchitektur für Ihre AKS auf Azure Stack HCI-Bereitstellungen von der zugrunde liegenden physischen Netzwerkarchitektur in Ihrem Rechenzentrum unterscheidet.

- Statisches IP-Netzwerk: Das virtuelle Netzwerk ordnet dem API-Server des Kubernetes-Clusters, den Kubernetes-Knoten, den zugrunde liegenden VMs, Lastenausgleichsmodulen und allen Kubernetes-Diensten, die Sie auf der Grundlage Ihres Clusters ausführen, statische IP-Adressen zu.

- DHCP-Netzwerk: Das virtuelle Netzwerk ordnet den Kubernetes-Knoten, zugrunde liegenden VMs und Lastenausgleichsmodulen mithilfe eines DHCP-Servers dynamische IP-Adressen zu. Dem API-Server des Kubernetes-Clusters und allen Kubernetes-Diensten, die Sie auf der Grundlage Ihres Clusters ausführen, werden immer noch statische IP-Adressen zugeordnet.

### <a name="virtual-ip-pool"></a>Virtueller IP-Pool
Virtuelle IP-Pools (VIP-Pools) sind für AKS-Bereitstellungen auf Azure Stack HCI zwingend erforderlich. Der VIP-Pool ist ein Bereich von reservierten statischen IP-Adressen, die zum Zuordnen von IP-Adressen zum API-Server des Kubernetes-Clusters und externen IPs für Kubernetes-Dienste verwendet werden, um sicherzustellen, dass Ihre Anwendungen immer erreichbar sind. Unabhängig vom virtuellen Netzwerkmodell müssen Sie einen VIP-Pool für Ihre AKS-Hostbereitstellung zur Verfügung stellen. Die Anzahl der IP-Adressen im VIP-Pool hängt für statische und DHCP-Netzwerkmodelle von der Anzahl der Workloadcluster und Kubernetes-Dienste ab, die in der Bereitstellung ausgeführt werden. Der VIP-Pool unterscheidet sich jedoch je nach Netzwerkmodell in den folgenden Punkten:

- Statische IP-Adresse: Wenn Sie statische IP-Adressen verwenden, stellen Sie sicher, dass Ihre virtuellen IPs aus demselben Subnetz stammen. 
- DHCP: Wenn Ihr Netzwerk mit DHCP konfiguriert ist, müssen Sie mit Ihrem Netzwerkadministrator zusammenarbeiten und einen IP-Adressbereich reservieren, der für den VIP-Pool verwendet werden soll.

### <a name="kubernetes-node-ip-pool"></a>IP-Pool des Kubernetes-Knotens
Wie oben erläutert, werden Kubernetes-Knoten auf spezialisierten virtuellen Computern in einer AKS auf Azure Stack HCI-Bereitstellung ausgeführt. AKS auf Azure Stack HCI ordnet diesen virtuellen Computern IP-Adressen zu, um zwischen Kubernetes-Knoten zu kommunizieren. Bei einem DHCP-Netzwerkmodell müssen Sie keinen VM-Pool des Kubernetes-Knotens angeben, weil IP-Adressen für die Kubernetes-Knoten vom DHCP-Server in Ihrem Netzwerk dynamisch zugewiesen werden. Bei einem statischen IP-basierten Netzwerkmodell müssen Sie einen IP-Poolbereich des Kubernetes-Knotens angeben. Die Anzahl der IP-Adressen in diesem Bereich hängt von der Gesamtzahl der Kubernetes-Knoten auf Ihrem AKS-Host und den Workload-Kubernetes-Clustern ab.

### <a name="virtual-network-with-static-ip-networking"></a>Virtuelles Netzwerk mit statischem IP-Netzwerk

Dieses Netzwerkmodell erstellt ein virtuelles Netzwerk, das allen Objekten in der Bereitstellung statische IP-Adressen zugeordnet. Ein zusätzlicher Vorteil der Verwendung statischer IP-Netzwerke besteht darin, dass langlebige Bereitstellungen und Anwendungsworkloads garantiert immer erreichbar sind. Es wird empfohlen, ein statisches virtuelles IP-Netzwerkmodell für Ihre AKS in Azure Stack HCI-Bereitstellung zu erstellen.

Beim Definieren eines virtuellen Netzwerks mit statischen IP-Konfigurationen müssen Sie die folgenden Parameter angeben:

- Name: Der Name Ihres virtuellen Netzwerks.
- AddressPrefix: Das IP-Adresspräfix, das für Ihr Subnetz verwendet werden soll.
- Gateway: Die IP-Adresse des Standardgateways für das Subnetz.
- DNS-Server: Ein Array von IP-Adressen, die auf die DNS-Server verweisen, die für das Subnetz verwendet werden sollen. Mindestens ein und maximal drei Server können bereitgestellt werden.
- IP-Pool des Kubernetes Knotens: Der fortlaufende Bereich von IP-Adressen, die für VMs des Kubernetes-Knotens verwendet werden sollen.
- Pool virtueller IP-Adressen: Der fortlaufende Bereich von IP-Adressen, die für den API-Server des Kubernetes-Clusters und die Kubernetes-Dienste verwendet werden, die eine externe IP-Adresse erfordern.
- vLAN-ID: Die vLAN-ID für das virtuelle Netzwerk. Wenn keine Angabe erfolgt, wird das virtuelle Netzwerk nicht markiert.

### <a name="virtual-network-with-dhcp-networking"></a>Virtuelles Netzwerk mit DHCP-Netzwerk

Dieses Netzwerkmodell erstellt ein virtuelles Netzwerk, das allen Objekten in der Bereitstellung statische IP-Adressen zugeordnet.  

Beim Definieren eines virtuellen Netzwerks mit statischen IP-Konfigurationen müssen Sie die folgenden Parameter angeben:

- Name: Der Name Ihres virtuellen Netzwerks.
- Pool virtueller IP-Adressen: Der fortlaufende Bereich von IP-Adressen, die für den API-Server des Kubernetes-Clusters und die Kubernetes-Dienste verwendet werden, die eine externe IP-Adresse erfordern.
- vLAN-ID: Die vLAN-ID für das virtuelle Netzwerk. Wenn keine Angabe erfolgt, wird das virtuelle Netzwerk nicht markiert.

## <a name="microsoft-on-premises-cloud-service"></a>Microsoft On-Premise Cloud-Dienst

Microsoft On-Premise Cloud (MOC) ist ein neuer Verwaltungsstapel, mit dem virtuelle Computer in Azure Stack HCI und auf Windows Server basierenden SDDC-Clouds verwaltet werden können. MOC umfasst Folgendes:

- Eine einzelne Instanz eines hochverfügbaren `cloud agent`-Diensts, der in einem Cluster bereitgestellt wird. Dieser Agent wird auf einem beliebigen Knoten im Azure Stack HCI-Cluster ausgeführt. 
- Einen `node agent`, der auf jedem physischen Azure Stack HCI-Knoten ausgeführt wird. 

`-cloudserviceCIDR` ist ein Parameter im Befehl `Set-AksHciConfig`, der verwendet wird, um eine virtuelle IP-Adresse zuzuweisen, um sicherzustellen, dass ein hochverfügbarer Cloud-Agent-Dienst vorhanden ist.

Die Zuordnung einer IP-Adresse für den MOC-Dienst hängt vom zugrunde liegenden Netzwerkmodell an, das die Azure Stack HCI-Clusterbereitstellung verwendet. **Die IP-Adresszuordnung für den MOC-Dienst ist unabhängig vom virtuellen Netzwerkmodell und hängt NUR vom zugrunde liegenden physischen Netzwerk ab, das IP-Adressen an die Azure Stack HCI-Clusterknoten in Ihrem Rechenzentrum vergibt.**

### <a name="azure-stack-hci-cluster-nodes-with-a-dhcp-based-ip-address-allocation-model"></a>Azure Stack HCI-Clusterknoten mit einem DHCP-basierten IP-Adresszuordnungsmodell

Wenn Ihrem Azure Stack HCI-Knoten eine IP-Adresse von einem DHCP-Server im physischen Netzwerk zugewiesen wird, müssen Sie für den MOC-Dienst nicht explizit eine IP-Adresse bereitstellen.

### <a name="azure-stack-hci-cluster-nodes-with-a-static-ip-allocation-model"></a>Azure Stack HCI-Clusterknoten mit einem statischen IP-Adresszuordnungsmodell 

Wenn Ihrem Azure Stack HCI-Clusterknoten statische IP-Adressen zugewiesen werden, müssen Sie explizit eine IP-Adresse für den MOC-Clouddienst angeben. Diese IP-Adresse für den MOC-Dienst muss sich im gleichen Subnetz wie die Azure Stack HCI-Clusterknoten befinden. Verwenden Sie zum expliziten Zuweisen einer IP-Adresse für den MOC-Dienst den Parameter `-cloudserviceCIDR` im Befehl `Set-AksHciConfig`. Stellen Sie sicher, dass Sie eine IP-Adresse im CIDR-Format eingeben, z. B. „10.11.23.45/16“.

## <a name="compare-network-models"></a>Vergleich der Netzwerkmodelle

Sowohl DHCP- als auch statische IP-Adressen stellen Netzwerkkonnektivität für Ihre AKS in Azure Stack HCI-Bereitstellung bereit. Es gibt jedoch jeweils Vor- und Nachteile. Ganz allgemein gelten die folgenden Überlegungen:

* **DHCP**
    - Garantiert keine langlebigen IP-Adressen für einige Ressourcentypen in einer AKS in Azure Stack HCI-Bereitstellung.
    - Unterstützt die Erweiterung reservierter DHCP-IP-Adressen, wenn die Bereitstellung größer als die ursprünglich erwartete wird.
    
* **Statische IP**
    - Garantiert langlebige IP-Adressen für alle Ressourcen in einer AKS auf Azure Stack HCI-Bereitstellung.
    - Da die automatische Erweiterung des IP-Pools des Kubernetes-Knotens nicht unterstützt wird, können Sie möglicherweise keine neuen Cluster erstellen, wenn Sie den IP-Pool des Kubernetes-Knotens ausgeschöpft haben.


In der folgenden Tabelle wird die IP-Adresszuordnung für Ressourcen zwischen statischen IP- und DHCP-Netzwerkmodellen verglichen:

| Funktion                                                                                   | Statische IP   | DHCP |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| API-Server des Kubernetes-Clusters                                             | Statisch mit VIP-Pool zugewiesen | Statisch mit VIP-Pool zugewiesen |
| Kubernetes-Knoten (auf virtuellen Computern)                                                                         | Mithilfe des IP-Pools des Kubernetes-Knotens zugewiesen | Dynamisch zugewiesen |
| Kubernetes-Dienste                                          | Statisch mit VIP-Pool zugewiesen | Statisch mit VIP-Pool zugewiesen |
| VM für HAProxy-Lastenausgleich                                          | Mithilfe des IP-Pools des Kubernetes-Knotens zugewiesen | Dynamisch zugewiesen |
| Microsoft On-Premise Cloud-Dienst                                               | Hängt von der physischen Netzwerkkonfiguration für Azure Stack HCI-Clusterknoten ab. | Hängt von der physischen Netzwerkkonfiguration für Azure Stack HCI-Clusterknoten ab. |
| VIP-Pool                                             | Obligatorisch. | Obligatorisch. |
| IP-Pool des Kubernetes-Knotens | Obligatorisch. | Nicht unterstützt |


## <a name="minimum-ip-address-reservations-for-an-aks-on-azure-stack-hci-deployment"></a>Mindestens zu reservierende IP-Adressen für eine AKS auf Azure Stack HCI-Bereitstellung
Unabhängig von Ihrem Bereitstellungsmodell bleibt die Anzahl der reservierten IP-Adressen gleich. In diesem Abschnitt wird die Anzahl der IP-Adressen beschrieben, die basierend auf Ihrem AKS auf Azure Stack HCI-Bereitstellungsmodell reserviert werden müssen.

### <a name="minimum-ip-address-reservation"></a>Mindestens zu reservierende IP-Adressen

Sie sollten mindestens die folgende Anzahl von IP-Adressen für Ihre Bereitstellung reservieren:

| Clustertyp  | Knoten der Steuerungsebene | Workerknoten | Für Aktualisierungsvorgänge | Load Balancer  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AKS-Host |  1 IP-Adresse |  Nicht verfügbar  |  2 IP-Adressen |  Nicht verfügbar  |
| Workloadcluster  |  1 IP-Adresse pro Knoten  | 1 IP-Adresse pro Knoten |  5 IP-Adressen  |  1 IP-Adresse |

Zusätzlich sollten Sie die folgende Anzahl von IP-Adressen für Ihren VIP-Pool reservieren:

| Ressourcentyp  | Anzahl von IP-Adressen 
| ------------- | ------------------
| Cluster-API-Server |  1 pro Cluster 
| Kubernetes-Dienste  |  1 pro Dienst  

Wie Sie sehen können, ist die Anzahl der erforderlichen IP-Adressen variabel, je nach AKS auf Azure Stack HCI-Architektur und der Anzahl der Dienste, die Sie auf Ihrem Kubernetes-Cluster ausführen. Es wird empfohlen, für Ihre Bereitstellung insgesamt 256 IP-Adressen (/24-Subnetz) zu reservieren.

### <a name="walking-through-an-example-deployment"></a>Details einer Beispielbereitstellung

Jane ist ein IT-Administrator und fängt gerade mit der Verwendung von AKS auf Azure Stack HCI an. Sie möchte 2 Kubernetes-Cluster (Kubernetes-Cluster A und Kubernetes-Cluster B) in ihrem Azure Stack HCI-Cluster bereitstellen. Sie möchte auch eine Abstimmungsanwendung auf der Grundlage Ihres Clusters ausführen. Diese Anwendung verfügt über drei Instanzen der Front-End-Benutzeroberfläche, die über die beiden Cluster und eine Instanz der Back-End-Datenbank ausgeführt werden.

- Kubernetes-Cluster A verfügt über drei Knoten der Steuerungsebene und fünf Workerknoten.
- Kubernetes-Cluster B verfügt über einen Knoten der Steuerungsebene und drei Workerknoten.
- Drei Instanzen der Front-End-Benutzeroberfläche (Port 443)
- Eine Instanz der Back-End-Datenbank (Port 80)

Basierend auf der Tabelle oben muss sie Folgendes reservieren:

- Drei IP-Adressen für ihren AKS-Host (eine IP-Adresse für den Knoten der Steuerungsebene und zwei IP-Adressen zum Ausführen von Aktualisierungsvorgängen)
- Drei IP-Adressen für die Knoten der Steuerungsebene in Cluster A (eine IP-Adresse pro Knoten der Steuerungsebene)
- Fünf IP-Adressen für ihre Workerknoten in Cluster A (eine IP-Adresse pro Knoten der Steuerungsebene)
- Sechs IP-Adressen zusätzlich für Cluster A (fünf IP-Adressen zum Ausführen von Aktualisierungsvorgängen und eine IP-Adresse für Lastenausgleich)
- Eine IP-Adresse für die Knoten der Steuerungsebene in Cluster B (eine IP-Adresse pro Knoten der Steuerungsebene)
- Drei IP-Adressen für ihre Workerknoten in Cluster B (eine IP-Adresse pro Knoten der Steuerungsebene)
- Sechs IP-Adressen zusätzlich für Cluster B (fünf IP-Adressen zum Ausführen von Aktualisierungsvorgängen und eine IP-Adresse für Lastenausgleich)
- Zwei IP-Adressen für ihre Kubernetes-Cluster-API-Server (eine IP-Adresse pro Kubernetes-Cluster)
- Drei IP-Adressen für ihren Kubernetes-Dienst (eine IP-Adresse pro Instanz der Front-End-Benutzeroberfläche, da sie alle denselben Port verwenden. Die Back-End-Datenbank kann eine der drei IP-Adressen verwenden, da sie einen anderen Port verwendet.)

Wie bereits erwähnt, benötigt Jane insgesamt 32 IP-Adressen. Jane sollte daher ein /26-Subnetz für ihr virtuelles Netzwerk reservieren. 

### <a name="splitting-reserved-ip-addresses-based-on-a-static-ip-network-model"></a>Aufteilen reservierter IP-Adressen basierend auf einem statischen IP-Netzwerkmodell

Während die Gesamtzahl der reservierten IP-Adressen gleich bleibt, bestimmt das Bereitstellungsmodell, wie diese IP-Adressen auf IP-Gruppen aufgeteilt werden. Wie bereits erläutert, verfügt das statische IP-Netzwerkmodell über zwei IP-Pools:

- IP-Pool des Kubernetes Knotens: Für VMs des Kubernetes-Knotens und die Lastenausgleichs-VM. Dieser IP-Pool enthält auch die IP-Adresse, die zum Ausführen von Aktualisierungsvorgängen erforderlich ist.
- Virtueller IP-Pool: Für den Kubernetes-API-Server und Kubernetes-Dienste.

Ausgehend vom Beispiel oben, muss Jane diese IP-Adressen weiter auf VIP-Pools und IP-Pools des Kubernetes-Knotens aufteilen:
- Fünf (zwei für Kubernetes-Cluster-API-Server und drei für Kubernetes-Dienste) der 32 IP-Adressen für ihren VIP-Pool.
- 27 (alle IP-Adressen für ihre Kubernetes-Knoten und zugrunde liegenden VMs, Lastenausgleichs-VMs und Aktualisierungsvorgänge) für ihren IP-Pool des Kubernetes-Knotens.

### <a name="splitting-reserved-ip-addresses-based-on-a-dhcp-network-model"></a>Aufteilen reservierter IP-Adressen basierend auf einem statischen DHCP-Netzwerkmodell

Während die Gesamtzahl der reservierten IP-Adressen gleich bleibt, bestimmt das Bereitstellungsmodell, wie diese IP-Adressen auf IP-Gruppe(n) aufgeteilt werden. Wie bereits erläutert, verfügt das DHCP-Netzwerkmodell über einen IP-Pool:

- Virtueller IP-Pool: Für den Kubernetes-API-Server und Kubernetes-Dienste.

Ausgehend vom Beispiel oben:
- Jane muss insgesamt 32 IP-Adressen oder ein /26-Subnetz auf dem DHCP-Server reservieren. 
- Sie muss fünf (zwei für Kubernetes-Cluster-API-Server und drei für Kubernetes-Dienste) der 32 IP-Adressen für ihren VIP-Pool reservieren. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die wichtigsten Konzepte kennengelernt, mit denen Sie Netzwerke für Ihre Anwendungen in AKS auf Azure Stack HCI bereitstellen. Als Nächstes haben Sie folgende Möglichkeiten:

- [Konfigurieren von Proxyeinstellungen für AKS auf Azure Stack HCI](./proxy.md)
- [Systemanforderungen](./system-requirements.md)

