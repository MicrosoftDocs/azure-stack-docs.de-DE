---
title: Vergleichen der Azure Stack HCI mit Azure Stack Hub
description: In diesem Artikel erhalten Sie Informationen, mit denen Sie bestimmen können, ob sich die Azure Stack HCI oder Azure Stack Hub besser für Ihre Organisation eignet.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/04/2021
ms.openlocfilehash: 7c501650d5c9a4d2d6df66269f689d80ffd3f8e0
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570683"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Vergleichen der Azure Stack HCI mit Azure Stack Hub

> Gilt für: Azure Stack HCI, Version 20H2; Azure Stack Hub

Während der digitalen Transformation Ihrer Organisation gelangen Sie unter Umständen zu der Erkenntnis, dass Sie mit Diensten in der öffentlichen Cloud noch schneller moderne Architekturen erstellen und bestehende Apps überarbeiten können. Viele Workloads müssen aber beispielsweise aus technischen oder gesetzlichen Gründen weiterhin lokal verarbeitet werden. Ermitteln Sie anhand der Tabelle unten, mit welcher Microsoft Hybrid Cloud-Strategie Ihre Anforderungen an Komponenten und Standorte erfüllt und innovative Cloudoptionen für Workloads unabhängig vom jeweiligen Ort bereitgestellt werden.

| Azure Stack HCI | Azure Stack Hub |
| --------------- | --------------- |
| Gleiche Fähigkeiten, vertraute Prozesse | Neue Fähigkeiten, innovative Prozesse |
| Verbindung Ihres Rechenzentrums mit Azure-Diensten | Azure-Dienste in Ihrem Rechenzentrum |

## <a name="when-to-use-azure-stack-hci"></a>Nutzung von Azure Stack HCI

In der folgenden Tabelle werden Anwendungsfälle verglichen, in denen die Azure Stack HCI eine bessere Wahl ist als Azure Stack Hub.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                         |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Verwenden Sie Azure Stack HCI für den minimalen Speicherbedarf für abgelegene Büros und Zweigstellen. Beginnen Sie mit nur zwei Servern und Back-to-Back-Netzwerken ohne Switch, um für Einfachheit und Kostengünstigkeit zu sorgen. | Für Azure Stack Hub sind mindestens vier Server und eigene Netzwerkswitches erforderlich. |
| Verwenden Sie die Azure Stack HCI zum Virtualisieren von klassischen Unternehmens-Apps wie Exchange, SharePoint und SQL Server sowie für die Virtualisierung von Windows Server-Rollen wie Dateiserver, DNS, DHCP, IIS und Active Directory. Mit dieser Option erhalten Sie uneingeschränkten Zugriff auf Hyper-V-Features.| Azure Stack Hub schränkt die Hyper-V-Konfigurierbarkeit und die Features ein, um für Einheitlichkeit mit Azure zu sorgen. | 
| Verwenden Sie Azure Stack HCI, um eine softwaredefinierte Infrastruktur anstelle von verwalteten Speicherarrays oder Netzwerkgeräten zu nutzen, ohne dass größere Änderungen an der Architektur vorgenommen werden müssen. Die integrierten Optionen für Hyper-V, „Direkte Speicherplätze“ und softwaredefinierte Netzwerke (SDN) sind direkt zugänglich und können direkt verwaltet werden. | Diese Infrastrukturtechnologien werden nicht von Azure Stack Hub verfügbar gemacht. |

## <a name="when-to-use-azure-stack-hub"></a>Gründe für die Verwendung von Azure Stack Hub

In der folgenden Tabelle werden Anwendungsfälle verglichen, in denen Azure Stack Hub eine bessere Wahl ist als die Azure Stack HCI.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                          |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| In Azure Stack HCI wird Mehrinstanzenfähigkeit nicht nativ erzwungen oder bereitgestellt. | Verwenden Sie Azure Stack Hub für Self-Service-IaaS-Zwecke (Infrastructure-as-a-Service) mit starker Isolation und präziser Nutzungsnachverfolgung und verbrauchsbasierter Kostenzuteilung für mehrere zusammengestellte Mandanten. Ideal für Dienstanbieter und private Clouds von Unternehmen geeignet. Vorlagen aus dem Azure Marketplace. | 
| Für Azure Stack HCI ist die lokale Ausführung von PaaS-Diensten (Platform-as-a-Service) nicht vorgesehen, aber das Hosten von [Azure Kubernetes Service](../../aks-hci/overview.md) ist möglich. | Verwenden Sie Azure Stack Hub, um Apps zu entwickeln und auszuführen, die auf lokalen PaaS-Diensten (Platform-as-a-Service) wie Web-Apps, Azure Functions oder Azure Event Hubs basieren. Diese Dienste werden unter Azure Stack Hub genauso wie in Azure ausgeführt und stellen eine einheitliche Hybridentwicklungs- und Laufzeitumgebung dar. |
| Azure Stack HCI enthält keine nativen DevOps-Tools. | Nutzen Sie Azure Stack Hub, um Bereitstellung und Betrieb von Apps mit DevOps-Methoden (etwa „Infrastructure-as-Code“ oder Continuous Integration und Continuous Deployment (CI/CD)) und benutzerfreundlichen Features (etwa einheitlichen Azure-VM-Erweiterungen) zu modernisieren. Ideal geeignet für Entwicklungs- und DevOps-Teams. |

## <a name="next-steps"></a>Nächste Schritte

- [Vergleichen der Azure Stack HCI mit Azure Stack Hub und Windows Server](compare-windows-server.md)
