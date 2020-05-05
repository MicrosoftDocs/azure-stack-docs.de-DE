---
title: Privilegierter Endpunkt „Get-AzureStackSupportConfiguration“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-AzureStackSupportConfiguration'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 710201cc045b356037a54e6f7f0eeee63284b0d6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563542"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>Zusammenfassung
Ruft die Konfigurationseinstellungen des Supportdiensts ab.

## <a name="syntax"></a>Syntax

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Konfigurationseinstellungen des Supportdiensts.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
Im folgenden Beispiel werden Registrierungsdetails abgerufen, wenn der Stempel registriert wurde. Andernfalls wird NULL zurückgegeben.

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>Parameter

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
Optional.
Hierfür ist eine Internetverbindung erforderlich.
Ruft die Objekt-ID der Registrierungsidentität ab.

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

## <a name="notes"></a>Notizen
Erfordert, dass die unterstützende VM über eine Internetverbindung verfügt.

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).