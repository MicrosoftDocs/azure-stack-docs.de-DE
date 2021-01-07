---
title: Verwalten der Speicherinfrastruktur für Azure Stack Hub
titleSuffix: Azure Stack
description: Erfahren Sie, wie Sie die Speicherinfrastruktur für Azure Stack Hub verwalten können. Machen Sie sich mit der Überwachung von Volumes und Laufwerken vertraut. Sehen Sie sich Tipps zur Problembehandlung beim Hinzufügen von Laufwerken zu Pools an.
author: PatAltimore
ms.topic: article
ms.date: 10/19/2020
ms.author: patricka
ms.lastreviewed: 5/4/2020
ms.reviewer: jiaha
ms.openlocfilehash: 122d1b6e2d2103387605c337bd68c31ec03f5d10
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870340"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>Verwalten der Speicherinfrastruktur für Azure Stack Hub
In diesem Artikel werden die Integrität und der Betriebszustand von Ressourcen der Azure Stack Hub-Speicherinfrastruktur beschrieben. Diese Ressourcen umfassen Speicherdatenträger und Volumes. Die Informationen in diesem Thema sind sehr hilfreich bei der Behandlung verschiedener Probleme, etwa wenn ein Laufwerk nicht einem Pool hinzugefügt werden kann.

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

In den folgenden Abschnitten sind die Informationen zur Integrität und zum Betriebszustand angegeben:

### <a name="volume-health-state-healthy"></a>Integritätszustand des Volumes: Healthy

| Betriebszustand | BESCHREIBUNG |
|---|---|
| OK | Das Volume ist fehlerfrei. |
| Suboptimal | Daten werden nicht gleichmäßig auf Laufwerke geschrieben.<br> <br>**Aktion:** Wenden Sie sich an den Support, um die Laufwerknutzung im Speicherpool zu optimieren. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. Sie müssen unter Umständen eine Wiederherstellung aus einer Sicherung durchführen, nachdem die fehlerhafte Verbindung wiederhergestellt wurde. |

### <a name="volume-health-state-warning"></a>Integritätszustand des Volumes: Warnung

Wenn sich das Volume im Integritätszustand „Warnung“ befindet, ist mindestens eine Kopie Ihrer Daten nicht verfügbar, aber Azure Stack Hub kann trotzdem mindestens eine Kopie Ihrer Daten lesen.

| Betriebszustand | BESCHREIBUNG |
|---|---|
| Aktiv | Azure Stack Hub repariert das Volume, z. B. nach dem Hinzufügen oder Entfernen eines Laufwerks. Nach Abschluss der Reparatur sollte das Volume in den Integritätszustand „OK“ zurückkehren.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub die Reparatur des Volumes abgeschlossen hat, und überprüfen Sie den Zustand anschließend. |
| Unvollständig | Die Resilienz des Volumes wird reduziert, weil mindestens ein Laufwerk ausgefallen ist oder fehlt. Die fehlenden Laufwerke enthalten aber aktuelle Kopien Ihrer Daten.<br> <br>**Aktion:** Stellen Sie die Verbindung für fehlende Laufwerke wieder her, tauschen Sie alle fehlerhaften Laufwerke aus, und versetzen Sie alle im Offlinezustand befindlichen Server wieder in den Onlinezustand. |
| Heruntergestuft | Die Resilienz des Volumes wird reduziert, weil mindestens ein Laufwerk ausgefallen ist oder fehlt und sich veraltete Kopien Ihrer Daten auf den Laufwerken befinden.<br> <br>**Aktion:** Stellen Sie die Verbindung für fehlende Laufwerke wieder her, tauschen Sie alle fehlerhaften Laufwerke aus, und versetzen Sie alle im Offlinezustand befindlichen Server wieder in den Onlinezustand. |

### <a name="volume-health-state-unhealthy"></a>Integritätszustand des Volumes: Fehlerhaft

Wenn sich ein Volume im Integritätszustand „Fehlerhaft“ befindet, kann auf einige oder alle Daten des Volumes derzeit nicht zugegriffen werden.

| Betriebszustand | BESCHREIBUNG |
|---|---|
| Keine Redundanz | Für das Volume ist es zu einem Datenverlust gekommen, weil zu viele Laufwerke ausgefallen sind.<br> <br>**Aktion:** Wenden Sie sich an den Support. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |

### <a name="volume-health-state-unknown"></a>Integritätszustand des Volumes: Unknown

Das Volume kann sich auch im Integritätszustand „Unbekannt“ befinden, falls der virtuelle Datenträger getrennt wurde.

| Betriebszustand | BESCHREIBUNG |
|---|---|
| Getrennt | Ein Speichergerätfehler ist aufgetreten, der dazu führt, dass das Volume nicht zugänglich ist. Unter Umständen gehen einige Daten verloren.<br> <br>**Aktion:** <br>1. Überprüfen Sie die physische Verbindung und Netzwerkverbindung aller Speichergeräte.<br>2. Wenden Sie sich an den Support, falls alle Geräte richtig verbunden sind. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. Sie müssen unter Umständen eine Wiederherstellung aus einer Sicherung durchführen, nachdem die fehlerhafte Verbindung wiederhergestellt wurde. |

## <a name="drive-states"></a>Laufwerkstatus

Verwenden Sie die folgenden PowerShell-Befehle, um den Zustand der Laufwerke zu überwachen:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

In den folgenden Abschnitten werden die Integritätszustände beschrieben, in denen sich ein Laufwerk befinden kann:

### <a name="drive-health-state-healthy"></a>Integritätszustand des Laufwerks: Healthy

| Betriebszustand | BESCHREIBUNG |
|---|---|
| OK | Das Volume ist fehlerfrei. |
| Aktiv | Das Laufwerk führt einige interne Housekeepingvorgänge durch. Nach Abschluss der Aktion sollte das Laufwerk in den Integritätszustand „OK“ zurückkehren. |

### <a name="drive-health-state-warning"></a>Integritätszustand des Laufwerks: Warnung

Ein Laufwerk im Zustand „Warnung“ kann Daten erfolgreich lesen und schreiben, aber es liegt ein Problem vor.

| Betriebszustand | BESCHREIBUNG |
|---|---|
| Verbindung unterbrochen | Die Konnektivität für das Laufwerk ist verloren gegangen.<br> <br>**Aktion:** Versetzen Sie alle Server wieder in den Onlinezustand. Schließen Sie das Laufwerk wieder an, falls das Problem hierdurch nicht behoben wurde. Tauschen Sie das Laufwerk aus, falls dieser Zustand weiterhin besteht, um die vollständige Resilienz sicherzustellen. |
| Vorhersehbarer Fehler | Es ist vorhersehbar, dass für das Laufwerk in Kürze ein Fehler auftritt.<br> <br>**Aktion:** Tauschen Sie das Laufwerk so bald wie möglich aus, um die vollständige Resilienz sicherzustellen. |
| E/A-Fehler | Beim Zugreifen auf das Laufwerk ist ein vorübergehender Fehler aufgetreten.<br> <br>**Aktion:** Tauschen Sie das Laufwerk aus, falls dieser Zustand weiterhin besteht, um die vollständige Resilienz sicherzustellen. |
| Vorübergehender Fehler | Für das Laufwerk ist ein vorübergehender Fehler aufgetreten. Normalerweise bedeutet dieser Fehler, dass das Laufwerk nicht reagiert hat. Es kann aber auch der Fall sein, dass die Schutzpartition von „Direkte Speicherplätze“ auf nicht korrekte Weise vom Laufwerk entfernt wurde. <br> <br>**Aktion:** Tauschen Sie das Laufwerk aus, falls dieser Zustand weiterhin besteht, um die vollständige Resilienz sicherzustellen. |
| Nicht ordnungsgemäße Wartezeit | Das Laufwerk reagiert manchmal nicht und weist Anzeigen für einen Ausfall auf.<br> <br>**Aktion:** Tauschen Sie das Laufwerk aus, falls dieser Zustand weiterhin besteht, um die vollständige Resilienz sicherzustellen. |
| Wird aus dem Pool entfernt | Azure Stack Hub entfernt das Laufwerk gerade aus seinem Speicherpool.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub die Entfernung des Laufwerks abgeschlossen hat, und überprüfen Sie den Status anschließend.<br>Wenden Sie sich an den Support, falls der Status beibehalten wird. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
| Wartungsmodus wird gestartet | Azure Stack Hub versetzt das Laufwerk gerade in den Wartungsmodus. Dies ist ein vorübergehender Zustand. Das Laufwerk sollte sich in Kürze im Zustand „Wartungsmodus“ befinden.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub den Prozess beendet, und prüfen Sie anschließend den Status. |
| Im Wartungsmodus | Das Laufwerk befindet sich im Wartungsmodus, und Lese- und Schreibvorgänge für das Laufwerk werden angehalten. Dies bedeutet normalerweise, dass das Laufwerk über Azure Stack Hub-Verwaltungsaufgaben, z. B. PNU oder FRU, betrieben wird. Aber auch der Administrator kann das Laufwerk in den Wartungsmodus versetzen.<br> <br>**Aktion:** Warten Sie, bis Azure Stack Hub die Verwaltungsaufgabe abgeschlossen hat, und prüfen Sie anschließend den Status.<br>Wenden Sie sich an den Support, falls der Status beibehalten wird. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
| Wartungsmodus wird beendet | Azure Stack Hub versetzt das Laufwerk gerade wieder in den Onlinezustand. Dies ist ein vorübergehender Zustand. Das Laufwerk sollte sich in Kürze in einem anderen Zustand befinden (idealerweise „Fehlerfrei“).<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub den Prozess beendet, und prüfen Sie anschließend den Status. |

### <a name="drive-health-state-unhealthy"></a>Integritätszustand des Laufwerks: Fehlerhaft

Auf ein Laufwerk im Zustand „Fehlerhaft“ kann derzeit nicht geschrieben werden, und der Zugriff darauf ist nicht möglich.

| Betriebszustand | BESCHREIBUNG |
|---|---|
| Split | Das Laufwerk wurde vom Pool getrennt.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Nicht verwendbar | Der physikalische Datenträger wurde unter Quarantäne gestellt, da er von Ihrem Lösungsanbieter nicht unterstützt wird. Es werden nur Datenträger unterstützt, die für die Lösung genehmigt wurden und über die richtige Datenträgerfirmware verfügen.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen Datenträger, der für die Lösung über einen genehmigten Hersteller und eine Modellnummer verfügt. |
| Veraltete Metadaten | Der Ersatzdatenträger wurde bereits verwendet und enthält unter Umständen Daten eines unbekannten Speichersystems. Der Datenträger wurde unter Quarantäne gestellt. <br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Unbekannte Metadaten | Auf dem Laufwerk wurden unbekannte Metadaten gefunden. Dies bedeutet normalerweise, dass auf dem Laufwerk Metadaten eines anderen Pools vorhanden sind.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Medien mit Fehlern | Für das Laufwerk ist ein Fehler aufgetreten, und es wird von „Direkte Speicherplätze“ nicht mehr verwendet.<br> <br>**Aktion:** Tauschen Sie das Laufwerk so bald wie möglich aus, um die vollständige Resilienz sicherzustellen. |
| Hardwarefehler des Geräts | Auf diesem Laufwerk ist ein Hardwarefehler aufgetreten. <br> <br>**Aktion:** Tauschen Sie das Laufwerk so bald wie möglich aus, um die vollständige Resilienz sicherzustellen. |
| Firmware wird aktualisiert | Azure Stack Hub aktualisiert die Firmware auf dem Laufwerk. Dies ist ein vorübergehender Zustand, der normalerweise weniger als eine Minute dauert. Während dieses Zeitraums werden alle Lese- und Schreibvorgänge von anderen Laufwerken im Pool verarbeitet.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub die Aktualisierung beendet, und prüfen Sie anschließend den Status. |
| Wird gestartet | Das Laufwerk wird für den Betrieb vorbereitet. Dies ist ein vorübergehender Zustand. Nach Abschluss des Vorgangs sollte das Laufwerk in einen anderen Betriebszustand übergehen.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub den Vorgang beendet, und prüfen Sie anschließend den Status. |

## <a name="reasons-a-drive-cant-be-pooled"></a>Gründe, warum ein Laufwerk nicht in einen Pool aufgenommen werden kann

Einige Laufwerke sind noch nicht dafür bereit, in einen Azure Stack Hub-Speicherpool aufgenommen zu werden. Sie können ermitteln, warum ein Laufwerk nicht für die Aufnahme in den Pool geeignet ist, indem Sie sich die `CannotPoolReason`-Eigenschaft des Laufwerks ansehen. Die folgende Tabelle enthält einige weitere Informationen zu den einzelnen Gründen.

| `Reason` | BESCHREIBUNG |
|---|---|
| Hardware nicht kompatibel | Das Laufwerk ist nicht in der Liste mit den genehmigten Speichermodellen enthalten, die über den Integritätsdienst angegeben wird.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Firmware nicht kompatibel | Die Firmware auf dem physischen Laufwerk ist nicht in der Liste mit den genehmigten Firmwarerevisionen enthalten, die über den Integritätsdienst erstellt wird.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Von Cluster verwendet | Das Laufwerk wird derzeit von einem Failovercluster verwendet.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Wechselmedien | Das Laufwerk ist als Wechseldatenträger klassifiziert. <br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Fehlerhaft | Das Laufwerk befindet sich nicht in einem fehlerfreien Zustand und muss unter Umständen ausgetauscht werden.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. |
| Unzureichende Kapazität | Es sind Partitionen vorhanden, die den freien Speicherplatz auf dem Laufwerk nutzen.<br> <br>**Aktion:** Ersetzen Sie das Laufwerk durch einen neuen Datenträger. Wenn Sie diesen Datenträger verwenden müssen, sollten Sie ihn aus dem System entfernen und sicherstellen, dass sich darauf keine benötigten Daten befinden, den Inhalt des Datenträgers löschen und ihn anschließend wieder einsetzen. |
| Überprüfung in Bearbeitung | Der Integritätsdienst überprüft, ob das Laufwerk oder die darauf enthaltene Firmware für die Nutzung genehmigt ist.<br> <br>**Aktion:** Warten Sie darauf, dass Azure Stack Hub den Prozess beendet, und prüfen Sie anschließend den Status. |
| Überprüfungsfehler | Der Integritätsdienst konnte nicht überprüfen, ob das Laufwerk oder die darauf enthaltene Firmware für die Nutzung genehmigt ist.<br> <br>**Aktion:** Wenden Sie sich an den Support. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
| Offline | Das Laufwerk ist offline. <br> <br>**Aktion:** Wenden Sie sich an den Support. Starten Sie vorher anhand der Anleitung unter https://aka.ms/azurestacklogfiles den Erfassungsprozess für Protokolldateien. |
