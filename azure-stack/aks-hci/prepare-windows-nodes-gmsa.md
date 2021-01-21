---
title: Vorbereiten von Windows-Knoten für die Unterstützung von gruppenverwalteten Dienstkonten
description: Erfahren Sie, wie Sie gruppenverwaltete Dienstkonten für Container auf Windows-Knoten konfigurieren.
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: fd08f288d816468610dadf7e5c063c08b63c0cf2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254703"
---
# <a name="prepare-windows-nodes-for-group-managed-service-account-support"></a>Vorbereiten von Windows-Knoten für die Unterstützung von gruppenverwalteten Dienstkonten

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

Gruppenverwaltete Dienstkonten sind eine bestimmte Art von Active Directory-Konto, das Folgendes bietet:
- Automatische Kennwortverwaltung
- Vereinfachte Verwaltung von Dienstprinzipalnamen (Service Principal Name, SPN)
- Die Möglichkeit, über mehrere Server hinweg die Verwaltung an andere Administratoren zu delegieren 

Zum Konfigurieren von gruppenverwalteten Dienstkonten (gMSA) für Pods und Container, die auf Ihren Windows-Knoten ausgeführt werden sollen, müssen Sie Ihre Windows-Knoten zunächst mit einer Active Directory-Domäne verknüpfen.

Damit die Unterstützung von gruppenverwalteten Dienstkonten ermöglicht wird, muss der Name Ihres Kubernetes-Clusters aus weniger als vier Zeichen bestehen. Er muss weniger als vier Zeichen enthalten, da die maximal unterstützte Länge für einen mit einer Domäne verbundenen Servernamen 15 Zeichen beträgt. Die Kubernetes-Clusternamenskonvention für AKS in Azure Stack HCI für einen Workerknoten fügt einem Knotennamen einige vordefinierte Zeichen hinzu.

## <a name="join-the-worker-nodes-to-a-domain"></a>Verknüpfen der Workerknoten mit einer Domäne

Um Ihre Windows-Workerknoten mit einer Domäne zu verknüpfen, melden Sie sich bei einem Windows-Workerknoten an, indem Sie `kubectl get` ausführen und den Wert `EXTERNAL-IP` notieren.

```
kubectl get nodes -o wide
```  

Sie können dann mithilfe von `ssh Administrator@ip` per SSH auf den Knoten gelangen. Weitere Informationen zum Anmelden mit SSH finden Sie unter [Problembehandlung für Windows-Workerknoten mithilfe von SSH](troubleshoot.md#troubleshooting-windows-worker-nodes).

Führen Sie nach erfolgreicher Anmeldung bei Ihrem Windows-Workerknoten über SSH den folgenden Befehl aus, um den Knoten in eine Domäne einzubinden. Anschließend müssen Sie den Windows-Workerknoten neu starten. 

```
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password
```  

Nachdem alle Windows-Workerknoten mit einer Domäne verknüpft wurden, führen Sie die unter [Konfigurieren von gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/) beschriebenen Schritte aus. Mit diesen Schritten werden die benutzerdefinierten Kubernetes-gMSA-Ressourcendefinitionen und Webhooks auf Ihren Kubernetes-Cluster angewendet.

Weitere Informationen zu Windows-Containern mit gMSA finden Sie unter [Windows-Container und gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure Monitor zum Überwachen Ihres Clusters und Ihrer Anwendung](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Verwenden eines permanenten Volumes für einen Kubernetes-Cluster](persistent-volume.md).