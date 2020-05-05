---
title: Privilegierter Endpunkt „Set-SyslogClient“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Set-SyslogClient'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ecb66bdceef8ee4da5018f0f2ceac81cd7d57061
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563632"
---
# <a name="set-syslogclient"></a>Set-SyslogClient

## <a name="synopsis"></a>Zusammenfassung
Importiert das Endpunktzertifikat für den Syslog-Client und wendet es an.

## <a name="syntax"></a>Syntax

```
Set-SyslogClient [-OutputSeverity <Object>] [-PfxBinary <Object>] [-RemoveCertificate] [-CertPassword <Object>]
 [-AsJob]
```

## <a name="parameters"></a>Parameter

### <a name="-pfxbinary"></a>-PfxBinary
Das Zertifikat in einem Binärformat.
Verwenden Sie **Get-Content**, um das Bytearray aus der Zertifikatsdatei zu extrahieren.

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

### <a name="-certpassword"></a>-CertPassword
Das Kennwort für das Zertifikat in Form einer sicheren Zeichenfolge.

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

### <a name="-removecertificate"></a>-RemoveCertificate
 

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

### <a name="-outputseverity"></a>-OutputSeverity
 

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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).