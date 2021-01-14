---
title: Grundlagen der ASDK-Verwaltung
description: Dieser Artikel beschreibt die grundlegenden Verwaltungsaufgaben für Azure Stack Development Kit (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 02/19/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 205df69311e2543f245af7bdb8c8ddd98f2511be
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97973349"
---
# <a name="asdk-admin-basics"></a>Grundlagen der ASDK-Verwaltung
Wenn Sie mit der Verwaltung des Azure Stack Development Kits (ASDK) noch nicht vertraut sind, gibt es verschiedene Aspekte, die Sie kennen müssen. Dieser Leitfaden bietet eine Übersicht über Ihre Rolle als Azure Stack-Operator in der Evaluierungsumgebung. Wenn Sie sich mit diesen Informationen vertraut machen, stellen Sie sicher, dass Ihre Testbenutzer schnell produktiv arbeiten können.

Lesen Sie zunächst den Artikel [Was ist das Azure Stack Development Kit?](asdk-what-is.md), um sicherzustellen, dass Sie den Zweck des ASDK und die entsprechenden Einschränkungen verstanden haben. Sie sollten das Development Kit als „Sandbox“ verwenden, in dem Sie Azure Stack evaluieren und Ihre Apps in einer Nichtproduktionsumgebung entwickeln und testen können. 

Ebenso wie bei Azure gibt es bei Azure Stack häufig Innovationen – daher veröffentlichen wir regelmäßig neue ASDK-Builds. Sie können das ASDK jedoch nicht auf die gleiche Weise aktualisieren wie Bereitstellungen integrierter Azure Stack-Systeme. Wenn Sie eine Migration zum neuesten Build durchführen möchten, müssen Sie [das ASDK vollständig neu bereitstellen](asdk-redeploy.md). Es ist nicht möglich, Updatepakete anzuwenden. Dieser Vorgang dauert zwar eine Weile, doch danach können Sie die neuesten Funktionen ausprobieren, sobald diese verfügbar sind. 

## <a name="what-account-should-i-use"></a>Welches Konto sollte verwendet werden?
Es gibt einige Überlegungen zu Konten, die Sie beim Verwalten von Azure Stack beachten sollten. Sie gelten insbesondere für Bereitstellungen, in denen Windows Server Active Directory-Verbunddienste (AD FS) als Identitätsanbieter verwendet wird, und nicht Azure Active Directory (Azure AD). Die folgenden Überlegungen zu Konten gelten für integrierte Azure Stack-Systeme und ASDK-Bereitstellungen:

|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Lokaler Administrator (.\Administrator)|ASDK-Hostadministrator|ASDK-Hostadministrator|
|AzureStack\AzureStackAdmin|ASDK-Hostadministrator<br><br>Kann zum Anmelden beim Azure Stack-Administratorportal verwendet werden<br><br>Zugriff zum Anzeigen und Verwalten von Service Fabric-Ringen|ASDK-Hostadministrator<br><br>Kein Zugriff auf das Azure Stack-Administratorportal<br><br>Zugriff zum Anzeigen und Verwalten von Service Fabric-Ringen<br><br>Nicht mehr Besitzer des Standardabonnements des Anbieters (DPS)|
|AzureStack\CloudAdmin|Kann auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen|Kann auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen<br><br>Kann sich nicht beim ASDK-Host anmelden<br><br>Besitzer des Standardabonnements des Anbieters (DPS)|
|Globaler Azure AD-Administrator|Während der Installation verwendet<br><br>Besitzer des Standardabonnements des Anbieters (DPS)|Nicht verfügbar|
|

## <a name="what-tools-do-i-use-to-manage"></a>Welche Tools verwende ich für die Verwaltung?
Für die Verwaltung von Azure Stack können Sie das Azure Stack-Administratorportal (`https://adminportal.local.azurestack.external`) oder PowerShell verwenden. Die einfachste Möglichkeit, sich mit den grundlegenden Konzepten vertraut zu machen, bietet das Portal. Wenn Sie PowerShell verwenden möchten, müssen Sie [PowerShell für Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) installieren und [die Azure Stack-Tools aus GitHub herunterladen](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack verwendet den Azure Resource Manager als zugrunde liegenden Mechanismus für die Bereitstellung, Verwaltung und Organisation. Wenn Sie Azure Stack verwalten und Support für Benutzer bereitstellen werden, sollten Sie sich mit dem Azure Resource Manager vertraut machen. Weitere Informationen finden Sie im Whitepaper [Erste Schritte mit dem Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Typische Aufgaben
Ihre Benutzer möchten Dienste nutzen. Aus ihrer Sicht besteht Ihre Hauptrolle darin, ihnen diese Dienste zur Verfügung zu stellen. Mit dem ASDK finden Sie heraus, welche Dienste Sie anbieten sollten und wie Sie diese Dienste durch die [Erstellung von Plänen, Angeboten und Kontingenten](../operator/tutorial-offer-services.md) zur Verfügung stellen. Zudem müssen Sie dem Marketplace Elemente hinzufügen, z. B. Images für virtuelle Computer (VMs). Die einfachste Möglichkeit besteht darin, [Marketplace-Elemente](../operator/azure-stack-create-and-publish-marketplace-item.md) von Azure in Azure Stack herunterzuladen.

> [!NOTE]
> Wenn Sie Ihre Pläne, Angebote und Dienste testen möchten, sollten Sie das Benutzerportal (`https://portal.local.azurestack.external`) verwenden, nicht das Administratorportal (`https://adminportal.local.azurestack.external`).

Neben der Bereitstellung von Diensten müssen Sie alle regulären Aufgaben eines Azure Stack-Operators durchführen, um den reibungslosen Betrieb des ASDK sicherzustellen. Diese Pflichten umfassen folgende Aufgaben:
- Hinzufügen von Benutzerkonten für Azure AD- oder AD FS-Bereitstellungen.
- Zuweisen von Rollen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC); diese Aufgabe ist nicht auf Administratoren beschränkt).
- Überwachen der Infrastrukturintegrität.
- Verwalten von Netzwerk- und Speicherressourcen.
- Ersetzen fehlerhafter Hardware für den Development Kit-Hostcomputer.

## <a name="where-to-get-support"></a>Supportquellen
Fragen rund um den Support für das ASDK können Sie im [Azure Stack-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen.

Sie können auch auf die Foren zugreifen, indem Sie in der oberen rechten Ecke des Administratorportals auf **Hilfe** (das Fragezeichen) klicken. Klicken Sie anschließend auf **Hilfe und Support**, um die **Übersicht** zu Hilfe und Support zu öffnen. Dort finden Sie einen Link zum Forum. MSDN-Foren werden regelmäßig überprüft.  

> [!IMPORTANT]
> Da das ASDK eine Evaluierungsumgebung ist, wird über Microsoft-Support kein offizieller Support angeboten.

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen des ASDK](asdk-install.md)
