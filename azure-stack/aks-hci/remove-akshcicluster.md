---
title: Remove-AksHciCluster
description: Der PowerShell-Befehl Remove-AksHciCluster löscht einen Managed Kubernetes-Cluster.
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 868aa8a739c69ad6e29d73192f91c9bda73a3195
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874481"
---
# <a name="remove-akshcicluster"></a>Remove-AksHciCluster

## <a name="synopsis"></a>Übersicht
Löschen eines Managed Kubernetes-Clusters.

## <a name="syntax"></a>Syntax

### <a name="delete-a-managed-kubernetes-cluster"></a>Löschen eines Managed Kubernetes-Clusters.
```powershell
Remove-AksHciCluster -name 
                    [-force]   
```

## <a name="description"></a>BESCHREIBUNG
Löschen eines Managed Kubernetes-Clusters.

## <a name="examples"></a>Beispiele

### <a name="delete-an-existing-managed-kubernetes-cluster"></a>Löschen eines vorhandenen Managed Kubernetes-Clusters.
```powershell
PS C:\> Remove-AksHciCluster -name myCluster
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der Name Ihres Kubernetes-Clusters.

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
