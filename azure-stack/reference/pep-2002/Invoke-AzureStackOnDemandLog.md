---
title: Endpunkt „Invoke-AzureStackOnDemandLog“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Invoke-AzureStackOnDemandLog'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3c64b8efd1f5833a9b02c680ac93901bbfb59275
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486800"
---
# <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

## <a name="synopsis"></a>Zusammenfassung
Generiert ggf. bedarfsgesteuerte Protokolle aus Azure Stack Hub-Rollen.

## <a name="syntax"></a>Syntax

```
Invoke-AzureStackOnDemandLog [-OutputPath <Object>] [-SlbTimeOutInMinutes <Object>] [-Generate]
 [-FilterByRole <Object>] [-TimeOutInMinutes <Object>] [-AsJob]
```

## <a name="parameters"></a>Parameter

### <a name="-outputpath"></a>-OutputPath

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

### <a name="-filterbyrole"></a>-FilterByRole
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

### <a name="-timeoutinminutes"></a>-TimeOutInMinutes
```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 30
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-slbtimeoutinminutes"></a>-SlbTimeOutInMinutes

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 20
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-generate"></a>-Generate

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
