---
title: Datenwiederherstellung in Azure Stack mit dem Infrastructure Backup-Dienst – MDC
description: Erfahren Sie, wie Sie Konfigurations- und Dienstdaten mithilfe des Infrastructure Backup-Diensts in Azure Stack sichern und wiederherstellen können. Für Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 5a9e8615168714b3346f735d9182c38b2c604cca
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910804"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service---modular-data-center-mdc"></a>Datenwiederherstellung in Azure Stack mit dem Infrastructure Backup-Dienst – Modular Data Center (MDC)

*Anwendungsbereich: Modular Data Center und Azure Stack Hub Ruggedized*

Sie können Infrastrukturdienstmetadaten mithilfe des Azure Stack Infrastructure Backup-Diensts sichern. Diese Sicherungen werden verwendet, um Probleme bei beeinträchtigten Infrastrukturdiensten zu beheben. Sie enthalten nur Daten aus systeminternen Infrastrukturdiensten. Die Sicherungsdaten umfassen keine Benutzer- und Anwendungsdaten. Benutzer- und Anwendungsdaten müssen separat geschützt werden.

Infrastruktursicherungen sind zur Bereitstellungszeit standardmäßig aktiviert. Diese Sicherungen werden systemintern gespeichert und sind nur in Fällen von erweitertem Support zugänglich. Wenn das System über Zugriff auf einen externen Speicherort verfügt, kann der Infrastructure Backup-Dienst angewiesen werden, eine Sicherung als sekundäre Kopie an diesen Speicherort zu exportieren.

Stellen Sie vor der Aktivierung des Sicherungsdiensts sicher, dass die [Voraussetzungen erfüllt sind](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

> [!NOTE]
> Der Infrastructure Backup-Dienst deckt keine Benutzerdaten und Apps ab. Weitere Informationen zum Schützen von IaaS-VM-basierten Apps finden Sie in den folgenden Artikeln:
>
> - [Schutz von in Azure Stack bereitgestellten VMs](../../user/azure-stack-manage-vm-protect.md)
> - Schützen von Event Hubs-Zeitreihendaten in Azure Stack
> - Schützen von Blobdaten in Azure Stack

## <a name="the-infrastructure-backup-service"></a>Dienst für die Infrastruktursicherung

Der Dienst umfasst die folgenden Features:

| Funktion                                            | BESCHREIBUNG                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherungsinfrastrukturdienste                     | Koordiniert die Sicherung für eine Teilmenge der Infrastrukturdienste in Azure Stack |
| Komprimierung und Verschlüsslung von Sicherungsdaten | Die Sicherungsdaten werden vom System komprimiert und verschlüsselt, bevor sie intern gespeichert oder an den externen Speicherort exportiert werden, der vom Operator angegeben wurde.                |
| Überwachung von Sicherungsaufträgen                              | Das System benachrichtigt Sie, wenn bei Sicherungsaufträgen Fehler auftreten, und teilt Ihnen mit, wie das Problem behoben werden kann.                                                                                                |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Überprüfen der Anforderungen für den Dienst zur Infrastruktursicherung

- **Speicherort**: Wenn zuverlässiger Zugriff auf einen externen Speicherort besteht, benötigen Sie eine Dateifreigabe, auf die von der Azure Stack-Infrastruktur aus zugegriffen werden kann und in der mehrere Sicherungen gespeichert werden können. Weitere Informationen zur Wahl eines Speicherorts für den Infrastructure Backup-Dienst finden Sie unter [Anforderungen an Backup Controller](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

- **Anmeldeinformationen**: Sie benötigen die Anmeldeinformationen für ein Benutzerkonto, das auf den Speicherort zugreifen kann.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack über das Administratorportal](../../operator/azure-stack-backup-enable-backup-console.md).

- Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack mit PowerShell](../../operator/azure-stack-backup-enable-backup-powershell.md).
