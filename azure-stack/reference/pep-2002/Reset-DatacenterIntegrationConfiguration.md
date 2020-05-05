---
title: Privilegierter Endpunkt „Reset-DatacenterIntegrationConfiguration“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Reset-DatacenterIntegrationConfiguration'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 42cf50694c333b91d110ac921be8b616bbc93107
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563692"
---
# <a name="reset-datacenterintegrationconfiguration"></a>Reset-DatacenterIntegrationConfiguration

## <a name="synopsis"></a>Zusammenfassung
Skript zum Zurücksetzen von Änderungen der Rechenzentrumsintegration.

## <a name="syntax"></a>Syntax

```
Reset-DatacenterIntegrationConfiguration [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Skript zum Zurücksetzen von Änderungen der Rechenzentrumsintegration.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Reset-DatacenterIntegrationConfiguration -TimeoutInSecs 2000
```

## <a name="parameters"></a>Parameter

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
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