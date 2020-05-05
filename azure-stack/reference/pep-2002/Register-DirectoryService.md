---
title: Privilegierter Endpunkt „Register-DirectoryService“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Register-DirectoryService'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 4ac71d57c0f2e5028883a4bede539fdf89a45afb
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563734"
---
# <a name="register-directoryservice"></a>Register-DirectoryService

## <a name="synopsis"></a>Zusammenfassung
Skript zum Registrieren einer benutzerdefinierten Active Directory-Instanz (Azure AD) beim Graph-Dienst.
**CustomADGlobalCatalog** ist der globale Katalog für eine benutzerdefinierte Active Directory-Instanz.
Dies ist der Wert `RootDomain` in der Ausgabe des Cmdlets **Get-ADForest**.

## <a name="syntax"></a>Syntax

```
Register-DirectoryService [-Force] [[-TimeoutInSecs] <Object>] [-SkipRootDomainValidation]
 [[-CustomADAdminCredential] <Object>] [-ValidateParameters] [[-CustomADGlobalCatalog] <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Skript zum Registrieren einer benutzerdefinierten Active Directory-Instanz beim Graph-Dienst.
**CustomADGlobalCatalog** ist der globale Katalog für eine benutzerdefinierte Active Directory-Instanz.
Dies ist der Wert `RootDomain` in der Ausgabe des Cmdlets **Get-ADForest**.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1
```
Register-DirectoryService -CustomADGlobalCatalog "contoso.com" -CustomADAdminCredential (Get-Credential) -TimeoutInSecs 1000
```

## <a name="parameters"></a>Parameter

### <a name="-customadglobalcatalog"></a>-CustomADGlobalCatalog
 

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

### <a name="-customadadmincredential"></a>-CustomADAdminCredential
 

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

### <a name="-validateparameters"></a>-ValidateParameters
 

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

### <a name="-skiprootdomainvalidation"></a>-SkipRootDomainValidation
 

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

### <a name="-force"></a>-Force
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
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