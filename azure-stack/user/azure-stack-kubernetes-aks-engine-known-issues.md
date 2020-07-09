---
title: Bekannte Probleme mit der AKS-Engine auf Azure Stack Hub
description: Informationen zu bekannten Probleme bei Verwendung der AKS-Engine auf Azure Stack Hub
author: mattbriggs
ms.topic: article
ms.date: 07/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 07/02/2020
ms.openlocfilehash: 4012f350fff45e3d09b543692fa315ffeeec1240
ms.sourcegitcommit: a5bb340c5689f7dcf1ef3a340416f7f337782170
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2020
ms.locfileid: "85942305"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Bekannte Probleme mit der AKS-Engine auf Azure Stack Hub

In diesem Thema werden bekannte Probleme der AKS-Engine auf Azure Stack Hub behandelt.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Upgradeprobleme in AKS Engine 0.51.0

* Während des Upgrades (aks-engine upgrade) eines Kubernetes-Clusters von Version 1.15.x auf 1.16.x sind für ein Upgrade der folgenden Kubernetes-Komponenten zusätzliche manuelle Schritte erforderlich: **kube-proxy**, **azure-cni-networkmonitor**, **csi-secrets-store**, **kubernetes-dashboard**. Nachfolgend wird beschrieben, welche Probleme möglicherweise auftreten und wie Sie diese umgehen können.

  * In verbundenen Umgebungen ist dieses Problem nicht offensichtlich, da es im Cluster keine Anzeichen dafür gibt, dass die betroffenen Komponenten nicht aktualisiert wurden. Alles funktioniert scheinbar wie erwartet.
  * In nicht verbundenen Umgebungen können Sie dieses Problem erkennen, wenn Sie den Status der Systempods abfragen und feststellen, dass die Pods für die unten aufgeführten Komponenten nicht den Status „Bereit“ aufweisen:

    ```PowerShell
    kubectl get pods -n kube-system
    ```

  * Führen Sie als Problemumgehung für jeder dieser Komponenten den Befehl in der Spalte „Problemumgehung“ in der folgenden Tabelle aus.

    |Komponentenname |Problemumgehung |Betroffene Szenarien|
    |---------------|-----------|------------------|
    |**kube-proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Verbunden, nicht verbunden |
    |**azure-cni-networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Verbunden, nicht verbunden |
    |**csi-secrets-store**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Getrennt |
    |**kubernetes-dashboard** |Führen Sie den folgenden Befehl auf jedem Masterknoten aus:<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Getrennt |

* Kubernetes 1.17 wird in diesem Release nicht unterstützt. Einige PRs verweisen zwar auf 1.17, diese Version wird jedoch nicht unterstützt.

## <a name="basic-load-balancer-sku-limitations"></a>Einschränkungen der Load Balancer Basic-SKU

* Einschränkung eines einzelnen Agentpools. Derzeit unterstützt Azure Stack Hub nur die Load Balancer Basic-SKU. Diese SKU [beschränkt](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) die Back-End-Poolendpunkte auf virtuelle Computer in einer einzelnen Verfügbarkeitsgruppe (oder VM-Skalierungsgruppe sein). Dies bedeutet, dass alle Replikate eines LoadBalancer-Diensts im selben Agentpool bereitgestellt werden müssen. Außerdem bedeutet es, dass jeder einzelne Cluster entweder einen Linux-LoadBalancer-Dienst oder einen Windows-LoadBalancer-Dienst enthalten kann.

  Sie können Kubernetes zwingen, Pods in einem bestimmten Agentpool zu erstellen, indem Sie die [Knotenauswahl](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) „agentpool: MY_POOL_NAME“ in Ihrer Podvorlage hinzufügen.

  ```powershell
  nodeSelector:

        agentpool: linuxpool
  ```
  
  Wenn bereits ein LoadBalancer-Dienst in Ihrem Cluster erstellt wurde, können Sie herausfinden, welcher Agentpool als Back-End-Pool des Lastenausgleichs ausgewählt wurde, indem Sie das Blatt mit den Back-End-Pools des Lastenausgleichs im Azure Stack Hub-Portal überprüfen. Sobald Sie über diese Informationen verfügen, können Sie den Ziel-Agentpool angeben, indem Sie die YAML-Datei der Bereitstellung/des Pods (wie im vorherigen Absatz erläutert) aktualisieren.

* Befehlsbereich für `get-versions`. Die Ausgabe des `get-versions`-Befehls bezieht sich nur auf Azure und nicht auf Azure Stack Hub-Clouds. Weitere Informationen zu den verschiedenen Upgradepfaden finden Sie unter [Schritte zum Durchführen eines Upgrades auf eine neuere Kubernetes-Version](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über AKS Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
