---
title: Vergleich zwischen Azure Stack Hub und der globalen Azure-Infrastruktur | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Azure und die Familie der Azure Stack Hub-Dienste von Microsoft in einer Azure-Infrastruktur bereitgestellt werden.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/03/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 2d350bedd88ba5c3d04b29f833a17bdd19705f05
ms.sourcegitcommit: 320eddb281a36d066ec80d67b103efad7d4f33c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76145851"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>Unterschiede zwischen der globalen Azure-Infrastruktur, Azure Stack Hub und Azure Stack HCI

Azure und die Familie der Azure Stack Hub-Dienste werden von Microsoft in einer Azure-Infrastruktur bereitgestellt. Verwenden Sie unabhängig davon, ob Ihr Unternehmen eine globale Azure-Infrastruktur oder lokale Ressourcen nutzt, dasselbe Anwendungsmodell, Self-Service-Portale und APIs mit Azure Resource Manager, um cloudbasierte Funktionen bereitzustellen.

In diesem Artikel werden Funktionen der globalen Azure-Infrastruktur, von Azure Stack Hub und von Azure Stack HCI beschrieben. Er enthält zudem Empfehlungen zu häufigen Szenarien, damit Sie die bestmögliche Auswahl treffen können, wenn es um die Bereitstellung von cloudbasierten Microsoft-Diensten für Ihre Organisation geht.

![Übersicht über die Azure-Infrastruktur](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globale Azure-Umgebung

Bei Microsoft Azure handelt es sich um Clouddienste, die stetig erweitert werden, um Ihre Organisation bei geschäftlichen Herausforderungen zu unterstützen. Sie können Anwendungen mit Ihren bevorzugten Tools und Frameworks in einem großen globalen Netzwerk erstellen, verwalten und bereitstellen.

Über die globale Azure-Infrastruktur werden mehr als 100 Dienste angeboten, die in 54 Regionen weltweit verfügbar sind. Die aktuelle Liste mit den globalen Azure-Diensten finden Sie unter [*Verfügbare Produkte nach Region*](https://azure.microsoft.com/regions/services). Die in Azure verfügbaren Dienste sind nach Kategorie aufgeführt, und es ist angegeben, ob sie bereits allgemein verfügbar sind oder sich noch in der Vorschauphase befinden.

Weitere Informationen zu globalen Azure-Diensten finden Sie auf der [Seite zu den ersten Schritten mit Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack-hub"></a>Azure Stack Hub

Azure Stack Hub ist eine Erweiterung von Azure, die für Ihre lokale Umgebung die Flexibilität und das Innovationspotenzial des Cloud Computing ermöglicht. Bei lokaler Bereitstellung kann Azure Stack Hub genutzt werden, um einheitliche Azure-Dienste bereitzustellen – entweder mit Internetverbindung (und Azure-Verbindung) oder in Umgebungen ohne Internetverbindung. Für Azure Stack Hub wird die gleiche zugrunde liegende Technologie wie für die globale Azure-Infrastruktur verwendet. Dies umfasst als Kernkomponenten IaaS- (Infrastructure-as-a-Service), SaaS- (Software-as-a-Service) und optional PaaS-Funktionen (Platform-as-a-Service), z. B.:

- Azure-VMs für Windows und Linux
- Azure Web Apps und Functions
- Azure-Schlüsseltresor
- Azure Resource Manager
- Azure Marketplace
- Container
- Verwaltungstools (Pläne, Angebote, rollenbasierte Zugriffssteuerung usw.)

Die PaaS-Funktionen von Azure Stack Hub sind optional, weil Azure Stack Hub nicht von Microsoft betrieben wird, sondern von unseren Kunden. Dies bedeutet, dass Sie Endbenutzern jeden gewünschten PaaS-Dienst anbieten können, wenn Sie darauf vorbereitet sind, die zugrunde liegende Infrastruktur und die Prozesse für den Endbenutzer zu abstrahieren. Azure Stack Hub verfügt aber über mehrere optionale PaaS-Dienstanbieter, z. B. App Service, SQL-Datenbanken und MySQL-Datenbanken. Diese werden als Ressourcenanbieter bereitgestellt. Sie sind daher mehrinstanzenfähig, werden regelmäßig mit Azure Stack Hub-Standardupdates aktualisiert, sind im Azure Stack Hub-Portal sichtbar und sind gut in Azure Stack Hub integriert.

Zusätzlich zu den oben beschriebenen Ressourcenanbietern sind noch zusätzliche PaaS-Dienste verfügbar, die als [Lösungen auf Basis von Azure Resource Manager-Vorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) getestet wurden und in IaaS ausgeführt werden. Sie als Azure Stack Hub-Betreiber können sie Ihren Benutzern aber als PaaS-Dienste anbieten, z. B.:

- Service Fabric
- Kubernetes Container Service
- Ethereum-Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Beispiele für Azure Stack Hub-Anwendungsfälle:

- Finanzmodelle
- Klinische Daten und Daten zur Geltendmachung von Ansprüchen
- IoT-Geräteanalysen
- Sortimentsoptimierung für Einzelhandel
- Optimierung der Lieferkette
- Industrielles IoT
- Predictive Maintenance
- Smart City
- Bürgereinbindung

Weitere Informationen zu Azure Stack Hub finden Sie unter [Was ist Azure Stack Hub?](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI

Mit [Azure Stack HCI](azure-stack-hci-overview.md)-Lösungen können Sie virtuelle Computer lokal ausführen und über eine HCI-Lösung (Hyper-Converged Infrastructure, hyperkonvergente Infrastruktur) leicht eine Verbindung mit Azure herstellen. Setzen Sie einheitliche Azure-Dienste für die lokale Erstellung und Ausführung von Cloudanwendungen ein, um die Einhaltung gesetzlicher Bestimmungen und technischer Anforderungen zu gewährleisten. Zusätzlich zur lokalen Ausführung von virtualisierten Anwendungen können Sie mit Azure Stack HCI veraltete Serverinfrastruktur austauschen und konsolidieren und für Clouddienste eine Verbindung mit Azure herstellen, indem Sie Windows Admin Center nutzen.

Über Azure Stack HCI werden überprüfte HCI-Lösungen auf Basis von Hyper-V und Direkte Speicherplätze mit Windows Server 2019 Software-Defined Datacenter (SDDC) bereitgestellt. Windows Admin Center wird für die Verwaltung und den integrierten Zugriff auf Azure-Dienste genutzt, z. B.:

- Azure Backup
- Azure Site Recovery
- Azure Monitor und Update

Eine aktualisierte Liste mit Azure-Diensten, mit denen Sie für Azure Stack HCI eine Verbindung herstellen können, finden Sie unter [Connecting Windows Server to Azure hybrid services](https://docs.microsoft.com/windows-server/azure-hybrid-services/index) (Verbinden von Windows Server mit Azure-Hybriddiensten).

### <a name="example-use-cases-for-azure-stack-hci"></a>Beispiele für Azure Stack HCI-Anwendungsfälle
- Remote- oder Filialsysteme
- Konsolidierung von Rechenzentren
- Virtuelle Desktopinfrastruktur
- Unternehmenskritische Infrastruktur
- Kostengünstige Speicherung
- Hochverfügbarkeit und Notfallwiederherstellung in der Cloud
- Unternehmens-Apps, z. B. SQL Server

Besuchen Sie die [Azure Stack HCI-Website](https://azure.microsoft.com/overview/azure-stack/hci/), um sich über die mehr als 70 Azure Stack HCI-Lösungen zu informieren, die Microsoft-Partner derzeit im Angebot haben.

## <a name="next-steps"></a>Nächste Schritte

[Grundlagen zur Verwaltung von Azure Stack Hub](azure-stack-manage-basics.md)

[Schnellstart: Verwenden des Azure Stack Hub-Verwaltungsportals](azure-stack-manage-portals.md)
