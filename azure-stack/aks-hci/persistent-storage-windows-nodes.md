---
title: Verwenden von beständigem Speicher in einem Windows-Container
description: Verwenden von beständigem Speicher in einem Windows-Container und Vorbereiten von Windows-Knoten für gruppenverwaltete Dienstkonten
author: abha
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 7436498ea993281f803d7ff8a0c4108728570f59
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949327"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>Verwenden von beständigem Speicher in einem Windows-Container und Vorbereiten von Windows-Knoten für gruppenverwaltete Dienstkonten

Ein persistentes Volume stellt ein Speicherelement dar, das für die Verwendung in Kubernetes-Pods bereitgestellt wurde. Ein persistentes Volume kann von einem oder mehreren Pods verwendet werden und ist für die langfristige Speicherung konzipiert. Es ist auch unabhängig vom Lebenszyklus von Pods oder Knoten.  In diesem Abschnitt erfahren Sie, wie Sie ein persistentes Volume erstellen, und wie Sie dieses Volume in Ihrer Windows-Anwendung verwenden können.

## <a name="before-you-begin"></a>Voraussetzungen

Voraussetzungen für die ersten Schritte:

* Ein Kubernetes-Cluster mit mindestens einem Windows-Workerknoten.
* Eine kubeconfig-Datei für den Zugriff auf den Kubernetes-Cluster.


## <a name="create-a-persistent-volume-claim"></a>Erstellen eines Anspruchs auf ein persistentes Volume

Ein Anspruch auf ein persistentes Volume (Persistent Volume Claim, PVC) wird verwendet, um basierend auf einer Speicherklasse automatisch Speicher bereitzustellen. Erstellen Sie zum Erstellen eines Volumeanspruchs zunächst eine Datei namens `pvc-akshci-csi.yaml`, und kopieren Sie die folgende YAML-Definition in die Datei. Der Anspruch fordert einen Datenträger mit einer Größe von 10 GB mit *ReadWriteOnce* -Zugriff. Die *standardmäßige* Speicherklasse wird als Speicherklasse (VHDX) angegeben.  

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


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
Die folgende Ausgabe zeigt, dass Ihr Anspruch auf ein persistentes Volume erfolgreich erstellt wurde:

**Ausgabe:**
```PowerShell
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Verwenden des persistenten Volumes

Erstellen Sie zur Verwendung eines persistenten Volumes eine Datei namens „winwebserver.yaml“, und kopieren Sie die folgende YAML-Definition in die Datei. Anschließend erstellen Sie einen Pod mit Zugriff auf den Anspruch auf ein persistentes Volume und auf VHDX. 

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

```PowerShell
Kubectl create -f winwebserver.yaml 
```

Führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Pod ausgeführt wird. Warten Sie einige Minuten, bis sich der Pod im Zustand „Wird ausgeführt“ befindet, da das Pullen des Images einige Zeit in Anspruch nimmt.

```PowerShell
kubectl get pods -o wide 
```
Sobald Ihr Pod ausgeführt wird, können Sie den Podstatus anzeigen, indem Sie den folgenden Befehl ausführen: 

```PowerShell
kubectl.exe describe pod %podName% 
```

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Volume im Pod eingebunden wurde:

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Löschen eines Anspruchs auf ein persistentes Volume

Vor dem Löschen eines Anspruchs auf ein persistentes Volume müssen Sie die App-Bereitstellung löschen, indem Sie Folgendes ausführen:

```PowerShell
kubectl.exe delete deployments win-webserver
```

Sie können dann einen Anspruch auf ein persistentes Volume löschen, indem Sie Folgendes ausführen:

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>Vorbereiten von Windows-Knoten für die Unterstützung von gruppenverwalteten Dienstkonten auf Windows-Knoten

Gruppenverwaltete Dienstkonten sind eine spezielle Art von Active Directory-Konten, die eine automatische Kennwortverwaltung, eine vereinfachte Verwaltung von Dienstprinzipalnamen (SPN) und die Möglichkeit bieten, die Verwaltung über mehrere Server hinweg an andere Administratoren zu delegieren. Zum Konfigurieren von gruppenverwalteten Dienstkonten (gMSA) für Pods und Container, die auf Ihren Windows-Knoten ausgeführt werden sollen, müssen Sie Ihre Windows-Knoten zunächst mit einer Active Directory-Domäne verknüpfen.

Damit die Unterstützung von gruppenverwalteten Dienstkonten ermöglicht wird, muss der Name Ihres Kubernetes-Clusters aus weniger als vier Zeichen bestehen. Der Grund dafür ist, dass die maximal unterstützte Länge für einen in eine Domäne eingebundenen Servernamen 15 Zeichen beträgt und die Kubernetes-Clusternamenskonvention für AKS in Azure Stack HCI für einen Workerknoten einige vordefinierte Zeichen zu einem Knotennamen hinzufügt.

Um Ihre Windows-Workerknoten mit einer Domäne zu verknüpfen, melden Sie sich bei einem Windows-Workerknoten an, indem Sie `kubectl get` ausführen und den Wert `EXTERNAL-IP` notieren.

```PowerShell
kubectl get nodes -o wide
``` 

Sie können dann mithilfe von `ssh Administrator@ip` per SSH auf den Knoten gelangen. 

Nachdem Sie sich erfolgreich bei Ihrem Windows-Workerknoten angemeldet haben, führen Sie den folgenden PowerShell-Befehl aus, um dem Knoten mit der Domäne zu verknüpfen. Sie werden aufgefordert, die Anmeldeinformationen für Ihr **Domainadministratorkonto** einzugeben. Sie können auch Anmeldeinformationen von Benutzern mit erhöhten Rechten verwenden, denen Rechte zum Verknüpfen von Computern mit der angegebenen Domäne erteilt wurden. Anschließend müssen Sie Ihren Windows-Workerknoten neu starten.

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

Nachdem alle Windows-Workerknoten mit einer Domäne verknüpft wurden, führen Sie die unter [Konfigurieren von gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) beschriebenen Schritte aus, um die benutzerdefinierten Kubernetes-gMSA-Ressourcendefinitionen und Webhooks auf Ihren Kubernetes-Cluster anzuwenden.

Weitere Informationen zu Windows-Containern mit gMSA finden Sie unter [Windows-Container und gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Nächste Schritte
- [Bereitstellen einer Windows-Anwendung in Ihrem Kubernetes-Cluster](./deploy-windows-application.md).
- [Verbinden Ihres Kubernetes-Clusters mit Azure Arc für Kubernetes](./connect-to-arc.md).
