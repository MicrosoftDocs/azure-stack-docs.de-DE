---
title: Azure Stack Hub-Wartungsrichtlinie
titleSuffix: Azure Stack Hub
description: Hier erhalten Sie Informationen zur Azure Stack Hub-Wartungsrichtlinie und erfahren, wie Sie den unterstützten Zustand eines integrierten Systems aufrechterhalten.
author: sethmanheim
ms.topic: article
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: f81ca3e48387f806e591deea08c4b0c9c518cebd
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697534"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack Hub-Wartungsrichtlinie

Dieser Artikel beschreibt die Wartungsrichtlinie für integrierte Azure Stack Hub-Systeme sowie die Schritte, die Sie ausführen müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten.

## <a name="download-update-packages-for-integrated-systems"></a>Herunterladen der Updatepakete für integrierte Systeme

Microsoft veröffentlicht sowohl vollständige monatliche Updatepakete als auch Hotfixpakete, um spezielle Probleme zu behandeln.

Monatliche Updatepakete werden in einem sicheren Azure-Endpunkt gehostet. Sie können sie manuell mithilfe des [Downloadtools für Azure Stack Hub-Updates](https://aka.ms/azurestackupdatedownload) herunterladen. Wenn Ihre Skalierungseinheit verbunden ist, wird das Update automatisch im Administratorportal als **Update verfügbar** angezeigt. Vollständige, monatlich Updatepakete werden bei jeder Veröffentlichung ausführlich dokumentiert. Weitere Informationen zu jedem Release erhalten Sie, wenn Sie im Abschnitt [Updatepaketrelease-Intervall](#update-package-release-cadence) dieses Artikels auf das jeweilige Release klicken.

Hotfixupdatepakete werden in demselben sicheren Azure-Endpunkt gehostet. Sie können sie mithilfe der eingebetteten Links in jedem der jeweiligen Hotfix-KB-Artikel herunterladen, z. B. [Azure Stack Hub-Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Ähnlich wie bei den vollständigen, monatlichen Updatepaketen können Azure Stack Hub-Operators die XML-, BIN- und EXE-Dateien herunterladen und mithilfe des Verfahrens unter [Anwenden von Updates in Azure Stack Hub](azure-stack-apply-updates.md) importieren. Azure Stack Hub-Operators mit verbundenen Skalierungseinheiten werden die Hotfixes automatisch im Administratorportal mit der Meldung **Update verfügbar** angezeigt.

Wenn Ihre Skalierungseinheit nicht verbunden ist und Sie bei jedem Hotfixrelease benachrichtigt werden möchten, abonnieren Sie den [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)- oder [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)-Feed, der in jedem Release vermerkt ist.

## <a name="update-package-types"></a>Updatepakettypen

Es gibt zwei Arten von Updatepaketen für integrierte Systeme:

- **Softwareupdates von Microsoft**. Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack Hub-Features enthalten. Sie können diese Updatepakete direkt von Microsoft herunterladen.

- **Updates des OEM-Hardwareanbieters**. Azure Stack Hub-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack Hub-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="update-package-release-cadence"></a>Updatepaketrelease-Intervall

Microsoft veröffentlicht Softwareupdatepakete in der Regel monatlich. Allerdings ist es möglich, dass in einem Monat mehrere oder keine Updatereleases veröffentlicht werden. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Die Dokumentation zur Planung und Verwaltung von Updates sowie zur Ermittlung Ihrer aktuellen Version finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).

Informationen zu einem bestimmten Update und darüber, wie sie es herunterladen, finden Sie in den Versionshinweisen zu diesem Update:

- [Azure Stack Hub 1910-Update](/azure-stack/operator/release-notes?view=azs-1910)
- [Azure Stack Hub 1908-Update](/azure-stack/operator/release-notes?view=azs-1908)
- [Azure Stack Hub 1907-Update](/azure-stack/operator/release-notes?view=azs-1907)
- [Azure Stack Hub 1906-Update](/azure-stack/operator/release-notes?view=azs-1906)

## <a name="hotfixes"></a>Hotfixes

Gelegentlich stellt Microsoft Hotfixes für Azure Stack Hub zur Verfügung, um Probleme präventiv oder zeitnah zu beheben. Jeder Hotfix wird mit einem entsprechenden Microsoft Knowledge Base-Artikel veröffentlicht, der das Problem, die Ursache und die Lösung beschreibt.

Hotfixes werden wie die regulären Pakete mit vollständigen Updates für Azure Stack Hub heruntergeladen und installiert. Im Gegensatz zu einem vollständigen Update können Hotfixes jedoch innerhalb von Minuten installiert werden. Azure Stack Hub-Operators wird empfohlen, die Wartungsfenster bei der Installation von Hotfixes festzulegen. Hotfixes aktualisieren die Version Ihrer Azure Stack Hub-Cloud, sodass Sie leicht feststellen können, ob der Hotfix angewendet wurde. Für jede Version von Azure Stack Hub, die noch unterstützt wird, wird ein separater Hotfix bereitgestellt. Jeder Fix für eine bestimmte Iteration ist kumulativ und enthält die vorherigen Updates für dieselbe Version. Wenn Sie mehr über die Anwendbarkeit eines bestimmten Hotfixes erfahren möchten, lesen Sie den entsprechenden Knowledge Base-Artikel. Siehe die Links mit den Versionshinweisen im vorherigen Abschnitt.

Informationen zu den derzeit verfügbaren Hotfixes finden Sie in den Versionshinweisen für das jeweilige Update:

- [Azure Stack Hub 1910-Hotfix](/azure-stack/operator/release-notes?view=azs-1910#hotfixes)
- [Azure Stack Hub 1908-Hotfix](/azure-stack/operator/release-notes?view=azs-1908#hotfixes-1)
- [Azure Stack Hub 1907-Hotfix](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2)
- [Azure Stack Hub 1906-Hotfix](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3)

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

Damit die Azure Stack Hub-Instanz auch weiterhin unterstützt wird, muss diese die zuletzt veröffentlichte Updateversion oder eine der beiden vorherigen Updateversionen ausführen.

Hotfixes werden nicht als Updatehauptversionen angesehen. Wenn die Version der Azure Stack Hub-Instanz *mehr als zwei Updates* zurückliegt, wird sie als nicht konform eingestuft. Sie müssen mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten.

Beispiel: Wenn die neueste verfügbare Updateversion 1904 ist und die beiden vorherigen Updatepakete die Versionen 1903 und 1902 waren, besteht für 1902 und 1903 weiterhin Support. Allerdings gibt es für 1901 keinen Support mehr. Die Richtlinie gilt, wenn es für einen oder zwei Monate kein Release gab. Beispiel: Wenn die aktuelle Version 1807 ist und es keine Version 1806 gab, besteht für die vorherigen beiden Updatepakete 1805 und 1804 weiterhin Support.

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das jeweils vorherige Updatepaket bzw. den vorherigen Hotfix voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

## <a name="get-support"></a>Support

Azure Stack Hub folgt demselben Supportprozess wie Azure. Unternehmenskunden können dem beschriebenen in [Gewusst wie: Erstellen einer Azure-Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) beschriebenen Prozess folgen. Wenden Sie sich an Ihren CSP-Support, wenn Sie Kunde eines Cloudlösungsanbieters (Cloud Solution Provider, CSP) sind. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).

Informationen zum Beheben von Problemen bei der Aktualisierung finden Sie unter [Bewährte Methoden für die Behandlung von Problemen bei Azure Stack Hub-Patches und -Updates](azure-stack-updates-troubleshoot.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md)
- [Bewährte Methoden für die Behandlung von Problemen bei Azure Stack Hub-Patches und -Updates](azure-stack-updates-troubleshoot.md)
