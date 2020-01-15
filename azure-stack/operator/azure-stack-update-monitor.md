---
title: Überwachen von Updates in Azure Stack mithilfe von PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von PowerShell Updates in Azure Stack überwachen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: f14b4180e567df13e576b520cdac01a676c101ea
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717942"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Überwachen von Updates in Azure Stack Hub mithilfe von PowerShell

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

Sie können die Azure Stack Hub-Verwaltungsendpunkte verwenden, um Ihre Updates zu überwachen und zu verwalten. Sie können darauf mit PowerShell zugreifen. Anweisungen zum Einrichten von PowerShell in Azure Stack Hub finden Sie unter [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md).

Sie können das folgende PowerShell-Cmdlet verwenden, um Ihre Updates zu verwalten:

| Cmdlet | BESCHREIBUNG |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Abrufen der Liste verfügbarer Updates |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| Abrufen der Liste der Updatespeicherorte |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | Abrufen der Liste der Updateausführungen  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Anwenden eines bestimmten Updates an einem Updatespeicherort |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Fortsetzen einer zuvor gestarteten fehlerhaften Updateausführung |

## <a name="get-a-list-of-update-runs"></a>Abrufen einer Liste der Updateausführungen

Verwenden Sie zum Abrufen der Liste der Updateausführungen den folgenden Befehl:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Fortsetzen eines fehlerhaften Updatevorgangs

Wenn beim Update ein Fehler auftritt, können Sie den Updatevorgang an der Stelle, an der er abgebrochen wurde, wieder aufnehmen, indem Sie den folgenden Befehl ausführen:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>Nächste Schritte

-   [Verwalten von Updates in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
