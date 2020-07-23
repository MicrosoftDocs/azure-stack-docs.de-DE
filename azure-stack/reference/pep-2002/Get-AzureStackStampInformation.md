---
title: Privilegierter Endpunkt „Get-AzureStackStampInformation“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-AzureStackStampInformation'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ec2fb2a7e23cbce90d0ab8ebd75ed33a3e446013
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487208"
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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
