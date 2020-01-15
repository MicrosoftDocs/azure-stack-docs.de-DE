---
title: Checkliste für Updateaktivitäten in Azure Stack Hub | Microsoft-Dokumentation
description: Checkliste für die Vorbereitung des Systems auf das aktuelle Azure Stack Hub-Update
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
ms.date: 12/10/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: a626b50a742d3219d7934e4cc3f77cd890ea1b93
ms.sourcegitcommit: c3be6b2e962c5905eb3c54f9555e13095f6b4d40
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/20/2019
ms.locfileid: "75303725"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Azure Stack Hub-Checkliste für Updateaktivitäten

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

Nutzen Sie diese Checkliste, um ein Azure Stack Hub-Update vorzubereiten. Dieser Artikel enthält eine Checkliste mit Updateaktivitäten für Azure Stack Hub-Betreiber.

## <a name="prepare-for-azure-stack-hub-update"></a>Vorbereiten auf ein Azure Stack Hub-Update

| Aktivität                     | Details                                                   |
|------------------------------|-----------------------------------------------------------|
| Überprüfen bekannter Probleme     | [Liste mit bekannten Problemen](known-issues.md)                |
| Überprüfen von Sicherheitsupdates | [Liste mit Sicherheitsupdates](release-notes-security-updates.md)      |
| Anwenden des aktuellen OEM-Pakets | Wenden Sie sich an ihren OEM, um sicherzustellen, dass Ihr System die Mindestanforderungen für OEM-Pakete für die Azure Stack Hub-Version des Updates für Ihr System erfüllt. Stellen Sie sicher, dass Ihr OEM-Paket mit der Azure Stack Hub-Version, auf die Sie aktualisieren, kompatibel ist. Wenn das OEM-Paket nicht mit der Azure Stack Hub-Version, auf die Sie aktualisieren, kompatibel ist, müssen Sie vor dem Ausführen eines Azure Stack Hub-Updates ein Update des OEM-Pakets durchführen. Anweisungen finden Sie unter „Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub“. |
| Optional: Konfigurieren der automatischen Protokollsammlung | Es empfiehlt sich, die automatische Protokollsammlung für Ihre Azure Stack Hub-Umgebung zu konfigurieren, um die Sammlung von Systemprotokollen zu optimieren, falls Sie einmal ein Supportticket erstellen müssen. Eine Konfigurationsanleitung für die automatische Protokollsammlung finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection.md). |
| Anwenden aktueller Hotfixes | Wenden Sie die neuesten Hotfixes an, die für die derzeit installierte Version gelten. Eine Liste der neuesten Hotfixes finden Sie im Abschnitt mit den Versionshinweisen zum jeweiligen Hotfix. |
| Ausführen des Capacity Planner-Tools | Achten Sie darauf, dass Sie für die Planung und Größenanpassung Ihrer Workloads die neueste Version des Tools [Azure Stack Hub Capacity Planner](azure-stack-capacity-planning-overview.md) verwenden. Die neueste Version umfasst Fehlerbehebungen und neue Features, die mit jedem Azure Stack Hub-Update veröffentlicht werden. |
| Ausführen von „Test-AzureStack“ | Führen Sie `Test-AzureStack -Group UpdateReadiness` aus, um Betriebsprobleme zu ermitteln. Auf das Cmdlet kann über die Sitzung des privilegierten Endpunkts (Privileged Endpoint Session, PEP) zugegriffen werden. Weitere Informationen finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md). |
| Beheben von Problemen | Beheben Sie alle Betriebsprobleme, die mithilfe von `Test-AzureStack` ermittelt wurden. |
| Update verfügbar | Nur in Szenarien mit vorhandener Verbindung überprüfen Azure Stack Hub-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Kunden ohne Verbindung können neue Pakete wie [hier beschrieben](azure-stack-apply-updates.md) herunterladen und importieren. |
| Benachrichtigen der Benutzer | Sie sollten die Benutzer über alle Wartungsvorgänge unterrichten und normale Wartungsfenster möglichst außerhalb der Geschäftszeiten planen. Wartungsvorgänge können sowohl Mandantenworkloads als auch Portalvorgänge beeinträchtigen. |

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
