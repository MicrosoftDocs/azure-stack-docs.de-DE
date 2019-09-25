---
title: Datenwiederherstellung in Azure Stack mit dem Infrastructure Backup-Dienst | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Konfigurations- und Dienstdaten mithilfe des Infrastructure Backup-Diensts in Azure Stack sichern und wiederherstellen können.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 832d2146e79f3724c5f1b3bf9da1776388636da8
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974700"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service"></a>Datenwiederherstellung in Azure Stack mit dem Infrastructure Backup-Dienst

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können Konfigurations- und Dienstdaten mithilfe des Infrastructure Backup-Diensts in Azure Stack sichern und wiederherstellen. Jede Azure Stack-Installation enthält eine Instanz des Diensts. Sie können die vom Dienst erstellten Sicherungen für die erneute Bereitstellung der Azure Stack-Cloud verwenden, um Identitäts-, Sicherheits- und Azure Resource Manager-Daten wiederherzustellen.

Aktivieren Sie die Sicherung, wenn Sie bereit sind, Ihre Cloud in der Produktion einzusetzen. Aktivieren Sie die Sicherung nicht, wenn Sie planen, für einen längeren Zeitraum Tests und Überprüfungen durchzuführen.

Stellen Sie vor der Aktivierung des Sicherungsdiensts sicher, dass die [Voraussetzungen erfüllt sind](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Der Infrastructure Backup-Dienst deckt keine Benutzerdaten und Apps ab. Weitere Informationen zum Schutz von IaaS-VM-basierten Apps finden Sie unter [Schutz von in Azure Stack bereitgestellten virtuellen Computern](../user/azure-stack-manage-vm-protect.md). Umfassende Informationen zum Schützen von Apps in Azure Stack finden Sie im Whitepaper [Azure Stack: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Überlegungen zu Business Continuity und Disaster Recovery).

## <a name="the-infrastructure-backup-service"></a>Dienst für die Infrastruktursicherung

Der Dienst umfasst die folgenden Features:

| Feature                                            | BESCHREIBUNG                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherungsinfrastrukturdienste                     | Koordiniert die Sicherung für eine Teilmenge der Infrastrukturdienste in Azure Stack. Im Notfall können die Daten im Rahmen einer erneuten Bereitstellung wiederhergestellt werden. |
| Komprimierung und Verschlüsslung von exportierten Sicherungsdaten | Die Sicherungsdaten werden vom System komprimiert und verschlüsselt, bevor sie an den externen Speicherort exportiert werden, der vom Administrator angegeben wurde.                |
| Überwachung von Sicherungsaufträgen                              | Das System benachrichtigt Sie, wenn bei Sicherungsaufträgen Fehler auftreten, und teilt Ihnen mit, wie das Problem behoben werden kann.                                                                                                |
| Benutzeroberfläche für die Sicherungsverwaltung                       | Der Sicherungsressourcenanbieter unterstützt die Aktivierung der Sicherung.                                                                                                                         |
| Cloudwiederherstellung                                     | Bei einem schwerwiegenden Datenverlust können Sicherungen dazu verwendet werden, grundlegende Azure Stack-Informationen im Rahmen der Bereitstellung wiederherzustellen.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Überprüfen der Anforderungen für den Dienst zur Infrastruktursicherung

- **Speicherort**  
  Sie benötigen eine Dateifreigabe, die von Azure Stack aus zugänglich ist und sieben Sicherungen enthalten kann. Jede Sicherung ist etwa 10 GB groß. Ihre Freigabe sollte Sicherungen mit einer Größe von 140 GB speichern können. Weitere Informationen zur Auswahl eines Speicherorts für den Infrastructure Backup-Dienst finden Sie unter [Anforderungen für den Sicherungscontroller](azure-stack-backup-reference.md#backup-controller-requirements).
- **Anmeldeinformationen**  
  Sie benötigen ein Domänenbenutzerkonto und Anmeldeinformationen. Beispielsweise können Sie Ihre Administratoranmeldeinformationen für Azure Stack verwenden.
- **Verschlüsselungszertifikat**  
  Die Sicherungsdateien werden mit dem öffentlichen Schlüssel im Zertifikat verschlüsselt. Stellen Sie sicher, dass Sie dieses Zertifikat an einem sicheren Speicherort speichern. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack über das Administratorportal](azure-stack-backup-enable-backup-console.md).

Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack mit PowerShell](azure-stack-backup-enable-backup-powershell.md).

Erfahren Sie mehr über das [Sichern von Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Erfahren Sie mehr über das [Wiederherstellen nach schwerwiegendem Datenverlust](azure-stack-backup-recover-data.md).
