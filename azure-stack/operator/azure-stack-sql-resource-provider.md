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
ms.openlocfilehash: d2ce6c0af2912a2658db80301c9a64c8e3d5c066
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780727"
---
# <a name="use-sql-databases-on-azure-stack"></a>Verwenden von SQL-Datenbanken in Azure Stack

Verwenden Sie den SQL Server-Ressourcenanbieter, um SQL-Datenbanken als Dienst in [Azure Stack](azure-stack-overview.md) anzubieten. Nachdem Sie den Ressourcenanbieter installiert und mit mindestens einer SQL Server-Instanz verbunden haben, können Sie und Ihre Benutzer damit Folgendes erstellen:

- Datenbanken für native Cloud-Apps.
- Websites, die SQL verwenden.
- Workloads, die SQL verwenden.

Der Ressourcenanbieter bietet nicht alle Datenbankverwaltungsfunktionen von [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/). Zum Beispiel werden Pools für elastische Datenbanken, die automatisch Ressourcen zuordnen, nicht unterstützt. Der Ressourcenanbieter unterstützt jedoch ähnliche Vorgänge für das Erstellen, Lesen, Aktualisieren und Löschen in einer SQL Server-Datenbank.

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
