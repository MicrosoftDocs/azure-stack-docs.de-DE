---
title: Überwachen von Updates mithilfe von PowerShell in Azure Stack Hub
description: Hier erfahren Sie, wie Sie mit PowerShell Updates in Azure Stack Hub überwachen.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 4e80c4677bf2c21689586de574f56ecb413b7f18
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868946"
---
# <a name="monitor-updates-with-powershell-in-azure-stack-hub"></a>Überwachen von Updates mithilfe von PowerShell in Azure Stack Hub

Sie können die Azure Stack Hub-Verwaltungsendpunkte verwenden, um Ihre Updates zu überwachen und zu verwalten. Sie können darauf mit PowerShell zugreifen. Anweisungen zum Einrichten von PowerShell in Azure Stack Hub finden Sie unter [Installieren von PowerShell für Azure Stack Hub](powershell-install-az-module.md).

Sie können die folgenden PowerShell-Cmdlets verwenden, um Ihre Updates zu verwalten:

| Cmdlet | BESCHREIBUNG |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](/powershell/module/azs.update.admin/get-azsupdate?view=azurestackps-1.8.0) | Abrufen der Liste verfügbarer Updates |
| [Get-AzsUpdateLocation](/powershell/module/azs.update.admin/get-azsupdatelocation?view=azurestackps-1.8.0)| Abrufen der Liste der Updatespeicherorte |
| [Get-AzsUpdateRun](/powershell/module/azs.update.admin/get-azsupdaterun?view=azurestackps-1.8.0) | Abrufen der Liste der Updateausführungen  |
| [Install-AzsUpdate](/powershell/module/azs.update.admin/install-azsupdate?view=azurestackps-1.8.0) | Anwenden eines bestimmten Updates an einem Updatespeicherort |
| [Resume-AzsUpdateRun](/powershell/module/azs.update.admin/resume-azsupdaterun?view=azurestackps-1.8.0) | Fortsetzen einer zuvor gestarteten fehlerhaften Updateausführung |

## <a name="get-a-list-of-update-runs"></a>Abrufen einer Liste der Updateausführungen

Führen Sie zum Abrufen der Liste der Updateausführungen den folgenden Befehl aus:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Fortsetzen eines fehlerhaften Updatevorgangs

Wenn beim Update ein Fehler auftritt, können Sie den Updatevorgang an der Stelle, an der er abgebrochen wurde, wieder aufnehmen, indem Sie den folgenden Befehl ausführen:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="troubleshoot"></a>Problembehandlung

Weitere Informationen zur Problembehandlung von Updates finden Sie unter [Behandeln von Problemen in Azure Stack Hub](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack Hub](./azure-stack-updates.md)
