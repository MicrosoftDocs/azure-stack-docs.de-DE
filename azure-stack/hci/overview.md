---
title: Übersicht über die Azure Stack HCI-Lösung
description: Enthält eine Übersicht über Azure Stack HCI.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.date: 07/22/2020
ms.openlocfilehash: fccb9ff45afbeb5fee0bc7c971adcdea1f532404
ms.sourcegitcommit: 16ff77f7157e5b04a8cd401b095f7b71f51d5a11
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86949592"
---
# <a name="azure-stack-hci-solution-overview"></a>Übersicht über die Azure Stack HCI-Lösung

Azure Stack HCI ist eine hyperkonvergente Clusterlösung, mit der virtualisierte Windows- und Linux-Workloads in einer lokalen Hybridumgebung ausgeführt werden. Mit Azure-Hybriddiensten wird der Cluster um Funktionen erweitert, z. B. cloudbasierte Überwachung, Site Recovery und VM-Sicherungen, und Sie erhalten einen zentralen Überblick über Ihre gesamten Azure Stack HCI-Bereitstellungen im Azure-Portal. Sie können den Cluster mit Ihren vorhandenen Tools verwalten, z. B. Windows Admin Center, System Center und PowerShell.

Version 20H2 von Azure Stack HCI umfasst ein neues Betriebssystem, das sich derzeit in der öffentlichen Vorschauphase (Public Preview) befindet. Diese Version ist für lokale Cluster bestimmt, in denen virtualisierte Workloads ausgeführt werden, und sie verfügt über integrierte Hybrid Cloud-Verbindungen. Azure Stack HCI wird daher als Azure-Dienst bereitgestellt und basierend auf einem Azure-Abonnement abgerechnet.

Azure Stack HCI ist im Grunde genommen eine Lösung, die Folgendes vereint:

- Überprüfte Hardware von einem OEM-Partner
- Azure Stack HCI-Betriebssystem
- Windows Admin Center
- Azure-Dienste

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="Das Azure Stack HCI-Betriebssystem wird auf überprüfter Hardware ausgeführt, mit Windows Admin Center verwaltet und ermöglicht die Verbindungsherstellung mit Azure." border="false":::

Version 20H2 von Azure Stack HCI verfügt über neue Funktionen, die unter Windows Server nicht vorhanden sind. Ein Beispiel hierfür ist die Verwendung von Windows Admin Center zum Erstellen eines hyperkonvergenten Clusters, bei dem „Direkte Speicherplätze“ genutzt wird, um ein besseres Preis-Leistungs-Verhältnis für Speicher zu erzielen. Hierzu gehört auch die Option zum Strecken des Clusters über mehrere Standorte, um dafür ein automatisches Failover zu ermöglichen. Weitere Informationen finden Sie unter [Neuerungen in Azure Stack HCI](#whats-new-in-azure-stack-hci).

## <a name="use-cases-for-azure-stack-hci"></a>Anwendungsfälle für Azure Stack HCI

Es gibt viele Anwendungsfälle für Azure Stack HCI, obwohl die Lösung nicht für nicht virtualisierte Workloads bestimmt ist (im Gegensatz zur SQL Server-Lösung, die unter Azure Stack HCI problemlos ausgeführt werden kann). Kunden wählen für die folgenden Szenarien häufig Azure Stack HCI aus:

### <a name="data-center-consolidation-and-modernization"></a>Konsolidierung und Modernisierung von Rechenzentren

Indem Sie veraltete Virtualisierungshosts mit Azure Stack HCI aktualisieren und konsolidieren, können Sie die Skalierbarkeit verbessern und Ihre Umgebung leichter verwalten und schützen. Außerdem besteht die Möglichkeit, den Legacy-SAN-Speicher außer Betrieb zu nehmen, um den Speicherumfang und die Gesamtkosten zu reduzieren. Die Betriebs- und Systemverwaltung wird durch einheitliche Tools und Schnittstellen und eine zentrale Anlaufstelle für den Support vereinfacht.

### <a name="remote-and-branch-offices"></a>Remotebüros und Zweigstellen

Da Sie bei Azure Stack HCI Clusterlösungen mit zwei Servern schon für weniger als 20.000 US-Dollar pro Standort erhalten, ist dies eine hervorragende Option, um Remotebüros/Zweigstellen, Einzelhandelsgeschäfte und Außenstandorte kostengünstig zu modernisieren. Dank der innovativen geschachtelten Resilienz können Volumes auch dann online und zugänglich bleiben, wenn mehrere Hardwarefehler gleichzeitig auftreten. Mit der Cloudzeugentechnologie können Sie Azure als einfache Entscheidungshilfe für das Clusterquorum nutzen, um Split-Brain-Zustände zu verhindern, ohne dass Kosten für einen dritten Host anfallen. Kunden können Azure Stack HCI-Remotebereitstellungen auch zentral im Azure-Portal anzeigen.

### <a name="virtual-desktops"></a>Virtuelle Desktops

Viele Organisationen möchten virtuelle Desktops lokal hosten, um für eine geringe Latenz und für Datenhoheit zu sorgen. Mit Azure Stack HCI kann eine ähnliche Leistung wie bei einer lokalen Bereitstellung erreicht werden.

### <a name="high-performance-virtualized-workloads"></a>Virtualisierte Hochleistungsworkloads

Mit Azure Stack HCI kann die branchenweit beste Leistung für SQL Server-Datenbanken und andere leistungsabhängige virtualisierte Workloads erzielt werden, für die Millionen von Speicher-IOPS oder Datenbanktransaktionen pro Sekunde erforderlich sind. Die Lösung ermöglicht eine dauerhaft geringe Latenz mit NVMe-SSDs, einen stabilen RDMA-Stapel und persistenten Speicher.

## <a name="azure-integration-benefits"></a>Vorteile der Azure-Integration

Azure Stack HCI ist hervorragend für Hybridinfrastrukturen geeignet und ermöglicht Ihnen die Nutzung der Zusammenarbeit von Cloud- und lokalen Ressourcen sowie der nativen Überwachung, des Schutzes und der Sicherungen in der Cloud.

Nachdem Sie Ihren Azure Stack HCI-Cluster bei Azure registriert haben, können Sie das Azure-Portal anfänglich für folgende Zwecke verwenden:

- **Überwachung:** Zeigen Sie Ihre gesamten Azure Stack HCI-Cluster in einer zentralen globalen Ansicht an, in der Sie sie nach Ressourcengruppe gruppieren und markieren können.
- **Abrechnung:** Bezahlen Sie für Azure Stack HCI über Ihr Azure-Abonnement. (Beachten Sie, dass während der öffentlichen Vorschauphase (Public Preview) keine Kosten anfallen.)

Wir arbeiten hart an der Erstellung weiterer Funktionen. Achten Sie also auf weitere Updates.

Sie können auch zusätzliche Azure-Hybriddienste abonnieren:

- **Azure Site Recovery** zur Erzielung von Hochverfügbarkeit und Notfallwiederherstellung als Dienst (Disaster-Recovery-as-a-Service, DRaaS)
- **Azure Monitor**, ein zentraler Hub, in dem Sie die Aktivität Ihrer Apps, Netzwerke und Infrastrukturen überwachen können – mithilfe erweiterter KI-Analysen
- **Cloudzeuge**, um Azure als einfache Entscheidungshilfe für ein Clusterquorum zu verwenden
- **Azure Backup** für den Schutz von Daten durch Speicherung an anderen Standorten und den Schutz vor Ransomware
- **Azure-Updateverwaltung** für die Bewertung und Bereitstellung von Updates für Windows-VMs, die in Azure und lokal ausgeführt werden
- **Azure-Netzwerkadapter** zum Verbinden lokaler Ressourcen mit Ihren VMs in Azure über ein Point-to-Site-VPN
- **Azure-Dateisynchronisierung** zum Synchronisieren Ihres Dateiservers mit der Cloud

Weitere Informationen finden Sie unter [Verbinden von Windows Server mit Azure-Hybriddiensten](/windows-server/manage/windows-admin-center/azure/index).

## <a name="why-azure-stack-hci"></a>Gründe für die Verwendung von Azure Stack HCI

Bei Azure Stack HCI handelt es sich um einen erstklassigen integrierten Virtualisierungsstapel. Dieser basiert auf bewährten Technologien, die bereits in großem Stil bereitgestellt wurden, z. B. Hyper-V, Direkte Speicherplätze und durch Azure inspiriertes SDN (Software-Defined Networking). Es gibt viele Gründe, warum Kunden Azure Stack HCI wählen, z. B.:

- Es ist eine Lösung, mit der Hyper-V- und Serveradministratoren vertraut sind, sodass sie vorhandene Virtualisierungs- und Speicherkonzepte sowie die zugehörigen Fähigkeiten nutzen können.
- Es können vorhandene Rechenzentrumsprozesse und -tools verwendet werden, z. B. Microsoft System Center, Active Directory, Gruppenrichtlinie und PowerShell-Skripts.
- Die Lösung funktioniert mit gängigen Sicherungs-, Sicherheits- und Überwachungstools von Drittanbietern.
- Flexible Hardwareoptionen ermöglichen es Kunden, den Anbieter mit dem besten Dienst und Support für ihre geografische Region auszuwählen.
- Der gemeinsame Support von Microsoft und dem Hardwareanbieter führt zu einer Verbesserung der Kundenfreundlichkeit.
- Dank nahtloser Full-Stack-Updates ist es einfach, immer auf dem neuesten Stand zu bleiben.
- Ein flexibles und umfassendes Ökosystem gibt IT-Experten die Flexibilität, die sie für die Erstellung einer Lösung benötigen, mit der die jeweiligen Anforderungen am besten erfüllt werden.

## <a name="what-you-need-for-azure-stack-hci"></a>Voraussetzungen für Azure Stack HCI

Für den Einstieg benötigen Sie Folgendes:

- Einen Cluster mit zwei oder mehr Servern aus dem [Azure Stack HCI-Katalog](https://azure.microsoft.com/products/azure-stack/hci/catalog/), den Sie über Ihren bevorzugten Microsoft-Hardwarepartner erwerben.
- [Ein Azure-Abonnement](https://azure.microsoft.com/).
- Eine Internetverbindung für jeden Server im Cluster, über die mindestens alle 30 Tage per HTTPS eine Verbindung für ausgehenden Datenverkehr mit dem folgenden Endpunkt hergestellt werden kann: *-azurestackhci-usage.azurewebsites.net
- Für über mehrere Standorte gestreckte Cluster benötigen Sie mindestens eine 1 GB-Standortverbindung (RDMA-Verbindung mit 25 GB empfohlen) mit einer durchschnittlichen Latenz von 5 ms pro Roundtrip, falls Sie eine synchrone Replikation durchführen möchten, bei der Schreibvorgänge gleichzeitig an beiden Standorten durchgeführt werden.

Weitere Informationen finden Sie im Artikel zu den [Vorbereitungsmaßnahmen](deploy/before-you-start.md).

## <a name="hardware-partners"></a>Hardwarepartner

Sie können überprüfte Azure Stack HCI-Lösungen von Ihrem bevorzugten Microsoft-Partner erwerben, um ohne großen Entwurfs- und Buildaufwand starten zu können. Microsoft-Partner dienen auch als zentrale Ansprechpartner für die Implementierung und für Supportleistungen. Sie können entweder überprüfte Knoten oder ein integriertes System erwerben, auf dem das Azure Stack HCI-Betriebssystem vorinstalliert ist und das über Partnererweiterungen für Treiber- und Firmwareupdates verfügt.

Besuchen Sie die Seite mit den [Azure Stack HCI-Lösungen](https://azure.microsoft.com/overview/azure-stack/hci), oder sehen Sie sich im [Azure Stack HCI-Katalog](https://azure.microsoft.com/products/azure-stack/hci/catalog/) die mehr als 70 Azure Stack HCI-Lösungen an, die bei Microsoft-Partnern derzeit erhältlich sind. Beispiele für Partner sind ASUS, Axellio, Blue Chip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD und Supermicro.

## <a name="software-partners"></a>Softwarepartner

Es gibt eine Vielzahl von Microsoft-Partnern, die an Software zur Erweiterung der Funktionen von Azure Stack HCI arbeiten, während IT-Administratoren gleichzeitig die Nutzung von vertrauten Tools ermöglicht wird. Beispielsweise unterstützt Altaro, ein weltweiter Anbieter von Sicherungslösungen und Microsoft Gold Partner, Azure Stack HCI in seiner Altaro-Lösung zum Sichern von VMs. Dies ermöglicht es Kunden und Anbietern von verwalteten Diensten, virtuelle Computer, die unter Azure Stack HCI ausgeführt werden, bis Ende Juni 2021 kostenlos zu sichern. [Erfahren Sie mehr zu dieser Ankündigung](http://www.altaro.com/news/single/News-Altaro-applies-its-expertise-in-Hyper-V-backup-to-support-Microsoft.php).

## <a name="licensing-billing-and-pricing"></a>Lizenzierung, Abrechnung und Preise

Die Abrechnung für Azure Stack HCI basiert auf einer monatlichen Abonnementgebühr pro physischem Prozessorkern und nicht auf einer zeitlich unbeschränkten Lizenz. Wenn Kunden eine Verbindung mit Azure herstellen, wird die Anzahl von verwendeten Kernen automatisch hochgeladen und zu Abrechnungszwecken ausgewertet. Über die physischen Prozessorkerne hinaus variieren die Kosten nicht basierend auf dem Verbrauch. Dies bedeutet, dass eine höhere Zahl von VMs nicht mehr kostet und Kunden, die virtuelle Umgebungen mit höherer Dichte betreiben können, einen Vorteil erzielen.

Kunden können entweder überprüfte Server von einem Hardwarepartner erwerben, auf dem das Azure Stack HCI-Betriebssystem vorinstalliert ist, oder überprüfte Bare-Metal-Server bei einem OEM kaufen und dann den Azure Stack HCI-Dienst abonnieren und das Azure Stack HCI-Betriebssystem über das [Azure-Portal](https://azure.microsoft.com/products/azure-stack/hci/) herunterladen.

## <a name="management-tools"></a>Verwaltungstools

Mit Azure Stack HCI verfügen Sie über alle Administratorrechte im Cluster und können die entsprechende Technologie direkt verwalten:

- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Softwaredefiniertes Netzwerk](/windows-server/networking/sdn/)
- [Failoverclustering](/windows-server/failover-clustering/failover-clustering-overview)

Sie können für die Verwaltung dieser Technologie die folgenden Tools verwenden:

- [Windows Admin Center](/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](/powershell/?view=powershell-6)
- Andere Verwaltungstools, z. B. [Server-Manager](/windows-server/administration/server-manager/server-manager) und MMC-Snap-Ins
- Tools, die nicht von Microsoft stammen, z. B. 5Nine Manager

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Welche Beziehung besteht zwischen Azure Stack HCI und Windows Server?

Windows Server ist das Fundament von nahezu allen Azure-Produkten, und alle Features, die für Sie wichtig sind, werden weiterhin basierend auf Windows Server ausgeliefert und unterstützt. Das ursprüngliche Angebot von Azure Stack HCI hat auf Windows Server 2019 basiert, und es wurde das herkömmliche Windows Server-Lizenzierungsmodell genutzt. Heute verfügt Azure Stack HCI über ein eigenes Betriebssystem und ein abonnementbasiertes Lizenzierungsmodell. Azure Stack HCI ist die empfohlene Anwendung zur lokalen Bereitstellung von HCI, indem die von Microsoft validierte Hardware unserer Partner verwendet wird.

### <a name="does-azure-stack-hci-need-to-connect-to-azure"></a>Muss für Azure Stack HCI eine Verbindung mit Azure hergestellt werden?

Ja. Der Cluster muss mindestens einmal alle 30 Tage eine Verbindung mit Azure herstellen, damit die Anzahl von Kernen zu Abrechnungszwecken ermittelt werden kann. Sie können die Integration in Azure auch für Hybridszenarien nutzen, z. B. externe Sicherung und Notfallwiederherstellung sowie cloudbasierte Überwachung und Updateverwaltung. Diese Vorgehensweise ist aber optional. Die Ausführung über längere Zeit ist auch ohne Internetverbindung kein Problem.

### <a name="can-i-upgrade-from-windows-server-2019-to-azure-stack-hci"></a>Kann ich ein Upgrade von Windows Server 2019 auf Azure Stack HCI durchführen?

Derzeit ist ein Upgrade von Windows Server 2019 auf Azure Stack HCI nur möglich, indem Sie ein [paralleles Clusterupgrade](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade) durchführen. Ein direktes Upgrade ist derzeit nicht verfügbar.

### <a name="what-do-azure-stack-hub-and-azure-stack-hci-solutions-have-in-common"></a>Welche Gemeinsamkeiten haben Azure Stack Hub- und Azure Stack HCI-Lösungen?

Azure Stack HCI verfügt über die gleichen Hyper-V-basierten, softwaredefinierten Compute-, Speicher- und Netzwerktechnologien wie Azure Stack Hub. Beide Angebote erfüllen strenge Test- und Validierungsvorgaben, um die Zuverlässigkeit und Kompatibilität mit der zugrunde liegenden Hardwareplattform sicherzustellen.

### <a name="how-are-they-different"></a>Inwiefern unterscheiden sie sich?

Mit Azure Stack Hub führen Sie Clouddienste lokal aus. Sie können IaaS- und -PaaS-Dienste von Azure lokal ausführen, um Cloud-Apps unabhängig vom Standort konsistent erstellen und ausführen zu können. Die Verwaltung wird lokal über das Azure-Portal durchgeführt.

Mit Azure Stack HCI führen Sie virtualisierte Workloads lokal aus, und für die Verwaltung nutzen Sie Windows Admin Center und vertraute Windows Server-Tools. Sie können auch eine Verbindung mit Azure für Hybridszenarien herstellen, z. B. cloudbasierte Sitewiederherstellung, Überwachung und mehr.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Weshalb fügt Microsoft sein HCI-Angebot der Azure Stack-Familie hinzu?

Die hyperkonvergente Technologie von Microsoft ist bereits die Basis für Azure Stack Hub.

Viele Microsoft-Kunden arbeiten mit komplexen IT-Umgebungen. Unser Ziel ist es, unseren Kunden die ideale Technologie für ihre Unternehmensanforderungen bereitzustellen. Azure Stack HCI ist die Weiterentwicklung softwaredefinierter Windows Server-Lösungen, die in der Vergangenheit von unseren Hardwarepartnern angeboten wurden. Wir haben das Angebot der Azure Stack-Familie hinzugefügt, weil wir immer mehr neue Optionen für die nahtlose Verbindung mit Azure für Infrastrukturverwaltungsdienste veröffentlichen.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack-hub"></a>Kann ich ein Upgrade von Azure Stack HCI auf Azure Stack Hub durchführen?

Nein. Kunden können Ihre Workloads aber von Azure Stack HCI zu Azure Stack Hub oder Azure migrieren.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>Für welche Azure-Dienste kann ich eine Verbindung mit Azure Stack HCI herstellen?

Eine aktualisierte Liste mit Azure-Diensten, mit denen Sie für Azure Stack HCI eine Verbindung herstellen können, finden Sie unter [Connecting Windows Server to Azure hybrid services](/windows-server/manage/windows-admin-center/azure/index) (Verbinden von Windows Server mit Azure-Hybriddiensten).

### <a name="does-azure-stack-hci-collect-any-data-from-my-system"></a>Werden von Azure Stack HCI die Daten meines Systems gesammelt?

Ja. Es wird eine sehr begrenzte Menge von Daten gesammelt. Diese Daten werden verwendet, um HCI aktuell zu halten, die Leistung sicherzustellen, Informationen für das Azure-Portal bereitzustellen und die Anzahl von Prozessorkernen im Cluster zu Abrechnungszwecken zu ermitteln.

### <a name="to-which-endpoints-is-the-data-transmitted"></a>An welche Endpunkte werden die Daten übertragen?  

Azure Stack HCI verwendet den folgenden Endpunkt für die Übertragung von Abrechnungsdaten: *-azurestackhci-usage.azurewebsites.net

## <a name="the-azure-stack-family"></a>Azure Stack-Familie

Azure Stack HCI ist Teil der Azure- und Azure Stack-Familie, und es wird die gleiche softwaredefinierte Compute-, Speicher- und Netzwerksoftware verwendet wie für Azure Stack Hub. Unten ist eine kurze Zusammenfassung der unterschiedlichen Lösungen angegeben. Weitere Informationen finden Sie unter [Unterschiede zwischen der globalen Azure-Infrastruktur, Azure Stack Hub und Azure Stack HCI](../operator/compare-azure-azure-stack.md).

- [Azure:](https://azure.microsoft.com) Nutzen Sie öffentliche Clouddienste für bedarfsorientierte Self-Service-Computeressourcen, um vorhandene Apps zu migrieren und zu modernisieren und neue cloudnative Apps zu entwickeln.
- [Azure Stack Edge](/azure/databox-online/data-box-edge-overview): Beschleunigen Sie Workloads für maschinelles Lernen, und führen Sie lokale Containeranwendungen oder virtualisierte Workloads auf einem in der Cloud verwalteten Gerät aus.
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci): Führen Sie virtualisierte Anwendungen aus, ersetzen und konsolidieren Sie veraltete Serverinfrastruktur, und stellen Sie für Clouddienste eine Verbindung mit Azure her.
- [Azure Stack Hub](../operator/azure-stack-overview.md): Führen Sie mithilfe einheitlicher Azure-Dienste Cloudanwendungen lokal aus, entweder nach dem Trennen der Verbindung oder zum Erfüllen rechtlicher Anforderungen.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Diagramm zu den Lösungen der Azure Stack-Familie" border="false":::

## <a name="compare-windows-server-and-azure-stack-hci"></a>Vergleich von Windows Server und Azure Stack HCI

Viele Kunden fragen sich, ob Windows Server oder Azure Stack HCI für ihre Bedürfnisse besser geeignet ist. Anhand der Informationen in der folgenden Tabelle können Sie entscheiden, welche Lösung für Ihre Organisation geeignet ist. Sowohl Windows Server als auch Azure Stack HCI verfügt über eine Benutzeroberfläche von hoher Qualität mit einer Roadmap für neue Releases.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Bester Gast- und herkömmlicher Server | Bester Virtualisierungshost für ein softwaredefiniertes Rechenzentrum, einschließlich „Direkte Speicherplätze“ |
| Ausführung überall möglich, Verwendung eines herkömmlichen Modells für die Softwarelizenzierung | Kann auf Hardware Ihres bevorzugten Anbieters ausgeführt werden, wird aber als Azure-Dienst bereitgestellt und über Ihr Azure-Konto in Rechnung gestellt |
| Zwei Installationsoptionen: Server mit Desktopdarstellung oder Server Core | Basiert auf einem leicht angepassten Server Core |

### <a name="when-to-use-windows-server"></a>Nutzung von Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server ist ein äußerst vielseitiges Mehrzweck-Betriebssystem mit Dutzenden von Rollen und Hunderten von Features, einschließlich Gastrechten. | Azure Stack HCI verfügt nicht über Gastrechte und ist für eine moderne, hyperkonvergente Architektur bestimmt. |
| Verwenden Sie Windows Server zum Ausführen von VMs oder für Bare-Metal-Installationen, die alle herkömmlichen Serverrollen umfassen, z. B. Active Directory, Dateidienste, DNS, DHCP, Internetinformationsdienste (IIS), Containerhost/-gast, SQL Server, Exchange Server, Host-Überwachungsdienst (Host Guardian Service, HGS) und viele mehr. | Azure Stack HCI ist für die Nutzung als Hyper-V-Virtualisierungshost bestimmt und nur für die direkte Ausführung einer geringen Anzahl von Serverrollen lizenziert. Alle anderen Rollen müssen innerhalb von VMs ausgeführt werden. |

### <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server kann lokal oder in der Cloud ausgeführt werden, aber es ist selbst kein vollständiges hyperkonvergentes Angebot.| Verwenden Sie Azure Stack HCI, um VMs lokal auszuführen. Optional können Sie sie über zwei Standorte strecken und Verbindungen zu Azure-Hybriddiensten verwenden. Dies ist eine einfache Möglichkeit, Ihre Rechenzentren und Zweigstellen zu modernisieren und zu schützen, für SQL Server-Datenbanken die branchenweit beste Leistung zu erzielen und virtuelle Desktops lokal auszuführen, um für geringe Latenz und Datenhoheit zu sorgen.|
| Windows Server ist für alle Windows Server-Rollen – ob virtualisiert oder nicht – quasi das vielseitige „Schweizer Taschenmesser“. | Verwenden Sie Azure Stack HCI zum Virtualisieren von klassischen Unternehmens-Apps wie Exchange, SharePoint und SQL Server sowie für die Virtualisierung von Windows Server-Rollen wie Dateiserver, DNS, DHCP, IIS und AD. Dies umfasst auch den uneingeschränkten Zugriff auf alle Hyper-V-Features, z. B. abgeschirmte VMs.|
| Viele Windows Server-Bereitstellungen werden auf veralteter Hardware ausgeführt. | Verwenden Sie Azure Stack HCI, um eine softwaredefinierte Infrastruktur anstelle von verwalteten Speicherarrays oder Netzwerkgeräten zu nutzen, ohne dass größere Änderungen an der Architektur vorgenommen werden müssen. Die integrierten Optionen für Hyper-V, „Direkte Speicherplätze“ und softwaredefinierte Netzwerke (SDN) sind direkt zugänglich und können direkt verwaltet werden. Führen Sie Apps auf Windows- oder Linux-VMs aus.|

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>Vergleich von Azure Stack Hub und Azure Stack HCI

Während der digitalen Transformation Ihrer Organisation gelangen Sie unter Umständen zu der Erkenntnis, dass Sie mit Diensten in der öffentlichen Cloud noch schneller moderne Architekturen erstellen und bestehende Apps überarbeiten können. Viele Workloads müssen aber beispielsweise aus technischen oder gesetzlichen Gründen weiterhin lokal verarbeitet werden. Ermitteln Sie anhand der Tabelle unten, mit welcher Microsoft Hybrid Cloud-Strategie Ihre Anforderungen an Komponenten und Standorte erfüllt und innovative Cloudoptionen für Workloads unabhängig vom jeweiligen Ort bereitgestellt werden.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Neue Fähigkeiten, innovative Prozesse | Gleiche Fähigkeiten, vertraute Prozesse |
| Azure-Dienste in Ihrem Rechenzentrum | Verbindung Ihres Rechenzentrums mit Azure-Diensten |

### <a name="when-to-use-azure-stack-hub"></a>Gründe für die Verwendung von Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Verwenden Sie Azure Stack Hub für Self-Service-IaaS-Zwecke (Infrastructure-as-a-Service) mit starker Isolation und präziser Nutzungsnachverfolgung und verbrauchsbasierter Kostenzuteilung für mehrere zusammengestellte Mandanten. Ideal für Dienstanbieter und private Clouds von Unternehmen geeignet. Vorlagen aus dem Azure Marketplace. | In Azure Stack HCI wird Mehrinstanzenfähigkeit nicht nativ erzwungen oder bereitgestellt. |
| Verwenden Sie Azure Stack Hub, um Apps zu entwickeln und auszuführen, die auf lokalen PaaS-Diensten (Platform-as-a-Service) wie Web-Apps, Azure Functions oder Azure Event Hubs basieren. Diese Dienste werden unter Azure Stack Hub genauso wie in Azure ausgeführt und stellen eine einheitliche Hybridentwicklungs- und Laufzeitumgebung dar. | In Azure Stack HCI werden keine PaaS-Dienste lokal ausgeführt. |
| Nutzen Sie Azure Stack Hub, um Bereitstellung und Betrieb von Apps mit DevOps-Methoden (etwa „Infrastructure-as-Code“ oder Continuous Integration und Continuous Deployment (CI/CD)) und benutzerfreundlichen Features (etwa einheitlichen Azure-VM-Erweiterungen) zu modernisieren. Ideal geeignet für Entwicklungs- und DevOps-Teams. | Azure Stack HCI enthält keine nativen DevOps-Tools. |

### <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Für Azure Stack Hub sind mindestens vier Knoten und eigene Netzwerkswitches erforderlich. | Verwenden Sie Azure Stack HCI für den minimalen Speicherbedarf für abgelegene Büros und Zweigstellen. Beginnen Sie mit nur zwei Serverknoten und benachbarten Netzwerken, um für Einfachheit und Kostengünstigkeit zu sorgen. Die Hardwareangebote beginnen bei vier Laufwerken mit 64 GB Speicher zu einem Preis von deutlich unter 10.000 USD pro Knoten. |
| Azure Stack Hub schränkt die Hyper-V-Konfigurierbarkeit und den Featuresatz ein, um Einheitlichkeit mit Azure zu erzielen. | Verwenden Sie Azure Stack HCI für eine einfache Hyper-V-Virtualisierung für klassische Unternehmens-Apps wie Exchange, SharePoint und SQL Server sowie für die Virtualisierung von Windows Server-Rollen wie Dateiserver, DNS, DHCP, IIS und AD. Uneingeschränkter Zugriff auf alle Hyper-V-Features, z. B. abgeschirmte VMs.|
| Diese Infrastrukturtechnologien werden nicht von Azure Stack Hub verfügbar gemacht. | Verwenden Sie Azure Stack HCI, um eine softwaredefinierte Infrastruktur anstelle von verwalteten Speicherarrays oder Netzwerkgeräten zu nutzen, ohne dass größere Änderungen an der Architektur vorgenommen werden müssen. Die integrierten Optionen für Hyper-V, „Direkte Speicherplätze“ und softwaredefinierte Netzwerke (SDN) sind direkt zugänglich und können direkt verwaltet werden. |

## <a name="whats-new-in-azure-stack-hci"></a>Neuerungen in Azure Stack HCI

Cluster, in denen Version 20H2 von Azure Stack HCI ausgeführt wird, verfügen im Vergleich zu Windows Server 2019-basierten Lösungen über die folgenden neuen Features:

- **Neue Funktionen in Windows Admin Center**: Da hyperkonvergente Cluster über eine intuitive Benutzeroberfläche erstellt und aktualisiert werden können, ist die Nutzung von Azure Stack HCI einfacher als jemals zuvor.
- **Gestreckte Cluster für automatisches Failover**: Das Clustering für mehrere Standorte mit Replikation von Speicherreplikaten und automatischem VM-Failover ermöglicht eine native Notfallwiederherstellung und Geschäftskontinuität für Cluster, in denen das Feature „Direkte Speicherplätze“ genutzt wird.
- **Affinitäts- und Antiaffinitätsregeln**: Diese Regeln können auf ähnliche Weise wie Verfügbarkeitszonen in Azure verwendet werden, um VMs und Speicher in Clustern mit mehreren Fehlerdomänen, z. B. gestreckten Clustern, zusammen oder getrennt anzuordnen.
- **Azure-Portal-Integration**: Die Benutzeroberfläche im Azure-Portal für Azure Stack HCI ist dafür ausgelegt, Ihre gesamten Azure Stack HCI-Cluster weltweit anzuzeigen, und neue Features befinden sich gerade in der Entwicklung.
- **GPU-Beschleunigung für Hochleistungsworkloads**: KI- und ML-Anwendungen können davon profitieren, wenn die Leistung mit GPUs gesteigert wird.
- **BitLocker-Verschlüsselung**: Sie können jetzt BitLocker zum Verschlüsseln des Inhalts von Datenvolumes unter Azure Stack HCI verwenden, um dafür zu sorgen, dass Behörden und andere Kunden bestimmte Standards erfüllen, z. B. FIPS 140-2 und HIPAA.
- **Verbesserte Reparaturgeschwindigkeit für Volumes von „Direkte Speicherplätze“** : Führen Sie die Reparatur von Volumes schnell und nahtlos durch.

Version 20H2 von Windows Admin Center verfügt auch über eine neue Benutzeroberfläche für die Aktualisierung auf Windows Server-basierte Cluster, einschließlich der ursprünglichen Azure Stack HCI-Lösungen. Sie können zwar den neuen Assistenten für die Clustererstellung mit Windows Server verwenden, aber die Erstellung von Windows Server-Clustern mit „Direkte Speicherplätze“ ist hiermit nicht möglich. Für diese Aufgabe benötigen Sie das Azure Stack HCI-Betriebssystem.

## <a name="roles-you-can-run-without-virtualizing"></a>Rollen, die ohne Virtualisierung ausgeführt werden können

Da Azure Stack HCI als Virtualisierungshost ausgelegt ist, auf dem Sie alle Workloads auf virtuellen Computern ausführen, können Sie gemäß den Azure Stack HCI-Nutzungsbedingungen nur die Vorgänge durchführen, die für das Hosten der virtuellen Computer erforderlich sind.

Dies bedeutet, dass Sie die folgenden Serverrollen ausführen können:

- Hyper-V
- Netzwerkcontroller und andere Komponenten, die für softwaredefinierte Netzwerke (SDN) erforderlich sind

Alle anderen Rollen und Apps müssen aber innerhalb der VMs ausgeführt werden. Beachten Sie, dass Sie Hilfsprogramme, Apps und Dienste ausführen können, die für die Verwaltung und Integrität der gehosteten VMs benötigt werden.

## <a name="video-based-learning"></a>Lernen anhand von Videos

Das Video zum erweiterten Azure-Netzwerk finden Sie unter folgendem Link:

- [Nahtlose Konnektivität mit Azure mit Windows Server und Hybridnetzwerken](https://www.youtube.com/watch?v=do2_4Y2p9dk)

Videos zur ursprünglichen Windows Server 2019-basierten Version von Azure Stack HCI:

- [Übersicht über Azure Stack und Azure Stack HCI](https://aka.ms/AzureStackOverviewVideo)
- [Microsoft Ignite Live 2019 – Erste Schritte mit Azure Stack HCI](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Informationen zu Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [Modernisieren von Einzelhandelsgeschäften oder Zweigstellen mit Azure Stack HCI](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Neuerungen in Azure Stack HCI: 45 Dinge in 45 Minuten](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [Schneller Einstieg in die Azure Stack HCI-Bereitstellung](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [Verkleinerung von Daten: Datendeduplizierung in Azure Stack HCI](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Anmerkungen aus der Praxis zu Azure Stack HCI von Dave Kawula](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Video von einer virtuellen Hybrid Cloud-Veranstaltung:

- [Azure Stack HCI | Virtuelle Hybrid Cloud-Veranstaltung](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Stack HCI mit Windows Admin Center](get-started.md)
