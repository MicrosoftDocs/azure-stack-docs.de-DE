---
title: Überwachen von Azure Stack HCI-Clustern
description: Hier erfahren Sie, wie Sie Azure Stack HCI-Cluster, Server, virtuelle Computer, Laufwerke und Volumes mithilfe von Windows Admin Center und PowerShell überwachen.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: 9ce1dc258243e9e17458c1f70e5a852a0b56996a
ms.sourcegitcommit: a3e042c782a38ecf3baf7a64b1d492a655972f9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202043"
---
# <a name="monitor-azure-stack-hci-clusters"></a>Überwachen von Azure Stack HCI-Clustern

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI-Cluster und die zugrunde liegenden Komponenten können auf drei Arten überwacht werden: mit Windows Admin Center, [Azure Monitor](azure-monitor.md) und PowerShell.

## <a name="monitor-using-windows-admin-center-dashboard"></a>Überwachen mithilfe des Windows Admin Center-Dashboards

[Installieren Sie Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) auf einem Verwaltungscomputer oder -server, fügen Sie den zu überwachenden Azure Stack HCI-Cluster hinzu, und stellen Sie eine Verbindung her. Kritische Warnungen werden direkt nach der Anmeldung gut sichtbar im oberen Bereich des Windows Admin Center-Dashboards angezeigt. Auf dem folgenden Screenshot ist beispielsweise zu sehen, dass Updates installiert werden müssen und dass bei dem Cluster ein kritischer Laufwerksfehler aufgetreten ist:

:::image type="content" source="media/monitor-cluster/dashboard-alert.png" alt-text="Beispiel für Warnungen auf dem Windows Admin Center-Dashboard":::

## <a name="monitor-virtual-machines"></a>Überwachen virtueller Maschinen

Es ist wichtig, mit der Integrität der virtuellen Computer (virtual machines, VMs) vertraut zu sein, auf denen Ihre Anwendungen und Datenbanken ausgeführt werden. Sind einem virtuellen Computer nicht genügend CPU- oder Arbeitsspeicherressourcen für die darauf ausgeführten Workloads zugewiesen, kann es passieren, dass sich die Leistung verschlechtert oder dass die Anwendung nicht mehr verfügbar ist. Wenn ein virtueller Computer fünf Minuten oder länger auf weniger als drei Heartbeats antwortet, liegt möglicherweise ein Problem vor.

Klicken Sie zur Überwachung virtueller Computer in Windows Admin Center auf der linken Seite im Menü **Extras** auf **Virtuelle Computer**. Klicken Sie auf **Bestand**, um den Gesamtbestand der virtuellen Computer anzuzeigen, die in dem Cluster ausgeführt werden. Daraufhin wird eine Tabelle mit Informationen zu den einzelnen virtuellen Computern angezeigt. Hierzu zählen:

- **Name:** Der Name der VM.
- **Zustand:** Gibt an, ob der virtuelle Computer ausgeführt wird oder beendet wurde.
- **Hostserver:** Gibt an, auf welchem Server im Cluster der virtuelle Computer ausgeführt wird.
- **CPU-Auslastung:** Der durch den virtuellen Computer beanspruchte Prozentsatz der gesamten CPU-Ressourcen des Clusters.
- **Memory pressure** (Speicherauslastung): Der durch den virtuellen Computer beanspruchte Prozentsatz der verfügbaren Arbeitsspeicherressourcen.
- **Speicherbedarf:** Die durch den virtuellen Computer beanspruchte zugewiesene Arbeitsspeichermenge (in GB oder MB).
- **Zugewiesener Speicher:** Die Gesamtmenge des Arbeitsspeichers, der dem virtuellen Computer zugewiesen ist.
- **Uptime:** Gibt die Ausführungsdauer des virtuellen Computers an (in Tagen:Stunden:Minuten:Sekunden).
- **Heartbeat:** Gibt an, ob der Cluster mit dem virtuellen Computer kommunizieren kann.
- **Disaster recovery status** (Status der Notfallwiederherstellung): Gibt an, ob der virtuelle Computer bei Azure Site Recovery angemeldet ist.

## <a name="monitor-servers"></a>Überwachen von Servern

Die Hostserver eines Azure Stack HCI-Clusters können direkt von Windows Admin Center aus überwacht werden. Hostserver, die nicht mit genügend CPU- oder Arbeitsspeicherressourcen konfiguriert sind, um die von virtuellen Computern benötigten Ressourcen bereitzustellen, können sich als Leistungsengpass erweisen. 

Klicken Sie zur Überwachung von Servern in Windows Admin Center auf der linken Seite im Menü **Extras** auf **Server**. Klicken Sie auf **Bestand**, um den Gesamtbestand der Server im Cluster anzuzeigen. Daraufhin wird eine Tabelle mit Informationen zu den einzelnen Servern angezeigt. Hierzu zählen:

- **Name:** Der Name des Hostservers im Cluster.
- **Status:** Gibt an, ob der Server bereit ist.
- **Uptime:** Gibt die Bereitschaftsdauer des Servers an.
- **Hersteller:** Der Hardwarehersteller des Servers.
- **Modell:** Das Modell des Servers.
- **Seriennummer:** Die Seriennummer des Servers.
- **CPU-Auslastung:** Die Auslastung der Hostserver-CPU (in Prozent). Die CPU-Auslastung sollte bei keinem Server im Cluster länger als zehn Minuten über 85 Prozent liegen. 
- **Speicherauslastung:** Die Auslastung des Hostserver-Arbeitsspeichers (in Prozent). Sollten bei einem Server mindestens zehn Minuten lang weniger als 100 MB Arbeitsspeicher verfügbar sein, empfiehlt es sich gegebenenfalls, den Arbeitsspeicher zu erweitern.

## <a name="monitor-volumes"></a>Überwachen von Volumes

Da sich Speichervolumes schnell füllen können, empfiehlt sich eine regelmäßige Überwachung, um Auswirkungen auf Anwendungen zu vermeiden. Klicken Sie zur Überwachung von Volumes in Windows Admin Center auf der linken Seite im Menü **Extras** auf **Volumes**. Klicken Sie auf **Bestand**, um den Gesamtbestand der Speichervolumes im Cluster anzuzeigen. Daraufhin wird eine Tabelle mit Informationen zu den einzelnen Volumes angezeigt. Hierzu zählen:

- **Name:** Der Name des Volumes.
- **Status:** „OK“ gibt an, dass das Volume fehlerfrei ist. Andernfalls wird eine Warnung oder ein Fehler ausgegeben.
- **Dateisystem:** Das Dateisystem auf dem Volume (ReFS, CSVFS).
- **Resilienz:** Gibt an, ob es sich bei dem Volume um eine Zwei-Wege-Spiegelung, um eine Drei-Wege-Spiegelung oder um eine Parität mit Beschleunigung per Spiegelung handelt.
- **Size:** Die Größe des Volumes (in TB/GB).
- **Speicherpool:** Der Speicherpool, zu dem das Volume gehört.
- **Speichernutzung:** Der Prozentsatz der beanspruchten Speicherkapazität des Volumes.
- **IOPS:** Die Anzahl von E/A-Vorgängen pro Sekunde.

## <a name="monitor-drives"></a>Überwachen von Laufwerken

Azure Stack HCI virtualisiert den Speicher so, dass der Cluster durch den Verlust eines einzelnen Laufwerks nicht signifikant beeinträchtigt wird. Fehlerhafte Laufwerke [müssen jedoch ersetzt werden](replace-drives.md), und Laufwerke können sich negativ auf die Leistung auswirken, wenn sie voll werden oder Wartezeiten verursachen. Falls das Betriebssystem nicht mit einem Laufwerk kommunizieren kann, ist das Laufwerk möglicherweise locker oder nicht angeschlossen. Unter Umständen ist aber auch der Stecker oder das Laufwerk selbst defekt. Laufwerke, mit denen nicht kommuniziert werden kann, werden von Windows nach 15 Minuten automatisch außer Betrieb gesetzt. 

Klicken Sie zur Überwachung von Laufwerken in Windows Admin Center auf der linken Seite im Menü **Extras** auf **Laufwerke**. Klicken Sie auf **Bestand**, um den Gesamtbestand der Laufwerke im Cluster anzuzeigen. Daraufhin wird eine Tabelle mit Informationen zu den einzelnen Laufwerken angezeigt. Hierzu zählen:

- **Seriennummer:** Die Seriennummer des Laufwerks.
- **Status:** „OK“ gibt an, dass das Laufwerk fehlerfrei ist. Andernfalls wird eine Warnung oder ein Fehler ausgegeben.
- **Modell:** Das Modell des Laufwerks.
- **Size:** Die Gesamtkapazität des Laufwerks (in TB/GB).
- **Typ:** Die Art des Laufwerks (SSD, HDD).
- **Verwendet für:** Gibt an, ob das Laufwerk für Cache oder für Kapazität verwendet wird.
- **Standort:** Der Speicheradapter und der Port, mit dem das Laufwerk verbunden ist.
- **Server:** Der Name des Servers, mit dem das Laufwerk verbunden ist.
- **Speicherpool:** Der Speicherpool, zu dem das Laufwerk gehört.
- **Speichernutzung:** Der Prozentsatz der beanspruchten Speicherkapazität des Laufwerks.

## <a name="add-performance-counters"></a>Hinzufügen von Leistungsindikatoren

Verwenden Sie das Tool „Systemmonitor“ in Windows Admin Center, um Leistungsindikatoren für Windows, Apps oder Geräte in Echtzeit anzuzeigen und zu vergleichen.

1. Wählen Sie im Menü **Tools** auf der linken Seite **Systemmonitor** aus.
1. Klicken Sie auf **Leerer Arbeitsbereich**, um einen neuen Arbeitsbereich zu starten, oder auf **Vorgängerversionen wiederherstellen**, um einen vorherigen Arbeitsbereich wiederherzustellen.
1. Wenn Sie einen neuen Arbeitsbereich erstellen, klicken Sie auf die Schaltfläche **Leistungsindikator hinzufügen**, und wählen Sie einen oder mehrere zu überwachende Quellserver oder den gesamten Cluster aus.
1. Wählen Sie das Objekt und die Instanz aus, die Sie überwachen möchten, sowie den Leistungsindikator- und Diagrammtyp, um dynamische Leistungsinformationen anzuzeigen.
1. Speichern Sie den Arbeitsbereich, indem Sie im oberen Menü **Speichern > Speichern unter** auswählen.
 
Der folgende Screenshot zeigt beispielsweise einen Leistungsindikator namens „Memory usage“ (Speicherauslastung) mit Informationen zum Arbeitsspeicher in einem Cluster mit zwei Knoten:

:::image type="content" source="media/monitor-cluster/performance-monitor.png" alt-text="Beispiel für einen Echtzeit-Leistungsindikator in Windows Admin Center":::

## <a name="query-and-process-performance-history-with-powershell"></a>Abfragen und Verarbeiten des Leistungsverlaufs mit PowerShell

Azure Stack HCI-Cluster können auch mithilfe von PowerShell-Cmdlets überwacht werden, die Informationen zum Cluster und zu den zugehörigen Komponenten zurückgeben. Weitere Informationen finden Sie unter [Leistungsverlauf für direkte Speicherplätze](/windows-server/storage/storage-spaces/performance-history).

## <a name="use-the-health-service-feature"></a>Verwenden des Features „Integritätsdienst“

Jeglicher Integritätsdienstfehler im Cluster sollte untersucht werden. Unter [Integritätsdienst in Windows Server](/windows-server/failover-clustering/health-service-overview) erfahren Sie, wie Sie Berichte ausführen und Fehler identifizieren.

## <a name="troubleshoot-health-and-operational-states"></a>Behandeln von Problemen im Zusammenhang mit Integritäts-und Betriebszuständen

Informationen zu den Integritäts- und Betriebszuständen von Speicherpools, virtuellen Datenträgern und Laufwerken finden Sie unter [Behandeln von Problemen im Zusammenhang mit Integritäts- und Betriebszuständen von Speicherplätzen und direkten Speicherplätzen](/windows-server/storage/storage-spaces/storage-spaces-states).

## <a name="monitor-performance-using-storage-qos"></a>Überwachen der Leistung mithilfe von Speicher-QoS

Quality of Service (QoS) für Speicher ermöglicht die zentrale Überwachung und Verwaltung von Speicher-E/A-Vorgängen für virtuelle Computer, um sogenannte Noisy-Neighbor-Problem zu vermeiden und eine konsistente Leistung zu gewährleisten. Weitere Informationen finden Sie unter [Quality of Service für Speicher](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="set-up-alerts-in-azure-monitor"></a>Einrichten von Warnungen in Azure Monitor

Azure Stack HCI wird in Azure Monitor integriert, um Benutzern das [Einrichten von Warnungen](azure-monitor.md#setting-up-alerts-using-windows-admin-center) zu ermöglichen. Dadurch können sich die Benutzer benachrichtigen lassen, wenn die Schwellenwerte für CPU-Auslastung, Festplattenkapazität und Speicherauslastung überschritten werden, wenn keine VM-Heartbeats zurückgegeben werden oder wenn ein systemkritischer Fehler oder ein Integritätsdienstfehler vorliegt.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Erste Schritte mit Azure Stack HCI und Windows Admin Center](../get-started.md)
- [Überwachen von Azure Stack HCI mit Azure Monitor](azure-monitor.md)