---
title: Verwenden von SQL-Datenbanken
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie den SQL Server-Ressourcenanbieter verwenden, um SQL-Datenbanken als Dienst in Azure Stack Hub anzubieten.
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: c0f599d8a63f0a1ea16e5a39303391cb1b0790a0
ms.sourcegitcommit: 8079220917523ab9ddb824e4bba3e9b091f38a9f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661587"
---
# <a name="use-sql-databases-on-azure-stack-hub"></a>Verwenden von SQL-Datenbanken in Azure Stack Hub

Verwenden Sie den SQL-Ressourcenanbieter, um SQL-Datenbanken in [Azure Stack Hub](azure-stack-overview.md) anzubieten. Nachdem Sie den Ressourcenanbieter installiert und mit mindestens einer SQL Server-Instanz verbunden haben, können Sie und Ihre Benutzer damit Folgendes erstellen:

- SQL-Datenbanken für native Cloud-Apps
- SQL-Datenbanken für Webanwendungen

Beachten Sie vor der Installation des SQL-Ressourcenanbieters die folgenden Einschränkungen:

- Benutzer können nur einzelne Datenbanken erstellen und verwalten. Endbenutzer können nicht auf die Datenbankserverinstanz zugreifen. Dies schränkt unter Umständen die Kompatibilität mit lokalen Datenbank-Apps ein, die Zugriff auf den Master oder auf die temporäre Datenbank oder für die dynamische Verwaltung von Datenbanken benötigen.
- Ihr Azure Stack Hub-Operator ist für die Bereitstellung, Aktualisierung, Sicherung, Konfiguration und Wartung der SQL-Datenbankserver und -hosts zuständig. Der Ressourcenanbieterdienst bietet keine Funktionen für die Verwaltung von Host- und Datenbankserverinstanzen.
- Datenbanken unterschiedlicher Benutzer in verschiedenen Abonnements können sich in der gleichen Datenbankserverinstanz befinden. Der Ressourcenanbieter bietet keinen Mechanismus zur Isolierung von Datenbanken auf verschiedenen Hosts oder in verschiedenen Datenbankserverinstanzen.
- Der Ressourcenanbieter bietet keine Berichte zur Mandantennutzung von Datenbanken.
- Sie können nur einen SQL-Hostserver in ein anderes Abonnement in Azure weltweit verschieben. Azure Stack Hub unterstützt nicht das Verschieben eines SQL-Hostservers in ein anderes Abonnement.

Für herkömmliche lokale SQL Server-Workloads wird ein virtueller SQL Server-Computer in Azure Stack Hub empfohlen.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektur des SQL-Ressourcenanbieteradapters

Der Ressourcenanbieter besteht aus den folgenden Komponenten:

- **Dem virtuellen Computer des SQL-Ressourcenanbieteradapters**, einem virtuellen Windows Server-Computer, der die Anbieterdienste ausführt.
- **Dem Ressourcenanbieter selbst**, der Anforderungen der und Zugriffe auf Datenbankressourcen verarbeitet.
- **Servern, die SQL Server hosten**, die Kapazität für Datenbanken bereitstellen und als „Hostserver“ bezeichnet werden.

Sie müssen mindestens eine SQL Server-Instanz erstellen bzw. den Zugriff auf externe SQL Server-Instanzen bereitstellen.

> [!NOTE]
> Hostserver, die auf integrierten Azure Stack Hub-Systemen installiert werden, müssen mit einem Mandantenabonnement erstellt werden. Sie können nicht mit dem Standardabonnement des Anbieters erstellt werden. Sie müssen im Benutzerportal oder über PowerShell mit geeigneten Anmeldedaten erstellt werden. Alle Hostserver sind abrechenbare VMs, die Lizenzen benötigen. Der Dienstadministrator kann Besitzer des Mandantenabonnements sein.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des SQL Server-Ressourcenanbieters](azure-stack-sql-resource-provider-deploy.md)
