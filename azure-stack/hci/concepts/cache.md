---
title: Grundlegendes zum Cache in Azure Stack HCI
description: Es wird beschrieben, wie das Zwischenspeichern von Lese- und Schreibvorgängen in „Direkte Speicherplätze“ und Azure Stack HCI funktioniert.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: f1fc40a6475b8e51a063491cc120e2c4236cbeea
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79026098"
---
# <a name="understanding-the-cache-in-azure-stack-hci"></a>Grundlegendes zum Cache in Azure Stack HCI

>Gilt für: Windows Server 2019

[Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) verfügt über einen integrierten serverseitigen Cache, um die Speicherleistung zu erhöhen. Es handelt sich um einen großen, persistenten Echtzeitcache für Lese- *und* Schreibvorgänge. Der Cache wird automatisch konfiguriert, wenn „Direkte Speicherplätze“ aktiviert wird. In den meisten Fällen ist keine manuelle Verwaltung erforderlich.
Die Funktionsweise des Caches hängt davon ab, welche Laufwerkstypen vorhanden sind.

Im folgenden Video wird ausführlich veranschaulicht, wie die Zwischenspeicherung für „Direkte Speicherplätze“ funktioniert, und es wird auch auf andere Entwurfsaspekte eingegangen.

<strong>Entwurfsaspekte für „Direkte Speicherplätze“</strong><br>(20 Minuten)<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>Laufwerkstypen und Bereitstellungsoptionen

Für „Direkte Speicherplätze“ können derzeit drei Arten von Speichergeräten verwendet werden:

<table>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/NVMe-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            NVMe (Non-Volatile Memory Express)
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/SSD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            SATA/SAS SSD (Solid State Drive)
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/HDD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            HDD (Hard Disk Drive)
        </td>
    </tr>
</table>

Es gibt sechs Möglichkeiten, diese Speichergeräte zu kombinieren. Wir nutzen zwei Kategorien für die Gruppierung: „nur Flash“ und „Hybrid“.

### <a name="all-flash-deployment-possibilities"></a>Optionen bei Nur-Flash-Bereitstellungen

Bei Nur-Flash-Bereitstellungen wird die Speicherleistung maximiert, und es sind keine rotierenden Festplattenlaufwerke (HDD) vorhanden.

![Optionen bei Nur-Flash-Bereitstellungen](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>Optionen bei Hybridbereitstellungen

Bei Hybridbereitstellungen soll eine Balance zwischen Leistung und Kapazität erzielt oder die Kapazität maximiert werden, und es werden rotierende Festplattenlaufwerke (HDD) verwendet.

![Optionen bei Hybridbereitstellungen](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>Cachelaufwerke werden automatisch ausgewählt

Bei Bereitstellungen mit mehreren Laufwerkstypen werden von „Direkte Speicherplätze“ automatisch alle „schnellsten“ Laufwerke für die Zwischenspeicherung genutzt. Alle weiteren Datenträger werden zur Bereitstellung der Kapazität verwendet.

Welche Laufwerke am schnellsten sind, wird anhand der folgenden Hierarchie ermittelt.

![Laufwerkstyphierarchie](media/cache/Drive-Type-Hierarchy.png)

Wenn Sie beispielsweise über NVMe-Laufwerke und SSDs verfügen, wird auf den NVMe-Laufwerken die Zwischenspeicherung für die SSDs durchgeführt.

Wenn Sie über SSDs und HDDs verfügen, übernehmen die SSDs die Zwischenspeicherung für die HDDs.

   >[!NOTE]
   > Cachelaufwerke tragen keine nutzbare Speicherkapazität bei. Alle im Cache gespeicherten Daten sind auch an anderen Orten gespeichert. Dies ist spätestens nach dem De-Staging der Fall. Dies bedeutet, dass sich die gesamte Rohspeicherkapazität Ihrer Bereitstellung nur aus der Summe Ihrer Kapazitätslaufwerke ergibt.

Wenn alle Laufwerke denselben Typ haben, wird nicht automatisch ein Cache konfiguriert. Sie haben die Möglichkeit, Laufwerke mit längerer Lebensdauer manuell so zu konfigurieren, dass sie die Zwischenspeicherung für Laufwerke desselben Typs mit kürzerer Lebensdauer übernehmen. Dies ist im Abschnitt [Manuelle Konfiguration](#manual-configuration) beschrieben.

   >[!TIP]
   > Bei Bereitstellungen ausschließlich mit Laufwerken vom Typ NVMe oder SSD kann die Speichereffizienz deutlich erhöht werden, wenn keine Laufwerke für Cacheaufgaben genutzt werden müssen. Dies gilt vor allem für Bereitstellungen mit sehr kleinem Umfang.

## <a name="cache-behavior-is-set-automatically"></a>Cacheverhalten wird automatisch festgelegt

Das Verhalten des Caches wird automatisch anhand des Typs von Laufwerken ermittelt, für die die Zwischenspeicherung durchgeführt wird. Bei der Zwischenspeicherung für SSD-Datenträger (z. B. NVMe-Zwischenspeicherung für SSDs) werden nur Schreibvorgänge zwischengespeichert. Bei der Zwischenspeicherung für Festplattenlaufwerke (z. B. auf SSDs für HDDs) werden sowohl Lese- als auch Schreibvorgänge zwischengespeichert.

![Cacheverhalten für Lese-/Schreibvorgänge](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>Lesegeschützte Zwischenspeicherung für reine Flash-Bereitstellungen

Bei der Zwischenspeicherung für SSD-Laufwerke (NVMe oder SSDs) werden nur Schreibvorgänge zwischengespeichert. So wird die Abnutzung der Kapazitätslaufwerke reduziert, weil viele erstmalig bzw. erneut durchgeführte Schreibvorgänge im Cache zusammengefasst werden, für die das De-Staging dann nur nach Bedarf erfolgt. Dies führt zu einer Verringerung des kumulativen Datenverkehrs für die Kapazitätslaufwerke und zu einer Verlängerung der Lebensdauer. Aus diesem Grund empfehlen wir Ihnen, für den Cache Laufwerke mit [längerer Lebensdauer und Schreibvorgangoptimierung](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day) auszuwählen. Die Kapazitätslaufwerke verfügen in Bezug auf Schreibvorgänge ggf. über eine kürzere Lebensdauer.

Da Lesevorgänge keine signifikante Auswirkung auf die Lebensdauer des Flashspeichers haben und Solid State Drives generell eine niedrigere Leselatenz aufweisen, werden Lesevorgänge nicht zwischengespeichert, sondern direkt von den Kapazitätslaufwerken bereitgestellt (sofern die Daten nicht so neu sind, dass das De-Staging noch nicht durchgeführt wurde). Der Cache kann in diesem Fall ausschließlich für Schreibvorgänge genutzt werden, sodass sich die Effektivität erhöht.

Dies führt dazu, dass Merkmale der Schreibvorgänge, z. B. die Schreiblatenz, von den Cachelaufwerken vorgegeben werden, während die Merkmale der Lesevorgänge von den Kapazitätslaufwerken vorgegeben werden. Beide sind jeweils konsistent, vorhersagbar und einheitlich.

### <a name="readwrite-caching-for-hybrid-deployments"></a>Zwischenspeicherung von Lese-/Schreibvorgängen für Hybridbereitstellungen

Beim Zwischenspeichern für Festplattenlaufwerke (HDDs) werden *sowohl Lese- als auch Schreibvorgänge* zwischengespeichert, um in beiden Fällen Latenzwerte wie bei Flashspeicher zu erzielen (häufig ca. um den Faktor 10 verbessert). Im Lesecache werden Daten gespeichert, die vor kurzer Zeit und häufig gelesen werden, um den Zugriff zu beschleunigen und zufälligen Datenverkehr für die HDDs möglichst gering zu halten. (Aufgrund von Such- und Rotationsverzögerungen sind die Latenz und die Verlustzeit aufgrund des zufälligen Zugriffs auf eine HDD nicht zu unterschätzen.) Schreibvorgänge werden zwischengespeichert, um Bursts aufzufangen und – wie oben bereits geschildert – erstmalig und erneut durchgeführte Schreibvorgänge zusammenzufassen und den kumulativen Datenverkehr für die Kapazitätslaufwerke zu verringern.

Von „Direkte Speicherplätze“ wird ein Algorithmus implementiert, mit dem die Zufälligkeit von Schreibvorgängen vor dem De-Staging beseitigt wird. Für Datenträger wird ein E/A-Muster emuliert, dass sequenziell erscheint, obwohl der eigentliche E/A-Ablauf der Workload (z. B. virtuelle Computer) zufälliger Art ist. Auf diese Weise werden die IOPS-Rate und der Durchsatz für die HDDs erhöht.

### <a name="caching-in-deployments-with-drives-of-all-three-types"></a>Zwischenspeicherung bei Bereitstellungen mit allen drei Laufwerkstypen

Wenn Laufwerke aller drei Typen vorhanden sind, übernehmen die NVMe-Laufwerke die Zwischenspeicherung sowohl für die SSDs als auch für die HDDs. Das Verhalten ist hierbei wie oben beschrieben: für die SSDs werden nur Schreibvorgänge zwischengespeichert, und für die HDDs sowohl Lese- als auch Schreibvorgänge. Die Last der Zwischenspeicherung für die HDDs ist gleichmäßig auf die Cachelaufwerke verteilt.

## <a name="summary"></a>Zusammenfassung

In dieser Tabelle ist zusammengefasst, welche Laufwerke für die Zwischenspeicherung und welche für die Kapazität verwendet werden und wie das Cacheverhalten für die einzelnen Bereitstellungsoptionen aussieht.

| Bereitstellung     | Cachelaufwerke                        | Kapazitätslaufwerke | Cacheverhalten (Standard)  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| Nur NVMe         | Keine (optional: manuelle Konfiguration) | NVMe            | Lesegeschützt (falls konfiguriert)  |
| Nur SSD          | Keine (optional: manuelle Konfiguration) | SSD             | Lesegeschützt (falls konfiguriert)  |
| NVMe und SSD       | NVMe                                | SSD             | Lesegeschützt                  |
| NVMe und HDD       | NVMe                                | Festplattenlaufwerk             | Lesen und Schreiben                |
| SSD und HDD        | SSD                                 | Festplattenlaufwerk             | Lesen und Schreiben                |
| NVMe und SSD und HDD | NVMe                                | SSD und HDD       | Lesen und Schreiben für HDD, Lesegeschützt für SSD  |

## <a name="server-side-architecture"></a>Serverseitige Architektur

Der Cache wird auf Laufwerksebene implementiert: Einzelne Cachelaufwerke auf einem Server sind an ein oder mehrere Kapazitätslaufwerke auf demselben Server gebunden.

Da sich der Cache unterhalb der restlichen Elemente des softwaredefinierten Speicherstapels von Windows befindet, verfügt er nicht über ein „Konzeptbewusstsein“, z. B. in Bezug auf Speicherplätze oder Fehlertoleranz, und benötigt dies auch nicht. Sie können sich dies wie die Erstellung von „Hybridlaufwerken“ (teilweise Flash, teilweise Datenträger) vorstellen, die dann für Windows bereitgestellt werden. Wie auch bei einem echten Hybridlaufwerk ist die Echtzeitverschiebung von heißen und kalten Daten zwischen den schnelleren und langsameren Teilen der physischen Medien von außen nahezu unsichtbar.

Da die Resilienz bei „Direkte Speicherplätze“ mindestens auf der Serverebene angeordnet ist (Datenkopien werden immer auf andere Server geschrieben, und es wird maximal eine Kopie pro Server erstellt), profitieren Daten im Cache von der gleichen Resilienz wie Daten außerhalb des Caches.

![Serverseitige Cachearchitektur](media/cache/Cache-Server-Side-Architecture.png)

Bei Verwendung der Drei-Wege-Spiegelung werden drei Kopien aller Daten auf unterschiedliche Server geschrieben, auf denen sie dann im Cache angeordnet werden. Unabhängig davon, ob dafür später das De-Staging durchgeführt wird, sind immer drei Kopien vorhanden.

## <a name="drive-bindings-are-dynamic"></a>Laufwerkbindungen sind dynamisch

Die Bindung zwischen Cache- und Kapazitätslaufwerken kann ein beliebiges Verhältnis von 1:1 bis 1:12 oder höher aufweisen. Dieses Verhältnis wird jeweils dynamisch angepasst, wenn Laufwerke hinzugefügt oder entfernt werden, z. B. beim zentralen Hochskalieren oder nach Ausfällen. Dies bedeutet, dass Sie Cache- oder Kapazitätslaufwerke jederzeit einzeln hinzufügen können.

![Dynamische Bindung](media/cache/Dynamic-Binding.gif)

Wir empfehlen Ihnen, aus Symmetriegründen als Anzahl von Kapazitätslaufwerken ein Vielfaches der Anzahl von Cachelaufwerken zu wählen. Wenn Sie beispielsweise vier Cachelaufwerke verwenden, erhalten Sie eine gleichmäßigere Leistung, wenn Sie acht Kapazitätslaufwerke (Verhältnis 1:2) nutzen, und nicht sieben oder neun.

## <a name="handling-cache-drive-failures"></a>Vorgehensweise bei Ausfällen von Cachelaufwerken

Wenn ein Cachelaufwerk ausfällt, gehen alle Schreibvorgänge, für die bisher noch kein De-Staging durchgeführt wurde, für den *lokalen Server* verloren. Dies bedeutet also, dass sie nur noch in den anderen Kopien (auf anderen Servern) vorhanden sind. Wie nach anderen Laufwerksausfällen auch, ist für Speicherplätze eine automatische Wiederherstellung möglich und wird auch durchgeführt, indem die intakten Kopien herangezogen werden.

Für kurze Zeit werden die Kapazitätslaufwerke, die an das ausgefallene Cachelaufwerk gebunden waren, als fehlerhaft angezeigt. Nachdem die Cachebindung wiederhergestellt (automatisch) und die Reparatur der Daten durchgeführt wurde (automatisch), werden sie wieder als fehlerfrei angezeigt.

Dieses Szenario ist der Grund, warum mindestens zwei Cachelaufwerke pro Server benötigt werden, um die Leistung aufrechtzuerhalten.

![Vorgehensweise bei Ausfällen](media/cache/Handling-Failure.gif)

Sie können das Cachelaufwerk dann genau wie alle anderen Laufwerke austauschen.

   >[!NOTE]
   > Unter Umständen müssen Sie den Computer ausschalten, um ein NVMe-Laufwerk sicher austauschen zu können, bei dem es sich um eine Add-In-Karte (AIC) oder ein Gerät mit M.2-Formfaktor handelt.

## <a name="relationship-to-other-caches"></a>Beziehung zu anderen Caches

Der softwaredefinierte Speicherstapel von Windows enthält noch mehrere andere Caches. Beispiele hierfür sind der Speicherplätze-Zurückschreibcache und der Cache für In-Memory-Lesevorgänge des freigegebenen Clustervolumes (Cluster Shared Volume, CSV).

Für „Direkte Speicherplätze“ sollte das Standardverhalten des Speicherplätze-Zurückschreibcaches nicht geändert werden. Beispielsweise sollten Parameter wie **-WriteCacheSize** im Cmdlet **New-Volume** nicht verwendet werden.

Ob Sie den CSV-Cache nutzen, ist allein Ihre Entscheidung. In „Direkte Speicherplätze“ ist er standardmäßig deaktiviert, aber es kommt mit dem neuen Cache, der in diesem Thema beschrieben wird, nicht zu einem Konflikt. In bestimmten Szenarien können hiermit wertvolle Leistungssteigerungen erzielt werden. Weitere Informationen finden Sie unter [Aktivieren des CSV-Caches](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional).

## <a name="manual-configuration"></a>Manuelle Konfiguration

Für die meisten Bereitstellungen ist die manuelle Konfiguration nicht erforderlich. Falls Sie sie durchführen möchten, helfen Ihnen die Informationen in den folgenden Abschnitten weiter. 

Wenn Sie nach dem Setup Änderungen am Cachegerätemodell vornehmen müssen, können Sie das Dokument mit den Supportkomponenten des Integritätsdiensts bearbeiten. Dies ist in der [Übersicht über den Integritätsdienst](/windows-server/failover-clustering/health-service-overview#supported-components-document) beschrieben.

### <a name="specify-cache-drive-model"></a>Angeben des Cachelaufwerk-Modells

Bei Bereitstellungen, bei denen alle Laufwerke den gleichen Typ haben, z. B. nur NVMe oder SSD, wird kein Cache konfiguriert. Dies liegt daran, dass Windows Merkmale wie die Lebensdauer für Schreibvorgänge bei Laufwerken desselben Typs nicht automatisch unterscheiden kann.

Wenn Laufwerke mit längerer Lebensdauer die Zwischenspeicherung für Laufwerke desselben Typs mit kürzerer Lebensdauer übernehmen sollen, können Sie mit dem Parameter **-CacheDeviceModel** des Cmdlets **Enable-ClusterS2D** angeben, welches Laufwerkmodell verwendet werden soll. Nachdem „Direkte Speicherplätze“ aktiviert wurde, werden alle Laufwerke dieses Modells für die Zwischenspeicherung genutzt.

   >[!TIP]
   > Achten Sie darauf, dass Sie die Modellzeichenfolge exakt so angeben, wie sie in der Ausgabe von **Get-PhysicalDisk** enthalten ist.

####  <a name="example"></a>Beispiel

Erstellen Sie zuerst eine Liste mit den physischen Datenträgern:

```PowerShell
Get-PhysicalDisk | Group Model -NoElement
```

Hier ist eine Beispielausgabe angegeben:

```
Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

Geben Sie anschließend den folgenden Befehl ein, und geben Sie dabei das Modell des Cachegeräts an:

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

Sie können überprüfen, ob die gewünschten Laufwerke für die Zwischenspeicherung genutzt werden, indem Sie **Get-PhysicalDisk** in PowerShell ausführen und nachsehen, ob für die jeweilige **Usage**-Eigenschaft **„Journal“** angegeben ist.

### <a name="manual-deployment-possibilities"></a>Optionen bei manuellen Bereitstellungen

Bei der manuellen Konfiguration haben Sie die folgenden Bereitstellungsoptionen:

![Ungewöhnliche Bereitstellungsoptionen](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>Festlegen des Cacheverhaltens

Es ist möglich, das Standardverhalten des Caches außer Kraft zu setzen. Beispielsweise können Sie auch bei einer reinen Flash-Bereitstellung festlegen, dass Lesevorgänge zwischengespeichert werden sollen. Wir raten Ihnen von einer Änderung des Verhaltens ab, sofern Sie nicht sicher sind, dass die Standardeinstellung für Ihre Workload nicht geeignet ist.

Verwenden Sie für die Außerkraftsetzung des Verhaltens das Cmdlet **Set-ClusterStorageSpacesDirect** und die zugehörigen Parameter **-CacheModeSSD** und **-CacheModeHDD**. Mit dem Parameter **CacheModeSSD** wird das Cacheverhalten bei der Zwischenspeicherung für Solid State Drives festgelegt. Mit dem Parameter **CacheModeHDD** wird das Cacheverhalten bei der Zwischenspeicherung für Festplattenlaufwerke festgelegt. Dies kann zu einem beliebigen Zeitpunkt nach dem Aktivieren von „Direkte Speicherplätze“ erfolgen.

Sie können **Get-ClusterStorageSpacesDirect** verwenden, um zu überprüfen, ob das Verhalten festgelegt wurde.

#### <a name="example"></a>Beispiel

Rufen Sie zuerst die Einstellungen für „Direkte Speicherplätze“ ab:

```PowerShell
Get-ClusterStorageSpacesDirect
```

Hier ist eine Beispielausgabe angegeben:

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

Gehen Sie nun wie folgt vor:

```PowerShell
Set-ClusterStorageSpacesDirect -CacheModeSSD ReadWrite

Get-ClusterS2D
```

Hier ist eine Beispielausgabe angegeben:

```
CacheModeHDD : ReadWrite
CacheModeSSD : ReadWrite
```

## <a name="sizing-the-cache"></a>Anpassen der Cachegröße

Die Größe des Caches sollte so ausgelegt sein, dass der Arbeitssatz (die Daten, die aktiv gelesen oder geschrieben werden) Ihrer Anwendungen und Workloads abgedeckt ist.

Dies ist insbesondere bei Hybridbereitstellungen mit Festplattenlaufwerken wichtig. Wenn der aktive Arbeitssatz die Größe des Caches überschreitet oder sich zu schnell verändert, kommt es vermehrt zu Lesecachefehlern, und das De-Staging der Schreibvorgänge muss aggressiver erfolgen. Dies wirkt sich negativ auf die Gesamtleistung aus.

Sie können das integrierte Hilfsprogramm „Leistungsmonitor“ (PerfMon.exe) unter Windows verwenden, um die Rate an Cachefehlern zu untersuchen. Sie können insbesondere den Wert für **Cache Miss Reads/sec** (Cachefehler für Lesevorgänge pro Sek.) des Indikatorensatzes **Cluster Storage Hybrid Disk** (Clusterspeicher-Hybriddatenträger) mit dem IOPS-Gesamtwert für Lesevorgänge Ihrer Bereitstellung vergleichen. Jeder „Hybriddatenträger“ entspricht einem Kapazitätslaufwerk.

Beispielsweise führen zwei Cachelaufwerke, die an vier Kapazitätslaufwerke gebunden sind, zu vier „Hybriddatenträger“-Objektinstanzen pro Server.

![Leistungsmonitor](media/cache/PerfMon.png)

Es gibt keine allgemeingültige Regel, aber wenn es für zu viele Lesevorgänge zu Cachefehlern kommt, ist dieser ggf. nicht groß genug. In diesem Fall sollten Sie erwägen, Cachelaufwerke hinzuzufügen, um Ihren Cache zu erweitern. Sie können Cache- oder Kapazitätslaufwerke jederzeit einzeln hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Speicher finden Sie unter:

- [Fehlertoleranz und Speichereffizienz](fault-tolerance.md)
- [Quorum für Cluster und Pools](quorum.md)
