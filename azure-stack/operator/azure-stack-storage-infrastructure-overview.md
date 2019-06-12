---
title: Verwalten der Speicherinfrastruktur für Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Speicherinfrastruktur für Azure Stack verwalten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: c7ae7f0c8fa510b0f2b55e458266065544e1bd5e
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828215"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Verwalten der Speicherinfrastruktur für Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesem Artikel werden die Integrität und der Betriebszustand von Ressourcen der Azure Stack-Speicherinfrastruktur beschrieben. Diese Ressourcen umfassen Speicherdatenträger und Volumes. Die Informationen in diesem Thema können sehr hilfreich bei der Behandlung verschiedener Probleme sein, z. B. wenn ein Laufwerk einem Pool nicht hinzugefügt werden kann.

## <a name="understand-drives-and-volumes"></a>Grundlegendes zu Laufwerken und Volumes

### <a name="drives"></a>Laufwerke

Azure Stack basiert auf Windows Server-Software und definiert Speicherfunktionen mit einer Kombination aus „Direkte Speicherplätze“ (Storage Spaces Direct, S2D) und Windows Server-Failoverclustering, um einen leistungsstarken, skalierbaren und resilienten Speicherdienst bereitzustellen.

Partner für integrierte Azure Stack-Systeme bieten viele verschiedene Lösungsvarianten an, z. B. umfassende Flexibilität in Bezug auf Speicher. Sie können derzeit eine Kombination aus drei Laufwerkstypen auswählen: Nicht flüchtiger Speicher (Non-Volatile Memory Express, NVMe), SSD-Datenträger (SATA/SAS SSD) und Festplattenlaufwerk (Hard Disk Drive, HDD).

„Direkte Speicherplätze“ verfügt über einen Cache zum Maximieren der Speicherleistung. Bei einer Azure Stack-Appliance mit einem oder mehreren Laufwerkstypen werden für „Direkte Speicherplätze“ automatisch alle Laufwerke des „schnellsten“ Typs (NVMe &gt; SSD &gt; HDD) für die Zwischenspeicherung verwendet. Alle weiteren Datenträger werden zur Bereitstellung der Kapazität verwendet. Diese Laufwerke können entweder zu einer Bereitstellung vom Typ „Nur Flash“ oder „Hybrid“ gruppiert werden:

![Azure Stack-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image1.png)

Bei Nur-Flash-Bereitstellungen wird die Speicherleistung maximiert, und es sind keine rotierenden Festplattenlaufwerke (HDD) vorhanden.

![Azure Stack-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image2.png)

Bei Hybridbereitstellungen soll eine Balance zwischen Leistung und Kapazität erzielt oder die Kapazität maximiert werden, und es werden rotierende Festplattenlaufwerke (HDD) verwendet.

Das Verhalten des Caches wird automatisch anhand des Typs von Laufwerken ermittelt, für die die Zwischenspeicherung durchgeführt wird. Bei der Zwischenspeicherung für SSD-Datenträger (z. B. NVMe-Zwischenspeicherung für SSDs) werden nur Schreibvorgänge zwischengespeichert. Auf diese Weise wird die Belastung der Kapazitätslaufwerke verringert und der gesamte Datenverkehr reduziert, sodass sich ihre Lebensdauer erhöht. In der Zwischenzeit werden Lesevorgänge nicht zwischengespeichert, weil sich Lesevorgänge nicht stark auf die Lebensdauer von Flashspeicher auswirken und SSD-Datenträger bei diesen Vorgängen generell eine geringe Latenz aufweisen. Beim Zwischenspeichern für Festplattenlaufwerke (z. B. SSD-Zwischenspeicherung für HDDs) werden Lese- und Schreibvorgänge zwischengespeichert, um in beiden Fällen Latenzwerte wie bei Flashspeicher zu erzielen (häufig ca. um den Faktor 10 verbessert).

![Azure Stack-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image3.png)

Informationen zu den verfügbaren Speicherkonfigurationen und eine ausführliche Spezifikation erhalten Sie beim Azure Stack-OEM-Partner (https://azure.microsoft.com/overview/azure-stack/partners/).

> [!Note]  
> Die Azure Stack-Appliance kann im Rahmen einer Hybridbereitstellung, die sowohl über HDD- als auch über SSD-Laufwerke (oder NVMe) verfügt, verfügbar gemacht werden. Die Laufwerke des schnelleren Typs werden dann aber als Cachelaufwerke verwendet, und alle verbleibenden Laufwerke werden als Kapazitätslaufwerke in einem Pool genutzt. Die Mandantendaten (Blobs, Tabellen, Warteschlangen und Datenträger) werden auf Kapazitätslaufwerken angeordnet. Die Bereitstellung von Premium-Datenträgern oder die Auswahl des Kontotyps „Storage Premium“ bedeutet also nicht, dass die Objekte auf jeden Fall SSD- oder NVMe-Laufwerken zugeordnet werden und eine höhere Leistung erhalten.

### <a name="volumes"></a>Volumes

Der *Speicherdienst* partitioniert den verfügbaren Speicher in separate Volumes, die zum Speichern von System- und Mandantendaten zugeordnet werden. Für Volumes werden die Laufwerke im Speicherpool kombiniert, um die Fehlertoleranz, Skalierbarkeit und Leistungsvorteile von „Direkte Speicherplätze“ zu erhalten.

![Azure Stack-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image4.png)

Es gibt drei Arten von Volumes, die im Azure Stack-Speicherpool erstellt werden:

-   Infrastruktur: Hier werden Dateien gehostet, die von VMs und Kerndiensten der Azure Stack-Infrastruktur verwendet werden.

-   VM temporär: Hier werden die temporären Datenträger gehostet, die an Mandanten-VMs angefügt sind, und diese Daten werden auf diesen Datenträgern gespeichert.

-   Objektspeicher: Hier werden Mandantendaten für Blobs, Tabellen, Warteschlangen und VM-Datenträger gehostet.

In einer Bereitstellung mit mehreren Knoten sind drei Infrastrukturvolumes vorhanden, und die Anzahl von temporären VM-Volumes und Objektspeichervolumes entspricht der Anzahl von Knoten in der Azure Stack-Bereitstellung:

-   Bei einer Bereitstellung mit vier Knoten sind vier gleiche temporäre VM-Volumes und vier gleiche Objektspeichervolumes vorhanden.

-   Wenn Sie dem Cluster einen neuen Knoten hinzufügen, wird für beide Typen ein neues Volume erstellt.

-   Die Anzahl von Volumes bleibt auch dann gleich, wenn ein Knoten fehlerhaft ist oder entfernt wurde.

-   Bei Verwendung des Azure Stack Developer Kits ist ein einzelnes Volume mit mehreren Freigaben vorhanden.

Volumes unter „Direkte Speicherplätze“ bieten Resilienz als Schutz vor Hardwareproblemen, z. B. Laufwerks- oder Serverfehler, und ermöglichen eine ununterbrochene Verfügbarkeit bei der Serverwartung, z. B. Softwareupdates. Für die Azure Stack-Bereitstellung wird die Drei-Wege-Spiegelung verwendet, um für Datenresilienz zu sorgen. Drei Kopien von Mandantendaten werden auf unterschiedliche Server geschrieben, wo sie im Cache gespeichert werden:

![Azure Stack-Speicherinfrastruktur](media/azure-stack-storage-infrastructure-overview/image5.png)

Die Spiegelung ermöglicht Fehlertoleranz, indem mehrere Kopien aller Daten aufbewahrt werden. Das Striping und die Anordnung dieser Daten ist nicht trivial (weitere Informationen in diesem Blog), aber es lässt sich festhalten, dass alle per Spiegelung gespeicherten Daten vollständig mehrfach geschrieben werden. Jede Kopie wird auf andere physische Hardware (unterschiedliche Laufwerke von nicht identischen Servern) geschrieben, für die angenommen wird, dass sie nicht gemeinsam ausfallen. Bei der Drei-Wege-Spiegelung können mindestens zwei gleichzeitige Hardwareprobleme (Laufwerk oder Server) problemlos toleriert werden. Wenn Sie beispielsweise einen Server neu starten und plötzlich ein anderes Laufwerk bzw. ein Server ausfällt, bleiben alle Daten geschützt und sind ohne Unterbrechung zugänglich.

## <a name="volume-states"></a>Volumezustände

Verwenden Sie die folgenden PowerShell-Befehle, um zu ermitteln, in welchem Zustand sich Volumes befinden:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

Hier ist ein Beispiel für eine Ausgabe mit einem getrennten Volume und einem heruntergestuften/unvollständigen Volume angegeben:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Unknown | Getrennt |
| ObjStore_2 | Warnung | {Heruntergestuft, Unvollständig} |

In den folgenden Abschnitten sind die Informationen zur Integrität und zum Betriebszustand angegeben.

### <a name="volume-health-state-healthy"></a>Integritätszustand des Volumes: Healthy

| Betriebszustand | BESCHREIBUNG |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Das Volume ist fehlerfrei. |
| Suboptimal | Daten werden nicht gleichmäßig auf Laufwerke geschrieben.<br> <br>**Aktion:** Wenden Sie sich an den Support, um die Laufwerknutzung im Speicherpool zu optimieren. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. Sie müssen unter Umständen eine Wiederherstellung aus einer Sicherung durchführen, nachdem die fehlerhafte Verbindung wiederhergestellt wurde. |


### <a name="volume-health-state-warning"></a>Integritätszustand des Volumes: Warnung

Wenn sich das Volume im Integritätszustand „Warnung“ befindet, ist mindestens eine Kopie Ihrer Daten nicht verfügbar, aber Azure Stack kann trotzdem mindestens eine Kopie Ihrer Daten lesen.

| Betriebszustand | BESCHREIBUNG |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktiv | Azure Stack repariert das Volume, z. B. nach dem Hinzufügen oder Entfernen eines Laufwerks. Nach Abschluss der Reparatur sollte das Volume in den Integritätszustand „OK“ zurückkehren.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack die Reparatur des Volumes abgeschlossen hat, und überprüfen Sie den Zustand anschließend. |
| Unvollständig | Die Resilienz des Volumes wird reduziert, weil mindestens ein Laufwerk ausgefallen ist oder fehlt. Die fehlenden Laufwerke enthalten aber aktuelle Kopien Ihrer Daten.<br> <br>**Aktion:** Stellen Sie die Verbindung für fehlende Laufwerke wieder her, tauschen Sie alle fehlerhaften Laufwerke aus, und versetzen Sie alle im Offlinezustand befindlichen Server wieder in den Onlinezustand. |
| Heruntergestuft | Die Resilienz des Volumes wird reduziert, weil mindestens ein Laufwerk ausgefallen ist oder fehlt, und auf den Laufwerken befinden sich veraltete Kopien Ihrer Daten.<br> <br>**Aktion:** Stellen Sie die Verbindung für fehlende Laufwerke wieder her, tauschen Sie alle fehlerhaften Laufwerke aus, und versetzen Sie alle im Offlinezustand befindlichen Server wieder in den Onlinezustand. |

 

### <a name="volume-health-state-unhealthy"></a>Integritätszustand des Volumes: Fehlerhaft

Wenn sich ein Volume im Integritätszustand „Fehlerhaft“ befindet, kann auf einige oder alle Daten des Volumes derzeit nicht zugegriffen werden.

| Betriebszustand | BESCHREIBUNG |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Keine Redundanz | Für das Volume ist es zu einem Datenverlust gekommen, weil zu viele Laufwerke ausgefallen sind.<br> <br>**Aktion:** Wenden Sie sich an den Support. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |


### <a name="volume-health-state-unknown"></a>Integritätszustand des Volumes: Unknown

Das Volume kann sich auch im Integritätszustand „Unbekannt“ befinden, falls der virtuelle Datenträger getrennt wurde.

| Betriebszustand | BESCHREIBUNG |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Getrennt | Ein Speichergerätfehler ist aufgetreten, der dazu führt, dass das Volume nicht zugänglich ist. Unter Umständen gehen einige Daten verloren.<br> <br>**Aktion:** <br>1. Überprüfen Sie die physische Verbindung und Netzwerkverbindung aller Speichergeräte.<br>2. Wenden Sie sich an den Support, falls alle Geräte richtig verbunden sind. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. Sie müssen unter Umständen eine Wiederherstellung aus einer Sicherung durchführen, nachdem die fehlerhafte Verbindung wiederhergestellt wurde. |

## <a name="drive-states"></a>Laufwerkstatus

Verwenden Sie die folgenden PowerShell-Befehle, um den Zustand der Laufwerke zu überwachen:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

In den folgenden Abschnitten werden die Integritätszustände beschrieben, in denen sich ein Laufwerk befinden kann.

### <a name="drive-health-state-healthy"></a>Integritätszustand des Laufwerks: Healthy

| Betriebszustand | BESCHREIBUNG |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Das Volume ist fehlerfrei. |
| Aktiv | Das Laufwerk führt einige interne Housekeepingvorgänge durch. Nach Abschluss der Aktion sollte das Laufwerk in den Integritätszustand „OK“ zurückkehren. |

### <a name="drive-health-state-healthy"></a>Integritätszustand des Laufwerks: Healthy

Ein Laufwerk im Zustand „Warnung“ kann Daten erfolgreich lesen und schreiben, aber es liegt ein Problem vor.

| Betriebszustand | BESCHREIBUNG |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Verbindung unterbrochen | Die Konnektivität für das Laufwerk ist verloren gegangen.<br> <br>**Aktion:** Versetzen Sie alle Server wieder in den Onlinezustand. Schließen Sie das Laufwerk wieder an, falls das Problem hierdurch nicht behoben wurde. Tauschen Sie das Laufwerk aus, falls dieses Problem weiterhin auftritt, um die vollständige Resilienz sicherzustellen. |
| Vorhersehbarer Fehler | Es ist vorhersehbar, dass für das Laufwerk in Kürze ein Fehler auftritt.<br> <br>**Aktion:** Tauschen Sie das Laufwerk so bald wie möglich aus, um die vollständige Resilienz sicherzustellen. |
| E/A-Fehler | Beim Zugreifen auf das Laufwerk ist ein vorübergehender Fehler aufgetreten.<br> <br>**Aktion:** Tauschen Sie das Laufwerk aus, falls dieses Problem weiterhin auftritt, um die vollständige Resilienz sicherzustellen. |
| Vorübergehender Fehler | Für das Laufwerk ist ein vorübergehender Fehler aufgetreten. Normalerweise bedeutet dies, dass das Laufwerk nicht reagiert hat. Es kann aber auch der Fall sein, dass die Schutzpartition von „Direkte Speicherplätze“ auf nicht korrekte Weise vom Laufwerk entfernt wurde. <br> <br>**Aktion:** Tauschen Sie das Laufwerk aus, falls dieses Problem weiterhin auftritt, um die vollständige Resilienz sicherzustellen. |
| Nicht ordnungsgemäße Wartezeit | Das Laufwerk reagiert manchmal nicht und weist Anzeigen für einen Ausfall auf.<br> <br>**Aktion:** Tauschen Sie das Laufwerk aus, falls dieses Problem weiterhin auftritt, um die vollständige Resilienz sicherzustellen. |
| Wird aus dem Pool entfernt | Azure Stack entfernt das Laufwerk gerade aus seinem Speicherpool.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack die Entfernung des Laufwerks abgeschlossen hat, und überprüfen Sie den Status anschließend.<br>Wenden Sie sich an den Support, falls der Status beibehalten wird. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
|  |  |
| Wartungsmodus wird gestartet | Azure Stack versetzt das Laufwerk gerade in den Wartungsmodus. Dies ist ein vorübergehender Zustand. Das Laufwerk sollte sich in Kürze im Zustand „Wartungsmodus“ befinden.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack den Prozess beendet, und prüfen Sie anschließend den Status. |
| Im Wartungsmodus | Das Laufwerk befindet sich im Wartungsmodus, und Lese- und Schreibvorgänge für das Laufwerk werden angehalten. Dies bedeutet normalerweise, dass das Laufwerk über Azure Stack-Verwaltungsaufgaben, z. B. PNU oder FRU, betrieben wird. Aber auch der Administrator kann das Laufwerk in den Wartungsmodus versetzen.<br> <br>**Aktion:** Warten Sie, bis Azure Stack die Verwaltungsaufgabe abgeschlossen hat, und prüfen Sie anschließend den Status.<br>Wenden Sie sich an den Support, falls der Status beibehalten wird. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
|  |  |
| Wartungsmodus wird beendet | Azure Stack versetzt das Laufwerk gerade wieder in den Onlinezustand. Dies ist ein vorübergehender Zustand. Das Laufwerk sollte sich in Kürze in einem anderen Zustand befinden (idealerweise „Fehlerfrei“).<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack den Prozess beendet, und prüfen Sie anschließend den Status. |

 

### <a name="drive-health-state-unhealthy"></a>Integritätszustand des Laufwerks: Fehlerhaft

Auf ein Laufwerk im Zustand „Fehlerhaft“ kann derzeit nicht geschrieben werden, und der Zugriff darauf ist nicht möglich.

| Betriebszustand | BESCHREIBUNG |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Split | Das Laufwerk wurde vom Pool getrennt.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Nicht verwendbar | Der physikalische Datenträger wurde unter Quarantäne gestellt, da er von Ihrem Lösungsanbieter nicht unterstützt wird. Es werden nur Datenträger unterstützt, die für die Lösung genehmigt wurden und über die richtige Datenträgerfirmware verfügen.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen Datenträger, der für die Lösung über einen genehmigten Hersteller und eine Modellnummer verfügt. |
| Veraltete Metadaten | Der Ersatzdatenträger wurde bereits verwendet und enthält unter Umständen Daten eines unbekannten Speichersystems. Der Datenträger wurde unter Quarantäne gestellt.        <br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Unbekannte Metadaten | Auf dem Laufwerk wurden unbekannte Metadaten gefunden. Dies bedeutet normalerweise, dass auf dem Laufwerk Metadaten eines anderen Pools vorhanden sind.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Medien mit Fehlern | Für das Laufwerk ist ein Fehler aufgetreten, und es wird von „Direkte Speicherplätze“ nicht mehr verwendet.<br> <br>**Aktion:** Tauschen Sie das Laufwerk so bald wie möglich aus, um die vollständige Resilienz sicherzustellen. |
| Hardwarefehler des Geräts | Auf diesem Laufwerk ist ein Hardwarefehler aufgetreten. <br> <br>**Aktion:** Tauschen Sie das Laufwerk so bald wie möglich aus, um die vollständige Resilienz sicherzustellen. |
| Firmware wird aktualisiert | Azure Stack aktualisiert die Firmware auf dem Laufwerk. Dies ist ein vorübergehender Zustand, der normalerweise weniger als eine Minute dauert. Während dieses Zeitraums werden alle Lese- und Schreibvorgänge von anderen Laufwerken im Pool verarbeitet.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack die Aktualisierung beendet, und prüfen Sie anschließend den Status. |
| Wird gestartet | Das Laufwerk wird für den Betrieb vorbereitet. Dies ist ein vorübergehender Zustand. Nach Abschluss des Vorgangs sollte das Laufwerk in einen anderen Betriebszustand übergehen.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack den Vorgang beendet, und prüfen Sie anschließend den Status. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Gründe, warum ein Laufwerk nicht in einen Pool aufgenommen werden kann

Einige Laufwerke sind noch nicht dafür bereit, in einen Azure Stack-Speicherpool aufgenommen zu werden. Sie können ermitteln, warum ein Laufwerk nicht für die Aufnahme in den Pool geeignet ist, indem Sie sich die CannotPoolReason-Eigenschaft des Laufwerks ansehen. Die folgende Tabelle enthält einige weitere Informationen zu den einzelnen Gründen.

| `Reason` | BESCHREIBUNG |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware nicht kompatibel | Das Laufwerk ist nicht in der Liste mit den genehmigten Speichermodellen enthalten, die über den Integritätsdienst angegeben wird.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Firmware nicht kompatibel | Die Firmware auf dem physischen Laufwerk ist nicht in der Liste mit den genehmigten Firmwarerevisionen enthalten, die über den Integritätsdienst erstellt wird.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Von Cluster verwendet | Das Laufwerk wird derzeit von einem Failovercluster verwendet.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Wechselmedien | Das Laufwerk ist als Wechseldatenträger klassifiziert. <br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Fehlerhaft | Das Laufwerk befindet sich nicht in einem fehlerfreien Zustand und muss unter Umständen ausgetauscht werden.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Unzureichende Kapazität | Es sind Partitionen vorhanden, die den freien Speicherplatz auf dem Laufwerk nutzen.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Überprüfung in Bearbeitung | Der Integritätsdienst überprüft, ob das Laufwerk oder die darauf enthaltene Firmware für die Nutzung genehmigt ist.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack den Prozess beendet, und prüfen Sie anschließend den Status. |
| Überprüfungsfehler | Der Integritätsdienst konnte nicht überprüfen, ob das Laufwerk oder die darauf enthaltene Firmware für die Nutzung genehmigt ist.<br> <br>**Aktion:** Wenden Sie sich an den Support. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
| Offline | Das Laufwerk ist offline. <br> <br>**Aktion:** Wenden Sie sich an den Support. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |

## <a name="next-step"></a>Nächster Schritt

[Verwalten der Speicherkapazität](azure-stack-manage-storage-shares.md) 
