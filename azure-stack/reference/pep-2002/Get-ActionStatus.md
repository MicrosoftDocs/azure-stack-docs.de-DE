---
title: Privilegierter Endpunkt „Get-ActionStatus“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-ActionStatus'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f7300224d6b512361c978c33a2f599dfb9a1d852
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563614"
---
# <a name="get-actionstatus"></a>Get-ActionStatus

## <a name="synopsis"></a>Zusammenfassung
Ruft den Status der letzten Aktion für den Vorgang mit dem angegebenen Funktionsnamen ab.

## <a name="syntax"></a>Syntax

```
Get-ActionStatus [[-ActionType] <Object>] [[-FunctionName] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Ruft den Status der letzten Aktion für den Vorgang mit dem angegebenen Funktionsnamen ab.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Get-ActionStatus -FunctionName "Start-SecretRotation"
```

## <a name="parameters"></a>Parameter

### <a name="-functionname"></a>-FunctionName
Der Name der Funktion, für die der Status abgefragt wird.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-actiontype"></a>-ActionType
Der Typname der Aktion des Vorgangs.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
