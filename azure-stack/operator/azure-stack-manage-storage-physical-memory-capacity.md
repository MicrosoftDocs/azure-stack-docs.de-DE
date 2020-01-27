---
title: Verwalten der Kapazität des physischer Speichers in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den physischen Speicher und die Kapazität in Azure Stack Hub überwachen und verwalten können.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 24fdcfc4b28a1c5fb9ebb2d7facb774ec176de05
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535143"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>Verwalten der Kapazität des physischen Speichers in Azure Stack Hub

Sie können zusätzlichen Speicher hinzufügen, um die verfügbare Gesamtspeicherkapazität in Azure Stack Hub zu erhöhen. In Azure Stack Hub wird Ihr physischer Server auch als *Skalierungseinheitenknoten* bezeichnet. Alle Skalierungseinheitknoten, die Mitglieder einer einzelnen Skalierungseinheit sind, müssen über die gleiche Speichermenge verfügen.

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

1. Beenden Sie Azure Stack Hub mit den Schritten, die im Artikel [Starten und Beenden von Azure Stack Hub](azure-stack-start-and-stop.md) dokumentiert sind.
2. Aktualisieren Sie den Speicher auf den einzelnen physischen Computern mithilfe der Dokumentation des Hardwareherstellers.
3. Starten Sie Azure Stack Hub mit den Schritten, die im Artikel [Starten und Beenden von Azure Stack Hub](azure-stack-start-and-stop.md) angegeben sind.

## <a name="next-steps"></a>Nächste Schritte

 - Unter [Verwalten von Speicherkonten in Azure Stack Hub](azure-stack-manage-storage-accounts.md) erhalten Sie Informationen zum Verwalten von Speicherkonten in Azure Stack Hub.
 - Informationen dazu, wie die Speicherkapazität ihrer Azure Stack Hub-Bereitstellung überwacht und verwaltet werden kann, finden Sie unter [Verwalten der Speicherkapazität für Azure Stack Hub](azure-stack-manage-storage-shares.md).
