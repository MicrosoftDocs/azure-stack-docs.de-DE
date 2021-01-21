---
title: Aktionen für Skalierungseinheitknoten in Azure Stack Hub für ein MDC
description: Hier erhalten Sie Informationen zu Aktionen für Skalierungseinheitknoten (einschließlich „Einschalten“, „Ausschalten“, „Deaktivieren“ und „Fortsetzen“), und Sie erfahren, wie Sie den Knotenstatus in integrierten Azure Stack Hub-Systemen anzeigen.
author: PatAltimore
ms.topic: article
ms.date: 10/26/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: e07e06d2dbe90c846fc8ea96f18e74b0be3caf49
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255349"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub---modular-data-center-mdc"></a>Aktionen für Skalierungseinheitknoten in Azure Stack Hub – Modular Data Center (MDC)

In diesem Artikel wird beschrieben, wie Sie den Status einer Skalierungseinheit anzeigen. Sie können die Knoten der Einheit anzeigen. Sie können Knotenaktionen wie „Einschalten“, „Ausschalten“, „Deaktivieren“, „Fortsetzen“ und „Reparieren“ ausführen. Normalerweise verwenden Sie diese Knotenaktionen für den Austausch von Teilen im Betrieb oder bei der Wiederherstellung eines Knotens.

> [!Important]  
> Alle in diesem Artikel beschriebenen Knotenaktionen sollten immer nur auf einen Knoten gleichzeitig ausgerichtet sein.

## <a name="view-the-node-status"></a>Anzeigen des Knotenstatus

Im Administratorportal können Sie den Status einer Skalierungseinheit und ihrer zugehörigen Knoten anzeigen.

So zeigen Sie den Status einer Skalierungseinheit an

1. Klicken Sie auf die Kachel **Regionsverwaltung**, und wählen Sie die Region aus.
2. Wählen Sie auf der linken Seite unter **Infrastrukturressourcen** die Option **Skalierungseinheiten** aus.
3. Wählen Sie in den Ergebnissen die Skalierungseinheit aus.
4. Wählen Sie links unter **Allgemein** den Eintrag **Knoten** aus.

   Zeigen Sie die folgenden Informationen an:

   - Die Liste der einzelnen Knoten
   - Betriebszustand (siehe Liste weiter unten)
   - Energiezustand („Wird ausgeführt“ oder „Angehalten“)
   - Servermodell
   - IP-Adresse des Baseboard-Verwaltungscontrollers (BMC)
   - Gesamtanzahl von Kernen
   - Gesamtspeichermenge

     ![Status einer Skalierungseinheit](media/azure-stack-node-actions/multinode-actions.png)

### <a name="node-operational-states"></a>Betriebszustände von Knoten

| Status | BESCHREIBUNG |
|----------------------|-------------------------------------------------------------------|
| Wird ausgeführt | Der Knoten ist aktiv an der Skalierungseinheit beteiligt. |
| Beendet | Der Knoten ist nicht verfügbar. |
| Wird hinzugefügt | Der Knoten wird aktiv zur Skalierungseinheit hinzugefügt. |
| Wird repariert | Der Knoten wird aktiv repariert. |
| Wartung | Der Knoten wurde angehalten, und es wird kein aktiver Benutzerworkload ausgeführt. |
| Korrektur erforderlich | Ein Fehler wurde ermittelt, der eine Reparatur des Knotens erfordert. |

## <a name="scale-unit-node-actions"></a>Knotenaktionen für Skalierungseinheiten

Beim Anzeigen von Informationen zu einem Knoten einer Skalierungseinheit können Sie Knotenaktionen wie die folgenden ausführen:

 - Starten und Beenden (je nach aktuellem Energiestatus)
 - Deaktivieren und Fortsetzen (je nach Betriebsstatus)
 - Reparieren
 - Herunterfahren

Der Betriebszustand des Knotens bestimmt, welche Optionen verfügbar sind.

Sie müssen Azure Stack Hub-PowerShell-Module installieren. Diese Cmdlets befinden sich im **Azs.Fabric.Admin**-Modul. Informationen zum Installieren von PowerShell bzw. zum Überprüfen Ihrer PowerShell-Installation für Azure Stack Hub finden Sie unter [Installieren von PowerShell für Azure Stack Hub](../../operator/powershell-install-az-module.md).

## <a name="stop"></a>Beenden

Die Aktion **Beenden** schaltet den Knoten aus. Dies entspricht dem Drücken des Netzschalters. Es wird kein Signal zum Herunterfahren an das Betriebssystem gesendet. Probieren Sie bei geplanten Beenden-Vorgängen immer zuerst den Vorgang zum Herunterfahren aus.

Diese Aktion wird in der Regel verwendet, wenn ein Knoten nicht reagiert.

Um die Beenden-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Versuchen Sie in dem unwahrscheinlichen Fall, dass die Beenden-Aktion nicht funktioniert, den Vorgang noch mal, und falls er ein zweites Mal fehlschlägt, verwenden Sie stattdessen die BMC-Webbenutzeroberfläche.

Weitere Informationen finden Sie unter [Stop-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

Die Aktion **Starten** schaltet den Knoten ein. Dies entspricht dem Drücken des Netzschalters.

Um die Starten-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Sollte der unwahrscheinliche Fall eintreten, dass die Aktion „Starten“ nicht funktioniert, wiederholen Sie den Vorgang. Wenn auch beim zweiten Mal ein Fehler auftritt, verwenden Sie stattdessen die BMC-Webschnittstelle.

Weitere Informationen finden Sie unter [Start-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Entladen

Die Aktion **Entladen** verschiebt alle aktiven Workloads auf die übrigen Knoten in dieser spezifischen Skalierungseinheit.

Diese Aktion wird in der Regel für den Austausch von Teilen im Betrieb verwendet, z. B. beim Ersetzen eines vollständigen Knotens.

> [!Important]
> Achten Sie darauf, Vorgänge zum Entladen nur während geplanten Wartungen auf Knoten anzuwenden, über die die Benutzer benachrichtigt wurden. Unter bestimmten Umständen können bei aktiven Workloads Unterbrechungen auftreten.

Um die Entladen-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Weitere Informationen finden Sie unter [Disable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Fortfahren

Die Aktion **Fortsetzen** setzt einen entladenen Knoten fort und markiert ihn für die Verteilung von Workloads als aktiv. Für frühere Workloads, die auf dem Knoten ausgeführt wurden, wird kein Failback durchgeführt. (Wenn Sie einen Entladen-Vorgang auf einem Knoten verwenden, stellen Sie sicher, dass er ausgeschaltet ist. Wenn Sie den Knoten wieder einschalten, ist er für die Workloadplatzierung nicht als „Aktiv“ gekennzeichnet. Sie müssen zunächst noch die Aktion „Fortsetzen“ verwenden, um den Knoten als aktiv zu kennzeichnen.)

Um die Fortsetzen-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Weitere Informationen finden Sie unter [Enable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Reparieren

Die Aktion **Reparieren** repariert einen Knoten. Verwenden Sie diese Aktion nur für eines der folgenden Szenarien:

- Vollständiger Knotenaustausch (mit oder ohne neue Datenträger)
- Nach dem Ausfall oder dem Austausch einer Hardwarekomponente (sofern in der Dokumentation der FRU (Field Replaceable Unit) empfohlen).

> [!Important]  
> Wenn Sie einen Knoten oder einzelne Hardwarekomponenten ersetzen müssen, finden Sie die genauen Schritte in der FRU-Dokumentation Ihres OEM-Hardwareherstellers. In der FRU-Dokumentation ist auch angegeben, ob Sie die Reparaturaktion nach dem Ersetzen einer Hardwarekomponente ausführen müssen.

Beim Ausführen der Reparaturaktion müssen Sie die BMC-IP-Adresse angeben.

Um die Reparieren-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

Die Aktion **Herunterfahren** verschiebt zunächst alle aktiven Workloads auf die übrigen Knoten in derselben Skalierungseinheit. Danach wird der Knoten der Skalierungseinheit ordnungsgemäß heruntergefahren.

Nach dem Starten eines heruntergefahrenen Knotens muss die Aktion [Fortsetzen](#resume) ausgeführt werden. Für frühere Workloads, die auf dem Knoten ausgeführt wurden, wird kein Failback durchgeführt.

Sollte beim Herunterfahren ein Fehler auftreten, führen Sie vor dem Herunterfahren erst den Vorgang [Entladen](#drain) aus.

Öffnen Sie zum Herunterfahren eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zum Modul für Azure Stack Hub-Fabric-Operatoren](/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0)