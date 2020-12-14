---
title: Erste Schritte mit Azure Stack HCI und Windows Admin Center
description: Stellen Sie schnell eine Verbindung mit einem vorhandenen Azure Stack HCI-Cluster her, und überwachen Sie die Cluster- und Speicherleistung mithilfe von Windows Admin Center.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 3ae8cf313b0b89813bb32efb9fbb45d0fbf05c91
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051722"
---
# <a name="get-started-with-azure-stack-hci-and-windows-admin-center"></a>Erste Schritte mit Azure Stack HCI und Windows Admin Center

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Dieses Thema enthält eine Anleitung zum Herstellen einer Verbindung mit einem Azure Stack HCI-Cluster sowie zum Überwachen der Cluster- und Speicherleistung. Falls Sie noch keinen Cluster eingerichtet haben, [laden Sie Azure Stack HCI herunter](https://azure.microsoft.com/products/azure-stack/hci/hci-download/), und lesen Sie die Anweisungen unter [Schnellstart: Erstellen eines Azure Stack HCI-Clusters und Registrieren des Clusters bei Azure](deploy/deployment-quickstart.md).

## <a name="install-windows-admin-center"></a>Installieren von Windows Admin Center

Windows Admin Center ist eine lokal bereitgestellte, browserbasierte App zum Verwalten von Azure Stack HCI. Die einfachste Möglichkeit zum [Installieren von Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) ist die Verwendung eines lokalen Verwaltungscomputers (Desktopmodus). Die Installation auf einem Server (Dienstmodus) ist jedoch ebenfalls möglich.

Bei der Installation von Windows Admin Center auf einem Server muss für Aufgaben, die CredSSP erfordern (etwa Clustererstellung sowie Installation von Updates und Erweiterungen), ein Konto verwendet werden, das der Gatewayadministratorgruppe auf dem Windows Admin Center-Server angehört. Weitere Informationen finden Sie in den ersten beiden Abschnitten des Artikels [Konfigurieren der Benutzerzugriffssteuerung und von Berechtigungen](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="add-and-connect-to-an-azure-stack-hci-cluster"></a>Hinzufügen eines Azure Stack HCI-Clusters und Herstellen einer Verbindung mit dem Cluster

Nachdem Sie die Installation von Windows Admin Center abgeschlossen haben, können Sie über die Hauptübersichtsseite einen zu verwaltenden Cluster hinzufügen.

1. Klicken Sie unter **Alle Verbindungen** auf **+ Hinzufügen**.

    :::image type="content" source="media/get-started/addcluster.png" alt-text="Screenshot: Hinzufügen eines Clusters":::

2. Wählen Sie die Option zum Hinzufügen eines Windows Server-Clusters aus:

    :::image type="content" source="media/get-started/chooseconnectiontype.png" alt-text="Screenshot: Verbindungstyp auswählen":::

3. Geben Sie den Namen des zu verwaltenden Clusters ein, und klicken Sie auf **Hinzufügen**. Der Cluster wird der Verbindungsliste auf der Übersichtsseite hinzugefügt.

4. Klicken Sie unter **Alle Verbindungen** auf den Namen des Clusters, den Sie soeben hinzugefügt haben. In Windows Admin Center wird **Cluster-Manager** gestartet, und Sie gelangen direkt zum Windows Admin Center-Dashboard für diesen Cluster.

## <a name="monitor-cluster-performance-with-the-windows-admin-center-dashboard"></a>Überwachen der Clusterleistung mit dem Windows Admin Center-Dashboard

Das Windows Admin Center-Dashboard bietet Warnungen und Integritätsinformationen zu Servern, Laufwerken und Volumes sowie Details zur CPU-Nutzung, Arbeitsspeicherauslastung und Speichernutzung. Am unteren Rand des Dashboards werden Informationen zur Clusterleistung angezeigt, z. B. IOPS und Latenz nach Stunde, Tag, Woche, Monat oder Jahr.

:::image type="content" source="media/get-started/dashboard.png" alt-text="Screenshot: Windows Admin Center-Dashboard":::

## <a name="monitor-performance-of-individual-components"></a>Überwachen der Leistung einzelner Komponenten

Im Menü **Tools** auf der linken Seite des Dashboards können Sie einen Drilldown für eine beliebige Komponente des Clusters ausführen, um Zusammenfassungen und Inventare von virtuellen Computern, Servern, Volumes und Laufwerken anzuzeigen.

### <a name="virtual-machines"></a>Virtuelle Computer

Um eine Zusammenfassung der VMs anzuzeigen, die im Cluster ausgeführt werden, klicken Sie im Menü **Tools** auf der linken Seite auf **Virtuelle Computer**.

:::image type="content" source="media/get-started/vms-summary.png" alt-text="Zusammenfassung der VMs":::

Um ein vollständiges Inventar der im Cluster ausgeführten VMs, einschließlich ihres Status, des Hostservers, der CPU-Nutzung, der Speicherauslastung, des Speicherbedarfs, des zugewiesenen Speichers und der Betriebszeit, anzuzeigen, klicken Sie oben auf der Seite auf **Inventar**.

:::image type="content" source="media/get-started/vms-inventory.png" alt-text="Inventar der VMs":::

### <a name="servers"></a>Server

Um eine Zusammenfassung der Server im Cluster anzuzeigen, klicken Sie im Menü **Tools** auf der linken Seite auf **Server**.

:::image type="content" source="media/get-started/servers-summary.png" alt-text="Zusammenfassung der Server":::

Um ein vollständiges Inventar der Server im Cluster, einschließlich ihres Status, der Betriebszeit, des Herstellers, des Modells und der Seriennummer, anzuzeigen, klicken Sie oben auf der Seite auf **Inventar**.

:::image type="content" source="media/get-started/servers-inventory.png" alt-text="Inventar der Server":::

### <a name="volumes"></a>Volumes

Um eine Zusammenfassung der Volumes im Cluster anzuzeigen, klicken Sie im Menü **Tools** auf der linken Seite auf **Volumes**.

:::image type="content" source="media/get-started/volumes-summary.png" alt-text="Zusammenfassung der Volumes":::

Um ein vollständiges Inventar der Volumes im Cluster, einschließlich Status, Dateisystem, Resilienz, Größe, Speichernutzung und IOPS, anzuzeigen, klicken Sie oben auf der Seite auf **Inventar**.

:::image type="content" source="media/get-started/volumes-inventory.png" alt-text="Inventar der Volumes":::

### <a name="drives"></a>Laufwerke

Um eine Zusammenfassung der Laufwerke im Cluster anzuzeigen, klicken Sie im Menü **Tools** auf der linken Seite auf **Laufwerke**.

:::image type="content" source="media/get-started/drives-summary.png" alt-text="Zusammenfassung der Laufwerke":::

Um ein vollständiges Inventar der Laufwerke im Cluster sowie ihre Seriennummer, ihren Status, ihr Modell, ihren Typ, ihre Verwendung, ihre Position, ihren Server und ihre Kapazität anzuzeigen, klicken Sie oben auf der Seite auf **Inventar**.

:::image type="content" source="media/get-started/drives-inventory.png" alt-text="Inventar der Laufwerke":::

### <a name="virtual-switches"></a>Virtuelle Switches

Um die Einstellungen für einen virtuellen Switch im Cluster anzuzeigen, klicken Sie im Menü **Tools** auf der linken Seite auf **Virtuelle Switches**, und klicken Sie dann auf den Namen des virtuellen Switches, dessen Einstellungen Sie anzeigen möchten. In Windows Admin Center werden die dem virtuellen Switch zugeordneten Netzwerkadapter angezeigt, einschließlich ihrer IP-Adressen, des Verbindungsstatus, der Verbindungsgeschwindigkeit und der MAC-Adresse.

:::image type="content" source="media/get-started/virtual-switch-settings.png" alt-text="Einstellungen für virtuelle Switches":::

## <a name="add-counters-with-the-performance-monitor-tool"></a>Hinzufügen von Leistungsindikatoren mit dem Tool Systemmonitor

Verwenden Sie das Tool Systemmonitor, um Leistungsindikatoren für Windows, Apps oder Geräte in Echtzeit anzuzeigen und zu vergleichen.

1. Wählen Sie im Menü **Tools** auf der linken Seite **Systemmonitor** aus.
2. Klicken Sie auf **Leerer Arbeitsbereich**, um einen neuen Arbeitsbereich zu starten, oder auf **Vorgängerversionen wiederherstellen**, um einen vorherigen Arbeitsbereich wiederherzustellen.
    :::image type="content" source="media/get-started/performance-monitor.png" alt-text="Screenshot von Systemmonitor":::
3. Wenn Sie einen neuen Arbeitsbereich erstellen, klicken Sie auf die Schaltfläche **Leistungsindikator hinzufügen**, und wählen Sie einen oder mehrere zu überwachende Quellserver oder den gesamten Cluster aus.
4. Wählen Sie das Objekt und die Instanz aus, die Sie überwachen möchten, sowie den Leistungsindikator- und Diagrammtyp, um dynamische Leistungsinformationen anzuzeigen.
    :::image type="content" source="media/get-started/example-counter.png" alt-text="Screenshot: Beispiel für Leistungsindikatoren":::
5. Speichern Sie den Arbeitsbereich, indem Sie im oberen Menü **Speichern > Speichern unter** auswählen.

## <a name="use-azure-monitor-for-monitoring-and-alerts"></a>Verwenden von Azure Monitor für Überwachung und Warnungen

Sie können auch [Azure Monitor](manage/azure-monitor.md) verwenden, um Ereignisse und Leistungsindikatoren für die Analyse und Berichterstellung zu erfassen, Maßnahmen zu ergreifen, wenn eine bestimmte Bedingung erkannt wird, und Benachrichtigungen per E-Mail zu empfangen. Klicken Sie im Menü **Tools** auf **Azure Monitor**, um über Windows Admin Center eine direkte Verbindung mit Azure herzustellen.

## <a name="collect-diagnostics-information"></a>Sammeln von Diagnosedaten

Wählen Sie im Menü **Tools** die Option **Diagnose** aus, um Informationen zur Behandlung von Problemen beim Cluster zu sammeln. Wenn Sie den Microsoft-Support aufzurufen, werden Sie möglicherweise um diese Informationen gebeten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Überwachung Ihrer Azure Stack HCI-Cluster finden Sie auch in den folgenden Artikeln:

- [Leistungsverlauf für Direkte Speicherplätze](/windows-server/storage/storage-spaces/performance-history)
- [Überwachen von Azure Stack HCI mit Azure Monitor](manage/azure-monitor.md)
