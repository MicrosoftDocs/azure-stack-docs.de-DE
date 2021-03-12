---
title: Was ist die AKS-Engine in Azure Stack Hub?
description: Erfahren Sie, wie Sie mit dem AKS-Engine-Befehlszeilentool einen Kubernetes-Cluster in Azure und Azure Stack Hub bereitstellen und verwalten.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 42ccc5c8858bf113b7308e4375ee3261a0a96a1f
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231488"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Was ist die AKS-Engine in Azure Stack Hub?

Sie können mit dem AKS-Engine-Befehlszeilentool einen Kubernetes-Cluster in Azure und Azure Stack Hub bereitstellen und verwalten. Verwenden Sie die AKS-Engine, um native Azure Resource Manager-Cluster zu erstellen, zu aktualisieren und zu skalieren. Sie können mit der Engine in verbundenen und nicht verbundenen Umgebungen einen Cluster bereitstellen. Dieser Artikel bietet eine Übersicht über die AKS-Engine, unterstützte Szenarien für die Verwendung der Engine mit Azure Stack Hub und eine Einführung in Vorgänge wie Bereitstellung, Upgrade und Skalierung.

## <a name="overview-of-the-aks-engine"></a>Übersicht über die AKS-Engine

Die [AKS-Engine](https://github.com/Azure/aks-engine) stellt ein Befehlszeilentool zum Bootstrap von Kubernetes-Clustern in Azure und Azure Stack Hub bereit. Mithilfe von Azure Resource Manager unterstützt die AKS-Engine das Erstellen und Verwalten von Clustern, die auf virtuellen Computern, virtuellen Netzwerken und anderen IaaS-Ressourcen (Infrastructure-as-a-Service) in Azure Stack Hub ausgeführt werden.

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>Überlegungen zur AKS-Engine in Azure Stack Hub

Machen Sie sich vor der Verwendung der AKS-Engine in Azure Stack Hub mit den Unterschieden zwischen Azure Stack Hub und Azure vertraut. In diesem Abschnitt werden verschiedene Features und wichtige Aspekte der Verwaltung Ihres Kubernetes-Clusters mit der AKS-Engine in Azure Stack Hub beschrieben.

Weitere Informationen zu den Besonderheiten der AKS-Engine in Azure Stack Hub und den Unterschieden in Bezug auf Azure finden Sie unter [AKS Engine on Azure Stack Hub](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md) (AKS-Engine in Azure Stack Hub).

Weitere Informationen zur AKS-Engine und Azure Stack Hub finden Sie unter [Supportrichtlinien für AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Installieren der AKS-Engine und Bereitstellen eines Kubernetes-Clusters

So stellen Sie einen Kubernetes-Cluster mit der AKS-Engine in Azure Stack Hub bereit:

1. [Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-set-up.md)
2. Installieren Sie die AKS-Engine auf einem Computer mit Zugriff auf Ihre Azure Stack Hub-Umgebung.
     - [Installieren der AKS-Engine unter Windows in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Installieren der AKS-Engine unter Linux in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-set-up.md)