---
title: Azure Stack HCI-Datensammlung
description: In diesem Thema werden die Konzeption und die Richtlinien beschrieben, die für die von Azure Stack HCI gesammelten Diagnosedaten relevant sind.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 91f578bcb0dc3a9d2debd086e6ed95cb591de90d
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97053137"
---
# <a name="azure-stack-hci-data-collection"></a>Azure Stack HCI-Datensammlung

> Gilt für: Azure Stack HCI, Version 20H2

In diesem Thema werden die Daten beschrieben, die erforderlich sind, um Azure Stack HCI zu schützen und auf dem neuesten Stand zu halten. Außerdem wird anhand dieser Daten sichergestellt, dass das Funktionieren den Erwartungen an die allgemeine Verfügbarkeit (General Availability, GA) im Dezember 2020 entspricht.

Die unten beschriebenen Daten benötigt Microsoft, um Azure Stack HCI bereitstellen zu können. Diese Daten werden einmal täglich gesammelt, und Datensammlungsereignisse werden in den Ereignisprotokollen aufgezeichnet. Azure Stack HCI sammelt nur die Daten, die mindestens erforderlich sind, um Ihre Cluster auf dem neuesten Stand zu halten, zu sichern und ihr ordnungsgemäßes Funktionieren sicherzustellen.

   > [!IMPORTANT]
   > Die im Folgenden beschriebenen Daten, die von Azure Stack HCI gesammelt werden, sind von den Windows-Diagnosedaten unabhängig, die für verschiedene Sammlungsebenen konfiguriert werden können. In Azure Stack HCI ist die Standardeinstellung für die Sammlung von Windows-Diagnosedaten „Sicherheit“ (aus). Dies bedeutet, dass keine Windows-Diagnosedaten gesendet werden, es sei denn, der Administrator ändert die Einstellungen für die Diagnosedaten. Weitere Informationen finden Sie unter [Konfigurieren von Windows-Diagnosedaten in Ihrer Organisation](/windows/privacy/configure-windows-diagnostic-data-in-your-organization). Microsoft ist ein unabhängiger Controller von Windows-Diagnosedaten, die in Verbindung mit Azure Stack HCI gesammelt werden. Microsoft verarbeitet die Windows-Diagnosedaten unter Einhaltung der [Microsoft-Datenschutzbestimmungen](https://privacy.microsoft.com/privacystatement).

## <a name="data-collection-and-residency"></a>Datensammlung und -residenz

Folgende Azure Stack HCI-Daten: 

- werden erst an Microsoft gesendet, wenn das Produkt bei Azure registriert wurde. Wenn Azure Stack HCI nicht registriert ist, wird diese Datensammlung angehalten.
- werden im Ereigniskanal „Microsoft-AzureStack-HCI/Analytic“ protokolliert. 
- liegen im JSON-Format vor, sodass Systemadministratoren die gesendeten Daten überprüfen und analysieren können.
- werden in den USA in einem sicheren, von Microsoft betriebenen Rechenzentrum gespeichert.

## <a name="data-retention"></a>Beibehaltung von Daten

Nachdem diese Daten von Azure Stack HCI gesammelt wurden, werden sie 90 Tage lang aufbewahrt. Aggregierte, anonymisierte Daten können länger aufbewahrt werden.

## <a name="what-data-is-collected"></a>Welche Daten werden gesammelt?

Folgende Daten werden von Azure Stack HCI gesammelt:

- Informationen zu Servern, z. B. Betriebssystemversion, Prozessormodell, Anzahl der Prozessorkerne, Arbeitsspeichergröße, Cluster-ID und Hash der Hardware-ID
- Liste der installierten Azure Stack HCI-Serverfunktionen (z. B. BitLocker)
- Erforderliche Informationen zum Berechnen der Zuverlässigkeit des Azure Stack HCI-Betriebssystems
- Erforderliche Informationen zum Berechnen der Zuverlässigkeit der gesammelten Integritätsdaten
- Informationen, die im Ereignisprotokoll für bestimmte Fehler erfasst wurden, z. B. Fehler beim Herunterladen eines Updates
- Informationen zum Berechnen der Zuverlässigkeit des Speichers
- Informationen zum Berechnen der Zuverlässigkeit des physischen Datenträgers
- Informationen zum Berechnen der Zuverlässigkeit der Volumeverschlüsselung
- Informationen zum Berechnen der Zuverlässigkeit und der Leistung der Reparatur von Speicherplätzen
- Informationen zum Überprüfen der Sicherheit des Azure Stack HCI-Betriebssystems
- Informationen zum Berechnen der Zuverlässigkeit des Status von Antiviren-/Antischadsoftware des Azure Stack HCI-Betriebssystems
- Informationen zum Korrelieren der Zuverlässigkeit der Netzwerkkomponenten
- Informationen zum Korrelieren der Netzwerkleistung
- Informationen zum Korrelieren der Zuverlässigkeit von Updates und Installationen
- Informationen zum Messen der Zuverlässigkeit von Hyper-V
- Informationen zum Messen/Korrelieren der Zuverlässigkeit der Clusteringkomponenten
- Informationen zum Nachverfolgen des Erfolgs des Features „Clusterfähiges Aktualisieren“ (CAU)
- Informationen zum Messen/Korrelieren der Zuverlässigkeit des Notfallwiederherstellungsfeatures
- Informationen zum Beschreiben der SMB-Bandbreitenlimits, die für Azure Stack HCI-Server gelten

## <a name="view-this-data"></a>Anzeigen dieser Daten

1. Aktivieren Sie das Analyseprotokoll mit dem folgenden PowerShell-Befehl:

   ```PowerShell
   wevtutil sl Microsoft-AzureStack-HCI/Analytic /e:True
   ```

2. Öffnen Sie das Protokoll, um die gesammelten Daten zu prüfen:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest
   ```

3. Formatieren Sie die Daten für den Export:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest `
   | Where-Object Id -eq 802 `
   | ForEach-Object { 
       [pscustomobject] @{
           TimeCreated = $_.TimeCreated 
           EventName=$_.Properties[0].Value 
           Value=$_.Properties[1].Value 
       } 
   }
   ```
 
Die Ausgabe sollte ungefähr wie folgt aussehen:

```shell
TimeCreated            EventName                                                  Value
-----------            ---------                                                  -----
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.Core                   {"OEMName":"Microsoft Corporation"...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ProductFeatures        {"InstalledFeatures":["Server-Core...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.OSReliability          {"DailyDirtyRestarts":0,"WeeklyDir...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.DiagnosticHealth       {"DailySuccessfulDiagnosticUploads...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ErrorSummary           {"ErrorSummary":[{"EventName":"Win...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.VolumeSummary          {"VolumeCount":2,"HealthyVolumeCou...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.DiskSummary            {"DiskCount":33,"Summary":[]}
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.BitlockerVolumeSummary {"BitlockerVolumeCount":0,"Summary...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageErrors          {"ErrorSummary":[{"EventName":"Sto...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageRepairSummary   {"DailyRepairStartCount":0,"Weekly...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.TrustedPlatformModule  {"Manufacturer":"MSFT","Manufactur...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.MicrosoftDefender      {"AMEngineVersion":"1.1.17600.5","...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkInfo            {"NetworkDirect":true,"NetworkDire...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkAdapterSummary  {"NetworkAdapterGroup":[{"DriverNa...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.OSDeploy               {"OSInstallType":0}
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.ClusterProperties      {"Id":"fd2fc061-b924-4d61-a45b-3b3...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.DisasterRecovery       {"IsDisasterRecoveryEnabled":false...
```
