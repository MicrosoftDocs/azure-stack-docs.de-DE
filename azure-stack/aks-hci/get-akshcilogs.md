---
title: Get-AksHciLogs
author: jessicaguan
description: Der PowerShell-Befehl Get-AksHciLogs erstellt einen ZIP-Ordner mit Protokollen von allen ihren Pods.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: ef78efb91a6a8b5c0c91e815cb8aedd0a57adfe8
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874466"
---
# <a name="get-akshcilogs"></a>Get-AksHciLogs

## <a name="synopsis"></a>Übersicht
Erstellen eines ZIP-Ordners mit Protokollen von allen Ihren Pods. 

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciLogs
```

## <a name="description"></a>BESCHREIBUNG
Erstellen eines ZIP-Ordners mit Protokollen von allen Ihren Pods. Dieser Befehl erstellt einen ZIP-Ausgabeordner namens `akshcilogs.zip` in Ihrem AKS auf Azure Stack HCI-Arbeitsverzeichnis. Der vollständige Pfad zur Datei `akshcilogs.zip` ist die Ausgabe nach der Ausführung von `Get-AksHciLogs` (z. B. `C:\AksHci\0.9.6.3\akshcilogs.zip`, wobei `0.9.6.3` die AKS auf Azure Stack HCI-Releasenummer ist).

## <a name="examples"></a>Beispiele

### <a name="example"></a>Beispiel
```powershell
PS C:\> Get-AksHciLogs
```
