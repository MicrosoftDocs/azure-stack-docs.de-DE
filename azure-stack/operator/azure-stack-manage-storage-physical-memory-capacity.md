---
title: Verwalten der Kapazität des physischer Speichers für Azure Stack | Microsoft-Dokumentation
description: Überwachen und verwalten Sie den verfügbaren Speicherplatz für Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e81a02c7d9384cc50c9a8fe1afcc519475ca9d77
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985103"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Verwalten der Kapazität des physischen Speichers für Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Sie können zusätzlichen Speicher hinzufügen, um die verfügbare Gesamtspeicherkapazität für Azure Stack zu erhöhen. In Azure Stack wird Ihr physischer Server auch als *Skalierungseinheitknoten* bezeichnet. Alle Skalierungseinheitknoten, die Mitglieder einer einzelnen Skalierungseinheit sind, müssen über die gleiche Speichermenge verfügen.

> [!note]  
> Sehen Sie vor dem Fortfahren in der Dokumentation des Hardwareherstellers nach, ob Ihr Hersteller ein Upgrade des physischen Speichers unterstützt. Im Rahmen des Supportvertrags Ihres OEM-Hardwareanbieters kann festgelegt sein, dass der Einbau von physischen Serverracks und Updates der Gerätefirmware vom Anbieter durchgeführt werden müssen.

Im folgenden Flussdiagramm ist der allgemeine Prozess zum Hinzufügen von Speicher zu den einzelnen Skalierungseinheitknoten dargestellt.

![Hinzufügen von Speicher zu den einzelnen Skalierungseinheitknoten](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

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

 - Informationen zur Verwaltung von Speicherkonten in Azure Stack, um Speicherkapazität basierend auf Geschäftsanforderungen zu suchen, wiederherzustellen und freizugeben, finden Sie unter [Verwalten von Speicherkonten in Azure Stack](azure-stack-manage-storage-accounts.md).
 - Informationen dazu, wie Azure Stack-Cloudbetreiber die Speicherkapazität ihrer Azure Stack-Bereitstellung überwachen und verwalten, finden Sie unter [Verwalten der Speicherkapazität für Azure Stack](azure-stack-manage-storage-shares.md). 
