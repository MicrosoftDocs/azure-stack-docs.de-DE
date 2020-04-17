---
title: Grundlegendes zum Cluster- und Poolquorum in Azure Stack HCI
description: Hier finden Sie Informationen zum Cluster- und Poolquorum in „Direkte Speicherplätze“ in Azure Stack HCI mit spezifischen Beispielen zu den Feinheiten.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 70f10bd8c2c2e5eb639229ba743090ba5e5ac79c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79026104"
---
# <a name="understanding-cluster-and-pool-quorum-on-azure-stack-hci"></a>Grundlegendes zum Cluster- und Poolquorum in Azure Stack HCI

>Gilt für: Windows Server 2019

[Windows Server-Failoverclustering](/windows-server/failover-clustering/failover-clustering-overview) bietet Hochverfügbarkeit für Workloads. Diese Ressourcen gelten als hochverfügbar, wenn die Knoten, die Ressourcen hosten, online sind. In der Regel erfordert der Cluster jedoch, dass mehr als die Hälfte der Knoten ausgeführt wird. Dies wird als *Quorum* bezeichnet.

Das Quorum soll *Split-Brain*-Szenarien verhindern, die auftreten können, wenn eine Partition im Netzwerk vorhanden ist und Teilmengen bzw. Gruppen von Knoten nicht miteinander kommunizieren können. In diesem Fall versuchen möglicherweise beide Teilmengen von Knoten, den Besitz der Workload zu übernehmen und auf denselben Datenträger zu schreiben, was zu zahlreichen Problemen führen kann. Das Quorumkonzept des Failoverclusterings verhindert dies, indem es erzwingt, dass nur eine dieser Knotengruppen weiter ausgeführt wird und so nur eine der Gruppen online bleibt.

Das Quorum bestimmt, wie viele Ausfälle der Cluster überstehen und dabei online bleiben kann. Das Quorum ist für das Szenario gedacht, in dem ein Problem mit der Kommunikation zwischen Teilmengen von Clusterknoten vorliegt. Es verhindert, dass mehrere Server gleichzeitig versuchen, eine Ressourcengruppe zu hosten und auf denselben Datenträger zu schreiben. Aufgrund dieses Quorumkonzepts erzwingt der Cluster die Beendigung des Clusterdiensts in einer der Teilmengen von Knoten, um sicherzustellen, dass eine einzelne Ressourcengruppe nur einen „echten“ Besitzer hat. Wenn die beendeten Knoten wieder mit der Hauptknotengruppe kommunizieren können, treten sie dem Cluster automatisch erneut bei und starten ihren Clusterdienst.

In Windows Server 2019 verfügen zwei Systemkomponenten über eigene Quorummechanismen:

- **Clusterquorum**: Dieses Quorum wird auf der Clusterebene angewendet (d. h. der Cluster bleibt auch bei einem Ausfall von Knoten verfügbar).
- **Poolquorum**: Dieses Quorum wird auf der Poolebene angewendet, wenn „Direkte Speicherplätze“ aktiviert ist (d. h. der Pool bleibt auch bei einem Ausfall von Knoten und Laufwerken verfügbar). Speicherpools sind zur Verwendung in Szenarien mit und ohne Cluster konzipiert und haben daher einen anderen Quorummechanismus.

## <a name="cluster-quorum-overview"></a>Übersicht über das Clusterquorum

In der folgenden Tabelle finden Sie eine Übersicht über die Ergebnisse des Clusterquorums für verschiedene Szenarien:

| Serverknoten | Übersteht einen Serverknotenausfall | Übersteht zwei nacheinander auftretende Serverknotenausfälle | Übersteht zwei gleichzeitige Serverknotenausfälle |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | 50/50                               | Nein                                                | Nein                                                 |
| 2 + Zeuge  | Ja                                 | Nein                                                | Nein                                                 |
| 3            | Ja                                 | 50/50                                             | Nein                                                 |
| 3 + Zeuge  | Ja                                 | Ja                                               | Nein                                                 |
| 4            | Ja                                 | Ja                                               | 50/50                                              |
| 4 + Zeuge  | Ja                                 | Ja                                               | Ja                                                |
| 5 und mehr  | Ja                                 | Ja                                               | Ja                                                |

### <a name="cluster-quorum-recommendations"></a>Empfehlungen für das Clusterquorum

- Wenn Sie zwei Knoten haben, ist ein Zeuge **erforderlich**.
- Wenn Sie drei oder vier Knoten haben, wird ein Zeuge **ausdrücklich empfohlen**.
- Verwenden Sie einen **[Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness)** , wenn Sie über Internetzugriff verfügen.
- Verwenden Sie im Fall einer IT-Umgebung mit anderen Computern und Dateifreigaben einen Dateifreigabezeugen.

## <a name="how-cluster-quorum-works"></a>Funktionsweise des Clusterquorums

Wenn Knoten ausfallen oder bei einer Teilmenge von Knoten die Verbindung mit einer anderen Teilmenge unterbrochen wird, müssen die verbleibenden Knoten nachweisen, dass sie die *Mehrheit* des Clusters darstellen, damit sie online bleiben. Können sie dies nicht, werden sie offline geschaltet.

Das Konzept der *Mehrheit* funktioniert jedoch nur, wenn die Gesamtanzahl von Knoten im Cluster ungerade ist (z. B. drei Knoten in einem Cluster mit fünf Knoten). Was geschieht also bei Clustern mit einer geraden Anzahl von Knoten (z. B. vier Knoten)?

Es gibt zwei Möglichkeiten, wie der Cluster für eine ungerade *Gesamtanzahl von Stimmen* sorgen kann:

1. Die Anzahl von Stimmen kann durch Hinzufügen eines *Zeugen* mit einer zusätzlichen Stimme *erhöht* werden. Hierfür muss der Benutzer einige Einrichtungsschritte durchführen.
2. Die Anzahl von Stimmen kann durch Löschen der Stimme eines Knotens *verringert* werden (dies erfolgt bei Bedarf automatisch).

Wenn die verbleibenden Knoten erfolgreich nachweisen können, dass sie die *Mehrheit* bilden, wird die Definition des Begriffs *Mehrheit* aktualisiert und auf die restlichen Knoten begrenzt. So kann ein Knoten des Clusters ausfallen, dann ein weiterer, noch ein weiterer usw. Diese Anpassung der *Gesamtanzahl von Stimmen* nach aufeinander folgenden Ausfällen wird als ***dynamisches Quorum*** bezeichnet.  

### <a name="dynamic-witness"></a>Dynamischer Zeuge

Der dynamische Zeuge schaltet die Stimme des Zeugen ein oder aus, um sicherzustellen, dass die *Gesamtanzahl von Stimmen* ungerade ist. Bei einer ungeraden Anzahl von Stimmen hat der Zeuge keine Stimme. Ist die Anzahl von Stimmen gerade, hat der Zeuge eine Stimme. Das Risiko, dass der Cluster aufgrund eines Ausfalls des Zeugen ausfällt, wird durch den dynamischen Zeugen erheblich verringert. Der Cluster entscheidet je nach Anzahl von Abstimmungsknoten, die im Cluster verfügbar sind, ob die Stimme des Zeugen verwendet wird.

Wie das dynamische Quorum mit dem dynamischen Zeugen funktioniert, wird im Folgenden beschrieben.

### <a name="dynamic-quorum-behavior"></a>Verhalten des dynamischen Quorums

- Wenn Sie eine **gerade** Anzahl von Knoten und keinen Zeugen haben, wird die *Stimme eines Knotens gelöscht*. Beispielsweise erhalten nur drei der vier Knoten eine Stimme, sodass die *Gesamtzahl von Stimmen* drei ist und zwei verbleibende Knoten mit Stimme als Mehrheit gelten.
- Wenn Sie eine **ungerade** Anzahl von Knoten und keinen Zeugen haben, *erhalten alle Knoten eine Stimme*.
- Wenn Sie eine **gerade** Anzahl von Knoten und einen Zeugen haben, *stimmt der Zeuge ab*, sodass die Gesamtanzahl ungerade ist.
- Wenn Sie eine **ungerade** Anzahl von Knoten und einen Zeugen haben, *stimmt der Zeuge nicht ab*.

Mithilfe des dynamischen Quorums kann einem Knoten dynamisch eine Stimme zugewiesen werden, damit die Stimmenmehrheit nicht verloren geht und der Cluster mit einem Knoten (dem so genannten „Last Man Standing“) ausgeführt werden kann. Nehmen wir als Beispiel einen Cluster mit vier Knoten. Angenommen, das Quorum erfordert drei Stimmen. 

In diesem Fall wäre der Cluster beim Ausfall von zwei Knoten nicht mehr verfügbar.

![Diagramm mit vier Clusterknoten, von denen jeder eine Stimme erhält](media/quorum/dynamic-quorum-base.png)

Das dynamische Quorum verhindert jedoch, dass dies geschieht. Die *Gesamtanzahl von Stimmen*, die für das Quorum erforderlich ist, wird jetzt basierend auf der Anzahl verfügbarer Knoten bestimmt. Bei einem dynamischen Quorum bleibt der Cluster daher auch dann verfügbar, wenn drei Knoten ausfallen.

![Diagramm mit vier Clusterknoten, die nacheinander ausfallen, und der nach jedem Ausfall angepassten Anzahl erforderlicher Stimmen](media/quorum/dynamic-quorum-step-through.png)

Das obige Szenario gilt für einen allgemeinen Cluster, für den „Direkte Speicherplätze“ nicht aktiviert ist. Wenn „Direkte Speicherplätze“ aktiviert ist, toleriert der Cluster nur zwei Knotenausfälle. Weitere Informationen dazu finden Sie im Abschnitt zum [Poolquorum](#pool-quorum-overview).

### <a name="examples"></a>Beispiele

#### <a name="two-nodes-without-a-witness"></a>Zwei Knoten ohne einen Zeugen
Die Stimme eines Knotens wird gelöscht, sodass die *Stimmenmehrheit* anhand einer Gesamtanzahl von **einer Stimme** bestimmt wird. Wenn der Knoten, der nicht abstimmt, unerwartet ausfällt, hat der verbleibende Knoten die 1/1-Mehrheit, und der Cluster bleibt online. Wenn der Knoten, der abstimmt, unerwartet ausfällt, hat der verbleibende Knoten die 0/1-Mehrheit, und der Cluster ist nicht mehr verfügbar. Wenn der Knoten, der abstimmt, korrekt heruntergefahren wird, wird die Stimme an den anderen Knoten übertragen, und der Cluster bleibt online. ***Aus diesem Grund ist es wichtig, einen Zeugen zu konfigurieren.***

![Funktionsweise des Quorums im Fall von zwei Knoten ohne einen Zeugen](media/quorum/2-node-no-witness.png)

- Übersteht einen Serverausfall: **Chance von 50 Prozent**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Nein**.
- Übersteht zwei gleichzeitige Serverausfälle: **Nein**.

#### <a name="two-nodes-with-a-witness"></a>Zwei Knoten mit einem Zeugen
Beide Knoten und der Zeuge stimmen ab, sodass die *Mehrheit* anhand einer Gesamtanzahl von **drei Stimmen** bestimmt wird. Wenn ein Knoten ausfällt, hat der verbleibende Knoten die 2/3-Mehrheit, und der Cluster bleibt online.

![Funktionsweise des Quorums im Fall von zwei Knoten mit einem Zeugen](media/quorum/2-node-witness.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Nein**.
- Übersteht zwei gleichzeitige Serverausfälle: **Nein**.

#### <a name="three-nodes-without-a-witness"></a>Drei Knoten ohne einen Zeugen
Alle Knoten stimmen ab, sodass die *Mehrheit* anhand einer Gesamtanzahl von **drei Stimmen** bestimmt wird. Wenn ein Knoten ausfällt, haben die verbleibenden Knoten die 2/3-Mehrheit, und der Cluster bleibt online. Der Cluster besteht dann aus zwei Knoten ohne einen Zeugen. Somit gilt das obige Szenario 1.

![Funktionsweise des Quorums im Fall von drei Knoten ohne einen Zeugen](media/quorum/3-node-no-witness.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Chance von 50 Prozent**.
- Übersteht zwei gleichzeitige Serverausfälle: **Nein**.

#### <a name="three-nodes-with-a-witness"></a>Drei Knoten mit einem Zeugen
Alle Knoten stimmen ab, sodass der Zeuge zunächst nicht abstimmt. Die *Mehrheit* wird anhand einer Gesamtanzahl von **drei Stimmen** bestimmt. Nach einem Ausfall verfügt der Cluster über zwei Knoten mit einem Zeugen. Somit gilt wieder Szenario 2. Jetzt stimmen daher beide Knoten und der Zeuge ab.

![Funktionsweise des Quorums im Fall von drei Knoten mit einem Zeugen](media/quorum/3-node-witness.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Ja**.
- Übersteht zwei gleichzeitige Serverausfälle: **Nein**.

#### <a name="four-nodes-without-a-witness"></a>Vier Knoten ohne einen Zeugen
Die Stimme eines Knotens wird gelöscht, sodass die *Mehrheit* anhand einer Gesamtanzahl von **drei Stimmen** bestimmt wird. Nach einem Ausfall verfügt der Cluster über drei Knoten, und es gilt Szenario 3.

![Funktionsweise des Quorums im Fall von vier Knoten ohne einen Zeugen](media/quorum/4-node-no-witness.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Ja**.
- Übersteht zwei gleichzeitige Serverausfälle: **Chance von 50 Prozent**.

#### <a name="four-nodes-with-a-witness"></a>Vier Knoten mit einem Zeugen
Alle Knoten und der Zeuge stimmen ab, sodass die *Mehrheit* anhand einer Gesamtanzahl von **fünf Stimmen** bestimmt wird. Nach einem Ausfall gilt Szenario 4. Nach zwei gleichzeitigen Ausfällen gilt wieder Szenario 2.

![Funktionsweise des Quorums im Fall von vier Knoten mit einem Zeugen](media/quorum/4-node-witness.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Ja**.
- Übersteht zwei gleichzeitige Serverausfälle: **Ja**. 

#### <a name="five-nodes-and-beyond"></a>Fünf und mehr Knoten
Alle Knoten oder alle bis auf einen Knoten stimmen ab, je nachdem, wodurch eine ungerade Gesamtanzahl erreicht wird. „Direkte Speicherplätze“ unterstützt in jedem Fall maximal zwei ausgefallene Knoten. Daher ist ein Zeuge an dieser Stelle weder erforderlich noch nützlich.

![Funktionsweise des Quorums im Fall von fünf und mehr Knoten](media/quorum/5-nodes.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Ja**.
- Übersteht zwei gleichzeitige Serverausfälle: **Ja**. 

Nachdem Sie nun wissen, wie das Quorum funktioniert, befassen wir uns jetzt mit den Typen von Quorumzeugen.

### <a name="quorum-witness-types"></a>Typen von Quorumzeugen

Failoverclustering unterstützt drei Typen von Quorumzeugen:

- **[Cloudzeuge](/windows-server/failover-clustering/deploy-cloud-witness)** : BLOB-Speicher in Azure, auf den alle Knoten des Clusters zugreifen können. Dieser Zeuge verwaltet Clusteringinformationen in einer Datei „witness.log“, speichert aber keine Kopie der Clusterdatenbank.
- **SMB-Dateifreigabe**: Eine SMB-Dateifreigabe, die auf einem Dateiserver mit Windows Server konfiguriert ist. Dieser Zeuge verwaltet Clusteringinformationen in einer Datei „witness.log“, speichert aber keine Kopie der Clusterdatenbank.
- **Datenträgerzeuge**: Ein kleiner Clusterdatenträger in der Gruppe „Verfügbarer Clusterspeicher“. Dieser Datenträger ist hochverfügbar und unterstützt ein Failover zwischen Knoten. Er enthält eine Kopie der Clusterdatenbank.  ***Ein Datenträgerzeuge wird von „Direkte Speicherplätze“ nicht unterstützt.***

## <a name="pool-quorum-overview"></a>Übersicht über das Poolquorum

Bisher ging es um das Clusterquorum auf der Clusterebene. Als Nächstes befassen wir uns mit dem Poolquorum, das auf der Poolebene angewendet wird (d. h. der Pool bleibt auch bei einem Ausfall von Knoten und Laufwerken verfügbar). Speicherpools sind zur Verwendung in Szenarien mit und ohne Cluster konzipiert und haben daher einen anderen Quorummechanismus.

In der folgenden Tabelle finden Sie eine Übersicht über die Ergebnisse des Poolquorums für verschiedene Szenarien:

| Serverknoten | Übersteht einen Serverknotenausfall | Übersteht zwei nacheinander auftretende Serverknotenausfälle | Übersteht zwei gleichzeitige Serverknotenausfälle |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | Nein                                  | Nein                                                | Nein                                                 |
| 2 + Zeuge  | Ja                                 | Nein                                                | Nein                                                 |
| 3            | Ja                                 | Nein                                                | Nein                                                 |
| 3 + Zeuge  | Ja                                 | Nein                                                | Nein                                                 |
| 4            | Ja                                 | Nein                                                | Nein                                                 |
| 4 + Zeuge  | Ja                                 | Ja                                               | Ja                                                |
| 5 und mehr  | Ja                                 | Ja                                               | Ja                                                |

## <a name="how-pool-quorum-works"></a>Funktionsweise des Poolquorums

Wenn Laufwerke ausfallen oder bei einer Teilmenge von Laufwerken die Verbindung mit einer anderen Teilmenge unterbrochen wird, müssen die verbleibenden Laufwerke nachweisen, dass sie die *Mehrheit* des Pools darstellen, damit sie online bleiben. Können sie dies nicht, werden sie offline geschaltet. Der Pool ist die Entität, die je nachdem, ob ausreichend Datenträger für das Quorum verfügbar sind, offline geschaltet wird oder online bleibt (50 % + 1). Der Besitzer der Poolressource (der aktive Clusterknoten) kann das „+ 1“ sein.

Das Poolquorum funktioniert jedoch anders als das Clusterquorum:

- Der Pool verwendet einen Knoten im Cluster als Zeugen und Tiebreaker, um einen Ausfall der Hälfte der Laufwerke zu überstehen (der Knoten, der der Besitzer der Poolressource ist).
- Der Pool verfügt NICHT über ein dynamisches Quorum.
- Der Pool implementiert KEINE eigene Variante der Entfernung einer Stimme.

### <a name="examples"></a>Beispiele

#### <a name="four-nodes-with-a-symmetrical-layout"></a>Vier Knoten mit symmetrischem Layout 
Jedes der 16 Laufwerke verfügt über eine Stimme, und Knoten 2 hat ebenfalls eine Stimme (da er der Besitzer der Poolressource ist). Die *Mehrheit* wird anhand einer Gesamtanzahl von **16 Stimmen** bestimmt. Wenn die Knoten 3 und 4 ausfallen, besteht die verbleibende Teilmenge aus acht Laufwerken und dem Besitzer der Poolressource, was 9/16 Stimmen entspricht. Der Pool ist daher weiterhin verfügbar.

![Poolquorum 1](media/quorum/pool-1.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Ja**.
- Übersteht zwei gleichzeitige Serverausfälle: **Ja**. 

#### <a name="four-nodes-with-a-symmetrical-layout-and-drive-failure"></a>Vier Knoten mit symmetrischem Layout und Laufwerkausfall 
Jedes der 16 Laufwerke verfügt über eine Stimme, und Knoten 2 hat ebenfalls eine Stimme (da er der Besitzer der Poolressource ist). Die *Mehrheit* wird anhand einer Gesamtanzahl von **16 Stimmen** bestimmt. Zuerst fällt Laufwerk 7 aus. Wenn die Knoten 3 und 4 ausfallen, besteht die verbleibende Teilmenge aus sieben Laufwerken und dem Besitzer der Poolressource, was 8/16 Stimmen entspricht. Der Pool hat somit keine Mehrheit und ist nicht mehr verfügbar.

![Poolquorum 2](media/quorum/pool-2.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Nein**.
- Übersteht zwei gleichzeitige Serverausfälle: **Nein**. 

#### <a name="four-nodes-with-a-non-symmetrical-layout"></a>Vier Knoten mit nicht symmetrischem Layout 
Jedes der 24 Laufwerke verfügt über eine Stimme, und Knoten 2 hat ebenfalls eine Stimme (da er der Besitzer der Poolressource ist). Die *Mehrheit* wird anhand einer Gesamtanzahl von **24 Stimmen** bestimmt. Wenn die Knoten 3 und 4 ausfallen, besteht die verbleibende Teilmenge aus acht Laufwerken und dem Besitzer der Poolressource, was 9/24 Stimmen entspricht. Der Pool hat somit keine Mehrheit und ist nicht mehr verfügbar.

![Poolquorum 3](media/quorum/pool-3.png)

- Übersteht einen Serverausfall: **Ja**.
- Übersteht zwei nacheinander auftretende Serverausfälle: **Variiert je nach Fall** (übersteht einen Ausfall von Knoten 3 und 4 nicht, bleibt aber in allen anderen Szenarien verfügbar).
- Übersteht zwei gleichzeitige Serverausfälle: **Variiert je nach Fall** (übersteht einen Ausfall von Knoten 3 und 4 nicht, bleibt aber in allen anderen Szenarien verfügbar).

### <a name="pool-quorum-recommendations"></a>Empfehlungen für das Poolquorum

- Stellen Sie sicher, dass alle Knoten im Cluster symmetrisch sind (über die gleiche Anzahl von Laufwerken verfügen).
- Aktivieren Sie die Drei-Wege-Spiegelung oder duale Parität, damit Sie Knotenausfälle tolerieren können und die virtuellen Datenträger online bleiben. 
- Wenn mehr als zwei Knoten oder zwei Knoten und ein Datenträger auf einem anderen Knoten ausfallen, haben die Volumes möglicherweise keinen Zugriff auf alle drei Kopien der Daten, sodass sie offline geschaltet werden und nicht verfügbar sind. Es wird empfohlen, die Server schnell wieder online zu schalten oder die Datenträger umgehend zu ersetzen, um die bestmögliche Resilienz für alle Daten im Volume sicherzustellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Konfigurieren und Verwalten des Quorums](/windows-server/failover-clustering/manage-cluster-quorum)
- [Bereitstellen eines Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness)
