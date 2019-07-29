---
title: 'Versionshinweise zu Azure Stack: Checkliste für Updateaktivitäten | Microsoft-Dokumentation'
description: Kurze Checkliste für die Vorbereitung des Systems auf das aktuelle Azure Stack-Update
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 49b4276da5c7e42728cf96261104f59c70355c20
ms.sourcegitcommit: 159da88a52701679571bbedde1c36b72bbfe32dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380405"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack-Checkliste für Updateaktivitäten

Dieser Artikel enthält eine Checkliste mit Updateaktivitäten für Azure Stack-Betreiber. Lesen Sie diese Informationen, wenn Sie ein Update für Azure Stack vorbereiten.

## <a name="prepare-for-azure-stack-update"></a>Vorbereiten auf ein Azure Stack-Update

| Aktivität                     | Details                                                   |
|------------------------------|-----------------------------------------------------------|
| Überprüfen bekannter Probleme     | [Liste mit bekannten Problemen](azure-stack-release-notes-known-issues-1906.md)                |
| Überprüfen von Sicherheitsupdates | [Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1906.md)      |
| Anwenden des aktuellen OEM-Pakets | Wenden Sie sich an ihren OEM, um sicherzustellen, dass Ihr System die Mindestanforderungen für OEM-Pakete für die Azure Stack-Version des Updates für Ihr System erfüllt. |
| Ausführen von „Test-AzureStack“     | Führen Sie `Test-AzureStack -Group UpdateReadiness` aus, um Betriebsprobleme zu ermitteln.      |
| Beheben von Problemen          | Beheben Sie alle Betriebsprobleme, die mithilfe von **Test-AzureStack** ermittelt wurden.                |
| Anwenden aktueller Hotfixes   | Wenden Sie die neuesten Hotfixes an, die für die derzeit installierte Version gelten.         |
| Ausführen des Capacity Planner-Tools   | Achten Sie darauf, dass Sie für die Planung und Größenanpassung Ihrer Workloads die neueste Version des Tools [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) verwenden. Die neueste Version umfasst Fehlerbehebungen und neue Features, die mit jedem Azure Stack-Update veröffentlicht werden. |
| Update verfügbar        | Nur in verbundenen Szenarios überprüfen Azure Stack-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Kunden ohne Verbindung können das neue Paket für 1906 wie [hier beschrieben](azure-stack-apply-updates.md) herunterladen und importieren. |


## <a name="during-azure-stack-update"></a>Während des Azure Stack-Updates

| Aktivität              | Details                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Verwalten des Updates         | [Verwalten Sie Updates in Azure Stack über das Betreiberportal.](azure-stack-updates.md) |
| Überwachen des Updates        | Ist das Betreiberportal nicht verfügbar, [überwachen Sie Updates in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md). |
| Fortsetzen von Updates            | Nachdem Sie Fehler mit einem Update behoben haben, können Sie [Updates in Azure Stack mithilfe des privilegierten Endpunkts fortsetzen](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Führen Sie **Test-AzureStack** nicht während eines Updates aus, da dies zu Verzögerungen beim Update führen kann.

## <a name="after-azure-stack-update"></a>Nach dem After Azure Stack-Update

| Aktivität              | Details                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Anwenden aktueller Hotfixes | Wenden Sie die neuesten Hotfixes an, die für die aktualisierte Version gelten.                          |
| Abrufen von Verschlüsselungsschlüsseln | Rufen Sie die Verschlüsselungsschlüssel für ruhende Daten ab, und speichern Sie diese sicher außerhalb Ihrer Azure Stack-Bereitstellung. Befolgen Sie die [Anleitungen zum Abrufen der Schlüssel](azure-stack-security-bitlocker.md). |

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Liste mit bekannten Problemen](azure-stack-release-notes-known-issues-1906.md)
- [Überprüfen der Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1906.md)
