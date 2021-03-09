---
title: Install-AksHciAdAuth
author: jessicaguan
description: Mit dem PowerShell-Befehl Install-AksHciAdAuth wird Active Directory Authentifizierung installiert.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d89861da54cf7fef44d8da57077aba9f372022c3
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874456"
---
# <a name="install-akshciadauth"></a>Install-AksHciAdAuth

## <a name="synopsis"></a>Übersicht
Installieren von Active Directory-Authentifizierung.

## <a name="syntax"></a>Syntax

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUser <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroup <String>    
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUserSID <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroupSID <String>    
                     [-TTL]    
                    
```

## <a name="description"></a>BESCHREIBUNG
Installieren von Active Directory-Authentifizierung.

## <a name="examples"></a>BEISPIELE

### <a name="example"></a>Beispiel
```powershell
PS C:\> Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der alphanumerische Name Ihres Kubernetes-Clusters.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-keytab"></a>-keytab
Der Dateipfad, der die KEYTAB-Datei enthält. Stellen Sie sicher, dass die Datei den Namen `current.keytab` aufweist.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-previouskeytab"></a>-previousKeytab
Die vorherige KEYTAB-Datei, die vor dem Aktualisieren des Kennworts für das Active Directory-Konto verwendet wurde. Dies ist erforderlich, wenn Active Directory-Kennwörter geändert werden.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-spn"></a>-SPN
Der Name des Dienstprinzipals, der dem AD-Konto des API-Servers zugeordnet ist.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adminuser"></a>-adminUser
Der Benutzername des Administrators, dem Clusteradministratorberechtigungen erteilt werden sollen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-admingroup"></a>-adminGroup
Der Gruppenname, dem Clusteradministratorberechtigungen erteilt werden sollen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adminusersid"></a>-adminUserSID
Die Benutzer-SID, der Clusteradministratorberechtigungen erteilt werden sollen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-admingroupsid"></a>-adminGroupSID
Die Gruppen-SID, der Clusteradministratorberechtigungen erteilt werden sollen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ttl"></a>-TTL
Gültigkeitsdauer (in Stunden) für `-previousKeytab`, wenn angegeben. Der Standardwert ist 10 Stunden.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 10
Accept pipeline input: False
Accept wildcard characters: False
```
