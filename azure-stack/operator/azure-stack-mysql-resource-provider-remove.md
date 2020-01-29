---
title: Entfernen des MySQL-Ressourcenanbieters in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieter aus Ihrer Azure Stack Hub-Bereitstellung entfernen können.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 0f616bced206bf32da0ace8c1f3cb836c66962e6
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534735"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Entfernen des MySQL-Ressourcenanbieters in Azure Stack Hub

Bevor Sie den MySQL-Ressourcenanbieter entfernen, müssen Sie alle Anbieterabhängigkeiten entfernen. Außerdem benötigen Sie eine Kopie des Bereitstellungspakets, mit dem der Ressourcenanbieter installiert wurde.

> [!NOTE]
> Die Downloadlinks für die Ressourcenanbieter-Installationsprogramme finden Sie in den [Voraussetzungen für die Bereitstellung des Ressourcenanbieters](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Wenn Sie den MySQL-Ressourcenanbieter löschen, werden die Mandantendatenbanken dadurch nicht von den Hostingservern gelöscht.

## <a name="dependency-cleanup"></a>Bereinigung von Abhängigkeiten

Es müssen verschiedene Bereinigungsaufgaben durchgeführt werden, bevor Sie das Skript „DeployMySqlProvider.ps1 script“ zum Entfernen des Ressourcenanbieters ausführen können.

Der Azure Stack Hub-Operator ist für die folgenden Bereinigungsaufgaben zuständig:

* Löschen aller Pläne, die auf den MySQL-Adapter verweisen
* Löschen aller Kontingente, die dem MySQL-Adapter zugeordnet sind

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
    - **DirectoryTenantID**
    - **AzCredential**: Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub.

## <a name="next-steps"></a>Nächste Schritte

[Anbieten von App Services als PaaS](azure-stack-app-service-overview.md)
