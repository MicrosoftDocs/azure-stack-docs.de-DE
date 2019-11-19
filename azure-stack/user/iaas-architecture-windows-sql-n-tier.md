---
title: n-schichtige Windows-Anwendung in Azure Stack mit SQL Server | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine n-schichtige Windows-Anwendung in Azure Stack mit SQL Server ausführen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: ced042ac48017a8191d02e48de12e107677051fc
ms.sourcegitcommit: 8a74a5572e24bfc42f71e18e181318c82c8b4f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569301"
---
# <a name="windows-n-tier-application-on-azure-stack-with-sql-server"></a>n-schichtige Windows-Anwendung in Azure Stack mit SQL Server

Diese Referenzarchitektur zeigt, wie Sie für eine [n-schichtige](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/n-tier) Anwendung konfigurierte virtuelle Computer (VMs) und ein entsprechendes virtuelles Netzwerk mit SQL Server unter Windows für die Datenschicht bereitstellen. 

## <a name="architecture"></a>Architecture

Diese Architektur besteht aus den folgenden Komponenten.

![](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>Allgemein

-   **Ressourcengruppe**. [Ressourcengruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) dienen zum Gruppieren von Azure-Ressourcen, damit sie nach Lebensdauer, Besitzer oder anderen Kriterien verwaltet werden können.

-   **Verfügbarkeitsgruppe**. Eine Verfügbarkeitsgruppe ist eine Rechenzentrumskonfiguration, um für VMs Redundanz und Verfügbarkeit bereitzustellen. Durch diese Konfiguration in einem Azure Stack-Stamp wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist. VMs werden in einer Verfügbarkeitsgruppe angeordnet, die sie auf mehrere Fehlerdomänen (Azure Stack-Hosts) verteilt.

## <a name="networking-and-load-balancing"></a>Netzwerk und Lastenausgleich

-   **Virtuelles Netzwerk und Subnetze**. Jede Azure-VM wird in einem virtuellen Netzwerk bereitgestellt, das in Subnetze segmentiert werden kann. Erstellen Sie für jede Schicht ein separates Subnetz.

-   **Layer 7-Lastenausgleich.** Da Application Gateway noch nicht für Azure Stack verfügbar ist, sind Alternativen in [Azure Stack Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1908) verfügbar, wie z. B.: [KEMP LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) oder [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Lastenausgleichsmodule**. Verwenden Sie [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) zum Verteilen von Netzwerkdatenverkehr von der Webschicht auf die Geschäftsschicht und von der Geschäftsschicht an SQL Server.

-   **Netzwerksicherheitsgruppen** (NSGs). Verwenden Sie NSGs, um den Netzwerkdatenverkehr im virtuellen Netzwerk zu beschränken. In der hier gezeigten dreischichtigen Architektur akzeptiert die Datenbankschicht beispielsweise keinen Datenverkehr vom Web-Front-End, sondern nur von der Unternehmensschicht und dem Verwaltungssubnetz.

-   **DNS**. Azure Stack bietet keinen eigenen DNS-Hostingdienst. Verwenden Sie daher den DNS-Server in Ihren AD DS.

**Virtuelle Computer**

-   **SQL Server Always On-Verfügbarkeitsgruppe**. Stellt Hochverfügbarkeit in der Datenschicht durch Replikation und Failover bereit. Verwendet WSFC-Technologie (Windows Server-Failovercluster) für Failover.

-   **AD DS-Server (Active Directory Domain Services):** Die Computerobjekte für den Failovercluster und die zugehörigen Clusterrollen werden in Active Directory Domain Services (AD DS) erstellt. Das Einrichten von AD DS-Servern auf VMs im gleichen virtuellen Netzwerk ist eine bevorzugte Methode, um andere VMs in AD DS einzubinden. Sie können die VMs auch in die AD DS des Unternehmens einbinden, indem Sie das virtuelle Netzwerk über eine VPN-Verbindung mit dem Unternehmensnetzwerk verbinden. Bei beiden Ansätzen müssen Sie das DNS des virtuellen Netzwerks in Ihren AD DS-DNS-Server (im virtuellen Netzwerk oder im bestehenden Unternehmensnetzwerk) ändern, um den vollqualifizierten Domänennamen (FQDN) der AD DS-Domäne aufzulösen.

-   **Cloudzeuge**. Bei einem Failovercluster muss mindestens die Hälfte der Knoten ausgeführt werden (Quorum). Enthält der Cluster nur zwei Knoten, kann eine Netzwerkpartition dazu führen, dass sich beide Knoten als Masterknoten betrachten. In diesem Fall benötigen Sie einen *Zeugen*, um den Konflikt zu lösen und ein Quorum festzulegen. Ein Zeuge ist eine Ressource (beispielsweise ein freigegebener Datenträger), der den Ausschlag für das Quorum gibt. Ein Cloudzeuge ist ein Zeugentyp, der Azure Blob Storage nutzt. Weitere Informationen zum Konzept des Quorums finden Sie unter [Understanding cluster and pool quorum](https://docs.microsoft.com/windows-server/storage/storage-spaces/understand-quorum) Weitere Informationen zu Cloudzeugen finden Sie unter [Deploy a cloud witness for a Failover Cluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) (Bereitstellen eines Cloudzeugen für einen Failovercluster). In Azure Stack unterscheidet sich der Endpunkt des Cloudzeugen vom globalen Azure. 

Er kann wie folgt aussehen:

- Für Global Azure:  
  `https://mywitness.blob.core.windows.net/`

- Für Azure Stack:  
  `https://mywitness.blob.<region>.<FQDN>`

-   **Jumpbox**: Wird auch als [geschützter Host](https://en.wikipedia.org/wiki/Bastion_host) bezeichnet. Dies ist eine geschützte VM im Netzwerk, die von Administratoren zum Herstellen der Verbindung mit anderen VMs verwendet wird. Die Jumpbox verfügt über eine NSG, bei der Remotedatenverkehr nur von öffentlichen IP-Adressen zugelassen wird, die in einer Liste mit sicheren Adressen enthalten sind. Die NSG sollte Remotedesktop-Datenverkehr (RDP) zulassen.

## <a name="recommendations"></a>Empfehlungen

Ihre Anforderungen können von der hier beschriebenen Architektur abweichen. Verwenden Sie diese Empfehlungen als Startpunkt.

### <a name="virtual-machines"></a>Virtuelle Computer

Empfehlungen zum Konfigurieren der VMs finden Sie unter [Ausführen eines virtuellen Windows-Computers in Azure Stack](iaas-architecture-vm-windows.md).

### <a name="virtual-network"></a>Virtuelles Netzwerk

Legen Sie bei der Erstellung des virtuellen Netzwerks (VNET) fest, wie viele IP-Adressen Ihre Ressourcen in jedem Subnetz benötigen. Geben Sie mithilfe der [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) eine Subnetzmaske und einen VNET-Adressbereich an, der für die erforderlichen IP-Adressen groß genug ist. Verwenden Sie einen Adressraum, der in die standardmäßigen [privaten IP-Adressblöcke](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) 10.0.0.0/8, 172.16.0.0/12 und 192.168.0.0/16 fällt.

Wählen Sie einen Adressbereich, der sich nicht mit Ihrem lokalen Netzwerk überschneidet, für den Fall, dass Sie später ein Gateway zwischen dem virtuellen Netzwerk und dem lokalen Netzwerk einrichten müssen. Sobald Sie das virtuelle Netzwerk erstellt haben, können Sie den Adressbereich nicht mehr ändern.

Entwerfen Sie Subnetze unter Berücksichtigung der Funktionalität und Sicherheitsanforderungen. Alle VMs innerhalb derselben Schicht oder Rolle sollten im selben Subnetz platziert werden, was eine Sicherheitsbegrenzung darstellen kann. Weitere Informationen zum Entwerfen von virtuellen Netzwerken und Subnetzen finden Sie unter [Planen und Entwerfen von Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### <a name="load-balancers"></a>Load Balancer

Machen Sie die VMs nicht direkt über das Internet verfügbar. Weisen Sie stattdessen jeder VM eine private IP-Adresse zu. Clients stellen über die öffentliche IP-Adresse eine Verbindung her, die dem Layer 7-Lastenausgleich zugeordnet ist.

Definieren Sie Lastenausgleichsregeln, um Netzwerkdatenverkehr an die virtuellen Computer weiterzuleiten. Um HTTP-Datenverkehr zuzulassen, ordnen Sie beispielsweise den Port 80 in der Front-End-Konfiguration dem Port 80 im Back-End-Adresspool zu. Wenn ein Client eine HTTP-Anforderung an Port 80 sendet, wählt der Lastenausgleich mithilfe eines [Hashalgorithmus](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#fundamental-load-balancer-features), der die Quell-IP-Adresse enthält, eine Back-End-IP-Adresse aus. So werden Clientanforderungen auf alle VMs im Back-End-Adresspool verteilt.

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Verwenden Sie NSG-Regeln, um den Datenverkehr zwischen den Schichten zu beschränken. In der oben gezeigten dreischichtigen Architektur kommuniziert die Internetschicht nicht direkt mit der Datenbankschicht. Um diese Regel zu erzwingen, sollte die Datenbankschicht eingehenden Datenverkehr aus dem Subnetz der Internetschicht blockieren.

1.  Verweigern Sie sämtlichen eingehenden Datenverkehr aus dem virtuellen Netzwerk. (Verwenden Sie in der Regel das Tag VIRTUAL_NETWORK.)

2.  Lassen Sie eingehenden Datenverkehr aus dem Subnetz der Unternehmensschicht zu.

3.  Lassen Sie eingehenden Datenverkehr aus dem Subnetz der Datenbankschicht zu. Diese Regel ermöglicht die Kommunikation zwischen den virtuellen Datenbankcomputern, die für die Replikation und das Failover der Datenbank erforderlich ist.

4.  Lassen Sie RDP-Datenverkehr (Port 3389) aus dem Subnetz der Jumpbox zu. Diese Regel erlaubt Administratoren, über die Jumpbox eine Verbindung mit der Datenbankschicht herzustellen.

Erstellen Sie die Regeln 2-4 mit einer höheren Priorität als die erste Regel, damit sie Vorrang haben.

## <a name="sql-server-always-on-availability-groups"></a>SQL Server Always On-Verfügbarkeitsgruppen

Um Hochverfügbarkeit von SQL Server zu erzielen, werden [AlwaysOn-Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/hh510230.aspx) empfohlen. Bei früheren Versionen als Windows Server 2016 erfordern AlwaysOn-Verfügbarkeitsgruppen einen Domänencontroller, und alle Knoten in der Verfügbarkeitsgruppe müssen sich in der gleichen AD-Domäne befinden.

Für Hochverfügbarkeit auf VM-Ebene müssen sich alle virtuellen SQL-VMs in einer Verfügbarkeitsgruppe befinden.

Andere Schichten stellen über einen [Verfügbarkeitsgruppenlistener](https://msdn.microsoft.com/library/hh213417.aspx) eine Verbindung mit der Datenbank her. Mit dem Listener kann ein SQL-Client eine Verbindung herstellen, ohne den Namen der physischen SQL Server-Instanz zu kennen. VMs, die auf die Datenbank zugreifen, müssen Mitglied der Domäne sein. Der Client (in diesem Fall eine andere Schicht) verwendet DNS, um den Namen des virtuellen Netzwerks des Listeners in IP-Adressen aufzulösen.

Konfigurieren Sie die SQL Server-Always On-Verfügbarkeitsgruppe wie folgt:

1.  Erstellen Sie einen WSFC-Cluster (Windows Server Failover Clustering), eine SQL Server Always On-Verfügbarkeitsgruppe und ein primäres Replikat. Weitere Informationen finden Sie unter [Erste Schritte mit Always On-Verfügbarkeitsgruppen (SQL Server)](https://msdn.microsoft.com/library/gg509118.aspx).

2.  Erstellen Sie ein internes Lastenausgleichsmodul mit einer statischen privaten IP-Adresse.

3.  Erstellen Sie einen Verfügbarkeitsgruppenlistener, und ordnen Sie den DNS-Namen des Listeners der IP-Adresse eines internen Lastenausgleichsmoduls zu.

4.  Erstellen Sie eine Lastenausgleichsregel für den SQL Server-Überwachungsport (standardmäßig TCP-Port 1433). Die Lastenausgleichsregel muss *Floating IP*, auch als „Direct Server Return“ bezeichnet, aktivieren. Dies bewirkt, dass die VM direkt auf den Client antwortet, was eine direkte Verbindung mit dem primären Replikat ermöglicht.

> [!Note]
> Wenn die Floating IP aktiviert ist, muss die Portnummer des Front-Ends mit der des Back-Ends in der Lastenausgleichsregel übereinstimmen.

Wenn ein SQL-Client versucht, eine Verbindung herzustellen, leitet das Lastenausgleichsmodul die Verbindungsanforderung an das primäre Replikat weiter. Bei einem Failover zu einem anderen Replikat leitet der Lastenausgleich neue Anforderungen automatisch an ein neues primäres Replikat weiter. Weitere Informationen finden Sie unter [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

Bei einem Failover werden bestehende Clientverbindungen geschlossen. Nachdem das Failover abgeschlossen ist, werden neue Verbindungen an das neue primäre Replikat weitergeleitet.

Wenn Ihre Anwendung mehr Lese- als Schreibzugriffe tätigt, können Sie einige der schreibgeschützten Abfragen in ein sekundäres Replikat auslagern. Weitere Informationen finden Sie unter [Verwenden eines Listeners zum Herstellen einer Verbindung mit einem schreibgeschützten sekundären Replikat (schreibgeschütztes Routing)](https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary).

Testen Sie Ihre Bereitstellung, indem Sie [ein manuelles Failover der Verfügbarkeitsgruppe erzwingen](https://msdn.microsoft.com/library/ff877957.aspx).

Informationen zur Optimierung der SQL-Leistung finden Sie im Artikel [Bewährte SQL Server-Methoden zum Optimieren der Leistung in Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-sql-server-vm-considerations).

**Jumpbox**

Verweigern Sie den RDP-Zugriff (Remotedesktopprotokoll) über das öffentliche Internet auf die VMs, auf denen die Anwendungsworkload ausgeführt wird. Der gesamte RDP-Zugriff auf diese VMs muss stattdessen über die Jumpbox erfolgen. Ein Administrator meldet sich bei der Jumpbox und von der Jumpbox aus dann bei der anderen VM an. Die Jumpbox lässt RDP-Datenverkehr aus dem Internet zu, jedoch nur von bekannten, sicheren IP-Adressen.

Die Jumpbox hat sehr geringe Leistungsanforderungen. Wählen Sie daher einen virtuellen Computer mit geringer Größe. Erstellen Sie eine [öffentliche IP-Adresse](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) für die Jumpbox. Platzieren Sie die Jumpbox im selben virtuellen Netzwerk wie die anderen VMs, jedoch in einem separaten Verwaltungssubnetz.

Fügen Sie zum Schutz der Jumpbox eine NSG-Regel hinzu, die ausschließlich RDP-Verbindungen von einer sicheren Gruppe öffentlicher IP-Adressen zulässt. Konfigurieren Sie die NSGs für die anderen Subnetze, um RDP-Datenverkehr aus dem Verwaltungssubnetz zuzulassen.

## <a name="scalability-considerations"></a>Überlegungen zur Skalierbarkeit

### <a name="scale-sets"></a>Skalierungsgruppen

Erwägen Sie für die Internet- und Unternehmensschichten die Verwendung von [Skalierungsgruppen für virtuelle Computer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview), anstatt separate VMs bereitzustellen. Eine VM-Skalierungsgruppe ermöglicht die Bereitstellung und Verwaltung einer Gruppe identischer VMs. Erwägen Sie die Verwendung von Skalierungsgruppen, wenn Sie VMs schnell horizontal skalieren müssen.

Es gibt zwei grundlegende Methoden für das Konfigurieren von virtuellen Computern in einer Skalierungsgruppe:

-   Verwenden Sie Erweiterungen, um die VM nach ihrer Bereitstellung zu konfigurieren. Bei diesem Ansatz dauert das Starten neuer VM-Instanzen länger als bei virtuellen Computern ohne Erweiterungen.

-   Stellen Sie einen [verwalteten Datenträger](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations) mit einem benutzerdefinierten Datenträgerimage bereit. Diese Option kann möglicherweise schneller bereitgestellt werden. Allerdings müssen Sie das Image auf dem neuesten Stand halten.

Weitere Informationen finden Sie unter [Überlegungen zum Entwurf von Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). Diese Entwurfsüberlegung gilt vor allem für Azure Stack, allerdings gibt es einige Nachteile:

-   VM-Skalierungsgruppen in Azure Stack unterstützen weder die Überbereitstellung noch parallele Upgrades.

-   VM-Skalierungsgruppen können in Azure Stack nicht automatisch skaliert werden.

-   Es wird dringend empfohlen, für eine VM-Skalierungsgruppe anstelle von nicht verwalteten Datenträgern verwaltete Datenträger in Azure Stack zu verwenden.

-   Derzeit gilt ein Grenzwert von 700 VMs in Azure Stack, der alle Azure Stack-Infrastruktur-VMs, einzelne VMs und Skalierungsgruppeninstanzen berücksichtigt.

## <a name="subscription-limits"></a>Grenzwerte für Abonnements

Für jedes Azure Stack-Mandantenabonnement gelten Standardgrenzwerte, einschließlich einer maximalen Anzahl von VMs pro Region, die vom Azure Stack-Betreiber konfiguriert wird. Weitere Informationen finden Sie unter [Übersicht: Azure Stack-Dienste, -Pläne, -Angebote und Abonnements](https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview). Siehe auch [Kontingenttypen in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-quota-types).

## <a name="security-considerations"></a>Sicherheitshinweise

Virtuelle Netzwerke stellen in Azure eine Isolationsbegrenzung für Datenverkehr dar. VMs in einem virtuellen Netzwerk können standardmäßig nicht direkt mit VMs in einem anderen virtuellen Netzwerk kommunizieren.

**NSGs**. Verwenden Sie [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs), um den Datenverkehr in das und aus dem Internet zu beschränken. Weitere Informationen finden Sie unter [Microsoft Cloud Services und Netzwerksicherheit](https://docs.microsoft.com/azure/best-practices-network-security).

**DMZ**. Für die Erstellung einer DMZ zwischen dem Internet und dem virtuellen Azure-Netzwerk sollten Sie eventuell eine virtuelle Netzwerkappliance (Network Virtual Appliance, NVA) hinzufügen. NVA ist ein Oberbegriff für eine virtuelle Appliance, die netzwerkbezogene Aufgaben wie Erstellung von Firewalls, Paketüberprüfung, Überwachung und benutzerdefiniertes Routing ausführen kann.

**Verschlüsselung**. Verschlüsseln Sie sensible ruhende Daten, und verwalten Sie die Verschlüsselungsschlüssel für die Datenbank mithilfe von [Key Vault in Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal). Weitere Informationen finden Sie unter [Konfigurieren der Azure-Schlüsseltresor-Integration für SQL Server auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault). Es empfiehlt sich außerdem, Anwendungsgeheimnisse wie Datenbankverbindungszeichenfolgen in Key Vault zu speichern.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
