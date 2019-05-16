---
title: Austauschen eines Skalierungseinheitknotens für ein in Azure Stack integriertes System | Microsoft Docs
description: Erfahren Sie, wie Sie einen physischen Skalierungseinheitknoten in einem in Azure Stack integrierten System austauschen.
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
ms.openlocfilehash: 739e15b2cc95384a164f91f1b1267be256380a43
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984868"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Austauschen eines Skalierungseinheitknotens für ein in Azure Stack integriertes System

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird der allgemeine Vorgang zum Austauschen eines physischen Computers (auch als „Skalierungseinheitknoten“ bezeichnet) für ein in Azure Stack integriertes System beschrieben. Die tatsächlichen Schritte zum Austauschen eines Skalierungseinheitknotens sind abhängig von Ihrem OEM-Hardwareanbieter unterschiedlich. Die für Ihr System spezifischen ausführlichen Schritte finden Sie in der FRU-Dokumentation (Field Replaceable Unit) des Herstellers.

Das folgende Flussdiagramm zeigt den allgemeinen FRU-Vorgang zum Austauschen einer vollständigen Hardwarekomponente.

![Flussdiagramm für den Vorgang des Knotenaustauschs](media/azure-stack-replace-node/replacenodeflow.png)

*Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

> [!Note]  
> Wenn der Vorgang zum Herunterfahren fehlschlägt, empfiehlt es sich, den Ausgleichen-Vorgang, gefolgt vom Beenden-Vorgang zu verwenden. Weitere Details finden Sie unter den verfügbaren Knotenvorgängen.  

## <a name="review-alert-information"></a>Überprüfen von Warnungsinformation

Wenn ein Knoten für Skalierungseinheiten nicht verfügbar ist, erhalten Sie die folgenden kritischen Warnungen:

- Knoten ist nicht mit Netzwerkcontroller verbunden.
- Auf Knoten kann für Platzierung des virtuellen Computers nicht zugegriffen werden.
- Skalierungseinheitknoten ist offline.

![Liste der Warnungen für nicht verfügbare Skalierungseinheit](media/azure-stack-replace-node/nodedownalerts.png)

Wenn Sie die Warnung **Skalierungseinheitknoten ist offline.** öffnen, können Sie der Beschreibung der Warnung den Skalierungseinheitknoten entnehmen, auf den nicht zugegriffen werden kann. Möglicherweise erhalten Sie auch weitere Warnungen in der OEM-spezifischen Überwachungslösung, die auf dem Hardwarelebenszyklushost ausgeführt wird.

![Details der Warnung „Knoten offline“](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Austauschvorgang des Skalierungeinheitknotens

Die folgenden Schritte werden als allgemeine Übersicht zum Austauschvorgang des Skalierungseinheitknotens bereitgestellt. Die für Ihr System spezifischen ausführlichen Schritte finden Sie in der FRU-Dokumentation (Field Replaceable Unit) des OEM-Hardwareherstellers. Befolgen Sie diese Schritte nicht, ohne zuvor die vom OEM-Anbieter bereitgestellte Dokumentation zu Rate gezogen zu haben.

1. Verwenden Sie die **Herunterfahren**-Aktion, um den Skalierungseinheitknoten ordnungsgemäß herunterzufahren. Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich. 

2. Gesetzt den unwahrscheinlichen Fall, dass das Herunterfahren fehlschlägt, verwenden Sie die Aktion [Ausgleichen](azure-stack-node-actions.md#drain), um den Skalierungseinheitknoten in den Wartungsmodus zu versetzen. Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

   > [!NOTE]  
   > Auf jeden Fall kann nur jeweils ein Knoten deaktiviert und ausgeschaltet werden, ohne dass S2D (Storage Spaces Direct, Direkte Speicherplätze) unterbrochen wird.

3. Nachdem sich der Skalierungseinheitenknoten im Wartungsmodus befindet, verwenden Sie die Aktion [Beenden](azure-stack-node-actions.md#stop). Diese Aktion ist abhängig vom physischen Zustand der Hardware möglicherweise nicht erforderlich.

   > [!NOTE]  
   > Verwenden Sie in dem unwahrscheinlichen Fall, dass die Ausschaltaktion nicht funktioniert, stattdessen die Webbenutzeroberfläche des Baseboard-Verwaltungscontrollers (BMC).

4. Tauschen Sie den physischen Computer aus. In der Regel erfolgt dies durch Ihren OEM-Hardwarehersteller.
5. Verwenden Sie die Aktion [Reparieren](azure-stack-node-actions.md#repair), um der Skalierungseinheit den neuen physischen Computer hinzuzufügen.
6. Verwenden Sie den privilegierten Endpunkt, um den [Status der Reparatur des virtuellen Datenträgers zu überprüfen](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Bei neuen Datenlaufwerken kann ein vollständiger Speicherreparaturauftrag je nach Systemlast und genutztem Speicherplatz mehrere Stunden dauern.
7. Überprüfen Sie nach Abschluss der Reparaturaktion, ob alle aktiven Warnungen automatisch geschlossen wurden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Ersetzen eines physischen Datenträgers bei eingeschaltetem System finden Sie unter [Ersetzen eines Datenträgers](azure-stack-replace-disk.md). 
- Informationen zum Ersetzen einer Hardwarekomponente, die das Ausschalten des Systems erfordert, finden Sie unter [Ersetzen einer Hardwarekomponente](azure-stack-replace-component.md).
