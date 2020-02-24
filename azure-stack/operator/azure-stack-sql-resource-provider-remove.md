---
title: Entfernen des SQL-Ressourcenanbieters
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie den SQL-Ressourcenanbieter aus Ihrer Azure Stack Hub-Bereitstellung entfernen.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: f8b9ed17aa19a2d0ed9403ace3876f4c8f3a25bf
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491780"
---
# <a name="remove-the-sql-resource-provider"></a>Entfernen des SQL-Ressourcenanbieters

Bevor Sie den SQL-Ressourcenanbieter entfernen, müssen Sie alle Anbieterabhängigkeiten entfernen. Außerdem benötigen Sie eine Kopie des Bereitstellungspakets, mit dem der Ressourcenanbieter installiert wurde.

> [!NOTE]
> Die Downloadlinks für die Ressourcenanbieter-Installationsprogramme finden Sie in den [Voraussetzungen für die Bereitstellung des Ressourcenanbieters](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Wenn Sie den SQL-Ressourcenanbieter entfernen, werden die vom Operator verwalteten zugeordneten Pläne und Kontingente gelöscht. Aber es werden keine Mandantendatenbanken von Hostservern gelöscht.

## <a name="to-remove-the-sql-resource-provider"></a>So entfernen Sie den SQL-Ressourcenanbieter

1. Vergewissern Sie sich, dass Sie alle vorhandenen Abhängigkeiten des SQL-Ressourcenanbieters entfernt haben.

   > [!NOTE]
   > Die Deinstallation des SQL-Ressourcenanbieters wird fortgesetzt, auch wenn abhängige Ressourcen den Ressourcenanbieter derzeit verwenden.
  
2. Rufen Sie eine Kopie des Installationspakets für den SQL-Ressourcenanbieter ab, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren.

3. Öffnen Sie ein neues PowerShell-Konsolenfenster mit erhöhten Rechten, und wechseln Sie zu dem Verzeichnis, in dem Sie die Installationsdateien des SQL-Ressourcenanbieters extrahiert haben.

4. Führen Sie das „DeploySqlProvider.ps1“-Skript mit den folgenden Parametern aus:

    * **Uninstall**: Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen.
    * **PrivilegedEndpoint**: Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts.
    * **AzureEnvironment**: Die Azure-Umgebung, die für die Bereitstellung von Azure Stack Hub verwendet wird. Nur für Azure AD-Bereitstellungen erforderlich.
    * **CloudAdminCredential**: Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind.
    * **AzCredential**: Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub.

## <a name="next-steps"></a>Nächste Schritte

[Anbieten von App Services als PaaS](azure-stack-app-service-overview.md)
