---
title: Grundlagen zur Verwaltung von Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie alles, was Sie über die Verwaltung von Azure Stack wissen müssen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 83c4295c5f14cb89122aca096b7e90cfd44d8d4a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618600"
---
# <a name="azure-stack-administration-basics"></a>Grundlagen zur Verwaltung von Azure Stack
Wenn Sie nicht mit der Azure Stack-Verwaltung vertraut sind, gibt es verschiedene Punkte, die Ihnen bekannt sein müssen. Dieser Artikel enthält einen Überblick über Ihre Rolle als Azure Stack-Betreiber sowie Informationen, die Sie Ihren Benutzern bereitstellen müssen, damit diese rasch Produktivitätssteigerungen erzielen können.

## <a name="understand-the-builds"></a>Grundlegendes zu den Builds

### <a name="integrated-systems"></a>Integrierte Systeme

Wenn Sie ein integriertes Azure Stack-System verwenden, werden aktualisierte Versionen von Azure Stack über Updatepakete verteilt. Sie können diese Pakete importieren und anwenden, indem Sie im Administratorportal die Kachel „Updates“ verwenden. Das Aktualisieren von ASDK-Installation wird nicht unterstützt. 
 
### <a name="development-kit"></a>Development Kit

Lesen Sie bei Verwendung des Azure Stack Development Kit den Artikel [Neuerungen bei Azure Stack](../asdk/asdk-what-is.md), um sicherzustellen, dass Sie den Zweck des Development Kit und die entsprechenden Einschränkungen verstanden haben. Sie sollten das Development Kit als „Sandkasten“ verwenden, mit dem Sie Azure Stack auswerten und Ihre Apps in einer Nichtproduktionsumgebung entwickeln und testen können. (Informationen zur Bereitstellung finden Sie im Artikel [Azure Stack Development Kit-Bereitstellung](../asdk/asdk-install.md).)

Wir führen schnell Innovationen wie Azure ein. Regelmäßig veröffentlichen wir neue Builds. Wenn Sie das Development Kit ausführen und die Umstellung auf den neuesten Build durchführen möchten, müssen Sie [Azure Stack erneut bereitstellen](../asdk/asdk-redeploy.md). Es ist nicht möglich, Updatepakete anzuwenden. Dieser Vorgang dauert zwar eine Weile, doch danach können Sie die neuesten Funktionen ausprobieren. In der Development Kit-Dokumentation auf unserer Website ist der neueste Releasebuild angegeben.

## <a name="learn-about-available-services"></a>Weitere Informationen zu verfügbaren Diensten

Es muss Ihnen bewusst sein, welche Dienste Sie Ihren Benutzern zur Verfügung stellen können. Azure Stack unterstützt eine Teilgruppe der Azure-Dienste. Die Liste der unterstützten Dienste wird ständig erweitert.

**Grundlegende Dienste**

Standardmäßig umfasst Azure Stack bei der Bereitstellung folgende grundlegenden Dienste:

- Compute
- Storage
- Netzwerk
- Key Vault

Mit diesen grundlegenden Dienste können Sie Ihren Benutzern Infrastructure-as-a-Service (IaaS) mit Minimalkonfiguration anbieten.

**Zusätzliche Dienste**

Derzeit unterstützen wir die folgenden zusätzlichen Platform as a Service-Dienste (PaaS):

- App Service
- Azure-Funktionen
- SQL- und MySQL-Datenbanken

Bevor Sie diese Dienste Ihren Benutzern zur Verfügung stellen können, sind zusätzliche Konfigurationsschritte erforderlich. Weitere Informationen hierzu finden Sie in den Abschnitten „Tutorials“ und „Anleitungen\Angebotsdienste“ unserer Dokumentation für Azure Stack-Betreiber.

**Roadmap zu den Diensten**

In Azure Stack wird laufend die Unterstützung für Azure-Dienste erweitert. Die geplante Roadmap finden Sie im Whitepaper [Azure Stack: Eine Erweiterung von Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). Zudem können Sie sich durch die [Blogbeiträge zu Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) über neue Ankündigungen auf dem Laufenden halten.

## <a name="what-account-should-i-use"></a>Welches Konto sollte verwendet werden?
Es gibt einige Überlegungen zu Konten, die Sie beim Verwalten von Azure Stack beachten sollten. Sie gelten insbesondere für Bereitstellungen, in denen Windows Server Active Directory-Verbunddienste (AD FS) als Identitätsanbieter verwendet wird, und nicht Azure Active Directory (Azure AD). Die folgenden Überlegungen zu Konten gelten für integrierte Azure Stack-Systeme und ASDK-Bereitstellungen:


|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Lokaler Administrator (.\Administrator)|ASDK-Hostadministrator|ASDK-Hostadministrator|
|AzureStack\AzureStackAdmin|ASDK-Hostadministrator<br><br>Kann zum Anmelden beim Azure Stack-Verwaltungsportal verwendet werden.<br><br>Zugriff zum Anzeigen und Verwalten von Service Fabric-Ringen|ASDK-Hostadministrator<br><br>Kein Zugriff auf das Azure Stack-Verwaltungsportal<br><br>Zugriff zum Anzeigen und Verwalten von Service Fabric-Ringen<br><br>Nicht mehr Besitzer des Standardabonnements des Anbieters (DPS)|
|AzureStack\CloudAdmin|Kann auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen|Kann auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen<br><br>Kann sich nicht am ASDK-Host anmelden<br><br>Besitzer des Standardabonnements des Anbieters (DPS)|
|Globaler Azure AD-Administrator|Während der Installation verwendet<br><br>Besitzer des Standardabonnements des Anbieters (DPS)|Nicht zutreffend|
|

## <a name="what-tools-do-i-use-to-manage"></a>Welche Tools verwende ich für die Verwaltung?
 
Für die Verwaltung von Azure Stack können Sie das [Administratorportal](azure-stack-manage-portals.md) oder PowerShell verwenden. Die einfachste Möglichkeit, sich mit den grundlegenden Konzepten vertraut zu machen, bietet das Portal. Für die Verwendung von PowerShell sind einige Vorbereitungsschritte erforderlich. Sie müssen PowerShell [installieren](azure-stack-powershell-install.md), zusätzliche Module [herunterladen](azure-stack-powershell-download.md) und PowerShell [konfigurieren](azure-stack-powershell-configure-admin.md).

Azure Stack verwendet den Azure Resource Manager als zugrunde liegenden Mechanismus für die Bereitstellung, Verwaltung und Organisation. Wenn Sie Azure Stack verwalten und Support für Benutzer bereitstellen, sollten Sie sich mit dem Resource Manager vertraut machen. Weitere Informationen finden Sie im Whitepaper [Erste Schritte mit dem Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Typische Aufgaben

Ihre Benutzer möchten Dienste nutzen. Aus ihrer Sicht besteht Ihre Hauptrolle darin, ihnen diese Dienste zur Verfügung zu stellen. Sie müssen entscheiden, welche Dienste angeboten werden sollen, und diese Dienste durch die Erstellung von Plänen, Angeboten und Kontingenten zur Verfügung stellen. Weitere Informationen finden Sie unter [Übersicht über das Anbieten von Diensten in Azure Stack](azure-stack-offer-services-overview.md). 

Zudem müssen Sie Elemente, z.B. Images für virtuelle Computer, dem [Marketplace](azure-stack-marketplace.md) hinzufügen. Die einfachste Möglichkeit besteht darin, [Marketplace-Elemente von Azure in Azure Stack herunterzuladen](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Wenn Sie Ihre Pläne, Angebote und Dienste testen möchten, sollten Sie das [Benutzerportal](azure-stack-manage-portals.md) verwenden; nicht das Verwaltungsportal.

Neben der Bereitstellung von Diensten müssen Sie alle regulären Aufgaben eines Betreibers durchführen, um den reibungslosen Betrieb von Azure Stack sicherzustellen. Zu diesen Aufgaben gehören Folgende:

- Hinzufügen von Benutzerkonten (für die Bereitstellung von [Azure Active Directory](azure-stack-add-new-user-aad.md) oder [Active Directory-Verbunddiensten](azure-stack-add-users-adfs.md))
- [Zuweisen von rollenbasierten Zugriffssteuerungsrollen (Role-Based Access Control, RBAC)](azure-stack-manage-permissions.md) (Diese Aufgabe ist nicht auf Administratoren beschränkt.)
- [Überwachen der Infrastrukturintegrität](azure-stack-monitor-health.md)
- Verwalten von [Netzwerk-](azure-stack-viewing-public-ip-address-consumption.md) und [Speicherressourcen](azure-stack-manage-storage-accounts.md)
- Ersetzen Sie fehlerhafte Hardware, z.B. [einen fehlerhaften Datenträger](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Wichtige Informationen für Ihre Benutzer

Sie müssen Ihre Benutzer über verschiedene Punkte informieren, z.B. die Arbeit mit Diensten in Azure Stack, das Herstellen einer Verbindung mit der Umgebung und das Abonnieren von Angeboten. Zusätzlich zu benutzerdefinierter Dokumentation, die Sie ggf. für Ihre Benutzer bereitstellen möchten, können Sie Benutzer auch an die Website mit der Dokumentation für Azure Stack-Benutzer weiterleiten.

**Grundlegendes zum Arbeiten mit Diensten in Azure Stack**

Bevor Ihre Benutzer Dienste nutzen und Apps in Azure Stack erstellen, müssen ihnen einige Informationen bewusst sein. So gelten z.B. bestimmte PowerShell- und API-Versionsanforderungen. Darüber hinaus bestehen einige Funktionsunterschiede zwischen einem Dienst in Azure und dem entsprechenden Dienst in Azure Stack. Stellen Sie sicher, dass Ihre Benutzer folgende Artikel lesen:

- [Wichtige Aspekte: Verwenden von Diensten oder Erstellen von Apps für Azure Stack](../user/azure-stack-considerations.md)
- [Überlegungen zu virtuellen Computern in Azure Stack](../user/azure-stack-vm-considerations.md)
- [Speicher: Unterschiede und Überlegungen](../user/azure-stack-acs-differences.md)

Die folgenden Artikel enthalten Informationen, in denen die Unterschiede zwischen einem Dienst in Azure und dem von Azure Stack zusammengefasst werden. Diese ergänzen die Informationen, die in der globalen Azure-Dokumentation für einen Azure-Dienst verfügbar sind.

**Herstellen einer Verbindung mit Azure Stack als Benutzer**

Wenn ein Benutzer in einer Development Kit-Umgebung nicht über einen Remotedesktopzugriff auf den Development Kit-Host verfügt, müssen sie eine Verbindung mit einem virtuellen privaten Netzwerk (VPN) konfigurieren, bevor sie auf Azure Stack zugreifen können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Stack](../asdk/asdk-connect.md). 

Ihre Benutzer sollten wissen, wie sie [auf das Benutzerportal zugreifen](../user/azure-stack-use-portal.md) oder eine Verbindung über PowerShell herstellen. In der Umgebung eines integrierten Systems variiert die Adresse des Benutzerportals je nach Bereitstellung. Sie müssen für Ihre Benutzer die richtige URL bereitstellen.

Bei der Verwendung von PowerShell müssen Benutzer möglicherweise Ressourcenanbieter registrieren, bevor sie Dienste verwenden können. (Ein Ressourcenanbieter verwaltet einen Dienst. Der Netzwerkressourcenanbieter verwaltet beispielsweise Ressourcen wie virtuelle Netzwerke, Netzwerkschnittstellen und Lastenausgleichsmodule.) Diese Anbieter müssen PowerShell [installieren](azure-stack-powershell-install.md), zusätzliche Module [herunterladen](azure-stack-powershell-download.md) und PowerShell (sowie die Registrierung von Ressourcenanbietern) [konfigurieren](../user/azure-stack-powershell-configure-user.md).

**Abonnieren von Angeboten**

Bevor ein Benutzer auf Dienste zugreifen kann, muss dieser [ein Angebot abonnieren](azure-stack-subscribe-plan-provision-vm.md), das Sie als Betreiber erstellt haben.

## <a name="where-to-get-support"></a>Supportquellen

### <a name="integrated-systems"></a>Integrierte Systeme

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Hardwarepartnern (Original Equipment Manufacturer).

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Kundensupport (Microsoft Customer Support Services, CSS) angeboten. Wenn Sie oben rechts im Administratorportal auf das Hilfe- und Supportsymbol (Fragezeichen) und dann auf **Neue Supportanfrage** klicken, wird eine Website angezeigt, auf der Sie direkt eine Supportanfrage öffnen können.

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z.B. auf dem Hardware-Lebenszyklushost ausgeführte Software.

Wenden Sie sich bei allen anderen Problemen an Microsoft CSS.

### <a name="development-kit"></a>Development Kit

Fragen rund um den Support für das Development Kit können Sie in den [Microsoft-Foren](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen. Wenn Sie oben rechts im Administratorportal auf das Hilfe- und Supportsymbol (Fragezeichen) und dann auf **Neue Supportanfrage** klicken, wird die Forenwebsite direkt geöffnet. Diese Foren werden regelmäßig überprüft. Da das Development Kit eine Evaluierungsumgebung ist, wird über den Microsoft CSS kein offizieller Support angeboten.

## <a name="next-steps"></a>Nächste Schritte

[Region management in Azure Stack](azure-stack-region-management.md) (Regionsverwaltung in Azure Stack)


