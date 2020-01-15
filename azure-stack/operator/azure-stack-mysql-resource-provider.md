---
title: Verwenden von MySQL-Datenbanken als PaaS in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieter bereitstellen und MySQL-Databases as a Service in Azure Stack einrichten.
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
ms.openlocfilehash: ceda705c4a06ac9465c3f017a87986ba6e20e4b3
ms.sourcegitcommit: e57a53caac50d1f8762307e065fe886fcda7eadf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2019
ms.locfileid: "75190861"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Verwenden von MySQL-Datenbanken in Microsoft Azure Stack

Verwenden Sie den MySQL-Ressourcenanbieter, um den MySQL-Datenbankdienst in [Azure Stack](azure-stack-overview.md) anzubieten. Nachdem Sie den Ressourcenanbieter bereitgestellt und mit mindestens einer MySQL-Serverinstanz verbunden haben, haben Sie folgende Möglichkeiten:

* Erstellen von MySQL-Datenbanken mithilfe von Azure Resource Manager-Bereitstellungsvorlagen
* Bereitstellen von MySQL-Datenbanken als Dienst  

Vor der Installation des MySQL-Ressourcenanbieters sind einige Einschränkungen zu beachten:

- Benutzer können nur einzelne Datenbanken erstellen und verwalten. Endbenutzer können nicht auf die Datenbankserverinstanz zugreifen. Dies schränkt unter Umständen die Kompatibilität mit lokalen Datenbankanwendungen ein, die Zugriff auf den Master oder auf die temporäre Datenbank oder für die dynamische Verwaltung von Datenbanken benötigen.
- Ihr Azure Stack-Operator ist für die Bereitstellung, Aktualisierung, Absicherung, Konfiguration und Wartung der MySQL-Datenbankserver und Hosts verantwortlich. Der Ressourcenanbieterdienst bietet keine Funktionen für die Verwaltung von Host- und Datenbankserverinstanzen. 
- Datenbanken unterschiedlicher Benutzer in verschiedenen Abonnements können sich in der gleichen Datenbankserverinstanz befinden. Der Ressourcenanbieter bietet keinen Mechanismus zur Isolierung von Datenbanken auf verschiedenen Hosts oder in verschiedenen Datenbankserverinstanzen.
- Der Ressourcenanbieter bietet keine Berichte zur Mandantennutzung von Datenbanken.

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektur des MySQL-Ressourcenanbieteradapters

Der Ressourcenanbieter verfügt über folgenden Komponenten:

* **Den virtuellen Computer des MySQL-Ressourcenanbieteradapters**, einem virtuellen Windows Server-Computer, der die Anbieterdienste ausführt.
* **Dem Ressourcenanbieter selbst**, der Anforderungen der und Zugriffe auf Datenbankressourcen verarbeitet.
* **Servern, die MySQL Server hosten**, die Kapazität für Datenbanken bereitstellen und als „Hostserver“ bezeichnet werden. Sie können MySQL-Instanzen selbst erstellen oder Zugriff auf externe MySQL-Instanzen gewähren. Im [Azure Stack-Schnellstartkatalog](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) finden Sie eine Beispielvorlage mit folgenden Funktionen:

  * Erstellen eines MySQL-Servers
  * Herunterladen und Bereitstellen eines MySQL-Servers aus dem Azure Marketplace

> [!NOTE]
> Hostserver, die auf integrierten Azure Stack-Systemen installiert werden, müssen mit einem Mandantenabonnement erstellt werden. Sie können nicht mit dem Standardabonnement des Anbieters erstellt werden. Sie müssen im Mandantenportal oder einer PowerShell-Sitzung mit einer geeigneten Anmeldung erstellt werden. Alle Hostserver sind abrechenbare VMs, die Lizenzen benötigen. Der Dienstadministrator kann Besitzer des Mandantenabonnements sein.

### <a name="required-privileges"></a>Erforderliche Berechtigungen

Das Systemkonto muss über die folgenden Berechtigungen verfügen:

* **Datenbank:** erstellen, löschen
* **Anmeldung:** erstellen, festlegen, löschen, gewähren, widerrufen  

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des MySQL-Ressourcenanbieters](azure-stack-mysql-resource-provider-deploy.md)
