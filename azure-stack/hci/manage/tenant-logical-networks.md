---
title: Verwalten logischer Mandantennetzwerke
description: Dieses Thema enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Windows Admin Center zum Erstellen, Aktualisieren und Löschen von logischen Netzwerken, nachdem Sie einen Netzwerkcontroller bereitgestellt haben.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/02/2021
ms.openlocfilehash: e45ca61a09bd989645998a2e8993f29176d1b9cf
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99501068"
---
# <a name="manage-tenant-logical-networks"></a>Verwalten logischer Mandantennetzwerke

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

Dieses Thema enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Windows Admin Center zum Erstellen, Aktualisieren und Löschen von logischen Netzwerken, nachdem Sie einen Netzwerkcontroller bereitgestellt haben. Bei einem logischen SDN-Netzwerk (softwaredefiniertes Netzwerk) handelt es sich um ein herkömmliches VLAN-basiertes Netzwerk.

Indem Sie ein VLAN-basiertes Netzwerk als logisches SDN-Netzwerk modellieren, können Sie Netzwerkrichtlinien auf Workloads anwenden, die diesen Netzwerken zugeordnet sind. Beispielsweise können Sie zur Erhöhung der Sicherheit Zugriffssteuerungslisten (ACLs) auf Workloads anwenden, die logischen SDN-Netzwerken zugeordnet sind. Durch die Anwendung der ACLs sind Ihre VLAN-basierten Workloads sowohl vor externen als auch vor internen Angriffen geschützt.

## <a name="create-a-logical-network"></a>Ein logisches Netzwerk erstellen
Führen Sie die folgenden Schritte in Windows Admin Center aus, um ein logisches Netzwerk zu erstellen.

:::image type="content" source="./media/tenant-logical-networks/create-logical-network.png" alt-text="Screenshot: Windows Admin Center-Startseite mit Feld für den Namen des logischen Netzwerks" lightbox="./media/tenant-logical-networks/create-logical-network.png":::

1. Wählen Sie auf der Startseite von Windows Admin Center unter **Alle Verbindungen** den Cluster aus, in dem Sie das logische Netzwerk erstellen möchten.
1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Logische Netzwerke** aus.
1. Wählen Sie unter **Logische Netzwerke** die Registerkarte **Bestand** und dann **Neu** aus.
1. Geben Sie im Bereich **Logische Netzwerke** einen Namen für das logische Netzwerk ein.
1. Wählen Sie unter **Logisches Subnetz** die Option **Hinzufügen** aus.

    :::image type="content" source="./media/tenant-logical-networks/create-logical-network-subnet.png" alt-text="Screenshot: Windows Admin Center-Startseite mit Bereich für logisches Subnetz" lightbox="./media/tenant-logical-networks/create-logical-network-subnet.png":::

1. Geben Sie im Bereich **Logisches Subnetz** einen Namen für das Subnetz und dann die folgenden Informationen an:
    1. Eine **VLAN-ID** für das Netzwerk.
    1. Ein **Adresspräfix** in CIDR-Notation (Classless Interdomain Routing).
    1. Das **Standardgateway** für das Netzwerk.
    1. Die **DNS**-Serveradresse, falls erforderlich.
    1. Aktivieren Sie das Kontrollkästchen **Public Logical network** (Öffentliches logisches Netzwerk), falls über das logische Netzwerk die Konnektivität mit externen Clients möglich sein soll.
1. Wählen Sie unter **Logical subnet IP Pools** (IP-Pools des logischen Subnetzes) die Option **Hinzufügen** aus, und geben Sie dann die folgenden Informationen an:
    1. Einen **Namen** für den logischen IP-Pool.
    1. Eine **Start-IP-Adresse**.
    1. Eine **End-IP-Adresse**. Die Start- und die End-IP-Adresse müssen sich innerhalb des Adresspräfixes befinden, das für das Subnetz angegeben wurde.
    1. Wählen Sie **Hinzufügen**.
1. Wählen Sie auf der Seite **Logisches Subnetz** die Option **Hinzufügen** aus.
1. Wählen Sie auf der Seite **Logische Netzwerke** die Option **Senden** aus.
1. Vergewissern Sie sich in der Liste **Logische Netzwerke**, dass als Status für das logische Netzwerk **Fehlerfrei** angezeigt wird.

## <a name="get-a-list-of-logical-networks"></a>Abrufen einer Liste mit den logischen Netzwerken
Es ist einfach, alle logischen Netzwerke Ihres Clusters anzuzeigen.

:::image type="content" source="./media/tenant-logical-networks/list-logical-networks.png" alt-text="Screenshot: Windows Admin Center-Startseite mit Bereich „Bestand“ für logische Netzwerke" lightbox="./media/tenant-logical-networks/list-logical-networks.png":::

1. Wählen Sie auf der Startseite von Windows Admin Center unter **Alle Verbindungen** den Cluster aus, für den Sie die logischen Netzwerke anzeigen möchten.
1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Logische Netzwerke** aus.
1. Auf der Registerkarte **Bestand** sind alle verfügbaren logischen Netzwerke des Clusters aufgeführt. Darüber hinaus enthält sie Befehle zum Verwalten der einzelnen logischen Netzwerke. Ihre Möglichkeiten:
    - Anzeigen der Liste mit den logischen Netzwerken
    - Anzeigen der Einstellungen für logische Netzwerke und des jeweiligen Zustands und Überprüfen, ob die Netzwerkvirtualisierung für ein logisches Netzwerk aktiviert ist. Falls die Netzwerkvirtualisierung aktiviert ist, können Sie auch die Anzahl von virtuellen Netzwerken anzeigen, die den einzelnen logischen Netzwerken zugeordnet sind.
    - Ändern der Einstellungen eines logischen Netzwerks
    - Löschen eines logischen Netzwerks

## <a name="view-logical-network-details"></a>Anzeigen der Details eines logischen Netzwerks
Sie können ausführliche Informationen zu einem bestimmten logischen Netzwerk auf der zugehörigen Seite anzeigen.

:::image type="content" source="./media/tenant-logical-networks/view-logical-network-details.png" alt-text="Screenshot: Windows Admin Center mit Detailansicht für ein logisches Netzwerk" lightbox="./media/tenant-logical-networks/view-logical-network-details.png":::

1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Logische Netzwerke** aus.
1. Wählen Sie die Registerkarte **Bestand** und dann das logische Netzwerk aus, zu dem Sie Details anzeigen möchten. Auf der nächsten Seite können Sie Folgendes durchführen:
    - Anzeigen des Bereitstellungsstatus des logischen Netzwerks (Erfolg, Fehler)
    - Anzeigen, ob die Netzwerkvirtualisierung für das logische Netzwerk aktiviert ist
    - Anzeigen der Subnetze im logischen Netzwerk
    - Hinzufügen neuer Subnetze, Löschen vorhandener Subnetze und Ändern der Einstellungen des Subnetzes eines logischen Netzwerks
    - Auswählen der einzelnen Subnetze zum Zugreifen auf die Seite **Subnetz**, um IP-Pools für ein logisches Subnetz hinzuzufügen, zu entfernen und zu ändern
    - Anzeigen von virtuellen Netzwerken und Verbindungen, die dem logischen Netzwerk zugeordnet sind

## <a name="change-a-logical-networks-virtualization-setting"></a>Ändern der Virtualisierungseinstellung eines logischen Netzwerks
Sie können die Einstellung für die Netzwerkvirtualisierung für ein logisches Netzwerk ändern.

:::image type="content" source="./media/tenant-logical-networks/change-logical-network-setting.png" alt-text="Screenshot: Windows Admin Center mit Kontrollkästchen „Enable network virtualization“ (Netzwerkvirtualisierung aktivieren) für ein logisches Netzwerk" lightbox="./media/tenant-logical-networks/change-logical-network-setting.png":::

1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Logische Netzwerke** aus.
1. Wählen Sie die Registerkarte **Bestand**, das logische Netzwerk und dann die Option **Einstellungen** aus.
1. Wenn Sie planen, zusätzlich zum logischen Netzwerk virtuelle Netzwerke bereitzustellen, aktivieren Sie unter dem Namen des logischen Netzwerks das Kontrollkästchen **Enable network virtualization** (Netzwerkvirtualisierung aktivieren) und wählen dann **Senden** aus.

## <a name="delete-a-logical-network"></a>Löschen eines logischen Netzwerks
Sie können ein logisches Netzwerk löschen, falls Sie es nicht mehr benötigen.

:::image type="content" source="./media/tenant-logical-networks/delete-logical-network.png" alt-text="Screenshot: Windows Admin Center mit Bestätigungsaufforderung für das Löschen eines logischen Netzwerks" lightbox="./media/tenant-logical-networks/delete-logical-network.png":::

1. Scrollen Sie unter **Tools** zum Bereich **Netzwerk**, und wählen Sie die Option **Logische Netzwerke** aus.
1. Wählen Sie die Registerkarte **Bestand**, das logische Netzwerk und dann die Option **Löschen** aus.
1. Wählen Sie in der Bestätigungsaufforderung für das **Löschen** die Option **Ja** aus.
1. Wählen Sie neben dem Suchfeld **Logische Netzwerke** die Option **Aktualisieren** aus, um sich zu vergewissern, dass das logische Netzwerk gelöscht wurde.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auch unter:
- [Verwalten von virtuellen Mandantennetzwerken](tenant-virtual-networks.md)
- [Software-Defined Networking (SDN) in Azure Stack HCI](../concepts/software-defined-networking.md)
