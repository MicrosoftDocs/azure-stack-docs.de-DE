---
title: Privilegierter Endpunkt „Set-TLSPolicy“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Set-TLSPolicy'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a662ccbc76d25a936bd91bfe7ec499458c538321
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563620"
---
# <a name="set-tlspolicy"></a>Set-TLSPolicy

## <a name="syntax"></a>Syntax

```
Set-TLSPolicy [[-Version] <Object>] [-AsJob]
```

## <a name="parameters"></a>Parameter

### <a name="-version"></a>-Version
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Accepted values: TLS_1.2, TLS_All

Required: False
Position: 0
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