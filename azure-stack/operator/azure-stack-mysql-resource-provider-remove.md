---
title: Entfernen des MySQL-Ressourcenanbieters in Azure Stack Hub
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieter aus Ihrer Azure Stack Hub-Bereitstellung entfernen können.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2
ms.openlocfilehash: 7c3c33371e50df0dabb7db9fc8c0204fc3caaa83
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011143"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Entfernen des MySQL-Ressourcenanbieters in Azure Stack Hub

Bevor Sie den MySQL-Ressourcenanbieter entfernen, müssen Sie alle Anbieterabhängigkeiten entfernen. Außerdem benötigen Sie eine Kopie des Bereitstellungspakets, mit dem der Ressourcenanbieter installiert wurde.

> [!NOTE]
> Die Downloadlinks für die Ressourcenanbieter-Installationsprogramme finden Sie in den [Voraussetzungen für die Bereitstellung des Ressourcenanbieters](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Wenn Sie den MySQL-Ressourcenanbieter entfernen, werden die vom Operator verwalteten zugeordneten Pläne und Kontingente gelöscht. Aber es werden keine Mandantendatenbanken von Hostservern gelöscht.

## <a name="to-remove-the-mysql-resource-provider"></a>So entfernen Sie den MySQL-Ressourcenanbieter

1. Vergewissern Sie sich, dass Sie alle vorhandenen Abhängigkeiten des MySQL-Ressourcenanbieters entfernt haben.

   > [!NOTE]
   > Die Deinstallation des MySQL-Ressourcenanbieters wird fortgesetzt, auch wenn abhängige Ressourcen den Ressourcenanbieter derzeit verwenden.
  
2. Rufen Sie eine Kopie des Installationspakets für den MySQL-Ressourcenanbieter ab, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren.
3. Öffnen Sie ein neues PowerShell-Konsolenfenster mit erhöhten Rechten, und wechseln Sie zu dem Verzeichnis, in dem Sie die Installationsdateien des MySQL-Ressourcenanbieters extrahiert haben.
4. Führen Sie das Skript „DeployMySqlProvider.ps1“ mit den folgenden Parametern aus:
    - **Uninstall**: Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen.
    - **PrivilegedEndpoint**: Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts.
    - **AzureEnvironment**: Die Azure-Umgebung, die für die Bereitstellung von Azure Stack Hub verwendet wird. Nur für Azure AD-Bereitstellungen erforderlich.
    - **CloudAdminCredential**: Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind.
    - **AzCredential**: Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub. Beim Skript tritt ein Fehler auf, wenn für das Konto, das Sie mit AzCredential verwenden, mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

[Anbieten von App Services als PaaS](azure-stack-app-service-overview.md)
