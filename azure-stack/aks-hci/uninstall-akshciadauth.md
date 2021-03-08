---
title: Uninstall-AksHciAdAuth
author: jessicaguan
description: Mit dem PowerShell-Befehl Uninstall-AksHciAdAuth wird AD-Authentifizierung deinstalliert.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 4239d9c268d57315584381e02fa121848395e683
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874432"
---
# <a name="uninstall-akshciadauth"></a>Uninstall-AksHciAdAuth

## <a name="synopsis"></a>Übersicht
Deinstallieren von Active Directory-Authentifizierung.

## <a name="syntax"></a>Syntax

```powershell
Uninstall-AksHciAdAuth -name <String>
```

## <a name="description"></a>BESCHREIBUNG
Deinstallieren von Active Directory-Authentifizierung.

## <a name="examples"></a>Beispiele

### <a name="example"></a>Beispiel
```powershell
PS C:\> Uninstall-AksHciAksHciAdAuth -name myCluster
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
