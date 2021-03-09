---
title: Update-AksHciCluster
author: jessicaguan
description: Der PowerShell-Befehl Update-AksHciCluster aktualisiert einen Managed Kubernetes-Cluster auf eine neuere Kubernetes- oder Betriebssystemversion.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a8fc5c1e87ed78891333af7a3a998767110428ea
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874433"
---
# <a name="update-akshcicluster"></a>Update-AksHciCluster

## <a name="synopsis"></a>Übersicht
Aktualisieren eines Managed Kubernetes-Clusters auf eine neuere Kubernetes- oder Betriebssystemversion.

## <a name="syntax"></a>Syntax

```powershell
Update-AksHciCluster -name <String>
                    [-kubernetesVersion <String>]
                    [-operatingSystem]
```

## <a name="description"></a>BESCHREIBUNG
Aktualisieren eines Managed Kubernetes-Clusters auf eine neuere Kubernetes- oder Betriebssystemversion.

## <a name="examples"></a>Beispiele

### <a name="example"></a>Beispiel
```powershell
PS C:\> Update-AksHciCluster -clusterName myCluster -kubernetesVersion v1.18.8 -operatingSystem
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

### <a name="-kubernetesversion"></a>-kubernetesVersion
Die Version von Kubernetes, auf die Sie ein Upgrade ausführen möchten.

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

### <a name="-operatingsystem"></a>-operatingSystem
Verwenden Sie dieses Flag, wenn Sie ein Update auf die nächste Version des Betriebssystems durchführen möchten.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

