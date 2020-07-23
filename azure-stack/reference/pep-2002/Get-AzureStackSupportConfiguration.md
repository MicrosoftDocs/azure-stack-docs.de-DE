---
title: Privilegierter Endpunkt „Get-AzureStackSupportConfiguration“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-AzureStackSupportConfiguration'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 97f6bd0fb751885a2a4a4553aba0d7a9329e5458
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487225"
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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
