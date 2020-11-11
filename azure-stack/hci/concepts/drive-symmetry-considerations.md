---
title: Überlegungen zur Laufwerkssymmetrie für Azure Stack HCI
description: In diesem Thema werden die Einschränkungen der Laufwerkssymmetrie erläutert und Beispiele für unterstützte und nicht unterstützte Konfigurationen bereitgestellt.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/29/2020
ms.openlocfilehash: 6cf983d6cf64b0b41bb9710bdf720dd1777c9ad6
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064802"
---
# <a name="drive-symmetry-considerations-for-azure-stack-hci"></a>Überlegungen zur Laufwerkssymmetrie für Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI funktioniert am besten, wenn jeder Server genau über die gleichen Laufwerke verfügt.

Wir sind uns bewusst, dass dies in der Praxis nicht immer praktikabel ist, denn Azure Stack HCI ist so konzipiert, dass es jahrelang arbeitet und mit den wachsenden Anforderungen Ihrer Organisation skaliert werden kann. Heute können Sie großzügige 3 TB-Festplatten kaufen, während es nächstes Jahr vielleicht unmöglich ist, so kleine Laufwerke zu finden. Daher wird ein gewisses Maß an Kombination und Abgleich erwartet und unterstützt. Denken Sie jedoch daran, dass eine stärkere Symmetrie immer besser ist.

In diesem Thema werden die Einschränkungen erläutert und Beispiele für unterstützte und nicht unterstützte Konfigurationen bereitgestellt.

## <a name="constraints"></a>Einschränkungen

In diesem Abschnitt werden Einschränkungen hinsichtlich der Typen, Modelle, Größen und Anzahl von Laufwerken erläutert.

### <a name="type"></a>type

Alle Server sollten dieselben [Laufwerkstypen](choose-drives.md#drive-types) aufweisen.

Wenn z. B. ein Server über NVMe verfügt, sollten sie *alle* NVMe verwenden.

### <a name="number"></a>Number

Alle Server sollten dieselbe Anzahl von Laufwerken der einzelnen Typen aufweisen.

Wenn z. B. ein Server über sechs SSDs verfügt, sollten sie *alle* sechs SSDs verwenden.

   > [!NOTE]
   > Es ist in Ordnung, wenn die Anzahl der Laufwerke bei Fehlern oder beim Hinzufügen oder Entfernen von Laufwerken vorübergehend unterschiedlich ist.

### <a name="model"></a>Modell

Wir empfehlen die Verwendung von Laufwerken desselben Modells und derselben Firmwareversion, wann immer dies möglich ist. Wenn dies nicht möglich ist, wählen Sie sorgfältig Laufwerke aus, die so ähnlich wie möglich sind. Wir raten davon ab, Laufwerke desselben Typs mit stark unterschiedlichen Eigenschaften hinsichtlich Leistung und Lebensdauer zu kombinieren und abzugleichen (es sei denn, das eine ist für den Cache und das andere für die Kapazität vorgesehen), da Azure Stack HCI die E/A gleichmäßig verteilt und nicht aufgrund des Modells unterscheidet.

   > [!NOTE]
   > Es ist in Ordnung, ähnliche SATA- und SAS-Laufwerke zu kombinieren und abzugleichen.

### <a name="size"></a>Size

Es wird nach Möglichkeit empfohlen, Laufwerke mit denselben Größen zu verwenden. Die Verwendung von Laufwerken unterschiedlicher Größe kann zu einer unbrauchbaren Kapazität führen, und die Verwendung von Cachelaufwerken unterschiedlicher Größe verbessert die Cacheleistung möglicherweise nicht. Nähere Informationen finden Sie im nächsten Abschnitt.

   > [!WARNING]
   > Unterschiedliche Größen von Laufwerken mit unterschiedlicher Kapazität auf verschiedenen Servern können zu Kapazitätsproblemen führen.

## <a name="understand-capacity-imbalance"></a>Grundlegendes zu unausgeglichenen Kapazitäten

Azure Stack HCI ist hinsichtlich unausgeglichenen Kapazitäten zwischen Laufwerken und Servern stabil. Selbst bei einer schweren Unausgeglichenheit wird alles weiter funktionieren. Abhängig von mehreren Faktoren kann es jedoch sein, dass Kapazität, die nicht auf jedem Server verfügbar ist, nicht genutzt werden kann.

Betrachten Sie die vereinfachte Abbildung unten, um die Gründe dafür zu sehen. Jedes farbige Kästchen stellt eine Kopie der gespiegelten Daten dar. Die mit A, A' und A'' gekennzeichneten Felder sind z. B. drei Kopien derselben Daten. Diese Kopien *müssen* auf verschiedenen Servern gespeichert werden, um die Serverfehlertoleranz zu berücksichtigen.

### <a name="stranded-capacity"></a>Isolierte Kapazität

Wie dargestellt, sind Server 1 (10 TB) und Server 2 (10 TB) voll. Server 3 hat größere Laufwerke, daher ist seine Gesamtkapazität größer (15 TB). Um jedoch weitere Drei-Wege-Spiegelungsdaten auf Server 3 zu speichern, wären auch Kopien auf Server 1 und Server 2 erforderlich, die bereits voll sind. Die verbleibenden 5 TB Kapazität auf Server 3 können nicht genutzt werden – es handelt sich um *„isolierte“* Kapazität.

![Drei-Wege-Spiegelung, drei Server, isolierte Kapazität](media/drive-symmetry-considerations/Size-Asymmetry-3N-Stranded.png)

### <a name="optimal-placement"></a>Optimale Platzierung

Umgekehrt ist es bei vier Servern mit einer Kapazität von 10 TB, 10 TB, 10 TB und 15 TB und einer Drei-Wege-Spiegelungsresilienz *möglich* , Kopien gültig so zu platzieren, dass die gesamte verfügbare Kapazität wie dargestellt genutzt wird. Wann immer dies möglich ist, wird die direkte Speicherplatzzuweisung die optimale Platzierung finden und nutzen, sodass keine isolierte Kapazität übrig bleibt.

![Drei-Wege-Spiegelung, vier Server, keine isolierte Kapazität](media/drive-symmetry-considerations/Size-Asymmetry-4N-No-Stranded.png)

Die Anzahl der Server, die Resilienz, der Schweregrad der unausgeglichenen Kapazitäten und andere Faktoren beeinflussen, ob es isolierte Kapazitäten gibt. **Die sinnvollste allgemeine Regel ist die Annahme, dass nur die in jedem Server verfügbare Kapazität garantiert nutzbar ist.**

## <a name="understand-cache-imbalance"></a>Grundlegendes zu unausgeglichenem Cache

Azure Stack HCI ist hinsichtlich unausgeglichenen Caches zwischen Laufwerken und Servern stabil. Selbst bei einer schweren Unausgeglichenheit wird alles weiter funktionieren. Außerdem nutzt Azure Stack HCI immer den gesamten verfügbaren Cache voll aus.

Die Verwendung von Cachelaufwerken unterschiedlicher Größe verbessert die Cacheleistung jedoch möglicherweise nicht einheitlich oder vorhersagbar: nur bei E/A für [Laufwerksbindungen](cache.md#server-side-architecture) mit größeren Cachelaufwerken kann die Leistung verbessert werden. Azure Stack HCI verteilt die E/A gleichmäßig über die Bindungen und nicht aufgrund des Verhältnisses von Cache und Kapazität.

![Unausgeglichener Cache](media/drive-symmetry-considerations/Cache-Asymmetry.png)

   > [!TIP]
   > Weitere Informationen zu Cachebindungen finden Sie unter [Grundlegendes zum Cache](cache.md).

## <a name="example-configurations"></a>Beispielkonfigurationen

Hier sind einige unterstützte und nicht unterstützte Konfigurationen aufgeführt:

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-between-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Unterstützt: verschiedene Modelle zwischen Servern

Die ersten beiden Server verwenden das NVMe-Modell „X“, aber der dritte Server verwendet das NVMe-Modell „Z“, das sehr ähnlich ist.

| Server 1                    | Server 2                    | Server 3                    |
|-----------------------------|-----------------------------|-----------------------------|
| 2 x NVMe-Modell X (Cache)    | 2 x NVMe-Modell X (Cache)    | 2 x NVMe-Modell Z (Cache)    |
| 10 x SSD-Modell Y (Kapazität) | 10 x SSD-Modell Y (Kapazität) | 10 x SSD-Modell Y (Kapazität) |

Dies wird unterstützt.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false":::Unterstützt: verschiedene Modelle innerhalb des Servers

Jeder Server verwendet eine andere Kombination aus den HDD-Modellen „Y“ und „Z“, die sehr ähnlich sind. Jeder Server verfügt über insgesamt 10 Festplattenlaufwerke.

| Server 1                   | Server 2                   | Server 3                   |
|----------------------------|----------------------------|----------------------------|
| 2 x SSD-Modell X (Cache)    | 2 x SSD-Modell X (Cache)    | 2 x SSD-Modell X (Cache)    |
| 7 x HDD-Modell Y (Kapazität) | 5 x HDD-Modell Y (Kapazität) | 1 x HDD-Modell Y (Kapazität) |
| 3 x HDD-Modell Z (Kapazität) | 5 x HDD-Modell Z (Kapazität) | 9 x HDD-Modell Z (Kapazität) |

Dies wird unterstützt.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Unterstützt: verschiedene serverübergreifende Größen

Die ersten beiden Server verwenden ein 4 TB-Festplattenlaufwerk, aber der dritte Server verwendet ein sehr ähnliches 6 TB-Festplattenlaufwerk.

| Server 1                | Server 2                | Server 3                |
|-------------------------|-------------------------|-------------------------|
| 2 x 800 GB NVMe (Cache) | 2 x 800 GB NVMe (Cache) | 2 x 800 GB NVMe (Cache) |
| 4 x 4 TB HDD (Kapazität) | 4 x 4 TB HDD (Kapazität) | 4 x 6 TB HDD (Kapazität) |

Dies wird unterstützt, obwohl es zu isolierten Kapazitäten führt.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Unterstützt: verschiedene Größen innerhalb des Servers

Jeder Server verwendet eine andere Kombination aus 1,2 TB und sehr ähnlichen 1,6 TB SSDs. Jeder Server verfügt über insgesamt 4 SSDs.

| Server 1                 | Server 2                 | Server 3                 |
|--------------------------|--------------------------|--------------------------|
| 3 x 1,2 TB SSD (Cache)   | 2 x 1,2 TB SSD (Cache)   | 4 x 1,2 TB SSD (Cache)   |
| 1 x 1,6 TB SSD (Cache)   | 2 x 1,6 TB SSD (Cache)   | -                        |
| 20 x 4 TB HDD (Kapazität) | 20 x 4 TB HDD (Kapazität) | 20 x 4 TB HDD (Kapazität) |

Dies wird unterstützt.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-types-of-drives-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nicht unterstützt: verschiedene Laufwerkstypen auf verschiedenen Servern

Server 1 verfügt über NVMe, aber die anderen nicht.

| Server 1            | Server 2            | Server 3            |
|---------------------|---------------------|---------------------|
| 6 x NVMe (Cache)    | -                   | -                   |
| -                   | 6 x SSD (Cache)     | 6 x SSD (Cache)     |
| 18 x HDD (Kapazität) | 18 x HDD (Kapazität) | 18 x HDD (Kapazität) |

Dies wird nicht unterstützt. Die Laufwerkstypen sollten in jedem Server gleich sein.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-number-of-each-type-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nicht unterstützt: verschiedene Anzahl der einzelnen Typen auf verschiedenen Servern

Server 3 hat mehr Laufwerke als die anderen.

| Server 1            | Server 2            | Server 3            |
|---------------------|---------------------|---------------------|
| 2 x NVMe (Cache)    | 2 x NVMe (Cache)    | 4 x NVMe (Cache)    |
| 10 x HDD (Kapazität) | 10 x HDD (Kapazität) | 20 x HDD (Kapazität) |

Dies wird nicht unterstützt. Die Anzahl der Laufwerke der einzelnen Typen sollten in jedem Server gleich sein.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-only-hdd-drives"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nicht unterstützt: nur HDD-Laufwerke

An alle Server sind nur HDD-Laufwerke angeschlossen.

|Server 1|Server 2|Server 3|
|-|-|-|
|18 x HDD (Kapazität) |18 x HDD (Kapazität)|18 x HDD (Kapazität)|

Dies wird nicht unterstützt. Sie müssen mindestens zwei Cachelaufwerke (NvME oder SSD) hinzufügen, die an jeden der Server angeschlossen sind.

## <a name="summary"></a>Zusammenfassung

Um es noch einmal zusammenzufassen: Jeder Server im Cluster sollte über dieselben Laufwerkstypen und dieselbe Anzahl von Laufwerken des jeweiligen Typs verfügen. Es wird unterstützt, um Laufwerksmodelle und Laufwerksgrößen bei Bedarf mit den obigen Überlegungen zu kombinieren und abzugleichen.

| Constraint | State |
|--|--|
| Gleiche Laufwerkstypen in jedem Server | **Erforderlich** |
| Gleiche Anzahl der einzelnen Typen in jedem Server | **Erforderlich** |
| Gleiche Laufwerksmodelle in jedem Server | Empfohlen |
| Gleiche Laufwerksgrößen in jedem Server | Empfohlen |

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Systemanforderungen](system-requirements.md)
- [Auswählen von Laufwerken](choose-drives.md)
