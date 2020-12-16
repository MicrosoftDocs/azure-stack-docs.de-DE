---
title: Konzepte – Grundlagen zu Kubernetes für Azure Kubernetes Service (AKS) in Azure Stack HCI
description: Lernen Sie die grundlegenden Cluster- und Workloadkomponenten von Kubernetes kennen, und erfahren Sie, wie diese mit den Features in Azure Kubernetes Service in Azure Stack HCI in Zusammenhang stehen.
author: daschott
ms.author: daschott
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 9870f2921be561a8fef66f1bfc530b5c18f494b4
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612640"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-on-azure-stack-hci"></a>Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service in Azure Stack HCI

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

Azure Kubernetes Service in Azure Stack HCI ist eine für Unternehmen konzipierte Kubernetes-Containerplattform, die von Azure Stack HCI unterstützt wird. Es umfasst von Microsoft unterstütztes grundlegendes Kubernetes, Add-Ons, einen speziell erstellten Windows-Containerhost und einen von Microsoft unterstützten Linux-Containerhost mit dem Ziel, über eine **einfache Bereitstellung und Umgebung für die Lebenszyklusverwaltung zu verfügen**.

In diesem Artikel werden die grundlegenden Kubernetes-Infrastrukturkomponenten wie Steuerungsebene, Knoten und Knotenpools vorgestellt. Darüber hinaus werden Workloadressourcen wie Pods, Bereitstellungen und Sets erläutert, und es wird beschrieben, wie Sie Ressourcen in Namespaces gruppieren.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-Cluster – Architektur
Kubernetes ist die Kernkomponente von Azure Kubernetes Service in Azure Stack HCI. Azure Kubernetes Service in Azure Stack HCI verwendet eine Reihe von vordefinierten Konfigurationen, um Kubernetes-Cluster effektiv und unter Berücksichtigung der Skalierbarkeit bereitzustellen.
 
Der Bereitstellungsvorgang erstellt mehrere virtuelle Linux- oder Windows-Computer und verknüpft diese zu Kubernetes-Clustern. 
 
Das bereitgestellte System ist bereit, standardmäßige Kubernetes-Workloads zu empfangen, diese Workloads oder sogar die Anzahl der virtuellen Computer sowie die Anzahl der Cluster nach Bedarf herauf- oder herunterzuskalieren.

Ein Azure Kubernetes Service-Cluster ist in Azure Stack HCI in zwei Hauptkomponenten unterteilt:

- Der *Verwaltungscluster* stellt den zentralen Orchestrierungsmechanismus und die Schnittstelle für die Bereitstellung und Verwaltung eines oder mehrerer Zielcluster dar.
- *Zielcluster* (auch als Workloadcluster bezeichnet) sind Cluster, in denen Anwendungsworkloads ausgeführt und von einem Verwaltungscluster verwaltet werden.

![Veranschaulicht die technische Architektur von Azure Kubernetes Service in Azure Stack HCI](.\media\concepts\architecture.png)

## <a name="management-cluster"></a>Verwaltungscluster
Wenn Sie einen Azure Kubernetes Service-Cluster in Azure Stack HCI erstellen, wird automatisch ein Verwaltungscluster erstellt und konfiguriert. Dieser Verwaltungscluster ist zuständig für die Bereitstellung und Verwaltung von Zielclustern, in denen Workloads ausgeführt werden. Ein Verwaltungscluster umfasst die folgenden Kubernetes-Kernkomponenten:
- *API-Server* – Über den API-Server werden die zugrunde liegenden Kubernetes-APIs verfügbar gemacht. Diese Komponente bietet die Interaktion für Verwaltungstools wie Windows Admin Center, PowerShell-Module oder `kubectl`.
- *Lastenausgleich* – Der Lastenausgleich ist eine einzelne dedizierte Linux-VM mit einer Lastenausgleichsregel für den API-Server des Verwaltungsclusters.

### <a name="windows-admin-center"></a>Windows Admin Center
Windows Admin Center bietet eine intuitive Benutzeroberfläche für den Kubernetes-Operator zur Verwaltung des Lebenszyklus von Azure Kubernetes Service-Clustern in Azure Stack HCI.

### <a name="powershell-module"></a>PowerShell-Modul
Das PowerShell-Modul ist eine einfache Möglichkeit, Azure Kubernetes Service in Azure Stack HCI herunterzuladen, zu konfigurieren und bereitzustellen. Das PowerShell-Modul unterstützt auch die Bereitstellung und Konfiguration zusätzlicher Zielcluster sowie die Neukonfiguration bestehender Cluster.

## <a name="target-cluster"></a>Zielcluster
Der Zielcluster (Workloadcluster) ist eine Bereitstellung von Kubernetes mit Hochverfügbarkeit unter Verwendung von Linux-VMs zur Ausführung von Komponenten der Kubernetes-Steuerungsebene sowie von Linux-Workerknoten. Windows Server Core-basierte VMs werden zur Einrichtung von Windows-Workerknoten verwendet. Ein Verwaltungscluster kann einen oder mehrere Zielcluster verwalten.

### <a name="worker-nodes"></a>Workerknoten
Zum Ausführen Ihrer Anwendungen und der unterstützenden Dienste benötigen Sie einen Kubernetes-Knoten. Ein Azure Kubernetes Service-Zielcluster in Azure Stack HCI verfügt über mindestens einen Workerknoten, d. h. einen virtuellen Computer (VM), auf dem die Kubernetes-Knotenkomponenten ausgeführt werden und der die Pods und Dienste hostet, die die Workload der Anwendung ausmachen.

### <a name="load-balancer"></a>Load Balancer
Der Lastenausgleich ist ein virtueller Computer mit Linux und HAProxy sowie KeepAlive zur Bereitstellung von Diensten für den Lastenausgleich für die vom Verwaltungscluster bereitgestellten Zielcluster.

Für jeden Zielcluster fügt der Azure Kubernetes Service in Azure Stack HCI mindestens einen virtuellen Computer für den Lastenausgleich (LB VM) hinzu. Darüber hinaus kann ein weiterer Lastenausgleich für die Hochverfügbarkeit des API-Servers auf dem Zielcluster erstellt werden. Jeder Kubernetes-Dienst vom Typ `LoadBalancer`, der auf dem Zielcluster erstellt wird, erstellt am Ende eine Lastenausgleichsregel auf dem virtuellen Computer für den Lastenausgleich (LB VM).

### <a name="add-on-components"></a>Add-On-Komponenten
Es gibt mehrere optionale Add-On-Komponenten, die in jedem beliebigen Cluster bereitgestellt werden können, aber vor allem in den Folgenden: Azure Arc, Prometheus, Grafana oder Kubernetes-Dashboard.

## <a name="kubernetes-components"></a>Kubernetes-Komponenten
In diesem Abschnitt werden die Kernkomponenten der Kubernetes-Workload vorgestellt, die auf Zielclustern von Azure Kubernetes Service in Azure Stack HCI bereitgestellt werden, z. B. Pods, Bereitstellungen und Gruppen. Außerdem enthält der Artikel Informationen zum Gruppieren von Ressourcen in Namespaces.

### <a name="pods"></a>Pods

Kubernetes verwendet *Pods*, um eine Instanz Ihrer Anwendung auszuführen. Ein Pod repräsentiert eine einzelne Instanz der Anwendung. Pods weisen in der Regel eine 1:1-Zuordnung mit einem Container auf. Es gibt jedoch auch erweiterte Szenarien, in denen ein Pod mehrere Container enthalten kann. Diese Pods mit mehreren Containern werden zusammen auf dem gleichen Knoten geplant und ermöglichen es Containern, zugehörige Ressourcen gemeinsam zu nutzen.

Wenn Sie einen Pod erstellen, können Sie *Ressourcenanforderungen* definieren, um eine bestimmte Menge an CPU- oder Arbeitsspeicherressourcen anzufordern. Der Kubernetes Scheduler versucht, die Ausführung der Pods auf einem Knoten mit den verfügbaren Ressourcen zu planen, um die Anforderung zu erfüllen. Sie können auch maximale Ressourcenlimits angeben, um zu verhindern, dass ein bestimmter Pod zu viele Computeressourcen des zugrunde liegenden Knotens verbraucht. Eine bewährte Methode ist es, Ressourcenlimits für alle Pods einzurichten, um den Kubernetes Scheduler darüber zu informieren, welche Ressourcen benötigt werden und zulässig sind.

Weitere Informationen finden Sie unter [Kubernetes Pods][kubernetes-pods] (Kubernetes-Pods) und [Kubernetes Pod Lifecycle][kubernetes-pod-lifecycle] (Lebenszyklus von Kubernetes-Pods).

Ein Pod ist eine logische Ressource, aber die Container sind die Ressourcen, in denen die Anwendungsworkloads ausgeführt werden. Pods sind in der Regel kurzlebige Ressourcen, die gelöscht werden können. Einzeln geplante Pods bieten nicht alle Hochverfügbarkeits- und Redundanzfeatures von Kubernetes. Stattdessen werden Pods von Kubernetes-*Controllern* bereitgestellt und verwaltet, beispielsweise dem Bereitstellungscontroller.

### <a name="deployments-and-yaml-manifests"></a>Bereitstellungen und YAML-Manifeste

Eine *Bereitstellung* repräsentiert einen oder mehrere identische Pods, die vom Kubernetes-Bereitstellungscontroller verwaltet werden. Eine Bereitstellung definiert die Anzahl von *Replikaten* (Pods), die erstellt werden sollen. Der Kubernetes Scheduler stellt sicher, dass weitere Pods auf intakten Knoten geplant werden, falls Probleme mit Pods oder Knoten auftreten sollten.

Sie können Bereitstellungen aktualisieren, um die Konfiguration von Pods, das verwendete Containerimage oder den angeschlossenen Speicher zu ändern. Der Bereitstellungscontroller leert und beendet eine bestimmte Anzahl von Replikaten, erstellt Replikate gemäß der neuen Bereitstellungsdefinition und setzt den Prozess so lange fort, bis alle Replikate in der Bereitstellung aktualisiert wurden.

Die meisten zustandslosen Anwendungen sollten das Bereitstellungsmodell verwenden, anstatt einzelne Pods zu planen. Kubernetes kann die Integrität und den Status von Bereitstellungen überwachen, um sicherzustellen, dass die erforderliche Anzahl von Replikaten im Cluster ausgeführt wird. Wenn Sie nur einzelne Pods planen, werden diese nach dem Auftreten eines Problems nicht neu gestartet. Außerdem werden die Pods nicht auf intakten Knoten erneut geplant, wenn auf dem aktuellen Knoten ein Problem auftritt.

Wenn eine Anwendung erfordert, dass jederzeit ein Quorum aus Instanzen verfügbar ist, damit Verwaltungsentscheidungen getroffen werden können, darf diese Funktionalität nicht durch Aktualisierungsprozesse unterbrochen werden. *Budgets für die Unterbrechung von Pods* können verwendet werden, um zu definieren, wie viele Replikate in einer Bereitstellung während einer Aktualisierung oder eines Knotenupgrades heruntergefahren werden können. Ein Beispiel: Wenn Sie in Ihrer Bereitstellung über *5* Replikate verfügen, können Sie eine Podunterbrechung für *4* Replikate definieren, damit nur ein Replikat gleichzeitig gelöscht bzw. neu geplant werden darf. Ebenso wie bei Podressourcenlimits besteht eine bewährte Methode darin, Budgets für die Unterbrechung von Pods für Anwendungen zu definieren, für die immer eine bestimmte Mindestanzahl von Replikaten vorhanden sein muss.

Bereitstellungen werden in der Regel mit `kubectl create` oder `kubectl apply` erstellt und verwaltet. Um eine Bereitstellung zu erstellen, definieren Sie eine Manifestdatei im YAML-Format (YAML Ain't Markup Language). Das folgende Beispiel erstellt eine grundlegende Bereitstellung eines NGINX-Webservers. Die Bereitstellung gibt an, dass *3* Replikate erstellt werden sollen und dass Port *80* auf dem Container geöffnet sein muss. Es werden auch Ressourcenanforderungen und -limits für CPU und Arbeitsspeicher definiert.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Indem Sie Dienste wie Lastenausgleichsmodule im YAML-Manifest angeben, können Sie auch komplexere Anwendungen erstellen.

Weitere Informationen finden Sie unter [Kubernetes-Bereitstellungen][kubernetes-deployments].

##### <a name="mixed-os-deployments"></a>Bereitstellungen mit gemischten Betriebssystemen

Wenn ein bestimmter Zielcluster von Azure Kubernetes Service in Azure Stack HCI sowohl aus Linux- als auch aus Windows-Workerknoten besteht, müssen Workloads auf einem Betriebssystem geplant werden, das die Bereitstellung der Workload unterstützen kann. Kubernetes bietet zwei Mechanismen, um sicherzustellen, dass Workloads auf Knoten mit einem Zielbetriebssystem landen:

- *Knotenselektor* ist ein einfaches Feld in der Podspezifikation, mit dem Pods nur auf fehlerfreien Knoten geplant werden können, die mit dem Betriebssystem übereinstimmen. 
- *Taints und Toleranzen* arbeiten zusammen, um sicherzustellen, dass Pods nicht unbeabsichtigt auf Knoten geplant werden. Ein Knoten kann „verfälscht“ sein, um keine Pods zu akzeptieren, die seinen Taint nicht ausdrücklich durch eine „Toleranz“ in der Podspezifikation zu tolerieren.

Weitere Informationen finden Sie unter [Knotenselektoren][node-selectors] und [Taints und Toleranzen][taints-tolerations].

### <a name="statefulsets-and-daemonsets"></a>StatefulSets und DaemonSets

Der Bereitstellungscontroller verwendet den Kubernetes Scheduler, um eine bestimmte Anzahl von Replikaten auf einem beliebigen verfügbaren Knoten mit verfügbaren Ressourcen auszuführen. Diese Art der Verwendung von Bereitstellungen mag für zustandslose Anwendungen ausreichend sein, nicht aber für Anwendungen, die eine permanente Namenskonvention oder einen permanenten Speicher benötigen. Bei Anwendungen, für die auf jedem Knoten oder auf ausgewählten Knoten in einem Cluster ein Replikat vorhanden sein muss, überprüft der Bereitstellungscontroller nicht, wie die Replikate auf den Knoten verteilt sind.

Es gibt zwei Kubernetes-Ressourcen, mit denen Sie diese Arten von Anwendungen verwalten können:

- *StatefulSets*: Verwalten den Zustand von Anwendungen über den Lebenszyklus eines einzelnen Pods hinweg, z.B. Speicher.
- *DaemonSets*: Stellen zu einem frühen Zeitpunkt im Kubernetes-Bootstrapprozess eine ausgeführte Ressource auf jedem Knoten sicher.

### <a name="statefulsets"></a>StatefulSets

Die moderne Anwendungsentwicklung strebt häufig nach zustandslosen Anwendungen, aber *StatefulSets* können für zustandsbehaftete Anwendungen verwendet werden, z.B. für Anwendungen, die Datenbankkomponenten enthalten. Ein StatefulSet ähnelt einer Bereitstellung insofern, als ein oder mehrere identische Pods erstellt und verwaltet werden. Replikate in einem StatefulSet folgen einem ordnungsgemäßen, sequenziellen Verfahren für Bereitstellung, Skalierung, Upgrades und Beendigungen. In einem StatefulSet bleiben Namenskonvention, Netzwerknamen und Speicher permanent erhalten, wenn Replikate neu geplant werden.

Sie definieren die Anwendung mit `kind: StatefulSet` im YAML-Format. Der StatefulSet-Controller sorgt dann für die Bereitstellung und Verwaltung der erforderlichen Replikate. Die Daten werden in den beständigen Speicher geschrieben, wobei der zugrunde liegende Speicher auch nach dem Löschen von „StatefulSets“ bestehen bleibt.

Weitere Informationen finden Sie unter [Kubernetes StatefulSets][kubernetes-statefulsets].

Replikate in einem „StatefulSet“ werden geplant und über jeden verfügbaren Knoten in einem Azure Kubernetes Service in Azure Stack HCI-Cluster ausgeführt. Wenn Sie sicherstellen müssen, dass mindestens ein Pod in Ihrem Set auf einem Knoten ausgeführt wird, können Sie stattdessen ein DaemonSet verwenden.

### <a name="daemonsets"></a>DaemonSets

Bei spezifischen Anforderungen an die Protokollsammlung oder Überwachung müssen Sie möglicherweise einen bestimmten Pod auf allen bzw. auf ausgewählten Knoten ausführen. Auch ein *DaemonSet* wird zum Bereitstellen von einem oder mehreren identischen Pods verwendet, aber der DaemonSet-Controller stellt sicher, dass jeder angegebene Knoten eine Instanz des Pods ausführt.

Der DaemonSet-Controller kann Pods auf Knoten zu einem frühen Zeitpunkt im Clusterstartprozess planen, bevor der Kubernetes Scheduler gestartet wird. Dies stellt sicher, dass die Pods in einem DaemonSet gestartet wurden, bevor herkömmliche Pods in einer Bereitstellung oder einem StatefulSet geplant werden.

Ähnlich wie StatefulSets wird auch ein DaemonSet mit `kind: DaemonSet` als Teil einer YAML-Definition definiert.

Weitere Informationen finden Sie unter [Kubernetes DaemonSets][kubernetes-daemonset].

### <a name="namespaces"></a>Namespaces

Kubernetes-Ressourcen wie Pods und Bereitstellungen werden logisch in einen *Namespace* gruppiert. Diese Gruppierungen ermöglichen es, einen Azure Kubernetes Service in Azure Stack HCI-Zielcluster logisch zu unterteilen und den Zugriff zum Erstellen, Anzeigen oder Verwalten von Ressourcen einzuschränken. Sie können Namespaces erstellen, um beispielsweise Unternehmensgruppen voneinander zu trennen. Benutzer können nur mit den Ressourcen innerhalb der ihnen zugewiesenen Namespaces interagieren.

Wenn Sie einen Azure Kubernetes Service-Cluster in Azure Stack HCI erstellen, stehen Ihnen die folgenden Namespaces zur Verfügung:

- *default*: In diesem Namespace werden Pods und Bereitstellungen standardmäßig erstellt, wenn kein anderer Namespace angegeben wird. In kleineren Umgebungen können Sie Anwendungen direkt im Standardnamespace bereitstellen, ohne weitere logische Unterteilungen zu erstellen. Wenn Sie mit der Kubernetes-API interagieren, z.B. mit `kubectl get pods`, wird der Standardnamespace verwendet, wenn kein anderer Namespace angegeben wurde.
- *kube-system*: In diesem Namespace befinden sich grundlegende Ressourcen, beispielsweise Netzwerkfeatures wie DNS und Proxy oder das Kubernetes-Dashboard. In diesem Namespace stellen Sie in der Regel keine eigenen Anwendungen bereit.
- *kube-public*: Dieser Namespace wird in der Regel nicht genutzt. Er kann jedoch für Ressourcen verwendet werden, die über den gesamten Cluster hinweg sichtbar sein sollen, und er kann von allen Benutzern angezeigt werden.

Weitere Informationen finden Sie unter [Kubernetes-Namespaces][kubernetes-namespaces].

[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[node-selectors]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
[taints-tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
