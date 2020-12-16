---
title: Anpassen von Anwendungen zur Verwendung in Kubernetes-Clustern mit gemischten Betriebssystemen
description: Hier erfahren Sie, wie Sie Knotenselektoren oder Taints und Toleranzen für Azure Kubernetes Service verwenden, um sicherzustellen, dass Anwendungen in Kubernetes-Clustern mit gemischten Betriebssystemen, die auf Azure Stack HCI ausgeführt werden, auf dem richtigen Workerknotenbetriebssystem geplant werden.
author: abha
ms.topic: how-to
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 0d4fd0e62e10e4afc4a76c9cac2deaed97e23549
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761557"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>Anpassen von Apps für Kubernetes-Cluster mit gemischten Betriebssystemen unter Verwendung von Knotenselektoren oder Taints und Toleranzen

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

Mit Azure Kubernetes Service in Azure Stack HCI können Sie Kubernetes-Cluster mit Linux- und Windows-Knoten ausführen. Für die Verwendung in diesen Clustern mit gemischten Betriebssystemen müssen Sie jedoch kleine Änderungen an Ihren Apps vornehmen. In dieser Schrittanleitung erfahren Sie, wie Sie sicherstellen können, dass Ihre Anwendung entweder mithilfe von Knotenselektoren oder Taints und Toleranzen auf dem richtigen Hostbetriebssystem geplant wird.

Für diese Schrittanleitung werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service in Azure Stack HCI](kubernetes-concepts.md).

## <a name="node-selector"></a>Knotenselektor

*Knotenselektor* ist ein einfaches Feld in der YAML der Podspezifikation, mit dem Pods nur auf fehlerfreien Knoten geplant werden können, die mit dem Betriebssystem übereinstimmen. Geben Sie in Ihrer YAML der Podspezifikation ein `nodeSelector` - Windows oder Linux an, wie in den folgenden Beispielen gezeigt. 

```yaml
kubernetes.io/os = Windows
```
oder

```yaml
kubernetes.io/os = Linux
```

Weitere Informationen zu „nodeSelectors“ finden Sie unter [Knotenselektoren](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/). 

## <a name="taints-and-tolerations"></a>Taints und Toleranzen

*Taints* und *Toleranzen* arbeiten zusammen, um sicherzustellen, dass Pods nicht unbeabsichtigt auf Knoten geplant werden. Ein Knoten kann „verfälscht“ sein, um keine Pods zu akzeptieren, die seinen Taint nicht ausdrücklich durch eine „Toleranz“ in der YAML der Podspezifikation zu tolerieren.

Windows-Betriebssystemknoten in Azure Kubernetes Service in Azure Stack HCI können mit dem folgenden Schlüssel-Wert-Paar verfälscht werden. Benutzer sollten kein anderes verwenden.

```yaml
node.kubernetes.io/os=Windows:NoSchedule
```
Führen Sie `kubectl get` aus, und identifizieren Sie die Windows-Workerknoten, die Sie verfälschen möchten.

```
kubectl get nodes --all-namespaces -o=custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.operatingSystem
```
Ausgabe:
```output
NAME                                     OS
my-aks-hci-cluster-control-plane-krx7j   linux
my-aks-hci-cluster-md-md-1-5h4bl         windows
my-aks-hci-cluster-md-md-1-5xlwz         windows
```

Taint-Windows-Serverworkerknoten, die `kubectl taint node` verwenden.

```
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

Sie geben eine Toleranz für einen Pod in der YAML der Podspezifikation an. Die folgende Toleranz „entspricht“ dem durch die obige kubectl-Taintzeile erstellten Taint, und somit könnte ein Pod mit der Toleranz auf „my-aks-hci-cluster-md-md-1-5h4bl“ oder „my-aks-hci-cluster-md-md-1-5xlwz“ geplant werden:

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
Weitere Informationen zu Taints und Toleranzen finden Sie unter [Taints und Toleranzen](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). 

## <a name="next-steps"></a>Nächste Schritte

In dieser Schrittanleitung haben Sie erfahren, wie Sie mithilfe von kubectl Knotenselektoren oder Taints und Toleranzen zu Ihren Kubernetes-Clustern hinzufügen können. Als Nächstes haben Sie folgende Möglichkeiten:
- [Bereitstellen von Linux-Anwendungen in einem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows Server-Anwendung in einem Kubernetes-Cluster](./deploy-windows-application.md).
