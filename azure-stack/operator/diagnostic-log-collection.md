---
title: Erfassung von Diagnoseprotokollen
description: Informieren Sie sich über die Erfassung von Diagnoseprotokollen.
author: PatAltimore
ms.topic: article
ms.date: 02/03/2021
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 02/03/2021
ms.openlocfilehash: ad5f0a7f6028249dba3d63490cdc3c91d7a45e72
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013216"
---
# <a name="diagnostic-log-collection"></a>Erfassung von Diagnoseprotokollen

Die von Azure Stack Hub erstellten Diagnoseprotokolle können freigegeben werden. Diese Protokolle werden von den Windows-Komponenten und lokalen Azure-Diensten erstellt. Der Microsoft-Support kann mithilfe der Protokolle Probleme mit Ihrer Azure Stack Hub-Instanz beheben oder identifizieren.

Wenn Diagnoseprotokolle für Ihre Azure Stack Hub-Instanz erfasst werden sollen, müssen Sie Ihre Instanz zunächst registrieren. Wenn Sie Azure Stack Hub nicht registriert haben, [verwenden Sie den privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) für die Weitergabe von Protokollen. 

::: moniker range=">= azs-2005"

Es gibt mehrere Möglichkeiten, Diagnoseprotokolle an den Microsoft-Support zu senden. Je nach Ihrer Verbindung zu Azure haben Sie folgende Optionen:
* [Proaktives Senden von Protokollen (empfohlen)](#send-logs-proactively)
* [Sofortiges Senden von Protokollen](#send-logs-now)
* [Lokales Speichern von Protokollen](#save-logs-locally)

Das folgende Flussdiagramm zeigt, welche Option für den Versand von Diagnoseprotokollen verwendet werden muss. Wenn Azure Stack Hub eine Verbindung mit Azure herstellt, müssen Sie die **proaktive Protokollsammlung** aktivieren. Durch die proaktive Protokollsammlung werden automatisch Diagnoseprotokolle in ein von Microsoft kontrolliertes Speicherblob in Azure hochgeladen, wenn eine kritische Warnung ausgelöst wird. Außerdem können Sie Protokolle nach Bedarf erfassen, indem Sie **Protokolle sofort senden**. Bei einer Azure Stack Hub-Instanz, die in einer nicht verbundenen Umgebung ausgeführt wird, oder bei Verbindungsproblemen, sollten Sie **Protokolle lokal speichern**.

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>Proaktives Senden von Protokollen

Bei der proaktiven Protokollsammlung werden Diagnoseprotokolle automatisch gesammelt und von Azure Stack Hub an Microsoft gesendet, bevor Sie eine Supportanfrage erstellen. Diese Protokolle werden nur gesammelt, wenn eine Systemintegritätswarnung ausgelöst wird, und der Microsoft-Support greift nur im Kontext einer Supportanfrage auf diese Protokolle zu.

::: moniker range=">= azs-2008"

Ab Version 2008 von Azure Stack Hub verwendet die proaktive Protokollsammlung einen verbesserten Algorithmus, der Protokolle selbst bei Fehlerbedingungen erfasst, die für einen Bedienenden nicht sichtbar sind. Dadurch wird sichergestellt, dass die richtigen Diagnoseinformationen zum richtigen Zeitpunkt gesammelt werden, ohne dass eine Interaktion des Operators erforderlich ist. Der Microsoft-Support kann so in einigen Fällen schneller mit der Problembehandlung beginnen und Probleme beheben. Die Verbesserungen der ursprünglichen Algorithmen beziehen sich hauptsächlich auf Patch- und Aktualisierungsvorgänge.

Azure Stack Hub erfasst Protokolle zu Warnungen und anderen versteckten Fehlerereignissen, die für Sie nicht sichtbar sind.

Azure Stack Hub sammelt proaktiv Protokolle für folgende Ereignisse:

- Die Aktualisierung ist fehlgeschlagen.
- Update erfordert Ihre Aufmerksamkeit.

Wenn ein Ereignis diese Warnungen auslöst, sendet Azure Stack Hub die Protokolle proaktiv an Microsoft.

Außerdem sendet Azure Stack Hub Protokolle an Microsoft, die von anderen Fehlerereignissen ausgelöst werden. Diese Ereignisse sind für Sie nicht sichtbar.

Die Aktivierung von proaktiven Protokollsammlungen wird empfohlen, da weitere Vorgänge optimiert werden und mehr Vorteile entstehen.

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

## <a name="send-logs-now"></a>Sofortiges Senden von Protokollen

> [!TIP]
> Sparen Sie Zeit, indem Sie [Send logs proactively](#send-logs-proactively) (Protokolle proaktiv senden) anstelle von „Send logs now“ (Protokolle jetzt senden) verwenden.

Das sofortige Senden von Protokollen ist nun eine Option, bei der Sie Ihre Diagnoseprotokolle manuell erfassen und von Azure Stack Hub hochladen (üblicherweise vor dem Erstellen einer Supportanfrage).

Es gibt zwei Möglichkeiten, mit denen Sie Diagnoseprotokolle manuell an den Microsoft-Support senden können:
* [Administratorportal (empfohlen)](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt sich die Verwendung des Administratorportals, da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Sollte das Portal nicht verfügbar sein, sollten Sie die Protokolle mit PowerShell senden.

### <a name="send-logs-now-with-the-administrator-portal"></a>Sofortiges Senden von Protokollen mit dem Administratorportal

So senden Sie Protokolle jetzt mithilfe des Administratorportals:

1. Öffnen Sie **Help + support > Log Collection > Send logs now** („Hilfe und Support“ > „Protokollsammlung“ > „Protokolle jetzt senden“). 
1. Geben Sie die Start- und Endzeit für die Protokollsammlung an. 
1. Wählen Sie die lokale Zeitzone aus.
1. Wählen Sie **Collect and Upload** (Sammeln und hochladen) aus.

Falls Sie keine Internetverbindung haben oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand.

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

  ::: moniker range=">= azs-2008"

  So senden Sie Diagnoseprotokolle für den SQL-Ressourcenanbieter: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  So senden Sie Diagnoseprotokolle für den MySQL-Ressourcenanbieter: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
  ```
  
  ::: moniker-end

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

Sie können Protokolle in einer lokalen SMB-Freigabe (Server Message Block) speichern, wenn Azure Stack Hub nicht mit Azure verbunden ist. Sie können z. B. eine nicht verbundene Umgebung ausführen. Wenn normalerweise eine Internetverbindung besteht, zurzeit jedoch Konnektivitätsprobleme auftreten, können Sie Protokolle lokal speichern, um die Problembehandlung zu erleichtern.

 Geben Sie auf dem Blatt **Einstellungen** den Pfad sowie einen Benutzernamen und ein Kennwort mit Schreibberechtigung für die Freigabe ein. Während eines Supportfalls werden von Microsoft-Support ausführliche Schritte für die Übertragung dieser lokalen Protokolle bereitgestellt. Wenn das Administratorportal nicht verfügbar ist, können Sie [Get-AzureStackLog](azure-stack-get-azurestacklog.md) verwenden, um Protokolle lokal zu speichern.

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Bandbreitenaspekte

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen variiert abhängig von der Art der Ausführung (proaktiv oder manuell). Bei der **proaktiven Protokollsammlung** beträgt die durchschnittliche Größe ca. 2 GB. Beim **sofortigen Senden von Protokollen** hängt die Größe davon ab, wie viele Stunden die Sammlung umfasst.

In der folgenden Tabelle sind Überlegungen zu Umgebungen mit eingeschränkten oder getakteten Azure-Verbindungen aufgelistet.

| Netzwerkverbindung | Auswirkung |
|----|---|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Protokollupload dauert längere Zeit. |
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Apps/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen. |
| Getaktete Verbindung | Möglicherweise fallen andere Nutzungsgebühren seitens Ihres ISP für die Mehrnutzung des Netzwerks an. |

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
