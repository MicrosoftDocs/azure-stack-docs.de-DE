---
title: Privilegierter Endpunkt „Set-ServiceAdminOwner“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Set-ServiceAdminOwner'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b44a15c2b5ae1569e747a0c371d11950b5a40297
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563662"
---
# <a name="set-serviceadminowner"></a>Set-ServiceAdminOwner

## <a name="synopsis"></a>Zusammenfassung
Skript zum Aktualisieren des Dienstadministrators.

## <a name="syntax"></a>Syntax

```
Set-ServiceAdminOwner [[-TimeoutInSecs] <Object>] [[-ServiceAdminOwnerUpn] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Skript zum Aktualisieren des Benutzerprinzipalnamens (User Principal Name, UPN) des Dienstadministrators.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Set-ServiceAdminOwner -NewServiceAdminUpn "administrator@contoso.com" -TimeoutInSecs 1000
```

## <a name="parameters"></a>Parameter

### <a name="-serviceadminownerupn"></a>-ServiceAdminOwnerUpn
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

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