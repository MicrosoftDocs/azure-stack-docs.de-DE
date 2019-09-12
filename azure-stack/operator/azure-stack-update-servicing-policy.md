---
title: Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack | Microsoft-Dokumentation
description: In diesem Artikel wird das Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack erläutert.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 70fe7c8cdb43f55c8e013dcca27dcde870b1cc9a
ms.sourcegitcommit: dc633e862d49412a963daee481226c1543287e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862966"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack-Wartungsrichtlinie

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird die Wartungsrichtlinie für integrierte Azure Stack-Systeme beschrieben, was Sie tun müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten, und wo Sie Hilfe erhalten.

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

Um weiterhin Support zu erhalten, muss Ihre Azure Stack-Instanz auf dem aktuellen Stand bleiben.

Damit die Azure Stack-Instanz auch weiterhin unterstützt wird, muss diese die zuletzt veröffentlichte Updateversion oder eine der beiden vorherigen Updateversionen ausführen.

Hotfixes werden nicht als wesentliche Updateversionen angesehen. Wenn die Version der Azure Stack-Instanz *mehr als zwei Updates* zurückliegt, wird sie als nicht konform eingestuft. Sie müssen mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten.

Beispiel: Wenn die neueste verfügbare Updateversion 1904 ist und die beiden vorherigen Updatepakete die Versionen 1903 und 1902 waren, besteht für 1902 und 1903 weiterhin Support. Allerdings gibt es für 1901 keinen Support mehr. Die Richtlinie gilt, wenn es für einen oder zwei Monate kein Release gab. Beispiel: Wenn die aktuelle Version 1807 ist und es keine Version 1806 gab, besteht für die vorherigen beiden Updatepakete 1805 und 1804 weiterhin Support.

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das jeweils vorherige Updatepaket bzw. den vorherigen Hotfix voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

## <a name="get-support"></a>Support

Azure Stack folgt demselben Supportprozess wie Azure. Unternehmenskunden können dem beschriebenen in [Gewusst wie: Erstellen einer Azure-Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) beschriebenen Prozess folgen. Wenn Sie Kunde eines Clouddienstanbieters (Cloud Service Provider, CSP) sind, wenden Sie sich an Ihren CSP-Support. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).

# <a name="next-steps"></a>Nächste Schritte

[Verwalten von Updates in Azure Stack](azure-stack-updates.md)
