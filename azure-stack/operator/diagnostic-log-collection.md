---
title: Erfassung von Diagnoseprotokollen
description: Informieren Sie sich über die Erfassung von Diagnoseprotokollen.
author: myoungerman
ms.topic: article
ms.date: 10/30/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: eaa265189769bf1f192ef6fce260a221935736cb
ms.sourcegitcommit: 076ece88c3177db321f0ae32cba1d05179ffc393
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97794191"
---
# <a name="diagnostic-log-collection"></a>Erfassung von Diagnoseprotokollen

Azure Stack Hub ist eine Sammlung von Windows-Komponenten und lokalen Azure-Diensten, die miteinander interagieren. Von allen diesen Komponenten und Diensten werden jeweils eigene Protokolle generiert. Da diese Protokolle vom Microsoft-Support verwendet werden, um Ihre Probleme zu identifizieren und zu beheben, bieten wir eine Diagnoseprotokollsammlung an. Die Diagnoseprotokollsammlung unterstützt Sie dabei, Diagnoseprotokolle schnell zu erfassen und an den Microsoft-Support weiterzugeben.

> [!IMPORTANT]
> Sie müssen Azure Stack Hub registrieren, um die Diagnoseprotokollsammlung verwenden zu können. Wenn Sie Azure Stack Hub nicht registriert haben, [verwenden Sie den privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) für die Weitergabe von Protokollen. 

::: moniker range=">= azs-2005"

Azure Stack Hub bietet mehrere Methoden zum Erfasse, Speichern und Senden von Diagnoseprotokollen an den Microsoft-Support. Abhängig von der Konnektivität mit Azure lauten Ihre Optionen zum Erfassen und Senden von Protokollen wie folgt:
* [Proaktives Senden von Protokollen (empfohlen)](#send-logs-proactively)
* [Sofortiges Senden von Protokollen](#send-logs-now)
* [Lokales Speichern von Protokollen](#save-logs-locally)

Das folgende Flussdiagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss: Wenn Azure Stack Hub eine Verbindung mit Azure herstellen kann, empfiehlt es sich, die **proaktive Protokollsammlung** zu aktivieren. Dadurch werden automatisch Diagnoseprotokolle in ein von Microsoft gesteuertes Speicherblob in Azure hochgeladen, wenn eine kritische Warnung ausgelöst wird. Alternativ können Sie Protokolle nach Bedarf erfassen, indem Sie **Send logs now** (Protokolle jetzt senden) verwenden. Wenn keine Verbindung mit Azure Stack Hub besteht, können Sie **Protokolle lokal speichern**. 

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>Proaktives Senden von Protokollen

Bei der proaktiven Protokollsammlung werden Diagnoseprotokolle automatisch gesammelt und von Azure Stack Hub an Microsoft gesendet, bevor Sie eine Supportanfrage erstellen. Diese Protokolle werden nur gesammelt, wenn eine [Systemintegritätswarnung](#proactive-diagnostic-log-collection-alerts) ausgelöst wird, und der Microsoft-Support greift nur im Kontext einer Supportanfrage auf diese Protokolle zu.

::: moniker range=">= azs-2008"

Ab Version 2008 von Azure Stack Hub verwendet die proaktive Protokollsammlung einen verbesserten Algorithmus, der Protokolle selbst bei Fehlerbedingungen erfasst, die für einen Operator nicht sichtbar sind. Dadurch wird sichergestellt, dass die richtigen Diagnoseinformationen zum richtigen Zeitpunkt gesammelt werden, ohne dass eine Interaktion des Operators erforderlich ist. Der Microsoft-Support kann so in einigen Fällen schneller mit der Problembehandlung beginnen und Probleme beheben. Die Verbesserungen der ursprünglichen Algorithmen beziehen sich hauptsächlich auf Patch- und Aktualisierungsvorgänge. Die Aktivierung von proaktiven Protokollsammlungen wird empfohlen, da weitere Vorgänge optimiert werden und mehr Vorteile entstehen.

::: moniker-end

Die proaktive Protokollsammlung kann jederzeit deaktiviert und erneut aktiviert werden. Gehen Sie zum Einrichten der proaktiven Protokollsammlung wie folgt vor:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
1. Öffnen Sie **Hilfe und Support – Übersicht**.
1. Wenn das Banner angezeigt wird, wählen Sie **Enable proactive log collection** (Proaktive Protokollsammlung aktivieren) aus. Sie können auch **Einstellungen** auswählen, **Proactive log collection** (Proaktive Protokollsammlung) auf **Aktivieren** festlegen und anschließend **Speichern** auswählen.

> [!NOTE]
> Falls für eine lokale Dateifreigabe Protokollspeicherorteinstellungen konfiguriert sind, stellen Sie sicher, dass Lebenszyklusverwaltungsrichtlinien vorhanden sind, die das Erreichen des Größenkontingents für den Freigabespeicher verhindern. Die lokale Dateifreigabe wird von Azure Stack Hub nicht überwacht, und von Azure Stack Hub werden keine Aufbewahrungsrichtlinien erzwungen.   

### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Sie erklären sich damit einverstanden, dass Microsoft in regelmäßigen Abständen und ausschließlich auf der Grundlage von Azure Stack Hub-Systemintegritätswarnungen automatisch Protokolle gesammelt. Darüber hinaus erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden.

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet.

Der Widerruf Ihrer Einwilligung hat keine Auswirkungen auf Daten, die bereits mit Ihrer Zustimmung gesammelt wurden.

Mit der **proaktiven Protokollsammlung** gesammelte Protokolle werden in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen. Auf diese Protokolle kann von Microsoft im Kontext einer Supportanfrage sowie zur Verbesserung der Integrität von Azure Stack Hub zugegriffen werden.

### <a name="proactive-diagnostic-log-collection-alerts"></a>Warnungen für die proaktive Sammlung von Diagnoseprotokollen

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

## <a name="send-logs-now"></a>Sofortiges Senden von Protokollen

> [!TIP]
> Sparen Sie Zeit, indem Sie [Send logs proactively](#send-logs-proactively) (Protokolle proaktiv senden) anstelle von „Send logs now“ (Protokolle jetzt senden) verwenden.

Das sofortige Senden von Protokollen ist nun eine Option, bei der Sie Ihre Diagnoseprotokolle von Azure Stack Hub manuell sammeln und hochladen (üblicherweise vor dem Erstellen einer Supportanfrage).

Es gibt zwei Möglichkeiten, mit denen Sie Diagnoseprotokolle manuell an den Microsoft-Support senden können:
* [Administratorportal (empfohlen)](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt sich die Verwendung des Administratorportals, da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Sollte das Portal nicht verfügbar sein, senden Sie die Protokolle stattdessen per PowerShell.

### <a name="send-logs-now-with-the-administrator-portal"></a>Sofortiges Senden von Protokollen mit dem Administratorportal

So senden Sie Protokolle jetzt mithilfe des Administratorportals:

1. Öffnen Sie **Help + support > Log Collection > Send logs now** („Hilfe und Support“ > „Protokollsammlung“ > „Protokolle jetzt senden“). 
1. Geben Sie die Start- und Endzeit für die Protokollsammlung an. 
1. Wählen Sie die lokale Zeitzone aus.
1. Wählen Sie **Collect and Upload** (Sammeln und hochladen) aus.

Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand.

### <a name="send-logs-now-with-powershell"></a>Sofortiges Senden von Protokollen mit PowerShell

Wenn Sie die Methode **Send logs now** (Protokolle jetzt senden) verwenden und PowerShell anstelle des Administratorportals verwenden möchten, können Sie das Cmdlet `Send-AzureStackDiagnosticLog` verwenden, um spezifische Protokolle zu erfassen und zu senden.

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
  So senden Sie Diagnoseprotokolle für den SQL-Ressourcenanbieter: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  So senden Sie Diagnoseprotokolle für den MySQL-Ressourcenanbieter: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
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

> [!NOTE]
> Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. 

### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Durch Initiieren der Sammlung von Diagnoseprotokollen über Azure Stack Hub erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden. Der Microsoft-Support kann im Rahmen der Supportanfrage umgehend und ohne weitere Kundeninteraktion hinsichtlich der Protokollsammlung auf diese Protokolle zugreifen.

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Lokales Speichern von Protokollen

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

## <a name="view-log-collection"></a>Anzeigen der Protokollsammlung

Der Verlauf der von Azure Stack Hub gesammelten Protokolle wird auf der Seite **Protokollsammlung** in **Hilfe und Support** mit den folgenden Daten und Uhrzeiten angezeigt:

- **Time Collected** (Zeitpunkt der Sammlung): Zeitpunkt, zu dem der Sammlungsvorgang begonnen hat.
- **Status:** Entweder „In Bearbeitung“ oder „Abgeschlossen“.
- **Logs start** (Beginn der Protokolle): Beginn des Zeitraums, für den Sie die Sammlung ausführen möchten.
- **Logs end** (Ende der Protokolle): Ende des Zeitraums.
- **Typ:** Gibt an, ob es sich um eine manuelle oder um eine proaktive Protokollsammlung handelt.

![Protokollsammlungen in Hilfe und Support](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](./azure-stack-data-collection.md)
