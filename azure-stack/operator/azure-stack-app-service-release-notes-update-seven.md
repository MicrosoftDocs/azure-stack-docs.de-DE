---
title: 'App Service in Azure Stack Hub: Versionshinweise zu Update 7'
description: Versionshinweise zu Update 7 für App Service in Azure Stack Hub, z. B. zu neuen Features, Fehlerbehebungen und bekannten Problemen.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 779bbce3a3dbcf3b19b8bb142b10729f85d960b2
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974216"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>App Service in Azure Stack Hub: Versionshinweise zu Update 7

In diesen Versionshinweisen werden die neuen Features, Fehlerbehebungen und bekannten Probleme in Update 7 für Azure App Service in Azure Stack Hub beschrieben. Die bekannten Probleme sind in zwei Abschnitte unterteilt: Probleme, die sich direkt auf den Upgradeprozess beziehen, und Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Wenden Sie Update 1910 auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) vor der Bereitstellung von Azure App Service 1.7 bereit.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer von Update 7 für App Service in Azure Stack Hub lautet **84.0.2.10**.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie vor Beginn der Bereitstellung die Informationen zu den [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

Bevor Sie mit dem Upgrade von Azure App Service in Azure Stack Hub auf 1.7 beginnen:

- Stellen Sie sicher, dass alle Rollen in der Azure App Service-Verwaltung im Azure Stack Hub-Administratorportal bereit sind.

- Sichern Sie die App Service-Geheimnisse mithilfe der App Service-Verwaltung im Azure Stack Hub-Verwaltungsportal.

- Sichern Sie App Service- und Masterdatenbanken:
  - AppService_Hosting
  - AppService_Metering
  - master

- Sichern Sie die Inhaltsdateifreigabe der Mandanten-App.

  > [!Important]
  > Cloudoperatoren sind für die Verwaltung und den Betrieb des Dateiservers und von SQL Server verantwortlich.  Der Ressourcenanbieter verwaltet diese Ressourcen nicht.  Der Cloudoperator ist für das Sichern der App Service-Datenbanken und der Mandanten-Inhaltsdateifreigabe verantwortlich.

- Beziehen Sie die **benutzerdefinierte Skripterweiterung** (Version **1.9.3**) über den Azure Stack Hub Marketplace.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Update 7 für Azure App Service in Azure Stack Hub enthält folgende Verbesserungen und Fehlerbehebungen:

- Lösung für [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) (Sicherheitsanfälligkeit bezüglich Remotecodeausführung).

- Updates für **App Service-Mandanten, Administrator, Functions-Portale und Kudu-Tools**. Konsistent mit Azure Stack Hub-Portal-SDK-Version.

- Aktualisiert **Azure Functions Runtime** auf **v1.0.12582**.

- Updates für den Kerndienst zur Verbesserung der Zuverlässigkeit und der Fehlermeldungen, die eine einfachere Diagnose von häufigen Problemen ermöglichen.

- **Updates für folgende App-Frameworks und Tools**:

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
  - Benutzer können jetzt Zugriffseinschränkungen für ihre Web-, API- und Functions-Apps gemäß der veröffentlichten Dokumentation konfigurieren: [Azure App Service-Zugriffseinschränkungen](/azure/app-service/app-service-ip-restrictions).
  
  > [!NOTE]
  > Azure App Service unter Azure Stack Hub unterstützt keine Dienstendpunkte.

- **Funktionalität der Bereitstellungsoptionen (klassisch) wiederhergestellt**:
  - Benutzer können wieder die Bereitstellungsoptionen (klassisch) verwenden, um die Bereitstellung ihrer Apps über GitHub, Bitbucket, Dropbox, OneDrive und lokale und externe Repositorys zu konfigurieren und die Bereitstellungsanmeldeinformationen für ihre Apps festzulegen.

- Richtige Konfiguration der **Azure Functions-Überwachung**.

- **Windows Update-Verhalten**: Anhand des Feedbacks von Kunden haben wir ab Update 7 die Konfiguration von Windows Update für App Service-Rollen geändert:
  - Es gibt drei Modi:
    - **Deaktiviert**: Der Windows Update-Dienst ist deaktiviert. Windows wird mit der KB aktualisiert, die im Lieferumfang von Azure App Service für Azure Stack Hub-Releases enthalten ist.
    - **Automatisch**: Der Windows Update-Dienst ist aktiviert, und Windows Update bestimmt, wie und wann Updates durchgeführt werden.
    - **Verwaltet**: Der Windows Update-Dienst ist deaktiviert. Azure App Service führt während des OnStart-Vorgangs der individuellen Rolle einen Windows Update-Zyklus durch.

  **Neue** Bereitstellungen: Der Windows Update-Dienst ist standardmäßig deaktiviert.

  **Vorhandene** Bereitstellungen: Wenn Sie die Einstellung im Controller geändert haben, ändert sich der Wert von **Falsch** in **Deaktiviert**, und der vorherige Wert **Wahr** wird zu **Automatisch**.

## <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

> [!IMPORTANT]
> Wenn Sie den App Service-Ressourcenanbieter mit einer SQL Always On-Instanz bereitgestellt haben, MÜSSEN Sie die [Datenbanken „appservice_hosting“ und „appservice_metering“ einer Verfügbarkeitsgruppe hinzufügen](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) und synchronisieren, damit es im Falle eines Datenbankfailovers nicht zu Dienstausfällen kommt.

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

- Worker können den Dateiserver nicht erreichen, wenn App Service in einem bestehenden virtuellen Netzwerk bereitgestellt wird und der Dateiserver nur im privaten Netzwerk verfügbar ist. Dies ist in der Bereitstellungsdokumentation zu Azure App Service in Azure Stack Hub dargestellt.

Wenn Sie sich für die Bereitstellung in einem bestehenden virtuellen Netzwerk und eine interne IP-Adresse für die Verbindung mit Ihrem Dateiserver entschieden haben, müssen Sie eine Sicherheitsregel für ausgehenden Datenverkehr hinzufügen, die den SMB-Verkehr zwischen dem Workersubnetz und dem Dateiserver ermöglicht. Wechseln Sie im Administratorportal zu WorkersNsg, und fügen Sie eine Sicherheitsregel für ausgehenden Datenverkehr mit den folgenden Eigenschaften hinzu:

* Quelle: Any
* Quellportbereich: *
* Ziel: IP-Adressen
* IP-Zieladressbereich: Bereich der IPs für Ihren Dateiserver
* Zielportbereich: 445
* Protokoll: TCP
* Aktion: Allow
* Priorität: 700
* Name: Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Bekannte Probleme von Cloudadministratoren, die Azure App Service in Azure Stack Hub verwenden

Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub 1907](./release-notes.md?view=azs-1907&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über Azure App Service finden Sie unter [Übersicht über Azure App Service und Azure Functions in Azure Stack Hub](azure-stack-app-service-overview.md).
- Weitere Informationen zum Vorbereiten der Bereitstellung von App Service in Azure Stack Hub finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
