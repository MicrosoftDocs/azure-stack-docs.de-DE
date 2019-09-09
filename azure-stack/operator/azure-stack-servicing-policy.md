---
title: Azure Stack-Wartungsrichtlinie | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zur Azure Stack-Wartungsrichtlinie und erfahren, wie Sie den unterstützten Zustand eines integrierten Systems aufrechterhalten.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1022ab056157ea1a9bc925d3992a99bd0b395a35
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188083"
---
- **Updates des OEM-Hardwareanbieters**. Azure Stack-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="update-package-release-cadence"></a>Updatepaketrelease-Intervall

Microsoft veröffentlicht Softwareupdatepakete in der Regel monatlich. Allerdings ist es möglich, dass in einem Monat mehrere oder keine Updatereleases veröffentlicht werden. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Die Dokumentation zur Planung und Verwaltung von Updates sowie zur Ermittlung Ihrer aktuellen Version finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).

Informationen zu einem bestimmten Update und darüber, wie sie es herunterladen, finden Sie in den Versionshinweisen zu diesem Update:

- [Azure Stack-Update 1908](azure-stack-release-notes-1908.md)
- [Azure Stack-Update 1907](azure-stack-release-notes-1907.md)
- [Azure Stack-Update 1906](azure-stack-release-notes-1906.md)
- [Azure Stack-Update 1905](azure-stack-release-notes-1905.md)

## <a name="hotfixes"></a>Hotfixes

Gelegentlich stellt Microsoft Hotfixes für Azure Stack zur Verfügung, die ein bestimmtes Problem beheben, das oft präventiv oder zeitkritisch ist.  Jeder Hotfix wird mit einem entsprechenden Microsoft Knowledge Base-Artikel veröffentlicht, der das Problem, die Ursache und die Lösung beschreibt.

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird die Wartungsrichtlinie für integrierte Azure Stack-Systeme beschrieben, was Sie tun müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten, und wo Sie Hilfe erhalten.

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

Damit die Azure Stack-Instanz auch weiterhin unterstützt wird, muss diese die zuletzt veröffentlichte Updateversion oder eine der beiden vorherigen Updateversionen ausführen.

Hotfixes werden nicht als wesentliche Updateversionen angesehen. Wenn die Version der Azure Stack-Instanz *mehr als zwei Updates* zurückliegt, wird sie als nicht konform eingestuft. Sie müssen mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten.

Beispiel: Wenn die neueste verfügbare Updateversion 1904 ist und die beiden vorherigen Updatepakete die Versionen 1903 und 1902 waren, besteht für 1902 und 1903 weiterhin Support. Allerdings gibt es für 1901 keinen Support mehr. Die Richtlinie gilt, wenn es für einen oder zwei Monate kein Release gab. Beispiel: Wenn die aktuelle Version 1807 ist und es keine Version 1806 gab, besteht für die vorherigen beiden Updatepakete 1805 und 1804 weiterhin Support.

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das jeweils vorherige Updatepaket bzw. den vorherigen Hotfix voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

## <a name="get-support"></a>Support

Azure Stack folgt demselben Supportprozess wie Azure. Unternehmenskunden können dem beschriebenen in [Gewusst wie: Erstellen einer Azure-Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) beschriebenen Prozess folgen. Wenn Sie Kunde eines Clouddienstanbieters (Cloud Service Provider, CSP) sind, wenden Sie sich an Ihren CSP-Support. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
