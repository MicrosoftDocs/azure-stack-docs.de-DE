---
title: Uninstall-AksHci
author: jessicaguan
description: Mit dem PowerShell-Befehl Uninstall-AksHci wird AKS auf Azure Stack HCI entfernt.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 89cd1bd48518f90dd4ce850e35e42c3106681823
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874485"
---
# <a name="uninstall-akshci"></a>Uninstall-AksHci

## <a name="synopsis"></a>Übersicht
Entfernen von Azure Kubernetes Service auf Azure Stack HCI.

## <a name="syntax"></a>Syntax

```powershell
Uninstall-AksHci [-skipConfigCleanup]
```

## <a name="description"></a>BESCHREIBUNG
Entfernen von Azure Kubernetes Service auf Azure Stack HCI. 

Wenn PowerShell-Befehle in einem Cluster ausgeführt werden, auf dem zuvor Windows Admin Center für die Bereitstellung verwendet wurde, überprüft das PowerShell-Modul, ob die Windows Admin Center-Konfigurationsdatei vorhanden ist. Windows Admin Center platziert die Windows Admin Center-Konfigurationsdatei knotenübergreifend. 

## <a name="example"></a>Beispiel

### <a name="example"></a>Beispiel
```powershell
PS C:\> Uninstall-AksHci
```

## <a name="parameters"></a>Parameter

### <a name="-skipconfigcleanup"></a>-skipConfigCleanup
Überspringen des Entfernens der Konfigurationen nach der Deinstallation. Wenn Sie dieses Flag verwenden, wird die alte Konfiguration beibehalten.

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
