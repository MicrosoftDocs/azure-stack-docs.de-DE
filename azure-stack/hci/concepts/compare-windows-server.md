---
title: Vergleichen der Azure Stack HCI mit Windows Server
description: In diesem Artikel wird erläutert, wie Sie feststellen können, ob die Azure Stack HCI oder Windows Server die richtige Wahl für Ihre Organisation ist.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/21/2021
ms.openlocfilehash: c691424251d096794315880106c131ecaaea1f57
ms.sourcegitcommit: 925351b77490364b3d52746f788c4c1b93343631
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98705192"
---
# <a name="compare-azure-stack-hci-to-windows-server-2019"></a>Vergleichen der Azure Stack HCI mit Windows Server 2019

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

In diesem Artikel werden die wichtigsten Unterschiede zwischen der Azure Stack HCI und Windows Server 2019 erläutert. Darüber hinaus finden Sie Hinweise dazu, wann die Lösungen jeweils verwendet werden müssen. Beide Produkte werden von Microsoft aktiv unterstützt und gewartet. In der Tat entscheiden sich unter Umständen viele Organisationen, beide Lösungen bereitzustellen, da sie für unterschiedliche und komplementäre Zwecke vorgesehen sind.

## <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

Die Azure Stack HCI ist die primäre hyperkonvergente Infrastrukturplattform von Microsoft und für die lokale Ausführung von VMs oder virtuellen Desktops bestimmt, wobei Verbindungen mit Azure-Hybriddiensten bestehen. Dies ist eine einfache Möglichkeit, Ihre Rechenzentren und Filialen zu modernisieren und zu schützen sowie die branchenweit die beste Leistung mit geringer Latenz und Datenhoheit zu erzielen.


:::image type="content" source="media/compare-windows-server/hci-scenarios.png" alt-text="Verwendung der Azure Stack HCI anstelle von Windows Server 2019" border="false" lightbox="media/compare-windows-server/hci-scenarios.png":::


Verwenden Sie die Azure Stack HCI:

- Als besten Virtualisierungshost zum Modernisieren Ihrer Infrastruktur, entweder für vorhandene Workloads in Ihrem zentralen Rechenzentrum oder für die sich entwickelnden Anforderungen von Filialen und Edgestandorten
- Für eine einfache Erweiterbarkeit auf die Cloud, mit einem regelmäßigen Innovationsstrom aus Ihrem Azure-Abonnement sowie konsistenten Tools und Umgebungen
- Um alle Vorteile einer hyperkonvergenten Infrastruktur zu nutzen: eine einfachere, konsolidiertere Rechenzentrumsarchitektur mit Hochgeschwindigkeitsspeicher und einem Netzwerk

  >[!NOTE]
  >Da die Azure Stack HCI als Hyper-V-Virtualisierungshost für eine moderne, hyperkonvergente Architektur verwendet werden soll, sind keine Gastrechte enthalten. Aus diesem Grund ist die Azure Stack HCI nur für die direkte Ausführung einer geringen Anzahl von Serverrollen lizenziert. Alle anderen Rollen müssen innerhalb von VMs ausgeführt werden.

## <a name="when-to-use-windows-server-2019"></a>Nutzung von Windows Server 2019

Windows Server 2019 ist ein äußerst vielseitiges Mehrzweckbetriebssystem mit Dutzenden Rollen und Hunderten Features, einschließlich Gastrechten. Windows Server-Computer können in der Cloud oder lokal ausgeführt werden, einschließlich der Virtualisierung auf der Azure Stack HCI.


:::image type="content" source="media/compare-windows-server/windows-server-scenarios.png" alt-text="Verwendung von Windows Server 2019 anstelle der Azure Stack HCI" border="false" lightbox="media/compare-windows-server/windows-server-scenarios.png":::


Verwenden Sie Windows Server 2019:

- Als Gastbetriebssystem innerhalb von virtuellen Computern (Virtual Machines, VMs) oder Containern
- Als Runtime für eine Windows-Anwendung
- Um mindestens eine der integrierten Serverrollen verwenden zu können, z. B. Active Directory, Dateidienste, DNS, DHCP oder die Internetinformationsdienste (Internet Information Services, IIS)
- Als herkömmlicher Server, z. B. Bare-Metal-Domänencontroller oder SQL Server-Installation
- Als traditionelle Infrastruktur, z. B. als VMs, die mit Fibre Channel-SAN-Speicher verbunden sind

## <a name="compare-product-positioning"></a>Vergleichen der Produktpositionierung

Die folgende Tabelle enthält eine allgemeine Gegenüberstellung der Produkteigenschaften der Azure Stack HCI und von Windows Server 2019.

| **Attribut**    | **Azure Stack HCI** | **Windows Server 2019** |
| ---------------- | ------------------- | ----------------------- |
| Produkttyp     | Clouddienst, der ein Betriebssystem und mehr umfasst | Betriebssystem |
| Rechtliche Hinweise            | Durch Ihre Microsoft-Kundenvereinbarung oder Onlineabonnementvereinbarung abgedeckt | Verfügt über eine eigene Endbenutzer-Lizenzvereinbarung |
| Lizenzierung        | Abrechnung über Ihr Azure-Abonnement | Verfügt über eine eigene kostenpflichtige Lizenz |
| Support          | Durch den Azure-Support angeboten | Kann durch unterschiedliche Supportvereinbarungen abgedeckt werden, einschließlich des Microsoft Premier Support |
| Ursprung  | Download von [Azure.com/HCI](https://azure.com/hci) oder auf integrierten Systemen vorinstalliert | Microsoft Volume Licensing Service Center oder Evaluation Center |
| Ausführung auf VMs      | Nur zur Auswertung, als Hostbetriebssystem gedacht | Ja, in der Cloud oder lokal |
| Hardware         | Auf über 200 vorab überprüften Lösungen aus dem [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net) ausführbar | Auf jeder Hardware mit dem Logo „Certified for Windows Server 2019“ (Für Windows Server 2019 zertifiziert) |
| Lebenszyklusrichtlinie | Dank der neuesten Features immer auf dem neuesten Stand | Auswahl zwischen [Windows Server-Wartungskanälen](/windows-server/get-started-19/servicing-channels-19): Long-Term Servicing Channel (LTSC) und Semi-Annual Channel (SAC) |

## <a name="compare-technical-features"></a>Vergleichen der technischen Features

In der folgenden Tabelle werden die technischen Features der Azure Stack HCI und von Windows Server 2019 verglichen.

| **Attribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Grundlegende Hyper-V-Funktionalität | Ja | Ja |
| Grundlegendes Feature „Direkte Speicherplätze“ | Ja | Ja |
| Grundlegende SDN-Funktionalität | Ja | Ja |
| Stretchclustering für die Notfallwiederherstellung | Ja | - |
| 4–5-mal schnellere Reparatur von Speicherplätzen | Ja | - |
| Integrierte Treiber und Firmwareupdates | Ja (nur integrierte Systeme) | - |
| Angeleitete Bereitstellung | Ja | - |

## <a name="compare-management-options"></a>Vergleichen der Verwaltungsoptionen

In der folgenden Tabelle werden die Verwaltungsoptionen für die Azure Stack HCI und Windows Server 2019 verglichen. Beide Produkte wurden für die Remoteverwaltung konzipiert, und viele Verwaltungstools können für beide Lösungen eingesetzt werden.

| **Attribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Desktopdarstellung | - | Ja |
| Windows Admin Center | Ja | Ja |
| Microsoft System Center | Ja (separat verkauft) | Ja (separat verkauft) |
| Azure-Portal | Ja (nativ) | Erfordert den Arc-Agent |
| Tools von Drittanbietern | Ja | Ja |

## <a name="compare-product-pricing"></a>Vergleichen der Produktpreise

In der folgenden Tabelle werden die Produktpreise der Azure Stack HCI und von Windows Server 2019 verglichen. Weitere Informationen finden Sie unter [Preise der Azure Stack HCI](https://azure.microsoft.com/pricing/details/azure-stack/hci/).

| **Attribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Preistyp | Abonnementdienst | Variiert: meistens eine einmalige Lizenz |
| Preisstruktur | Pro Kern, pro Monat | Variiert: normalerweise pro Kern |
| Preis | 10 USD pro Kern, pro Monat | Weitere Informationen unter [Preise und Lizenzierung für Windows Server 2019](https://www.microsoft.com/windows-server/pricing) |
| Auswertungs-/Testzeitraum | 30-tägige kostenlose Testversion nach der Registrierung | 180-tägige Auswertungsversion |
| Channels | Enterprise Agreement, Clouddienstanbieter oder direkt | Enterprise Agreement/Volumenlizenzierung, OEM, Diensteanbieter-Lizenzvereinbarung (Services Provider License Agreement, SPLA) |

## <a name="next-steps"></a>Nächste Schritte

- [Vergleichen der Azure Stack HCI mit Azure Stack Hub](compare-azure-stack-hub.md)
