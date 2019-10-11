---
title: Wiederherstellung nach schwerwiegendem Datenverlust in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Infrastrukturdaten in Azure Stack nach einem schwerwiegenden Datenverlust wiederherstellen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: fd7c1ede611f9505ed48c8efc3caa5311c285cd3
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342867"
---
# <a name="recover-from-catastrophic-data-loss"></a>Wiederherstellen nach schwerwiegendem Datenverlust

*Anwendungsbereich: Integrierte Azure Stack-Systeme.*

Azure Stack führt Dienste in Ihrem Rechenzentrum aus und kann in so kleinen Umgebungen wie vier in einem Rack installierten Knoten ausgeführt werden. Im Gegensatz dazu wird Azure in mehr als 40 Regionen in mehreren Rechenzentren und mehreren Zonen in jeder Region ausgeführt. Benutzerressourcen können mehrere Server, Racks, Rechenzentren und Regionen umfassen. Mit Azure Stack haben Sie derzeit nur die Möglichkeit, Ihre gesamte Cloud in einem einzelnen Rack bereitzustellen. Diese Einschränkung setzt Ihre Cloud dem Risiko von Katastrophenfällen in Ihrem Rechenzentrum oder Ausfällen aufgrund von gravierenden Produktfehlern aus. Bei einem Notfall wird die Azure Stack-Instanz offline geschaltet. Alle Daten sind möglicherweise nicht mehr wiederherstellbar.

Je nach Grundursache des Datenverlusts müssen Sie eventuell einen einzelnen Infrastrukturdienst reparieren oder die gesamte Azure Stack-Instanz wiederherstellen. Unter Umständen müssen Sie die Wiederherstellung sogar auf einer anderen Hardware am gleichen oder an einem anderen Standort durchführen.

Das folgende Szenario betrifft die Wiederherstellung Ihrer gesamten Installation bei einem Ausfall und die erneute Bereitstellung der privaten Cloud.

| Szenario                                                           | Datenverlust                            | Überlegungen                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Wiederherstellen nach schwerwiegendem Datenverlust aufgrund eines Notfalls oder Produktfehlers. | Alle Infrastruktur-, Benutzer- und App-Daten. | Benutzer-App und -daten werden getrennt von Infrastrukturdaten gesichert. |

## <a name="workflows"></a>Workflows

Der Schutz von Azure Stack beginnt mit der getrennten Sicherung der Infrastrukturdaten und der Anwendungs-/Mandantendaten. In diesem Dokument wird erläutert, wie die Infrastruktur gesichert wird. 

![Workflow für die Azure Stack-Datenwiederherstellung – Bereitstellung](media/azure-stack-backup/azure-stack-backup-workflow1.png)

Im ungünstigsten Fall, bei dem alle Daten verloren gehen, besteht die Wiederherstellung von Azure Stack darin, die spezifischen Infrastrukturdaten dieser Bereitstellung von Azure Stack und alle Benutzerdaten wiederherzustellen. 

![Workflow für die Azure Stack-Datenwiederherstellung – erneute Bereitstellung](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restore

Wenn der Datenverlust schwerwiegend ist, die Hardware jedoch weiterhin genutzt werden kann, muss Azure Stack erneut bereitgestellt werden. Während der erneuten Bereitstellung können Sie den Speicherort und die für den Zugriff auf Sicherungen erforderlichen Anmeldeinformationen angeben. In diesem Modus müssen die wiederherzustellenden Dienste nicht angegeben werden. Infrastructure Backup Controller fügt den Status der Steuerungsebene als Teil des Bereitstellungsworkflows ein.

Bei einem Notfall, nach dem die Hardware nicht mehr genutzt werden kann, ist die erneute Bereitstellung nur auf neuer Hardware möglich. Eine erneute Bereitstellung kann mehrere Wochen dauern, bis Ersatzhardware bestellt und an das Rechenzentrum geliefert wurde. Die Wiederherstellung von Daten auf Steuerungsebene kann jederzeit durchgeführt werden. Die Wiederherstellung wird jedoch nicht unterstützt, wenn die Version der erneut bereitgestellten Instanz mehr als eine Version höher ist als die bei der letzten Sicherung verwendete Version.

| Bereitstellungsmodus | Startpunkt | Endpunkt                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Neuinstallation   | Baselinebuild | OEM stellt Azure Stack bereit und wird auf die neueste unterstützte Version aktualisiert.                                                                                                                                          |
| Wiederherstellungsmodus   | Baselinebuild | OEM stellt Azure Stack im Wiederherstellungsmodus bereit und verarbeitet die Anforderungen zur Versionsübereinstimmung basierend auf der neuesten verfügbaren Sicherung. OEM schließt die Bereitstellung durch Aktualisierung auf die neueste unterstützte Version ab. |

## <a name="data-in-backups"></a>Daten in Sicherungen

Azure Stack unterstützt die Bereitstellung im sogenannten Cloudwiederherstellungsmodus. Dieser Modus wird nur verwendet, wenn Sie Azure Stack wiederherstellen möchten, nachdem die Lösung infolge eines Notfalls oder Produktfehlers nicht mehr wiederherstellbar war. Bei diesem Bereitstellungsmodus werden keine der in der Lösung gespeicherten Benutzerdaten wiederhergestellt. Der Umfang dieses Bereitstellungsmodus beschränkt sich auf die Wiederherstellung der folgenden Daten:

 - Bereitstellungseingaben
 - Daten des internen Identitätsdiensts (ADFS-Bereitstellungen).
 - Partneridentifizierungskonfiguration (ADFS-Bereitstellungen).
 - Von interner Zertifizierungsstelle verwendete Stammzertifikate.
 - Benutzerdaten der Azure Resource Manager-Konfiguration, z. B. Abonnements, Tarife, Angebote, Speicher- und Netzwerkkontingente sowie Computeressourcen.
 - Key Vault-Geheimnisse und Tresore.
 - RBAC-Richtlinienzuweisungen und Rollenzuweisungen

Während der Bereitstellung werden keine der IaaS-Ressourcen (Infrastructure-as-a-Service) oder PaaS-Ressourcen (Platform-as-a-Service) wiederhergestellt. Diese Verluste beinhalten IaaS-VMs, Speicherkonten, Blobs, Tabellen, Netzwerkkonfiguration usw. Die Cloudwiederherstellung soll sicherstellen, dass die Bediener und Benutzer sich nach Abschluss der Bereitstellung wieder im Portal anmelden können. Benutzer, die sich wieder anmelden, können keine ihrer Ressourcen anzeigen. Für die Benutzer werden Abonnements sowie die vom Administrator definierten ursprünglichen Pläne, Angebote und Richtlinien wiederhergestellt. Die erneute Anmeldung der Benutzer im System unterliegt den gleichen Einschränkungen, die für die ursprüngliche Lösung vor dem Notfall galten. Nach Abschluss der Cloudwiederherstellung kann der Bediener Erweiterungs- und Drittanbieter-RPs sowie zugehörige Werte wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

Erhalten Sie Informationen über die bewährten Methoden zur [Verwendung des Infrastructure Backup-Diensts](azure-stack-backup-best-practices.md).
