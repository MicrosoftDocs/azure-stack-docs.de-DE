---
title: 'Tutorial: Bereitstellen einer Linux-Anwendung in AKS in Azure Stack HCI'
description: In diesem Tutorial stellen Sie mithilfe eines in Azure Container Registry gespeicherten benutzerdefinierten Images eine Linux-Anwendung mit mehreren Containern in Ihrem Cluster bereit.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 6fd907a44cdaad5f5dfe7ccb3a29f5fc6a0152b6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948739"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Tutorial: Bereitstellen von Linux-Anwendungen in Azure Kubernetes Service in Azure Stack HCI

In diesem Tutorial stellen Sie eine Anwendung mit mehreren Containern bereit, die ein Web-Front-End und eine Redis-Datenbankinstanz in Ihrem Azure Kubernetes Service in Azure Stack HCI-Cluster enthält. Anschließend erfahren Sie, wie Sie Ihre Anwendung testen und skalieren. 

In diesem Tutorial werden grundlegende Kenntnisse von Kubernetes-Konzepten vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service in Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Anforderungen erfüllt sind:

* Ein Azure Kubernetes Service in einem Azure Stack HCI-Cluster mit mindestens einem Linux-Workerknoten, der aktiv ist und ausgeführt wird. 
* Eine kubeconfig-Datei für den Zugriff auf den Cluster.
* Das Azure Kubernetes Service in Azure Stack HCI PowerShell-Modul ist installiert.
* Führen Sie die Befehle in diesem Dokument in einem PowerShell-Fenster mit Administratorrechten aus.
* Stellen Sie sicher, dass die betriebssystemspezifischen Workloads auf dem entsprechenden Containerhost landen. Wenn Sie einen Kubernetes-Cluster mit gemischten Linux- und Windows-Workerknoten haben, können Sie entweder Knotenselektoren oder Taints und Toleranzen verwenden. Weitere Informationen finden Sie unter [Verwenden von Knotenselektoren und Taints und Toleranzen](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. In dieser Schnellstartanleitung wird ein Manifest verwendet, um alle Objekte zu erstellen, die zum Ausführen der [Azure Vote-Anwendung](https://github.com/Azure-Samples/azure-voting-app-redis) benötigt werden. Dieses Manifest umfasst zwei Kubernetes-Bereitstellungen: eine für die Azure Vote-Python-Beispielanwendungen und eine für eine Redis-Instanz. Außerdem werden zwei Kubernetes-Dienste erstellt: ein interner Dienst für die Redis-Instanz und ein externer Dienst, über den aus dem Internet auf die Azure Vote-Anwendung zugegriffen wird.

Erstellen Sie eine Datei namens `azure-vote.yaml`, und fügen Sie die folgende YAML-Definition ein.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Stellen Sie die Anwendung über den Befehl `kubectl apply` bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```PowerShell
kubectl apply -f azure-vote.yaml
```

In der folgenden Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl `kubectl get service` mit dem Argument `--watch`.

```PowerShell
kubectl get service azure-vote-front --watch
```

Die externe IP-Adresse (*EXTERNAL-IP*) für den Dienst *azure-vote-front* wird zunächst als *ausstehend* angezeigt.

```output
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27      <pending>     80:30572/TCP   22m
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   24m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Azure-Abstimmungs-App in Aktion zu sehen.

![Abbildung: Kubernetes-Cluster in Azure](media/deploy-linux-application/azure-vote.png)

## <a name="scale-application-pods"></a>Skalieren von Anwendungspods

Wir haben ein einzelnes Replikat des Azure Vote-Front-Ends und der Redis-Instanz erstellt. Um die Anzahl und den Zustand von Pods in Ihrem Cluster anzuzeigen, verwenden Sie den Befehl `kubectl get` wie folgt:

```console
kubectl get pods -n default
```

Die folgende Beispielausgabe zeigt einen Front-End-Pod und einen Back-End-Pod:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

Um die Anzahl von Pods in der *azure-vote-front*-Bereitstellung zu ändern, verwenden Sie den Befehl `kubectl scale`. Im folgenden Beispiel wird die Anzahl von Front-End-Pods auf *5* erhöht:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Führen Sie `kubectl get pods` erneut aus, um zu überprüfen, ob zusätzliche Pods erstellt wurden. Nach ca. einer Minute sind die zusätzlichen Pods in Ihrem Cluster verfügbar:

```console
kubectl get pods -n default

Name                                READY   STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1     Running   0          31m
azure-vote-front-84c8bf64fc-cdq86   1/1     Running   0          31m
azure-vote-front-84c8bf64fc-56h64   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-djkp8   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-jmmvs   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-znc6z   1/1     Running   0          80s
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure Monitor zum Überwachen Ihres Clusters und Ihrer Anwendung](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
