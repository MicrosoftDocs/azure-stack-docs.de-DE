---
title: Planen der Kapazität für Event Hubs in Azure Stack Hub
description: Erfahren Sie, wie Sie die Kapazität für den Event Hubs-Ressourcenanbieter in Azure Stack Hub planen.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 08/15/2020
ms.openlocfilehash: 41ce43c3eda27d3ede8e6a90175fb3042fa2bf68
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343568"
---
# <a name="how-to-do-capacity-planning-for-event-hubs-on-azure-stack-hub"></a>Planen der Kapazität für Event Hubs in Azure Stack Hub

Als Operator verwalten Sie Ihre Azure Stack Hub-Kapazität mit [Kontingenten](azure-stack-quota-types.md) für Ressourcen. Sie steuern den Event Hubs-Ressourcenverbrauch, indem Sie Kontingente für die maximale Anzahl von Kernen festlegen, die von Event Hubs-Clustern verwendet werden können. Event Hubs-Cluster werden von Benutzern erstellt, wenn diese eine Event Hubs-Ressource bereitstellen. Es gibt auch verschiedene Ressourcenverbrauchsanforderungen für den Ressourcenanbieter, die in diesem Artikel behandelt werden.

## <a name="cluster-resource-consumption"></a>Clusterressourcenverbrauch

Um den Kapazitätsverbrauch von Event Hubs-Bereitstellungen zu verstehen, muss klar sein, dass Benutzer Event Hubs-Cluster auf Basis von Kapazitätseinheiten (Capacity Units, CUs) erstellen. Sie geben keine Anzahl der CPU-Kerne an, wenn sie einen Event Hubs-Clusters erstellen. Allerdings wird jede CU direkt einer bestimmten Anzahl von genutzten Kernen zugeordnet. 

Ihre Benutzer müssen Event Hubs-Cluster mit CUs erstellen, der die geschäftlichen Anforderungen der Benutzer erfüllen. Damit Sie Ihre Entscheidung über die Kontingentkonfiguration treffen können, wird in der folgenden Tabelle Folgendes angezeigt:
- Die Gesamtanzahl der Kerne, die von einem 1 CU Event Hubs-Cluster verwendet werden
- Die ungefähre Kapazität, die für den Verbrauch durch andere Ressourcen erforderlich ist, einschließlich VM-Speicher, Arbeitsspeicher und Speicherkonten

| | VM-Typ | Clusterknoten | Kerne pro VM/Knoten | Kerne insgesamt | VM-Speicher | Arbeitsspeicher | Speicherkonten | Öffentliche IP-Adressen |
|-|---------|-------|-------------------|-------------|------------|--------|------------------|---|
| **1 CU Event Hubs-Cluster** | [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) | 5 | 2 | 10 | 500 GiB | 70 GiB | 4 | 1 |

Für alle Event Hubs-Cluster wird ein [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2)-VM-Typ für deren Knoten verwendet. Ein D11_V2-VM-Typ besteht aus 2 Kernen. Für einen 1 CU Event Hubs-Cluster werden also 5 D11_V2-VMs verwendet, woraus sich 10 verwendete Kerne ergeben. Zur Entscheidung über die Anzahl von Kernen, die für ein Kontingent zu konfigurieren sind, verwenden Sie ein Vielfaches der Gesamtanzahl von Kernen, die für 1 CU verwendet werden. In dieser Berechnung wird die maximale CU-Anzahl berücksichtigt, die Sie Ihren Benutzern zugestehen, wenn sie Event Hubs-Cluster erstellen. Um beispielsweise ein Kontingent zu konfigurieren, das es Benutzern ermöglicht, einen Cluster mit einer Kapazität für 2 CUs zu erstellen, legen Sie Ihr Kontingent auf 20 Kerne fest.

[!INCLUDE [event-hubs-scale](../includes/event-hubs-scale.md)]

## <a name="resource-provider-resource-consumption"></a>Ressourcenverbrauch des Ressourcenanbieters  

Der Ressourcenverbrauch durch den Event Hubs Ressourcenanbieter ist konstant und unabhängig von der Anzahl oder Größe der Cluster, die von Benutzern erstellt wurden. In der folgenden Tabelle sind die Kernauslastung durch den Event Hubs-Ressourcenanbieter in Azure Stack Hub und der ungefähre Ressourcenverbrauch durch andere Ressourcen zusammengestellt. Für die Bereitstellung des Event Hubs-Ressourcenanbieters wird eine VM des Typs [D2_V2](../user/azure-stack-vm-sizes.md#dv2-series) verwendet.

|                                  | VM-Typ | Clusterknoten | Kerne | VM-Speicher | Arbeitsspeicher | Speicherkonten | Öffentliche IP-Adressen |
|----------------------------------|---------|---------------|-------|------------|--------|------------------|------------|
| **Event Hubs-Ressourcenanbieter** | D2_V2   | 3     | 6     | 300 GiB | 21 GiB | 2 | 1 |

> [!IMPORTANT]
> Der Ressourcenanbieterverbrauch wird nicht durch Kontingente gesteuert. Sie müssen die Kerne, die vom Ressourcenanbieter verwendet werden, nicht in ihren Kontingentkonfigurationen berücksichtigen. Ressourcenanbieter werden über ein Administratorabonnement installiert. Das Abonnement erzwingt keine Ressourcenverbrauchsgrenzwerte für Operatoren, wenn diese ihre erforderlichen Ressourcenanbieter installieren.

## <a name="total-resource-consumption"></a>Gesamtressourcenauslastung

Die Gesamtkapazität, die vom Event Hubs-Dienst beansprucht wird, umfasst den Ressourcenverbrauch durch den Ressourcenanbieter und den Verbrauch durch von Benutzern erstellte Cluster.

In der folgenden Tabelle ist der Event Hubs-Gesamtverbrauch für verschiedene Konfigurationen aufgeführt, unabhängig davon, ob diese per Kontingent verwaltet werden. Diese Zahlen basieren auf den oben vorgestellten Ressourcenanbieter- und Event Hubs-Clusterverbräuchen. Anhand dieser Beispiele können Sie problemlos die Azure Stack Hub-Gesamtauslastung für andere Bereitstellungsgrößen berechnen.

|                                      | Kerne | VM-Speicher | Arbeitsspeicher  | Speicherkonten | Gesamtspeicher\* | Öffentliche IP-Adressen\*\* |
|--------------------------------------|-------|------------|---------|------------------|---------------|------------|
| **1-CU-Cluster + Ressourcenanbieter** | 16    | 800 GiB    | 91 GiB  | 6                | Variable    | 2 |
| **2-CU-Cluster + Ressourcenanbieter** | 26    | 1,3 TB     | 161 GiB | 10               | Variable    | 2 |
| **4-CU-Cluster + Ressourcenanbieter** | 46    | 2,3 TB     | 301 GiB | 18               | Variable    | 2 |

\* Die Rate für den Eingangsdatenblock (Nachrichten/Ereignis-Rate) und die Nachrichtenaufbewahrung sind zwei wichtige Faktoren, die zum Speicherbedarf von Event Hubs-Clustern beitragen. Wird die Nachrichtenaufbewahrung bei der Erstellung eines Event Hubs z. B. auf 7 Tage festgelegt, und werden Nachrichten mit einer Rate von 1 MB/s erfasst, beträgt der verwendete Speicher ungefähr 604 GB (1 MB × 60 Sekunden × 60 Minuten x 24 Stunden x 7 Tage). Werden Nachrichten mit einer Rate von 20 MB/s bei einer Aufbewahrungsdauer von 7 Tagen gesendet, beträgt der ungefähre Speicherverbrauch 12 TB. Sie müssen unbedingt die Rate der eingehenden Daten und die Aufbewahrungszeit berücksichtigen, um die benötigte Speicherkapazität geeignet zu berechnen.

\*\* Öffentliche IP-Adressen werden aus dem [Netzwerkkontingent genutzt, das als Teil Ihres Abonnements bereitgestellt wird](azure-stack-quota-types.md#network-quota-types).

## <a name="next-steps"></a>Nächste Schritte

Führen Sie die im Artikel [Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub](event-hubs-rp-prerequisites.md) aufgeführten Schritte aus, bevor Sie mit dem Installationsvorgang beginnen.
