---
title: Bekannte Probleme mit der AKS-Engine auf Azure Stack Hub
description: Informationen zu bekannten Probleme bei Verwendung der AKS-Engine auf Azure Stack Hub
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: e1a7768eee19fb8a2246ec8c3934f5742d23534c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874165"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Bekannte Probleme mit der AKS-Engine auf Azure Stack Hub

In diesem Thema werden bekannte Probleme der AKS-Engine auf Azure Stack Hub behandelt.

## <a name="unable-to-resize-cluster-vms-with-the-compute-service"></a>Die Größe von Cluster-VMs kann mit dem Computedienst nicht geändert werden

- **Geltungsbereich**: Azure Stack Hub, AKS-Engine (alle)
- **Beschreibung**: Das Ändern der Größe von Cluster-VMs mit dem Computedienst funktioniert nicht mit der AKS-Engine. Die AKS-Engine verwaltet den Zustand des Clusters in der JSON-Datei des API-Modells. Um sicherzustellen, dass sich die gewünschte VM-Größe in jedem Erstellungs-, Aktualisierungs- oder Skalierungsvorgang, der mit der AKS-Engine durchgeführt wird, widergespiegelt, müssen Sie das API-Modell vor dem Ausführen jedes dieser Vorgänge aktualisieren. Wenn Sie z. B. eine VM-Größe auf einem bereits bereitgestellten Cluster mit dem Computedienst in eine andere Größe ändern, geht der Zustand beim Ausführen von `aks-engine update` verloren.
- **Abhilfe**: Damit dies funktioniert, suchen Sie das API-Modell für den Cluster, ändern Sie dort die Größe, und führen Sie dann `aks-engine update` aus.
- **Häufigkeit**: Bei dem Versuch, die Größe mit dem Computedienst zu ändern.

## <a name="disk-detach-operation-fails-in-aks-engine-0550"></a>Fehler beim Trennen des Datenträgers in der AKS-Engine 0.55.0

- **Geltungsbereich**: Azure Stack Hub (Update 2005), AKS-Engine 0.55.0
- **Beschreibung**: Wenn Sie versuchen, eine Bereitstellung zu löschen, die Persistenzvolumes enthält, löst der Löschvorgang eine Reihe von Fehlern beim Anfügen/Trennen aus. Der Grund hierfür ist ein Fehler im Cloudanbieter der AKS-Engine v0.55.0. Der Cloudanbieter ruft den Azure Resource Manager unter Verwendung einer Version der API auf, die neuer ist als die, die aktuell von Azure Resource Manager in Azure Stack Hub unterstützt (Update 2005) wird.
- **Abhilfe**: Die Details und Korrekturschritte finden Sie im [GitHub-Repository der AKS-Engine (Issue 3817)](https://github.com/Azure/aks-engine/issues/3817#issuecomment-691329443). Führen Sie ein Upgrade durch, sobald ein neuer Build der AKS-Engine und das entsprechende Image verfügbar sind.
- **Häufigkeit**: Beim Löschen einer Bereitstellung, die Persistenzvolumes enthält.



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
