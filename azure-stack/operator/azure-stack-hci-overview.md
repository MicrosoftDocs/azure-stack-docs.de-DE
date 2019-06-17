---
title: 'Azure Stack HCI: Übersicht'
description: Azure Stack HCI ist ein hyperkonvergenter Windows Server 2019-Cluster, für den validierte Hardware zum lokalen Ausführen von virtualisierten Workloads verwendet wird. Sie können optional auch eine Verbindung mit Azure-Diensten für cloudbasierte Sicherungen, Sitewiederherstellung und andere Zwecke herstellen. Für Azure Stack HCI-Lösungen wird von Microsoft validierte Hardware verwendet, um für eine optimale Leistung und Zuverlässigkeit zu sorgen, und sie verfügen über Unterstützung für Technologie wie NVMe-Laufwerke, persistenter Speicher und RDMA-Netzwerke (Remote-Direct Memory Access).
ms.technology: storage
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.prod: windows-server-threshold
ms.date: 05/31/2019
ms.openlocfilehash: c3f25f38429b58d8d72037ff4ed9758d9993a2c5
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453603"
---
# <a name="azure-stack-hci-overview"></a>Azure Stack HCI: Übersicht

>Gilt für: Windows Server 2019

Azure Stack HCI ist ein hyperkonvergenter Windows Server 2019-Cluster, für den validierte Hardware zum lokalen Ausführen von virtualisierten Workloads verwendet wird. Sie können optional auch eine Verbindung mit Azure-Diensten für cloudbasierte Sicherungen, Sitewiederherstellung und andere Zwecke herstellen. Für Azure Stack HCI-Lösungen wird von Microsoft validierte Hardware verwendet, um für eine optimale Leistung und Zuverlässigkeit zu sorgen, und sie verfügen über Unterstützung für Technologie wie NVMe-Laufwerke, persistenter Speicher und RDMA-Netzwerke (Remote-Direct Memory Access).

Azure Stack HCI ist eine Lösung, in der mehrere Produkte kombiniert sind:

- Hardware von einem OEM-Partner

- Windows Server 2019 Datacenter Edition

- Windows Admin Center

- Azure-Dienste (optional)

![Azure Stack HCI ist eine hyperkonvergente Lösung von Microsoft, die bei vielen Hardwarepartnern erhältlich ist.](media/azure-stack-hci/azure-stack-hci-solution.png)

Azure Stack HCI ist eine hyperkonvergente Lösung von Microsoft, die bei vielen Hardwarepartnern erhältlich ist. Erwägen Sie die folgenden Szenarien für eine hyperkonvergente Lösung, damit Sie ermitteln können, ob Azure Stack HCI die beste Lösung für Ihre Anforderungen ist:

- **Aktualisieren veralteter Hardware**: Ersetzen Sie ältere Server und Speicherinfrastruktur, und führen Sie virtuelle Windows- und Linux-Computer lokal und im Edgebereich mit vorhandenen IT-Fähigkeiten und Tools aus.

- **Zusammenfassen virtualisierter Workloads**: Fassen Sie ältere Apps in einer effizienten hyperkonvergenten Infrastruktur zusammen. Nutzen Sie die gleichen effizienten Cloudoptionen, die zum Ausführen von Datencentern mit Hyperskalierung, z. B. Microsoft Azure, verwendet werden.

- **Herstellen einer Verbindung mit Azure für Hybrid Cloud-Dienste**: Optimieren Sie den Zugriff auf Cloudverwaltungs- und Sicherheitsdienste in Azure, z. B. externe Sicherung, Sitewiederherstellung, cloudbasierte Überwachung und mehr.

## <a name="the-azure-stack-family"></a>Azure Stack-Familie

Azure Stack HCI ist Teil der Azure- und Azure Stack-Familie, und es wird die gleiche softwaredefinierte Compute-, Speicher- und Netzwerksoftware wie für Azure Stack verwendet. Hier ist eine kurze Übersicht über die unterschiedlichen Lösungen angegeben:

- [Azure](https://azure.microsoft.com): Verwenden von öffentlichen Clouddiensten
- [Azure Stack](azure-stack-overview.md): Lokales Betreiben von Clouddiensten
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci): Ausführen von virtualisierten Apps mit optionalen Verbindungen mit Azure

![In Azure und Azure Stack werden Clouddienste ausgeführt, während in Azure Stack HCI virtualisierte Anwendungen lokal ausgeführt werden.](media/azure-stack-hci/azure-family.png)

|Azure: Verwenden von öffentlichen Clouddiensten|Azure Stack: Lokales Betreiben von Clouddiensten|Azure Stack HCI: Lokales Ausführen von virtualisierten Apps|
|-----------------|-----------------|-----------------|
|Ist für bedarfsorientierte Self-Service-Computeressourcen bestimmt, um vorhandene Apps zu migrieren und zu modernisieren und neue cloudnative Apps zu entwickeln.|Sie können Cloudanwendungen im Edgebereich erstellen und ausführen, wenn die Verbindung getrennt ist, oder um rechtliche Anforderungen zu erfüllen, indem einheitliche lokale Azure-Dienste verwendet werden.| Führen Sie virtualisierte Anwendungen lokal aus, tauschen Sie veraltete Serverinfrastruktur aus, und fassen Sie sie zusammen. Stellen Sie anschließend für Clouddienste eine Verbindung mit Azure her.|
|Mehr als 100 Dienste, die in 54 Regionen weltweit verfügbar sind|Azure-VMs für Windows und Linux, Azure-Web-Apps und Functions, Azure Key Vault, Azure Resource Manager, Azure Marketplace, Container, Azure IoT und Event Hubs, Verwaltungstools (Pläne, Angebote, rollenbasierte Zugriffssteuerung)|Validierte HCI-Lösungen auf Grundlage von Hyper-V und „Direkte Speicherplätze“ mit Windows Server 2019 und Windows Admin Center für die Verwaltung und integrierten Zugriff auf Azure-Dienste|

Weitere Informationen:

- Informieren Sie sich auf unserer Website mit den Lösungen für [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci).
- Sehen Sie sich das Video mit den Microsoft-Experten Jeff Woolsey und Vijay Tewari an, in dem diese über die [neuen Azure Stack HCI-Lösungen](https://aka.ms/AzureStackOverviewVideo) sprechen.

## <a name="hyperconverged-efficiencies"></a>Effizienz durch Hyperkonvergenz

In Azure Stack HCI-Lösungen sind stark virtualisierte Compute-, Speicher- und Netzwerkeinheiten auf branchenüblichen x86-Servern und -Komponenten vereint. Das Kombinieren von Ressourcen in demselben Cluster erleichtert Ihnen die Bereitstellung, Verwaltung und Skalierung. Führen Sie die Verwaltung über eine Befehlszeilenautomatisierung Ihrer Wahl oder mit Windows Admin Center durch.

Erzielen Sie branchenführende VM-Leistung für Ihre Serveranwendungen mit Hyper-V, der grundlegenden Hypervisor-Technologie der Microsoft-Cloud, und der Technologie „Direkte Speicherplätze“ mit integrierter Unterstützung für NVMe, persistenten Speicher und RDMA-Netzwerke (Remote-Direct Memory Access).

Sorgen Sie für den Schutz von Apps und Daten, indem Sie abgeschirmte virtuelle Computer, Mikrosegmentierung von Netzwerken und native Verschlüsselung nutzen.

## <a name="hybrid-capabilities"></a>Hybridfunktionen

Sie können die Vorteile der Zusammenarbeit der Cloud und der lokalen Umgebung nutzen, indem Sie eine hyperkonvergente Infrastrukturplattform in der öffentlichen Cloud verwenden. Ihr Team kann mit dem Aufbauen von Cloudfähigkeiten mit integrierter Integration in Azure-Infrastruktur-Verwaltungsdienste beginnen:

- Azure Site Recovery für Hochverfügbarkeit und Notfallwiederherstellung als Dienst (Disaster-Recovery-as-a-Service, DRaaS)

- Azure Monitor, ein zentraler Hub, in dem Sie die Aktivität Ihrer Anwendungen, Netzwerke und Infrastrukturen überwachen können – mithilfe erweiterter KI-Analysen

- Cloud Witness, um Azure als einfache Entscheidungshilfe für ein Clusterquorum zu verwenden

- Azure Backup für Offsitedatenschutz und den Schutz vor Ransomware

- Azure-Updateverwaltung für die Bewertung und Bereitstellung von Updates für Windows-VMs, die in Azure und lokal ausgeführt werden

- Azure-Netzwerkadapter zum Verbinden lokaler Ressourcen mit Ihren VMs in Azure über ein Point-to-Site-VPN

- Synchronisieren Sie Ihren Dateiserver mit der Cloud, indem Sie die Azure-Dateisynchronisierung verwenden.

Weitere Informationen finden Sie unter [Verbinden von Windows Server mit Azure Hybrid-Diensten](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

## <a name="management-tools-and-system-center"></a>Verwaltungstools und System Center

Für Azure Stack HCI wird die gleiche Virtualisierung und softwaredefinierte Speicher- und Netzwerksoftware wie für Azure Stack verwendet. Mit Azure Stack HCI verfügen Sie aber über alle Administratorrechte im Cluster und können die entsprechende Technologie direkt verwalten:

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Direkte Speicherplätze](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Softwaredefiniertes Netzwerk](https://docs.microsoft.com/windows-server/networking/sdn/)
- [Failoverclustering](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

Sie können für die Verwaltung dieser Technologie die folgenden Tools verwenden:

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- Andere Verwaltungstools, z. B. [Server-Manager](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager) und MMC-Snap-Ins
- Tools, die nicht von Microsoft stammen, z. B. 5Nine Manager

Wenn Sie sich für System Center entscheiden, um Ihre Infrastruktur bereitzustellen und zu verwalten, verwenden Sie System Center Virtual Machine Management (VMM) und System Center Operations Manager. Mit VMM führen Sie die Bereitstellung und Verwaltung der Ressourcen durch, die zum Erstellen und Bereitstellen von virtuellen Computern und Diensten in privaten Clouds benötigt werden. Mit Operations Manager überwachen Sie Dienste, Geräte und Vorgänge in Ihrem gesamten Unternehmen, um Probleme zu identifizieren, bei denen sofort Maßnahmen ergriffen werden müssen.

## <a name="hardware-partners"></a>Hardwarepartner

Sie können validierte Azure Stack HCI-Lösungen, für die Windows Server 2019 ausgeführt wird, bei 15 Partnern erwerben. Ihr bevorzugter Microsoft-Partner führt für Sie die Einrichtung ohne lange Entwurfs- und Builddauern durch. Er dient auch als zentraler Ansprechpartner für die Implementierung und Supportleistungen.

Besuchen Sie unsere [Azure Stack HCI-Website](https://azure.microsoft.com/overview/azure-stack/hci), um sich über die mehr als 70 Azure Stack HCI-Lösungen zu informieren, die diese Microsoft-Partner derzeit im Angebot haben: ASUS, Axellio, bluechip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD und Supermicro.

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="what-do-azure-stack-and-azure-stack-hci-solutions-have-in-common"></a>Welche Gemeinsamkeiten haben Azure Stack und Azure Stack HCI-Lösungen?

Azure Stack HCI-Lösungen umfassen dieselben Hyper-V-basierten, softwaredefinierten Compute-, Speicher- und Netzwerktechnologien wie Azure Stack. Beide Angebote erfüllen strenge Test- und Validierungsvorgaben, um die Zuverlässigkeit und Kompatibilität mit der zugrunde liegenden Hardwareplattform sicherzustellen.

### <a name="how-are-they-different"></a>Inwiefern unterscheiden sie sich?

Mit Azure Stack führen Sie Clouddienste lokal aus. Sie können Azure-IaaS- und -PaaS-Dienste lokal ausführen, um Cloudanwendungen unabhängig vom Standort konsistent erstellen und ausführen zu können. Die Verwaltung wird lokal über das Azure-Portal durchgeführt.

Mit Azure Stack HCI führen Sie virtualisierte Workloads lokal aus, und für die Verwaltung nutzen Sie Windows Admin Center und vertraute Windows Server-Tools. Sie können optional eine Verbindung mit Azure für Hybridszenarien herstellen, z. B. cloudbasierte Sitewiederherstellung, Überwachung und mehr.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Weshalb fügt Microsoft sein HCI-Angebot der Azure Stack-Familie hinzu?

Die hyperkonvergente Technologie von Microsoft ist bereits die Basis für Azure Stack.

Viele Microsoft-Kunden arbeiten mit komplexen IT-Umgebungen. Unser Ziel ist es, unseren Kunden die ideale Technologie für ihre Unternehmensanforderungen bereitzustellen. Azure Stack HCI ist die Weiterentwicklung softwaredefinierter Windows Server-Lösungen, die auf Windows Server 2016 basieren und in der Vergangenheit von unseren Hardwarepartnern angeboten wurden. Wir haben das Angebot der Azure Stack-Familie hinzugefügt, weil wir immer mehr neue Optionen für die nahtlose Verbindung mit Azure für Infrastrukturverwaltungsdienste veröffentlichen.

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Muss für Azure Stack HCI eine Verbindung mit Azure hergestellt werden?

Nein. Dies ist optional. Sie können die Integration in Azure für Hybridszenarien nutzen, z. B. externe Sicherung und Notfallwiederherstellung sowie cloudbasierte Überwachung und Updateverwaltung, aber diese Vorgehensweise ist optional. Die Ausführung ist auch ohne Internetverbindung kein Problem.

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Welche Beziehung besteht zwischen Azure Stack HCI und Windows Server?

Windows Server 2019 ist die Grundlage nahezu jedes Azure-Produkts. Alle für Sie wichtigen Features werden weiterhin bereitgestellt und in Windows Server unterstützt. Azure Stack HCI ist die empfohlene Anwendung zur lokalen Bereitstellung von HCI, indem die von Microsoft validierte Hardware unserer Partner verwendet wird.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack"></a>Kann ich ein Upgrade von Azure Stack HCI auf Azure Stack durchführen? 

Nein. Kunden können Ihre Workloads aber von Azure Stack HCI zu Azure Stack oder Azure migrieren.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>Für welche Azure-Dienste kann ich eine Verbindung mit Azure Stack HCI herstellen?

Eine aktualisierte Liste mit Azure-Diensten, mit denen Sie für Azure Stack HCI eine Verbindung herstellen können, finden Sie unter [Connecting Windows Server to Azure hybrid services](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index) (Verbinden von Windows Server mit Azure-Hybriddiensten).

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack"></a>Welche Kosten ergeben sich für Azure Stack HCI im Vergleich zu Azure Stack? 

Azure Stack wird als vollständig integriertes System angeboten, in dem auch die Dienste und der Support enthalten sind. Sie können Azure Stack von unseren Partnern als System erwerben, das Sie selbst verwalten, oder als vollständig verwalteten Dienst. Neben dem Basissystem werden Azure-Dienste, die in Azure Stack oder Azure ausgeführt werden, nutzungsbasiert in Rechnung gestellt.

Für Azure Stack HCI-Lösungen wird das herkömmliche Kaufmodell verwendet. Sie können validierte Hardware von Azure Stack HCI-Partnern erwerben, und Sie können Software (Windows Server 2019 Datacenter Edition mit softwaredefinierten Rechenzentrumsfunktionen und Windows Admin Center) über verschiedene bekannte Kanäle erwerben. Azure-Dienste, die Sie mit Windows Admin Center verwenden, werden einem Azure-Abonnement in Rechnung gestellt.

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>Wie kann ich Azure Stack HCI-Lösungen kaufen?

Folgen Sie diesen Schritten:

1. Erwerben Sie ein von Microsoft validiertes Hardwaresystem von einem unserer Partner.
1. Installieren Sie zur Verwaltung die Windows Server 2019 Datacenter Edition und Windows Admin Center. So erhalten Sie auch die Möglichkeit, eine Verbindung mit Azure herzustellen, um Clouddienste zu nutzen.
1. Verwenden Sie Ihr Azure-Konto, um cloudbasierte Verwaltungs- und Sicherheitsdienste an Ihre Workloads anzufügen.

![Wählen Sie den Hardwarepartner und die Konfiguration aus, der bzw. die Ihre Anforderungen am besten erfüllt, um Azure Stack HCI-Lösungen zu erwerben.](media/azure-stack-hci/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-and-azure-stack-hci"></a>Vergleich von Azure Stack und Azure Stack HCI

Während der digitalen Transformation Ihrer Organisation gelangen Sie unter Umständen zu der Erkenntnis, dass sie mit Diensten in der öffentlichen Cloud noch schneller moderne Architekturen erstellen und bestehende Apps überarbeiten können. Viele Workloads müssen aber beispielsweise aus technischen oder gesetzlichen Gründen weiterhin lokal verarbeitet werden. Anhand der folgenden Tabelle können Sie ermitteln, mit welcher Microsoft Hybrid Cloud-Strategie Ihre Anforderungen an Komponenten und Standorte erfüllt und innovative Cloudoptionen für Workloads unabhängig vom Ort bereitgestellt werden.

|Azure Stack|Azure Stack HCI|
|--------|-------|
|Neue Fähigkeiten, innovative Prozesse|Gleiche Fähigkeiten, vertraute Prozesse|
|Azure-Dienste in Ihrem Rechenzentrum|Verbindung Ihres Rechenzentrums mit Azure-Diensten|

### <a name="when-to-use-azure-stack"></a>Nutzung von Azure Stack

|Azure Stack|Azure Stack HCI|
|--------|-------|
|Verwenden Sie Azure Stack für Self-Service-IaaS-Zwecke (Infrastructure-as-a-Service) mit starker Isolation und präziser Nutzungsnachverfolgung und verbrauchsbasierter Kostenzuteilung für mehrere zusammengefasste Mandanten. Ideal für Dienstanbieter und private Clouds von Unternehmen geeignet. Vorlagen aus dem Azure Marketplace.|In Azure Stack HCI wird Mehrinstanzenfähigkeit nicht nativ erzwungen oder bereitgestellt.|
|Verwenden Sie Azure Stack, um Apps zu entwickeln und auszuführen, die auf lokalen PaaS-Diensten (Platform-as-a-Service) wie Web-Apps, Functions oder Event Hubs basieren. Diese Dienste werden unter Azure Stack genauso wie in Azure ausgeführt und stellen eine einheitliche Hybridentwicklungs- und Laufzeitumgebung dar.|In Azure Stack HCI werden keine PaaS-Dienste lokal ausgeführt.
|Nutzen Sie Azure Stack, um die Bereitstellung und den Betrieb von Apps mit DevOps-Methoden, z. B. „Infrastructure-as-Code“ und Continuous Integration und Continuous Deployment (CI/CD), und benutzerfreundlichen Features, z. B. einheitlichen Azure-VM-Erweiterungen, zu modernisieren. Ideal geeignet für Entwicklungs- und DevOps-Teams.|Azure Stack HCI enthält keine nativen DevOps-Tools.

### <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

|Azure Stack|Azure Stack HCI|
|---------------|---------------|
|Für Azure Stack sind mindestens vier Knoten und eigene Netzwerkswitches erforderlich.|Verwenden Sie Azure Stack HCI für den minimalen Speicherbedarf für abgelegene Büros und Zweigstellen. Beginnen Sie mit nur zwei Serverknoten und benachbarten Netzwerken, um für Einfachheit und Kostengünstigkeit zu sorgen. Die Hardwareangebote beginnen bei vier Laufwerken mit 64 GB Speicher zu einem Preis von deutlich unter 10.000 USD pro Knoten.
|Azure Stack schränkt die Hyper-V-Konfigurierbarkeit und den Featuresatz ein, um Einheitlichkeit mit Azure zu erzielen.|Verwenden Sie Azure Stack HCI für eine einfache Hyper-V-Virtualisierung für klassische Unternehmens-Apps wie Exchange, SharePoint und SQL Server sowie für die Virtualisierung von Windows Server-Rollen wie Dateiserver, DNS, DHCP, IIS und AD. Uneingeschränkter Zugriff auf alle Hyper-V-Features, z. B. abgeschirmte VMs.|
|Diese Infrastrukturtechnologie wird von Azure Stack nicht verfügbar gemacht.|Verwenden Sie Azure Stack HCI, um eine softwaredefinierte Infrastruktur anstelle von verwalteten Speicherarrays oder Netzwerkgeräten zu nutzen, ohne dass größere Änderungen an der Architektur vorgenommen werden müssen. Die integrierten Optionen für Hyper-V, „Direkte Speicherplätze“ und softwaredefinierte Netzwerke (SDN) sind direkt zugänglich und können direkt verwaltet werden.|