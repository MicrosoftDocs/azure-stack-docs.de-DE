---
title: Azure Stack-Wartungsrichtlinie
titleSuffix: Azure Stack
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
ms.date: 10/30/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: b874e81afc37684e15dd9cfe79adbf123f126f89
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780659"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack-Wartungsrichtlinie

Dieser Artikel beschreibt die Wartungsrichtlinie für integrierte Azure Stack-Systeme sowie die Schritte, die Sie ausführen müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten.

## <a name="download-update-packages-for-integrated-systems"></a>Herunterladen der Updatepakete für integrierte Systeme

Microsoft veröffentlicht sowohl vollständige monatliche Updatepakete als auch Hotfixpakete, um spezielle Probleme zu behandeln.

Monatliche Updatepakete werden in einem sicheren Azure-Endpunkt gehostet. Sie können sie manuell mithilfe des [Downloadtools für Azure Stack-Updates](https://aka.ms/azurestackupdatedownload) herunterladen. Wenn Ihre Skalierungseinheit verbunden ist, wird das Update automatisch im Administratorportal als **Update verfügbar** angezeigt. Vollständige, monatlich Updatepakete werden bei jeder Veröffentlichung ausführlich dokumentiert. Weitere Informationen zu jedem Release erhalten Sie, wenn Sie im Abschnitt [Updatepaketrelease-Intervall](#update-package-release-cadence) dieses Artikels auf das jeweilige Release klicken.

Hotfixupdatepakete werden in demselben sicheren Azure-Endpunkt gehostet. Sie können sie mithilfe der eingebetteten Links in jedem der jeweiligen Hotfix-KB-Artikel herunterladen, z. B. [Azure Stack-Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Ähnlich wie bei den vollständigen, monatlichen Updatepaketen können Azure Stack-Betreiber die XML-, BIN- und EXE-Dateien herunterladen und mithilfe des Verfahrens unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md) importieren. Azure Stack-Betreibern mit verbundenen Skalierungseinheiten werden die Hotfixes automatisch im Administratorportal mit der Meldung **Update verfügbar** angezeigt.

Wenn Ihre Skalierungseinheit nicht verbunden ist und Sie bei jedem Hotfixrelease benachrichtigt werden möchten, abonnieren Sie den [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)- oder [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)-Feed, der in jedem Release vermerkt ist.

## <a name="update-package-types"></a>Updatepakettypen

Es gibt zwei Arten von Updatepaketen für integrierte Systeme:

- **Softwareupdates von Microsoft**. Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack-Feature enthalten. Sie können diese Updatepakete direkt von Microsoft herunterladen.

- **Updates des OEM-Hardwareanbieters**. Azure Stack-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="update-package-release-cadence"></a>Updatepaketrelease-Intervall

Microsoft veröffentlicht Softwareupdatepakete in der Regel monatlich. Allerdings ist es möglich, dass in einem Monat mehrere oder keine Updatereleases veröffentlicht werden. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Die Dokumentation zur Planung und Verwaltung von Updates sowie zur Ermittlung Ihrer aktuellen Version finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).

Informationen zu einem bestimmten Update und darüber, wie sie es herunterladen, finden Sie in den Versionshinweisen zu diesem Update:

- [Azure Stack-Update 1910](/azure-stack/operator/release-notes?view=azs-1910)
- [Azure Stack-Update 1908](/azure-stack/operator/release-notes?view=azs-1908)
- [Azure Stack-Update 1907](/azure-stack/operator/release-notes?view=azs-1907)
- [Azure Stack-Update 1906](/azure-stack/operator/release-notes?view=azs-1906)

## <a name="hotfixes"></a>Hotfixes

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Gelegentlich stellt Microsoft Hotfixes für Azure Stack zur Verfügung, um Probleme präventiv oder zeitnah zu beheben. Jeder Hotfix wird mit einem entsprechenden Microsoft Knowledge Base-Artikel veröffentlicht, der das Problem, die Ursache und die Lösung beschreibt.

Hotfixes werden wie die regulären Pakete mit vollständigen Updates für Azure Stack heruntergeladen und installiert. Im Gegensatz zu einem vollständigen Update können Hotfixes jedoch innerhalb von Minuten installiert werden. Azure Stack-Operatoren wird empfohlen, die Wartungsfenster bei der Installation von Hotfixes zu setzen. Hotfixes aktualisieren die Version Ihrer Azure Stack-Cloud, sodass Sie leicht feststellen können, ob der Hotfix angewendet wurde. Für jede Version von Azure Stack, die noch unterstützt wird, wird ein separater Hotfix bereitgestellt. Jeder Fix für eine bestimmte Iteration ist kumulativ und enthält die vorherigen Updates für dieselbe Version. Wenn Sie mehr über die Anwendbarkeit eines bestimmten Hotfixes erfahren möchten, lesen Sie den entsprechenden Knowledge Base-Artikel. Siehe die Links mit den Versionshinweisen im vorherigen Abschnitt.

Informationen zu den derzeit verfügbaren Hotfixes finden Sie in den Versionshinweisen für das jeweilige Update:

- [Azure Stack-Hotfix 1910](/azure-stack/operator/release-notes?view=azs-1910#hotfixes)
- [Azure Stack-Hotfix 1908](/azure-stack/operator/release-notes?view=azs-1908#hotfixes)
- [Azure Stack-Hotfix 1907](/azure-stack/operator/release-notes?view=azs-1907#hotfixes)
- [Azure Stack-Hotfix 1906](/azure-stack/operator/release-notes?view=azs-1906#hotfixes)

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

Damit die Azure Stack-Instanz auch weiterhin unterstützt wird, muss diese die zuletzt veröffentlichte Updateversion oder eine der beiden vorherigen Updateversionen ausführen.

Hotfixes werden nicht als Updatehauptversionen angesehen. Wenn die Version der Azure Stack-Instanz *mehr als zwei Updates* zurückliegt, wird sie als nicht konform eingestuft. Sie müssen mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten.

Beispiel: Wenn die neueste verfügbare Updateversion 1904 ist und die beiden vorherigen Updatepakete die Versionen 1903 und 1902 waren, besteht für 1902 und 1903 weiterhin Support. Allerdings gibt es für 1901 keinen Support mehr. Die Richtlinie gilt, wenn es für einen oder zwei Monate kein Release gab. Beispiel: Wenn die aktuelle Version 1807 ist und es keine Version 1806 gab, besteht für die vorherigen beiden Updatepakete 1805 und 1804 weiterhin Support.

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das jeweils vorherige Updatepaket bzw. den vorherigen Hotfix voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

## <a name="get-support"></a>Support

Azure Stack folgt demselben Supportprozess wie Azure. Unternehmenskunden können dem beschriebenen in [Gewusst wie: Erstellen einer Azure-Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) beschriebenen Prozess folgen. Wenden Sie sich an Ihren CSP-Support, wenn Sie Kunde eines Cloudlösungsanbieters (Cloud Solution Provider, CSP) sind. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack](azure-stack-updates.md)
