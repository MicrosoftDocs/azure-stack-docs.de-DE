---
title: Azure Stack Hub-Checkliste für Updateaktivitäten
description: Checkliste für die Vorbereitung des Systems auf das aktuelle Azure Stack Hub-Update
author: sethmanheim
ms.topic: article
ms.date: 05/04/2020
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 03f2b9d80b4fde1f0c7b73b3c24a2d5e799d4d49
ms.sourcegitcommit: 519f4298dc1ed5c33f9c4fef811f61d61731dd84
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799848"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Azure Stack Hub-Checkliste für Updateaktivitäten

Nutzen Sie diese Checkliste, um ein Azure Stack Hub-Update vorzubereiten. Dieser Artikel enthält eine Checkliste mit Updateaktivitäten für Azure Stack Hub-Betreiber.

## <a name="prepare-for-azure-stack-hub-update"></a>Vorbereiten auf ein Azure Stack Hub-Update

| &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Aktivität &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;                   | Details                                                   |
|------------------------------|-----------------------------------------------------------|
| Überprüfen bekannter Probleme     | [Liste mit bekannten Problemen](known-issues.md)                |
| Überprüfen von Sicherheitsupdates | [Liste mit Sicherheitsupdates](release-notes-security-updates.md)      |
| Überprüfen von Add-On-Ressourcenanbieterupdates | [App Service](azure-stack-app-service-update.md)<br>[Event Hubs](resource-provider-apply-updates.md)<br> [MySQL](azure-stack-sql-resource-provider-update.md)<br>[SQL](azure-stack-mysql-resource-provider-update.md)<br>  |
| Anwenden des aktuellen OEM-Pakets | Wenden Sie sich an ihren OEM, um sicherzustellen, dass Ihr System die Mindestanforderungen für OEM-Pakete für die Azure Stack Hub-Version des Updates für Ihr System erfüllt. Stellen Sie sicher, dass Ihr OEM-Paket mit der Azure Stack Hub-Version, auf die Sie aktualisieren, kompatibel ist. Wenn das OEM-Paket nicht mit der Azure Stack Hub-Version, auf die Sie aktualisieren, kompatibel ist, müssen Sie vor dem Ausführen eines Azure Stack Hub-Updates ein Update des OEM-Pakets durchführen. Anweisungen finden Sie unter „Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub“. |
| Optional: Konfigurieren der automatischen Protokollsammlung | Es empfiehlt sich, die automatische Protokollsammlung für Ihre Azure Stack Hub-Umgebung zu konfigurieren, um die Sammlung von Systemprotokollen zu optimieren, falls Sie einmal ein Supportticket erstellen müssen. Eine Konfigurationsanleitung für die automatische Protokollsammlung finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). |
| Anwenden aktueller Hotfixes | Wenden Sie die neuesten Hotfixes an, die für die derzeit installierte Version gelten. Eine Liste der neuesten Hotfixes finden Sie im Abschnitt mit den [Versionshinweisen zum jeweiligen Hotfix](release-notes.md). |
| Ausführen des Capacity Planner-Tools | Achten Sie darauf, dass Sie für die Planung und Größenanpassung Ihrer Workloads die neueste Version des Tools [Azure Stack Hub Capacity Planner](azure-stack-capacity-planning-overview.md) verwenden. Die neueste Version umfasst Fehlerbehebungen und neue Features, die mit jedem Azure Stack Hub-Update veröffentlicht werden. |
| Ausführen von **Test-AzureStack** | Führen Sie `Test-AzureStack -Group UpdateReadiness` aus, um Betriebsprobleme zu ermitteln. Auf das Cmdlet kann über die Sitzung des privilegierten Endpunkts (Privileged Endpoint Session, PEP) zugegriffen werden. Weitere Informationen finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md). |
| Beheben von Problemen | Beheben Sie alle Betriebsprobleme, die mithilfe von `Test-AzureStack` ermittelt wurden. |
| Update verfügbar | Nur in Szenarien mit vorhandener Verbindung überprüfen Azure Stack Hub-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Kunden ohne Verbindung können neue Pakete wie [hier beschrieben](azure-stack-apply-updates.md) herunterladen und importieren. |
| Planen eines Wartungsfensters und Benachrichtigen der Benutzer | Sie sollten die Benutzer über alle Wartungsvorgänge unterrichten und normale Wartungsfenster möglichst außerhalb der Geschäftszeiten planen. Wartungsvorgänge können sich auf vorhandene Mandantenworkloads auswirken und dazu führen, dass neue Mandantenvorgänge (z. B. Erstellen, Neukonfigurieren oder Löschen von VMs) fehlschlagen. Dies ist unabhängig davon, ob der Vorgang über das Portal oder programmgesteuert über die Azure Resource Manager-API initiiert wird. Andere Vorgänge, z. B. Sicherungen, sind unter Umständen ebenfalls erst verfügbar, nachdem das Update abgeschlossen wurde. Für Express-Updates und vollständige Updates von Azure Stack Hub finden Sie in den [Versionshinweisen](release-notes.md) Schätzungen, wie lange die Updates für die von Ihnen angewendete Version dauern. |

## <a name="during-azure-stack-hub-update"></a>Während des Azure Stack Hub-Updates

| Aktivität | Details |
|--------------------|------------------------------------------------------------------------------------------------------|
| Verwalten des Updates |[Verwalten Sie Updates in Azure Stack Hub über das Betreiberportal.](azure-stack-updates.md) |
|  |  |
| Überwachen des Updates | Ist das Betreiberportal nicht verfügbar, [überwachen Sie Updates in Azure Stack Hub mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md). |
|  |  |
| Fortsetzen von Updates | Nachdem Sie Fehler mit einem Update behoben haben, können Sie [Updates in Azure Stack Hub mithilfe des privilegierten Endpunkts fortsetzen](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Führen Sie **Test-AzureStack** nicht während eines Updates aus, da dies zu Verzögerungen beim Update führen kann.

## <a name="after-azure-stack-hub-update"></a>Nach dem Azure Stack Hub-Update

| Aktivität | Details |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Anwenden aktueller Hotfixes | Wenden Sie die neuesten Hotfixes an, die für die aktualisierte Version gelten. |
| Abrufen von Verschlüsselungsschlüsseln | Rufen Sie die Verschlüsselungsschlüssel für ruhende Daten ab, und speichern Sie diese sicher außerhalb Ihrer Azure Stack Hub-Bereitstellung. Befolgen Sie die [Anleitungen zum Abrufen der Schlüssel](azure-stack-security-bitlocker.md). |
|  |  |
| Erneutes Aktivieren der Mehrinstanzenfähigkeit | Bei mehrinstanzenfähigem Azure Stack Hub müssen Sie [darauf achten, alle Gastverzeichnismandanten zu konfigurieren](azure-stack-enable-multitenancy.md#configure-guest-directory), nachdem ein Update erfolgreich durchgeführt wurde. |

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Liste mit bekannten Problemen](known-issues.md)
- [Überprüfen der Liste mit Sicherheitsupdates](release-notes-security-updates.md)
