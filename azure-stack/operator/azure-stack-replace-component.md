---
title: Ersetzen einer Hardwarekomponente auf einem Azure Stack-Skalierungseinheitenknoten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Hardwarekomponente in einem System mit integriertem Azure Stack ersetzen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: ea316ebdba51ea80fcd02382023ccb46447a8cc0
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984829"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Ersetzen einer Hardwarekomponente auf einem Azure Stack-Skalierungseinheitenknoten

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird das allgemeine Verfahren zum Ersetzen von Hardwarekomponenten beschrieben, die nicht im laufenden Betrieb austauschbar sind. Die tatsächlichen Schritte zum Austausch variieren abhängig von Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM). Die für Ihr System mit integriertem Azure Stack spezifischen ausführlichen Schritte finden Sie in der FRU-Dokumentation (Field Replaceable Unit) des Anbieters.

Nicht im laufenden Betrieb austauschbare Komponenten umfassen Folgendes:

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

Das Integritäts- und Überwachungssystem von Azure Stack verfolgt die Integrität von Netzwerkadaptern und Datenlaufwerken nach, die von „Direkte Speicherplätze“ gesteuert werden. Andere Hardwarekomponenten werden nicht nachverfolgt. Für alle anderen Hardwarekomponenten werden Warnungen in der anbieterspezifischen Hardwareüberwachungslösung ausgelöst, die auf dem Lebenszyklushost der Hardware ausgeführt wird.  

## <a name="component-replacement-process"></a>Komponentenaustauschprozess

Die folgenden Schritte ermöglichen eine allgemeine Übersicht über den Komponentenaustauschprozess. Befolgen Sie diese Schritte nicht, ohne zuvor die vom OEM-Anbieter bereitgestellte FRU-Dokumentation zu Rate zu ziehen.

1. Verwenden Sie die Herunterfahren-Aktion, um den Skalierungseinheitknoten ordnungsgemäß herunterzufahren. Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

2. Gesetzt den unwahrscheinlichen Fall, dass das Herunterfahren fehlschlägt, verwenden Sie die Aktion [Ausgleichen](azure-stack-node-actions.md#drain), um den Skalierungseinheitknoten in den Wartungsmodus zu versetzen. Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

   > [!NOTE]  
   > Auf jeden Fall kann nur jeweils ein Knoten deaktiviert und ausgeschaltet werden, ohne dass S2D (Storage Spaces Direct, Direkte Speicherplätze) unterbrochen wird.

3. Nachdem sich der Skalierungseinheitenknoten im Wartungsmodus befindet, verwenden Sie die Aktion [Ausschalten](azure-stack-node-actions.md#scale-unit-node-actions). Diese Aktion ist auf Basis des physischen Zustands der Hardware möglicherweise nicht erforderlich.

   > [!NOTE]  
   > Verwenden Sie in dem unwahrscheinlichen Fall, dass die Ausschaltaktion nicht funktioniert, stattdessen die Webbenutzeroberfläche des Baseboard-Verwaltungscontrollers (BMC).

4. Ersetzen Sie die beschädigte Hardwarekomponente. Ob der OEM-Hardwareanbieter den Austausch von Komponenten durchführt, kann auf Basis Ihres Supportvertrags variieren.  
5. Aktualisieren Sie die Firmware. Befolgen Sie den anbieterspezifischen Aktualisierungsprozess für die Firmware mithilfe des Lebenszyklushosts der Hardware, um sicherzustellen, dass auf die ausgetauschte Hardwarekomponente die genehmigte Firmwareebene angewendet wird. Ob der OEM-Hardwareanbieter diesen Schritt durchführt, kann auf Basis Ihres Supportvertrags variieren.  
6. Verwenden Sie die Aktion [Reparieren](azure-stack-node-actions.md#scale-unit-node-actions), um den Skalierungseinheitenknoten wieder in die Skalierungseinheit zu versetzen.
7. Verwenden Sie den privilegierten Endpunkt, um den [Status der Reparatur des virtuellen Datenträgers zu überprüfen](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Bei neuen Datenlaufwerken kann ein vollständiger Speicherreparaturauftrag je nach Systemlast und genutztem Speicherplatz mehrere Stunden dauern.
8. Überprüfen Sie nach Abschluss der Reparaturaktion, ob alle aktiven Warnungen automatisch geschlossen wurden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Ersetzen eines im laufenden Betrieb austauschbaren physischen Datenträgers finden Sie unter [Ersetzen eines Datenträgers](azure-stack-replace-disk.md).
- Informationen zum Ersetzen eines physischen Knotens finden Sie unter [Ersetzen eines Skalierungseinheitenknotens](azure-stack-replace-node.md).
