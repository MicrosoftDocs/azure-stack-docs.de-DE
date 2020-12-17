---
title: Verwalten von Event Hubs in Azure Stack Hub
description: Erfahren Sie, wie Sie den Event Hubs-Ressourcenanbieter in Azure Stack Hub verwalten.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: aaadabe207146f200d6090c2e59bcc563831c6d4
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343585"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>Verwalten von Event Hubs in Azure Stack Hub

Mit der Umgebung zur Event Hubs-Verwaltung können Sie den Dienst steuern und dessen Status und Warnungen visualisieren. 

## <a name="overview"></a>Übersicht

Führen Sie die folgenden Schritte aus, um auf die Event Hubs-Verwaltungsseite zuzugreifen:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
2. Wählen Sie im linken Bereich die Option **Alle Dienste** aus.
3. Suchen Sie nach „Event Hubs“, und wählen Sie den Dienst aus. Wenn Sie den Event Hubs-Dienst nicht finden können, muss der Ressourcenanbieter zunächst [installiert](event-hubs-rp-install.md) werden.
4. Die Übersichtsseite für die Event Hubs-Verwaltung wird angezeigt. Der linke Bereich enthält Sie vier Abschnitte:
   - **Übersicht**: Bietet eine allgemeine Ansicht und Zugriff auf bestimmte Verwaltungsbereiche.
   - **Benachrichtigungen** (Alerts): Zeigt alle Benachrichtigungen des Typs „Kritisch“ oder „Warnung“ für Event Hubs an. Ausführliche Informationen finden Sie im Abschnitt [Alerts](#alerts).
   - **Kontingente**: Ermöglicht das Erstellen, Aktualisieren und Löschen von Kontingenten. Ausführliche Informationen finden Sie im Abschnitt [Kontingente](#quotas).
   - **Event Hubs-Cluster**: Zeigt eine Liste aller konfigurierten Cluster an. Ausführliche Informationen finden Sie im Abschnitt [Event Hubs-Cluster](#event-hubs-clusters).

   [![Event Hubs verwalten](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>Kontingente

Wenn Sie **Kontingente** auf der Hauptseite auswählen, wird die Liste der verwendeten Kontingente angezeigt, einschließlich der zugehörigen Pläne, die die Kontingente angeben. 
 
[![Event Hubs verwalten – Kontingente](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

Weitere Informationen zu Kontingenttypen, die für Event Hubs definiert sind, finden Sie unter [Kontingenttypen](azure-stack-quota-types.md#event-hubs-quota-types).

## <a name="alerts"></a>Alerts

Der Event Hubs-Ressourcenanbieter unterstützt die folgenden Benachrichtigungen (Warnungen, Alerts):
   
| Category | Warnung | type | Bedingung |
|----------|-------|------|-----------|
| Leistung | | | |
| | EventHub-CpuUsage | Warnung | Die durchschnittliche prozentuale CPU-Auslastung des Event-Hubs-Clusters war in den letzten 6 Stunden größer als 50 %. |
| | EventHub-MemoryUsage | Warnung | Die durchschnittliche prozentuale Datenträgerauslastung (Auslastung von E:) des Event-Hubs-Clusters war in den letzten 6 Stunden größer als 50 %. |
| | EventHub-DiskUsage | Warnung | Die durchschnittliche Prozentwert für freien Speicherplatz des Event-Hubs-Clusters war in den letzten 6 Stunden kleiner als 50 %. |
| Auslastung/Kontingent | | | |
| | EventHub-QuotaExceeded | Warnung | In den letzten sechs Stunden ist ein „Kontingent überschritten“-Fehler aufgetreten. |
| | EventHub-NamespaceCreditUsage | Warnung | Die Summe der Namespace-Guthabennutzungen in den letzten sechs Stunden ist größer als 10000,0. |
| Dienst beeinträchtigt | | | |
| | EventHub-InternalServerError | Warnung | In den letzten sechs Stunden ist ein interner Serverfehler aufgetreten. |
| | EventHub-ServerBusy | Warnung | In den letzten sechs Stunden ist ein „Server ausgelastet“-Fehler aufgetreten. |
| Client | | | |
| | EventHub-ClientError | Warnung | In den letzten sechs Stunden ist ein Clientfehler aufgetreten. |
| Resource | | | |
| | EventHub-PendingDeletingResources | Warnung | Die Summe der ausstehenden zu löschenden Ressourcen in den letzten sechs Stunden ist größer als 100. |
| | EventHub-ProvisioningQueueLength | Warnung | Die durchschnittliche Länge der Bereitstellungswarteschlange in den letzten sechs Stunden ist größer als 30. |

Wenn Sie **Benachrichtigungen** auf der Hauptseite auswählen, wird die Liste der ausgegebenen Warnungen angezeigt:

[![Event Hubs verwalten: Zusammenfassung der Benachrichtigungen (Alerts)](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

Wenn Sie in der Liste eine Benachrichtigung auswählen, wird der Bereich **Warnungsdetails** auf der rechten Seite angezeigt:

[![Event Hubs verwalten: Warnungsdetails](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

Weitere Informationen zur Azure Stack Hub-Überwachungsfunktionalität, einschließlich Warnungen, finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack Hub](azure-stack-monitor-health.md). Ausführliche Informationen zum Erfassen von Protokollen finden Sie unter [Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview.md).

## <a name="event-hubs-clusters"></a>Event Hubs-Cluster

Wenn Sie **Event Hubs-Cluster** auf der Hauptseite auswählen, wird eine Liste der verfügbaren Benutzercluster angezeigt. Diese Liste enthält die folgenden Informationen für jeden Cluster:

- Grundsätzliche Konfigurationsinformationen
- Dienstintegrität
- Sicherungsstatus

[![Event Hubs verwalten: Benutzerressourcen](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

Wenn Sie einen Link unter **Integrität** oder **Sicherung** auswählen, werden ausführliche Informationen zum Event Hubs-Integritäts- bzw. -Sicherungsstatus angezeigt. Über den Link unter **Name** werden weitere Details zu dem Cluster angezeigt, einschließlich:
- Status- und Konfigurationsinformationen
- Eine Liste der Dienstgrenzwerte für den Cluster

[![Event Hubs verwalten: Benutzercluster](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

Dienstgrenzwerte sind Konfigurationsparameter, die die Betriebsgrenzen von Event Hubs definieren. Die verfügbaren Dienstgrenzwerte ähneln denen, die für Azure Event Hubs Dedicated angeboten werden. Durch Auswählen eines Links unter **Konfigurationswert** können Sie den zugewiesenen Wert ändern.

> [!IMPORTANT]
> Sie sollten die vollständigen Auswirkungen sorgfältig analysieren, bevor Sie die Dienstgrenzwerte ändern. Änderungen an Dienstgrenzwerten können sich auf das Verhalten Ihrer Lösung auswirken, die Ereignisse nutzt und erzeugt. Änderungen können sich auch auf den Ressourcenverbrauch für ihre Azure Stack-Kapazität auswirken.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

- Informationen zu den Kontingenttypen, die für Event Hubs definiert sind, finden Sie unter [Kontingenttypen](azure-stack-quota-types.md#event-hubs-quota-types).
- Informationen zu den Azure Stack Hub-Überwachungsfunktionalitäten, einschließlich Warnungen, finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack Hub](azure-stack-monitor-health.md). 
- Informationen zum Erfassen von Azure Stack Hub-Protokollen finden Sie unter [Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview.md).













