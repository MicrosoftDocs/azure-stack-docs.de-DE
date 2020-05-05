---
title: Privilegierter Endpunkt „New-GraphApplication“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: New-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: e9bd1d8fa51108f6384b29f33cf6cc0ea8cc166e
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563794"
---
# <a name="new-graphapplication"></a>New-GraphApplication

## <a name="synopsis"></a>Zusammenfassung
„New-GraphApplication“ ist eine Wrapperfunktion zum Aufrufen von AD FS-Graph-Cmdlets in AD FS.

## <a name="syntax"></a>Syntax

```
New-GraphApplication [-ClientCertificates <Object>] [-Name <Object>] [-ClientRedirectUris <Object>]
 [-GenerateClientSecret] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Ruft die „New-GraphApplicationGroup“ in AD FS auf, um eine neue Anwendung auf dem AD FS-Computer hinzuzufügen.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-Name
Der Name der Anwendung mit einer maximalen Länge von 50 Zeichen. Er wird in `Azurestack-$Name-$({guid}::{NewGuid}())`geändert und von der Funktion zurückgegeben.

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

### <a name="-clientredirecturis"></a>-ClientRedirectUris
Der Umleitungs-URI, der zum Erstellen einer neuen Anwendung in AD FS verwendet wird.

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

### <a name="-clientcertificates"></a>-ClientCertificates
Das Zertifikat, das zum Erstellen einer neuen Anwendung in AD FS verwendet wird.

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

### <a name="-generateclientsecret"></a>-GenerateClientSecret
 

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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).