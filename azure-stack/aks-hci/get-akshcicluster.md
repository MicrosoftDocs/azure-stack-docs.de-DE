---
title: Get-AksHciCluster
author: jessicaguan
description: Der PowerShell-Befehl Get-AksHciCluster listet von Kubernetes verwaltete Cluster auf, einschließlich des Azure Kubernetes Service-Hosts.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 14d7f807ef04432e7ea8c3c57dbb4bb80c64731b
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874440"
---
# <a name="get-akshcicluster"></a>Get-AksHciCluster

## <a name="synopsis"></a>Übersicht
Auflisten von Kubernetes-verwalteten Clustern, einschließlich des Azure Kubernetes Service-Hosts.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciCluster [-name <String>]
```

## <a name="description"></a>BESCHREIBUNG
Auflisten von Kubernetes-verwalteten Clustern, einschließlich des Azure Kubernetes Service-Hosts.

## <a name="examples"></a>Beispiele

### <a name="list-all-kubernetes-clusters"></a>Auflisten aller Kubernetes-Cluster
```powershell
PS C:\> Get-AksHciCluster
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der Name Ihres Clusters.

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
