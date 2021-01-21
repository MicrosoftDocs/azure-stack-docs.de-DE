---
title: Verwalten von IoT Hub in Azure Stack Hub
description: IoT Hub-Verwaltungsfunktion und Warnungen
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 3fe1459d28c399269ccdb1d4cffed024bd741000
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255740"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>Verwalten von IoT Hub in Azure Stack Hub

Mit der IoT Hub-Verwaltungsfunktion können Sie den Gesamtstatus, Warnungen und die Kapazität visualisieren und verwalten.

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>IoT Hub-Verwaltungsdashboard

So greifen Sie auf das IoT Hub-Verwaltungsdashboard zu:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an, wählen Sie links die Ansicht **Dashboard** und dann den Ressourcenanbieter **IoT Hub** aus:

   [![Operatordashboard](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. Anschließend zeigt das IoT Hub-Dashboard eine Zusammenfassungsansicht an, auf der Sie die aktuellen Warnungen, die für den Stempel erstellten Kontingente sowie die Gesamtanzahl der IoT Hub-Cluster in Ihrem Abonnement: 

   [![IoT Hub-Dashboard: Übersicht](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>Alerts

Der IoT Hub-Ressourcenanbieter unterstützt die folgenden Warnungen:

|Category|Warnung|type|BESCHREIBUNG|
|-|-|-|-|
|Leistung|IoT Hub CPU usage needs attention. (Die IoT Hub-CPU-Auslastung erfordert Aufmerksamkeit.)|Warnung|Die durchschnittliche prozentuale CPU-Auslastung des IoT Hub-Clusters lag in den letzten 6 Stunden über 75 %.|
|Leistung|IoT Hub memory usage needs attention. (Die IoT Hub-Arbeitsspeicherauslastung erfordert Aufmerksamkeit.)|Warnung|Die verbleibende Arbeitsspeicherauslastung des IoT Hub-Ressourcenanbieters in den letzten 6 Stunden lag unter 1.024 MB.|
|Leistung|Low disk space for IoT Hub resource provider. (Wenig Speicherplatz für IoT Hub-Ressourcenanbieter.)|Warnung|Der verbleibende Speicherplatz auf dem Datenträger liegt unter 10 %.|
|Resource|Creating or updating IoT Hub resource failed. (Erstellung oder Update einer IoT Hub-Ressource ist fehlgeschlagen.)|Warnung|Die IoT Hub-Fehleranzahl für Erstellungen oder Updates von IoT Hub-Ressourcenanbietern liegt innerhalb von 15 Minuten nicht unter 1.|
|Dienst|IoT Hub resource provider log errors needs attention. (Protokollfehler des IoT Hub-Ressourcenanbieters erfordern Aufmerksamkeit.)|Warnung|Die Anzahl der Protokollfehler des IoT Hub-Ressourcenanbieters liegt innerhalb von 15 Minuten über 3.|
|Dienst|IoTHub-SU-InternalError|Warnung|Die Anzahl interner Fehler und Timeout der IoT Hub-Speichereinheit lag in den letzten 30 Minuten nicht unter 5.|
|Dienst|IoT Hub data plane backup failure occurred. (Es ist ein Fehler bei der Sicherung der IoT Hub-Datenebene aufgetreten.)|Warnung|In den letzten 15 Minuten sind Sicherungsfehler bei der IoT Hub-Datenebene aufgetreten. Gerätedaten werden nicht geschützt.|
|Dienst|IoT Hub data plane consecutive backup failure has occurred. (Es sind aufeinanderfolgende Sicherungsfehler bei der IoT Hub-Datenebene aufgetreten.)|Warnung|In den letzten 15 Minuten sind aufeinanderfolgende Sicherungsfehler bei der IoT Hub-Datenebene aufgetreten. Gerätedaten werden nicht geschützt.|
|Dienst|IoT Hub data plane no success backup failure occurred. (Es ist keine erfolgreiche Sicherung der IoT Hub-Datenebene aufgetreten.)|Warnung|In den letzten 3 Stunden wurde keine erfolgreiche Sicherung von IoT Hub durchgeführt. Ihre Gerätedaten werden möglicherweise nicht geschützt.|
|Dienst|IoT Hub data plane restore failed. (Die Wiederherstellung der IoT Hub-Datenebene ist fehlgeschlagen.)|Warnung|Die Wiederherstellung der IoT Hub-Geräteinformationen ist fehlgeschlagen. Der Wert von IotHubPerformanceMetrics hat den Schwellenwert in den letzten 15 Minuten überschritten.|
|Dienst|IoT Hub gateway failure occurred. (Ein IoT Hub-Gatewayfehler ist aufgetreten.)|Warnung|Ein IoT Hub-Gatewayfehler ist aufgetreten. Die Gerätetelemetriefunktionen sind möglicherweise beeinträchtigt.|
|Dienst|IoT Hub Container failure occurred. (Ein IoT Hub-Containerfehler ist aufgetreten.)|Warnung|Ein IoT Hub-Containerfehler ist aufgetreten. Daher kann die Geräteauthentifizierung fehlschlagen. |
|Dienst|IoT Hub device management container failure occurred. (Es ist ein Fehler beim IoT Hub-Geräteverwaltungscontainer aufgetreten.)|Warnung|Es ist ein Fehler beim IoT Hub-Geräteverwaltungscontainer aufgetreten. Dies kann die Funktionen für Gerätezwillinge und direkte Methoden beeinträchtigen.|

So überwachen Sie Warnungen und Kontingente:

1. Klicken Sie auf **Warnungen**, um den Warnungsverlauf anzuzeigen: 
 
   [![IoT Hub-Dashboard: Warnungen](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. Klicken Sie auf **Kontingente**, um die Liste der aktiven Kontingente anzuzeigen:  

   > [!NOTE]
   > Während der Vorschau ist das Feature **Erstellen** deaktiviert und unbegrenzter Standardkontingent wird bereitgestellt. Das Feature **Erstellen** wird in der allgemeinen Verfügbarkeit verfügbar sein.

   [![IoT Hub-Dashboard: Kontingente](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>Kapazitätsmanagement

Als Operator sind Sie wie jeder Cloudoperator für die Verwaltung und den Betrieb Ihrer Azure Stack Hub-Instanz verantwortlich. Sie stellen sicher, dass Software ordnungsgemäß installiert wird, ordnungsgemäß und sicher konfiguriert wird sowie kohärent und effizient für Hochverfügbarkeit betrieben wird. Sie müssen Kapazitätsverwaltungsprinzipien für den Betrieb der Azure Stack Hub-Instanz und des IoT Hub-Ressourcenanbieters anwenden.

### <a name="azure-stack-hub-capacity-management"></a>Azure Stack Hub-Kapazitätsverwaltung

Sie müssen die Workload einschätzen (hauptsächlich die Anzahl der Geräte und den Nachrichtendurchsatz), um die erforderliche Kapazität für IoT Hub zu bestimmen. Zur Unterstützung bei der Planung wurden die folgenden Tests als Referenz an einer Azure Stack Hub-Umgebung mit vier Knoten durchgeführt:

| Szenario | VMs | Virtuelle Kerne | Geräte/Hub | [Hub-Edition](https://azure.microsoft.com/pricing/details/iot-hub) | Hubs | Einheiten/Hub | Total devices (Geräte gesamt) | Hub-Einheiten gesamt | Nachrichten/Tag (in Mio.) |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|Standard|5|20|300.000|S2|4|200|1\.200.000|800|4\.800|
|12 VMs|12|48|500.000|S2|4|200|2\.000.000|800|4\.800|
|18 VMs|18|72|400.000|S3|4|10|1\.600.000|40|12.000|

Weitere Informationen finden Sie unter [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md).

### <a name="iot-hub-capacity-management"></a>IoT Hub-Kapazitätsverwaltung

Da Azure Stack Hub in einem lokalen Rechenzentrum mit begrenzten Ressourcen bereitgestellt wird, teilen sich alle Dienste, die in Azure Stack Hub ausgeführt werden, denselben Ressourcenpool. Operatoren müssen die Kapazität entsprechend der geschäftlichen Anforderungen planen und verwalten. Der IoT Hub-Ressourcenanbieter ermöglicht Operatoren die Verwaltung der Kapazitätsanforderungen für den Dienst.

IoT Hub verfügt über einen einzigen VM-Typ. Im Rahmen einer IoT Hub-Bereitstellung stellt das System eine Gruppe dieser VMs in Azure Stack Hub bereit. Die VMs können eine gewisse Anzahl von Geräten und den Nachrichtendurchsatz unterstützen. Die Standardeinstellung sollte die meisten Anforderungen erfüllen. Wenn Sie jedoch mehr Geräte oder einen höheren Nachrichtendurchsatz benötigen, können Sie die Kapazität mithilfe des Administratorportals, der CLI oder mit PowerShell erhöhen. 

So überwachen und ändern Sie die Kapazitätseinstellungen:

1. Wählen Sie links die Ansicht **Kapazität** aus. Daraufhin wird Ihnen der Kapazitätsstatus, einschließlich der Anzahl in einem IoT Hub-Cluster bereitgestellter VMs und deren Ressourcenverwendung angezeigt. Die angezeigte Anzahl der Knoten entspricht der aktuellen Anzahl von Knoten, die IoT Hub zugeordnet sind. 

2. Zum erhöhen der Kapazität wählen Sie den Namen des IoT Hub-Clusters aus, ändern die Anzahl der Knoten und klicken dann auf **Update Scale** (Skalierung aktualisieren). Sie können die Kapazität erhöhen, indem Sie so viele VMs hinzufügen, wie es die verfügbaren Ressourcen zulassen.

   > [!IMPORTANT]
   > In der Vorschauphase wird nur die horizontale Hochskalierung (kleiner zu größer) von IoT Hub-Clustern unterstützt. Die horizontale Herunterskalierung (größer zu kleiner) wird in der allgemein verfügbaren Version von IoT Hub unterstützt.

   [![IoT Hub-Dashboard: Kapazität](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

Informationen zu den Azure Stack Hub-Überwachungsfunktionalitäten, einschließlich Warnungen, finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack Hub](azure-stack-monitor-health.md).

Informationen zum Erfassen von Azure Stack Hub-Protokollen finden Sie unter [Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen](./diagnostic-log-collection.md).