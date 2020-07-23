---
title: Privilegierter Endpunkt „Get-GraphApplication“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Get-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a100b974a3d622c0b9bb51d850d9a8435d72f549
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487089"
---
# <a name="get-graphapplication"></a>Get-GraphApplication

## <a name="synopsis"></a>Zusammenfassung
„Get-GraphApplication“ ist eine Wrapperfunktion zum Abrufen der Graph-Anwendungsinformationen für den angegebenen Anwendungsnamen oder -bezeichner.

## <a name="syntax"></a>Syntax

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Ruft die „Get-GraphApplicationGroup“ auf dem AD FS-Computer auf, um die Graph-Anwendungsinformationen abzurufen.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>Parameter

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Der Anwendungsbezeichner der Anwendungsregistrierung in AD FS.

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

### <a name="-applicationname"></a>-ApplicationName
Der Anwendungsname der Anwendungsregistrierung in AD FS.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
