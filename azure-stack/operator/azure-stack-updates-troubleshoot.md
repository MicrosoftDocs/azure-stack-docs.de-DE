---
title: Problembehandlung bei Updates in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Azure Stack-Bediener Probleme bei Updates beheben, sodass Azure Stack so schnell wie möglich wieder in den Produktivmodus zurückkehren kann.
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
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257746"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Behandeln von Patch- und Updateproblemen bei Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Nutzen Sie die Hinweise in diesem Artikel, um mögliche Probleme beim Aktualisieren von Azure Stack zu beheben.

## <a name="preparationfailed"></a>PreparationFailed

**Geltungsbereich**: Dieses Problem gilt für alle unterstützten Versionen.

**Ursache:** Wenn Sie versuchen, das Azure Stack-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in `PreparationFailed` geändert. Bei Systemen mit Internetverbindung weist dies in der Regel darauf hin, dass das Updatepaket aufgrund einer schlechten Internetverbindung nicht richtig heruntergeladen werden kann. 

**Abhilfe**: Sie können dieses Problem umgehen, indem Sie erneut auf **Jetzt installieren** klicken. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Häufigkeit**: Common

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren von Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack: Hilfe und Support](azure-stack-help-and-support-overview.md)
