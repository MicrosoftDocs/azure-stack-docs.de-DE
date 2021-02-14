---
title: Planen der Bereitstellung von Netzwerkcontroller
description: In diesem Thema wird beschrieben, wie Sie die Bereitstellung eines Netzwerkcontrollers mit Windows Admin Center in einer Gruppe mit virtuellen Computern (VMs) planen.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: bfea9216cefdc64c7749f8b49d5ecc3a422e5130
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510787"
---
# <a name="plan-to-deploy-network-controller"></a>Planen der Bereitstellung von Netzwerkcontroller

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

Für die Planung der Bereitstellung eines Netzwerkcontrollers über Windows Admin Center benötigen Sie eine Gruppe mit virtuellen Computern (VMs), auf denen das Betriebssystem Azure Stack HCI oder Windows Server ausgeführt wird. Netzwerkcontroller ist eine hochverfügbare und skalierbare Serverrolle, die mindestens drei VMs erfordert, um in Ihrem Netzwerk Hochverfügbarkeit zu gewährleisten.

   >[!NOTE]
   > Sie sollten Netzwerkcontroller auf eigenen dedizierten VMs bereitzustellen.

## <a name="network-controller-requirements"></a>Anforderungen an Netzwerkcontroller
Zum Bereitstellen von Netzwerkcontroller ist Folgendes erforderlich:
- Eine virtuelle Festplatte (Virtual Hard Disk, VHD) für das Betriebssystem Azure Stack HCI zum Erstellen der Netzwerkcontroller-VMs.
- Ein Domänenname und Anmeldeinformationen für das Einbinden von Netzwerkcontroller-VMs in eine Domäne.
- Mindestens ein virtueller Switch, den Sie mithilfe des Clustererstellungs-Assistenten im Windows Admin Center konfigurieren.
- Eine Konfiguration des physischen Netzwerks, die einer der Topologieoptionen in diesem Abschnitt entspricht.

    Windows Admin Center erstellt die Konfiguration in den Hyper-V-Hosts. Das Verwaltungsnetzwerk muss jedoch gemäß einer der drei folgenden Optionen eine Verbindung mit den physischen Adaptern des Hosts herstellen:

    **Option 1**: Das Verwaltungsnetzwerk ist physisch von den Netzwerken der Workloads getrennt. Bei dieser Option wird ein einzelner virtueller Switch sowohl für Compute als auch für Speicher verwendet:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Option 1 zum Erstellen eines physischen Netzwerks für den Netzwerkcontroller." lightbox="./media/network-controller/topology-option-1.png":::

    **Option 2**: Das Verwaltungsnetzwerk ist physisch von den Netzwerken der Workloads getrennt. Bei dieser Option wird ein einzelner virtueller Switch nur für Compute verwendet:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Option 2 zum Erstellen eines physischen Netzwerks für den Netzwerkcontroller." lightbox="./media/network-controller/topology-option-2.png":::

    **Option 3**: Das Verwaltungsnetzwerk ist physisch von den Netzwerken der Workloads getrennt. Bei dieser Option werden zwei virtuelle Switches verwendet, einer für Compute, der andere für Speicher:

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="Option 3 zum Erstellen eines physischen Netzwerks für Netzwerkcontroller." lightbox="./media/network-controller/topology-option-3.png":::

- Sie können die physischen Verwaltungsadapter auch für die Verwendung desselben Verwaltungsswitches kombinieren. In diesem Fall sollten Sie dennoch eine der Optionen in diesem Abschnitt verwenden.
- Verwaltungsnetzwerk-Informationen, die Netzwerkcontroller zur Kommunikation mit Windows Admin Center und den Hyper-V-Hosts verwendet.
- Entweder DHCP-basierte oder statische netzwerkbasierte Adressierung der Netzwerkcontroller-VMs.
- Der REST FQDN (Representational State Transfer, vollqualifizierter Domänenname) für Netzwerkcontroller, der von den Verwaltungsclients für die Kommunikation mit dem Netzwerkcontroller verwendet wird.

   >[!NOTE]
   > Windows Admin Center unterstützt derzeit keine Netzwerkcontroller-Authentifizierung, weder für die Kommunikation mit REST-Clients noch für die Kommunikation zwischen Netzwerkcontroller-VMs. Sie können die Kerberos-basierte Authentifizierung verwenden, die Sie mit PowerShell bereitstellen und verwalten können.

## <a name="configuration-requirements"></a>Konfigurationsanforderungen
Sie können die Netzwerkcontroller-Clusterknoten entweder im selben Subnetz oder in verschiedenen Subnetzen bereitstellen. Wenn Sie vorhaben, die Netzwerkcontroller-Clusterknoten in verschiedenen Subnetzen bereitzustellen, müssen Sie während des Bereitstellungsprozesses den REST-DNS-Namen des Netzwerkcontrollers angeben.

Weitere Informationen finden Sie unter [Konfigurieren der dynamischen DNS-Registrierung für den Netzwerkcontroller](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Nächste Schritte
Sie können den Netzwerkcontroller jetzt auf den VMs bereitstellen.

## <a name="see-also"></a>Weitere Informationen
- [Erstellen eines Azure Stack HCI-Clusters](../deploy/create-cluster.md)
- [Bereitstellen einer SDN-Infrastruktur mit SDN Express](../manage/sdn-express.md)
- [Netzwerkcontroller – Übersicht](network-controller-overview.md)
- [Hochverfügbarkeit des Netzwerkcontrollers](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)