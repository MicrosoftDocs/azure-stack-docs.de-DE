---
title: Hybridlösung für die Implementierung eines KI-basierten Musters zur Ermittlung der Kundenfrequenz mithilfe von Azure und Azure Stack
description: Finden Sie heraus, wie Sie Azure- und Azure Stack-Dienste verwenden, um ein KI-basiertes Muster zur Ermittlung der Kundenfrequenz implementieren, um den Kundenverkehr in einem Einzelhandelsgeschäft zu analysieren.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: e87baeba1b961c134fc5754fda8c9f1688f3fe90
ms.sourcegitcommit: f38903cb925276e96dd62ebe8ffe92df8bf6e5c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256553"
---
# <a name="footfall-detection-solution"></a>Lösung zur Ermittlung der Kundenfrequenz

Dieser Artikel bietet eine Übersicht über eine Lösung zur Implementierung eines KI-basierten Musters zur Ermittlung der Kundenfrequenz. Anhand eines solchen Musters lässt sich der Kundenverkehr in Einzelhandelsgeschäften analysieren. Die Lösung generiert mithilfe von Azure, Azure Stack und dem Custom Vision AI Dev Kit Erkenntnisse aus realen Aktionen.

## <a name="context-and-problem"></a>Kontext und Problem

Die Contoso Stores möchten gerne Erkenntnisse darüber gewinnen, wie Kunden ihre aktuellen Produkte in der derzeitigen Aufteilung und Gestaltung der Ladengeschäfte wahrnehmen. Das Unternehmen kann nicht für jede einzelne Abteilung Mitarbeiter bereitstellen, und es ist nicht effizient, sämtliche Kameraaufnahmen eines gesamten Geschäfts durch ein Analystenteam überprüfen zu lassen. Darüber hinaus verfügt keines der Ladengeschäfte über genügend Bandbreite, um die Videodaten aller Kameras zu Analysezwecken in die Cloud zu streamen. 

Contoso möchte eine unaufdringliche Möglichkeit finden, demografische Daten, Informationen zur Kundentreue sowie die Reaktionen der Kunden auf Werbeflächen und Produkte im Geschäft zu ermitteln, ohne den Datenschutz für die Kunden zu gefährden.

## <a name="solution"></a>Lösung

Die Analyselösung für die Einzelhandelsgeschäfte nutzt einen gestaffelten Ansatz im Edge zum Ziehen von Rückschlüssen. Mit dem Custom Vision AI Dev Kit werden nur Bilder mit Gesichtern von Menschen zur Analyse an eine private Azure Stack-Instanz gesendet, die Azure Cognitive Services ausführt. Anonymisierte Daten werden aggregiert und an Azure gesendet. Dort werden alle Daten aus allen Geschäften zusammengefasst und in Power BI visualisiert. Durch eine Kombination aus Edge und öffentlicher Cloud kann Contoso von moderner KI-Technologie profitieren. Gleichzeitig sind die Einhaltung der Unternehmensrichtlinien und der Datenschutz für die Kunden gewährleistet.

[![Muster zur Ermittlung der Kundenfrequenz](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)

So funktioniert die Lösung: 

1. Das Custom Vision AI Dev Kit ruft eine Konfiguration aus IoT Hub an, die die IoT Edge-Runtime und ein ML-Modell installiert.
2. Wenn das Modell eine Person erkennt, nimmt es ein Bild auf und lädt dieses in den Azure Stack-Blobspeicher hoch. 
3. Der Blobdienst löst eine Azure-Funktion in Azure Stack aus. 
4. Die Azure-Funktion ruft einen Container mit der Gesichtserkennungs-API auf, um demografische und emotionsbezogene Daten aus dem Bild abzurufen.
5. Diese Daten werden anonymisiert und an einen Azure Event Hub gesendet.
6. Der Event Hub pusht die Daten in Stream Analytics.
7. Stream Analytics aggregiert die Daten und pusht sie in Power BI.

## <a name="components"></a>Komponenten

Diese Lösung verwendet die folgenden Komponenten:

| Ebene | Komponente | BESCHREIBUNG |
|----------|-----------|-------------|
| Hardware im Ladengeschäft | [Custom Vision AI Dev Kit](https://azure.github.io/Vision-AI-DevKit-Pages/) | Diese Komponente führt im Geschäft eine Filterung anhand eines lokalen ML-Modells aus, das nur Bilder von Personen zu Analysezwecken erfasst. Die Daten werden über IoT Hub sicher bereitgestellt und aktualisiert.<br><br>|
| Azure Stack | [App Service](../operator/azure-stack-app-service-overview.md) | Der App Service-Ressourcenanbieter stellt eine Basis für Edgekomponenten bereit. Hierzu gehören Hosting- und Verwaltungsfeatures für Web-Apps/APIs und Azure Functions. |
| | Cluster für die [AKS-Engine](https://github.com/Azure/aks-engine) (Azure Kubernetes Service) | Der AKS-Ressourcenanbieter mit dem in Azure Stack bereitgestellten AKS-Engine-Cluster bietet eine skalierbare, robuste Engine für die Ausführung des Containers der Gesichtserkennungs-API. |
| | [Container der Gesichtserkennungs-API](/azure/cognitive-services/face/face-how-to-install-containers) in Azure Cognitive Services| Der Azure Cognitive Services-Ressourcenanbieter mit Containern der Gesichtserkennungs-API liefert demografische und emotionsbezogene Daten sowie Daten zur Erkennung einmaliger Besucher in das private Netzwerk von Contoso. |
| | Blob Storage | Die vom AI Dev Kit erfassten Bilder werden in den Blobspeicher von Azure Stack hochgeladen. |
| | Azure-Funktionen | Eine in Azure Stack ausgeführte Azure-Funktion empfängt Eingangsdaten aus dem Blobspeicher und verarbeitet die Interaktionen mit der Gesichtserkennungs-API. Die Funktion gibt anonymisierte Daten an einen in Azure befindlichen Event Hub aus.<br><br>|
| Azure |  |  |
|  | [Azure Event Hubs](/azure/event-hubs/) | Azure Event Hubs bietet eine skalierbare Plattform für die Erfassung anonymisierter Daten, die sich nahtlos in Azure Stream Analytics integrieren lassen. |
|  | [Azure Stream Analytics](/azure/stream-analytics/) | Ein Azure Stream Analytics-Auftrag aggregiert die anonymisierten Daten und gruppiert sie zur Visualisierung in 15 Sekunden große Zeitfenster. |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | Power BI bietet eine benutzerfreundliche Dashboardschnittstelle zur Anzeige der Ausgabe von Azure Stream Analytics. |

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:

### <a name="scalability"></a>Skalierbarkeit 

Um diese Lösung auf mehrere Kameras und Standorte zu skalieren, müssen Sie sicherstellen, dass alle Komponenten die entsprechende höhere Last verarbeiten können. Möglicherweise müssen Sie Aktionen wie die folgenden ausführen:

- Erhöhen der Anzahl von Stream Analytics-Streamingeinheiten
- Horizontales Hochskalieren der Bereitstellung der Gesichtserkennungs-API
- Erhöhen des Event Hubs-Durchsatzes
- In Extremfällen ist möglicherweise eine Migration von Azure Functions zu einem virtuellen Computer erforderlich.

### <a name="availability"></a>Verfügbarkeit

Da in dieser Lösung verschiedene Ebenen involviert sind, müssen Sie darüber nachdenken, was bei Netzwerk- oder Stromausfällen passieren soll. Je nach Geschäftsanforderungen ist es möglicherweise ausreichend, einen Mechanismus zu implementieren, der Bilder lokal zwischenspeichert und sie dann an Azure Stack weiterleitet, wenn wieder eine Verbindung besteht. Wenn der Standort groß genug ist, ist die Bereitstellung einer Data Box Edge-Instanz mit einem auf diesen Standort ausgerichteten Gesichtserkennungs-API-Container möglicherweise eine bessere Option.

### <a name="manageability"></a>Verwaltbarkeit

Diese Lösung kann eine Vielzahl von Geräten und Standorten umfassen und damit recht unhandlich werden. Um neue Standorte und Geräte automatisch online zu schalten und auf dem neuesten Stand zu halten, können die [IoT-Dienste von Azure](/azure/iot-fundamentals/) verwendet werden. 

### <a name="security"></a>Sicherheit

Da diese Lösung Bilder von Kunden erfasst, ist das Thema Sicherheit von größter Bedeutung. Stellen Sie sicher, dass alle Speicherkonten mit geeigneten Zugriffsrichtlinien geschützt sind und die Schlüssel regelmäßig rotiert werden. Sorgen Sie dafür, dass Speicherkonten und Event Hubs über Beibehaltungsrichtlinien verfügen, die die Datenschutzbestimmungen des Unternehmens und des zuständigen Gesetzgebers erfüllen. Stellen Sie auch sicher, dass verschiedene Zugriffsebenen eingerichtet werden, sodass alle Personen, die Zugriff auf das System erhalten müssen, nur auf die Daten zugreifen können, die sie für ihre jeweilige Rolle benötigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den von dieser Lösung verwendeten Mustern finden Sie unter [Gestaffeltes Datenmuster](azure-stack-solution-staged-data.md). 

Weitere Informationen zum benutzerdefinierten maschinellen Sehen finden Sie hier: [Custom Vision AI Dev Kit](https://azure.github.io/Vision-AI-DevKit-Pages/). 