---
title: Bekannte Probleme mit der AKS-Engine auf Azure Stack Hub
description: Informationen zu bekannten Probleme bei Verwendung der AKS-Engine auf Azure Stack Hub
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 106aafd33441c2961cf933606cc8f48cbe4fc60d
ms.sourcegitcommit: b80d529ff47b15b8b612d8a787340c7b0f68165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473211"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Bekannte Probleme mit der AKS-Engine auf Azure Stack Hub

In diesem Thema werden bekannte Probleme der AKS-Engine auf Azure Stack Hub behandelt.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Upgradeprobleme in AKS Engine 0.51.0

* Während des Upgrades (aks-engine upgrade) eines Kubernetes-Clusters von Version 1.15.x auf 1.16.x sind für ein Upgrade der folgenden Kubernetes-Komponenten zusätzliche manuelle Schritte erforderlich: **kube-proxy**, **azure-cni-networkmonitor**, **csi-secrets-store**, **kubernetes-dashboard**. Nachfolgend wird beschrieben, welche Probleme möglicherweise auftreten und wie Sie diese umgehen können.

  * In verbundenen Umgebungen ist dieses Problem nicht offensichtlich, da es im Cluster keine Anzeichen dafür gibt, dass die betroffenen Komponenten nicht aktualisiert wurden. Alles funktioniert scheinbar wie erwartet.
  <!-- * In disconnected environments, you can see this problem when you run a query for the system pods status and see that the pods for the components mentioned below are not in "Ready" state: -->

    ```bash  
    kubectl get pods -n kube-system
    ```

  * Führen Sie als Problemumgehung für jeder dieser Komponenten den Befehl in der Spalte „Problemumgehung“ in der folgenden Tabelle aus.

    |Komponentenname |Problemumgehung |Betroffene Szenarien|
    |---------------|-----------|------------------|
    |**kube-proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Verbunden, nicht verbunden |
    |**azure-cni-networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Verbunden, nicht verbunden |
    |**csi-secrets-store**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Getrennt |
    |**kubernetes-dashboard** |Führen Sie den folgenden Befehl auf jedem Masterknoten aus:<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Getrennt |

* Kubernetes 1.17 wird in diesem Release nicht unterstützt. Obwohl es GitHub-Pullanforderungen (PR) gibt, die sich auf die Version 1.17 beziehen, wird diese nicht unterstützt.

## <a name="aks-engine-get-versions-command-limitations"></a>Einschränkungen im Zusammenhang mit dem Befehl „aks-engine get-versions“

Die Ausgabe des Befehls **aks-engine** `get-versions` bezieht sich allgemein auf Azure, nicht speziell auf Azure Stack Hub. Weitere Informationen zu den verschiedenen Upgradepfaden finden Sie unter [Schritte zum Durchführen eines Upgrades auf eine neuere Kubernetes-Version](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über AKS Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
