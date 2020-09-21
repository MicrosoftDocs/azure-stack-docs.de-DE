---
title: Wiederherstellen von Azure Stack Hub-Daten mit dem Infrastructure Backup-Dienst
description: Erfahren Sie, wie Sie Konfigurations- und Dienstdaten mithilfe des Infrastructure Backup-Diensts in Azure Stack Hub sichern und wiederherstellen können.
author: justinha
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: a6017b6bdf1a7075ae01726ddfd1513adaa4ed63
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573070"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>Datenwiederherstellung in Azure Stack Hub mit dem Infrastructure Backup-Dienst

Sie können Konfigurations- und Dienstdaten mithilfe des Infrastructure Backup-Diensts in Azure Stack Hub sichern und wiederherstellen. Jede Azure Stack Hub-Installation enthält eine Instanz des Diensts. Sie können die vom Dienst erstellten Sicherungen für die erneute Bereitstellung der Azure Stack Hub-Cloud verwenden, um Identitäts-, Sicherheits- und Azure Resource Manager-Daten wiederherzustellen.

Aktivieren Sie die Sicherung, wenn Sie bereit sind, Ihre Cloud in der Produktion einzusetzen. Aktivieren Sie die Sicherung nicht, wenn Sie planen, für einen längeren Zeitraum Tests und Überprüfungen durchzuführen.

Stellen Sie vor der Aktivierung des Sicherungsdiensts sicher, dass die [Voraussetzungen erfüllt sind](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Der Infrastructure Backup-Dienst deckt keine Benutzerdaten und Apps ab. Weitere Informationen zum Schutz von IaaS-VM-basierten Apps finden Sie unter [Schutz von in Azure Stack Hub bereitgestellten virtuellen Computern](../user/azure-stack-manage-vm-protect.md). Umfassende Informationen zum Schützen von Apps in Azure Stack Hub finden Sie im Whitepaper [Azure Stack Hub: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Überlegungen zu Business Continuity und Disaster Recovery).

## <a name="the-infrastructure-backup-service"></a>Dienst für die Infrastruktursicherung

Der Dienst umfasst die folgenden Features:

| Funktion                                            | BESCHREIBUNG                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherungsinfrastrukturdienste                     | Koordiniert die Sicherung für eine Teilmenge der Infrastrukturdienste in Azure Stack Hub. Im Notfall können die Daten im Rahmen einer erneuten Bereitstellung wiederhergestellt werden. |
| Komprimierung und Verschlüsslung von exportierten Sicherungsdaten | Die Sicherungsdaten werden vom System komprimiert und verschlüsselt, bevor sie an den externen Speicherort exportiert werden, der vom Administrator angegeben wurde.                |
| Überwachung von Sicherungsaufträgen                              | Das System benachrichtigt Sie, wenn bei Sicherungsaufträgen Fehler auftreten, und teilt Ihnen mit, wie das Problem behoben werden kann.                                                                                                |
| Benutzeroberfläche für die Sicherungsverwaltung                       | Der Sicherungsressourcenanbieter unterstützt die Aktivierung der Sicherung.                                                                                                                         |
| Cloudwiederherstellung                                     | Bei einem schwerwiegenden Datenverlust können Sicherungen dazu verwendet werden, grundlegende Azure Stack Hub-Informationen im Rahmen der Bereitstellung wiederherzustellen.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Überprüfen der Anforderungen für den Dienst zur Infrastruktursicherung

- **Speicherort**  
  Sie benötigen eine Dateifreigabe, die von Azure Stack Hub aus zugänglich ist und sieben Sicherungen enthalten kann. Jede Sicherung ist etwa 10 GB groß. Ihre Freigabe sollte Sicherungen mit einer Größe von 140 GB speichern können. Weitere Informationen zur Auswahl eines Speicherorts für den Infrastructure Backup-Dienst finden Sie unter [Anforderungen für den Sicherungscontroller](azure-stack-backup-reference.md#backup-controller-requirements).
- **Anmeldeinformationen**  
  Sie benötigen ein Domänenbenutzerkonto und Anmeldeinformationen. Beispielsweise können Sie Ihre Administratoranmeldeinformationen für Azure Stack Hub verwenden.
- **Verschlüsselungszertifikat**  
  Die Sicherungsdateien werden mit dem öffentlichen Schlüssel im Zertifikat verschlüsselt. Stellen Sie sicher, dass Sie dieses Zertifikat an einem sicheren Speicherort speichern. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack Hub über das Administratorportal](azure-stack-backup-enable-backup-console.md).

Erfahren Sie mehr über das [Aktivieren der Sicherung für Azure Stack Hub mit PowerShell](azure-stack-backup-enable-backup-powershell.md).

Erfahren Sie mehr über das [Sichern von Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

Erfahren Sie mehr über das [Wiederherstellen nach schwerwiegendem Datenverlust](azure-stack-backup-recover-data.md).
