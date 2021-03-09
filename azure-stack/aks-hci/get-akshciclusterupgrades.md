---
title: Get-AksHciClusterUpgrades
author: jessicaguan
description: Der PowerShell-Befehl Get-AksHciClusterUpgrades ruft die verfügbaren Kubernetes-Upgrades für einen Azure Kubernetes Service-Cluster ab.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 3d5f4a6b67345baaa8df9b9eccc26d0a0503dafd
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874447"
---
# <a name="get-akshciclusterupgrades"></a>Get-AksHciClusterUpgrades

## <a name="synopsis"></a>Übersicht
Abrufen der verfügbaren Upgrades für einen Azure Kubernetes Service-Clusters.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciClusterUpgrades -name <String>
                          
```

## <a name="description"></a>BESCHREIBUNG
Abrufen der verfügbaren Upgrades für einen Azure Kubernetes Service-Clusters.

## <a name="examples"></a>Beispiele

```powershell
PS C:\> Get-AksHciClusterUpgrades -name mycluster
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der alphanumerische Name Ihres Clusters.

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
