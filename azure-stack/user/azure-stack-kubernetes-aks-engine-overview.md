---
title: Was ist die AKS-Engine in Azure Stack? | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem AKS-Engine-Befehlszeilentool einen Kubernetes-Cluster in Azure und Azure Stack bereitstellen und verwalten.
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 93a835b6d3eff233ccbd421930f9618325126ea4
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73057775"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Was ist die AKS-Engine in Azure Stack?

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können mit dem AKS-Engine-Befehlszeilentool einen Kubernetes-Cluster in Azure und Azure Stack bereitstellen und verwalten. Verwenden Sie die AKS-Engine, um native Azure Resource Manager-Cluster zu erstellen, zu aktualisieren und zu skalieren. Sie können mit der Engine in verbundenen und nicht verbundenen Umgebungen einen Cluster bereitstellen. Dieser Artikel bietet eine Übersicht über die AKS-Engine, unterstützte Szenarien für die Verwendung der Engine mit Azure Stack und eine Einführung in Vorgänge wie Bereitstellung, Upgrade und Skalierung.

> [!IMPORTANT]
> Die AKS-Engine ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Übersicht über die AKS-Engine

Die [AKS-Engine](https://github.com/Azure/aks-engine) stellt ein Befehlszeilentool zum Bootstrap von Kubernetes-Clustern in Azure und Azure Stack bereit. Mithilfe des Azure Resource Managers unterstützt die AKS-Engine das Erstellen und Verwalten von Clustern, die auf virtuellen Computern, virtuellen Netzwerken und anderen IaaS-Ressourcen (Infrastructure-as-a-Service) in Azure Stack ausgeführt werden.

## <a name="aks-engine-on-azure-stack-considerations"></a>Überlegungen zur AKS-Engine in Azure Stack

Machen Sie sich vor der Verwendung der AKS-Engine in Azure Stack mit den Unterschieden zwischen Azure Stack und Azure vertraut. In diesem Abschnitt werden verschiedene Features und wichtige Aspekte der Verwaltung Ihres Kubernetes-Clusters mit der AKS-Engine in Azure Stack beschrieben.

Weitere Informationen zu den Besonderheiten der AKS-Engine in Azure Stack und den Unterschieden in Bezug auf Azure finden Sie unter [AKS Engine on Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md) (AKS-Engine in Azure Stack).

## <a name="supported-scenarios-with-the-aks-engine"></a>Mit der AKS-Engine unterstützte Szenarien

Das Azure Stack-Supportteam unterstützt folgende Szenarien:

1.  Die AKS-Engine stellt alle Clusterartefakte gemäß den Richtlinien in dieser Dokumentation und unter Verwendung der [folgenden Vorlage](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) bereit.
2.  Die AKS-Engine stellt den Cluster in einem vorhandenen VNET bereit. Weitere Informationen finden Sie unter [Using a custom virtual network with AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md) (Verwenden eines benutzerdefinierten virtuellen Netzwerks mit der AKS-Engine).
3.  [Upgrade-](azure-stack-kubernetes-aks-engine-upgrade.md) und [Skalierungsvorgänge](azure-stack-kubernetes-aks-engine-scale.md).

Weitere Informationen zur AKS-Engine und Azure Stack finden Sie unter [Supportrichtlinien für AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Installieren der AKS-Engine und Bereitstellen eines Kubernetes-Clusters

So stellen Sie einen Kubernetes-Cluster mit der AKS-Engine in Azure Stack bereit:

1. [Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-set-up.md)
2. Installieren Sie die AKS-Engine auf einem Computer mit Zugriff auf Ihre Azure Stack-Umgebung.
     - [Installieren der AKS-Engine in Windows in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Installieren der AKS-Engine in Linux in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-set-up.md)