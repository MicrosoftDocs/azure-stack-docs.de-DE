---
title: Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub
description: In diesem Artikel wird das Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub erläutert.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppace
ms.openlocfilehash: cbc073150d8da6aa9b7542041b22f176c6147f22
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487531"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack Hub-Wartungsrichtlinie

In diesem Artikel wird die Wartungsrichtlinie für integrierte Azure Stack Hub-Systeme beschrieben, was Sie tun müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten, und wo Sie Hilfe erhalten.

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

Um weiterhin Support zu erhalten, muss Ihre Azure Stack Hub-Instanz auf dem aktuellen Stand bleiben.

Damit das Azure Stack Hub-System auch weiterhin unterstützt wird, muss die Instanz die zuletzt veröffentlichte Updateversion oder eine der beiden vorherigen Updateversionen ausführen.

Hotfixes werden nicht als wesentliche Updateversionen angesehen. Wenn die Version des Azure Stack Hub-Systems *mehr als zwei Updates* zurückliegt, wird sie als nicht konform eingestuft. Sie müssen mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten.

Beispiel: Wenn die neueste verfügbare Updateversion 1904 ist und die beiden vorherigen Updatepakete die Versionen 1903 und 1902 waren, besteht für 1902 und 1903 weiterhin Support. Allerdings gibt es für 1901 keinen Support mehr. Die Richtlinie gilt, wenn es für einen oder zwei Monate kein Release gab. Beispiel: Wenn die aktuelle Version 1807 ist und es keine Version 1806 gab, besteht für die vorherigen beiden Updatepakete 1805 und 1804 weiterhin Support.

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das jeweils vorherige Updatepaket bzw. den vorherigen Hotfix voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

## <a name="get-support"></a>Support

Azure Stack Hub folgt demselben Supportprozess wie Azure. Unternehmenskunden können dem beschriebenen in [Gewusst wie: Erstellen einer Azure-Supportanfrage](/azure/azure-supportability/how-to-create-azure-support-request) beschriebenen Prozess folgen. Wenden Sie sich an Ihren CSP-Support, wenn Sie Kunde eines Cloudlösungsanbieters (Cloud Solution Provider, CSP) sind. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md)
