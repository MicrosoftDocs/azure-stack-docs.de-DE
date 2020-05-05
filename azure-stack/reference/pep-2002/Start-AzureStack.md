---
title: Privilegierter Endpunkt „Start-AzureStack“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Start-AzureStack'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 70b0992239fc2f1f5cc5bb4b7ffb4b8615c94c0d
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563992"
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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).