---
title: Ersetzen einer Hardwarekomponente auf einem Azure Stack Hub-Knoten einer Skalierungseinheit
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie eine Hardwarekomponente in einem integrierten Azure Stack Hub-System ersetzen.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 07/18/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 0f0838d64dd205d56b0a82d487176296805f4ff3
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509601"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-hub-scale-unit-node"></a>Ersetzen einer Hardwarekomponente auf einem Azure Stack Hub-Knoten einer Skalierungseinheit

In diesem Artikel wird das allgemeine Verfahren zum Ersetzen von Hardwarekomponenten beschrieben, die nicht im laufenden Betrieb austauschbar sind. Die tatsächlichen Schritte zum Austausch variieren abhängig von Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM). Die für Ihr integriertes Azure Stack Hub-System spezifischen ausführlichen Schritte finden Sie in der FRU-Dokumentation (Field Replaceable Unit) des Anbieters.

> [!CAUTION]  
> Der Firmwareabgleich ist für den Erfolg des in diesem Artikel beschriebenen Vorgangs entscheidend. Wenn Sie diesen Schritt auslassen, kann dies zu einem instabilen System, Leistungseinbußen oder Sicherheitsbedrohungen führen oder eine Bereitstellung des Betriebssystems mithilfe der Azure Stack Hub-Automatisierung verhindern. Lesen Sie immer die Dokumentation des Hardwarepartners, wenn Sie Hardware austauschen, um sicherzustellen, dass die angewendete Firmware der OEM-Version entspricht, die im [Azure Stack Hub-Administratorportal](azure-stack-updates.md) angezeigt wird.

| Hardwarepartner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Betriebshandbuch für Cisco Integrated System für Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Versionshinweise für Cisco Integrated System für Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | All | [Cloud für Microsoft Azure Stack Hub 14G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud für Microsoft Azure Stack Hub 13G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu-Supportdesk für verwaltete Dienste (Konto und Anmeldung erforderlich)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Fujitsu-Support für IT-Produkte und -Systeme](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (Konto und Anmeldung erforderlich)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | All | [HPE ProLiant für Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM – bewährte Vorgehensweisen](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM-/Firmwarepaket](https://aka.ms/AA6z600)<br>[Terra Azure Stack Hub-Dokumentation (einschließlich FRU)](https://aka.ms/aa6zktc)

Nicht im laufenden Betrieb austauschbare Komponenten umfassen folgende Elemente:

- CPU*
- Arbeitsspeicher*
- Hauptplatine/Baseboard-Verwaltungscontroller (BMC)/Grafikkarte
- Datenträgercontroller/Hostbusadapter (HBA)/Rückwandplatine
- Netzwerkadapter (NIC)
- Betriebssystemdatenträger*
- Datenlaufwerke (Laufwerke, die den Austausch im laufenden Betrieb nicht unterstützen, z. B. PCI-e-Add-In-Karten)*

*Diese Komponenten unterstützen möglicherweise den Austausch per Hot-Swap, aber können auf Basis der Herstellerimplementierung variieren. Ausführliche Schritte finden Sie in der FRU-Dokumentation des OEM-Anbieters.

Das folgende Flussdiagramm zeigt den allgemeinen FRU-Vorgang zum Ersetzen einer nicht im laufenden Betrieb austauschbaren Hardwarekomponente.

![Flussdiagramm zum Ablauf des Komponentenaustauschs](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

** Ob der OEM-Hardwareanbieter den Austausch von Komponenten und die Aktualisierung der Firmware durchführt, kann je nach Ihrem Supportvertrag variieren.

## <a name="review-alert-information"></a>Überprüfen von Warnungsinformation

Das Integritäts- und Überwachungssystem von Azure Stack Hub verfolgt die Integrität von Netzwerkadaptern und Datenlaufwerken nach, die von „Direkte Speicherplätze“ gesteuert werden. Andere Hardwarekomponenten werden nicht nachverfolgt. Für alle anderen Hardwarekomponenten werden Warnungen in der anbieterspezifischen Hardwareüberwachungslösung ausgelöst, die auf dem Lebenszyklushost der Hardware ausgeführt wird.  

## <a name="component-replacement-process"></a>Komponentenaustauschprozess

Die folgenden Schritte ermöglichen eine allgemeine Übersicht über den Komponentenaustauschprozess. Führen Sie diese Schritte nicht aus, ohne zuvor die vom OEM-Anbieter bereitgestellte FRU-Dokumentation zu Rate zu ziehen.

1. Verwenden Sie die Herunterfahren-Aktion, um den Skalierungseinheitknoten ordnungsgemäß herunterzufahren. Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

2. Gesetzt den unwahrscheinlichen Fall, dass das Herunterfahren fehlschlägt, verwenden Sie die Aktion [Ausgleichen](azure-stack-node-actions.md#drain), um den Skalierungseinheitknoten in den Wartungsmodus zu versetzen. Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

   > [!NOTE]  
   > Auf jeden Fall kann nur jeweils ein Knoten deaktiviert und ausgeschaltet werden, ohne dass S2D (Storage Spaces Direct, Direkte Speicherplätze) unterbrochen wird.

3. Nachdem sich der Skalierungseinheitenknoten im Wartungsmodus befindet, verwenden Sie die Aktion [Ausschalten](azure-stack-node-actions.md#scale-unit-node-actions). Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

   > [!NOTE]  
   > Verwenden Sie in dem unwahrscheinlichen Fall, dass die Ausschaltaktion nicht funktioniert, stattdessen die Webbenutzeroberfläche des Baseboard-Verwaltungscontrollers (BMC).

4. Ersetzen Sie die beschädigte Hardwarekomponente. Ob der OEM-Hardwareanbieter den Austausch von Komponenten durchführt, kann auf Basis Ihres Supportvertrags variieren.  
5. Aktualisieren Sie die Firmware. Befolgen Sie den anbieterspezifischen Aktualisierungsprozess für die Firmware mithilfe des Lebenszyklushosts der Hardware, um sicherzustellen, dass auf die ausgetauschte Hardwarekomponente die genehmigte Firmwareebene angewendet wird. Ob der OEM-Hardwareanbieter diesen Schritt durchführt, kann auf Basis Ihres Supportvertrags variieren.  
6. Verwenden Sie die Aktion [Reparieren](azure-stack-node-actions.md#scale-unit-node-actions), um den Skalierungseinheitenknoten wieder in die Skalierungseinheit zu versetzen.
7. Verwenden Sie den privilegierten Endpunkt, um den [Status der Reparatur des virtuellen Datenträgers zu überprüfen](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Bei neuen Datenlaufwerken kann ein vollständiger Speicherreparaturauftrag je nach Systemlast und genutztem Speicherplatz mehrere Stunden dauern.
8. Überprüfen Sie nach Abschluss der Reparaturaktion, ob alle aktiven Warnungen automatisch geschlossen wurden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Ersetzen eines im laufenden Betrieb austauschbaren physischen Datenträgers finden Sie unter [Ersetzen eines Datenträgers](azure-stack-replace-disk.md).
- Informationen zum Ersetzen eines physischen Knotens finden Sie unter [Ersetzen eines Skalierungseinheitenknotens](azure-stack-replace-node.md).
