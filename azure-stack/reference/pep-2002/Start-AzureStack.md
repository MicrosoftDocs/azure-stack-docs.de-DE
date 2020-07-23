---
title: Privilegierter Endpunkt „Start-AzureStack“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Start-AzureStack'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2a8814182d518c7327a64324cdc844018e989f18
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485848"
---
# <a name="start-azurestack"></a>Start-AzureStack

## <a name="synopsis"></a>Zusammenfassung
Startet alle Azure Stack Hub-Dienste.

## <a name="syntax"></a>Syntax

```
Start-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Startet alle Azure Stack Hub-Dienste.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> 
```



## <a name="parameters"></a>Parameter

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
Maximale Zeitspanne, nach der die Ausführung angehalten wird.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 2400
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
