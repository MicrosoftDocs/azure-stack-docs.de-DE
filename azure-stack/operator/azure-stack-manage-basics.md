---
title: Grundlagen zur Verwaltung von Azure Stack Hub
description: Lernen Sie die Grundlagen zur Verwaltung von Azure Stack Hub kennen.
author: PatAltimore
ms.topic: article
ms.date: 06/16/2020
ms.author: patricka
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: a873af575c6d18a72cba19e3c3c9536fe4d95fc8
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99495430"
---
# <a name="azure-stack-hub-administration-basics"></a>Grundlagen zur Verwaltung von Azure Stack Hub

Es gibt verschiedene Punkte, mit denen Sie sich vertraut machen müssen, falls Sie noch keine Erfahrung mit der Azure Stack Hub-Verwaltung haben. Dieser Artikel enthält einen Überblick über Ihre Rolle als Azure Stack Hub-Operator und Informationen, die Sie für Ihre Benutzer bereitstellen müssen, um sie beim Steigern der Produktivität zu unterstützen.

## <a name="understand-the-builds"></a>Grundlegendes zu den Builds

### <a name="integrated-systems"></a>Integrierte Systeme

Über Updatepakete werden aktualisierte Versionen von Azure Stack Hub verteilt, wenn Sie ein integriertes Azure Stack Hub-System verwenden. Sie können diese Pakete importieren und anwenden, indem Sie im Administratorportal die Kachel **Updates** verwenden.
 
### <a name="development-kit"></a>Development Kit

Wenn Sie das Azure Stack Development Kit (ASDK) verwenden, helfen Ihnen die Informationen zum Zweck und zu den Einschränkungen des ASDK unter [Was ist Azure Stack Hub?](../asdk/asdk-what-is.md) weiter. Sie können das ASDK als *Sandbox* verwenden, mit der Sie Azure Stack Hub auswerten und Ihre Apps in einer Nichtproduktionsumgebung entwickeln und testen können. Informationen zur Bereitstellung finden Sie unter [Azure Stack Development Kit-Bereitstellung](../asdk/asdk-install.md).

Wir führen schnell Innovationen wie Azure ein. Regelmäßig veröffentlichen wir neue Builds. Wenn Sie das ASDK ausführen und die Umstellung auf den neuesten Build durchführen möchten, müssen Sie [Azure Stack Hub erneut bereitstellen](../asdk/asdk-redeploy.md). Es ist nicht möglich, Updatepakete anzuwenden. Dieser Vorgang dauert zwar eine Weile, doch danach können Sie die neuesten Funktionen ausprobieren. Die ASDK-Dokumentation auf unserer Website gilt für den neuesten Versionsbuild.

## <a name="learn-about-available-services"></a>Weitere Informationen zu verfügbaren Diensten

Es muss Ihnen bewusst sein, welche Dienste Sie Ihren Benutzern zur Verfügung stellen können. Azure Stack Hub unterstützt eine Teilgruppe der Azure-Dienste. Die Liste der unterstützten Dienste wird ständig erweitert.

**Grundlegende Dienste**

Standardmäßig umfasst Azure Stack Hub bei der Bereitstellung folgende grundlegenden Dienste:

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
- Event Hubs
- IoT Hub (in der Vorschauphase)
- Kubernetes (in der Vorschauphase)

Bevor Sie diese Dienste Ihren Benutzern zur Verfügung stellen können, sind zusätzliche Konfigurationsschritte erforderlich. Weitere Informationen hierzu finden Sie in den Abschnitten „Tutorials“ und „Anleitungen\Angebotsdienste“ unserer Dokumentation für Azure Stack Hub-Operator.

**Roadmap zu den Diensten**

In Azure Stack Hub wird die Unterstützung für Azure-Dienste laufend erweitert. Die geplante Roadmap finden Sie im Whitepaper [Azure Stack Hub: Eine Erweiterung von Azure](https://azure.microsoft.com/resources/videos/azure-friday-azure-stack-an-extension-of-azure/). Zudem können Sie sich durch die [Blogbeiträge zu Azure Stack Hub](https://techcommunity.microsoft.com/t5/azure-stack-blog/bg-p/AzureStackBlog) über neue Ankündigungen auf dem Laufenden halten.

## <a name="what-account-should-i-use"></a>Welches Konto sollte verwendet werden?

Es gibt einige Aspekte in Bezug auf Konten, die Sie beim Verwalten von Azure Stack Hub beachten sollten. Sie gelten insbesondere für Bereitstellungen, in denen Windows Server Active Directory-Verbunddienste (AD FS) als Identitätsanbieter verwendet wird, und nicht Azure Active Directory (Azure AD). Die folgenden Überlegungen zu Konten gelten für integrierte Azure Stack Hub-Systeme und ASDK-Bereitstellungen:

|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Lokaler Administrator (.\Administrator)|ASDK-Hostadministrator.|ASDK-Hostadministrator.|
|AzureStack\AzureStackAdmin|ASDK-Hostadministrator.<br><br>Kann zum Anmelden beim Azure Stack Hub-Administratorportal verwendet werden.<br><br>Zugriff zum Anzeigen und Verwalten von Service Fabric-Ringen.|ASDK-Hostadministrator.<br><br>Kein Zugriff auf das Azure Stack Hub-Administratorportal.<br><br>Zugriff zum Anzeigen und Verwalten von Service Fabric-Ringen.<br><br>Nicht mehr Besitzer des Standardabonnements des Anbieters (DPS).|
|AzureStack\CloudAdmin|Kann auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen.|Kann auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen.<br><br>Kann sich nicht beim ASDK-Host anmelden.<br><br>Besitzer des Standardabonnements des Anbieters (DPS).|
|Globaler Azure AD-Administrator|Während der Installation verwendet.<br><br>Besitzer des Standardabonnements des Anbieters (DPS).|Nicht zutreffend|
|

## <a name="what-tools-do-i-use-to-manage"></a>Welche Tools verwende ich für die Verwaltung?
 
Für die Verwaltung von Azure Stack Hub können Sie das [Administratorportal](azure-stack-manage-portals.md) oder PowerShell verwenden. Die einfachste Möglichkeit, sich mit den grundlegenden Konzepten vertraut zu machen, bietet das Portal. Für die Verwendung von PowerShell sind einige Vorbereitungsschritte erforderlich. Bevor Sie beginnen, sollten Sie sich damit vertraut machen, wie PowerShell unter Azure Stack Hub verwendet wird. Weitere Informationen finden Sie unter [Erste Schritte mit PowerShell in Azure Stack Hub](../user/azure-stack-powershell-overview.md).

Azure Stack Hub verwendet den Azure Resource Manager als zugrunde liegenden Mechanismus für die Bereitstellung, Verwaltung und Organisation. Wenn Sie Azure Stack Hub verwalten und Support für Benutzer bereitstellen, können Sie sich mit dem Resource Manager vertraut machen. Weitere Informationen finden Sie im Whitepaper [Erste Schritte mit dem Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Typische Aufgaben

Ihre Benutzer möchten Dienste nutzen. Aus ihrer Sicht besteht Ihre Hauptrolle darin, ihnen diese Dienste zur Verfügung zu stellen. Treffen Sie die Entscheidung, welche Dienste angeboten werden sollen, und stellen Sie diese Dienste durch die Erstellung von Plänen, Angeboten und Kontingenten zur Verfügung. Weitere Informationen finden Sie unter [Übersicht über das Anbieten von Diensten in Azure Stack Hub](service-plan-offer-subscription-overview.md). 

Außerdem müssen Sie dem [Azure Stack Hub-Marketplace](azure-stack-marketplace.md) Elemente hinzufügen. Die einfachste Möglichkeit besteht darin, [Marketplace-Elemente von Azure in Azure Stack Hub herunterzuladen](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Wenn Sie Ihre Pläne, Angebote und Dienste testen möchten, sollten Sie das [Benutzerportal](azure-stack-manage-portals.md) und nicht das Administratorportal verwenden.

Neben der Bereitstellung von Diensten müssen Sie die regulären Aufgaben eines Betreibers durchführen, um den reibungslosen Betrieb von Azure Stack Hub sicherzustellen. Diese Pflichten umfassen folgende Aufgaben:

- Hinzufügen von Benutzerkonten (für [Azure AD](azure-stack-add-new-user-aad.md)- oder [AD FS](azure-stack-add-users-adfs.md)-Bereitstellungen).
- [Zuweisen von Rollen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](azure-stack-manage-permissions.md); (diese Aufgabe ist nicht auf Administratoren beschränkt.)
- [Überwachen der Infrastrukturintegrität](azure-stack-monitor-health.md)
- Verwalten von [Netzwerk](azure-stack-viewing-public-ip-address-consumption.md)- und [Speicherressourcen](azure-stack-manage-storage-accounts.md)
- Ersetzen von fehlerhafter Hardware, z. B. [Ersetzen eines fehlerhaften Datenträgers](azure-stack-replace-disk.md)

## <a name="what-to-tell-your-users"></a>Wichtige Informationen für Ihre Benutzer

Sie müssen Ihre Benutzer über verschiedene Punkte informieren, z. B. die Arbeit mit Diensten in Azure Stack Hub, das Herstellen einer Verbindung mit der Umgebung und das Abonnieren von Angeboten. Zusätzlich zu benutzerdefinierter Dokumentation, die Sie ggf. für Ihre Benutzer bereitstellen möchten, können Sie Benutzer auch an die [Benutzerdokumentation zu Azure Stack Hub](../user/index.yml) weiterleiten.

**Grundlegendes zum Arbeiten mit Diensten in Azure Stack Hub**

Bevor Ihre Benutzer Dienste nutzen und Apps in Azure Stack Hub erstellen, benötigen sie einige Informationen. So gelten z.B. bestimmte PowerShell- und API-Versionsanforderungen. Darüber hinaus bestehen einige Funktionsunterschiede zwischen einem Dienst in Azure und dem entsprechenden Dienst in Azure Stack Hub. Stellen Sie sicher, dass Ihre Benutzer folgende Artikel lesen:

- [Wichtige Aspekte: Verwenden von Diensten oder Erstellen von Apps für Azure Stack Hub](../user/azure-stack-considerations.md)
- [Überlegungen zu virtuellen Computern in Azure Stack Hub](../user/azure-stack-vm-considerations.md)
- [Speicher: Unterschiede und Überlegungen](../user/azure-stack-acs-differences.md)

Die folgenden Artikel enthalten Informationen, in denen die Unterschiede zwischen einem Dienst in Azure und dem von Azure Stack Hub zusammengefasst werden. Diese ergänzen die Informationen, die in der globalen Azure-Dokumentation für einen Azure-Dienst verfügbar sind.

**Herstellen einer Verbindung mit Azure Stack Hub als Benutzer**

Wenn ein Benutzer in einer ASDK-Umgebung eine Verbindung mit dem ASDK-Host nicht per Remotedesktop herstellt, kann er für die Verbindung mit Azure Stack Hub eine VPN-Verbindung (Virtuelles Privates Netzwerk) konfigurieren. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub](../asdk/asdk-connect.md).

Ihre Benutzer sollten wissen, wie sie [auf das Benutzerportal zugreifen](../user/azure-stack-use-portal.md) oder eine Verbindung über PowerShell herstellen. In der Umgebung eines integrierten Systems variiert die Adresse des Benutzerportals je nach Bereitstellung. Sie müssen für Ihre Benutzer die richtige URL bereitstellen.

Bei der Verwendung von PowerShell müssen Benutzer möglicherweise Ressourcenanbieter registrieren, bevor sie Dienste verwenden können. Ein Ressourcenanbieter verwaltet einen Dienst. Der Netzwerkressourcenanbieter verwaltet beispielsweise Ressourcen wie virtuelle Netzwerke, Netzwerkschnittstellen und Lastenausgleichsmodule. Diese Anbieter müssen PowerShell [installieren](powershell-install-az-module.md), zusätzliche Module [herunterladen](azure-stack-powershell-download.md) und PowerShell (sowie die Registrierung von Ressourcenanbietern) [konfigurieren](../user/azure-stack-powershell-configure-user.md).

**Abonnieren von Angeboten**

Bevor ein Benutzer Dienste verwenden kann, muss er [ein Angebot abonnieren](azure-stack-subscribe-plan-provision-vm.md), das Sie als Betreiber erstellt haben.

## <a name="where-to-get-support"></a>Supportquellen

> [!Note]  
> Supportinformationen zu früheren Releases von Azure Stack Hub (vor 1905) finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md).

### <a name="integrated-systems"></a>Integrierte Systeme

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Hardwarepartnern (Original Equipment Manufacturer).

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Support angeboten. Klicken Sie oben rechts im Administratorportal auf das Symbol „Hilfe und Support“ (Fragezeichen), um eine Supportanfrage zu öffnen. Wählen Sie dann **Hilfe und Support** und anschließend im Abschnitt **Support** die Option **Neue Supportanfrage** aus.

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z. B. auf dem Hardware-Lebenszyklushost ausgeführte Software.

Wenden Sie sich bei allen anderen Problemen an den Microsoft-Support.

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK)

Fragen rund um den Support für das ASDK können Sie in den [Microsoft-Foren](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen. Zum Aufrufen der Foren wählen Sie oben rechts im Administratorportal das Symbol „Hilfe und Support“ (Fragezeichen) aus. Anschließend können Sie **Hilfe und Support** und dann im Abschnitt **Support** die Option **MSDN-Foren** auswählen. Diese Foren werden regelmäßig überprüft. Da das ASDK eine Evaluierungsumgebung ist, wird über Microsoft-Support kein offizieller Support angeboten.

## <a name="next-steps"></a>Nächste Schritte

[Regionsverwaltung in Azure Stack Hub](azure-stack-region-management.md)
