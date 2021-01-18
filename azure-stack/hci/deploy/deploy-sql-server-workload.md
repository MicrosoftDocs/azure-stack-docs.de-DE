---
title: Bereitstellen von SQL Server auf Azure Stack HCI (AKS-HCI)
description: Dieses Thema enthält Anleitungen zum Bereitstellen von SQL Server auf Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/11/2021
ms.openlocfilehash: 8f93a56840d4e4410a42aafe117f6cb1eebe84b4
ms.sourcegitcommit: 5f3adb99b40fa4473955fa408e7ff63d5e1b439f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98105347"
---
# <a name="deploy-sql-server-on-azure-stack-hci"></a>Bereitstellen von SQL Server auf Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2; SQL Server (alle unterstützten Versionen)

Dieses Thema enthält Anleitungen zum Planen, Konfigurieren und Bereitstellen von SQL Server auf dem Azure Stack HCI-Betriebssystem. Das Betriebssystem stellt eine hyperkonvergente Infrastruktur (HCI) in Form einer Clusterlösung dar, die die Workloads von Windows und Linux und ihren Speicher virtualisiert in einer hybriden lokalen Umgebung hostet.

## <a name="solution-overview"></a>Lösungsübersicht
Azure Stack HCI bietet eine hoch verfügbare, kosteneffiziente und flexible Plattform zum Ausführen von SQL Server und direkten Speicherplätzen. Azure Stack HCI kann OLTP-Workloads (Online Transaction Processing), Data Warehouse und BI sowie KI und erweiterte Analysen für Big Data ausführen.

Die Flexibilität der Plattform ist insbesondere für unternehmenskritische Datenbanken wichtig. Sie können SQL Server auf virtuellen Computern (VMs) ausführen, die entweder Windows Server oder Linux verwenden, was es Ihnen ermöglicht, mehrere Datenbankworkloads zu konsolidieren und Ihrer Azure Stack HCI-Umgebung bei Bedarf weitere VMs hinzuzufügen. Azure Stack HCI ermöglicht außerdem die Integration von SQL Server in Azure Site Recovery, um eine cloudbasierte Lösung für Migration, Wiederherstellung und Schutz für die Daten Ihrer Organisation bereitzustellen, die zuverlässig und sicher ist.

## <a name="deploy-sql-server"></a>Dialogfeld „Deploy SQL Server“ (SQL Server bereitstellen)
In diesem Abschnitt wird allgemein erläutert, wie Sie Hardware für SQL Server auf Azure Stack HCI erwerben und Windows Admin Center zum Verwalten des Betriebssystems auf Ihren Servern verwenden. Informationen zum Einrichten von SQL Server, zur Überwachung und Leistungsoptimierung sowie zur Verwendung von Hochverfügbarkeit (HA) und Azure-Hybriddiensten sind ebenfalls enthalten.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Schritt 1: Erwerb von Hardware aus dem Azure Stack HCI-Katalog
Zuerst müssen Sie Hardware beschaffen. Die einfachste Möglichkeit besteht darin, Ihren bevorzugten Microsoft-Hardwarepartner im [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net) zu suchen und ein integriertes System mit vorinstalliertem Azure Stack HCI-Betriebssystem zu kaufen. Im-Katalog können Sie filtern, um Herstellerhardware anzuzeigen, die für diese Art Workload optimiert ist.

Andernfalls müssen Sie das Azure Stack HCI-Betriebssystem auf Ihrer eigenen Hardware bereitstellen. Details zu den Azure Stack HCI-Bereitstellungsoptionen und zum Installieren von Windows Admin Center finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](./operating-system.md).

Verwenden Sie als nächstes das Windows Admin Center, um [einen Azure Stack HCI-Cluster zu erstellen](./create-cluster.md).

### <a name="step-2-install-sql-server-on-azure-stack-hci"></a>Schritt 2: Installieren von SQL Server auf Azure Stack HCI
Je nach Ihren Anforderungen können Sie SQL Server auf VMs installieren, die entweder Windows Server oder Linux ausführen.

Anweisungen zum Installieren von SQL Server finden Sie unter:
- [SQL Server-Installationshandbuch für Windows](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server?view=sql-server-ver15&preserve-view=true).
- [Leitfaden für die Installation von SQL Server für Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup?view=sql-server-ver15&preserve-view=true).

### <a name="step-3-monitor-and-performance-tune-sql-server"></a>Schritt 3: Überwachen und Optimieren der Leistung von SQL Server
Microsoft bietet eine umfassende Reihe von Tools für die Überwachung von Ereignissen in SQL Server und für die Optimierung des physischen Datenbankentwurfs. Die Wahl des Tools hängt von der Art der Überwachung oder Optimierung ab, die Sie ausführen möchten.

Informationen zum Sicherstellen von Leistung und Integrität Ihrer SQL Server-Instanzen unter Azure Stack HCI finden Sie unter [Tools für die Leistungsüberwachung und -optimierung](https://docs.microsoft.com/sql/relational-databases/performance/performance-monitoring-and-tuning-tools?view=sql-server-ver15&preserve-view=true).

Informationen zur Optimierung von SQL Server 2017 und SQL Server 2016 finden Sie unter [Empfohlene Updates und Konfigurationsoptionen für SQL Server 2017 und SQL Server 2016 mit Arbeitsauslastungen mit hoher Leistung](https://support.microsoft.com/help/4465518/recommended-updates-and-configurations-for-sql-server).

### <a name="step-4-use-sql-server-high-availability-features"></a>Schritt 4: Verwenden der Hochverfügbarkeitsfunktionen von SQL Server
Azure Stack HCI nutzt [Windows Server-Failoverclustering mit SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) (WSFC), um SQL Server bei einem Hardwarefehler auf virtuellen Computern zu unterstützen. SQL Server bietet außerdem [Always On-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) (AG), um Hochverfügbarkeit auf Datenbankebene bereitzustellen, die zur Unterstützung bei Anwendungs- und Softwarefehlern konzipiert ist. Zusätzlich zu WSFC und AG kann Azure Stack HCI eine [Always On-Failoverclusterinstanz](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server) (FCI) verwenden, die auf der Technologie [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) für freigegebenen Speicher basiert.

Diese Optionen funktionieren alle mit dem Microsoft Azure-[Cloudzeugen](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) für die Quorum-Steuerung. Wir empfehlen die Verwendung von [AntiAffinity](https://docs.microsoft.com/windows-server/failover-clustering/cluster-affinity)-Clusterregeln in WSFC für VMs, die auf unterschiedlichen physischen Knoten platziert sind, um die Betriebszeit für SQL Server im Fall von Hostfehlern aufrecht zu erhalten, wenn Always On-Verfügbarkeitsgruppen konfiguriert sind.

### <a name="step-5-set-up-azure-hybrid-services"></a>Schritt 5: Einrichten von Azure-Hybriddiensten
Es gibt mehrere Azure-Hybriddienste, die Sie verwenden können, um Ihre SQL Server Daten und Anwendungen zusätzlich zu schützen. [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) ist eine Lösung für die Notfallwiederherstellung in Form eines Diensts (Disaster-Recovery-as-a-Service, DRaaS). Weitere Informationen zur Verwendung dieses Diensts zum Schützen des SQL Server Back-Ends einer Anwendung, um Workloads online zu halten, finden Sie unter [Einrichten der Notfallwiederherstellung für SQL Server](https://docs.microsoft.com/azure/site-recovery/site-recovery-sql).

Mit [Azure Backup](https://azure.microsoft.com/services/backup/) können Sie Sicherungsrichtlinien zum Schutz von Unternehmensworkloads definieren und die Sicherung und Wiederherstellung der SQL Server-Konsistenz unterstützen. Weitere Informationen zum Sichern Ihrer lokalen SQL-Daten finden Sie unter [installieren von Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup).

Alternativ können Sie die Funktion [SQL Server Managed Backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-ver15&preserve-view=true) in SQL Server verwenden, um Azure Blob Storage-Sicherungen zu verwalten.

Weitere Informationen zur Verwendung dieser Option, die sich für die externe Archivierung eignet, finden Sie unter: 

- [Tutorial: Verwenden des Microsoft Azure Blob Storage-Diensts mit SQL Server 2016](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016?view=sql-server-ver15&preserve-view=true)
- [Schnellstart: Sicherung und Wiederherstellung von SQL Server mit dem Azure Blob Storage-Dienst](https://docs.microsoft.com/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service?view=sql-server-ver15&tabs=SSMS&preserve-view=true)

Zusätzlich zu diesen Sicherungsszenarien können Sie auch weitere Datenbankdienste einrichten, die SQL Server bietet, einschließlich [Azure Data Factory](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-sql-azure-adf) und [Azure Feature Pack für Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit SQL Server finden Sie unter:
- [Tutorial: Erste Schritte mit der Datenbank-Engine](https://docs.microsoft.com/sql/relational-databases/tutorial-getting-started-with-the-database-engine?view=sql-server-ver15&preserve-view=true)
