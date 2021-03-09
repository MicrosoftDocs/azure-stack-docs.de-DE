---
title: Set-AksHciClusterNodeCount
author: jessicaguan
description: Mit dem PowerShell-Befehl Set-AksHciClusterNodeCount wird die Anzahl der Knoten der Steuerungsebene oder Workerknoten in einem Cluster skaliert.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 1dff2962ee1997d6eaa696f6e0eeadf7ef2d59e1
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874478"
---
# <a name="set-akshciclusternodecount"></a>Set-AksHciClusterNodeCount

## <a name="synopsis"></a>Übersicht
Skalieren der Anzahl von Knoten der Steuerungsebene oder von Workerknoten in einem Cluster.

## <a name="syntax"></a>Syntax

### <a name="scale-control-plane-nodes"></a>Skalieren der Knoten der Steuerungsebene
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -controlPlaneNodeCount <int> 
```

### <a name="scale-worker-nodes"></a>Skalieren von Workerknoten
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

## <a name="description"></a>BESCHREIBUNG
Skalieren der Anzahl von Knoten der Steuerungsebene oder von Workerknoten in einem Cluster. Die Knoten der Steuerungsebene und die Workerknoten müssen unabhängig voneinander skaliert werden.

## <a name="examples"></a>Beispiele

### <a name="scale-control-plane-nodes"></a>Skalieren der Knoten der Steuerungsebene
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -controlPlaneNodeCount 3
```

### <a name="scale-worker-nodes"></a>Skalieren von Workerknoten
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -linuxNodeCount 2 -windowsNodeCount 2
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der alphanumerische Name Ihres Kubernetes-Clusters.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
Die Anzahl der Knoten in Ihrer Steuerungsebene. Es ist kein Standardwert vorhanden.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: none
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Die Anzahl der Linux-Knoten in Ihrem Kubernetes-Cluster. Der Standardwert ist 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
Das Zertifikat, das zum Authentifizieren beim Proxyserver verwendet wird.
 
```yaml
Type: System.String
Parameter Sets: (All)
Aliases:
 
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsnodecount"></a>-windowsNodeCount
Die Anzahl der Windows-Knoten in Ihrem Kubernetes-Cluster. Der Standardwert ist 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
