---
title: 'Azure Stack: Integritätsüberprüfungen in erweitertem Speicher für Modular Data Center-Blobspeicher'
description: Dieser Artikel enthält Anleitungen zum Durchführen von Integritätsüberprüfungen im erweiterten Speicher für den Modular Data Center-Blobspeicher.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: patricka
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: 8dea13880e2703290e1528fca47b777f98fca7b2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255519"
---
# <a name="extended-storage-health-checks"></a>Integritätsüberprüfungen im erweiterten Speicher

Dieser Artikel enthält Anleitungen zum Überprüfen der Integrität der Hardware für den erweiterten Speicher von Rechenzentren und der Azure Stack Hub-Bereitstellung.

Lesen Sie vor Beginn die unter [Aktualisieren der Firmware für den erweiterten Speicher]() beschriebenen Schritte, und führen Sie sie aus.

Der letzte Schritt vor dem Versand des Systems ist das Herunterfahren. Führen Sie die Schritte im Schnellstarthandbuch aus.

## <a name="extended-storage-health"></a>Integrität des erweiterten Speichers

Dieser Abschnitt enthält Anleitungen zum Überprüfen der Integrität der Hardware für den erweiterten Speicher.
Überprüfen Sie alle Ereignisse, und benachrichtigen Sie den Administrator, falls Probleme behoben werden müssen. 


Führen Sie den folgenden Befehl aus, um die Integrität des Clusterspeicherpools zu überprüfen:
```console
isi storagepool health
```

Sofern keine Fehler vorliegen, sehen die Ergebnisse der Befehlsausführung etwa wie folgt aus:
```console
All pools are healthy.
Unprovisioned drives: none
```

Bei Fehlern oder fehlenden Laufwerken sehen die Ergebnisse des Befehls wie folgt aus:

```console
SmartPools Health
Name                  Health  Type Prot   Members          Down          Smartfailed
--------------------- ------- ---- ------ ---------------- ------------- -------------
a2000_200tb_800gb-    -M---
ssd-sed_16gb
 a2000_200tb_800gb-   -M---   HDD  3x     3,11,14,19,23,30 Nodes:        Nodes:
ssd-sed_16gb:24                           ,38,41,46:bay6,1 Drives:       Drives:
                                          0-11,16,19,
                                          25:bay6,10-11,16

OK = Ok, U = Too few nodes, M = Missing drives,
D = Some nodes or drives are down, S = Some nodes or drives are smartfailed,
R = Some nodes or drives need repair
Unprovisioned drives: none
```

Wenden Sie sich an den Microsoft-Support, um bei etwaigen Problemen Hilfe zu erhalten.

Mit dem nächsten Befehl wird die allgemeine Integrität des Clusters überprüft (vereinfachte Ansicht):
```console
isi status
```

Wenn keine Fehler vorliegen, wird ein grünes **OK** angezeigt, andernfalls werden gelbe Warnungen oder rote Fehler angezeigt. Diese stehen in der folgenden Tabelle in der Zeile zur **Clusterintegrität** oder in einer oder mehreren Zeilen mit den Clusterknoten-IDs.

Falls Sie weitere Informationen zur Integrität des Clusters benötigen, können Sie einen ausführlicheren Befehl ausführen (erweiterte Ansicht):
```console
isi status -a
```

Wenden Sie sich an den Microsoft-Support, um bei etwaigen Problemen Hilfe zu erhalten.

## <a name="azure-stack-hub-health"></a>Azure Stack Hub-Integrität

Dieser Abschnitt enthält Anleitungen zum Überprüfen der Integrität der Azure Stack Hub-Bereitstellung.

Führen Sie das folgende Skript aus, um eine allgemeine Übersicht über die Azure Stack-Bereitstellung zu erhalten, die mit dem erweiterten Speichersystem integriert ist. Das Skript ist ein Wrapper für Folgendes:
- Verbindung mit einer ERCS-VM unter Verwendung der angegebenen Anmeldeinformationen
- Ausführung des Befehls „Test-AzureStack -Debug“ (der ausführliche Informationen zur Integrität direkt auf dem Bildschirm ausgibt)

Für dieses Skript müssen folgende Voraussetzungen erfüllt sein:
- Die Skriptdatei „.\Invoke-ExtendedStorageConfiguration.ps1“ muss im Ordner „C:\OEMSoftware\ExtendedStorage\“ auf dem Hardware Lifecycle Host (HLH) vorhanden sein.
- Die Variable „$AzScred“ mit den Anmeldeinformationen muss die Anmeldeinformationen für „*DOMÄNE*\cloudadmin“ enthalten. Ersetzen Sie *DOMÄNE* durch den tatsächlichen Domänennamen, z. B. „CONTOSO“.


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

Überprüfen Sie anhand der Ausgabe den allgemeinen Integritätsstatus der Azure Stack-Bereitstellung sowie in den Abschnitten zum **NAS** der Ergebnisse den Integritätsstatus für die Integration des erweiterten Speichers mit Azure Stack.

Eine ausführlichere Beschreibung der Azure Stack-Diagnose finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](../operator/azure-stack-diagnostic-test.md).

## <a name="technical-support"></a>Technischer Support

Wenden Sie sich an den Microsoft-Support, um bei etwaigen Problemen Hilfe zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren der Firmware]()