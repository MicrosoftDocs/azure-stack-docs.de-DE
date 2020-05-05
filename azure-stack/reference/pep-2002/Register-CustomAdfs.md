---
title: Privilegierter Endpunkt „Register-CustomAdfs“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Register-CustomAdfs'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: fd40c3930705d450d7e48b373f209b6e2ce7d218
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563758"
---
# <a name="register-customadfs"></a>Register-CustomAdfs

## <a name="synopsis"></a>Zusammenfassung
Skript zum Registrieren einer benutzerdefinierten AD FS-Instanz (Active Directory-Verbunddienste) als Anspruchsanbieter mit Azure Stack AD FS

## <a name="syntax"></a>Syntax

```
Register-CustomAdfs [-CustomADFSFederationMetadataEndpointUri <Object>]
 [-CustomADFSFederationMetadataFileContent <Object>] [-TimeoutInSecs <Object>] [-CustomAdfsName <Object>]
 [-SigningCertificateRevocationCheck <Object>] [-EncryptionCertificateRevocationCheck <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Skript zum Registrieren einer benutzerdefinierten AD FS-Instanz (Active Directory-Verbunddienste) als Anspruchsanbieter mit Azure Stack AD FS

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataEndpointUri "https://adfs.contoso.com/federationmetadata/2007-06/federationmetadata.xml"  -TimeoutInSecs 1000
```

### <a name="example-2"></a>Beispiel 2
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataFile "c:\temp\FederationMetadata.xml" -TimeoutInSecs 1000
```
## <a name="parameters"></a>Parameter

### <a name="-customadfsname"></a>-CustomAdfsName
 

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

### <a name="-customadfsfederationmetadataendpointuri"></a>-CustomADFSFederationMetadataEndpointUri
 

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

### <a name="-customadfsfederationmetadatafilecontent"></a>-CustomADFSFederationMetadataFileContent
 

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

### <a name="-encryptioncertificaterevocationcheck"></a>-EncryptionCertificateRevocationCheck
 

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

### <a name="-signingcertificaterevocationcheck"></a>-SigningCertificateRevocationCheck
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: CheckChainExcludeRoot
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
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