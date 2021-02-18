---
title: Verwalten von virtuellen Mandantennetzwerken
description: Dieses Thema enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Windows Admin Center zum Erstellen, Aktualisieren und Löschen von virtuellen HNV-Netzwerken (Hyper-V-Netzwerkvirtualisierung), nachdem Sie ein softwaredefiniertes Netzwerk (SDN) bereitgestellt haben.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: fb90a8f9df28ed8fd67330c42ae1e3d2f3aa445c
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562927"
---
# <a name="manage-tenant-virtual-networks"></a>Verwalten von virtuellen Mandantennetzwerken

>Gilt für: Azure Stack HCI (Version 20H2), Windows Server 2019, Windows Server 2016

Dieses Thema enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Windows Admin Center zum Erstellen, Aktualisieren und Löschen von virtuellen HNV-Netzwerken (Hyper-V-Netzwerkvirtualisierung), nachdem Sie ein softwaredefiniertes Netzwerk (SDN) bereitgestellt haben.

Per Hyper-V-Netzwerkvirtualisierung können Sie Mandantennetzwerke isolieren, damit es sich bei jedem Mandantennetzwerk um eine separate Entität handelt. Für die einzelnen Entitäten sind keine Querverbindungen möglich, sofern Sie nicht entweder Workloads mit öffentlichem Zugriff oder das Peering zwischen virtuellen Netzwerken konfigurieren.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Führen Sie die folgenden Schritte in Windows Admin Center aus, um ein virtuelles Netzwerk zu erstellen.

:::image type="content" source="./media/tenant-virtual-networks/create-virtual-network.png" alt-text="Screenshot: Windows Admin Center-Startseite mit Bereich zum Erstellen des VNET-Namens und des Adresspräfixes" lightbox="./media/tenant-virtual-networks/create-virtual-network.png":::

1. Wählen Sie auf der Startseite von Windows Admin Center unter **Alle Verbindungen** den Cluster aus, in dem Sie das virtuelle Netzwerk erstellen möchten.
1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Virtuelle Netzwerke** aus.
1. Wählen Sie unter **Virtuelle Netzwerke** die Registerkarte **Bestand** und dann **Neu** aus.
1. Geben Sie im Bereich **Virtuelle Netzwerke** einen Namen für das virtuelle Netzwerk ein.
1. Wählen Sie unter **Adresspräfixe** die Option **Hinzufügen** aus, und geben Sie dann ein Adresspräfix in CIDR-Notation (Classless Interdomain Routing) ein. Optional können Sie weitere Adresspräfixe hinzufügen.
1. Wählen Sie unter **Subnetze** die Option **Hinzufügen** aus, geben Sie einen Namen für das Subnetz ein, und geben Sie dann ein Adresspräfix in CIDR-Notation an.

   >[!NOTE]
   > Das Adresspräfix des Subnetzes muss sich innerhalb des Adresspräfixbereichs befinden, den Sie unter **Adresspräfixe** für das virtuelle Netzwerk definiert haben.

1. Wählen Sie **Senden** aus, oder fügen Sie optional weitere Subnetze hinzu, bevor Sie **Senden** auswählen.
1. Vergewissern Sie sich in der Liste **Virtuelle Netzwerke**, dass als Status für das virtuelle Netzwerk **Fehlerfrei** angezeigt wird.

## <a name="get-a-list-of-virtual-networks"></a>Abrufen einer Liste mit den virtuellen Netzwerken
Es ist einfach, alle virtuellen Netzwerke Ihres Clusters anzuzeigen.

:::image type="content" source="./media/tenant-virtual-networks/list-virtual-networks.png" alt-text="Screenshot: Windows Admin Center mit Liste virtueller Netzwerke" lightbox="./media/tenant-virtual-networks/list-virtual-networks.png":::

1. Wählen Sie auf der Startseite von Windows Admin Center unter **Alle Verbindungen** den Cluster aus, für den Sie die virtuellen Netzwerke anzeigen möchten.
1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Virtuelle Netzwerke** aus.
1. Auf der Registerkarte **Bestand** sind alle verfügbaren virtuellen Netzwerke des Clusters aufgeführt. Darüber hinaus enthält sie Befehle zum Verwalten der einzelnen virtuellen Netzwerke. Ihre Möglichkeiten:
    - Anzeigen einer Liste mit den virtuellen Netzwerken
    - Anzeigen der Einstellungen für virtuelle Netzwerke, des Status der einzelnen virtuellen Netzwerke und der Anzahl von virtuellen Computern (VMs), die mit den einzelnen virtuellen Netzwerken verbunden sind
    - Ändern der Einstellungen eines virtuellen Netzwerks
    - Löschen eines virtuellen Netzwerks.

## <a name="view-virtual-network-details"></a>Anzeigen der Details für die virtuellen Netzwerke
Sie können ausführliche Informationen zu einem bestimmten virtuellen Netzwerk auf der zugehörigen Seite anzeigen.

:::image type="content" source="./media/tenant-virtual-networks/view-virtual-network-details.png" alt-text="Screenshot: Windows Admin Center mit Detailansicht für ein virtuelles Netzwerk" lightbox="./media/tenant-virtual-networks/view-virtual-network-details.png":::

1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Virtuelle Netzwerke** aus.
1. Wählen Sie die Registerkarte **Bestand** und dann das virtuelle Netzwerk aus, zu dem Sie Details anzeigen möchten. Auf der nächsten Seite können Sie Folgendes durchführen:
    - Anzeigen des **Bereitstellungsstatus** des virtuellen Netzwerks (Erfolg, Fehler)
    - Anzeigen des **Konfigurationsstatus** des virtuellen Netzwerks (Fehlerfrei, Fehler, Warnung, Unbekannt)
    - Anzeigen des zugrunde liegenden **logischen Netzwerks** für das virtuelle Netzwerk
    - Anzeigen des **Adressraums** des virtuellen Netzwerks
    - Hinzufügen neuer Subnetze, Löschen vorhandener Subnetze und Ändern der Einstellungen des Subnetzes eines virtuellen Netzwerks
    - Anzeigen der **Verbindungen virtueller Computer** für das virtuelle Netzwerk

## <a name="change-virtual-network-settings"></a>Ändern der Einstellungen eines virtuellen Netzwerks
Sie können Adresspräfixe von virtuellen Netzwerken aktualisieren, VNET-Peering verwalten und einen BGP-Router (Border Gateway Protocol) und Peers für das virtuelle Netzwerk konfigurieren.

:::image type="content" source="./media/tenant-virtual-networks/change-virtual-network-settings.png" alt-text="Screenshot: Windows Admin Center mit Ansicht „Einstellungen“ für ein virtuelles Netzwerk" lightbox="./media/tenant-virtual-networks/change-virtual-network-settings.png":::

1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Virtuelle Netzwerke** aus.
1. Wählen Sie die Registerkarte **Bestand**, ein virtuelles Netzwerk und dann die Option **Einstellungen** aus.
1. Auf der Registerkarte **Allgemein** können Sie Folgendes durchführen:
    - Entfernen von vorhandenen Adresspräfixen oder Hinzufügen von neuen
    - Konfigurieren des Peerings mit einem anderen virtuellen Netzwerk
    - Hinzufügen eines BGP-Routers zum virtuellen Netzwerk. Hierfür müssen Sie den Namen des BGP-Routers und die ASN (Autonomous System Number) angeben.
    - Hinzufügen von BGP-Peers für den BGP-Router. Hierfür müssen Sie die Namen der einzelnen BGP-Peers und jeweils die zugehörige ASN angeben.

## <a name="delete-a-virtual-network"></a>Löschen eines virtuellen Netzwerks
Sie können ein virtuelles Netzwerk löschen, falls Sie es nicht mehr benötigen.

:::image type="content" source="./media/tenant-virtual-networks/delete-virtual-network.png" alt-text="Screenshot: Windows Admin Center mit Bestätigungsaufforderung für das Löschen des virtuellen Netzwerks" lightbox="./media/tenant-virtual-networks/delete-virtual-network.png":::

1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Virtuelle Netzwerke** aus.
1. Wählen Sie die Registerkarte **Bestand**, ein virtuelles Netzwerk und dann die Option **Löschen** aus.
1. Wählen Sie in der Bestätigungsaufforderung für **Virtuelles Netzwerk löschen** die Option **Löschen** aus.
1. Wählen Sie neben dem Suchfeld „Virtuelle Netzwerke“ die Option **Aktualisieren** aus, um sich zu vergewissern, dass das virtuelle Netzwerk gelöscht wurde.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auch unter:
- [Software-Defined Networking (SDN) in Azure Stack HCI](../concepts/software-defined-networking.md)
