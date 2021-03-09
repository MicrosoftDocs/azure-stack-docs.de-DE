---
title: Get-AksHciCredential
author: jessicaguan
description: Der PowerShell-Befehl Get-AksHciCredential greift mithilfe von kubectl auf Ihren Cluster zu.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 50f99f6e5a64a1a0b687c0dfdc42b407b4106500
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874468"
---
# <a name="get-akshcicredential"></a>Get-AksHciCredential

## <a name="synopsis"></a>Übersicht
Zugreifen auf Ihren Cluster mit `kubectl`. Hierbei wird die _kubeconfig_-Datei des angegebenen Clusters als _kubeconfig_-Standarddatei für `kubectl` verwendet.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciCredential -name <String>
                    [-outputLocation <String>]
                    [-adAuth]
```

## <a name="description"></a>BESCHREIBUNG
Zugreifen auf Ihren Cluster mit kubectl.

## <a name="examples"></a>Beispiele

### <a name="access-your-cluster-using-kubectl"></a>Zugreifen auf Ihren Cluster mit kubectl.
```powershell
PS C:\> Get-AksHciCredential -name myCluster
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der Name des Clusters.

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

### <a name="-outputlocation"></a>-outputLocation
Der Speicherort, an den kubeconfig heruntergeladen werden soll. Der Standardwert ist `%USERPROFILE%\.kube`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %USERPROFILE%\.kube
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adauth"></a>-adAuth
Verwenden Sie dieses Flag, um die Active Directory-SSO-Version von kubeconfig abzurufen.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
