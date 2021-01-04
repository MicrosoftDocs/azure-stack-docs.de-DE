---
title: Übersicht über die Azure Stack HCI-Lösung
description: Azure Stack HCI ist eine HCI-Clusterlösung (Hyperconverged Infrastruktur, hyperkonvergente Infrastruktur), mit der virtualisierte Windows- und Linux-Workloads und ihr Speicher in einer lokalen Hybridumgebung gehostet werden. Mit Azure-Hybriddiensten wird der Cluster um Funktionen erweitert, z. B. cloudbasierte Überwachung, Site Recovery und VM-Sicherungen, und Sie erhalten einen zentralen Überblick über Ihre gesamten Azure Stack HCI-Bereitstellungen im Azure-Portal.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/15/2020
ms.openlocfilehash: 6eef8388367bb1bba3f419fba5b61c54fd729743
ms.sourcegitcommit: 32d77de1a554315f53473407279e464a72aa9aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97515021"
---
# <a name="azure-stack-hci-solution-overview"></a>Übersicht über die Azure Stack HCI-Lösung

> Gilt für: Azure Stack HCI, Version 20H2

Azure Stack HCI ist eine HCI-Clusterlösung (Hyperconverged Infrastruktur, hyperkonvergente Infrastruktur), mit der virtualisierte Windows- und Linux-Workloads und ihr Speicher in einer lokalen Hybridumgebung gehostet werden. Mit Azure-Hybriddiensten wird der Cluster um Funktionen erweitert, z. B. cloudbasierte Überwachung, Site Recovery und VM-Sicherungen, und Sie erhalten einen zentralen Überblick über Ihre gesamten Azure Stack HCI-Bereitstellungen im Azure-Portal. Sie können den Cluster mit Ihren vorhandenen Tools verwalten (einschließlich Windows Admin Center und PowerShell).

Azure Stack HCI, Version 20h2 ist nun [für den Download verfügbar](https://azure.microsoft.com/products/azure-stack/hci/hci-download/). Diese Version ist für lokale Cluster bestimmt, in denen virtualisierte Workloads ausgeführt werden, und sie verfügt über integrierte Hybrid Cloud-Verbindungen. Azure Stack HCI wird daher als Azure-Dienst bereitgestellt und basierend auf einem Azure-Abonnement abgerechnet. Azure Stack HCI bietet nun auch die Möglichkeit zum Hosten von Azure Kubernetes Service. Ausführliche Informationen finden Sie unter [Was ist Azure Kubernetes Service in Azure Stack HCI?](../aks-hci/overview.md).

Nehmen Sie sich einige Minuten Zeit, um sich das Video zu den allgemeinen Features von Azure Stack HCI anzusehen:

> [!VIDEO https://www.youtube.com/embed/fw8RVqo9dcs]

Azure Stack HCI ist im Grunde genommen eine Lösung, die Folgendes vereint:

- Überprüfte Hardware von einem OEM-Partner
- Azure Stack HCI-Betriebssystem
- Windows Admin Center
- Azure-Dienste

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="Das Azure Stack HCI-Betriebssystem wird auf überprüfter Hardware ausgeführt, mit Windows Admin Center verwaltet und ermöglicht die Verbindungsherstellung mit Azure." border="false":::

Version 20H2 von Azure Stack HCI verfügt über neue Funktionen, die unter Windows Server nicht vorhanden sind. Ein Beispiel hierfür ist die Verwendung von Windows Admin Center zum Erstellen eines hyperkonvergenten Clusters, bei dem „Direkte Speicherplätze“ genutzt wird, um ein besseres Preis-Leistungs-Verhältnis für Speicher zu erzielen. Hierzu gehört auch die Option zum Strecken des Clusters über mehrere Standorte, um dafür ein automatisches Failover zu ermöglichen. Weitere Informationen finden Sie unter [Neuerungen in Azure Stack HCI](#whats-new-in-azure-stack-hci).

## <a name="use-cases-for-azure-stack-hci"></a>Anwendungsfälle für Azure Stack HCI

Es gibt viele Anwendungsfälle für Azure Stack HCI, obwohl die Lösung nicht für nicht virtualisierte Workloads bestimmt ist. Kunden wählen für die folgenden Szenarien häufig Azure Stack HCI aus:

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
- **Abrechnung:** Bezahlen Sie Azure Stack HCI über Ihr Azure-Abonnement.

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

- Einen Cluster mit zwei oder mehr Servern aus dem [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net), den Sie über Ihren bevorzugten Microsoft-Hardwarepartner erwerben.
- [Ein Azure-Abonnement](https://azure.microsoft.com/).
- Eine Internetverbindung für jeden Server im Cluster, die über ausgehenden HTTPS-Datenverkehr mindestens alle 30 Tage eine Verbindung mit bekannten Azure-Endpunkten herstellen kann.
- Für über mehrere Standorte gestreckte Cluster benötigen Sie mindestens eine 1 GB-Standortverbindung (RDMA-Verbindung mit 25 GB empfohlen) mit einer durchschnittlichen Latenz von 5 ms pro Roundtrip, falls Sie eine synchrone Replikation durchführen möchten, bei der Schreibvorgänge gleichzeitig an beiden Standorten durchgeführt werden.
- Wenn Sie SDN (Software Defined Networking) verwenden möchten, benötigen Sie eine VHD (virtual hard disk, virtueller Datenträger) für das Azure Stack HCI-Betriebssystem, um Netzwerkcontroller-VMs zu erstellen (mehr dazu finden Sie unter [Planen der Bereitstellung von Netzwerkcontrollern](concepts/network-controller.md))

Weitere Informationen finden Sie unter [Systemanforderungen](concepts/system-requirements.md). Informationen zu den Anforderungen an Azure Kubernetes Service in Azure Stack HCI finden Sie unter [Was Sie für die ersten Schritte benötigen](../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="hardware-partners"></a>Hardwarepartner

Sie können überprüfte Azure Stack HCI-Lösungen von Ihrem bevorzugten Microsoft-Partner erwerben, um ohne großen Entwurfs- und Buildaufwand starten zu können. Microsoft-Partner dienen auch als zentrale Ansprechpartner für die Implementierung und für Supportleistungen. Sie können entweder überprüfte Knoten oder ein integriertes System erwerben, auf dem das Azure Stack HCI-Betriebssystem vorinstalliert ist und das über Partnererweiterungen für Treiber- und Firmwareupdates verfügt.

Besuchen Sie die Seite mit den [Azure Stack HCI-Lösungen](https://azure.microsoft.com/overview/azure-stack/hci), oder sehen Sie sich im [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net) die mehr als 70 Azure Stack HCI-Lösungen an, die bei Microsoft-Partnern derzeit erhältlich sind. Beispiele für Partner sind ASUS, Axellio, Blue Chip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD und Supermicro.

## <a name="software-partners"></a>Softwarepartner

Es gibt eine Vielzahl von Microsoft-Partnern, die an Software zur Erweiterung der Funktionen von Azure Stack HCI arbeiten, während IT-Administratoren gleichzeitig die Nutzung von vertrauten Tools ermöglicht wird. Weitere Informationen finden Sie unter [Hilfsprogrammanwendungen für Azure Stack HCI](concepts/utility-applications.md).

## <a name="licensing-billing-and-pricing"></a>Lizenzierung, Abrechnung und Preise

Die Abrechnung für Azure Stack HCI basiert auf einer monatlichen Abonnementgebühr pro physischem Prozessorkern und nicht auf einer zeitlich unbeschränkten Lizenz. Wenn Kunden eine Verbindung mit Azure herstellen, wird die Anzahl von verwendeten Kernen automatisch hochgeladen und zu Abrechnungszwecken ausgewertet. Über die physischen Prozessorkerne hinaus variieren die Kosten nicht basierend auf dem Verbrauch. Dies bedeutet, dass eine höhere Zahl von VMs nicht mehr kostet und Kunden, die virtuelle Umgebungen mit höherer Dichte betreiben können, einen Vorteil erzielen.

Kunden können entweder überprüfte Server von einem Hardwarepartner erwerben, auf denen das Azure Stack HCI-Betriebssystem vorinstalliert ist, oder sie können überprüfte Bare-Metal-Server bei einem OEM kaufen und dann den Azure Stack HCI-Dienst abonnieren und [das Azure Stack HCI-Betriebssystem herunterladen](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="the-azure-stack-family"></a>Azure Stack-Familie

Azure Stack HCI ist Teil der Azure- und Azure Stack-Familie, und es wird die gleiche softwaredefinierte Compute-, Speicher- und Netzwerksoftware verwendet wie für Azure Stack Hub. Unten ist eine kurze Zusammenfassung der unterschiedlichen Lösungen angegeben. Weitere Informationen finden Sie unter [Unterschiede zwischen der globalen Azure-Infrastruktur, Azure Stack Hub und Azure Stack HCI](../operator/compare-azure-azure-stack.md).

- [Azure:](https://azure.microsoft.com) Nutzen Sie öffentliche Clouddienste für bedarfsorientierte Self-Service-Computeressourcen, um vorhandene Apps zu migrieren und zu modernisieren und neue cloudnative Apps zu entwickeln.
- [Azure Stack Edge](/azure/databox-online/data-box-edge-overview): Beschleunigen Sie Workloads für maschinelles Lernen, und führen Sie lokale Containeranwendungen oder virtualisierte Workloads auf einem in der Cloud verwalteten Gerät aus.
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci): Führen Sie virtualisierte Anwendungen aus, ersetzen und konsolidieren Sie veraltete Serverinfrastruktur, und stellen Sie für Clouddienste eine Verbindung mit Azure her.
- [Azure Stack Hub](../operator/azure-stack-overview.md): Führen Sie mithilfe einheitlicher Azure-Dienste Cloudanwendungen lokal aus, entweder nach dem Trennen der Verbindung oder zum Erfüllen rechtlicher Anforderungen.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Diagramm zu den Lösungen der Azure Stack-Familie" border="false":::

## <a name="whats-new-in-azure-stack-hci"></a>Neuerungen in Azure Stack HCI

In Windows Admin Center Version 2009 wurden Azure Stack HCI mehrere Funktionen hinzugefügt, u. a.:

- **Azure Kubernetes Service-Hostfunktionen:** Sie können nun eine Vorschauversion von [Azure Kubernetes Service in Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) installieren.
- **Einbindung von SDN (Software-Defined Networking, softwaredefinierte Netzwerke) in den Clustererstellungs-Assistenten:** Der Clustererstellungs-Assistent enthält jetzt die Option zum Bereitstellen des Netzwerkcontrollerfeatures [Software-Defined Networking (SDN)](concepts/software-defined-networking.md) während der [Clustererstellung](deploy/create-cluster.md#step-5-sdn-optional).
- **RDMA-Erweiterungen (Remote Direct Memory Access) im Assistenten für die Clustererstellung**: Der Assistent für die Clustererstellung kann nun RDMA für iWarp- und RoCE-Netzwerkadapter konfigurieren, einschließlich DCB (Data Center Bridging).

Weitere Informationen zu neuen Features finden Sie unter [Ankündigung der allgemeinen Verfügbarkeit der Erweiterung für die Clustererstellung in Windows Admin Center](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/announcing-general-availability-of-the-cluster-creation/ba-p/1978332).

Cluster, in denen Version 20H2 von Azure Stack HCI ausgeführt wird, verfügen im Vergleich zu Windows Server 2019-basierten Lösungen über die folgenden neuen Features:

- **Neue Funktionen in Windows Admin Center**: Da hyperkonvergente Cluster über eine intuitive Benutzeroberfläche erstellt und aktualisiert werden können, ist die Nutzung von Azure Stack HCI einfacher als jemals zuvor.
- **Gestreckte Cluster für automatisches Failover**: Das Clustering für mehrere Standorte mit Replikation von Speicherreplikaten und automatischem VM-Failover ermöglicht eine native Notfallwiederherstellung und Geschäftskontinuität.
- **Affinitäts- und Antiaffinitätsregeln**: Diese Regeln können auf ähnliche Weise wie Verfügbarkeitszonen in Azure verwendet werden, um VMs und Speicher in Clustern mit mehreren Fehlerdomänen, z. B. gestreckten Clustern, zusammen oder getrennt anzuordnen.
- **Azure-Portal-Integration**: Die Benutzeroberfläche im Azure-Portal für Azure Stack HCI ist dafür ausgelegt, Ihre gesamten Azure Stack HCI-Cluster weltweit anzuzeigen, und neue Features befinden sich gerade in der Entwicklung.
- **GPU-Beschleunigung für Hochleistungsworkloads**: KI- und ML-Anwendungen können davon profitieren, wenn die Leistung mit GPUs gesteigert wird.
- **BitLocker-Verschlüsselung**: Sie können jetzt BitLocker zum Verschlüsseln des Inhalts von Datenvolumes unter Azure Stack HCI verwenden, um dafür zu sorgen, dass Behörden und andere Kunden bestimmte Standards erfüllen, z. B. FIPS 140-2 und HIPAA.
- **Verbesserte Reparaturgeschwindigkeit für Volumes von „Direkte Speicherplätze“** : Führen Sie die Reparatur von Volumes schnell und nahtlos durch.

Sehen Sie sich [dieses Video von Microsoft Inspire](https://www.youtube.com/watch?v=DPG7wGhh3sAa) an, um mehr über die Neuerungen in Azure Stack HCI 20H2 zu erfahren.

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

- [Herunterladen von Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)
- [Schnellstart: Erstellen eines Azure Stack HCI-Clusters und Registrieren des Clusters bei Azure](deploy/deployment-quickstart.md)
- [Verwenden von Azure Stack HCI mit Windows Admin Center](get-started.md)
