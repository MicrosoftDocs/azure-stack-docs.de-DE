---
title: Bereitstellen von Kubernetes für die Verwendung von Azure Stack-Containern | Microsoft-Dokumentation
description: Erfahren Sie, wie Kubernetes für die Verwendung von Containern in Azure Stack bereitgestellt wird.
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
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: e43ee8afd479d4bead1c513aee0229d8483fab33
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310446"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Bereitstellen von Kubernetes für die Verwendung von Containern in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!Note]  
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Ein Szenario mit nicht verbundenem Azure Stack wird von der Vorschau zurzeit nicht unterstützt.

Sie können die Schritte in diesem Artikel befolgen, um die Ressourcen für Kubernetes in einem einzigen, koordinierten Vorgang bereitzustellen und einzurichten. Die Schritte verwenden eine Azure Resource Manager-Lösungsvorlage. Sie müssen die erforderlichen Informationen zu Ihrer Azure Stack-Installation sammeln, die Vorlage generieren und dann die Bereitstellung in der Cloud ausführen. Die Azure Stack-Vorlage verwendet nicht denselben verwalteten AKS-Dienst, der in der globalen Azure-Umgebung angeboten wird.

## <a name="kubernetes-and-containers"></a>Kubernetes und Container

Sie können Kubernetes mithilfe von Azure Resource Manager-Vorlagen, die mit der ACS-Engine generiert wurden, in Azure Stack installieren. [Kubernetes](https://kubernetes.io) ist ein Open Source-System für die Automatisierung der Bereitstellung, Skalierung und Verwaltung von Anwendungen in Containern. Ein [Container](https://www.docker.com/what-container) befindet sich in einem Image. Das Containerimage ähnelt einer VM. Im Gegensatz zu einem virtuellen Computer enthält der Containe jedoch nur die Ressourcen, die er zum Ausführen einer Anwendung benötigt, etwa den Code, die Runtime zum Ausführen des Codes, bestimmte Bibliotheken und Einstellungen.

Sie können Kubernetes für Folgendes verwenden:

- Entwickeln hochgradig skalierbarer, aktualisierbarer Anwendungen, die in wenigen Sekunden bereitgestellt werden können 
- Vereinfachen des Entwurfs Ihrer Anwendung und Verbessern ihrer Zuverlässigkeit durch verschiedene Helm-Anwendungen. [Helm](https://github.com/kubernetes/helm) ist ein Open Source-Verpackungstool, das Ihnen dabei hilft, Kubernetes-Anwendungen zu installieren und ihren Lebenszyklus zu verwalten.
- Einfaches Überwachen und Diagnostizieren der Integrität Ihrer Anwendungen.

Ihnen werden lediglich die Kosten für die Computenutzung in Rechnung gestellt, die von den Knoten benötigt wird, die Ihren Cluster unterstützen. Weitere Informationen finden Sie unter [Verbrauch und Abrechnung in Azure Stack](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Bereitstellen von Kubernetes für die Verwendung von Containern

Die Schritte zum Bereitstellen eines Kubernetes-Clusters in Azure Stack richten sich nach Ihrem Identitätsverwaltungsdienst. Verifizieren Sie die von Ihrer Installation von Azure Stack verwendete Identitätsverwaltungslösung. Wenden Sie sich an ihren Azure Stack-Administrator, um Ihren Identitätsverwaltungsdienst zu verifizieren.

- **Azure Active Directory (Azure AD)**  
Anleitungen zum Installieren des Clusters bei Verwendung von Azure AD finden Sie unter [Bereitstellen von Kubernetes in Azure Stack mithilfe von Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory-Verbunddienste (AD FS)**  
Anleitungen zum Installieren des Clusters bei Verwendung von AD FS finden Sie unter [Bereitstellen von Kubernetes in Azure Stack mithilfe der Active Directory-Verbunddienste (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Herstellen einer Clusterverbindung

Sie können nun eine Verbindung mit Ihrem Cluster herstellen. Den Master mit dem Namen `k8s-master-<sequence-of-numbers>` finden Sie in Ihrer Clusterressourcengruppe. Verwenden Sie einen SSH-Client zum Herstellen einer Verbindung mit dem Master. Auf dem Master können Sie mit dem Kubernetes-Befehlszeilenclient **kubectl** Ihren Cluster verwalten. Anweisungen finden Sie unter [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Sie können zudem den **Helm**-Paket-Manager zum Installieren und Bereitstellen von Apps für Ihren Cluster verwenden. Anweisungen zur Installation und Verwendung von Helm mit Ihrem Cluster finden Sie unter [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren des Kubernetes-Dashboards](azure-stack-solution-template-kubernetes-dashboard.md)

[Hinzufügen von Kubernetes zum Marketplace (für den Azure Stack-Operator)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Bereitstellen von Kubernetes in Azure Stack mithilfe von Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Bereitstellen von Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
