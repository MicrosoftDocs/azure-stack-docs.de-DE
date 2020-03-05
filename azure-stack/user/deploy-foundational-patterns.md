---
title: Bereitstellen grundlegender Muster in Azure Stack Hub
description: Es wird beschrieben, wie Sie mit Azure Stack Hub grundlegende Muster bereitstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 28775cb3c279e2976a32a63fc21a8797bba6eb38
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705082"
---
# <a name="deploy-foundational-patterns-overview"></a>Bereitstellen grundlegender Muster: Übersicht


Jedes dieser Muster enthält eine Anleitung, Azure Resource Manager-Vorlagen und Tutorials. Sie können diese Muster zusammen mit Drittanbieter-Apps verwenden, um Angebote zu erstellen, die noch nicht von Azure Stack unterstützt werden. Operator müssen beispielsweise häufig die komplexen Aufgaben lösen, die mit dem Einrichten eines virtuellen privaten Netzwerks (VPN) mit einer einzelnen Azure Stack Hub-Instanz verbunden sind – ganz zu schweigen von der Erstellung eines VPN für zwei oder mehr Umgebungen. Für Operator kann es zu Problemen kommen, wenn sie versuchen, ein Lastenausgleichsmodul vor einer Azure Stack Hub-Instanz zu erstellen, um Workloads zu verwalten. Mit der folgenden Anleitung können Sie die Bereitstellungsdauer verkürzen, die für die Freigabe Ihrer produktionsbereiten Workloads benötigt wird.

## <a name="networking"></a>Netzwerk

Verwenden Sie die Netzwerkmuster, um nach einer Anleitung zur Erstellung eines VNET-Peerings mit Azure Stack Hub zu suchen. Beim VNET-Peering können Sie zwei virtuelle Netzwerke verbinden, damit diese als ein gemeinsames Netzwerk angezeigt werden. Die Standort-zu-Standort-Konnektivität zwischen virtuellen Netzwerken wird per RRAS (Routing and Remote Access Services) erzielt. Mit RRAS können virtuelle Windows-Computer (VMs) als Router genutzt werden. Mit diesen Skripts können Sie zwei virtuelle Netzwerke in den Ressourcengruppen in einer Azure Stack Hub-Ressourcengruppe, abonnementübergreifend und auf zwei Azure Stack Hub-Instanzen bereitstellen. Sie können die Skripts in Azure Stack Hub und in einer globalen Azure-Umgebung bereitstellen. 

In jedem Artikel werden die folgenden allgemeinen Aspekte behandelt: 
- Skalieren
- Bandbreite
- Sicherheit
- Geschäftskontinuität

|  Peering in virtuellen Netzwerken  |  VPN  |  Load Balancer  |
| --- | --- | --- |
| ![VNET-Peering mit VMs](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[VNET-Peering mit VMs](azure-stack-network-howto-vnet-peering.md) | ![Einrichten einer VPN-Verbindung mit einer lokalen Ressource](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Einrichten einer VPN-Verbindung mit einer lokalen Ressource](azure-stack-network-howto-vnet-to-onprem.md) | ![F5-Lastenausgleich](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[F5-Lastenausgleich](network-howto-f5.md) |
| ![VNET-Peering mit FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[VNET-Peering mit FortiGate](azure-stack-network-howto-vnet-to-vnet.md) | ![Virtuelles privates Netzwerk](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[VNET-zu-VNET-Verbindung](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![Erstellen eines VPN-Tunnels (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Erstellen eines VPN-Tunnels (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![Einrichten eines VPN mit mehreren Site-to-Site-Verbindungen](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Einrichten eines VPN mit mehreren Site-to-Site-Verbindungen](network-howto-vpn-tunnel.md) | |
|  | ![Erstellen eines VPN-Tunnels (IPSEC)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Erstellen eines VPN-Tunnels (IPSEC)](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>Storage

Verwenden Sie die Speichermuster, um Ihre Speicheroptionen mit Azure Stack Hub zu erweitern. In Azure Stack Hub ist der Speicher endlich. Stellen Sie eine Verbindung mit den Ressourcen in Ihrem vorhandenen Rechenzentrum her. Suchen Sie nach der Anleitung zum Erstellen einer Windows-VM in Azure Stack Hub, um eine Verbindung mit einem externen iSCSI-Ziel herzustellen. Sie können sich darüber informieren, wie Sie Schlüsselfunktionen wie Multipfad-E/A (MPIO) aktivieren, um Leistung und Konnektivität zwischen der VM und dem externen Speicher zu optimieren.

| iSCSI-Speicher | Erweitern des Speichers |
| --- | --- | --- |
| ![Verbinden mit iSCSI-Speicher](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[Verbinden mit iSCSI-Speicher](azure-stack-network-howto-iscsi-storage.md) | ![Erweitern des Rechenzentrums](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[Erweitern des Rechenzentrums](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>Backup

Sie können die Muster für Sicherung und Notfallwiederherstellung verwenden, um alle Ressourcen eines Abonnements in Azure oder einer anderen Azure Stack Hub-Instanz zu kopieren. Bei diesen Mustern wird die Commvault-Livesynchronisierung genutzt, um die auf den VMs gespeicherten Informationen in einer anderen Umgebung zu replizieren. Es sind Skripts verfügbar, mit denen Sie ein Speicherkonto und ein Sicherungsspeicherkonto zum Senden der Daten erstellen können. Mit dem Azure-Abonnementreplikator-Modul können Sie die Ressourcenreplikation orchestrieren und den Prozessor anpassen, um verschiedene Ressourcen zu verarbeiten. 



|  Sichern  |  Kopieren  |
| --- | --- | --- |
| ![Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault](azure-stack-network-howto-backup-commvault.md) | ![Kopieren der Abonnementressourcen](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[Kopieren der Abonnementressourcen](azure-stack-network-howto-backup-replicator.md) |
|  | ![Sichern Ihrer Speicherkonten in Azure Stack Hub](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[Sichern Ihrer Speicherkonten in Azure Stack Hub](azure-stack-network-howto-backup-storage.md)  |

## <a name="github-samples"></a>GitHub-Beispiele

Die Vorlagen finden Sie im GitHub-Repository [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).

## <a name="next-steps"></a>Nächste Schritte

[Dokumentation zu Hybridmustern und -lösungen von Azure](https://docs.microsoft.com/azure-stack/hybrid/)
