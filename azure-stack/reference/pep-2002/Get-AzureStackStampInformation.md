---
title: Privilegierter Endpunkt „Get-AzureStackStampInformation“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-AzureStackStampInformation'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3318ce344804174fe3252808d5857e93b8113e22
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563554"
---
# <a name="get-azurestackstampinformation"></a>Get-AzureStackStampInformation

## <a name="synopsis"></a>Zusammenfassung
Ruft die Stempelinformationen ab.

## <a name="syntax"></a>Syntax

```
Get-AzureStackStampInformation [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Importiert das ECE-Clientmodul und startet einen Aktionsplan für die Cloudrolle für den ECE-Dienst.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Get-AzureStackStampInformation
```

## <a name="parameters"></a>Parameter

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