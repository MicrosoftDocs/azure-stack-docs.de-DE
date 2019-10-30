---
title: Verwalten der Kapazität des physischer Speichers in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den physischen Speicher und die Kapazität in Azure Stack überwachen und verwalten können.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f72ad1b86bf8ef84e64f79603c27d14571b00838
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534151"
---
# <a name="manage-physical-memory-capacity-in-azure-stack"></a>Verwalten der Kapazität des physischen Speichers in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Sie können zusätzlichen Speicher hinzufügen, um die verfügbare Gesamtspeicherkapazität in Azure Stack zu erhöhen. In Azure Stack wird Ihr physischer Server auch als *Skalierungseinheitknoten* bezeichnet. Alle Skalierungseinheitknoten, die Mitglieder einer einzelnen Skalierungseinheit sind, müssen über die gleiche Speichermenge verfügen.

> [!note]  
> Sehen Sie vor dem Fortfahren in der Dokumentation des Hardwareherstellers nach, ob Ihr Hersteller ein Upgrade des physischen Speichers unterstützt. Im Rahmen des Supportvertrags Ihres OEM-Hardwareanbieters kann festgelegt sein, dass der Einbau von physischen Serverracks und Updates der Gerätefirmware vom Anbieter durchgeführt werden müssen.

Im folgenden Flussdiagramm ist der allgemeine Prozess zum Hinzufügen von Speicher zu den einzelnen Skalierungseinheitknoten dargestellt.

![Verfahren zum Hinzufügen von Speicher zu den einzelnen Skalierungseinheitknoten](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Hinzufügen von Speicher zu einem vorhandenen Knoten
Die folgenden Schritte enthalten einen allgemeinen Überblick über den Prozess zum Hinzufügen von Speicher.

> [!Warning]
> Befolgen Sie diese Schritte nicht, ohne zuvor die vom OEM-Anbieter bereitgestellte Dokumentation zu Rate gezogen zu haben.
> 
> [!Warning]
> Die gesamte Skalierungseinheit muss heruntergefahren werden, da ein rollierendes Upgrade für den Speicher nicht unterstützt wird.

1. Beenden Sie Azure Stack mit den Schritten, die im Artikel [Starten und Beenden von Azure Stack](azure-stack-start-and-stop.md) dokumentiert sind.
2. Aktualisieren Sie den Speicher auf den einzelnen physischen Computern mithilfe der Dokumentation des Hardwareherstellers.
3. Starten Sie Azure Stack mit den Schritten, die im Artikel [Starten und Beenden von Azure Stack](azure-stack-start-and-stop.md) angegeben sind.

## <a name="next-steps"></a>Nächste Schritte

 - Unter [Verwalten von Speicherkonten in Azure Stack](azure-stack-manage-storage-accounts.md) erhalten Sie Informationen zum Verwalten von Speicherkonten in Azure Stack.
 - Informationen dazu, wie die Speicherkapazität ihrer Azure Stack-Bereitstellung überwacht und verwaltet werden kann, finden Sie unter [Verwalten der Speicherkapazität für Azure Stack](azure-stack-manage-storage-shares.md).
