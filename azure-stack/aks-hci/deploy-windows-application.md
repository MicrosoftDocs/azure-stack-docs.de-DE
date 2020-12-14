---
title: Bereitstellen einer Windows .NET-Anwendung in AKS auf Azure Stack HCI
description: Erfahren Sie,wie Sie mithilfe eines in Azure Container Registry gespeicherten benutzerdefinierten Images eine Windows-Anwendung für Ihren Cluster bereitstellen.
author: abha
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 844bafbb497228d2e4527a5e974574dadb95ee7d
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612317"
---
# <a name="deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Bereitstellen von Windows-Anwendungen in Azure Kubernetes Service in Azure Stack HCI

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

In diesem Tutorial stellen Sie eine ASP.NET-Beispielanwendung in einem Windows Server-Container im Kubernetes-Cluster bereit und erfahren, wie Sie Ihre Anwendung testen und skalieren können. Außerdem erfahren Sie, wie Sie einen Windows-Knoten mit einer Active Directory Domäne verknüpfen.
In diesem Tutorial werden grundlegende Kenntnisse von Kubernetes-Konzepten vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service in Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Anforderungen erfüllt sind:

* Ein Azure Kubernetes Service in einem Azure Stack HCI-Cluster mit mindestens einem Windows-Workerknoten, der aktiv ist und ausgeführt wird. 
* Eine kubeconfig-Datei für den Zugriff auf den Cluster.
* Das Azure Kubernetes Service in Azure Stack HCI PowerShell-Modul ist installiert.
* Führen Sie die Befehle in diesem Dokument in einem PowerShell-Fenster mit Administratorrechten aus.
* Stellen Sie sicher, dass die betriebssystemspezifischen Workloads auf dem entsprechenden Containerhost landen. Wenn Sie einen Kubernetes-Cluster mit gemischten Linux- und Windows-Workerknoten haben, können Sie entweder Knotenselektoren oder Taints und Toleranzen verwenden. Weitere Informationen finden Sie unter [Verwenden von Knotenselektoren und Taints und Toleranzen](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. In diesem Artikel wird ein Manifest verwendet, um alle Objekte zu erstellen, die zum Ausführen der ASP.NET-Beispielanwendung in einem Windows Server-Container erforderlich sind. Dieses Manifest beinhaltet eine Kubernetes-Bereitstellung für die ASP.NET-Beispielanwendung und einen externen Kubernetes-Dienst für den Zugriff auf die Anwendung über das Internet.

Die ASP.NET-Beispielanwendung wird als Teil des .NET Framework-Beispiels bereitgestellt und in einem Windows Server-Container ausgeführt. Azure Kubernetes Service in Azure Stack HCI erfordert, dass Windows Server-Container auf Images von *Windows Server 2019* basieren. 

Mit der Kubernetes-Manifestdatei muss auch eine Knotenauswahl definiert werden. So wird Ihrem AKS-Cluster mitgeteilt, dass der Pod Ihrer ASP.NET-Beispielanwendung auf einem Knoten ausgeführt werden soll, für den die Ausführung von Windows Server-Containern möglich ist.

Erstellen Sie eine Datei namens `sample.yaml`, und fügen Sie die folgende YAML-Definition ein. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Stellen Sie die Anwendung über den Befehl `kubectl apply` bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f sample.yaml
```

In der folgende Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern. Gelegentlich kann die Bereitstellung des Diensts länger als ein paar Minuten dauern. Warten Sie in diesen Fällen bis zu 10 Minuten.

Verwenden Sie zum Überwachen des Fortschritts den Befehl `kubectl get service` mit dem Argument `--watch`.

```PowerShell
kubectl get service sample --watch
```

Die externe IP-Adresse *EXTERNAL-IP* für den *Beispieldienst* wird zunächst als *ausstehend* angezeigt.

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Beispielanwendung in Aktion zu sehen.

![Abbildung der Navigation zur ASP.NET-Beispielanwendung](media/deploy-windows-application/asp-net-sample-app.png)

Wenn bei dem Versuch, die Seite zu laden, ein Verbindungstimeout auftritt, überprüfen Sie mit dem Befehl `kubectl get pods --watch`, ob die Beispiel-App bereit ist. Manchmal wird ist die externe IP-Adresse verfügbar, bevor der Windows-Container gestartet wurde.

## <a name="scale-application-pods"></a>Skalieren von Anwendungspods

Wir haben ein einzelnes Replikat des Anwendungs-Front-Ends erstellt. Um die Anzahl und den Zustand von Pods in Ihrem Cluster anzuzeigen, verwenden Sie den Befehl `kubectl get` wie folgt:

```console
kubectl get pods -n default
```

Um die Anzahl von Pods in der *Beispiel* bereitstellung zu ändern, verwenden Sie den Befehl `kubectl scale`. Im folgenden Beispiel wird die Anzahl der Front-End-Pods auf *3* erhöht:

```console
kubectl scale --replicas=3 deployment/sample
```

Führen Sie `kubectl get pods` erneut aus, um zu überprüfen, ob zusätzliche Pods erstellt wurden. Nach ca. einer Minute sind die zusätzlichen Pods in Ihrem Cluster verfügbar:

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure Monitor zum Überwachen Ihres Clusters und Ihrer Anwendung](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Verwenden eines permanenten Volumes für einen Kubernetes-Cluster](persistent-volume.md).
