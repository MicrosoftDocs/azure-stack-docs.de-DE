---
title: Planen der Bereitstellung des Netzwerkcontrollers
description: In diesem Thema geht es um die Planung der Bereitstellung des Netzwerkcontrollers über Windows Admin Center in einer Gruppe von virtuellen Computern (VMs) mit dem Betriebssystem Azure Stack HCI.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 58ae46591e2119bbfc9269440f36e9c37fd0e7b9
ms.sourcegitcommit: 69c859a89941ee554d438d5472308eece6766bdf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89643937"
---
# <a name="plan-to-deploy-the-network-controller"></a>Planen der Bereitstellung des Netzwerkcontrollers

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019 

Die Planung der Bereitstellung des Netzwerkcontrollers über Windows Admin Center erfordert eine Gruppe virtueller Computer (VMs) mit dem Betriebssystem Azure Stack HCI. Der Netzwerkcontroller ist eine hochverfügbare und skalierbare Serverrolle, die mindestens drei VMs erfordert, um in Ihrem Netzwerk Hochverfügbarkeit zu gewährleisten.

   >[!NOTE]
   > Es wird empfohlen, den Netzwerkcontroller auf eigenen dedizierten VMs bereitzustellen.

## <a name="network-controller-requirements-for-azure-stack-hci"></a>Anforderungen an den Netzwerkcontroller für Azure Stack HCI
Zum Bereitstellen des Netzwerkcontrollers ist Folgendes erforderlich:
- Eine VHD für das Betriebssystem Azure Stack HCI zum Erstellen der Netzwerkcontroller-VMs
- Ein Domänenname und Anmeldeinformationen für den Beitritt der Netzwerkcontroller-VMs zu einer Domäne
- Eine Konfiguration des physischen Netzwerks, die einer der beiden Topologieoptionen in diesem Abschnitt entspricht

    Windows Admin Center erstellt die Konfiguration in den Hyper-V-Hosts. Das Verwaltungsnetzwerk muss sich jedoch gemäß einer der beiden folgenden Optionen mit den physischen Adaptern des Hosts verbinden:

    **Option 1**: Ein einzelner physischer Switch verbindet das Verwaltungsnetzwerk mit einem physischen Verwaltungsadapter auf dem Host und einem Trunk für die physischen Adapter, die der virtuelle Switch verwendet:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Option 1 zum Erstellen eines physischen Netzwerks für den Netzwerkcontroller." lightbox="./media/network-controller/topology-option-1.png":::

    **Option 2**: Wenn das Verwaltungsnetzwerk physisch von den Netzwerken der Workloads getrennt ist, sind zwei virtuelle Switches erforderlich:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Option 2 zum Erstellen eines physischen Netzwerks für den Netzwerkcontroller." lightbox="./media/network-controller/topology-option-1.png":::

- Verwaltungsnetzwerk-Informationen, die der Netzwerkcontroller zur Kommunikation mit Windows Admin Center und den Hyper-V-Hosts verwendet.
- Entweder DHCP-basierte oder statische netzwerkbasierte Adressierung der Netzwerkcontroller-VMs.
- Der REST FQDN (Representational State Transfer, vollqualifizierter Domänenname) für den Netzwerkcontroller, der von den Verwaltungsclients für die Kommunikation mit dem Netzwerkcontroller verwendet wird.

   >[!NOTE]
   > Windows Admin Center unterstützt derzeit keine Netzwerkcontroller-Authentifizierung, weder für die Kommunikation mit REST-Clients noch für die Kommunikation zwischen den Netzwerkcontroller-VMs. Sie können die Kerberos-basierte Authentifizierung verwenden, die Sie mit PowerShell bereitstellen und verwalten können.

## <a name="configuration-requirements"></a>Konfigurationsanforderungen
Sie können die Clusterknoten des Netzwerkcontrollers entweder im selben Subnetz oder in verschiedenen Subnetzen bereitstellen. Wenn Sie vorhaben, die Clusterknoten des Netzwerkcontrollers in verschiedenen Subnetzen bereitzustellen, müssen Sie während des Bereitstellungsprozesses den REST-DNS-Namen des Netzwerkcontrollers angeben.

Weitere Informationen finden Sie unter [Konfigurieren der dynamischen DNS-Registrierung für den Netzwerkcontroller](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).


## <a name="next-steps"></a>Nächste Schritte
Jetzt sind Sie bereit, den Netzwerkcontroller auf VMs mit dem Betriebssystem Azure Stack HCI bereitzustellen.

Weitere Informationen finden Sie unter:
- [Erstellen eines Azure Stack HCI-Clusters](../deploy/create-cluster.md)

## <a name="see-also"></a>Weitere Informationen
- [Netzwerkcontroller](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Hochverfügbarkeit des Netzwerkcontrollers](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)