---
title: Sicherung und Datenwiederherstellung für Azure Stack mit dem Dienst für die Infrastruktursicherung | Microsoft-Dokumentation
description: Sie können Konfigurations- und Dienstdaten mithilfe des Diensts für die Infrastruktursicherung sichern und wiederherstellen.
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
ms.openlocfilehash: 05bdf2fe5930777d44ec42eab8dede97b758cd15
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268854"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Sicherung und Datenwiederherstellung für Azure Stack mit dem Dienst für die Infrastruktursicherung

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können Konfigurations- und Dienstdaten mithilfe des Diensts für die Infrastruktursicherung sichern und wiederherstellen. Jede Azure Stack-Installation enthält eine Instanz des Diensts. Sie können die vom Dienst erstellten Sicherungen für die erneute Bereitstellung der Azure Stack-Cloud verwenden, um Identitäts-, Sicherheits- und Azure Resource Manager-Daten wiederherzustellen. 

Sie können die Sicherung aktivieren, wenn Sie bereit sind, Ihre Cloud in der Produktion einzusetzen. Aktivieren Sie die Sicherung nicht, wenn Sie planen, für einen längeren Zeitraum Tests und Überprüfungen durchzuführen.

Stellen Sie vor der Aktivierung des Sicherungsdiensts sicher, dass die [Voraussetzungen erfüllt sind](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Der Dienst für die Infrastruktursicherung deckt keine Benutzerdaten und Anwendungen ab. Weitere Informationen zum Schutz von IaaS-VM-basierten Anwendungen finden Sie unter [Schutz von in Azure Stack bereitgestellten virtuellen Computern](../user/azure-stack-manage-vm-protect.md). Umfassende Informationen zum Schützen von Anwendungen in Azure Stack finden Sie im Whitepaper [Azure Stack: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Überlegungen zu Business Continuity und Disaster Recovery).

## <a name="the-infrastructure-backup-service"></a>Dienst für die Infrastruktursicherung

Der Dienst umfasst die folgenden Features.

| Feature                                            | BESCHREIBUNG                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherungsinfrastrukturdienste                     | Koordiniert die Sicherung für eine Teilmenge der Infrastrukturdienste in Azure Stack. Im Notfall können die Daten im Rahmen einer erneuten Bereitstellung wiederhergestellt werden. |
| Komprimierung und Verschlüsslung von exportierten Sicherungsdaten | Die Sicherungsdaten werden vom System komprimiert und verschlüsselt, bevor sie an den externen Speicherort exportiert werden, der vom Administrator angegeben wurde.                |
| Überwachung von Sicherungsaufträgen                              | Das System gibt Benachrichtigungen zu Fehlern bei Sicherungsaufträgen aus und zeigt Bereinigungsschritte an.                                                                                                |
| Benutzeroberfläche für die Sicherungsverwaltung                       | Der Sicherungsressourcenanbieter unterstützt die Aktivierung der Sicherung.                                                                                                                         |
| Cloudwiederherstellung                                     | Bei einem schwerwiegenden Datenverlust können Sicherungen dazu verwendet werden, grundlegende Azure Stack-Informationen im Rahmen der Bereitstellung wiederherzustellen.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Überprüfen der Anforderungen für den Dienst zur Infrastruktursicherung

- **Speicherort**  
  Sie benötigen eine Dateifreigabe, die von Azure Stack aus zugänglich ist und sieben Sicherungen enthalten kann. Jede Sicherung ist etwa 10 GB groß. Ihre Freigabe sollte Sicherungen mit einer Größe von 140 GB speichern können. Weitere Informationen zur Auswahl eines Speicherorts für den Azure Stack-Dienst für die Infrastruktursicherung finden Sie unter [Anforderungen für den Sicherungscontroller](azure-stack-backup-reference.md#backup-controller-requirements).
- **Anmeldeinformationen**  
  Sie benötigen ein Domänenbenutzerkonto und Anmeldeinformationen. Sie können beispielsweise die Anmeldeinformationen des Azure Stack-Administrators verwenden.
- **Verschlüsselungszertifikat**  
  Die Sicherungsdateien werden mit dem öffentlichen Schlüssel im Zertifikat verschlüsselt. Stellen Sie sicher, dass Sie dieses Zertifikat an einem sicheren Speicherort speichern. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack über das Verwaltungsportal](azure-stack-backup-enable-backup-console.md).

Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack mit PowerShell](azure-stack-backup-enable-backup-powershell.md).

Erfahren Sie mehr über das [Sichern von Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

Erfahren Sie mehr über das [Wiederherstellen nach schwerwiegendem Datenverlust](azure-stack-backup-recover-data.md).
