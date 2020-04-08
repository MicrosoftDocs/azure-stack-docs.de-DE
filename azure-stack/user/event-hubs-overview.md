---
title: Event Hubs in Azure Stack Hub – Übersicht
description: Erfahren Sie mehr über den Event Hubs-Ressourcenanbieter in Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: b0a4e18afdc8ce68ffa212bcc7bc78506420c235
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424740"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Event Hubs in Azure Stack Hub – Übersicht

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Mit Event Hubs in Azure Stack Hub können Sie Hybrid Cloud-Szenarien umsetzen. Streaming- und ereignisbasierte Lösungen werden sowohl für lokale als auch für Azure-Cloudverarbeitung unterstützt. Unabhängig davon, ob Ihr Szenario ein hybrides (verbundenes) oder getrenntes Szenario ist, kann Ihre Lösung die Verarbeitung von großvolumigen Ereignissen/Streams unterstützen. Ihr Szenario ist nur an die Größe des Event Hubs-Clusters gebunden, die Sie entsprechend Ihren Anforderungen bereitstellen können. 

## <a name="features"></a>Features 

Die Event Hubs-Editionen (in Azure Stack Hub und in Azure) bieten ein hohes Maß an Featureparität (Funktionsparität). Diese Parität bedeutet, dass SDKs, Beispiele, PowerShell, CLI und Portale eine ähnliche Funktionalität bieten, mit wenigen Unterschieden. In der folgenden Tabelle sind die grundsätzlichen Unterschiede zusammengestellt, die es in der Verfügbarkeit von Features zwischen den Editionen gibt.  

| Funktion | Event Hubs in Azure Stack Hub | Azure Event Hubs |
|-|-|-|-|
| Operator-/Administratorfunktionalität | ✔ | ✘ |
| Kafka-Unterstützung | ✔ | ✔ |
| Identischer Satz von SDKs | ✔ | ✔ |
| Autorisieren des Zugriffs auf Event Hubs mit Azure Active Directory | ✘ | ✔ |
| Erfassungs-Feature | ✘ | ✔ |
| Georedundante Notfallwiederherstellung | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Automatisch vergrößern-Funktionalität | ✘ | ✔ |

Außerdem können Vorgänge zur Azure-Ressourcenverwaltung über Azure Resource Manager-Vorlagen, [PowerShell](/powershell/module/azurerm.eventhub/) und [Azure CLI](/cli/azure/eventhubs/eventhub/) ausgeführt werden. Vorgänge zur Operatorverwaltung werden in PowerShell und Azure CLI derzeit nicht unterstützt.

## <a name="feature-documentation"></a>Featuredokumentation

Die [Azure Event Hubs-Dokumentation](/azure/event-hubs/) gilt für beide Editionen von Event Hubs: Event Hubs in Azure Stack Hub und Azure Event Hubs. Diese Dokumentation enthält Themen zur Verwendung von Event Hubs und Aktivitäten. Dazu gehören:

- Details zu [Event Hubs-Konzepten](/azure/event-hubs/event-hubs-features)
- [Erstellen eines Event Hubs-Clusters und -Namespace](event-hubs-quickstart-cluster-portal.md)
- Erstellen eines [Event Hubs](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- Streamen [mit dem Kafka-Protokoll](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Operator-Dokumentation 
 
Weitere Informationen zur Event Hubs in Azure Stack Hub-Operatorfunktionalität finden Sie im Artikel [Event Hubs in Azure Stack Hub – Übersicht](/azure-stack/operator/event-hubs-rp-overview). Diese Dokumentation enthält Informationen zu Aktivitäten wie:

- Installieren von Event Hubs
- Verfügbarmachen von Event Hubs für Benutzer
- Abrufen von Informationen zur Integrität des Diensts
- Sammeln von Protokollen


## <a name="next-steps"></a>Nächste Schritte

Ist Event Hubs in Ihrem Abonnement nicht verfügbar, [installieren Sie den Ressourcenanbieter „Event Hubs on Azure Stack Hub“ (Event Hubs in Azure Stack Hub)](../operator/event-hubs-rp-overview.md) in Zusammenarbeit mit Ihrem Administrator.

Wenn Sie Event Hubs installiert haben und verwenden möchten, lesen Sie die [Event Hubs-Dokumentation](/azure/event-hubs/event-hubs-about), um weitere Details zu dem Dienst zu erfahren.
