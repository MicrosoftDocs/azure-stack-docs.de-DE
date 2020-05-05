---
title: Privilegierter Endpunkt „Remove-GraphApplication“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Remove-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 164a20928f312d2ffa4dea73f54c05f41809ebf5
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563716"
---
# <a name="remove-graphapplication"></a>Remove-GraphApplication

## <a name="synopsis"></a>Zusammenfassung
„Remove-GraphApplication“ ist eine Wrapperfunktion zum Aufrufen von AD FS-Graph-Cmdlets in AD FS.

## <a name="syntax"></a>Syntax

```
Remove-GraphApplication [[-ApplicationIdentifier] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Ruft „Remove-GraphApplication“ in AD FS auf, um die jeweilige Anwendung auf dem AD FS-Computer zu entfernen.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Remove-GraphApplication -ApplicationIdentifier "Application-Identifier-123456"
```

## <a name="parameters"></a>Parameter

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Der Bezeichner der zu löschenden Anwendung.

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