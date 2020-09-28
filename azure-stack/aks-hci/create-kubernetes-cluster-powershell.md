---
title: Schnellstart für das Erstellen von Kubernetes-Clustern in Azure Stack HCI mithilfe von Windows PowerShell
description: Erfahren Sie, wie Sie mithilfe von Windows PowerShell Kubernetes-Cluster in Azure Stack HCI erstellen.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 35d527b56a2429676d343ba8098fc6821835fb00
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949374"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Schnellstart: Erstellen von Kubernetes-Clustern in Azure Stack HCI mithilfe von Windows PowerShell

> Gilt für: Azure Stack HCI

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Windows PowerShell einen Kubernetes-Cluster in Azure Stack HCI erstellen. Informationen, um stattdessen das Windows Admin Center zu verwenden, finden Sie unter [Einrichten von Azure Kubernetes Service in Azure Stack HCI mithilfe des Windows Admin Centers](setup.md).

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie verfügen über einen Azure Stack HCI-Cluster mit 2–4-Knoten oder einen Azure Stack HCI mit einem einzelnen Knoten. **Wir empfehlen, dass Sie einen Azure Stack HCI-Cluster mit 2–4-Knoten haben.** Ist dies nicht der Fall, befolgen Sie [hier](./system-requirements.md) die Anweisungen zum Erstellen eines solchen.
- Sie haben einen Azure Stack Kubernetes-Host eingerichtet. Ist dies nicht der Fall, befolgen Sie [hier](./setup-powershell.md) die Anweisungen zum Einrichten eines solchen.

## <a name="step-1-create-a-kubernetes-cluster"></a>Schritt 1: Erstellen eines Kubernetes-Clusters

Nach der Installation des Azure Kubernetes Service-Hosts sind Sie bereit, um einen Kubernetes-Cluster bereitzustellen.

Öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus.

   ```powershell
   New-AksHciCluster -clusterName
                    [-kubernetesVersion]
                    [-controlPlaneNodeCount]
                    [-linuxNodeCount]
                    [-windowsNodeCount]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-linuxNodeVmSize]
                    [-windowsNodeVmSize]
   ```

### <a name="required-parameters"></a>Erforderliche Parameter

`-clusterName`

Der alphanumerische Name Ihres Kubernetes-Clusters.

### <a name="optional-parameters"></a>Optionale Parameter

`-kubernetesVersion`

Die Version von Kubernetes, die Sie bereitstellen möchten. Standard ist v1.18.6. Zum Abrufen einer Liste der verfügbaren Versionen führen Sie `Get-AksHciKubernetesVersion` aus.

`-controlPlaneNodeCount`

Die Anzahl der Knoten in Ihrer Steuerungsebene. Der Standardwert ist 1.

`-linuxNodeCount`

Die Anzahl der Linux-Knoten in Ihrem Kubernetes-Cluster. Der Standardwert ist 1.

`-windowsNodeCount`

Die Anzahl der Windows-Knoten in Ihrem Kubernetes-Cluster. Standard ist "0".

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
Set-AksHciClusterNodeCount –clusterName
                           -controlPlaneNodeCount
```

Führen Sie zum Skalieren der Workerknoten folgenden Befehl aus.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -linuxNodeCount
                           -windowsNodeCount
```

Die Knoten der Steuerungsebene und die Workerknoten müssen unabhängig voneinander skaliert werden.

## <a name="step-3-upgrade-kubernetes-version"></a>Schritt 3: Upgrade der Kubernetes-Version

Um die aktuelle Kubernetes-Version anzuzeigen, die Sie ausführen, führen Sie den folgenden Befehl aus.

```powershell
Get-AksHciKubernetesVersion
```

Um ein Upgrade auf die nächste Kubernetes-Version durchzuführen, führen Sie den folgenden Befehl aus.

```powershell
Update-AksHciCluster -clusterName
```

Wenn Sie Windows-Knoten verwenden möchten, ist die mindestens erforderliche Version v1.1.8.6.

## <a name="step-4-access-your-clusters-using-kubectl"></a>Schritt 4: Zugreifen auf Ihre Cluster mithilfe von kubectl

Um mithilfe von kubectl auf Ihren Azure Kubernetes Service-Host oder Kubernetes-Cluster zuzugreifen, führen Sie den folgenden Befehl aus. Dieser verwendet die kubeconfig-Datei des angegebenen Clusters als standardmäßige kubeconfig-Datei für kubectl.

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="delete-a-kubernetes-cluster"></a>Löschen eines Kubernetes-Clusters

Wenn Sie einen Kubernetes-Cluster löschen müssen, führen Sie den folgenden Befehl aus.

```powershell
Remove-AksHciCluster -clusterName
```

## <a name="get-logs"></a>Abrufen von Protokollen

Um von allen Ihren Pods Protokolle zu erhalten, führen Sie den folgenden Befehl aus. Dieser Befehl erstellt einen komprimierten Ordner (ZIP) namens `akshcilogs` im Pfad `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mit PowerShell einen Kubernetes-Cluster erstellen, skalieren und upgraden.

## <a name="next-steps"></a>Nächste Schritte

- [Verbinden Ihrer Cluster mit Azure Arc für Kubernetes](./connect-to-arc.md).
- [Bereitstellen eine Linux-Anwendung in Ihrem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows-Anwendung in Ihrem Kubernetes-Cluster](./deploy-windows-application.md).
