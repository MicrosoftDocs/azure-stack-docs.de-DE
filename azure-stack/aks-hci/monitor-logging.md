---
title: Überwachen von Azure Kubernetes Service auf Azure Stack HCI-Clustern
description: Überwachen und Anzeigen von Protokollierungsdaten für Azure Kubernetes Service auf Azure Stack HCI-Clustern
author: v-susbo
ms.topic: how-to
ms.date: 01/26/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 728f23954d99f6e8cd9373467ed8d104a457e636
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874453"
---
# <a name="monitor-aks-on-azure-stack-hci-clusters"></a>Überwachen von AKS auf Azure Stack HCI-Clustern

Es gibt zwei Arten von Überwachungs- und Protokollierungslösungen für AKS auf Azure Stack HCI. Im Folgenden finden Sie einen Vergleich der beiden Lösungen: 

| Lösung  | Azure-Konnektivität  | Support und Service  | Kosten | Bereitstellung |
| ------- |  ------------  | ---------  | --------------  | ---------------- |
| Azure Monitor | Erfordert Verbindung des AKS auf Azure Stack HCI-Clusters mit Azure unter Verwendung von Azure Arc für Kubernetes. | Vollständige Unterstützung und Service durch Microsoft | Erfordert Registrierung für den Azure Monitor-Dienst. |  Verwenden von Azure Arc zum [Überwachen von Clustern](#monitor-clusters-using-azure-monitor) |
| Lokale Überwachung und Protokollierung | Erfordert keine Azure-Konnektivität. | Unterstützt als Open-Source-Software von Microsoft (keine Supportvereinbarung oder SLAs), durch die Community und/oder externe Anbieter.  | Herstellerabhängig | Kundenorientiert, siehe [Überwachen von Clustern mit lokaler Überwachung](#monitor-clusters-using-on-premises-monitoring). |

## <a name="monitor-clusters-using-azure-monitor"></a>Überwachen von Clustern mithilfe von Azure Monitor
Wenn Sie Azure Monitor mit AKS auf Azure Stack HCI-Clustern verwenden möchten, führen Sie die Schritte in den beiden folgenden Themen aus: 

- [Verbinden Ihres Clusters mit Azure mithilfe von Azure Arc für Kubernetes](./connect-to-arc.md)  
- [Aktivieren von Azure Monitor auf einem Azure Arc-fähigen Kubernetes-Cluster](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters) 

## <a name="monitor-clusters-using-on-premises-monitoring"></a>Überwachen von Clustern mit lokaler Überwachung

Das Überwachen der Integrität, Leistung und Ressourcennutzung der Knoten der Steuerungsebene und der Workloads in Ihrem Cluster ist entscheidend, wenn Apps in der Produktionsumgebung ausgeführt werden. Informationen zum Einrichten einer lokalen Überwachungslösung finden Sie unter [Installieren von Prometheus und Grafana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring). Diese Überwachungslösung umfasst die folgenden beiden Tools: 

- **Prometheus** ist ein Überwachungs- und Warnungstoolkit, das Sie zum Überwachen von containerisierten Workloads verwenden können. Prometheus arbeitet mit verschiedenen Typen von Collectors und Agents zusammen, um Metriken zu erfassen und in einer Datenbank zu speichern, die Sie abfragen und für Berichterstellung verwenden können. 

- **Grafana** ist ein Tool, mit dem Metriken auf den Grafana-Dashboards angezeigt, abgefragt und visualisiert werden können. Er ist so vorkonfiguriert, dass Prometheus als Datenquelle verwendet wird. 

### <a name="install-monitoring-tools"></a>Installieren von Überwachungstools

Wenn Sie in Ihrer Umgebung schnell eine einfache Überwachungslösung einrichten möchten, führen Sie das PowerShell-Skript [Setup-Monitoring.ps1](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#easy-steps-to-setup-monitoring-to-use-local-port-forward-to-access-grafana) aus. Das Skript umfasst alle Konfigurationsschritte, die erforderlich sind, um eine Überwachungslösung schnell einzurichten und zu aktivieren. 

Wenn Sie einen Eingangscontroller mit Grafana einbinden möchten, befolgen Sie die Schritte unter [Verwenden eines Eingangscontrollers, um auf Grafana zuzugreifen](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#detailed-steps-to-setup-monitoring-to-use-ingress-controller-to-access-grafana). Ein Eingangscontroller ist Software, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung (Transport Layer Security) für Kubernetes-Dienste bereitstellt.

### <a name="on-premises-logging"></a>Lokale Protokollierung

Protokollierung ist wichtig für Problembehandlung und Diagnose. Die Protokollierungslösung in AKS auf Azure Stack HCI basiert auf Elasticsearch, Fluent Bit und Kibana (EFK). Diese Komponenten werden alle als Container bereitgestellt: 

- Fluent Bit ist der Protokollprozessor und die Weiterleitungskomponente, die Daten und Protokolle aus unterschiedlichen Quellen erfasst und diese dann formatiert, vereinheitlicht und in Elasticsearch speichert. 
- Elasticsearch ist eine verteilte Such- und Analyse-Engine, mit der die Protokolle für schnelle Suchvorgänge und Datenanalysen zentral gespeichert werden können.  
- Kibana bietet interaktive Visualisierungen in einem Webdashboard. Mit diesem Tool können Sie Protokolle anzeigen und abfragen, die in Elasticsearch gespeichert sind, und diese dann über Diagramme und Dashboards visualisieren.

Informationen zum Einrichten einer lokalen Protokollierungslösung finden Sie in den folgenden Schritten zum [Einrichten der Protokollierung für den Zugriff auf Kibana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#easy-steps-to-setup-logging-to-use-local-port-forward-to-access-kibana). Dieser Artikel umfasst alle Komponenten, die zum Erfassen, Aggregieren und Abfragen von Containerprotokollen im gesamten Cluster erforderlich sind. 

Erweiterte Konfigurationsschritte finden Sie unter [Windows-Protokollierung](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#detailed-steps-to-setup-logging).

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Linux-Anwendungen in einem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows Server-Anwendung in einem Kubernetes-Cluster](./deploy-windows-application.md).
- [Kubernetes-Kernkonzepte](kubernetes-concepts.md).