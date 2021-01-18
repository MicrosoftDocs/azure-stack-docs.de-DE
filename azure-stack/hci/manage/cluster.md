---
title: Verwalten von Azure Stack HCI-Clustern – Windows Admin Center
description: Hier erfahren Sie, wie Sie mithilfe von Windows Admin Center Ihre Cluster in Azure Stack HCI verwalten.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/12/2021
ms.openlocfilehash: 7f77855945ecfb31e223db46be8b2e2e3a012c16
ms.sourcegitcommit: 502df315764bbc4ff6d3de50b957dfd4a6c0043a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98130287"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Verwalten von Azure Stack HCI-Clustern mit Windows Admin Center

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Windows Admin Center kann für die Verwaltung Ihrer Azure Stack HCI-Cluster verwendet werden. Sie verwenden insbesondere die Cluster-Manager-Funktion in Windows Admin Center für die Verwaltung Ihrer Cluster.

## <a name="view-the-cluster-dashboard"></a>Anzeigen des Clusterdashboards

Auf dem Clusterdashboard werden Informationen zur Clusterintegrität und -leistung angezeigt.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="Bildschirm mit Clusterdashboard" lightbox="media/manage-cluster/cluster-dashboard.png":::

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

## <a name="change-storage-settings"></a>Ändern von Speichereinstellungen

Sie können sich entscheiden, den Serverarbeitsspeicher zum Zwischenspeichern häufiger Lesevorgänge zu verwenden, und angeben, wie viel Arbeitsspeicher pro Server maximal verwendet werden soll. Weitere Informationen finden Sie unter [Grundlegendes zum Cache in Azure Stack HCI](../concepts/cache.md).

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Tools** am unteren Rand **Einstellungen** aus.
1. Wählen Sie **in-Memory-Cache** aus, und geben Sie den neuen Namen ein.

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="Clusterbildschirm: In-Memory-Cache" lightbox="media/manage-cluster/cluster-settings-memory.png":::

1. Sie können den Namen des Speicherpools ändern, der von Direkte Speicherplätze verwendet wird. Wählen Sie **Speicherpools** aus, und geben Sie den neuen Namen ein. Dies gilt für Stretched Clusters.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-pools.png" alt-text="Speicherpool-Bildschirm eines Clusters" lightbox="media/manage-cluster/cluster-settings-storage-pools.png":::

1. Sie können die Einstellungen für Direkte Speicherplätze ändern. Wählen Sie **Direkte Speicherplätze** aus, und ändern Sie die folgenden Einstellungen nach Bedarf:

    - **Persistent Cache** (Persistenter Cache): Aktivieren oder Deaktivieren des persistenten Caches
    - **Cache mode for HDD** (Cachemodus für HDD): Ändern des Cachemodus für Festplattenlaufwerke
    - **Cache mode for SSD** (Cachemodus für SSD): Ändern des Caches für SSD-Laufwerke

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-spaces-direct.png" alt-text="Clusterbildschirm: Direkte Speicherplätze" lightbox="media/manage-cluster/cluster-settings-storage-spaces-direct.png":::

## <a name="change-cluster-settings"></a>Ändern der Clustereinstellungen

Es gibt verschiedene allgemeine Einstellungen, die auf Ihren Cluster angewendet werden können. Hier können Sie Zugriffspunkte, das Verhalten beim Herunterfahren von Knoten, die Verschlüsselung von Datenverkehr, den VM-Lastenausgleich und den Clusterzeugen festlegen und verwalten.

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Extras** die Option **Einstellungen** aus.
1. Wählen Sie zum Ändern des Clusternamens **Zugriffspunkt** aus, und geben Sie den neuen Namen ein.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Szenario mit einem aktiv-aktiven Stretchingcluster" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. Um das Verhalten beim Herunterfahren des Knotens zu steuern, wählen Sie **Node shutdown behavior** (Verhalten beim Herunterfahren des Knotens) aus, und vergewissern Sie sich, dass das Kontrollkästchen aktiviert ist. Dadurch werden zuerst alle virtuellen Computer von dem Knoten verschoben, um das ordnungsgemäße Herunterfahren des Knotens zu ermöglichen.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="Bildschirm: Verhalten beim Herunterfahren des Clusterknotens" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. Wenn Sie SMB-Verbindungen verschlüsseln möchten, die zum Senden von Daten zwischen Clusterknoten verwendet werden, wählen Sie **Cluster traffic encryption** (Verschlüsselung des Clusterdatenverkehrs) und anschließend **Verschlüsseln** in den Dropdownfeldern für die folgenden Optionen aus:

   - **Core traffic** (Kerndatenverkehr): Verschlüsselt Datenverkehr, der über NetFT (virtueller Clusteradapter) an Port 3343 gesendet wird.

   - **Storage traffic** (Speicherdatenverkehr): Verschlüsselt CSV-Datenverkehr (Cluster Shared Volume, freigegebenes Clustervolume) und SBL-Datenverkehr (Storage Bus Layer).

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="Bildschirm: Verschlüsselung von Clusterdatenverkehr" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. Wenn Sie automatisch einen clusterübergreifenden Lastenausgleich für virtuelle Computer ausführen möchten, wählen Sie **Virtual machine load balancing** (Lastenausgleich für virtuelle Computer) aus, und führen Sie die folgenden Schritte aus:

   - Wählen Sie für **Balance virtual machines** (Virtuelle Computer ausgleichen) die entsprechende Aktion aus.
   - Wählen Sie für **Aggressiveness** (Aggressivität) das entsprechende Verhalten aus.

     Informationen dazu finden Sie unter [Übersicht über den Lastenausgleich virtueller Computer](/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="Bildschirm: Lastenausgleich für virtuelle Computer im Cluster" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. Wählen Sie zum Festlegen eines Quorumzeugentyps **Zeuge** und dann für **Zeugentyp** eine der folgenden Optionen aus:

   - **Cloudzeuge**: Verwenden einer Azure-Cloudressource als Zeuge
   - **Datenträgerzeuge**: Verwenden einer Datenträgerressource als Zeuge (nicht für Stretchingcluster zu verwenden)
   - **Dateifreigabenzeuge**: Verwenden einer Dateifreigabe als Zeuge

        Ausführliche Informationen zum Einrichten eines Zeugen finden Sie unter [Einrichten eines Clusterzeugen](../deploy/witness.md). Lesen Sie außerdem [Grundlegendes zum Cluster- und Poolquorum in Azure Stack HCI](../concepts/quorum.md).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Bildschirm: Clusterzeuge" lightbox="media/manage-cluster/cluster-settings-witness.png":::

1. Wählen Sie **Affinitätsregeln** aus, und klicken Sie dann auf **Regel erstellen**, um die Platzierung von virtuellen Maschinen auf Hostservern und an Standorten mithilfe von Affinitätsregeln zu verwenden. Ausführliche Informationen zum Einrichten von Regeln finden Sie unter [Erstellen von Server- und Standortaffinitätsregeln für VMs](vm-affinity.md).

    :::image type="content" source="media/manage-cluster/affinity-rules.png" alt-text="Affinitätsregel-Bildschirm für einen Cluster" lightbox="media/manage-cluster/affinity-rules.png":::

1. Wählen Sie **Diagnosedaten** und dann eine der folgenden Optionen aus, um festzulegen, wieviel Daten zur Diagnose an Microsoft gesendet werden:

    - **Diagnostic data off (Security)** (Diagnosedaten deaktiviert (Sicherheit)): Es werden keine Daten gesendet.
    - **Required (Basic)** (Erforderliche (Standardeinstellung)): Es wird das Minimum an Daten gesendet, das erforderlich ist, um Sicherheit und Aktualität zu gewährleisten.
    - **Optional (Full)** (Optional (Vollständig)): Alle verfügbaren Daten werden gesendet

    :::image type="content" source="media/manage-cluster/cluster-diagnostic-data.png" alt-text="Datendiagnose-Bildschirm für einen Cluster " lightbox="media/manage-cluster/cluster-diagnostic-data.png":::

## <a name="change-hyper-v-settings"></a>Ändern von Hyper-V-Einstellungen

Es gibt verschiedene Hyper-V-Hosteinstellungen, die auf Ihren Cluster angewendet werden können.

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Extras** die Option **Einstellungen** aus.
1. Wählen Sie **Allgemein** aus, und verwenden Sie dann die folgenden Einstellungen:

   - **Virtual Hard Disks Path** (Pfad der virtuellen Festplatten): Geben Sie den Standardordner zum Speichern von VHD-Dateien an.

   - **Virtual Machines Path** (Pfad der virtuellen Computer): Geben Sie den Standardordner zum Speichern der Konfigurationsdateien virtueller Computer an.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="Bildschirm: allgemeine Hyper-V-Clustereinstellungen" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Um die Umleitung von lokalen Geräten und Ressourcen auf virtuellen Computern zuzulassen, wählen Sie **Erweiterter Sitzungsmodus** aus. Beachten Sie, dass für Verbindungen des erweiterten Sitzungsmodus ein unterstütztes Gastbetriebssystem erforderlich ist.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="Bildschirm: erweiterter Hyper-V-Sitzungsmodus des Clusters" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Wählen Sie **Aufteilung auf NUMA** aus, um zuzulassen, dass sich virtuelle Computer auf physische NUMA-Knoten erstrecken. Bei der Aufteilung auf NUMA (Non-Uniform Memory Architecture) kann für virtuelle Computer mehr Arbeitsspeicher bereitgestellt werden, als in einem einzelnen NUMA-Knoten verfügbar ist.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="Bildschirm: Aufteilung auf NUMA für den Cluster" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Wenn Sie die Anzahl von virtuellen Computern angeben möchten, die während der Ausführung gleichzeitig verschoben werden können (Livemigration), wählen Sie **Livemigration** und eine Anzahl aus, und geben Sie Folgendes an:

   - Wählen Sie unter **Authentifizierungsprotokoll** entweder **CredSSP** oder **Kerberos** aus.

   - Wählen Sie unter **Performance Options** (Leistungsoptionen) entweder **Komprimierung** oder **SMB** aus. Komprimierte Daten werden über eine TCP/IP-Verbindung gesendet.

   - Aktivieren Sie das Kontrollkästchen **Use any network** (Beliebiges Netzwerk verwenden), um ein beliebiges verfügbares Netzwerk in einem Knoten zum Durchführen der Migration zu verwenden.

        :::image type="content" source="media/manage-cluster/cluster-settings-live-migration.png" alt-text="Clusterbildschirm: Livemigration" lightbox="media/manage-cluster/cluster-settings-live-migration.png":::

1. Wählen Sie zum Angeben der Anzahl von Speichermigrationen, die gleichzeitig ausgeführt werden können, die Option **Speichermigration** und dann eine Zahl aus.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-migration.png" alt-text="Clusterbildschirm: Speichermigration" lightbox="media/manage-cluster/cluster-settings-storage-migration.png":::

## <a name="register-the-cluster-with-azure"></a>Registrieren des Clusters bei Azure

Wählen Sie **Azure Stack HCI-Registrierung** aus, um Ihren Cluster bei Azure zu registrieren oder die Registrierung aufzuheben. Ausführliche Informationen hierzu finden Sie unter [Herstellen einer Verbindung von Azure Stack HCI mit Azure](../deploy/register-with-azure.md).

:::image type="content" source="media/manage-cluster/cluster-registration.png" alt-text="Azure-Registrierungsbildschirm für einen Cluster" lightbox="media/manage-cluster/cluster-registration.png":::

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Überwachen Ihres Clusters finden Sie unter [Überwachen von Azure Stack HCI mit Azure Monitor](azure-monitor.md).