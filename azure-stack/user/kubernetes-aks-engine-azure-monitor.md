---
title: Verwenden von Azure Monitor für Container in Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Monitor für Container in Azure Stack Hub verwenden.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: a3941a3ada52a8588b504884a2d03cb00dd2c850
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310315"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>Verwenden von Azure Monitor für Container in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) für Container verwenden, um Ihre Container in einem von der AKS-Engine bereitgestellten Kubernetes-Cluster in Azure Stack Hub zu überwachen. 

> [!IMPORTANT]
> Azure Monitor für Container in Azure Stack Hub ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können die Containerleistung mit Azure Monitor überprüfen, indem Sie Arbeitsspeicher- und Prozessormetriken von Controllern, Knoten und Containern erfassen, die in Kubernetes über die Metrik-API verfügbar sind. Außerdem sammelt der Dienst Containerprotokolle. Mit diesen Protokollen können Sie Probleme in Ihrem lokalen Cluster in Azure diagnostizieren. Nachdem Sie die Überwachung über Ihre Kubernetes-Cluster eingerichtet haben, werden diese Metriken und Protokolle automatisch erfasst. Eine Containerversion des Azure Monitor Log Analytics-Agents für Linux sammelt die Protokolle. Azure Monitor speichert die Metriken und Protokolle in Ihrem Log Analytics-Arbeitsbereich, auf den Sie in Ihrem Azure-Abonnement zugreifen können.

Um Azure Monitor für Ihren Cluster zu aktivieren, stehen Ihnen zwei Methoden zur Auswahl. Beide Methoden erfordern, dass Sie einen Azure Monitor Log Analytics-Arbeitsbereich in Azure einrichten.

## <a name="prerequisites"></a>Voraussetzungen

Für beide Methoden gelten die unter [Azure Monitor – Container](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers) aufgeführten [Voraussetzungen](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites).

## <a name="method-one"></a>Methode 1

Sie können auch das [Helm](https://helm.sh/)-Diagramm verwenden, um die Überwachungs-Agents in Ihrem Cluster zu installieren. Folgen Sie den Anweisungen im Artikel [Azure Monitor – Container](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-two"></a>Methode 2

Sie können ein **Add-On** in der JSON-Datei mit der Clusterspezifikation der AKS-Engine angeben. Diese Datei wird auch als API-Modell bezeichnet. Geben Sie in diesem Add-On die Base64-codierte Version von **WorkspaceGUID** und **WorkspaceKey** des Azure Log Analytics-Arbeitsbereichs an, in dem die Überwachungsinformationen gespeichert werden.

Unterstützte API-Definitionen für den Azure Stack Hub-Cluster finden Sie in diesem Beispiel: [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Suchen Sie nach der **addons**-Eigenschaft im Abschnitt **kubernetesConfig**:

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).  
- Lesen Sie [Azure Monitor für Container – Übersicht](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).
