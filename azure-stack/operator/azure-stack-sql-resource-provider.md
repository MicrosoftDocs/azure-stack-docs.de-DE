---
title: Verwenden von SQL-Datenbanken
titleSuffix: Azure Stack
description: Hier erfahren Sie, wie Sie den SQL Server-Ressourcenanbieter verwenden, um SQL-Datenbanken als Dienst in Azure Stack anzubieten.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 9e7800c018816155205569397520271fb86c4c12
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492765"
---
# <a name="use-sql-databases-on-azure-stack"></a>Verwenden von SQL-Datenbanken in Azure Stack

Verwenden Sie den SQL-Ressourcenanbieter, um SQL-Datenbanken als Dienst in [Azure Stack](azure-stack-overview.md) anzubieten. Nachdem Sie den Ressourcenanbieter installiert und mit mindestens einer SQL Server-Instanz verbunden haben, können Sie und Ihre Benutzer damit Folgendes erstellen:

- Datenbanken für native Cloud-Apps.
- Websites, die SQL verwenden.
- Workloads, die SQL verwenden.

Vor der Installation des SQL-Ressourcenanbieters sind einige Einschränkungen zu beachten:

- Benutzer können nur einzelne Datenbanken erstellen und verwalten. Endbenutzer können nicht auf die Datenbankserverinstanz zugreifen. Dies schränkt unter Umständen die Kompatibilität mit lokalen Datenbankanwendungen ein, die Zugriff auf den Master oder auf die temporäre Datenbank oder für die dynamische Verwaltung von Datenbanken benötigen.
- Ihr Azure Stack-Operator ist für die Bereitstellung, Aktualisierung, Absicherung, Konfiguration und Wartung der SQL-Datenbankserver und Hosts verantwortlich. Der Ressourcenanbieterdienst bietet keine Funktionen für die Verwaltung von Host- und Datenbankserverinstanzen. 
- Datenbanken unterschiedlicher Benutzer in verschiedenen Abonnements können sich in der gleichen Datenbankserverinstanz befinden. Der Ressourcenanbieter bietet keinen Mechanismus zur Isolierung von Datenbanken auf verschiedenen Hosts oder in verschiedenen Datenbankserverinstanzen.
- Der Ressourcenanbieter bietet keine Berichte zur Mandantennutzung von Datenbanken.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektur des SQL-Ressourcenanbieteradapters

Der Ressourcenanbieter besteht aus den folgenden Komponenten:

- **Dem virtuellen Computer des SQL-Ressourcenanbieteradapters**, einem virtuellen Windows Server-Computer, der die Anbieterdienste ausführt.
- **Dem Ressourcenanbieter selbst**, der Anforderungen der und Zugriffe auf Datenbankressourcen verarbeitet.
- **Servern, die SQL Server hosten**, die Kapazität für Datenbanken bereitstellen und als „Hostserver“ bezeichnet werden.

Sie müssen mindestens eine SQL Server-Instanz erstellen bzw. den Zugriff auf externe SQL Server-Instanzen bereitstellen.

> [!NOTE]
> Hostserver, die auf integrierten Azure Stack-Systemen installiert werden, müssen mit einem Mandantenabonnement erstellt werden. Sie können nicht mit dem Standardabonnement des Anbieters erstellt werden. Sie müssen im Mandantenportal oder mit PowerShell mit einer geeigneten Anmeldung erstellt werden. Alle Hostserver sind abrechenbare VMs, die Lizenzen benötigen. Der Dienstadministrator kann Besitzer des Mandantenabonnements sein.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des SQL Server-Ressourcenanbieters](azure-stack-sql-resource-provider-deploy.md)
