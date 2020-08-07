---
title: Cmdlet „Start-SecretRotation“ des privilegierten Endpunkts für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack Hub-Endpunkten für PowerShell: Start-SecretRotation'
author: mattbriggs
ms.topic: reference
ms.date: 07/29/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 12614343df051b9283f2e3b8fc2af56f8fc4058e
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436442"
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
Ruft den Geheimnisrotationsprozess für Infrastrukturgeheimnisse eines Azure Stack Hub-Systems auf. Standardmäßig werden nur die Zertifikate der externen Netzwerkinfrastruktur-Endpunkte rotiert. Weitere Einzelheiten finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md).

## <a name="parameters"></a>Parameter

### <a name="-internal"></a>-Internal
Rotieren Sie Geheimnisse für interne Netzwerkinfrastruktur-Endpunkte.

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

Informationen dazu, wie Sie auf den privilegierten Endpunkt zugreifen und ihn verwenden, finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
