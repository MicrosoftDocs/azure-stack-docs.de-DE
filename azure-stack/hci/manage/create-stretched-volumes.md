---
title: Erstellen von Volumes für Stretchingcluster und Einrichten der Replikation
description: Hier wird beschrieben, wie Sie mithilfe von Windows Admin Center und PowerShell Volumes in Azure Stack HCI erstellen und Replikation für Stretchingcluster einrichten.
author: v-dasis
ms.author: v-dasis
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 8c885f7a344ff9d34228bad76c79e65f1f345f25
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573682"
---
# <a name="create-stretched-cluster-volumes-and-set-up-replication"></a>Erstellen von Volumes für Stretchingcluster und Einrichten der Replikation

> Gilt für: Azure Stack HCI, Version 20H2

In diesem Artikel wird beschrieben, wie Sie mithilfe von Windows Admin Center und PowerShell in Azure Stack HCI Volumes für Stretchingcluster erstellen und die Replikation einrichten.

Als Beispiel werden in diesem Artikel Volumes auf vier Servern in zwei Standorten erstellt (zwei Server pro Standort). Denken Sie jedoch daran, dass Sie mindestens sechs Server (drei pro Standort) benötigen, falls Sie Volumes mit Drei-Wege-Spiegelung erstellen möchten.

## <a name="stretched-volumes-and-replication-using-windows-admin-center"></a>Stretchingvolumes und Replikation in Windows Admin Center

So erstellen Sie ein Volume und richten die Replikation ein

1. Wählen Sie in Windows Admin Center unter **Extras** die Option **Volumes** aus.
1. Wählen Sie im rechten Bereich die Registerkarte **Inventory** (Inventar) und dann **Create** (Erstellen) aus.
1. Wählen Sie im Bereich **Create volume** (Volume erstellen) **Replicate volume between sites** (Volume zwischen Standorten replizieren).
1. Wählen Sie im Dropdownfeld eine Replikationsrichtung zwischen den Standorten aus.
1. Wählen Sie unter **Replication mode** (Replikationsmodus) **Asynchronous** (Asynchron) oder **Synchronous** (Synchron) aus.
1. Geben Sie einen Namen für die Quellreplikationsgruppe und einen Namen für die Zielreplikationsgruppe ein.
1. Geben Sie die gewünschte Größe für das Protokollvolume ein.
1. Führen Sie unter **Erweitert** optional Folgendes aus:
     - Geben Sie den **Namen der Quellreplikationsgruppe** ein bzw. ändern Sie ihn.
     - Geben Sie den **Namen der Zielreplikationsgruppe** ein bzw. ändern Sie ihn.
     - Zum **Verwenden von Blöcken, für die im Ziel bereits Seeding ausgeführt wurde**... aktivieren Sie das entsprechende Kontrollkästchen.
     - Zum **Verschlüsseln von Replikationsverkehr** aktivieren Sie das entsprechende Kontrollkästchen.
     - Zum **Aktivieren von Konsistenzgruppen** aktivieren Sie das entsprechende Kontrollkästchen.
1. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.
1. Überprüfen Sie im rechten Bereich, ob ein Datenträger für Daten und ein Protokolldatenträger an Ihrem primären (aktiven) Standort erstellt wurden und ob die entsprechenden Datenträgerreplikate für Daten und Protokolle am sekundären (passiven) Standort erstellt wurden. Bei bidirektionaler Replikation sollten zwei Sätze von Daten- und Volumedatenträgern angezeigt werden.
1. Wählen Sie unter **Extras** die Option **Speicherreplikat** aus.
1. Überprüfen Sie im rechten Bereich unter **Partnerships** (Partnerschaften), ob die Replikationspartnerschaft erfolgreich erstellt wurde.

Anschließend sollten Sie die erfolgreiche Datenreplikation zwischen den Standorten überprüfen, bevor Sie VMs und andere Workloads bereitstellen. Weitere Informationen finden Sie im Abschnitt zum Überprüfen der Replikation unter [Überprüfen des Clusters](../deploy/validate.md).

## <a name="create-stretched-volumes-using-powershell"></a>Erstellen von Stretchingvolumes mit PowerShell

Die Volumeerstellung unterscheidet sich für Standardcluster an einem Einzelstandort gegenüber Stretchingclustern mit zwei Standorten. Jedoch verwenden Sie für beide Szenarien das Cmdlet `New-Volume`, um einen virtuellen Datenträger zu erstellen, ihn zu partitionieren und zu formatieren, ein Volume mit passendem Namen zu erstellen und es freigegebenen Clustervolumes (Cluster Shared Volumes, CSV) hinzuzufügen.

Das Erstellen von Volumes und virtuellen Datenträgern für Stretchingcluster ist etwas komplizierter als bei Clustern für Einzelstandorte. Stretchingcluster erfordern mindestens vier Volumes – zwei Datenvolumes und zwei Protokollvolumes, wobei an jedem Standort ein Paar aus Daten und Protokollvolume gehostet wird. Anschließend erstellen Sie für jeden Standort eine Replikationsgruppe und richten die Replikation zwischen ihnen ein. Die Ressourcengruppen müssen zwischen den Servern verschoben werden. Dazu wird das Cmdlet `Move-ClusterGroup` verwendet.

1. Zuerst verschieben Sie die Ressourcengruppe des Speicherpools `Available Storage` nach `Server1` in `Site1`. Dazu verwenden Sie das Cmdlet `Move-ClusterGroup`:

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
    ```

1. Danach erstellen Sie den ersten virtuellen Datenträger (`Disk1`) für `Server1` in `Site1`:

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Erstellen Sie einen zweiten virtuellen Datenträger (`Disk2`) für `Server1` in `Site1`:

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Jetzt schalten Sie die Gruppe `Available Storage` offline:

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

1. Sie verschieben die Gruppe `Available Storage` nach `Server3` in `Site2`:

    ```powershell
    Move-ClusterGroup -Name 'Available Storage' -Node Server3
    ```

1. Erstellen Sie den ersten virtuellen Datenträger (`Disk3`) auf `Server3` in `Site2`:

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. Erstellen Sie einen zweiten virtuellen Datenträger (`Disk4`) auf `Server3` in `Site2`:

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. Jetzt schalten Sie die Gruppe `Available Storage` offline und verschieben sie anschließend zurück auf einen der Server in `Site1`:

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
    ```

1. Stellen Sie mithilfe des Cmdlets `Get-ClusterResource` sicher, dass vier virtuelle Datenträgervolumes erstellt wurden, zwei in jedem Speicherpool:

    ```powershell
    Get-ClusterResource -Cluster ClusterS1
    ```

1. Fügen Sie den freigegebenen Clustervolumes jetzt `Disk1` hinzu:

    ```powershell
    Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
    ```

Damit ist das Erstellen der Volumes beendet, und Sie sind bereit, das Speicherreplikat für die Replikation einzurichten.

## <a name="set-up-replication-using-powershell"></a>Einrichten der Replikation mithilfe von PowerShell

Wenn Sie PowerShell verwenden, um Speicherreplikation für einen Stretchingcluster einzurichten, muss der Datenträger, der für die Quelldaten verwendet werden soll, als freigegebenes Clustervolume (Cluster Shared Volume, CSV) hinzugefügt werden. Alle anderen Datenträger müssen als nicht-CSV-Datenträger in der Gruppe „Available Storage“ (Verfügbarer Speicher) verbleiben. Diese Datenträger werden dann während des Erstellungsvorgangs der Speicherreplikation als freigegebene Clustervolumes hinzugefügt.

Im vorhergehenden Schritt wurden die virtuellen Datenträger mithilfe von Laufwerksbuchstaben hinzugefügt, um ihre Identifikation zu erleichtern. Speicherreplikation bewirkt eine Eins-zu-Eins-Replikation, d. h., ein einzelner Datenträger kann auf einen anderen einzelnen Datenträger repliziert werden.

### <a name="step-1-validate-the-topology-for-replication"></a>Schritt 1: Überprüfen der Topologie für die Replikation

Bevor Sie beginnen, sollten Sie das Cmdlet `Test-SRTopology` für einen längeren Zeitraum (z. B. mehrere Stunden) ausführen. Das Cmdlet `Test-SRTopology` überprüft eine potenzielle Replikationspartnerschaft und validiert den lokalen Host gegen den Zielserver oder remote zwischen Quell- und Zielservern.

Mit diesem Cmdlet wird Folgendes überprüft:

- Auf SMB kann über das Netzwerk zugegriffen werden, was bedeutet, dass die TCP-Ports 445 und 5445 bidirektional offen sind.
- Auf WS-MAN kann im Netzwerk über HTTP zugegriffen werden, was bedeutet, dass die TCP-Ports 5985 und 5986 offen sind.
- Es kann auf einen SR WMIv2-Anbieter zugegriffen werden, und dieser akzeptiert Anforderungen.
- Quell- und Zieldatenvolumes sind vorhanden und schreibfähig.
- Quell- und Zielprotokollvolumes mit NTFS-Formatierung oder ReFS-Formatierung sind vorhanden und weisen ausreichend freien Speicherplatz auf.
- Der Speicher ist im GPT-Format anstelle von MBR mit passenden Sektorgrößen initialisiert.
- Es ist genügend physischer Arbeitsspeicher zum Ausführen der Replikation verfügbar.

Darüber hinaus misst das `Test-SRTopology`-Cmdlet Folgendes:

- Roundtriplatenz von ICMP; der Durchschnittswert wird gemeldet.
- Leistungsindikatoren für Eingabe/Ausgabe-Schreibvorgänge; der Durchschnittswert für das jeweilige Volume wird gemeldet.
- Geschätzte Dauer der Erstsynchronisierung.

Nach dem Abschluss erstellt „Test-SRTopology“ eine HTML-Datei (TestSrTopologyReport mit Datum und Uhrzeit) in Ihrem Windows Temp-Ordner. Alle Warnungen oder Fehler sollten überprüft werden, da sie dazu führen können, dass die Speicherreplikation nicht ordnungsgemäß erstellt wird.

Hier sehen Sie einen Beispielbefehl mit einer Ausführungszeit von 5 Stunden:

```powershell
Test-SRTopology -SourceComputerName Server1 -SourceVolumeName W: -SourceLogVolumeName X: -DestinationComputerName Server3 -DestinationVolumeName Y: -DestinationLogVolumeName Z: -DurationInMinutes 300 -ResultPath c:\temp
```

### <a name="step-2-create-the-replication-partnership"></a>Schritt 2: Erstellen der Replikationspartnerschaft

Nachdem Sie jetzt die `Test-SRTopology`-Tests abgeschlossen haben, sind Sie bereit, die Speicherreplikation zu konfigurieren und die Replikationspartnerschaft zu erstellen. Kurz zusammengefasst konfigurieren wir die Speicherreplikation, indem Replikationsgruppen (RGs) für jeden Standort erstellt werden. Danach werden die Daten- und Protokollvolumes für die Quellserverknoten in Site1 (Server1, Server2) und die (replizierten) Zielserverknoten in Site2 (Server3, Server4) angegeben.

Fangen wir an:

1. Fügen Sie den Site1-Datenträger für Daten als freigegebenes Clustervolume (CSV) hinzu:

   ```powershell
   Add-ClusterSharedVolume -Name "Cluster Virtual Disk (Site1)"
   ```

1. Die Gruppe „Available Storage“ sollte im Besitz des Knotens sein, auf dem sie aktuell ausgeführt wird. Die Gruppe kann mit diesem Befehl auf Server1 verschoben werden:

   ```powershell
   Move-ClusterGroup -Name “Available Storage” -Node Server1
   ```

1. Verwenden Sie das Cmdlet `New-SRPartnership`, um die Replikationspartnerschaft zu erstellen. Dieses Cmdlet ist auch der Ort, an dem Sie die Namen von Quelldatenvolume und Protokollvolume angeben:

   ```powershell
   New-SRPartnership -SourceComputerName "Server1" -SourceRGName "Replication1" -SourceVolumeName "C:\ClusterStorage\Disk1\" -SourceLogVolumeName "G:" -DestinationComputerName "Server3" -DestinationRGName "Replication2" -DestinationVolumeName "H:" -DestinationLogVolumeName "I:"
   ```

Das Cmdlet `New-SRPartnership` erstellt eine Replikationspartnerschaft zwischen den zwei Replikationsgruppen für die zwei Standorte. In diesem Beispiel ist `Replication1` die Replikationsgruppe für den primären Knoten Server1 an Standort Site1, und `Replication2` ist die Replikationsgruppe für den Zielknoten Server3 an Standort Site2.

Die Speicherreplikation richtet jetzt alles ein. Wenn zu replizierende Daten vorhanden sind, führt sie die Replikation jetzt durch. Abhängig von der Menge der zu replizierenden Daten kann das eine Zeit lang dauern. Es empfiehlt sich, vor dem Abschluss dieses Vorgangs keine Gruppen zu verschieben.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Themen und andere Speicherverwaltungsaufgaben finden Sie unter:

- [Stretchingcluster: Übersicht](../concepts/stretched-clusters.md)
- [Planen von Volumes](../concepts/plan-volumes.md)
- [Erweitern von Volumes](extend-volumes.md)
- [Löschen von Volumes](delete-volumes.md)
