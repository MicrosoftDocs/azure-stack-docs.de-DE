---
title: 'App Service in Azure Stack: Versionshinweise zu Update 2 | Microsoft-Dokumentation'
description: Erfahren Sie mehr über Verbesserungen, Fehlerbehebungen und bekannte Probleme in Update 2 für App Service in Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: c85cc176949c9f3c86cded80be14417add6c40da
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808146"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service in Azure Stack: Versionshinweise zu Update 2

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen, Fehlerbehebungen und bekannte Probleme in Update 2 für Azure App Service in Azure Stack beschrieben. Die bekannten Probleme sind in drei Abschnitte unterteilt: Probleme in Bezug auf die Bereitstellung, Probleme mit dem Updatevorgang und Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1804 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.2 bereit.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 2 für App Service in Azure Stack lautet **72.0.13698.10**.

### <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Neue Bereitstellungen von Azure App Service in Azure Stack erfordern jetzt aufgrund einer verbesserten SSO-Verarbeitung für Kudu in Azure App Service ein [Platzhalterzertifikat mit drei Antragstellern](azure-stack-app-service-before-you-get-started.md#get-certificates). Der neue Antragsteller lautet: **\*.sso.appservice.\<Region\>.\<Domänenname\>.\<Erweiterung\>**

Lesen Sie vor Beginn der Bereitstellung die [Voraussetzungen für das Bereitstellen von App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 2 für Azure App Service in Azure Stack enthält die folgenden Verbesserungen und Fehlerbehebungen:

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Konsistent mit Azure Stack-Portal-SDK-Version.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.11612**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools**:
  - .NET Framework 4.7.1 wurde hinzugefügt.
  - Hinzugefügte **Node.JS**-Versionen:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Hinzugefügte **NPM**-Versionen:
    - 5.6.0
  - .NET Core-Komponenten wurden aktualisiert, um mit Azure App Service in einer öffentlichen Cloud konsistent zu sein.
  - Aktualisierte Kudu

- Feature für den automatischen Austausch von Bereitstellungsslots aktiviert: [Konfigurieren des automatischen Austauschs](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap).

- Feature zum Testen in Produktionsumgebungen aktiviert: [Einführung in Tests in einer Produktionsumgebung](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

- Azure-Funktionsproxys aktiviert: [Verwenden von Azure-Funktionsproxys](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

- UX-Unterstützung der App Service-Administratorerweiterung hinzugefügt für:
  - Rotation für Geheimnisse
  - Rotation für Zertifikate
  - Rotation für Systemanmeldeinformationen
  - Rotation für Verbindungszeichenfolgen

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn der App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist.

Wenn Sie sich für die Bereitstellung in einem vorhandenen virtuellen Netzwerk und zur Verwendung einer internen IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

* Quelle: Any
* Quellportbereich: *
* Ziel: IP-Adressen
* IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
* Zielportbereich: 445
* Protokoll: TCP
* Aktion: ZULASSEN
* Priorität: 700
* Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

Lesen Sie die Dokumentation in den [Versionshinweisen zum Azure Stack-Update 1804](azure-stack-update-1903.md).

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
