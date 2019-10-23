---
title: App Service in Azure Stack – Versionshinweise zu Update 7 | Microsoft-Dokumentation
description: Erfahren Sie, was in Update 7 für App Service in Azure Stack enthalten ist, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 77fb9772f027d25b0d8c0d8355e3a868c2142d6c
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282601"
---
# <a name="app-service-on-azure-stack-update-7-release-notes"></a>App Service in Azure Stack: Versionshinweise zu Update 7

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und Fehlerbehebungen in Update 7 für Azure App Service in Azure Stack sowie bekannte Probleme beschrieben. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf die Bereitstellung und den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie das Update 1907 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit bereit, bevor Sie Azure App Service 1.7 bereitstellen.


## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 7 für App Service in Azure Stack lautet **84.0.2.10**.

### <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Dokumentation [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md), bevor Sie mit der Bereitstellung beginnen.

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack auf 1.7 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack-Verwaltungsportal bereit sind.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

- Syndizieren Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.3**) über den Marketplace.

### <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 7 für Azure App Service in Azure Stack enthält folgende Verbesserungen und Fehlerbehebungen:

- Lösung für [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) (Sicherheitsanfälligkeit bezüglich Remotecodeausführung)

- Updates für **App Service-Mandanten, Admin, Functions-Portale und Kudu-Tools**. Mit Azure Stack-Portal-SDK-Version konsistent.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.12582**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende Anwendungsframeworks und Tools**:
  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3.6
  - Kudu auf 82.10503.3890 aktualisiert

- **Updates des zugrunde liegenden Betriebssystems aller Rollen**:
  - [Samstag, 17. August 2019 – KB4512495 (BS-Build 14393.3181)](https://support.microsoft.com/help/4512495)

- **Zugriffseinschränkungen sind im Benutzerportal jetzt aktiviert**:
  - Ab diesem Release können Benutzer Zugriffseinschränkungen für ihre Web-, API- und Functions-Anwendungen gemäß der veröffentlichten Dokumentation konfigurieren: [Azure App Service – Zugriffseinschränkungen](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions). **HINWEIS**: Azure App Service unter Azure Stack unterstützt keine Dienstendpunkte.

- **Funktionalität der Bereitstellungsoptionen (klassisch) wiederhergestellt**:
  - Benutzer können wieder die Bereitstellungsoptionen (klassisch) verwenden, um die Bereitstellung ihrer Apps über GitHub, Bitbucket, Dropbox, OneDrive und lokale und externe Repositorys zu konfigurieren und die Bereitstellungsanmeldeinformationen für ihre Anwendungen festzulegen.

- Richtige Konfiguration der **Azure Functions-Überwachung**.

- **Windows Update-Verhalten**: Anhand des Feedbacks von Kunden haben wir ab Update 7 geändert, wie Windows Update für App Service-Rollen konfiguriert ist:
  - Es gibt drei Modi:
    - **Deaktiviert**: Der Windows Update-Dienst ist deaktiviert. Windows wird mit der KB aktualisiert, die im Lieferumfang von Azure App Service für Azure Stack-Releases enthalten ist.
    - **Automatisch**: Der Windows Update-Dienst ist aktualisiert, und Windows Update bestimmt, wie und wann Updates durchgeführt werden.
    - **Verwaltet**: Der Windows Update-Dienst ist deaktiviert. Azure App Service führt während des OnStart-Vorgangs der individuellen Rolle einen Windows Update-Zyklus durch.

  **Neue** Bereitstellungen: Der Windows Update-Dienst ist standardmäßig deaktiviert.

  **Vorhandene** Bereitstellungen: Wenn Sie die Einstellung im Controller geändert haben, ändert sich der Wert jetzt von **Falsch** in **Deaktiviert**, und der vorherige Wert **Wahr** wird zu **Automatisch**.

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie [die Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und die Datenbanken synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

### <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dies ist in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack dargestellt.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Verwaltungsportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:
 * Quelle: Any
 * Quellportbereich: *
 * Ziel: IP-Adressen
 * IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
 * Zielportbereich: 445
 * Protokoll: TCP
 * Aktion: Allow
 * Priorität: 700
 * Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack betreiben

Sehen Sie sich die Dokumentation in den [Versionshinweisen zum Azure Stack-Update 1907](azure-stack-release-notes-1907.md) an.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über App Service in Azure Stack](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack finden Sie unter [Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md).
