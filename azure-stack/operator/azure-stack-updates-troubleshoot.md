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
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301245"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Behandeln von Patch- und Updateproblemen bei Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Nutzen Sie die Hinweise in diesem Artikel, um mögliche Probleme beim Aktualisieren von Azure Stack zu beheben.

## <a name="preparationfailed"></a>PreparationFailed

**Geltungsbereich**: Dieses Problem gilt für alle unterstützten Versionen.

**Ursache:** Wenn Sie versuchen, das Azure Stack-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in `PreparationFailed` geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.

**Abhilfe**: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie erneut auf **Aktualisieren** (nicht auf **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Häufigkeit**: Common

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren von Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack: Hilfe und Support](azure-stack-help-and-support-overview.md)
