---
title: Privilegierter Endpunkt „Send-AzureStackDiagnosticLog“ für Azure Stack Hub
description: Referenz zum privilegierten Endpunkt „Send-AzureStackDiagnosticLog“ für PowerShell Azure Stack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f0e7bf2ebd7c3d5ef238a1e0e946a482c1e8d4a6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563680"
---
# <a name="send-azurestackdiagnosticlog"></a>Send-AzureStackDiagnosticLog

## <a name="synopsis"></a>Zusammenfassung
Sendet Azure Stack Hub-Diagnoseprotokolle an Microsoft.

## <a name="syntax"></a>Syntax

```
Send-AzureStackDiagnosticLog [[-FromDate] <Object>] [[-FilterByResourceProvider] <Object>]
 [[-FilterByRole] <Object>] [[-ToDate] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Sendet eine Anforderung an den Support Bridge Service, um Diagnoseprotokolle für den Stempel hochzuladen.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1

Im Beispiel unten werden die Protokolle der letzten vier Stunden an Microsoft gesendet.

```
PS  C:\> Send-AzureStackDiagnosticLog
```

### <a name="example-2"></a>Beispiel 2
Im Beispiel unten werden die Support Service- und WAS-Protokolle der letzten vier Stunden an Microsoft gesendet.
```
PS  C:\> Send-AzureStackDiagnosticLog -FilterByRole SupportBridgeController,WAS
```

## <a name="parameters"></a>Parameter

### <a name="-filterbyrole"></a>-FilterByRole
Optional.
Filtert nach Infrastrukturrollen-Typ.

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

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
Optional.
Filtert nach Mehrwert-Ressourcenanbietertyp.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-fromdate"></a>-FromDate
Optional.
Startzeit für die Erfassung von Protokollen.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-todate"></a>-ToDate
Optional.
Endzeit für die Erfassung von Protokollen.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
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

Informationen zum Zugreifen auf und Verwenden des privilegierten Endpunkts finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).