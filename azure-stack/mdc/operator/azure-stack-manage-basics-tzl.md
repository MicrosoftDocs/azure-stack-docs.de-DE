---
title: Grundlagen zur Verwaltung von Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Lernen Sie die Grundlagen zur Verwaltung von Azure Stack Hub kennen.
author: justinha
ms.topic: article
ms.date: 10/26/2020
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 6638ff430482a270b172155160d1599c2844781a
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330187"
---
# <a name="azure-stack-hub-administration-basics"></a>Grundlagen zur Verwaltung von Azure Stack Hub

Es gibt verschiedene Punkte, mit denen Sie sich vertraut machen müssen, falls Sie noch keine Erfahrung mit der Azure Stack Hub-Verwaltung haben. Dieser Artikel enthält einen Überblick über Ihre Rolle als Azure Stack Hub-Operator und Informationen, die Sie für Ihre Benutzer bereitstellen müssen, um sie beim Steigern der Produktivität zu unterstützen.

## <a name="understand-the-builds"></a>Grundlegendes zu den Builds

Wenn Sie ein integriertes Azure Stack Hub-System verwenden, werden aktualisierte Versionen von Azure Stack Hub über Updatepakete verteilt. Sie können diese Pakete importieren und anwenden, indem Sie im Administratorportal die Kachel **Updates** verwenden.

## <a name="learn-about-available-services"></a>Weitere Informationen zu verfügbaren Diensten

Beachten Sie die Dienste, die Sie Ihren Benutzern zur Verfügung stellen können. Azure Stack Hub unterstützt eine Teilgruppe der Azure-Dienste. Die Liste der unterstützten Dienste wird ständig erweitert.

### <a name="foundational-services"></a>Grundlegende Dienste

Standardmäßig umfasst Azure Stack Hub bei der Bereitstellung die folgenden grundlegenden Dienste:

- Compute
- Storage
- Netzwerk
- Key Vault

Mit diesen grundlegenden Dienste können Sie Ihren Benutzern Infrastructure-as-a-Service (IaaS) mit Minimalkonfiguration anbieten.

### <a name="additional-services"></a>Zusätzliche Dienste

Wir unterstützen die folgenden zusätzlichen Platform as a Service-Dienste (PaaS):

- App Service
- Azure-Funktionen
- SQL- und MySQL-Datenbanken
- Kubernetes
- IoT Hub
- Event Hub

Bevor Sie diese Dienste Ihren Benutzern zur Verfügung stellen können, sind zusätzliche Konfigurationsschritte erforderlich. Weitere Informationen hierzu finden Sie in den Abschnitten **Tutorials** und **Anleitungen** > **Angebotsdienste** unserer Dokumentation für [Azure Stack Hub-Operator](https://docs.microsoft.com/azure-stack/operator/).

### <a name="service-roadmap"></a>Roadmap für Dienste

In Azure Stack Hub wird die Unterstützung für Azure-Dienste laufend erweitert. Die geplante Roadmap finden Sie im Whitepaper [Azure Stack Hub: Eine Erweiterung von Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). Zudem können Sie sich durch die [Blogbeiträge zu Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) über neue Ankündigungen auf dem Laufenden halten.

## <a name="what-account-should-i-use"></a>Welches Konto sollte verwendet werden?

Es gibt einige Aspekte in Bezug auf Konten, die Sie beim Verwalten von Azure Stack Hub beachten sollten. Sie gelten insbesondere für Bereitstellungen, in denen Windows Server Active Directory-Verbunddienste (AD FS) als Identitätsanbieter verwendet wird, und nicht Azure Active Directory (Azure AD).

| **Konto** | **Azure** | **AD FS** |
|---|---|---|
| Lokaler Administrator (.\Administrator) |   |
| Globaler Azure AD-Administrator | Während der Installation verwendet. <br> Besitzer des Standardabonnements | Nicht zutreffend. |
| Konto für erweiterten Speicher|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Welche Tools verwende ich für die Verwaltung?

Für die Verwaltung von Azure Stack Hub können Sie das [Administratorportal](../../operator/azure-stack-manage-portals.md) oder PowerShell verwenden. Die einfachste Möglichkeit, sich mit den grundlegenden Konzepten vertraut zu machen, bietet das Portal. Für die Verwendung von PowerShell sind einige Vorbereitungsschritte erforderlich. Bevor Sie beginnen, sollten Sie sich damit vertraut machen, wie PowerShell unter Azure Stack Hub verwendet wird. Weitere Informationen finden Sie unter [Erste Schritte mit PowerShell in Azure Stack Hub](../../user/azure-stack-powershell-overview.md).

Azure Stack Hub verwendet den Azure Resource Manager als zugrunde liegenden Mechanismus für die Bereitstellung, Verwaltung und Organisation. Wenn Sie Azure Stack Hub verwalten und Support für Benutzer bereitstellen, sollten Sie sich mit dem Resource Manager vertraut machen. Weitere Informationen finden Sie im Whitepaper [Erste Schritte mit dem Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Typische Aufgaben

Ihre Benutzer möchten Dienste nutzen. Aus ihrer Sicht besteht Ihre Hauptrolle darin, ihnen diese Dienste zur Verfügung zu stellen. Treffen Sie die Entscheidung, welche Dienste angeboten werden sollen, und stellen Sie diese Dienste durch die Erstellung von Plänen, Angeboten und Kontingenten zur Verfügung. Weitere Informationen finden Sie unter [Übersicht über das Anbieten von Diensten in Azure Stack Hub](../../operator/service-plan-offer-subscription-overview.md).

Außerdem müssen Sie dem [Azure Stack Hub-Marketplace](../../operator/azure-stack-marketplace.md) Elemente hinzufügen. Die einfachste Möglichkeit besteht darin, [Marketplace-Elemente von Azure in Azure Stack Hub herunterzuladen](../../operator/azure-stack-download-azure-marketplace-item.md).

Wenn Sie Ihre Pläne, Angebote und Dienste testen möchten, sollten Sie das [Benutzerportal](../../operator/azure-stack-manage-portals.md) und nicht das Administratorportal verwenden.

Neben der Bereitstellung von Diensten müssen Sie die regulären Aufgaben eines Betreibers durchführen, um den reibungslosen Betrieb von Azure Stack Hub sicherzustellen. Diese Pflichten umfassen folgende Aufgaben:

- Hinzufügen von Benutzerkonten für die [Azure AD](../../operator/azure-stack-add-new-user-aad.md)-Bereitstellung
- [Festlegen von Zugriffsberechtigungen mithilfe der rollenbasierten Zugriffssteuerung](../../operator/azure-stack-manage-permissions.md) (Diese Aufgabe ist nicht auf Administratoren beschränkt.)
- [Überwachen der Infrastrukturintegrität](../../operator/azure-stack-monitor-health.md)
- Verwalten von [Netzwerk](../../operator/azure-stack-viewing-public-ip-address-consumption.md)- und [Speicherressourcen](../../operator/azure-stack-manage-storage-accounts.md)
- [Starten und Beenden von Azure Stack Hub](../../operator/azure-stack-start-and-stop.md)
- [Betreiben des erweiterten Speichers](../../user/azure-stack-network-howto-extend-datacenter.md)
- [Verwalten von IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)
- [Verwalten von Event Hub](https://docs.microsoft.com/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)
- [Verwalten von App Service](https://docs.microsoft.com/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)
- Ersetzen von fehlerhafter Hardware, hier ist die Liste der [ersetzbaren Teile](../../operator/azure-stack-replace-component.md).
- [Erhalten von Support](https://docs.microsoft.com/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)

## <a name="operator-tasks"></a>Operator-Aufgaben

Hier finden Sie eine Liste täglicher, wöchentlicher und monatlicher Aufgaben für einen Operator:

# <a name="daily"></a>[Täglich](#tab/daily)

1. Überprüfen von Warnungen
2. Überprüfen des Sicherungsstatus
3. Aktualisieren der Defender-Signature (getrennte Systeme)
4. Überprüfen der Isilon-Systemintegrität und -Ereignisse in OneFS
5. Überprüfen der Isilon-Kapazität

# <a name="weekly"></a>[Wöchentlich](#tab/weekly)

1. Überprüfen der Kapazität
2. Ausführen von `isi status –verbose` in der Avocent-Verbindung

# <a name="monthly"></a>[Monatlich](#tab/monthly)

1. Anwenden monatlicher Updatepakete (Microsoft und OEM)
2. Überprüfen der Sicherung mithilfe von ASDK
3. Verwalten des Azure Stack Hub-Marketplace (aktuell halten)
4. Aktualisieren der Switch-Firmware und von Avocent
5. Freigeben von Speicherkapazität

# <a name="ondemand"></a>[OnDemand](#tab/ondemand)

1. Rotation für Geheimnisse
2. Erstellen und Aktualisieren von Angeboten, Plänen und Kontingenten
3. Anwenden von Hotfixpaketen
4. Anwenden von Hotfixpaketen
5. Erweitern der Kapazität (Knoten und IPSpace)
6. Ausführen von `isi status –verbose` in der Avocent-Verbindung
7. Wiederherstellen von Speicherkonten
8. Beenden des Systems
9. Erfassen von Diagnoseprotokollen

---

## <a name="what-to-tell-your-users"></a>Wichtige Informationen für Ihre Benutzer

Sie müssen Ihre Benutzer über verschiedene Punkte informieren, z. B. die Arbeit mit Diensten in Azure Stack Hub, das Herstellen einer Verbindung mit der Umgebung und das Abonnieren von Angeboten. Zusätzlich zu benutzerdefinierter Dokumentation, die Sie ggf. für Ihre Benutzer bereitstellen möchten, können Sie Benutzer auch an die [Benutzerdokumentation zu Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/) weiterleiten.

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Grundlegendes zum Arbeiten mit Diensten in Azure Stack Hub

Bevor Ihre Benutzer Dienste nutzen und Apps in Azure Stack Hub erstellen, benötigen sie einige Informationen. So gelten z.B. bestimmte PowerShell- und API-Versionsanforderungen. Darüber hinaus bestehen einige Funktionsunterschiede zwischen einem Dienst in Azure und dem entsprechenden Dienst in Azure Stack Hub. Stellen Sie sicher, dass Ihre Benutzer folgende Artikel lesen:

- [Unterschiede zwischen Azure Stack Hub und Azure beim Verwenden von Diensten und Erstellen von Apps](../../user/azure-stack-considerations.md)
- [Features von Azure Stack Hub-VMs](../../user/azure-stack-vm-considerations.md)
- [Azure Stack Hub-Speicher: Unterschiede und Überlegungen](../../user/azure-stack-acs-differences.md)

Die folgenden Artikel enthalten Informationen, in denen die Unterschiede zwischen einem Dienst in Azure und dem von Azure Stack Hub zusammengefasst werden. Diese ergänzen die Informationen, die in der globalen Azure-Dokumentation für einen Azure-Dienst verfügbar sind.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Herstellen einer Verbindung mit Azure Stack Hub als Benutzer

Ihre Benutzer sollten wissen, wie sie [auf das Benutzerportal zugreifen](../../user/azure-stack-use-portal.md) oder eine Verbindung über PowerShell herstellen. In der Umgebung eines integrierten Systems variiert die Adresse des Benutzerportals je nach Bereitstellung. Sie müssen für Ihre Benutzer die richtige URL bereitstellen.

Bei der Verwendung von PowerShell müssen Benutzer möglicherweise Ressourcenanbieter registrieren, bevor sie Dienste verwenden können. Ein Ressourcenanbieter verwaltet einen Dienst. Der Netzwerkressourcenanbieter verwaltet beispielsweise Ressourcen wie virtuelle Netzwerke, Netzwerkschnittstellen und Lastenausgleichsmodule. Diese Anbieter müssen PowerShell [installieren](../../operator/azure-stack-powershell-install.md), zusätzliche Module [herunterladen](../../operator/azure-stack-powershell-download.md) und PowerShell (sowie die Registrierung von Ressourcenanbietern) [konfigurieren](../../user/azure-stack-powershell-configure-user.md).

### <a name="subscribe-to-an-offer"></a>Abonnieren von Angeboten

Bevor ein Benutzer Dienste verwenden kann, muss er [ein Angebot abonnieren](../../operator/azure-stack-subscribe-plan-provision-vm.md), das Sie als Betreiber erstellt haben.

## <a name="where-to-get-support"></a>Supportquellen

Supportinformationen zu früheren Releases von Azure Stack Hub (vor 1905) finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](../../operator/azure-stack-servicing-policy.md).

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Hardwarepartnern (Original Equipment Manufacturer).

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Kundensupport (Microsoft Customer Support Services, CSS) angeboten. Klicken Sie oben rechts im Administratorportal auf das Symbol „Hilfe und Support“ (Fragezeichen), um eine Supportanfrage zu öffnen. Wählen Sie dann **Hilfe und Support** und anschließend im Abschnitt **Support** die Option **Neue Supportanfrage** aus.

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z. B. auf dem Hardware-Lebenszyklushost ausgeführte Software.

Wenden Sie sich bei allen anderen Problemen an Microsoft CSS.

## <a name="next-steps"></a>Nächste Schritte

- [Regionsverwaltung in Azure Stack Hub](../../operator/azure-stack-region-management.md)
