---
title: Planen eines Azure Stack-Updates | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Azure Stack-Update planen.
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
ms.openlocfilehash: b4f98f61068d6c428dc17c635651ad9aac54f6a8
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010323"
---
# <a name="plan-for-an-azure-stack-update"></a>Planen eines Azure Stack-Updates

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Sie können Ihre Azure Stack-Instanz vorbereiten, um den Aktualisierungsprozess so reibungslos wie möglich zu gestalten, damit die Benutzer nur minimal beeinträchtigt werden. Informieren Sie sich anhand der Schritte in diesem Artikel, wie Sie Ihre Benutzer über mögliche Dienstausfälle informieren und ein Update vorbereiten.

## <a name="notify-your-users-of-maintenance-operations"></a>Informieren Ihrer Benutzer über Wartungsmaßnahmen

Sie sollten die Benutzer über alle Wartungsvorgänge unterrichten und normale Wartungsfenster möglichst außerhalb der Geschäftszeiten planen. Wartungsvorgänge können sowohl Mandantenworkloads als auch Portalvorgänge beeinträchtigen.

## <a name="prepare-for-an-azure-stack-update"></a>Vorbereiten eines Azure Stack-Updates

Sie können ein Update vorbereiten, indem Sie sicherstellen, dass alle Hotfixes, Sicherheitspatches und OEM-Updates angewandt wurden. Außerdem sollten Sie die Integrität Ihrer Azure Stack Instanz, die verfügbare Kapazität und das Updatepaket überprüft haben.

1. Überprüfen Sie die bekannten Probleme. Anweisungen hierzu finden Sie unter [Azure Stack – bekannte Probleme](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-known-issues-1907).

2. Überprüfen Sie die Sicherheitsupdates. Eine Liste der Updates finden Sie unter [Azure Stack-Sicherheitsupdates](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-security-updates-1907).

3. Bevor Sie mit der Installation dieses Updates beginnen, sollten Sie [Test-AzureStack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test) ausführen, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und führen Sie die Behebung für Einträge durch, für die eine Aktion erforderlich ist.

    1. Öffnen Sie auf einem Computer mit Netzwerkzugriff auf die Azure Stack ERCS-VMs eine PEP-Sitzung (privilegierter Endpunkt). Anweisungen zur Verwendung von PEP finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).

    2. Test-AzureStack -Group UpdateReadiness

    3. Überprüfen Sie die Ausgabe, und lösen Sie alle Probleme in Bezug auf die Integrität. Weitere Informationen finden Sie unter [Ausführen eines Validierungstests für Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test).

4. Beheben Sie alle Probleme. Weitere Informationen zu dem markierten Problem finden Sie im Thema zum Überwachen von Integrität und Warnungen. Anweisungen finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health).

5. Wenden Sie die aktuellen Hotfixes an. Eine Liste der neuesten Hotfixes finden Sie im Abschnitt mit den Versionshinweisen zum jeweiligen Hotfix. Wiederholen Sie die Schritte 3 und 4, nachdem Sie den neuesten Hotfix angewandt haben.

6. Stellen Sie sicher, dass Ihr OEM-Paket mit der Azure Stack-Version, auf die Sie aktualisieren, kompatibel ist. Wenn das OEM-Paket nicht mit der Azure Stack-Version, auf die Sie aktualisieren, kompatibel ist, müssen Sie vor dem Ausführen eines Azure Stack-Updates ein Update des OEM-Pakets durchführen. Anweisungen finden Sie unter „Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack“. Wiederholen Sie die Schritte 3 und 4 nach dem Anwenden des OEM-Paketupdates.

7. Führen Sie das Tool Capacity Planner aus. Eine Übersicht und Anweisungen zur Verwendung des Tools finden Sie unter [Übersicht über die Azure Stack-Kapazitätsplanung](https://docs.microsoft.com/azure-stack/operator/azure-stack-capacity-planning-overview).

8. Überprüfen Sie das Updatepaket. Bei der Planung für Ihr Wartungsfenster ist es wichtig, den spezifischen Typ des von Microsoft herausgegebenen Updatepakets zu prüfen. Hierauf wird auch in den Versionshinweisen hingewiesen.

## <a name="next-steps"></a>Nächste Schritte

[Wenden Sie das Azure Stack-Update an.](azure-stack-apply-updates.md)
