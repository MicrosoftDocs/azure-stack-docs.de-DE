---
title: Entfernen des SQL-Ressourcenanbieters
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie den SQL-Ressourcenanbieter aus Ihrer Azure Stack Hub-Bereitstellung entfernen.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2019
ms.openlocfilehash: 67c6331111e18ec04f3084b4c34a971120733569
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839979"
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

> [!IMPORTANT]
> Es wird dringend empfohlen, **Clear-AzureRmContext -Scope CurrentUser** und **Clear-AzureRmContext -Scope Process** zu verwenden, um den Cache vor dem Ausführen des Skripts zu löschen.


4. Führen Sie das „DeploySqlProvider.ps1“-Skript mit den folgenden Parametern aus:

    * **Uninstall**: Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen.
    * **PrivilegedEndpoint**: Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts.
    * **AzureEnvironment**: Die Azure-Umgebung, die für die Bereitstellung von Azure Stack Hub verwendet wird. Nur für Azure AD-Bereitstellungen erforderlich.
    * **CloudAdminCredential**: Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind.
    * **AzCredential**: Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub. Beim Skript tritt ein Fehler auf, wenn für das Konto, das Sie mit AzCredential verwenden, mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

[Anbieten von App Services als PaaS](azure-stack-app-service-overview.md)
