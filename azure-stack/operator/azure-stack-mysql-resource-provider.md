---
title: Verwenden von MySQL-Datenbanken als PaaS in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieter bereitstellen und MySQL-Datenbanken als Dienst in Azure Stack einrichten.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: e610f946868940f9fde66932bedec1dc998f390b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307098"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Verwenden von MySQL-Datenbanken in Microsoft Azure Stack

MySQL-Datenbanken werden häufig mit Websites verwendet und unterstützen zahlreiche Websiteplattformen. Beispielsweise können Sie WordPress-Websites mit dem App Services-Ressourcenanbieter (PaaS)-Add-On (Plattform als Dienst) erstellen.

Nachdem Sie den Ressourcenanbieter bereitgestellt haben, können Sie folgende Schritte ausführen:

* Erstellen von MySQL-Servern und -Datenbanken mithilfe von Azure Resource Manager-Bereitstellungsvorlagen
* Bereitstellen von MySQL-Datenbanken als Dienst  

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
