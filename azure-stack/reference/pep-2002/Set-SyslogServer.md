---
title: Privilegierter Endpunkt „Close-PrivilegedEndpoint“ für Azure Stack Hub
description: 'Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell: Close-PrivilegedEndpoint'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 1b0df5892149d8cd351e3a222b11cf63c9d37759
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563626"
---
# <a name="set-syslogserver"></a>Set-SyslogServer

## <a name="synopsis"></a>Zusammenfassung
Legt den Syslog-Serverendpunkt fest.

## <a name="syntax"></a>Syntax

```
Set-SyslogServer [-Remove] [[-ServerName] <Object>] [-NoEncryption] [-SkipCertificateCheck] [-UseUDP]
 [-SkipCNCheck] [[-ServerPort] <Object>] [-AsJob]
```


## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1

```
Set-SyslogServer -ServerName <FQDN or IP address of Syslog server>
```

### <a name="example-2"></a>Beispiel 2
```
-NoEncryption
```

### <a name="example-3"></a>Beispiel 3
```
-SkipCertificateCheck
```

### <a name="example-4"></a>Beispiel 4
```
-SkipCNCheck
```

### <a name="example-5"></a>Beispiel 5
```
-UseUDP
```

### <a name="example-6"></a>Beispiel 6
```
Set-SyslogServer -Remove
```

## <a name="parameters"></a>Parameter

### <a name="-servername"></a>-ServerName
Der FQDN (vollqualifizierter Domänenname) oder die IPv4-Adresse des Syslog-Servers.
Ohne Angabe eines weiteren Parameters ist das Standardprotokoll TCP mit aktivierter Verschlüsselung, Zertifikatüberprüfung und Überprüfung des Zertifikatnamens.

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

### <a name="-serverport"></a>-ServerPort
Der Serverport des Syslog-Servers.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-noencryption"></a>-NoEncryption
Geben Sie „NoEncryption“ an, wenn Sie die Verschlüsselung für TCP-Datenverkehr deaktivieren möchten.

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

### <a name="-skipcncheck"></a>-SkipCNCheck
Geben Sie „SkipCNCheck“ an, wenn Sie die Überprüfung des Zertifikatnamens überspringen möchten.

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

### <a name="-skipcertificatecheck"></a>-SkipCertificateCheck
Geben Sie „SkipCertificateCheck“ an, wenn Sie die Überprüfung des Syslog-Serverzertifikats überspringen möchten.

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

### <a name="-useudp"></a>-UseUDP
Geben Sie „UseUDP“ an, wenn der Syslog-Server das UDP-Protokoll verwendet.

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

### <a name="-remove"></a>-Remove
 

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