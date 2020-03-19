---
title: Fehlertoleranz und Speichereffizienz in Azure Stack HCI
description: Enthält eine Beschreibung der Resilienzoptionen in „Direkte Speicherplätze“, einschließlich Spiegelung und Parität.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 9ace3960b4c54461a4153c4997694e6d17ee4fd1
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79026116"
---
# <a name="fault-tolerance-and-storage-efficiency-in-azure-stack-hci"></a>Fehlertoleranz und Speichereffizienz in Azure Stack HCI

>Gilt für: Windows Server 2019

In diesem Thema werden die für „Direkte Speicherplätze“ verfügbaren Resilienzoptionen vorgestellt, und es werden jeweils Skalierungsanforderungen, Speichereffizienz und allgemeine Vor- und Nachteile beschrieben. Darüber hinaus werden einige Nutzungshinweise gegeben, um Ihnen den Einstieg zu erleichtern, und es wird auf hilfreiche Dokumente, Blogs und Ressourcen mit weiteren Informationen verwiesen.

Falls Sie mit „Direkte Speicherplätze“ bereits vertraut sind, können Sie ggf. direkt mit dem Abschnitt [Zusammenfassung](#summary) fortfahren.

## <a name="overview"></a>Übersicht

Im Kern geht es bei „Direkte Speicherplätze“ um die Erzielung von Fehlertoleranz für Ihre Daten (häufig als „Resilienz“ bezeichnet). Die Implementierung ähnelt RAID, aber sie ist auf Server verteilt und wird per Software implementiert.

Wie auch bei RAID gibt es für „Direkte Speicherplätze“ verschiedene Möglichkeiten, dies zu erreichen. Es handelt sich jeweils um unterschiedliche Kompromisse in Bezug auf Fehlertoleranz, Speichereffizienz und Computekomplexität. Hierbei gelten im Allgemeinen zwei Kategorien: „Spiegelung“ und „Parität“ (auch als „Erasure Coding“ bezeichnet).

## <a name="mirroring"></a>Spiegelung

Die Spiegelung ermöglicht Fehlertoleranz, indem mehrere Kopien aller Daten aufbewahrt werden. Dies ähnelt am ehesten RAID-1. Das Striping und die Anordnung dieser Daten ist nicht trivial (weitere Informationen in [diesem Blog](https://blogs.technet.microsoft.com/filecab/2016/11/21/deep-dive-pool-in-spaces-direct/)), aber es lässt sich festhalten, dass alle per Spiegelung gespeicherten Daten vollständig mehrfach geschrieben werden. Jede Kopie wird auf andere physische Hardware (unterschiedliche Laufwerke von nicht identischen Servern) geschrieben, für die angenommen wird, dass sie nicht gemeinsam ausfallen.

„Direkte Speicherplätze“ verfügt über zwei Spiegelungsarten: „Zwei-Wege“ und „Drei-Wege“.

### <a name="two-way-mirror"></a>Zwei-Wege-Spiegelung

Bei der Zwei-Wege-Spiegelung werden jeweils zwei Kopien aller Daten geschrieben. Die Speichereffizienz beträgt hierbei 50 Prozent. Zum Schreiben von 1 TB an Daten benötigen Sie mindestens 2 TB an physischer Speicherkapazität. Darüber hinaus benötigen Sie mindestens zwei [Hardwarefehlerdomänen](/windows-server/failover-clustering/fault-domains). Bei „Direkte Speicherplätze“ bedeutet dies, dass zwei Server verwendet werden.

![Zwei-Wege-Spiegelung](media/fault-tolerance/two-way-mirror-180px.png)

   >[!WARNING]
   > Wenn Sie über mehr als zwei Server verfügen, empfehlen wir Ihnen, stattdessen die Drei-Wege-Spiegelung zu nutzen.

### <a name="three-way-mirror"></a>Drei-Wege-Spiegelung

Bei der Drei-Wege-Spiegelung werden jeweils drei Kopien aller Daten geschrieben. Die Speichereffizienz beträgt hierbei 33,3 Prozent. Zum Schreiben von 1 TB an Daten benötigen Sie mindestens 3 TB an physischer Speicherkapazität. Darüber hinaus benötigen Sie mindestens drei Hardwarefehlerdomänen. Bei „Direkte Speicherplätze“ bedeutet dies, dass drei Server verwendet werden.

Bei der Drei-Wege-Spiegelung können mindestens [zwei gleichzeitige Hardwareprobleme (Laufwerk oder Server)](#examples) problemlos toleriert werden. Wenn Sie beispielsweise einen Server neu starten und plötzlich ein anderes Laufwerk bzw. ein Server ausfällt, bleiben alle Daten geschützt und sind ohne Unterbrechung zugänglich.

![Drei-Wege-Spiegelung](media/fault-tolerance/three-way-mirror-180px.png)

## <a name="parity"></a>Parität

Bei der Paritätscodierung (häufig als „Erasure Coding“ bezeichnet) wird die Fehlertoleranz per bitweiser Arithmetik bereitgestellt, und dies kann [erstaunlich kompliziert](https://www.microsoft.com/research/wp-content/uploads/2016/02/LRC12-cheng20webpage.pdf) sein. Die Funktionsweise ist hierbei weniger offensichtlich als bei der Spiegelung. Es sind viele hervorragende Onlineressourcen verfügbar (z. B. die [„Erasure Coding“-Anleitung für Anfänger](http://smahesh.com/blog/2012/07/01/dummies-guide-to-erasure-coding/) eines Drittanbieters), in denen die Grundlagen vermittelt werden. Kurz gesagt: Hierbei wird eine höhere Speichereffizienz ermöglicht, ohne dass dies zu Lasten der Fehlertoleranz geht.

Es gibt für „Direkte Speicherplätze“ zwei Arten von Parität: „Einzelparität“ und „Duale Parität“. Bei der dualen Parität wird bei größeren Umfängen ein erweitertes Verfahren mit dem Namen „Local Reconstruction Codes“ (Lokale Wiederherstellungscodes) genutzt.

> [!IMPORTANT]
> Wir empfehlen Ihnen für die meisten leistungsempfindlichen Workloads die Nutzung der Spiegelung. Weitere Informationen zum Abwägen zwischen Leistung und Kapazität je nach Workload finden Sie unter [Planen von Volumes](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

### <a name="single-parity"></a>Einzelparität
Bei Einzelparität wird nur ein Symbol für bitweise Parität beibehalten, sodass nur Fehlertoleranz für jeweils einen Fehler vorhanden ist. Dies ähnelt am ehesten RAID-5. Für die Nutzung der Einzelparität benötigen Sie mindestens drei Hardwarefehlerdomänen. Bei „Direkte Speicherplätze“ bedeutet dies, dass drei Server verwendet werden. Da die Drei-Wege-Spiegelung bei gleichem Umfang eine höhere Fehlertoleranz ermöglicht, raten wir von der Nutzung der Einzelparität ab. Diese Option steht Ihnen aber offen, falls Sie dies möchten, und die Nutzung wird vollständig unterstützt.

   >[!WARNING]
   > Wir raten von der Einzelparität ab, da nur jeweils ein Hardwarefehler problemlos toleriert werden kann: Wenn Sie einen Server neu starten und plötzlich ein anderes Laufwerk oder ein anderer Server ausfällt, kommt es zu Ausfallzeit. Falls Sie nur drei Server haben, empfehlen wir Ihnen, die Drei-Wege-Spiegelung zu verwenden. Bei vier oder mehr helfen Ihnen die Informationen im nächsten Abschnitt weiter.

### <a name="dual-parity"></a>Duale Parität

Bei der dualen Parität werden Reed-Solomon-Fehlerkorrekturcodes implementiert, um zwei Symbole für bitweise Parität beizubehalten. So wird die gleiche Fehlertoleranz wie bei der Drei-Wege-Spiegelung ermöglicht (also bis zu zwei Fehler auf einmal), aber es wird eine höhere Speichereffizienz erzielt. Dies ähnelt am ehesten RAID-6. Für die Nutzung der dualen Parität benötigen Sie mindestens vier Hardwarefehlerdomänen. Bei „Direkte Speicherplätze“ bedeutet dies, dass vier Server verwendet werden. Auf dieser Ebene beträgt die Speichereffizienz 50 %. Zum Speichern von 2 TB an Daten benötigen Sie 4 TB an physischer Speicherkapazität.

![Duale Parität](media/fault-tolerance/dual-parity-180px.png)

Die Speichereffizienz der dualen Parität steigert sich bei einer höheren Anzahl von Fehlerdomänen von 50 % auf bis zu 80 %. Bei einer Anzahl von sieben (bei „Direkte Speicherplätze“ sieben Server) erhöht sich die Effizienz auf 66,7 Prozent. Zum Speichern von 4 TB an Daten benötigen Sie lediglich 6 TB an physischer Speicherkapazität.

![Duale Parität (breit)](media/fault-tolerance/dual-parity-wide-180px.png)

Informationen zur Effizienz von dualer Parität und „Local Reconstruction Codes“ für jeden Umfang finden Sie im Abschnitt [Zusammenfassung](#summary).

### <a name="local-reconstruction-codes"></a>Local Reconstruction Codes

Für „Speicherplätze“ wird ein erweitertes Verfahren eingeführt, das von Microsoft Research entwickelt wurde und als „Local Reconstruction Codes“ (LRC) bezeichnet wird. Bei größerem Umfang wird LRC für die duale Parität genutzt, um die Codierung bzw. Decodierung in kleinere Gruppen zu unterteilen. So soll der Mehraufwand reduziert werden, der mit Schreibvorgängen oder der Wiederherstellung nach Fehlern verbunden ist.

Bei Festplattenlaufwerken (HDDs) beträgt die Gruppengröße vier Symbole, und bei Solid State Drives (SSDs) sind es sechs Symbole. Hier ist beispielsweise angegeben, wie das Layout mit Festplattenlaufwerken und zwölf Hardwarefehlerdomänen (zwölf Server) aussieht. Es sind zwei Gruppen mit vier Datensymbolen vorhanden. Es wird eine Speichereffizienz von 72,7 % erzielt.

![Local Reconstruction Codes](media/fault-tolerance/local-reconstruction-codes-180px.png)

Wir empfehlen Ihnen diese eingehende und dennoch gut lesbare Beschreibung zum [Umgang von Local Reconstruction Codes mit unterschiedlichen Fehlerszenarien und den Gründen für ihre Attraktivität](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/) von unserem Mitarbeiter [Claus Joergensen](https://twitter.com/clausjor).

## <a name="mirror-accelerated-parity"></a>Parität mit Beschleunigung per Spiegelung

Bei einem Volume von „Direkte Speicherplätze“ kann teilweise die Spiegelung und teilweise die Parität festgelegt werden. Schreibvorgänge landen zuerst im gespiegelten Teil und werden später dann allmählich in den Paritätsteil verschoben. Hierbei wird praktisch die [Spiegelung genutzt, um das Erasure Coding zu beschleunigen](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/).

Zum Mischen von Drei-Wege-Spiegelung und dualer Parität benötigen Sie mindestens vier Fehlerdomänen (also vier Server).

Die Speichereffizienz der Parität mit Beschleunigung per Spiegelung liegt zwischen den Ergebnissen, die Sie bei reiner Spiegelung und bei reiner Parität erzielen, und hängt von den gewählten Proportionen ab. Beispielsweise werden in der [Demo bei Minute 37 dieser Präsentation](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) verschiedene Mischungen veranschaulicht, mit denen mit zwölf Servern Effizienzen von 46 %, 54 % und 65 % erzielt werden.

> [!IMPORTANT]
> Wir empfehlen Ihnen für die meisten leistungsempfindlichen Workloads die Nutzung der Spiegelung. Weitere Informationen zum Abwägen zwischen Leistung und Kapazität je nach Workload finden Sie unter [Planen von Volumes](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

## <a name="summary"></a>Zusammenfassung

In diesem Abschnitt sind die in „Direkte Speicherplätze“ verfügbaren Resilienztypen, die minimalen Skalierungsanforderungen der einzelnen Typen, die tolerierte Anzahl von Ausfällen pro Typ und die entsprechende Speichereffizienz zusammengefasst.

### <a name="resiliency-types"></a>Resilienztypen

|    Resilienz          |    Fehlertoleranz       |    Speichereffizienz      |
|------------------------|----------------------------|----------------------------|
|    Zwei-Wege-Spiegelung      |    1                       |    50,0 %                   |
|    Drei-Wege-Spiegelung    |    2                       |    33,3 %                   |
|    Duale Parität         |    2                       |    50,0 % bis 80,0 %           |
|    Gemischt               |    2                       |    33,3 % bis 80,0 %           |

### <a name="minimum-scale-requirements"></a>Mindestanforderungen für die Skalierung

|    Resilienz          |    Mindestens erforderliche Fehlerdomänen   |
|------------------------|-------------------------------------|
|    Zwei-Wege-Spiegelung      |    2                                |
|    Drei-Wege-Spiegelung    |    3                                |
|    Duale Parität         |    4                                |
|    Gemischt               |    4                                |

   >[!TIP]
   > Wenn Sie keine [Chassis- oder Rack-Fehlertoleranz](/windows-server/failover-clustering/fault-domains) verwenden, bezieht sich die Anzahl von Fehlerdomänen auf die Anzahl von Servern. Die Anzahl von Laufwerken auf jedem Server wirkt sich nicht darauf aus, welche Resilienztypen Sie verwenden können, solange Sie die Mindestanforderungen für „Direkte Speicherplätze“ erfüllen.

### <a name="dual-parity-efficiency-for-hybrid-deployments"></a>Effizienz der dualen Parität für Hybridbereitstellungen

In dieser Tabelle ist die Speichereffizienz der dualen Parität und Local Reconstruction Codes für verschiedene Umfänge von Hybridbereitstellungen angegeben, die sowohl Festplattenlaufwerke (HDDs) als auch Solid State Drives (SSDs) enthalten.

|    Fehlerdomänen      |    Layout           |    Effizienz   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2+2           |    50,0 %        |
|    5                  |    RS 2+2           |    50,0 %        |
|    6                  |    RS 2+2           |    50,0 %        |
|    7                  |    RS 4+2           |    66,7 %        |
|    8                  |    RS 4+2           |    66,7 %        |
|    9                  |    RS 4+2           |    66,7 %        |
|    10                 |    RS 4+2           |    66,7 %        |
|    11                 |    RS 4+2           |    66,7 %        |
|    12                 |    LRC (8, 2, 1)    |    72,7 %        |
|    13                 |    LRC (8, 2, 1)    |    72,7 %        |
|    14                 |    LRC (8, 2, 1)    |    72,7 %        |
|    15                 |    LRC (8, 2, 1)    |    72,7 %        |
|    16                 |    LRC (8, 2, 1)    |    72,7 %        |

### <a name="dual-parity-efficiency-for-all-flash-deployments"></a>Effizienz der dualen Parität für reine Flash-Bereitstellungen

In dieser Tabelle ist die Speichereffizienz der dualen Parität und Local Reconstruction Codes für verschiedene Umfänge von reinen Flash-Bereitstellungen angegeben, die nur Solid State Drives (SSDs) enthalten. Für das Paritätslayout können größere Gruppen verwendet werden, und es kann eine bessere Speichereffizienz für eine reine Flash-Konfiguration erzielt werden.

|    Fehlerdomänen      |    Layout           |    Effizienz   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2+2           |    50,0 %        |
|    5                  |    RS 2+2           |    50,0 %        |
|    6                  |    RS 2+2           |    50,0 %        |
|    7                  |    RS 4+2           |    66,7 %        |
|    8                  |    RS 4+2           |    66,7 %        |
|    9                  |    RS 6+2           |    75,0 %        |
|    10                 |    RS 6+2           |    75,0 %        |
|    11                 |    RS 6+2           |    75,0 %        |
|    12                 |    RS 6+2           |    75,0 %        |
|    13                 |    RS 6+2           |    75,0 %        |
|    14                 |    RS 6+2           |    75,0 %        |
|    15                 |    RS 6+2           |    75,0 %        |
|    16                 |    LRC (12, 2, 1)   |    80,0 %        |

## <a name="examples"></a>Beispiele

Sofern Sie nicht nur über zwei Server verfügen, empfehlen wir Ihnen die Nutzung der Drei-Wege-Spiegelung bzw. der dualen Parität, weil diese Verfahren eine bessere Fehlertoleranz bieten. Vor allem wird dabei sichergestellt, dass alle Daten auch dann sicher und dauerhaft zugänglich bleiben, wenn zwei Fehlerdomänen – bei „Direkte Speicherplätze“ also zwei Server – von gleichzeitigen Ausfällen betroffen sind.

### <a name="examples-where-everything-stays-online"></a>Beispiele für den allgemeinen Erhalt des Onlinezustands

In diesen sechs Beispielen ist dargestellt, was bei der Drei-Wege-Spiegelung bzw. bei dualer Parität toleriert werden **kann**.

- **1.**    Ein Laufwerk ist ausgefallen (einschließlich der Cachelaufwerke)
- **2.**    Ein Server ist ausgefallen

![fault-tolerance-examples-1-and-2](media/fault-tolerance/Fault-Tolerance-Example-12.png)

- **3.**    Ein Server und ein Laufwerk sind ausgefallen
- **4.**    Zwei Laufwerke auf unterschiedlichen Servern sind ausgefallen

![fault-tolerance-examples-3-and-4](media/fault-tolerance/Fault-Tolerance-Example-34.png)

- **5.**    Mehr als zwei Laufwerke sind ausgefallen, sofern höchstens zwei Server betroffen sind
- **6.**    Zwei Server sind ausgefallen

![fault-tolerance-examples-5-and-6](media/fault-tolerance/Fault-Tolerance-Example-56.png)

...in allen Fällen bleiben alle Volumes im Onlinezustand. (Stellen Sie sicher, dass das Quorum für Ihren Cluster erhalten bleibt.)

### <a name="examples-where-everything-goes-offline"></a>Beispiele für einen allgemeinen Wechsel in den Offlinezustand

Während der Lebensdauer von „Speicherplätze“ kann das Feature eine beliebige Anzahl von Ausfällen tolerieren, weil nach jedem Ausfall die vollständige Resilienz wiederhergestellt wird – sofern dafür genügend Zeit ist. Es dürfen aber jeweils nur maximal zwei Fehlerdomänen von Ausfällen betroffen sein. Unten sind also Beispiele dafür aufgeführt, was bei der Drei-Wege-Spiegelung bzw. der dualen Parität **nicht** tolerierbar ist.

- **7.** Ausfall von Laufwerken auf drei oder mehr Servern gleichzeitig
- **8.** Ausfall von drei oder mehr Servern gleichzeitig

![fault-tolerance-examples-7-and-8](media/fault-tolerance/Fault-Tolerance-Example-78.png)

## <a name="usage"></a>Verwendung

Lesen Sie [Erstellen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/create-volumes).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den hier erwähnten Themen finden Sie in den folgenden Artikeln:

- [Erasure Coding in Azure von Microsoft Research](https://www.microsoft.com/research/publication/erasure-coding-in-windows-azure-storage/)
- [Local Reconstruction Codes und Beschleunigung von Paritätsvolumes](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)
- [Volumes in der Speicherverwaltungs-API](https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/)
- [Demo zur Speichereffizienz bei der Microsoft Ignite 2016](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)
- [Capacity Calculator (Vorschau) für „Direkte Speicherplätze“](https://aka.ms/s2dcalc)
