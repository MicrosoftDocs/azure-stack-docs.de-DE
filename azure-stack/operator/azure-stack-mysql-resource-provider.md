---
title: Verwenden von MySQL-Datenbanken als PaaS in Azure Stack Hub
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieter bereitstellen und MySQL-Databases as a Service in Azure Stack Hub einrichten.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 73c916a193492662cdbb3ba20031c58932721053
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698588"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack-hub"></a>Verwenden von MySQL-Datenbanken in Microsoft Azure Stack Hub

Verwenden Sie den MySQL-Ressourcenanbieter, um MySQL-Datenbanken in [Azure Stack Hub](azure-stack-overview.md) anzubieten. Nachdem Sie den Ressourcenanbieter bereitgestellt und mit mindestens einer MySQL-Serverinstanz verbunden haben, können Sie Folgendes erstellen:

* MySQL-Datenbanken für native Cloud-Apps
* MySQL-Datenbanken für Webanwendungen  

Vor der Installation des MySQL-Ressourcenanbieters sind einige Einschränkungen zu beachten:

- Benutzer können nur einzelne Datenbanken erstellen und verwalten. Endbenutzer können nicht auf die Datenbankserverinstanz zugreifen. Dies schränkt unter Umständen die Kompatibilität mit lokalen Datenbankanwendungen ein, die Zugriff auf den Master oder auf die temporäre Datenbank oder für die dynamische Verwaltung von Datenbanken benötigen.
- Ihr Azure Stack Hub-Operator ist für die Bereitstellung, Aktualisierung, Absicherung, Konfiguration und Wartung der MySQL-Datenbankserver und Hosts verantwortlich. Der Ressourcenanbieterdienst bietet keine Funktionen für die Verwaltung von Host- und Datenbankserverinstanzen. 
- Datenbanken unterschiedlicher Benutzer in verschiedenen Abonnements können sich in der gleichen Datenbankserverinstanz befinden. Der Ressourcenanbieter bietet keinen Mechanismus zur Isolierung von Datenbanken auf verschiedenen Hosts oder in verschiedenen Datenbankserverinstanzen.
- Der Ressourcenanbieter bietet keine Berichte zur Mandantennutzung von Datenbanken.

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektur des MySQL-Ressourcenanbieteradapters

Der Ressourcenanbieter verfügt über folgenden Komponenten:

* **Den virtuellen Computer des MySQL-Ressourcenanbieteradapters**, einem virtuellen Windows Server-Computer, der die Anbieterdienste ausführt.
* **Dem Ressourcenanbieter selbst**, der Anforderungen der und Zugriffe auf Datenbankressourcen verarbeitet.
* **Servern, die MySQL Server hosten**, die Kapazität für Datenbanken bereitstellen und als „Hostserver“ bezeichnet werden. Sie können MySQL-Instanzen selbst erstellen oder Zugriff auf externe MySQL-Instanzen gewähren. Im [Azure Stack Hub-Schnellstartkatalog](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) finden Sie eine Beispielvorlage mit folgenden Funktionen:

  * Erstellen eines MySQL-Servers
  * Herunterladen und Bereitstellen eines MySQL-Servers aus dem Azure Marketplace

> [!NOTE]
> Hostserver, die auf integrierten Azure Stack Hub-Systemen installiert werden, müssen mit einem Mandantenabonnement erstellt werden. Sie können nicht mit dem Standardabonnement des Anbieters erstellt werden. Sie müssen über das Benutzerportal oder eine PowerShell-Sitzung mit entsprechenden Anmeldedaten erstellt werden. Alle Hostserver sind abrechenbare VMs, die Lizenzen benötigen. Der Dienstadministrator kann Besitzer des Mandantenabonnements sein.

### <a name="required-privileges"></a>Erforderliche Berechtigungen

Das Systemkonto muss über die folgenden Berechtigungen verfügen:

* **Datenbank:** erstellen, löschen
* **Anmeldung:** erstellen, festlegen, löschen, gewähren, widerrufen  

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des MySQL-Ressourcenanbieters](azure-stack-mysql-resource-provider-deploy.md)
