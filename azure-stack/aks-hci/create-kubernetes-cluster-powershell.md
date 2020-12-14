---
title: Schnellstart für das Erstellen von Kubernetes-Clustern in Azure Stack HCI mithilfe von Windows PowerShell
description: Erfahren Sie, wie Sie mithilfe von Windows PowerShell Kubernetes-Cluster in Azure Stack HCI erstellen.
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 6a215308e5eab1fa7991f912f1cd4aaff2391f5e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612436"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Schnellstart: Erstellen von Kubernetes-Clustern in Azure Stack HCI mithilfe von Windows PowerShell

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Windows PowerShell einen Kubernetes-Cluster in Azure Stack HCI erstellen. Anschließend erfahren Sie, wie Sie Ihren Kubernetes-Cluster skalieren und die Kubernetes-Version Ihres Clusters aktualisieren. Informationen, um stattdessen das Windows Admin Center zu verwenden, finden Sie unter [Einrichten von Azure Kubernetes Service in Azure Stack HCI mithilfe des Windows Admin Centers](setup.md).

## <a name="before-you-begin"></a>Voraussetzungen

 - Stellen Sie sicher, dass Sie einen Azure Stack Kubernetes-Host eingerichtet haben. Wenn dies noch nicht der Fall ist, lesen Sie [Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts auf Azure Stack HCI mit PowerShell](./setup-powershell.md).
 - Stellen Sie sicher, dass das neueste PowerShell-Modul „Aks-Hci“ installiert ist. Wenn dies nicht der Fall ist, lesen Sie [Herunterladen und Installieren des PowerShell-Moduls „AksHci“](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module).

## <a name="step-1-create-a-kubernetes-cluster"></a>Schritt 1: Erstellen eines Kubernetes-Clusters

Nach der Installation des Azure Kubernetes Service-Hosts sind Sie bereit, um einen Kubernetes-Cluster bereitzustellen.

Öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus.

   ```powershell
   New-AksHciCluster -clusterName <String>
                    [-kubernetesVersion <String>]
                    [-controlPlaneNodeCount <int>]
                    [-linuxNodeCount <int>]
                    [-windowsNodeCount <int>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-linuxNodeVmSize <VmSize>]
                    [-windowsNodeVmSize <VmSize>]
   ```

### <a name="example"></a>Beispiel

   ```powershell
   New-AksHciCluster -clusterName mynewcluster -kubernetesVersion v1.18.8 -controlPlaneNodeCount 1 -linuxNodeCount 1 -windowsNodeCount 0 
   ```

### <a name="required-parameters"></a>Erforderliche Parameter

`-clusterName`

Der alphanumerische Name Ihres Kubernetes-Clusters.

### <a name="optional-parameters"></a>Optionale Parameter

`-kubernetesVersion`

Die Version von Kubernetes, die Sie bereitstellen möchten. Standard ist v1.18.8. Zum Abrufen einer Liste der verfügbaren Versionen führen Sie `Get-AksHciKubernetesVersion` aus.

`-controlPlaneNodeCount`

Die Anzahl der Knoten in Ihrer Steuerungsebene. Der Standardwert ist 1.

`-linuxNodeCount`

Die Anzahl der Linux-Knoten in Ihrem Kubernetes-Cluster. Der Standardwert ist 1.

`-windowsNodeCount`

Die Anzahl der Windows-Knoten in Ihrem Kubernetes-Cluster. Standard ist "0". Wenn Sie Kubernetes v1.18.8 ausführen, können Sie nur Windows-Knoten bereitstellen.

`-controlPlaneVmSize`

Die Größe der VM Ihrer Steuerungsebene. Der Standard ist „Standard_A2_v2“. Zum Abrufen einer Liste der verfügbaren VM-Größen führen Sie `Get-AksHciVmSize` aus.

`-loadBalancerVmSize`

Die Größe Ihrer Lastenausgleichs-VM. Der Standard ist „Standard_A2_V2“. Zum Abrufen einer Liste der verfügbaren VM-Größen führen Sie `Get-AksHciVmSize` aus.

`-linuxNodeVmSize`

Die Größe Ihrer Linux-Knoten-VM. Der Standard ist „Standard_K8S3_v1“. Zum Abrufen einer Liste der verfügbaren VM-Größen führen Sie `Get-AksHciVmSize` aus.

`-windowsNodeVmSize`

Die Größe Ihrer Windows-Knoten-VM. Der Standard ist „Standard_K8S3_v1“. Zum Abrufen einer Liste der verfügbaren VM-Größen führen Sie `Get-AksHciVmSize` aus.

### <a name="check-your-deployed-clusters"></a>Überprüfen Ihrer bereitgestellten Cluster

Um eine Liste Ihrer bereitgestellten Azure Kubernetes Service-Hosts und Kubernetes-Cluster zu erhalten, führen Sie den folgenden Befehl aus.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>Schritt 2: Skalieren eines Kubernetes-Clusters

Wenn Sie Ihren Cluster hoch- oder herunterskalieren müssen, können Sie die Anzahl der Knoten der Steuerungsebene, der Linux-Workerknoten oder der Windows-Workerknoten ändern.

Führen Sie zum Skalieren von Knoten der Steuerungsebene folgenden Befehl aus.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -controlPlaneNodeCount <int>
```

Führen Sie zum Skalieren der Workerknoten folgenden Befehl aus.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

Die Knoten der Steuerungsebene und die Workerknoten müssen unabhängig voneinander skaliert werden.

### <a name="example"></a>Beispiel

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -controlPlaneNodeCount 3
```

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -linuxNodeCount 2 -windowsNodeCount 2 
```

## <a name="step-3-upgrade-kubernetes-version"></a>Schritt 3: Upgrade der Kubernetes-Version

Führen Sie den folgenden Befehl aus, um die Liste der verfügbaren Kubernetes-Versionen anzuzeigen.

```powershell
Get-AksHciKubernetesVersion
```

Um ein Update auf die nächste Kubernetes-Version durchzuführen, führen Sie den folgenden Befehl aus.

```powershell
Update-AksHciCluster -clusterName <String>
                     [-patch]
```
Jede Kubernetes-Version verfügt über eine Hauptversion, eine Nebenversion und eine Patchversion. In v1.18.6 ist 1 beispielsweise die Hauptversion, 18 die Nebenversion und 6 die Patchversion. Im Laufe der Zeit unterstützt AKS-HCI ein Hauptrelease, drei Nebenreleases und zwei Patches pro Nebenrelease für insgesamt 6 unterstützte Versionen. In dieser Vorschauversion werden jedoch insgesamt 4 Releases unterstützt: v1.16.10, v1.16.15, v1.17.11, v1.18.8. 

Wenn der Parameter `patch` während der Ausführung von `Update-AksHciCluster` hinzugefügt wird, führt der Befehl ein Upgrade auf die nächste Patchversion (sofern vorhanden) für die Nebenversion aus. Wenn der Befehl ohne den Parameter `patch` ausgeführt wird, erfolgt standardmäßig ein Upgrade auf die nächste Nebenversion. Um diesen Vorgang zu vereinfachen, enthält die folgende Tabelle alle möglichen Updatemöglichkeiten:

| Aktuelles Release           | Aktualisierte Kubernetes-Version ohne -patch         | Aktualisierte Kubernetes-Version mit -patch
| ---------------------------- | ------------ | -------------------------------- |
| v1.16.10           |     v1.17.11      | v1.16.15
| v1.16.15            | v1.17.11 | Direktes Add-On-Upgrade
| v1.17.11           |  v1.18.8          | Direktes Add-On-Upgrade
| v1.18.8             | Direktes Add-On-Upgrade   | Direktes Add-On-Upgrade

Beim direkten Add-On-Upgrade werden alle Kubernetes-Add-Ons wie CSI aktualisiert, die von AKS-HCI für Sie verwaltet werden. Bei diesem Upgrade wird die Betriebssystemversion des Knotens nicht geändert. Außerdem wird die Kubernetes-Version nicht geändert.

### <a name="example---upgrade-kubernetes-version-to-the-next-minor-version"></a>Beispiel: Upgrade der Kubernetes-Version auf die nächste Nebenversion

```powershell
Update-AksHciCluster -clusterName mynewcluster
```

### <a name="example---upgrade-kubernetes-version-to-the-next-patch-version"></a>Beispiel: Upgrade der Kubernetes-Version auf die nächste Patchversion

```powershell
Update-AksHciCluster -clusterName mynewcluster -patch
```


## <a name="step-4-access-your-clusters-using-kubectl"></a>Schritt 4: Zugreifen auf Ihre Cluster mithilfe von kubectl

Führen Sie den folgenden Befehl aus, um mithilfe von kubectl auf Ihre Kubernetes-Cluster zuzugreifen. Dieser verwendet die kubeconfig-Datei des angegebenen Clusters als standardmäßige kubeconfig-Datei für kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Beispiel

```powershell
Get-AksHciCredential -clusterName mynewcluster
```

### <a name="required-parameters"></a>Erforderliche Parameter

`clusterName`

Der Name des Clusters.

### <a name="optional-parameters"></a>Optionale Parameter

`outputLocation`

Der Speicherort, an den kubeconfig heruntergeladen werden soll. Der Standardwert ist `%USERPROFILE%\.kube`.

## <a name="delete-a-kubernetes-cluster"></a>Löschen eines Kubernetes-Clusters

Wenn Sie einen Kubernetes-Cluster löschen müssen, führen Sie den folgenden Befehl aus.

```powershell
Remove-AksHciCluster -clusterName
```

### <a name="example"></a>Beispiel

```powershell
Remove-AksHciCluster -clusterName mynewcluster
```

## <a name="get-logs"></a>Abrufen von Protokollen

Führen Sie den folgenden Befehl aus, um die Protokolle Ihrer gesamten Pods zu erhalten. Mit diesem Befehl wird der gezippte Ausgabeordner `akshcilogs` unter dem Pfad `C:\wssd\akshcilogs` erstellt.

```powershell
Get-AksHciLogs
```

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mit PowerShell die Kubernetes-Version eines Clusters erstellen, skalieren und upgraden.

## <a name="next-steps"></a>Nächste Schritte

- [Verbinden Ihrer Cluster mit Azure Arc für Kubernetes](./connect-to-arc.md).
- [Bereitstellen eine Linux-Anwendung in Ihrem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows-Anwendung in Ihrem Kubernetes-Cluster](./deploy-windows-application.md).
