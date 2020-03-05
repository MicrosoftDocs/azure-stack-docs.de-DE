---
title: Behandeln von Problemen mit Updates in Azure Stack Hub
description: Erfahren Sie, wie Sie als Azure Stack Hub-Bediener Probleme bei Updates beheben, sodass Azure Stack Hub so schnell wie möglich wieder in den Produktivmodus zurückkehren kann.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/23/2019
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 160e1c12b09e5a012170673ab5429c9039c8b99e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696616"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Bewährte Methoden für die Behandlung von Problemen bei Azure Stack Hub-Patches und Updates

Dieser Artikel bietet eine Übersicht über bewährte Methoden zur Behandlung von Problemen bei Azure Stack Hub-Patches und Updates sowie Korrekturen bei häufigen Patch- und Updateproblemen.


Der Azure Stack Hub-Patch und -Updatevorgang ermöglicht den Betreibern das konsistente und optimierte Anwenden von Updatepaketen. Probleme bei Patch- und Updatevorgängen sind zwar selten, können jedoch auftreten. Die folgenden Schritte werden empfohlen, wenn während des Patch- und Updatevorgangs ein Problem auftritt:

0. **Voraussetzungen:** Stellen Sie sicher, dass Sie die [Checkliste für Updateaktivitäten](release-notes-checklist.md) befolgt und die [automatische Protokollsammlung konfiguriert](azure-stack-configure-automatic-diagnostic-log-collection.md) haben.
1. Führen Sie die Behebungsschritte in der Fehlerwarnung aus, die beim Fehlschlagen des Updates erstellt wurde.
2. Lesen Sie [Häufige Probleme bei Azure Stack Hub-Patches und -Updates](#common-azure-stack-hub-patch-and-update-issues), und führen Sie die empfohlenen Aktionen aus, wenn Ihr Problem aufgeführt ist.
3. Wenn Sie das Problem mit den oben beschriebenen Schritten nicht beheben konnten, erstellen Sie ein [Azure Stack Hub-Supportticket](azure-stack-help-and-support-overview.md). Stellen Sie sicher, dass für die Zeitspanne, in der das Problem aufgetreten ist, [Protokolle gesammelt](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) wurden.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Häufige Probleme bei Azure Stack Hub-Patches und -Updates

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

### <a name="preparationfailed"></a>PreparationFailed

**Geltungsbereich**: Dieses Problem gilt für alle unterstützten Versionen.

**Ursache:** Wenn Sie versuchen, das Azure Stack Hub-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in `PreparationFailed` geändert. Bei Systemen mit Internetverbindung weist dies in der Regel darauf hin, dass das Updatepaket aufgrund einer schlechten Internetverbindung nicht richtig heruntergeladen werden kann. 

**Abhilfe**: Sie können dieses Problem umgehen, indem Sie erneut auf **Jetzt installieren** klicken. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Häufigkeit**: Allgemein

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren von Azure Stack Hub](azure-stack-updates.md)  
- [Microsoft Azure Stack Hub: Hilfe und Support](azure-stack-help-and-support-overview.md)
