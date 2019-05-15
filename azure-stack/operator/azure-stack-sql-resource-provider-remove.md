---
title: Entfernen des SQL-Ressourcenanbieters in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den SQL-Ressourcenanbieter aus Ihrer Azure Stack-Bereitstellung entfernen.
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
ms.date: 11/20/2018
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: d7b512315eb1631d37ed51a2dcb83015fad197be
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618406"
---
# <a name="remove-the-sql-resource-provider"></a>Entfernen des SQL-Ressourcenanbieters

Bevor Sie den SQL-Ressourcenanbieter entfernen, müssen Sie alle Anbieterabhängigkeiten entfernen. Außerdem benötigen Sie eine Kopie des Bereitstellungspakets, mit dem der Ressourcenanbieter installiert wurde.

> [!NOTE]
> Die Downloadlinks für die Ressourcenanbieter-Installationsprogramme finden Sie in den [Voraussetzungen für die Bereitstellung des Ressourcenanbieters](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Wenn Sie den SQL-Ressourcenanbieter löschen, werden die Mandantendatenbanken dadurch nicht von den Hostingservern gelöscht.

## <a name="dependency-cleanup"></a>Bereinigung von Abhängigkeiten

Es müssen verschiedene Bereinigungsaufgaben durchgeführt werden, bevor Sie das Skript „DeploySqlProvider.ps1“ zum Entfernen des Ressourcenanbieters ausführen können.

Der Azure Stack-Bediener ist für die folgenden Bereinigungsaufgaben verantwortlich:

* Löschen aller Pläne, die auf den SQL-Adapter verweisen
* Löschen aller Kontingente, die dem SQL-Adapter zugeordnet sind

## <a name="to-remove-the-sql-resource-provider"></a>So entfernen Sie den SQL-Ressourcenanbieter

1. Vergewissern Sie sich, dass Sie alle vorhandenen Abhängigkeiten des SQL-Ressourcenanbieters entfernt haben.

   > [!NOTE]
   > Die Deinstallation des SQL-Ressourcenanbieters wird fortgesetzt, auch wenn abhängige Ressourcen den Ressourcenanbieter derzeit verwenden.
  
2. Rufen Sie eine Kopie des Installationspakets für den SQL-Ressourcenanbieter ab, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren.

3. Öffnen Sie ein neues PowerShell-Konsolenfenster mit erhöhten Rechten, und wechseln Sie zu dem Verzeichnis, in dem Sie die Installationsdateien des SQL-Ressourcenanbieters extrahiert haben.

4. Führen Sie das „DeploySqlProvider.ps1“-Skript mit den folgenden Parametern aus:

    * **Deinstallieren**. Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen.
    * **PrivilegedEndpoint**. Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts.
    * **AzureEnvironment**. Die Azure-Umgebung, die für die Bereitstellung von Azure Stack verwendet wird. Nur für Azure AD-Bereitstellungen erforderlich.
    * **CloudAdminCredential**. Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind.
    * **AzCredential**. Die Anmeldeinformationen für das Azure Stack-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack.

## <a name="next-steps"></a>Nächste Schritte

[Anbieten von App Services als PaaS](azure-stack-app-service-overview.md)
