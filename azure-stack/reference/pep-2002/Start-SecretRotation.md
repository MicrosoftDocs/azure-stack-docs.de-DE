---
title: Privilegierter Endpunkt „Close-PrivilegedEndpoint“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Close-PrivilegedEndpoint'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b61955c6fce760654430b999c3b393e9093db535
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564016"
---
# <a name="start-secretrotation"></a>Start-SecretRotation

## <a name="synopsis"></a>Zusammenfassung
Löst die Geheimnisrotation für einen Stempel aus.

## <a name="syntax"></a>Syntax

```
Start-SecretRotation [-PathAccessCredential <Object>] [-ReRun] [-CertificatePassword <Object>] [-Internal]
 [-PfxFilesPath <Object>] [-AsJob]
```

## <a name="description"></a>BESCHREIBUNG
Ruft den Prozess für die Geheimnisrotation auf.

## <a name="parameters"></a>Parameter

### <a name="-internal"></a>-Internal
 

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

### <a name="-pfxfilespath"></a>-PfxFilesPath
Der Pfad der neuen PFX-Dateien, die für die externe Zertifikatrotation freigegeben werden.
Diese Angabe ist obligatorisch, wenn eine externe Zertifikatrotation erfolgen soll.

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

### <a name="-pathaccesscredential"></a>-PathAccessCredential
Die Anmeldeinformationen für den Zugriff auf „PfxFilesPath“.
Diese Angabe ist obligatorisch, wenn eine externe Zertifikatrotation erfolgen soll.

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

### <a name="-certificatepassword"></a>-CertificatePassword
Das Kennwort für die bereitgestellten neuen PFX-Dateien.
Diese Angabe ist obligatorisch, wenn eine externe Zertifikatrotation erfolgen soll.
Es kann sich vom ursprünglichen PFX-Kennwort unterscheiden, das bei der Erstbereitstellung angegeben wurde.
Die PFX-Dateien werden mit dem richtigen Zertifizierungsstellenkennwort erneut generiert.

Syntax:
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun

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

### <a name="-rerun"></a>-ReRun
 

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