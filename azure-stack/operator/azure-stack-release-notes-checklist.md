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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: b789b7ba1f4c55f0ee44c214d4e214b73678b1fc
ms.sourcegitcommit: 49cfe13427f5255915d5ccbed87b36eec2caf8ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68719968"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack-Checkliste für Updateaktivitäten

Dieser Artikel enthält eine Checkliste mit Updateaktivitäten für Azure Stack-Betreiber. Lesen Sie diese Informationen, wenn Sie ein Update für Azure Stack vorbereiten.

## <a name="prepare-for-azure-stack-update"></a>Vorbereiten auf ein Azure Stack-Update

| Aktivität                     | Details                                                   |
|------------------------------|-----------------------------------------------------------|
| Überprüfen bekannter Probleme     | [Liste mit bekannten Problemen](azure-stack-release-notes-known-issues-1907.md)                |
| Überprüfen von Sicherheitsupdates | [Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1907.md)      |
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
| Erneutes Aktivieren der Mehrinstanzenfähigkeit | Im Fall von mehrinstanzenfähigem Azure Stack müssen Sie [darauf achten, alle Gastverzeichnismandanten zu konfigurieren](https://docs.microsoft.com/azure-stack/operator/azure-stack-enable-multitenancy#configure-guest-directory), nachdem ein Update erfolgreich durchgeführt wurde. |

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Liste mit bekannten Problemen](azure-stack-release-notes-known-issues-1907.md)
- [Überprüfen der Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1907.md)
