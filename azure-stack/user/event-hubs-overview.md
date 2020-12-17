---
title: Übersicht zu Event Hubs in Azure Stack Hub
description: Erfahren Sie mehr über Event Hubs in Azure Stack Hub. Lernen Sie, Hybridlösungen zu erstellen. Vergleichen Sie die Features von Azure Event Hubs und Event Hubs in Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/11/2020
ms.reviewer: jfggdl
ms.lastreviewed: 12/11/2020
ms.openlocfilehash: c7b54121f826fb514590f39c92cde0e62972ce97
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364148"
---
# <a name="overview-of-event-hubs-on-azure-stack-hub"></a>Übersicht zu Event Hubs in Azure Stack Hub

Mit Event Hubs in Azure Stack Hub können Sie Hybrid Cloud-Szenarien umsetzen. Streaming- und ereignisbasierte Lösungen werden sowohl für lokale als auch für Azure-Cloudverarbeitung unterstützt. Unabhängig davon, ob Ihr Szenario ein hybrides (verbundenes) oder getrenntes Szenario ist, kann Ihre Lösung die Verarbeitung von großvolumigen Ereignissen/Streams unterstützen. Ihr Szenario ist nur an die Größe des Event Hubs-Clusters gebunden, die Sie entsprechend Ihren Anforderungen bereitstellen können. 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>Lokales Ausführen von Aufgaben für die Ereignisverarbeitung und Erstellen von ereignisgesteuerten Anwendungen

Mit Event Hubs in Azure Stack Hub können Sie etwa die folgenden Geschäftsszenarien implementieren:

- KI- und Machine Learning-Workloads, bei denen Event Hubs als Ereignisstreaming-Engine fungiert
- Implementierung ereignisgesteuerter Architekturen an eigenen Standorten außerhalb der Azure-Rechenzentren
- Clickstreamanalyse für Ihre lokal bereitgestellten Webanwendungen
- Analyse der Gerätetelemetrie
- Streamverarbeitung mit Open-Source-Frameworks, die Apache Kafka verwenden (etwa Apache Spark, Flink, Storm und Samza)
- [Nutzen der Computemetriken und -ereignisse von Gastbetriebssystemen](azure-stack-metrics-monitor.md)

## <a name="build-hybrid-solutions"></a>Erstellen von Hybridlösungen

Erstellen Sie Hybridlösungen, die Edgedaten in Ihrer Azure Stack Hub-Instanz lokal erfassen und verarbeiten. Senden Sie aggregierte Daten zur weiteren Verarbeitung, Visualisierung und Speicherung an Azure. Nutzen Sie ggf. serverloses Computing in Azure.

[![Diagramm mit Hybridlösungen](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>Features 

Die Event Hubs-Editionen (in Azure Stack Hub und in Azure) bieten ein hohes Maß an Featureparität (Funktionsparität). Diese Parität bedeutet, dass SDKs, Beispiele, PowerShell, CLI und Portale eine ähnliche Funktionalität bieten, mit wenigen Unterschieden. In der folgenden Tabelle sind die grundsätzlichen Unterschiede zusammengestellt, die es in der Verfügbarkeit von Features zwischen den Editionen gibt.  

| Komponente | Event Hubs in Azure Stack Hub | Azure Event Hubs |
|-|-|-|-|
| Operator-/Administratorfunktionalität | ✔ | ✘ |
| Kafka-Unterstützung | ✔ | ✔ |
| Identischer Satz von SDKs | ✔ | ✔ |
| Autorisieren des Zugriffs auf Event Hubs mit Azure Active Directory | ✘ | ✔ |
| Erfassungs-Feature | ✘ | ✔ |
| Georedundante Notfallwiederherstellung | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Automatisch vergrößern-Funktionalität | ✘ | ✔ |

Vorgänge zur Azure-Ressourcenverwaltung können auch über Azure Resource Manager-Vorlagen, [PowerShell](/powershell/module/Az.eventhub/) und die [Azure CLI](/cli/azure/eventhubs/eventhub/) ausgeführt werden. Vorgänge zur Operatorverwaltung werden in PowerShell und Azure CLI derzeit nicht unterstützt.

## <a name="feature-documentation"></a>Featuredokumentation

Die [Azure Event Hubs-Dokumentation](/azure/event-hubs/) gilt für beide Editionen von Event Hubs: Event Hubs in Azure Stack Hub und Azure Event Hubs. Diese Dokumentation enthält Themen zur Verwendung von Event Hubs und Aktivitäten. Dazu gehören:

- Details zu [Event Hubs Konzepten](/azure/event-hubs/event-hubs-features)
- [Erstellen eines Event Hubs-Clusters und -Namespace](event-hubs-quickstart-cluster-portal.md)
- Erstellen eines [Event Hubs](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- Streamen [mit dem Kafka-Protokoll](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Operator-Dokumentation 
 
Weitere Informationen zur Event Hubs in Azure Stack Hub-Operatorfunktionalität finden Sie im Artikel [Event Hubs in Azure Stack Hub – Übersicht](../operator/event-hubs-rp-overview.md). Diese Dokumentation enthält Informationen zu Aktivitäten wie:

- Installieren von Event Hubs
- Verfügbarmachen von Event Hubs für Benutzer
- Abrufen von Informationen zur Integrität des Diensts
- Sammeln von Protokollen


## <a name="next-steps"></a>Nächste Schritte

Ist Event Hubs in Ihrem Abonnement nicht verfügbar, [installieren Sie den Ressourcenanbieter „Event Hubs on Azure Stack Hub“ (Event Hubs in Azure Stack Hub)](../operator/event-hubs-rp-overview.md) in Zusammenarbeit mit Ihrem Administrator.

Wenn Sie Event Hubs installiert haben und verwenden möchten, lesen Sie die [Event Hubs-Dokumentation](/azure/event-hubs/event-hubs-about), um weitere Details zu dem Dienst zu erfahren.
