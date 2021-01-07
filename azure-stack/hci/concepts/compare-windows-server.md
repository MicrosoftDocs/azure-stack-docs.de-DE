---
title: Vergleichen der Azure Stack HCI mit Azure Stack Hub und Windows Server
description: In diesem Artikel wird erläutert, wie Sie feststellen können, ob die Azure Stack HCI, Azure Stack Hub oder Windows Server für Ihre Organisation geeignet ist.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/22/2020
ms.openlocfilehash: a4429113088497fd6a7c887a65f524a7495c6b74
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737809"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub-and-windows-server"></a>Vergleichen der Azure Stack HCI mit Azure Stack Hub und Windows Server

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019; Azure Stack Hub

Viele Kunden fragen sich, ob Windows Server, die Azure Stack HCI oder Azure Stack Hub ihren Anforderungen am besten entspricht. Dieser Artikel hilft Ihnen dabei, zu bestimmen, welche Option am besten für Ihre Organisation geeignet ist. 

## <a name="compare-azure-stack-hci-to-windows-server"></a>Vergleichen der Azure Stack HCI mit Windows Server

Sowohl Windows Server als auch Azure Stack HCI verfügt über eine Benutzeroberfläche von hoher Qualität mit einer Roadmap für neue Releases.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Bester Gast- und herkömmlicher Server | Bester Virtualisierungshost für ein softwaredefiniertes Rechenzentrum, einschließlich „Direkte Speicherplätze“ |
| Ausführung überall möglich, Verwendung eines herkömmlichen Modells für die Softwarelizenzierung | Kann auf Hardware Ihres bevorzugten Anbieters ausgeführt werden, wird aber als Azure-Dienst bereitgestellt und über Ihr Azure-Konto in Rechnung gestellt |
| Zwei Installationsoptionen: Server mit Desktopdarstellung oder Server Core | Basiert auf einem leicht angepassten Server Core |

### <a name="when-to-use-windows-server"></a>Nutzung von Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server ist ein äußerst vielseitiges Mehrzweck-Betriebssystem mit Dutzenden von Rollen und Hunderten von Features, einschließlich Gastrechten. | Azure Stack HCI verfügt nicht über Gastrechte und ist für eine moderne, hyperkonvergente Architektur bestimmt. |
| Verwenden Sie Windows Server zum Ausführen von VMs oder für Bare-Metal-Installationen, die alle herkömmlichen Serverrollen umfassen, z. B. Active Directory, Dateidienste, DNS, DHCP, Internetinformationsdienste (IIS), Containerhost/-gast, SQL Server, Exchange Server, Host-Überwachungsdienst (Host Guardian Service, HGS) und viele mehr. | Azure Stack HCI ist für die Nutzung als Hyper-V-Virtualisierungshost bestimmt und nur für die direkte Ausführung einer geringen Anzahl von Serverrollen lizenziert. Alle anderen Rollen müssen innerhalb von VMs ausgeführt werden. |

### <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server kann lokal oder in der Cloud ausgeführt werden, verfügt in Zukunft aber nicht mehr über die aktuellen Hyperkonvergenzfunktionen.| Azure Stack HCI ist die zentrale Microsoft-Plattform für hyperkonvergente Infrastruktur, die für die lokale Ausführung von VMs bestimmt ist. Sie kann sich optional über zwei Standorte erstrecken und über Verbindungen mit Azure-Hybriddiensten verfügen. Dies ist eine einfache Möglichkeit, Ihre Rechenzentren und Zweigstellen zu modernisieren und zu schützen, für SQL Server-Datenbanken die branchenweit beste Leistung zu erzielen und VMs oder virtuelle Desktops lokal auszuführen, um für geringe Latenz und Datenhoheit zu sorgen.|
| Windows Server ist für alle Windows Server-Rollen – ob virtualisiert oder nicht – quasi das vielseitige „Schweizer Taschenmesser“. | Verwenden Sie Azure Stack HCI zum Virtualisieren von klassischen Unternehmens-Apps wie Exchange, SharePoint und SQL Server sowie für die Virtualisierung von Windows Server-Rollen wie Dateiserver, DNS, DHCP, IIS und AD. Dies umfasst auch den uneingeschränkten Zugriff auf alle Hyper-V-Features, z. B. abgeschirmte VMs.|
| Viele Windows Server-Bereitstellungen werden auf veralteter Hardware ausgeführt. | Verwenden Sie Azure Stack HCI, um eine softwaredefinierte Infrastruktur anstelle von verwalteten Speicherarrays oder Netzwerkgeräten zu nutzen, ohne dass größere Änderungen an der Architektur vorgenommen werden müssen. Die integrierten Optionen für Hyper-V, „Direkte Speicherplätze“ und softwaredefinierte Netzwerke (SDN) sind direkt zugänglich und können direkt verwaltet werden. Führen Sie Apps auf Windows- oder Linux-VMs aus.|

## <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Vergleichen der Azure Stack HCI mit Azure Stack Hub

Während der digitalen Transformation Ihrer Organisation gelangen Sie unter Umständen zu der Erkenntnis, dass Sie mit Diensten in der öffentlichen Cloud noch schneller moderne Architekturen erstellen und bestehende Apps überarbeiten können. Viele Workloads müssen aber beispielsweise aus technischen oder gesetzlichen Gründen weiterhin lokal verarbeitet werden. Ermitteln Sie anhand der Tabelle unten, mit welcher Microsoft Hybrid Cloud-Strategie Ihre Anforderungen an Komponenten und Standorte erfüllt und innovative Cloudoptionen für Workloads unabhängig vom jeweiligen Ort bereitgestellt werden.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Neue Fähigkeiten, innovative Prozesse | Gleiche Fähigkeiten, vertraute Prozesse |
| Azure-Dienste in Ihrem Rechenzentrum | Verbindung Ihres Rechenzentrums mit Azure-Diensten |

### <a name="when-to-use-azure-stack-hub"></a>Gründe für die Verwendung von Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Verwenden Sie Azure Stack Hub für Self-Service-IaaS-Zwecke (Infrastructure-as-a-Service) mit starker Isolation und präziser Nutzungsnachverfolgung und verbrauchsbasierter Kostenzuteilung für mehrere zusammengestellte Mandanten. Ideal für Dienstanbieter und private Clouds von Unternehmen geeignet. Vorlagen aus dem Azure Marketplace. | In Azure Stack HCI wird Mehrinstanzenfähigkeit nicht nativ erzwungen oder bereitgestellt. |
| Verwenden Sie Azure Stack Hub, um Apps zu entwickeln und auszuführen, die auf lokalen PaaS-Diensten (Platform-as-a-Service) wie Web-Apps, Azure Functions oder Azure Event Hubs basieren. Diese Dienste werden unter Azure Stack Hub genauso wie in Azure ausgeführt und stellen eine einheitliche Hybridentwicklungs- und Laufzeitumgebung dar. | In Azure Stack HCI werden keine PaaS-Dienste lokal ausgeführt. |
| Nutzen Sie Azure Stack Hub, um Bereitstellung und Betrieb von Apps mit DevOps-Methoden (etwa „Infrastructure-as-Code“ oder Continuous Integration und Continuous Deployment (CI/CD)) und benutzerfreundlichen Features (etwa einheitlichen Azure-VM-Erweiterungen) zu modernisieren. Ideal geeignet für Entwicklungs- und DevOps-Teams. | Azure Stack HCI enthält keine nativen DevOps-Tools. |

### <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Für Azure Stack Hub sind mindestens vier Knoten und eigene Netzwerkswitches erforderlich. | Verwenden Sie Azure Stack HCI für den minimalen Speicherbedarf für abgelegene Büros und Zweigstellen. Beginnen Sie mit nur zwei Serverknoten und benachbarten Netzwerken, um für Einfachheit und Kostengünstigkeit zu sorgen. Die Hardwareangebote beginnen bei vier Laufwerken mit 64 GB Speicher zu einem Preis von deutlich unter 10.000 USD pro Knoten. |
| Azure Stack Hub schränkt die Hyper-V-Konfigurierbarkeit und den Featuresatz ein, um Einheitlichkeit mit Azure zu erzielen. | Verwenden Sie Azure Stack HCI für eine einfache Hyper-V-Virtualisierung für klassische Unternehmens-Apps wie Exchange, SharePoint und SQL Server sowie für die Virtualisierung von Windows Server-Rollen wie Dateiserver, DNS, DHCP, IIS und AD. Uneingeschränkter Zugriff auf alle Hyper-V-Features, z. B. abgeschirmte VMs.|
| Diese Infrastrukturtechnologien werden nicht von Azure Stack Hub verfügbar gemacht. | Verwenden Sie Azure Stack HCI, um eine softwaredefinierte Infrastruktur anstelle von verwalteten Speicherarrays oder Netzwerkgeräten zu nutzen, ohne dass größere Änderungen an der Architektur vorgenommen werden müssen. Die integrierten Optionen für Hyper-V, „Direkte Speicherplätze“ und softwaredefinierte Netzwerke (SDN) sind direkt zugänglich und können direkt verwaltet werden. |

## <a name="next-steps"></a>Nächste Schritte

- [Was ist Azure Stack HCI?](../overview.md)
