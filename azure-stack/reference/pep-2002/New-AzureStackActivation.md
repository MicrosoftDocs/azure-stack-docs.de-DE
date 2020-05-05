---
title: Privilegierter Endpunkt „New-AzureStackActivation“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: New-AzureStackActivation'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: fdd30f54973d5e1d6485e89b30c1ae811c950822
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563818"
---
# <a name="new-azurestackactivation"></a>New-AzureStackActivation

## <a name="synopsis"></a>Zusammenfassung
Aktiviert Azure Stack Hub.

## <a name="syntax"></a>Syntax

```
New-AzureStackActivation [[-TimeoutInSeconds] <Object>] [[-ActivationKey] <Object>] [-AsJob]
```

## <a name="parameters"></a>Parameter

### <a name="-activationkey"></a>-ActivationKey
 

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

### <a name="-timeoutinseconds"></a>-TimeoutInSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 1000
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