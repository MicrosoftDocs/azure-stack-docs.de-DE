---
title: Privilegierter Endpunkt „Get-ClusterLog“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-ClusterLog'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ec984252235334b5706a79a510852868c8f40dba
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487106"
---
# <a name="get-clusterlog"></a>Get-ClusterLog

## <a name="syntax"></a>Syntax

```
Get-ClusterLog [-SkipClusterState] [-InputObject <Object>] [-Cluster <Object>] [-NetworkDiagnostics]
 [-Destination <Object>] [-NetworkDiagnosticsLevel <Object>] [[-Node] <Object>] [-TimeSpan <Object>]
 [-PerformanceHistoryTimeFrame <Object>] [-Health] [-ExportClusterPerformanceHistory] [-UseLocalTime] [-AsJob]
```

## <a name="parameters"></a>Parameter

### <a name="-cluster"></a>-Cluster
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-destination"></a>-Destination
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-exportclusterperformancehistory"></a>-ExportClusterPerformanceHistory
Exportiert die Daten des Clusterleistungsverlaufs (ClusterPerformanceHistory).

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-health"></a>-Health
Generiert die Clusterintegritätsprotokolle.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-inputobject"></a>-InputObject
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-networkdiagnostics"></a>-NetworkDiagnostics
Generiert die Clusternetzwerkdiagnose-Protokolle.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-networkdiagnosticslevel"></a>-NetworkDiagnosticsLevel
Gibt den Umfang der Netzwerkdiagnoseprotokolle an.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-node"></a>-Node
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-performancehistorytimeframe"></a>-PerformanceHistoryTimeFrame
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipclusterstate"></a>-SkipClusterState
Generiert das Clusterprotokoll, ohne Informationen zum Clusterzustand abzurufen.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: scs

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timespan"></a>-TimeSpan
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Span

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-uselocaltime"></a>-UseLocalTime
Generiert das Clusterprotokoll unter Verwendung der Ortszeit anstelle der westeuropäischen Zeit (GMT).

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: lt

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Nächste Schritte

[https://go.microsoft.com/fwlink/?LinkId=216212](https://go.microsoft.com/fwlink/?LinkId=216212)

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
