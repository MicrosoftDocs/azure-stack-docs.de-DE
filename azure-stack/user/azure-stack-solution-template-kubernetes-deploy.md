---
title: Bereitstellen von Kubernetes für die Verwendung von Azure Stack Hub-Containern | Microsoft-Dokumentation
description: Erfahren Sie, wie Kubernetes für die Verwendung von Containern in Azure Stack Hub bereitgestellt wird.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 17d9c16a6ee95672450dcfed1dee66e21390e022
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536435"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack-hub"></a>Bereitstellen von Kubernetes für die Verwendung von Containern in Azure Stack Hub

> [!Note]  
> Verwenden Sie das Kubernetes-Azure Stack-Marketplace-Element nur, um Cluster als Proof of Concept bereitzustellen. Verwenden Sie für unterstützte Kubernetes-Cluster in Azure Stack [die AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md).

Sie können die Schritte in diesem Artikel befolgen, um die Ressourcen für Kubernetes in einem einzigen, koordinierten Vorgang bereitzustellen und einzurichten. Die Schritte verwenden eine Azure Resource Manager-Lösungsvorlage. Sie müssen die erforderlichen Informationen zu Ihrer Azure Stack Hub-Installation sammeln, die Vorlage generieren und dann die Bereitstellung in der Cloud ausführen. Die Azure Stack Hub-Vorlage verwendet nicht denselben verwalteten AKS-Dienst, der in der globalen Azure-Umgebung angeboten wird.

## <a name="kubernetes-and-containers"></a>Kubernetes und Container

Sie können Kubernetes mithilfe von Azure Resource Manager-Vorlagen, die von der AKS-Engine (Azure Kubernetes Service) generiert wurden, in Azure Stack Hub installieren. [Kubernetes](https://kubernetes.io) ist ein Open Source-System für die Automatisierung der Bereitstellung, Skalierung und Verwaltung von Anwendungen in Containern. Ein [Container](https://www.docker.com/what-container) befindet sich in einem Image. Das Containerimage ähnelt einem virtuellen Computer (VM). Im Gegensatz zu einer VM enthält der Container aber nur die Ressourcen, die er zum Ausführen einer Anwendung benötigt, z. B. den Code, die Runtime zum Ausführen des Codes, bestimmte Bibliotheken und Einstellungen.

Sie können Kubernetes für Folgendes verwenden:

- Entwickeln hochgradig skalierbarer, aktualisierbarer Anwendungen, die in wenigen Sekunden bereitgestellt werden können 
- Vereinfachen des Entwurfs Ihrer Anwendung und Verbessern ihrer Zuverlässigkeit durch verschiedene Helm-Anwendungen. [Helm](https://github.com/kubernetes/helm) ist ein Open Source-Verpackungstool, das Ihnen dabei hilft, Kubernetes-Anwendungen zu installieren und ihren Lebenszyklus zu verwalten.
- Einfaches Überwachen und Diagnostizieren der Integrität Ihrer Anwendungen.

Ihnen werden lediglich die Kosten für die Computenutzung in Rechnung gestellt, die von den Knoten benötigt wird, die Ihren Cluster unterstützen. Weitere Informationen finden Sie unter [Verbrauch und Abrechnung in Azure Stack Hub](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Bereitstellen von Kubernetes für die Verwendung von Containern

Die Schritte zum Bereitstellen eines Kubernetes-Clusters in Azure Stack Hub richten sich nach Ihrem Identitätsverwaltungsdienst. Verifizieren Sie die von Ihrer Installation von Azure Stack Hub verwendete Identitätsverwaltungslösung. Wenden Sie sich an ihren Azure Stack Hub-Administrator, um Ihren Identitätsverwaltungsdienst zu verifizieren.

- **Azure Active Directory (Azure AD)**  
Anleitungen zum Installieren des Clusters bei Verwendung von Azure AD finden Sie unter [Bereitstellen von Kubernetes in Azure Stack Hub mithilfe von Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory-Verbunddienste (AD FS)**  
Anleitungen zum Installieren des Clusters bei Verwendung von AD FS finden Sie unter [Bereitstellen von Kubernetes in Azure Stack Hub mithilfe der Active Directory-Verbunddienste (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Herstellen einer Clusterverbindung

Sie können nun eine Verbindung mit Ihrem Cluster herstellen. Den Master mit dem Namen `k8s-master-<sequence-of-numbers>` finden Sie in Ihrer Clusterressourcengruppe. Verwenden Sie einen SSH-Client zum Herstellen einer Verbindung mit dem Master. Auf dem Master können Sie mit dem Kubernetes-Befehlszeilenclient **kubectl** Ihren Cluster verwalten. Anweisungen finden Sie unter [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Sie können zudem den **Helm**-Paket-Manager zum Installieren und Bereitstellen von Apps für Ihren Cluster verwenden. Anweisungen zur Installation und Verwendung von Helm mit Ihrem Cluster finden Sie unter [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren des Kubernetes-Dashboards](azure-stack-solution-template-kubernetes-dashboard.md)

[Hinzufügen von Kubernetes zum Marketplace (für den Azure Stack Hub-Operator)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Bereitstellen von Kubernetes in Azure Stack Hub mithilfe von Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Bereitstellen von Kubernetes in Azure Stack Hub mithilfe der Active Directory-Verbunddienste (Active Directory Federated Services, AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
