---
title: Verwalten von Azure Stack HCI-Clustern mit Windows Admin Center
description: Hier erfahren Sie, wie Sie mithilfe von Windows Admin Center Ihre Cluster in Azure Stack HCI verwalten.
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 55ce577cbedde36d271ab6fc13234b009c825d7e
ms.sourcegitcommit: 7c10a45a8de0c5c7649e5329ca5b69a0791e37b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83729443"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Verwalten von Azure Stack HCI-Clustern mit Windows Admin Center

> Gilt für: Windows Server 2019

Windows Admin Center kann zum Verwalten von Clustern in Azure Stack HCI verwendet werden. Sie verwenden insbesondere die Cluster-Manager-Erweiterung in Windows Admin Center für die Verwaltung Ihrer Cluster.

## <a name="view-the-cluster-dashboard"></a>Anzeigen des Clusterdashboards

Auf dem Clusterdashboard werden Informationen zur Clusterintegrität und -leistung angezeigt.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="Bildschirm mit Clusterdashboard":::

Wählen Sie zum Anzeigen dieser Informationen unter **Alle Verbindungen** den Clusternamen und anschließend auf der linken Seite unter **Tools** die Option **Dashboard** aus. Sie können die folgenden Informationen anzeigen:

- Benachrichtigungen zu Clusterereignissen
- Liste der Server, die mit dem Cluster verknüpft sind
- Liste der virtuellen Computer, die im Cluster ausgeführt werden
- Liste der im Cluster verfügbaren Datenträgerlaufwerke
- Liste der im Cluster verfügbaren Volumes
- Gesamte CPU-Auslastung für den Cluster
- Gesamte Arbeitsspeicherauslastung für den Cluster
- Gesamte Speicherauslastung für den Cluster
- Gesamte E/A-Vorgänge pro Sekunde (IOPS) für den Cluster
- Durchschnittliche Clusterwartezeit in Millisekunden

## <a name="change-cluster-general-settings"></a>Ändern der allgemeinen Clustereinstellungen

Es gibt fünf allgemeine Einstellungen, die auf Ihren Cluster angewendet werden können.

1. Klicken Sie in Windows Admin Center beim oberen Dropdownpfeil auf **Cluster-Manager**.
1. Klicken Sie unter **Tools** auf **Einstellungen**.
1. Wählen Sie zum Ändern des Clusternamens **Zugriffspunkt** aus, und geben Sie den neuen Namen ein.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Bildschirm: Clusterzugriffspunkt":::

1. Um das Verhalten beim Herunterfahren des Knotens zu steuern, wählen Sie **Node shutdown behavior** (Verhalten beim Herunterfahren des Knotens) aus, und vergewissern Sie sich, dass das Kontrollkästchen aktiviert ist. Dadurch werden zuerst alle virtuellen Computer von dem Knoten verschoben, um das ordnungsgemäße Herunterfahren des Knotens zu ermöglichen.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="Bildschirm: Verhalten beim Herunterfahren des Clusterknotens":::

1. Wenn Sie SMB-Verbindungen verschlüsseln möchten, die zum Senden von Daten zwischen Clusterknoten verwendet werden, wählen Sie **Cluster traffic encryption** (Verschlüsselung des Clusterdatenverkehrs) und anschließend **Verschlüsseln** in den Dropdownfeldern für die folgenden Optionen aus:

   - **Core traffic** (Kerndatenverkehr): Verschlüsselt Datenverkehr, der über NetFT (virtueller Clusteradapter) an Port 3343 gesendet wird.

   - **Server traffic** (Serverdatenverkehr): Verschlüsselt CSV-Datenverkehr (Cluster Shared Volume, freigegebenes Clustervolume) und SBL-Datenverkehr (Storage Bus Layer).

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="Bildschirm: Verschlüsselung von Clusterdatenverkehr":::

1. Wenn Sie automatisch einen clusterübergreifenden Lastenausgleich für virtuelle Computer ausführen möchten, wählen Sie **Virtual machine load balancing** (Lastenausgleich für virtuelle Computer) aus, und führen Sie die folgenden Schritte aus:

   - Wählen Sie für **Balance virtual machines** (Virtuelle Computer ausgleichen) die entsprechende Aktion aus.
   - Wählen Sie für **Aggressiveness** (Aggressivität) das entsprechende Verhalten aus.

     Informationen dazu finden Sie unter [Übersicht über den Lastenausgleich virtueller Computer](https://docs.microsoft.com/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="Bildschirm: Lastenausgleich für virtuelle Computer im Cluster":::

1. Wählen Sie zum Festlegen eines Quorumzeugentyps **Zeuge** und dann eine der folgenden Optionen aus:

   - **Cloudzeuge**: Verwenden einer Azure-Cloudressource als Zeuge
   - **Datenträgerzeuge**: Verwenden einer Datenträgerressource als Zeuge
   - **Dateifreigabenzeuge**: Verwenden einer Dateifreigabe als Zeuge

        Weitere Informationen finden Sie unter [Konfigurieren und Verwalten des Quorums](https://docs.microsoft.com/windows-server/failover-clustering/manage-cluster-quorum).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Bildschirm: Clusterzeuge":::

## <a name="change-cluster-hyper-v-settings"></a>Ändern der Hyper-V-Clustereinstellungen

Es gibt fünf Hyper-V-Hosteinstellungen, die auf Ihren Cluster angewendet werden können.

1. Klicken Sie in Windows Admin Center beim oberen Dropdownpfeil auf **Cluster-Manager**.
1. Klicken Sie unter **Tools** auf **Einstellungen**.
1. Wählen Sie **Allgemein** aus, und verwenden Sie dann die folgenden Einstellungen:

   - **Virtual Hard Disks Path** (Pfad der virtuellen Festplatten): Geben Sie den Standardordner zum Speichern von VHD-Dateien an.

   - **Virtual Machines Path** (Pfad der virtuellen Computer): Geben Sie den Standardordner zum Speichern der Konfigurationsdateien virtueller Computer an.

   - **Hypervisor Scheduler Type** (Typ des Hypervisorplaners): Wählen Sie **Core Scheduler** (Kernplaner) oder **Classic Scheduler** (Klassischer Planer) aus. Dadurch wird festgelegt, wie der Hypervisor die Ausführung virtueller Prozesse auf physischen Prozessoren plant, die simultanes Multithreading (auch als SMT oder Hyperthreading bezeichnet) verwenden.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="Bildschirm: allgemeine Hyper-V-Clustereinstellungen":::

1. Um die Umleitung von lokalen Geräten und Ressourcen auf virtuellen Computern zuzulassen, wählen Sie **Erweiterter Sitzungsmodus** aus. Beachten Sie, dass für Verbindungen des erweiterten Sitzungsmodus ein unterstütztes Gastbetriebssystem erforderlich ist.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="Bildschirm: erweiterter Hyper-V-Sitzungsmodus des Clusters":::

1. Wählen Sie **Aufteilung auf NUMA** aus, um zuzulassen, dass sich virtuelle Computer auf physische NUMA-Knoten erstrecken. Bei der Aufteilung auf NUMA (Non-Uniform Memory Architecture) kann für virtuelle Computer mehr Arbeitsspeicher bereitgestellt werden, als in einem einzelnen NUMA-Knoten verfügbar ist.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="Bildschirm: Aufteilung auf NUMA für den Cluster":::

1. Wenn Sie die Anzahl von virtuellen Computern angeben möchten, die während der Ausführung gleichzeitig verschoben werden können (Livemigration), wählen Sie **Livemigration** und eine Anzahl aus, und geben Sie Folgendes an:

   - Wählen Sie unter **Authentifizierungsprotokoll** entweder **CredSSP** oder **Kerberos** aus.

   - Wählen Sie unter **Performance Option** (Leistungsoption) entweder **Komprimierung** oder **SMB** aus. Komprimierte Daten werden über eine TCP/IP-Verbindung gesendet.

   - Aktivieren Sie das Kontrollkästchen **Use any network** (Beliebiges Netzwerk verwenden), um ein beliebiges verfügbares Netzwerk in einem Knoten zum Durchführen der Migration zu verwenden.

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="Clusterbildschirm: Livemigration":::

1. Wählen Sie zum Angeben der Anzahl von Speichermigrationen, die gleichzeitig ausgeführt werden können, die Option **Speichermigration** und dann eine Zahl aus.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="Clusterbildschirm: Speichermigration":::

## <a name="change-cluster-storage-settings"></a>Ändern der Clusterspeichereinstellungen

Es gibt zwei Einstellungen im Zusammenhang mit „Direkte Speicherplätze“, die Sie ändern und die auf Ihren Cluster angewendet werden können.

1. Klicken Sie in Windows Admin Center beim oberen Dropdownpfeil auf **Cluster-Manager**.
1. Klicken Sie unter **Tools** am unteren Rand auf **Einstellungen**.
1. Wählen Sie zum Konfigurieren des Speichercache die Option **Direkte Speicherplätze** aus, und konfigurieren Sie dann die folgenden Einstellungen:

   - Wählen Sie unter **Persistent cache** (Persistenter Cache) entweder **Aktiviert** oder **Deaktiviert** aus.

   - Wählen Sie unter **Cache mode for HDD** (Cachemodus für HDD) die Option **Nur lesen**, **Nur schreiben** oder **Lesen/Schreiben** aus.

   - Wählen Sie unter **Cache mode for SSD** (Cachemodus für SSD) die Option **Nur lesen**, **Nur schreiben** oder **Lesen/Schreiben** aus.

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="Clusterbildschirm: Direkte Speicherplätze":::

1. Um den Serverarbeitsspeicher zum Zwischenspeichern häufiger Lesevorgänge zu verwenden, wählen Sie **In-memory cache** (In-Memory-Cache) aus, und geben Sie an, wie viel Arbeitsspeicher pro Server maximal verwendet werden soll. Weitere Informationen finden Sie auch unter [Verwenden von „Direkte Speicherplätze“ mit dem CSV-In-Memory-Lesecache](https://docs.microsoft.com/windows-server/storage/storage-spaces/csv-cache).

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="Clusterbildschirm: In-Memory-Cache":::

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Überwachen der Clusterleistung finden Sie unter [Überwachen der Clusterleistung mit dem Windows Admin Center-Dashboard](https://docs.microsoft.com/azure-stack/hci/get-started#monitor-cluster-performance-with-the-windows-admin-center-dashboard).