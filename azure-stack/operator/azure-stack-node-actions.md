---
title: Aktionen für Knoten einer Skalierungseinheit in Azure Stack Hub
description: Hier erhalten Sie Informationen zu Aktionen für Knoten einer Skalierungseinheit (einschließlich „Einschalten“, „Ausschalten“, „Deaktivieren“ und „Fortsetzen“), und Sie erfahren, wie Sie den Knotenstatus in integrierten Azure Stack Hub-Systemen anzeigen.
author: PatAltimore
ms.topic: how-to
ms.date: 1/19/2021
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 1/19/2021
ms.openlocfilehash: 5bececf48222c9dc7401df7cb84d83f375c93d75
ms.sourcegitcommit: 01abc9d81ced31bd727626195148b4e00cc2d62e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98584296"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Aktionen für Knoten einer Skalierungseinheit in Azure Stack Hub

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
   - Betriebsstatus (weitere Informationen in der nachfolgenden Liste)
   - Energiestatus („Wird ausgeführt“ oder „Beendet“)
   - Servermodell
   - IP-Adresse des Baseboard-Verwaltungscontrollers (Baseboard Management Controller, BMC)
   - Gesamtanzahl von Kernen
   - Gesamtspeichermenge
   
    Knotenaktionen können auch zur Auslösung erwarteter Warnungen im Administratorportal führen. 

![Status einer Skalierungseinheit](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Betriebszustände von Knoten

| Status | BESCHREIBUNG |
|----------------------|-------------------------------------------------------------------|
| Wird ausgeführt | Der Knoten ist aktiv an der Skalierungseinheit beteiligt. |
| Beendet | Der Knoten ist nicht verfügbar. |
| Wird hinzugefügt | Der Knoten wird aktiv zur Skalierungseinheit hinzugefügt. |
| Wird repariert | Der Knoten wird aktiv repariert. |
| Wartung | Der Knoten wurde angehalten, und es wird kein aktiver Benutzerworkload ausgeführt. |
| Korrektur erforderlich | Ein Fehler wurde ermittelt, der eine Reparatur des Knotens erfordert. |

### <a name="azure-stack-hub-shows-adding-status-after-an-operation"></a>Azure Stack Hub zeigt das Hinzufügen des Status nach einem Vorgang an

Azure Stack Hub zeigt möglicherweise den Betriebsstatus des Knotens als **Adding** (Wird hinzugefügt) an, nachdem ein Vorgang wie Drain (Ausgleichen), Resume (Fortsetzen), Repair (Reparieren), Shutdown (Herunterfahren) oder Start (Starten) ausgeführt wurde.
Dies kann der Fall sein, wenn der Cache der Fabric-Ressourcenanbieterrolle nach einem Vorgang nicht aktualisiert wurde. 

Stellen Sie vor dem Anwenden der folgenden Schritte sicher, dass aktuell kein Vorgang ausgeführt wird. Aktualisieren Sie den Endpunkt entsprechend Ihrer Umgebung.

### <a name="az-modules"></a>[Az-Module](#tab/az1)

1. Öffnen Sie PowerShell, und fügen Sie Ihre Azure Stack Hub-Umgebung hinzu. Hierfür muss [Azure Stack Hub PowerShell](./powershell-install-az-module.md) auf dem Computer installiert sein.

    ```powershell
    Add-AzEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzAccount -Environment AzureStack
    ```

2. Führen Sie den folgenden Befehl aus, um die Fabric-Ressourcenanbieterrolle neu zu starten.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Überprüfen Sie, ob der Betriebsstatus des betroffenen Skalierungseinheitknotens in **Running** (Wird ausgeführt) geändert wurde. Sie können das Administratorportal oder den folgenden PowerShell-Befehl verwenden:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Wenn der Betriebsstatus des Knotens weiterhin als **Adding** (Wird hinzugefügt) angezeigt wird, öffnen Sie einen Supportfall.

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

1. Öffnen Sie PowerShell, und fügen Sie Ihre Azure Stack Hub-Umgebung hinzu. Hierfür muss [Azure Stack Hub PowerShell](./powershell-install-az-module.md) auf dem Computer installiert sein.

    ```powershell
    Add-AzureRMEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzureRMAccount -Environment AzureStack
    ```

2. Führen Sie den folgenden Befehl aus, um die Fabric-Ressourcenanbieterrolle neu zu starten.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Überprüfen Sie, ob der Betriebsstatus des betroffenen Skalierungseinheitknotens in **Running** (Wird ausgeführt) geändert wurde. Sie können das Administratorportal oder den folgenden PowerShell-Befehl verwenden:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Wenn der Betriebsstatus des Knotens weiterhin als **Adding** (Wird hinzugefügt) angezeigt wird, öffnen Sie einen Supportfall.

---



## <a name="scale-unit-node-actions"></a>Knotenaktionen für Skalierungseinheiten

Beim Anzeigen von Informationen zu einem Knoten einer Skalierungseinheit können Sie Knotenaktionen wie die folgenden ausführen:

 - Starten und Beenden (je nach aktuellem Energiestatus)
 - Deaktivieren und Fortsetzen (je nach Betriebsstatus)
 - Reparieren
 - Herunterfahren

Der Betriebszustand des Knotens bestimmt, welche Optionen verfügbar sind.

Sie müssen Azure Stack Hub-PowerShell-Module installieren. Diese Cmdlets befinden sich im **Azs.Fabric.Admin**-Modul. Informationen zum Installieren von PowerShell bzw. zum Überprüfen Ihrer PowerShell-Installation für Azure Stack Hub finden Sie unter [Installieren von PowerShell für Azure Stack Hub](powershell-install-az-module.md).

## <a name="stop"></a>Beenden

Die Aktion **Beenden** schaltet den Knoten aus. Dies entspricht dem Drücken des Netzschalters. Es wird kein Signal zum Herunterfahren an das Betriebssystem gesendet. Probieren Sie bei geplanten Beenden-Vorgängen immer zuerst den Vorgang zum Herunterfahren aus.

Diese Aktion wird normalerweise verwendet, wenn ein Knoten nicht mehr auf Anforderungen reagiert.

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

> [!CAUTION]  
> Der Firmwareabgleich ist für den Erfolg des in diesem Artikel beschriebenen Vorgangs entscheidend. Wenn Sie diesen Schritt auslassen, kann dies zu einem instabilen System, Leistungseinbußen, Sicherheitsrisiken oder Fehlern bei der Bereitstellung des Betriebssystems durch die Azure Stack Hub-Automatisierung führen. Lesen Sie immer die Dokumentation des Hardwarepartners, wenn Sie Hardware austauschen, um sicherzustellen, dass die angewendete Firmware der OEM-Version entspricht, die im [Azure Stack Hub-Administratorportal](azure-stack-updates.md) angezeigt wird.<br><br>
Weitere Informationen und Links zur Partnerdokumentation finden Sie unter [Ersetzen einer Hardwarekomponente](azure-stack-replace-component.md).

| Hardwarepartner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Betriebshandbuch für Cisco Integrated System für Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Versionshinweise für Cisco Integrated System für Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | All | [Cloud für Microsoft Azure Stack Hub 14G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud für Microsoft Azure Stack Hub 13G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu-Supportdesk für verwaltete Dienste (Konto und Anmeldung erforderlich)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Fujitsu-Support für IT-Produkte und -Systeme](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (Konto und Anmeldung erforderlich)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | All | [HPE ProLiant für Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM – bewährte Vorgehensweisen](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

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

- [Installieren von Azure Stack PowerShell](./powershell-install-az-module.md)
- [Weitere Informationen zum Azure Stack Hub Fabric-Operatormodul](/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0&preserve-view=true)
- [Überwachen des Vorgangs zum Hinzufügen eines Knotens](./azure-stack-add-scale-node.md#monitor-add-node-operations)
