---
title: Überwachen von Azure Stack HCI mit Azure Monitor
description: Verwenden Sie Azure Monitor in Windows Admin Center zum Überwachen von Servern und Konfigurieren von Warnungen.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 04/03/2020
ms.openlocfilehash: 43bcc5be8fd96e33d16cfdebd87e0d965c8eff41
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196969"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>Überwachen von Azure Stack HCI mit Azure Monitor

> Gilt für: Windows Server 2019

[Azure Monitor](/azure/azure-monitor/overview) sammelt, analysiert und verarbeitet Telemetriedaten von einer Vielzahl von Ressourcen, einschließlich Windows-Servern und virtuellen Computern (Virtual Machines, VMs), sowohl lokal als auch in der Cloud. Azure Monitor ruft zwar Daten aus Azure-VMs und anderen Azure-Ressourcen ab, in diesem Artikel wird jedoch die Funktionsweise von Azure Monitor mit lokalen Servern und VMs beschrieben, die in Azure Stack HCI ausgeführt werden, insbesondere mit Windows Admin Center.

## <a name="how-does-azure-monitor-work"></a>Funktionsweise von Azure Monitor
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="Diagramm der Funktionsweise von Azure Monitor" border="false":::
Von lokalen Windows-Servern generierte Daten werden in einem Log Analytics-Arbeitsbereich in Azure Monitor gesammelt. Sie können in einem Arbeitsbereich verschiedene Überwachungslösungen aktivieren. Dies sind Sätze von Logik, die Erkenntnisse für ein bestimmtes Szenario bieten. Überwachungslösungen, die in einem Arbeitsbereich aktiviert werden können, sind beispielsweise die Azure-Updateverwaltung, Azure Security Center und Azure Monitor für VMs.

Wenn Sie eine Überwachungslösung in einem Log Analytics-Arbeitsbereich aktivieren, beginnen alle Server, die an diesen Arbeitsbereich berichten, mit dem Sammeln von Daten, die für diese Lösung relevant sind, damit die Lösung Erkenntnisse für alle Server im Arbeitsbereich generieren kann.

Um Telemetriedaten auf einem lokalen Server zu sammeln und per Push in den Log Analytics-Arbeitsbereich zu übertragen, erfordert Azure Monitor die Installation von Microsoft Monitoring Agent (MMA). Bestimmte Überwachungslösungen erfordern zudem einen sekundären Agent. Beispielsweise benötigt Azure Monitor für VMs auch einen ServiceMap-Agent für zusätzliche Funktionalität, die von dieser Lösung bereitgestellt wird.

Einige Lösungen, z. B. die Azure-Updateverwaltung, benötigen auch Azure Automation, damit Ressourcen sowohl in Azure-Umgebungen als auch in Umgebungen außerhalb von Azure zentral verwaltet werden können. Beispielsweise verwendet die Azure-Updateverwaltung Azure Automation, um die Installation von Updates auf allen Computern in Ihrer Umgebung zentral über das Azure-Portal zu planen und zu orchestrieren.

## <a name="what-data-does-azure-monitor-collect"></a>Welche Daten sammelt Azure Monitor?

Alle von Azure Monitor gesammelten Daten gehören einem von zwei Grundtypen an: Metriken und Protokollen.

1. [Metriken](/azure/azure-monitor/platform/data-platform#metrics) sind numerische Werte, die einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Sie sind einfach strukturiert und in der Lage, Szenarien nahezu in Echtzeit zu unterstützen. Die von Azure Monitor gesammelten Daten können direkt auf der Seite **Übersicht** im Azure-Portal angezeigt werden.

    :::image type="content" source="media/monitor/metrics.png" alt-text="Abbildung der Erfassung von Metriken im Metrik-Explorer" border="false":::

2. [Protokolle](/azure/azure-monitor/platform/data-platform#logs) enthalten verschiedene Arten von Daten, die in Datensätzen mit unterschiedlichen Eigenschaften für jeden Typ organisiert sind. Telemetriedaten wie etwa Ereignisse und Ablaufverfolgungen werden als Protokolle zusätzlich zu Leistungsdaten gespeichert, die alle zur Analyse kombiniert werden können. Die in Azure Monitor gesammelten Protokolldaten können mit [Abfragen](/azure/azure-monitor/log-query/log-query-overview) analysiert werden, die die gesammelten Daten schnell abrufen, konsolidieren und analysieren. Sie können Abfragen mit [Log Analytics](/azure/azure-monitor/log-query/portals) im Azure-Portal erstellen und testen und die Daten dann entweder mit diesen Tools direkt analysieren oder Abfragen zur Verwendung mit [Visualisierungen](/azure/azure-monitor/visualizations) oder [Warnungsregeln](/azure/azure-monitor/platform/alerts-overview) speichern.

    :::image type="content" source="media/monitor/logs.png" alt-text="Abbildung der Erfassung von Protokollen in Log Analytics" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>Wie ermöglicht Ihnen Windows Admin Center die Verwendung von Azure Monitor?

Sie können in Windows Admin Center drei Überwachungslösungen aktivieren:

- [Azure Monitor für Cluster](#onboard-your-cluster-using-windows-admin-center)
- [Azure-Updateverwaltung](/windows-server/manage/windows-admin-center/azure/azure-update-management) (im Tool **Updates**)
- Azure Monitor für VMs (in „Servereinstellungen“), auch als „Virtual Machine Insights“ bezeichnet

Sie können über jedes dieser Tools mit der Verwendung von Azure Monitor beginnen. Wenn Sie bisher noch nie Azure Monitor verwendet haben, stellt Windows Admin Center automatisch einen Log Analytics-Arbeitsbereich (und bei Bedarf ein Azure Automation-Konto) bereit und installiert und konfiguriert MMA auf dem Zielserver. Anschließend wird die entsprechende Lösung im Arbeitsbereich installiert.

Wenn Sie beispielsweise zunächst das Tool **Updates** öffnen, um die Azure Updateverwaltung einzurichten, führt Windows Admin Center folgende Aktionen aus:

1. Installieren von MMA auf dem Computer
2. Erstellen des Log Analytics Arbeitsbereichs und des Azure Automation-Kontos (da in diesem Fall ein Azure Automation-Konto erforderlich ist)
3. Installieren der Updateverwaltungslösung im neu erstellten Arbeitsbereich

Wenn Sie in Windows Admin Centers eine weitere Überwachungslösung auf demselben Server hinzufügen möchten, wird diese Lösung einfach im vorhandenen Arbeitsbereich installiert, mit dem der Server verbunden ist. Von Windows Admin Center werden außerdem alle weiteren erforderlichen Agents installiert.

Wenn Sie eine Verbindung mit einem anderen Server herstellen, aber bereits einen Log Analytics Arbeitsbereich eingerichtet haben (entweder über Windows Admin Center oder manuell im Azure-Portal), können Sie MMA auch auf dem Server installieren und ihn mit einem vorhandenen Arbeitsbereich verbinden. Wenn Sie einen Server mit einem Arbeitsbereich verbinden, beginnt er automatisch mit dem Sammeln von Daten und der Berichterstellung in Lösungen, die in diesem Arbeitsbereich installiert sind.

## <a name="azure-monitor-for-virtual-machines-aka-virtual-machine-insights"></a>Azure Monitor für virtuelle Computer (auch als „Virtual Machine Insights“ bezeichnet)

Wenn Sie Azure Monitor für VMs in **Servereinstellungen** einrichten, aktiviert Windows Admin Center die Lösung Azure Monitor für VMs (auch als „Virtual Machine Insights“ bezeichnet). Mit dieser Lösung können Sie die Integrität und Ereignisse des Servers überwachen, E-Mail-Warnungen erstellen, eine konsolidierte Ansicht der Serverleistung in Ihrer Umgebung erhalten und Apps, Systeme und Dienste visualisieren, die mit einem bestimmten Server verbunden sind.

> [!NOTE]
> Obwohl ihr Name „Virtual Machine Insights“ lautet, kann die Lösung sowohl für physische Server als auch für VMs verwendet werden.

Dank des kostenlosen Azure Monitor-Kontingents von 5 GB Daten pro Monat und Kunde können Sie dies problemlos für einen oder zwei Server ausprobieren, ohne dass Kosten für Sie anfallen. Nachfolgend werden weitere Vorteile des Onboardings von Servern in Azure Monitor beschrieben, darunter eine konsolidierte Ansicht der Systemleistung der Server in Ihrer Umgebung.

## <a name="onboard-your-cluster-using-windows-admin-center"></a>Onboarding des Clusters mithilfe von Windows Admin Center

Die einfachste Möglichkeit, um das Onboarding des Clusters in Azure Monitor durchzuführen, ist die Verwendung des automatisierten Workflows in Windows Admin Center. Dieser konfiguriert den Integritätsdienst und Log Analytics und installiert dann MMA.

:::image type="content" source="media/monitor/onboarding.gif" alt-text="Abbildung des Onboardings des Clusters in Azure Monitor":::

Klicken Sie auf der Übersichtsseite einer Serververbindung auf die neue Schaltfläche **Warnungen verwalten**, oder wechseln Sie zu **Servereinstellungen > Überwachung und Warnungen**. Führen Sie auf dieser Seite das Onboarding des Servers in Azure Monitor durch, indem Sie auf **Einrichten** klicken und die Einstellungen im Bereich „Setup“ festlegen. Admin Center übernimmt die Bereitstellung des Azure Log Analytics-Arbeitsbereichs, die Installation des erforderlichen Agents und die Konfiguration der Lösung Virtual Machine Insights. Sobald der Vorgang beendet ist, sendet der Server Leistungsindikatordaten an Azure Monitor, sodass Sie über das Azure-Portal E-Mail-Warnungen auf Grundlage dieses Servers anzeigen und erstellen können.

## <a name="onboard-your-cluster-manually-using-powershell"></a>Manuelles Onboarding des Clusters mithilfe von PowerShell

Wenn Sie das Onboarding des Clusters manuell durchführen möchten, führen Sie die folgenden Schritte aus.

### <a name="configure-health-service"></a>Konfigurieren des Integritätsdiensts

Als Erstes müssen Sie den Cluster konfigurieren. Wie Sie vielleicht wissen, optimiert der [Integritätsdienst](/windows-server/failover-clustering/health-service-overview) die tägliche Überwachung und den Betrieb von Clustern, auf denen Direkte Speicherplätze ausgeführt wird.

Wie bereits erwähnt, sammelt Azure Monitor Protokolle von jedem Knoten, der in Ihrem Cluster ausgeführt wird. Daher müssen wir den Integritätsdienst für das Schreiben in einen Ereigniskanal konfigurieren, der wie folgt lautet:

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

Zum Konfigurieren des Integritätsdiensts führen Sie Folgendes aus:

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

Das obige Cmdlet legt in der Integritätseinstellung fest, dass Ereignisse in den Ereigniskanal *Microsoft-Windows-Health/Operational* geschrieben werden.

### <a name="configure-log-analytics"></a>Konfigurieren von Log Analytics

Nachdem Sie nun die ordnungsgemäße Protokollierung für den Cluster eingerichtet haben, ist der nächste Schritt das ordnungsgemäße Konfigurieren von Log Analytics.

[Azure Log Analytics](/azure/azure-monitor/platform/agent-windows) kann Daten direkt von Ihren physischen oder virtuellen Windows-Computern in Ihrem Rechenzentrum oder einer anderen Cloudumgebung zur detaillierten Analyse und Korrelation in einem einzelnen Repository sammeln.

Informationen zur unterstützten Konfiguration finden Sie in den Abschnitten zu [unterstützten Windows-Betriebssystemen](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) und zur [Netzwerkfirewallkonfiguration](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

#### <a name="login-in-to-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich unter [https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) beim Azure-Portal an.

#### <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Weitere Informationen zu den unten aufgeführten Schritten finden Sie in der [Azure Monitor-Dokumentation](/azure/azure-monitor/learn/quick-collect-windows-computer).

1. Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure portal":::

2. Klicken Sie auf **Erstellen**, und wählen Sie anschließend Optionen für die folgenden Elemente aus:

   * Geben Sie einen Namen für den neuen **Log Analytics-Arbeitsbereich** ein, etwa *DefaultLAWorkspace*.
   * Wählen Sie ein **Abonnement** aus, mit dem eine Verknüpfung erstellt werden soll, indem Sie in der Dropdownliste einen anderen Eintrag auswählen, falls der Standardeintrag nicht geeignet ist.
   * Wählen Sie für **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, die einen oder mehrere virtuelle Azure-Computer enthält.

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="Erstellen des Log Analytics-Ressourcenblatts":::

3. Klicken Sie nach dem Bereitstellen der erforderlichen Informationen im Bereich **Log Analytics-Arbeitsbereich** auf **OK**.

Die Informationen werden überprüft, und der Arbeitsbereich wird erstellt. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

#### <a name="obtain-workspace-id-and-key"></a>Abrufen von Arbeitsbereichs-ID und -Schlüssel
Vor der Installation von MMA für Windows benötigen Sie die Arbeitsbereich-ID und den Arbeitsbereichsschlüssel für Ihren Log Analytics-Arbeitsbereich.  Diese Informationen sind für den Setup-Assistenten erforderlich, um den Agent ordnungsgemäß zu konfigurieren und sicherzustellen, dass er erfolgreich mit Log Analytics kommunizieren kann.

1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den zuvor erstellten *DefaultLAWorkspace*.
3. Wählen Sie **Erweiterte Einstellungen**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Erweiterte Einstellungen für Log Analytics":::
4. Wählen Sie **Verbundene Quellen** und dann **Windows Server** aus.
5. Der Wert rechts von **Arbeitsbereichs-ID** und **Primärschlüssel**. Speichern Sie beide temporär, indem Sie sie kopieren und in Ihren bevorzugten Editor einfügen.

### <a name="installing-the-mma-on-windows"></a>Installieren von MMA unter Windows
Mit den folgenden Schritten wird Microsoft Monitoring Agent installiert und konfiguriert.

> [!IMPORTANT]
> Installieren Sie diesen Agent auf jedem Server im Cluster, und geben Sie an, dass der Agent beim Windows-Start ausgeführt werden soll.

1. Wählen Sie auf der Seite **Windows-Server** die entsprechende Version für **Windows-Agent herunterladen** aus, um in Abhängigkeit von der Prozessorarchitektur des Windows-Betriebssystems herunterzuladen.
2. Führen Sie Setup aus, um den Agent auf Ihrem Computer zu installieren.
3. Klicken Sie auf der Seite **Willkommen**auf **Weiter**.
4. Lesen Sie die Seite **Lizenzbedingungen** durch, und klicken Sie anschließend auf **Ich stimme zu**.
5. Auf der Seite **Zielordner** können Sie den Standardinstallationsordner entweder ändern oder beibehalten. Klicken Sie anschließend auf **Weiter**.
6. Wählen Sie auf der Seite **Agent-Setupoptionen** aus, dass der Agent mit Azure Log Analytics verbunden werden soll, und klicken Sie dann auf **Weiter**.
7. Fügen Sie auf der Seite **Azure Log Analytics** die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** ein, die Sie zuvor kopiert haben. Wenn der Computer über einen Proxyserver mit dem Log Analytics-Dienst kommunizieren muss, klicken Sie auf **Erweitert**, und stellen Sie die URL sowie die Portnummer des Proxyservers bereit. Wenn der Proxyserver eine Authentifizierung erfordert, geben Sie den Benutzernamen und das Kennwort für die Authentifizierung mit dem Proxyserver ein, und klicken Sie dann auf **Weiter**.
8. Klicken Sie auf **Weiter**, nachdem Sie die Bereitstellung der erforderlichen Konfigurationseinstellungen abgeschlossen haben.
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="Arbeitsbereichs-ID und Primärschlüssel einfügen":::
9. Überprüfen Sie Ihre Auswahl auf der Seite **Bereit zum Installieren**, und klicken Sie dann auf **Installieren**.
10. Klicken Sie auf der Seite **Die Konfiguration wurde erfolgreich abgeschlossen** auf **Fertig stellen**.

Nach Abschluss wird der **Microsoft Monitoring Agent** in der **Systemsteuerung** angezeigt. Sie können Ihre Konfiguration überprüfen und sicherstellen, dass der Agent mit Log Analytics verbunden ist. Wenn verbunden, zeigt der Agent auf der Registerkarte **Azure Log Analytics** eine Meldung an: **Der Microsoft Monitoring Agent hat erfolgreich eine Verbindung mit dem Microsoft Log Analytics-Dienst hergestellt.**

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="MMA-Verbindungsstatus mit Log Analytics":::

Informationen zur unterstützten Konfiguration finden Sie in den Abschnitten zu [unterstützten Windows-Betriebssystemen](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) und zur [Netzwerkfirewallkonfiguration](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

## <a name="setting-up-alerts-using-windows-admin-center"></a>Einrichten von Warnungen mithilfe von Windows Admin Center

Nachdem Sie den Server mit Azure Monitor verbunden haben, können Sie die intelligenten Hyperlinks auf der Seite **Einstellungen > Überwachung und Warnungen** verwenden, um zum Azure-Portal zu navigieren. In Windows Admin Center können Sie auf einfache Weise Standardwarnungen konfigurieren, die für alle Server im Log Analytics-Arbeitsbereich gelten. Windows Admin Center aktiviert automatisch die Erfassung von Leistungsindikatoren. Sie können also [eine neue Warnung erstellen](/azure/azure-monitor/platform/alerts-log), indem Sie eine der vielen vordefinierten Abfragen anpassen oder eine eigene Abfrage erstellen.

:::image type="content" source="media/monitor/setup1.gif" alt-text="Screenshot: Konfigurieren von Warnungen":::

Sie können die folgenden Warnungen mit den folgenden Standardbedingungen wählen:

| Name der Warnung                | Standardbedingung                                  |
|---------------------------|----------------------------------------------------|
| CPU-Auslastung           | 10 Minuten lang höher als 85 %                            |
| Datenträger-Kapazitätsauslastung | 10 Minuten lang höher als 85 %                            |
| Arbeitsspeichernutzung        | Verfügbarer Arbeitsspeicher 10 Minuten lang geringer als 100 MB   |
| Heartbeat                 | 5 Minuten lang weniger als 2 Takte                   |
| Schwerwiegender Systemfehler     | Jede kritische Warnung im Cluster-Systemereignisprotokoll |
| Dienstintegritätswarnung      | Jeder Integritätsdienstfehler im Cluster            |

Nachdem Sie die Warnungen in Windows Admin Center konfiguriert haben, können Sie die Warnungen im Log Analytics-Arbeitsbereich in Azure anzeigen.

:::image type="content" source="media/monitor/setup2.gif" alt-text="Screenshot: Anzeigen von Warnungen":::

### <a name="collecting-event-and-performance-data"></a>Sammeln von Ereignis- und Leistungsdaten

Log Analytics kann Ereignisdaten aus den Windows-Ereignisprotokollen und Leistungsindikatoren sammeln, die Sie für längerfristige Analysen und Berichte angeben, und Maßnahmen einleiten, wenn eine bestimmte Bedingung erkannt wird. Führen Sie diese Schritte aus, um die Sammlung von Ereignissen aus dem Ereignisprotokoll von Windows sowie mehreren allgemeinen Leistungsindikatoren zu konfigurieren.

1. Klicken Sie im Azure-Portal unten links auf **Weitere Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Wählen Sie **Erweiterte Einstellungen**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics: Erweiterte Einstellungen":::
3. Wählen Sie **Daten** und dann **Windows-Ereignisprotokolle**.
4. Fügen Sie hier den Integritätsdienst-Ereigniskanal hinzu, indem Sie unten den Namen eingeben und auf das Pluszeichen **+** klicken.
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. Aktivieren Sie in der Tabelle die Schweregrade **Fehler** und **Warnung**.
6. Klicken Sie ganz oben auf der Seite auf **Speichern**, um die Konfiguration zu speichern.
7. Wählen Sie **Windows-Leistungsindikatoren** aus, um die Sammlung von Leistungsindikatoren auf einem Windows-Computer zu aktivieren.
8. Wenn Sie die Windows-Leistungsindikatoren zum ersten Mal für einen neuen Log Analytics-Arbeitsbereich konfigurieren, haben Sie die Möglichkeit, schnell mehrere allgemeine Indikatoren zu erstellen. Diese werden in einer Liste aufgeführt, und neben jedem Indikator finden Sie ein Kontrollkästchen.
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="Standardmäßige Windows-Leistungsindikatoren ausgewählt":::
    Klicken Sie auf **Ausgewählte Leistungsindikatoren hinzufügen**.  Sie werden hinzugefügt und mit einem Stichprobenintervall von zehn Sekunden voreingestellt.
9. Klicken Sie ganz oben auf der Seite auf **Speichern**, um die Konfiguration zu speichern.

## <a name="create-queries-and-alerts-based-on-log-data"></a>Erstellen von Abfragen und Warnungen basierend auf Protokolldaten

Wenn Sie die oben beschriebenen Schritte durchgeführt haben, sollte der Cluster die Protokolle und Leistungsindikatoren an Log Analytics senden. Im nächsten Schritt werden Warnungsregeln erstellt, für die in regelmäßigen Abständen automatisch Protokollsuchen durchgeführt werden. Wenn die Ergebnisse der Protokollsuche bestimmte Kriterien erfüllen, wird eine Warnung ausgelöst, durch die eine Benachrichtigung per E-Mail oder SMS gesendet wird. Sehen wir uns dies in den folgenden Abschnitten an.

### <a name="create-a-query"></a>Erstellen einer Abfrage

Öffnen Sie zunächst das Portal für die **Protokollsuche**.

1. Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Monitor** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Monitor** aus.
2. Wählen Sie **Log Analytics** im Navigationsmenü **Monitor** und dann einen Arbeitsbereich aus.

Die schnellste Methode, Daten für die weitere Verwendung abzurufen, besteht in einer einfachen Abfrage, die alle Datensätze aus der Tabelle zurückgibt. Geben Sie die folgenden Abfragen im Suchfeld ein, und klicken Sie auf die Schaltfläche „Suchen“.

```
Event
```

Die Daten werden in der Standardlistenansicht zurückgegeben. Sie können ablesen, wie viele Datensätze insgesamt zurückgegeben wurden.

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="Screenshot einer einfachen Abfrage":::

Auf der linken Seite des Bildschirms befindet sich der Filterbereich, über den Sie Filter zu Abfragen hinzufügen können, ohne die Abfrage direkt zu verändern.  Mehrere Datensatzeigenschaften werden für diesen Datensatztyp angezeigt, und Sie können einen oder mehrere Eigenschaftenwerte auswählen, um Ihre Suchergebnisse einzugrenzen.

Aktivieren Sie das Kontrollkästchen neben **Fehler** unter **EVENTLEVELNAME**, oder geben Sie Folgendes ein, um die Ergebnisse auf Fehlerereignisse zu beschränken.

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="Screenshot von Filter":::

Nachdem Sie die entsprechenden Abfragen für relevante Ereignisse durchgeführt haben, speichern Sie sie für den nächsten Schritt.

### <a name="create-alerts"></a>Erstellen von Warnungen
Sehen wir uns nun ein Beispiel für das Erstellen einer Warnung an.

1. Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Klicken Sie im linken Bereich auf **Warnungen** und dann oben auf der Seite auf **Neue Warnungsregel**, um eine neue Warnung zu erstellen.
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="Screenshot: Erstellen einer neuen Warnungsregel":::
3. Im ersten Schritt wählen Sie im Abschnitt **Warnung erstellen** Ihren Log Analytics-Arbeitsbereich als Ressource aus, da es sich dabei um ein protokollbasiertes Warnungssignal handelt.  Wenn Sie mehrere Abonnements besitzen, filtern Sie die Ergebnisse, indem Sie in der Dropdownliste das gewünschte **Abonnement** auswählen, das den zuvor erstellten Log Analytics-Arbeitsbereich enthält.  Filtern Sie den **Ressourcentyp**, indem Sie in der Dropdownliste **Log Analytics** auswählen.  Wählen Sie abschließend die **Ressource** mit dem Namen **DefaultLAWorkspace** aus, und klicken Sie dann auf **Fertig**.
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="Screenshot: Erstellen einer neuen Warnungsregel, Schritt 1":::
4. Klicken Sie im Abschnitt **Warnungskriterien** auf **Kriterien hinzufügen**, um die gespeicherte Abfrage auszuwählen, und geben Sie dann eine Logik für die Warnungsregel an.
5. Konfigurieren Sie die Warnung mit den folgenden Informationen: a. Wählen Sie in der Dropdownliste **Basierend auf** die Option **Metrische Maßeinheit** aus.  Mit „Metrische Maßeinheit“ wird eine Warnung für jedes Objekt in der Abfrage mit einem Wert erzeugt, der den angegebenen Schwellenwert überschreitet.
   b. Wählen Sie für **Bedingung** die Option **Größer als** aus, und geben Sie einen Schwellenwert an.
   c. Legen Sie dann fest, wann die Warnung ausgelöst werden soll. Sie können beispielsweise **Aufeinanderfolgende Sicherheitsverletzungen** und in der Dropdownliste **Größer als** den Wert 3 auswählen.
   d. Ändern Sie unter „Evaluation based on“ (Auswertung basierend auf) den Wert für **Zeitraum** in **30** Minuten und den Wert für **Häufigkeit** in 5. Die Regel wird alle fünf Minuten ausgeführt und gibt Datensätze zurück, die innerhalb der letzten 30 Minuten erstellt wurden.  Durch die Verwendung eines weiter gefassten Zeitfensters wird potenziellen Datenlatenzen Rechnung getragen und sichergestellt, dass die Abfrage Daten zurückgibt, um ein falsch negatives Ergebnis zu vermeiden, bei dem die Warnung nie ausgelöst wird.
6. Klicken Sie auf **Fertig**, um die Warnungsregel fertig zu stellen.
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="Screenshot: Konfigurieren eines Warnungssignals":::
7. Geben Sie nun im zweiten Schritt im Feld **Name der Warnungsregel** einen Namen für die Warnung ein, etwa **Warnung bei allen Fehlerereignissen**.  Geben Sie eine **Beschreibung** mit Details zu Ihrer Warnung ein, und wählen Sie für **Schweregrad** aus den angegebenen Optionen **Kritisch (Schweregrad 0)** aus.
8. Um die Warnungsregel direkt bei der Erstellung zu aktivieren, übernehmen Sie den Standardwert für **Regel beim Erstellen aktivieren**.
9. Im dritten und letzten Schritt geben Sie eine **Aktionsgruppe** an. Dadurch wird sichergestellt, dass bei jeder Warnungsauslösung die gleichen Aktionen ausgeführt werden und diese für jede definierte Regel verwendet werden können. Konfigurieren Sie eine neue Aktionsgruppe mit den folgenden Informationen: a. Klicken Sie auf **Neue Aktionsgruppe**, und der Bereich **Aktionsgruppe hinzufügen** wird angezeigt.
   b. Geben Sie unter **Name der Aktionsgruppe** einen Namen wie **IT-Vorgänge – Benachrichtigen** und einen **Kurznamen** wie **itops-n** ein.
   c. Überprüfen Sie, ob die Standardwerte für **Abonnement** und **Ressourcengruppe** richtig sind. Ist dies nicht der Fall, wählen Sie die korrekten Werte in der Dropdownliste aus.
   d. Geben Sie im Bereich „Aktionen“ einen Namen für die Aktion ein, beispielsweise **E-Mail senden**, und wählen Sie unter **Aktionstyp** in der Dropdownliste **E-Mail/SMS/Push/Sprachanruf** aus. Der Eigenschaftenbereich **E-Mail/SMS/Push/Sprachanruf** wird mit weiteren Informationen auf der rechten Seite geöffnet.
   e. Wählen Sie im Bereich **E-Mail/SMS/Push/Sprachanruf** die bevorzugte Einstellung aus, und richten Sie sie ein. Aktivieren Sie beispielsweise **E-Mail**, und geben Sie eine gültige SMTP-E-Mail-Adresse ein, an die die Nachricht gesendet werden soll.
   f. Klicken Sie auf **OK** , um die Änderungen zu speichern.<br><br>

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="Screenshot: Erstellen der neuen Aktionsgruppe":::

10. Klicken Sie auf **OK**, um die Aktionsgruppe zu erstellen.
11. Klicken Sie auf **Warnungsregel erstellen**, um die Warnungsregel fertig zu stellen. Die Ausführung beginnt sofort.
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="Screenshot: Abschließen der Erstellung der neuen Warnungsregel":::

### <a name="example-alert"></a>Beispiel für Warnung

Hier ist ein Beispiel für eine Warnung in Azure dargestellt.

:::image type="content" source="media/monitor/alert.gif" alt-text="Screenshot: Warnung in Azure":::

Unten ist ein Beispiel für die E-Mail dargestellt, die von Azure Monitor gesendet wird:

:::image type="content" source="media/monitor/warning.png" alt-text="Screenshot: Beispiel für eine Warnungs-E-Mail":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>Erstellen benutzerdefinierter Kusto-Abfragen in Log Analytics

Sie können auch mit der Kusto-Abfragesprache [benutzerdefinierte Protokollabfragen](/azure/azure-monitor/log-query/get-started-queries) in Azure Monitor schreiben, um Daten von einer oder mehreren VMs zu sammeln.

## <a name="get-a-consolidated-view-across-multiple-servers"></a>Erhalten einer konsolidierten Ansicht über mehrere Server hinweg

Wenn Sie das Onboarding mehrerer Server in einen einzelnen Log Analytics-Arbeitsbereich durchführen, können Sie über die Lösung [Virtual Machine Insights](/azure/azure-monitor/insights/vminsights-overview) in Azure Monitor eine konsolidierte Ansicht aller dieser Server erhalten. (Beachten Sie, dass die Registerkarten „Leistung“ und „Zuordnungen“ von Virtual Machine Insights für Azure Monitor für lokale Server verwendet werden können – die Registerkarte „Integrität“ kann nur für Azure-VMs verwendet werden.) Um dies im Azure-Portal anzuzeigen, wechseln Sie zu **Azure Monitor > Virtuelle Computer** (unter „Erkenntnisse“), und navigieren Sie zur Registerkarte **Leistung** oder **Zuordnungen**.

## <a name="visualize-connected-services"></a>Visualisieren verbundener Dienste

Wenn Windows Admin Center das Onboarding eines Servers in die Lösung Virtual Machine Insights in Azure Monitor durchführt, wird auch eine Funktion mit dem Namen [Dienstzuordnung](/azure/azure-monitor/insights/service-map) angezeigt. Diese Funktion ermittelt automatisch Anwendungskomponenten und ordnet die Kommunikation zwischen Diensten zu, sodass Sie problemlos über das Azure-Portal Verbindungen zwischen Servern detailliert visualisieren können. Sie finden diese Funktion, indem Sie im Azure-Portal **Azure Monitor > Virtuelle Computer** (unter „Erkenntnisse“) auswählen und zur Registerkarte **Zuordnungen** navigieren.

> [!NOTE]
> Die Visualisierungen für Virtual Machine Insights für Azure Monitor werden zurzeit in sechs öffentlichen Regionen angeboten.  Aktuelle Informationen finden Sie in der [Dokumentation zu Azure Monitor für VMs](/azure/azure-monitor/insights/vminsights-onboard#log-analytics). Sie müssen den Log Analytics-Arbeitsbereich in einer der unterstützten Regionen bereitstellen, um die zusätzlichen Vorteile der oben beschriebenen Lösung Virtual Machine Insights nutzen zu können.

## <a name="disabling-monitoring"></a>Deaktivieren der Überwachung

Deinstallieren Sie MMA, um den Server vollständig vom Log Analytics-Arbeitsbereich zu trennen. Dies bewirkt, dass dieser Server keine Daten mehr an den Arbeitsbereich sendet und alle in diesem Arbeitsbereich installierten Lösungen keine Daten mehr von diesem Server sammeln und verarbeiten. Dies wirkt sich jedoch nicht auf den Arbeitsbereich selbst aus. Alle Ressourcen, die an diesen Arbeitsbereich berichten, berichten weiterhin an diesen. Wenn Sie MMA in WAC deinstallieren möchten, wechseln Sie zu **Apps & Features**, suchen Sie **Microsoft Monitoring Agent**, und klicken Sie auf **Deinstallieren**.

Wenn Sie eine bestimmte Lösung in einem Arbeitsbereich deaktivieren möchten, müssen Sie [die Überwachungslösung aus dem Azure-Portal entfernen](/azure/azure-monitor/insights/solutions#remove-a-management-solution). Das Entfernen einer Überwachungslösung bedeutet, dass die von dieser Lösung erstellten Erkenntnisse für _keinen_ der Server mehr generiert werden, die an diesen Arbeitsbereich berichten. Wenn Sie z. B. die Lösung Azure Monitor für VMs deinstallieren, werden keine Erkenntnisse mehr zur VM- oder Serverleistung von den Computern angezeigt, die mit Ihrem Arbeitsbereich verbunden sind.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Themen:

- [Weitere Informationen zur Azure-Integration in Windows Admin Center](/windows-server/manage/windows-admin-center/azure/)
- [Verwenden von Azure Monitor zum Senden von E-Mails über Integritätsdienstfehler](/windows-server/storage/storage-spaces/configure-azure-monitor)
