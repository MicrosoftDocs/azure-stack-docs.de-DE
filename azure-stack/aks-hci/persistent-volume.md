---
title: Verwenden des persistenten Volumes in einem AKS im Azure Stack HCI-Cluster
description: Verwenden von beständigem Speicher in einem Windows-Container und Vorbereiten von Windows-Knoten für gruppenverwaltete Dienstkonten
author: v-susbo
ms.topic: how-to
ms.date: 12/02/2020
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 4baaf83c16b3fc290d9dc1339aaf432dc05d89b7
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612606"
---
# <a name="use-persistent-volume-in-an-aks-on-azure-stack-hci-cluster"></a>Verwenden des persistenten Volumes in einem AKS im Azure Stack HCI-Cluster

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

Ein persistentes Volume stellt ein Speicherelement dar, das für die Verwendung in Kubernetes-Pods bereitgestellt wurde. Ein persistentes Volume kann von einem oder mehreren Pods verwendet werden und ist für die langfristige Speicherung konzipiert. Es ist auch unabhängig vom Lebenszyklus von Pods oder Knoten. Sie können einen PVC zwar für Windows- und Linux-Knoten bereitstellen, in diesem Abschnitt wird jedoch erläutert, wie Sie ein persistentes Volume erstellen und wie Sie dieses Volume in Ihrer Windows-Anwendung verwenden können. Weitere Informationen finden Sie unter [Persistente Datenträger in Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

## <a name="before-you-begin"></a>Voraussetzungen

Voraussetzungen für die ersten Schritte:

* Ein Kubernetes-Cluster mit mindestens einem Windows-Workerknoten.
* Eine kubeconfig-Datei für den Zugriff auf den Kubernetes-Cluster.

## <a name="create-a-persistent-volume-claim"></a>Erstellen eines Anspruchs auf ein persistentes Volume

Ein Anspruch auf ein persistentes Volume (Persistent Volume Claim, PVC) wird verwendet, um basierend auf einer Speicherklasse automatisch Speicher bereitzustellen.  Erstellen Sie zum Erstellen eines Volumeanspruchs zunächst eine Datei namens `pvc-akshci-csi.yaml`, und kopieren Sie die folgende YAML-Definition in die Datei. Der Anspruch fordert einen Datenträger mit einer Größe von 10 GB mit *ReadWriteOnce* -Zugriff. Die *standardmäßige* Speicherklasse wird als Speicherklasse (VHDX) angegeben.  

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: pvc-akshci-csi
spec:
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 10Gi
```
Erstellen Sie das Volume, indem Sie die folgenden Befehle in einer PowerShell-Administratorsitzung auf einem der Server im Azure Stack HCI-Cluster ausführen (mithilfe einer Methode wie [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession) oder Remotedesktop zum Herstellen einer Verbindung mit dem Server): 

```
kubectl create -f pvc-akshci-csi.yaml 
```
Die folgende Ausgabe zeigt, dass Ihr Anspruch auf ein persistentes Volume erfolgreich erstellt wurde:

**Ausgabe:**
```
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Verwenden des persistenten Volumes

Erstellen Sie zur Verwendung eines persistenten Volumes eine Datei namens „winwebserver.yaml“, und kopieren Sie die folgende YAML-Definition in die Datei.  Anschließend erstellen Sie einen Pod mit Zugriff auf den Anspruch auf ein persistentes Volume und auf VHDX. 

In der folgenden YAML-Definition ist *mountPath* der Pfad zum Einbinden eines Volumes in einen Container. Nach erfolgreicher Erstellung eines Pods sehen Sie, dass das Unterverzeichnis *mnt* in *C:\\* und das Unterverzeichnis *akshciscsi* in *mnt* erstellt wurde.


```yaml
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  labels: 
    app: win-webserver 
  name: win-webserver 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: win-webserver 
  template: 
    metadata: 
      labels: 
        app: win-webserver 
      name: win-webserver 
    spec: 
     containers: 
      - name: windowswebserver 
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 
        ports:  
          - containerPort: 80    
        volumeMounts: 
            - name: akshciscsi 
              mountPath: "/mnt/akshciscsi" 
     volumes: 
        - name: akshciscsi 
          persistentVolumeClaim: 
            claimName:  pvc-akshci-csi 
     nodeSelector: 
      kubernetes.io/os: windows 
```

Führen Sie Folgendes aus, um einen Pod mit der obigen YAML-Definition zu erstellen:
```
kubectl create -f winwebserver.yaml 
```

Führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Pod ausgeführt wird. Warten Sie einige Minuten, bis sich der Pod im Zustand „Wird ausgeführt“ befindet, da das Pullen des Images einige Zeit in Anspruch nimmt. 
```
kubectl get pods -o wide 
```
Sobald Ihr Pod ausgeführt wird, können Sie den Podstatus anzeigen, indem Sie den folgenden Befehl ausführen: 
```
kubectl.exe describe pod %podName% 
```

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Volume im Pod eingebunden wurde:
```
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Löschen eines Anspruchs auf ein persistentes Volume

Vor dem Löschen eines Anspruchs auf ein persistentes Volume müssen Sie die App-Bereitstellung löschen, indem Sie Folgendes ausführen:
```
kubectl delete deployments win-webserver
```

Sie können dann einen Anspruch auf ein persistentes Volume löschen, indem Sie Folgendes ausführen:
```
kubectl delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="next-steps"></a>Nächste Schritte
- [Bereitstellen einer Windows-Anwendung in Ihrem Kubernetes-Cluster](./deploy-windows-application.md).
- [Verbinden Ihres Kubernetes-Clusters mit Azure Arc für Kubernetes](./connect-to-arc.md).
