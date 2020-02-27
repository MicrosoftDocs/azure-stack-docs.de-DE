---
title: Automatisieren von VM-Aktualisierung und -Verwaltung in Azure Stack Hub
description: Erfahren Sie, wie Sie Azure Monitor für VMs, Updateverwaltung, Änderungsnachverfolgung und Bestandslösungen in Azure Automation verwenden, um in Azure Stack Hub bereitgestellte Windows- und Linux-VMs zu verwalten.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 0a105730f36aa9ce14e967bcdcefdb93ece0f26b
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636283"
---
# <a name="vm-update-and-management-automation-in-azure-stack-hub"></a>Automatisieren von VM-Aktualisierung und -Verwaltung in Azure Stack Hub
Verwenden Sie die folgenden Features der Azure Automation-Lösung, um Windows- und Linux-VMs zu verwalten, die mit Azure Stack Hub bereitgestellt werden:

- **[Updateverwaltung](https://docs.microsoft.com/azure/automation/automation-update-management)** : Mit der Updateverwaltungslösung können Sie den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und den Prozess der Installation der erforderlichen Updates für virtuelle Windows- und Linux-Computer verwalten.

- **[Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking)** : Änderungen an installierter Software, an Windows-Diensten, an der Windows-Registrierung und an Windows-Dateien sowie an Linux-Daemons auf den überwachten Servern werden zur Verarbeitung an den Azure Monitor-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf. Mithilfe der Informationen auf dem Dashboard zur Änderungsnachverfolgung können Sie ganz leicht die Änderungen erkennen, die an Ihrer Serverinfrastruktur vorgenommen wurden.

- **[Bestand](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . Die Bestandsnachverfolgung für eine Azure Stack Hub-VM bietet eine browserbasierte Benutzeroberfläche zum Einrichten und Konfigurieren der Bestandssammlung.

- **[Azure Monitor für VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** : Azure Monitor für VMs überwacht Ihre virtuellen Azure- und Azure Stack Hub-Computer (VMs) und VM-Skalierungsgruppen bedarfsorientiert. Der Dienst analysiert die Leistung und Integrität Ihrer Windows- und Linux-VMs und überwacht außerdem deren Prozesse und Abhängigkeiten von anderen Ressourcen und externen Prozessen.

> [!IMPORTANT]
> Diese Lösungen sind identisch mit den Lösungen, die zum Verwalten von virtuellen Azure-Computern verwendet werden. Sowohl Azure- als auch Azure Stack Hub-VMs werden auf die gleiche Weise, unter Verwendung der gleichen Benutzeroberfläche und mit den gleichen Tools verwaltet. Für Azure Stack Hub-VMs gelten für die Verwendung von Updateverwaltung, Änderungsnachverfolgung, Bestandsnachverfolgung und Azure Monitor für VMs mit Azure Stack Hub die gleichen Preise wie für Azure-VMs.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie diese Funktionen zum Aktualisieren und Verwalten von Azure Stack Hub-VMs verwenden können, müssen mehrere Voraussetzungen erfüllt sein. Dazu gehören Schritte, die im Azure-Portal und außerdem im Azure Stack Hub-Verwaltungsportal ausgeführt werden müssen.

### <a name="in-the-azure-portal"></a>Im Azure-Portal
Um die Azure Automation-Features Azure Monitor für VMs, Bestands- und Änderungsnachverfolgung sowie die Updateverwaltung für Azure Stack Hub-VMs zu nutzen, müssen Sie diese Lösungen zunächst in Azure aktivieren.

> [!TIP]
> Wenn Sie diese Features bereits für virtuelle Azure-Computer aktiviert haben, können Sie die bereits vorhandenen Anmeldeinformationen für den LogAnalytics-Arbeitsbereich verwenden. Wenn Sie bereits über eine LogAnalytics-WorkspaceID und einen Primärschlüssel verfügen, die bzw. den Sie verwenden möchten, fahren Sie [mit dem nächsten Abschnitt fort](./vm-update-management.md#in-the-azure-stack-hub-administrator-portal). Andernfalls fahren Sie mit diesem Abschnitt fort, um einen neuen LogAnalytics-Arbeitsbereich und ein Automation-Konto zu erstellen.

Der erste Schritt zur Aktivierung dieser Lösungen besteht darin, [einen LogAnalytics-Arbeitsbereich](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) in Ihrem Azure-Abonnement zu erstellen. Ein Log Analytics-Arbeitsbereich ist eine eigene Azure Monitor-Umgebung mit eigenem Datenrepository, eigenen Datenquellen und eigenen Lösungen. Nachdem Sie einen Arbeitsbereich erstellt haben, notieren Sie sich die WorkspaceID und den Schlüssel. Um diese Informationen anzuzeigen, wechseln Sie zum Blatt „Arbeitsbereich“, klicken Sie auf **Erweiterte Einstellungen**, und überprüfen Sie dann die Werte für die **Arbeitsbereich-ID** und den **Primärschlüssel**. 

Im nächsten Schritt müssen Sie ein [Automation-Konto erstellen](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Ein Automation-Konto ist ein Container für Ihre Azure Automation-Ressourcen. Es bietet eine Möglichkeit, Ihre Umgebungen zu trennen oder Ihre Automation-Workflows und -Ressourcen besser zu organisieren. Nachdem das Automation-Konto erstellt wurde, müssen Sie das Bestands- und Änderungsnachverfolgungs- sowie das Updateverwaltungsfeature aktivieren. Gehen Sie folgendermaßen vor, um die einzelnen Funktionen zu aktivieren:

1. Navigieren Sie im Azure-Portal zu dem Automation-Konto, das Sie verwenden möchten.

2. Wählen Sie die zu aktivierende Lösung aus (**Bestand**, **Änderungsnachverfolgung** oder **Updateverwaltung**).

3. Verwenden Sie die Dropdownliste **Arbeitsbereich auswählen**, um den zu verwendenden Log Analytics-Arbeitsbereich auszuwählen.

4. Überprüfen Sie, ob alle übrigen Informationen richtig sind, und klicken Sie dann auf **Aktivieren**, um die Lösung zu aktivieren.

5. Wiederholen Sie die Schritte 2 bis 4, um alle drei Lösungen zu aktivieren. 

   [![](media/vm-update-management/1-sm.PNG "Enable Azure Automation account features")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Aktivieren von Azure Monitor für VMs

Azure Monitor für VMs überwacht Ihre virtuellen Azure-Computer und VM-Skalierungsgruppen bedarfsorientiert. Der Dienst analysiert die Leistung und Integrität Ihrer Windows- und Linux-VMs und überwacht außerdem deren Prozesse und Abhängigkeiten von anderen Ressourcen und externen Prozessen.

Als Lösung umfasst Azure Monitor für VMs die Unterstützung für die Überwachung von Leistungs- und Anwendungsabhängigkeiten für VMs, die lokal oder bei einem anderen Cloudanbieter gehostet sind. Drei wichtige Features bieten tiefgreifende Einblicke:

1. Logische Komponenten von Azure-VMs, die Windows und Linux ausführen und anhand von vorkonfigurierten Integritätskriterien gemessen werden, warnen Sie, wenn die ausgewertete Bedingung erfüllt wird.

2. Vordefinierte Leistungstrenddiagramme zeigen Kernleistungsmetriken des VM-Gastbetriebssystems an.

3. Eine Abhängigkeitszuordnung zeigt die mit der VM verbundenen Komponenten aus verschiedenen Ressourcengruppen und Abonnements an.

Aktivieren Sie nach der Erstellung des Log Analytics-Arbeitsbereichs die Leistungsindikatoren im Arbeitsbereich für die Sammlung auf Linux- und Windows-VMs. Installieren und aktivieren Sie anschließend die Projektmappen „ServiceMap“ und „InfrastructureInsights“ in Ihrem Arbeitsbereich. Der Prozess wird in der Anleitung [Bereitstellen von Azure Monitor für VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview) beschrieben.

### <a name="in-the-azure-stack-hub-administrator-portal"></a>Im Azure Stack Hub-Administratorportal
Nachdem Sie die Azure Automation-Lösungen im Azure-Portal aktiviert haben, müssen Sie sich als Nächstes als Cloudadministrator beim Azure Stack Hub-Administratorportal anmelden und die Erweiterungen **Azure Monitor, Update- und Konfigurationsverwaltung** und **Azure Monitor, Update- und Konfigurationsverwaltung für Linux** aus dem Azure Stack Hub-Marketplace herunterladen.

   ![Marketplace-Erweiterung „Azure Monitor, Update- und Konfigurationsverwaltung“](media/vm-update-management/2.PNG) 

Um die Zuordnungslösung von Azure Monitor für VMs zu aktivieren und Einblicke in die Netzwerkabhängigkeiten zu erhalten, laden Sie den **Azure Monitor-Dependency-Agent** herunter:

   ![Azure Monitor-Dependency-Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-hub-vms"></a>Aktivieren der Updateverwaltung für Azure Stack Hub-VMs
Führen Sie die folgenden Schritte aus, um die Updateverwaltung für Azure Stack Hub-VMs zu aktivieren.

1. Melden Sie sich beim Azure Stack Hub-Benutzerportal an.

2. Navigieren Sie im Azure Stack Hub-Benutzerportal zum Blatt „Erweiterungen“ der VMs, für die Sie diese Lösungen aktivieren möchten, klicken Sie auf **+ Hinzufügen**, wählen Sie die Erweiterung **Azure Update- und Konfigurationsverwaltung** aus, und klicken Sie dann auf **Erstellen**:

   [![](media/vm-update-management/3-sm.PNG "VM extension blade")](media/vm-update-management/3-lg.PNG#lightbox)

3. Geben Sie die zuvor erstellte WorkspaceID und den Primärschlüssel ein, um den Agent mit dem LogAnalytics-Arbeitsbereich zu verknüpfen. Klicken Sie dann auf **OK**, um die Erweiterung bereitzustellen.

   [![](media/vm-update-management/4-sm.PNG "Providing the WorkspaceID and Key")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Wie in der [Dokumentation zur Updateverwaltung](https://docs.microsoft.com/azure/automation/automation-update-management) beschrieben, müssen Sie die Updateverwaltungslösung für jeden virtuellen Computer aktivieren, den Sie verwalten möchten. Um die Lösung für alle virtuellen Computer zu aktivieren, die den Arbeitsbereich verwenden, wählen Sie **Updateverwaltung** aus, klicken Sie auf **Computer verwalten**, und wählen Sie dann die Option **Auf allen verfügbaren und zukünftigen Computern aktivieren** aus.

   [![](media/vm-update-management/5-sm.PNG "Enable Update Management solution on all machines")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Wiederholen Sie diesen Schritt, um jede Lösung für die Azure Stack Hub-VMs zu aktivieren, die den Arbeitsbereich verwenden. 
  
Nachdem die Erweiterung „Azure Update- und Konfigurationsverwaltung“ aktiviert wurde, wird zwei Mal täglich ein Scan für jeden verwalteten virtuellen Computer durchgeführt. Die API wird alle 15 Minuten aufgerufen, um die letzte Aktualisierungszeit abzufragen und festzustellen, ob sich der Status geändert hat. Wenn sich der Status geändert hat, wird eine Konformitätsprüfung gestartet.

Nachdem die virtuellen Computer überprüft wurden, werden sie im Azure Automation-Konto in der Updateverwaltungslösung angezeigt: 

   [![](media/vm-update-management/6-sm.PNG "Azure Automation account in Update Management")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Es kann zwischen 30 Minuten und sechs Stunden dauern, bis im Dashboard aktualisierte Daten von verwalteten Computern angezeigt werden.

Die Azure Stack Hub-VMs können nun zusammen mit den Azure-VMs in geplante Updatebereitstellungen integriert werden.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack-hub"></a>Aktivieren von Azure Monitor für VMs, die in Azure Stack Hub ausgeführt werden
Sobald die VM die Erweiterungen **Azure Monitor, Update- und Konfigurationsverwaltung** und **Azure Monitor-Dependency-Agent** installiert hat, beginnt sie mit dem Melden von Daten in der [Azure Monitor für VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)-Lösung. 

> [!TIP]
> Für die Erweiterung **Azure Monitor- Dependency-Agent** sind keine Parameter erforderlich. Der Dependency-Agent für das Zuordnungsfeature von Azure Monitor für VMs überträgt selbst keine Daten und erfordert keine Änderungen an Firewalls oder Ports. Die Zuordnungsdaten werden immer vom Log Analytics-Agent an den Azure Monitor-Dienst übertragen, entweder direkt oder über das [OMS-Gateway](https://docs.microsoft.com/azure/azure-monitor/platform/gateway), falls Ihre IT-Sicherheitsrichtlinien es nicht zulassen, dass Computer im Netzwerk eine Verbindung mit dem Internet herstellen.

Azure Monitor für VMs beinhaltet einen Satz Leistungsdiagramme, die auf verschiedene Key Performance Indicators (KPIs) abzielen, um Sie beim Bestimmen der Leistung eines virtuellen Computers zu unterstützen. Die Diagramme zeigen die Ressourcenverwendung über einen Zeitraum, sodass Sie Engpässe und Anomalien identifizieren können. Sie können auch zu einer Perspektive wechseln, die jeden Computer auflistet, um die Ressourcenverwendung auf der Grundlage der ausgewählten Metrik darzustellen. Beim Thema Leistung sind zwar viele Elemente zu berücksichtigen, Azure Monitor für VMs überwacht jedoch Betriebssystem-Key Performance Indicators im Zusammenhang mit Prozessor, Arbeitsspeicher, Netzwerkadapter und Datenträgerverwendung. Leistungsdiagramme ergänzen die Funktion zur Integritätsüberprüfung und helfen dabei, Probleme offenzulegen, die auf einen möglichen Ausfall einer Systemkomponente hinweisen. Azure Monitor für VMS unterstützt außerdem die Kapazitätsplanung, -feinabstimmung und -optimierung, um Effizienz zu erzielen.

   ![Registerkarte „Leistung“ für Azure Monitor-VMs](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Das Anzeigen der ermittelten App-Komponenten auf Windows- und Linux-VMs, die in Azure Stack Hub ausgeführt werden, kann mit Azure Monitor für VMs auf zwei Arten erfolgen. Erstens direkt in einer VM und zweitens übergreifend für Gruppen von VMs in Azure Monitor.
Der Artikel [Verwenden der Zuordnung in Azure Monitor für VMs zum Verstehen von Anwendungskomponenten](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) hilft Ihnen, die Erfahrungen zwischen den beiden Perspektiven und die Verwendung der Zuordnungsfunktion zu verstehen.

   ![Registerkarte „Azure Monitor VMs Map“ (Azure Monitor-VM-Karte)](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

Falls Ihnen [Azure Monitor für VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) keine Leistungsdaten anzeigt, müssen Sie die Sammlung von Leistungsdaten für Windows und Linux in den „Erweiterten Einstellungen“ Ihres [LogAnalytics-Arbeitsbereichs](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters) aktivieren.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Aktivieren der Updateverwaltung mithilfe einer Resource Manager-Vorlage
Wenn Sie eine große Anzahl von Azure Stack Hub-VMs verwenden, können Sie [diese Azure Resource Manager-Vorlage](https://aka.ms/aa6zdzy) verwenden, um die Lösung einfacher auf den VMs bereitzustellen. Die Vorlage stellt die Microsoft Monitoring Agent-Erweiterung auf einer vorhandenen Azure Stack Hub-VM bereit und fügt sie einem vorhandenen Azure LogAnalytics-Arbeitsbereich hinzu.
 
## <a name="next-steps"></a>Nächste Schritte
[Optimierung der Leistung von SQL Server-VMs](azure-stack-sql-server-vm-considerations.md)
