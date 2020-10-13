---
title: Diagnoseprotokollsammlung in Azure Stack Hub
description: Erfahren Sie mehr über die Diagnoseprotokollsammlung in Azure Stack Hub – Hilfe und Support.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 88f2b267f493c05fdcd8a5419718d08d5b6efe37
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778261"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Diagnoseprotokollsammlung in Azure Stack Hub

Bei Azure Stack Hub handelt es sich um eine Sammlung von Windows-Komponenten und lokalen Azure-Diensten, die miteinander interagieren. Von allen diesen Komponenten und Diensten werden jeweils eigene Protokolle generiert. Da diese Protokolle vom Microsoft-Support verwendet werden, um Ihre Probleme effizient zu identifizieren und zu beheben, bieten wir eine Diagnoseprotokollsammlung an. Die Diagnoseprotokollsammlung ermöglicht die schnelle Sammlung von Diagnoseprotokollen und die Weitergabe an den Microsoft-Support über eine einfache Benutzeroberfläche ohne PowerShell. Protokolle werden auch gesammelt, wenn andere Infrastrukturdienste ausgefallen sind.  

Es empfiehlt sich, diesen Ansatz für die Protokollsammlung zu verwenden und nur dann [auf den privilegierten Endpunkt (PEP) zurückzugreifen](azure-stack-get-azurestacklog.md), wenn das Administratorportal oder das Blatt „Hilfe und Support“ nicht verfügbar ist. 

>[!NOTE]
>Azure Stack Hub muss registriert sein, um die Diagnoseprotokollsammlung verwenden zu können. Sollte Azure Stack Hub nicht registriert sein, [verwenden Sie den privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) für die Weitergabe von Protokollen. 

## <a name="collection-options-and-data-handling"></a>Sammlungsoptionen und Datenverarbeitung

::: moniker range=">= azs-2005"

Azure Stack Hub verfügt (abhängig von der Konnektivität mit Azure) über geeignete Möglichkeiten, um Diagnoseprotokolle zu erfassen, zu speichern und an CSS zu senden. Wenn Azure Stack Hub eine Verbindung mit Azure herstellen kann, empfiehlt es sich, die **proaktive Protokollsammlung** zu aktivieren. Dadurch werden automatisch Diagnoseprotokolle in ein von Microsoft gesteuertes Speicherblob in Azure hochgeladen, wenn eine kritische Warnung ausgelöst wird. Alternativ können Sie Protokolle mithilfe der Option zum **sofortigen Senden von Protokollen** bei Bedarf erfassen oder Protokolle lokal speichern, wenn keine Verbindung zwischen Azure Stack Hub und Azure besteht. 

In den folgenden Abschnitten werden die einzelnen Optionen sowie die jeweilige Verarbeitung der Daten erläutert. 

::: moniker-end

Das Feature für Diagnoseprotokollsammlung bietet zwei Möglichkeiten zum Senden von Protokollen:
* Proaktives Senden von Protokollen
* Sofortiges Senden von Protokollen

Sie können Protokolle auch lokal speichern.

Das folgende Diagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss: 

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

In den folgenden Abschnitten werden die einzelnen Optionen sowie die jeweilige Verarbeitung der Daten erläutert. 

### <a name="send-logs-proactively"></a>Proaktives Senden von Protokollen

Bei der proaktiven Protokollsammlung werden Diagnoseprotokolle automatisch gesammelt und von Azure Stack Hub an Microsoft gesendet, bevor Sie eine Supportanfrage erstellen. Diese Protokolle werden nur gesammelt, wenn eine [Systemintegritätswarnung](#proactive-diagnostic-log-collection-alerts) ausgelöst wird, und der Microsoft-Support greift nur im Kontext einer Supportanfrage auf diese Protokolle zu.

Die proaktive Protokollsammlung kann jederzeit deaktiviert und erneut aktiviert werden. Gehen Sie zum Einrichten der proaktiven Protokollsammlung wie folgt vor:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
1. Öffnen Sie **Hilfe und Support – Übersicht**.
1. Wenn das Banner angezeigt wird, wählen Sie **Enable proactive log collection** (Proaktive Protokollsammlung aktivieren) aus. Sie können auch **Einstellungen** auswählen, **Proactive log collection** (Proaktive Protokollsammlung) auf **Aktivieren** festlegen und anschließend **Speichern** auswählen.

>[!NOTE]
>Falls für eine lokale Dateifreigabe Protokollspeicherorteinstellungen konfiguriert sind, stellen Sie sicher, dass Lebenszyklusverwaltungsrichtlinien vorhanden sind, die das Erreichen des Größenkontingents für den Freigabespeicher verhindern. Die lokale Dateifreigabe wird von Azure Stack Hub nicht überwacht, und von Azure Stack Hub werden keine Aufbewahrungsrichtlinien erzwungen.   

### <a name="send-logs-now"></a>Sofortiges Senden von Protokollen

> [!TIP]
> Sparen Sie Zeit, indem Sie anstelle des sofortigen Sendens von Protokollen die [proaktive Protokollsammlung](#send-logs-proactively) verwenden.

Das sofortige Senden von Protokollen ist nun eine Option, bei der Sie Ihre Diagnoseprotokolle von Azure Stack Hub manuell sammeln und hochladen (üblicherweise vor dem Erstellen einer Supportanfrage).

Sie können Diagnoseprotokolle manuell über das Administratorportal oder per PowerShell an den Microsoft-Support senden. Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt sich die Verwendung des Administratorportals, da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Sollte das Portal nicht verfügbar sein, senden Sie die Protokolle stattdessen per PowerShell.

Gehen Sie zum sofortigen Senden der Protokolle wie folgt vor:

1. Öffnen Sie **Help + support > Log Collection > Send logs now** („Hilfe und Support“ > „Protokollsammlung“ > „Protokolle jetzt senden“). 
1. Geben Sie die Start- und Endzeit für die Protokollsammlung an. 
1. Wählen Sie die lokale Zeitzone aus.
1. Wählen Sie **Collect and Upload** (Sammeln und hochladen) aus.

Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. 

::: moniker range=">= azs-2005"

### <a name="save-logs-locally"></a>Lokales Speichern von Protokollen

Sie können Protokolle in einer lokalen SMB-Freigabe (Server Message Block) speichern, wenn Azure Stack Hub nicht mit Azure verbunden ist. Geben Sie auf dem Blatt **Einstellungen** den Pfad sowie einen Benutzernamen und ein Kennwort mit Schreibberechtigung für die Freigabe ein. Während eines Supportfalls werden von Microsoft-Support ausführliche Schritte für die Übertragung dieser lokalen Protokolle bereitgestellt. Wenn das Administratorportal nicht verfügbar ist, können Sie [Get-AzureStackLog](azure-stack-get-azurestacklog.md) verwenden, um Protokolle lokal zu speichern.

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Bandbreitenaspekte

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen variiert abhängig von der Art der Ausführung (proaktiv oder manuell). Bei der **proaktiven Protokollsammlung** beträgt die durchschnittliche Größe ca. 2 GB. Beim **sofortigen Senden von Protokollen** hängt die Größe davon ab, wie viele Stunden die Sammlung umfasst.

In der folgenden Tabelle sind Überlegungen zu Umgebungen mit eingeschränkten oder getakteten Azure-Verbindungen aufgelistet.

| Netzwerkverbindung | Auswirkung |
|----|---|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Protokollupload dauert längere Zeit. |
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Apps/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen. |
| Getaktete Verbindung | Möglicherweise fallen zusätzliche Nutzungsgebühren seitens Ihres ISPs für die Mehrnutzung des Netzwerks an. |

## <a name="parameter-considerations"></a>Überlegungen zu Parametern 

* Die Parameter **FromDate** und **ToDate** können zum Sammeln von Protokollen für einen bestimmten Zeitraum verwendet werden. Ohne Angabe dieser Parameter werden standardmäßig Protokolle für die letzten vier Stunden gesammelt.

* Verwenden Sie den Parameter **FilterByNode**, um Protokolle nach Computername zu filtern. Beispiel:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Verwenden Sie den Parameter **FilterByLogType**, um Protokolle nach Typ zu filtern. Sie können nach Datei (File), Freigabe (Share) oder Windows-Ereignis (WindowsEvent) filtern. Beispiel:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Verwenden Sie den Parameter **FilterByResourceProvider**, um Diagnoseprotokolle für Ressourcenanbieter zu senden, die Mehrwert schaffen. Die allgemeine Syntax sieht wie folgt aus:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  So senden Sie Diagnoseprotokolle für IoT Hub: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  So senden Sie Diagnoseprotokolle für Event Hubs:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  So senden Sie Diagnoseprotokolle für Azure Stack Edge:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* Verwenden Sie den Parameter **FilterByRole**, um Diagnoseprotokolle für die Rollen „VirtualMachines“ und „BareMetal“ zu senden:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* So senden Sie Diagnoseprotokolle für die Rollen „VirtualMachines“ und „BareMetal“ mit Datumsfilterung für Protokolldateien der letzten acht Stunden:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* So senden Sie Diagnoseprotokolle für die Rollen „VirtualMachines“ und „BareMetal“ mit Datumsfilterung für Protokolldateien für den Zeitraum zwischen acht und zwei Stunden vor dem aktuellen Zeitpunkt:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

Sparen Sie Zeit beim Kundensupport, indem Sie die Diagnoseprotokolle proaktiv sammeln, wenn eine Warnung auf Azure Stack Hub ausgelöst wird.

Wenn die Integritätsbedingungen des Systems untersucht werden müssen, können die Protokolle automatisch zur Analyse hochgeladen werden, bevor eine Supportanfrage für den Microsoft-Support erstellt wird.

>[!NOTE]
>Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. 

## <a name="view-log-collection"></a>Anzeigen der Protokollsammlung

Der Verlauf der von Azure Stack Hub gesammelten Protokolle wird auf der Seite **Protokollsammlung** in **Hilfe und Support** mit den folgenden Daten und Uhrzeiten angezeigt:

- **Time Collected** (Zeitpunkt der Sammlung): Zeitpunkt, zu dem der Sammlungsvorgang begonnen hat.
- **Status:** Entweder „In Bearbeitung“ oder „Abgeschlossen“.
- **Logs start** (Beginn der Protokolle): Beginn des Zeitraums, für den Sie die Sammlung ausführen möchten.
- **Logs end** (Ende der Protokolle): Ende des Zeitraums.
- **Typ:** Gibt an, ob es sich um eine manuelle oder um eine proaktive Protokollsammlung handelt.

![Protokollsammlungen in Hilfe und Support](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>Warnungen für die proaktive Sammlung von Diagnoseprotokollen

Bei aktivierter proaktiver Protokollsammlung werden Protokolle hochgeladen, wenn eines der folgenden Ereignisse ausgelöst wird.

**Fehler bei Update** ist beispielsweise eine Warnung, durch die eine proaktive Sammlung von Diagnoseprotokollen ausgelöst wird. Ist die Funktion aktiviert, werden Diagnoseprotokolle bei einem Updatefehler proaktiv gesammelt, um den Microsoft-Support bei der Behandlung des Problems zu unterstützen. Die Diagnoseprotokolle werden nur erfasst, wenn die Warnung für **Fehler bei Update** ausgelöst wird.

| Warnungstitel | FaultIdType |
|---|---|
|Es konnte keine Verbindung mit dem Remotedienst hergestellt werden | UsageBridge.NetworkError|
|Fehler bei Update | Urp.UpdateFailure |
|Infrastruktur oder Abhängigkeiten für Speicherressourcenanbieter nicht verfügbar |    StorageResourceProviderDependencyUnavailable |
|Knoten nicht mit Controller verbunden| ServerHostNotConnectedToController |  
|Fehler bei Routenveröffentlichung | SlbMuxRoutePublicationFailure |
|Interner Datenspeicher des Speicherressourcenanbieters nicht verfügbar |    StorageResourceProvider. DataStoreConnectionFail |
|Speichergerätfehler | Microsoft.Health.FaultType.VirtualDisks.Detached |
|Health Controller kann nicht auf das Speicherkonto zugreifen | Microsoft.Health.FaultType.StorageError |
|Konnektivität mit einem physischen Datenträger ist verloren gegangen | Microsoft.Health.FaultType.PhysicalDisk.LostCommunication |
|Blobdienst wird auf einem Knoten nicht ausgeführt | StorageService.The.blob.service.is.not.running.on.a.node-Critical |
|Infrastrukturrolle fehlerhaft | Microsoft.Health.FaultType.GenericExceptionFault |
|Fehler bei Tabellenspeicherdienst | StorageService.Table.service.errors-Critical |
|Dateifreigabe ist zu mehr als 80 % belegt | Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |
|Skalierungseinheitknoten ist offline. | FRP.Heartbeat.PhysicalNode |
|Infrastrukturrolleninstanz nicht verfügbar | FRP.Heartbeat.InfraVM |
|Infrastrukturrolleninstanz nicht verfügbar  | FRP.Heartbeat.NonHaVm |
|Infrastrukturrolle „Verzeichnisverwaltung“ hat Fehler bei der Zeitsynchronisierung gemeldet | DirectoryServiceTimeSynchronizationError |
|Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats) | CertificateExpiration.ExternalCert.Warning |
|Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats) | CertificateExpiration.ExternalCert.Critical |
|Virtuelle Computer können aufgrund einer niedrigen Arbeitsspeicherkapazität für bestimmte Klassen und Größen nicht bereitgestellt werden | AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Auf Knoten kann für Platzierung des virtuellen Computers nicht zugegriffen werden. | AzureStack.ComputeController.HostUnresponsive |
|Fehler bei Sicherung  | AzureStack.BackupController.BackupFailedGeneralFault |
|Geplante Sicherung wurde aufgrund eines Konflikts mit fehlerhaften Vorgängen übersprungen    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |

## <a name="how-the-data-is-handled"></a>Datenverarbeitung

Wenn Sie **Send logs proactively** (Protokolle proaktiv senden) aktivieren, erklären Sie sich damit einverstanden, dass Microsoft in regelmäßigen Abständen und ausschließlich auf der Grundlage von Azure Stack Hub-Systemintegritätswarnungen automatisch Protokolle sammelt. Darüber hinaus erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden.

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet.

Der Widerruf Ihrer Einwilligung hat keine Auswirkungen auf Daten, die bereits mit Ihrer Zustimmung gesammelt wurden.

## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](./azure-stack-data-collection.md)
