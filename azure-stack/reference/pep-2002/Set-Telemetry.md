---
title: Set-Telemetry
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Set-Telemetry'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 89e9182dd5ebb7a7ab9da6e9d4e7b21da6d4ce9e
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810914"
---
# <a name="set-telemetry"></a>Set-Telemetry

## <a name="synopsis"></a>Zusammenfassung
Aktiviert oder deaktiviert die Übertragung von Telemetriedaten an Microsoft.

## <a name="syntax"></a>Syntax

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Mit dem Cmdlet „Set-Telemetry“ können Sie steuern, ob Telemetriedaten an Microsoft gesendet werden, indem Sie die entsprechende Einstellung in der Registrierung ändern.

Insbesondere wird mit diesem Cmdlet die Domänengruppenrichtlinie so konfiguriert, dass der Telemetrieregistrierungswert auf 0 festgelegt und der Windows-UTC-Dienst nicht auf allen Infrastruktur-VMs und-Hosts ausgeführt wird.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>Beispiel 2
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>Parameter

### <a name="-enable"></a>-Enable
Aktiviert die Übertragung von Telemetriedaten an Microsoft.

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

### <a name="-disable"></a>-Disable
Deaktiviert die Übertragung von Telemetriedaten an Microsoft.

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

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
