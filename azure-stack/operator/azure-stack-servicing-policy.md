---
title: Azure Stack Hub-Wartungsrichtlinie
titleSuffix: Azure Stack Hub
description: Hier erhalten Sie Informationen zur Azure Stack Hub-Wartungsrichtlinie und erfahren, wie Sie den unterstützten Zustand eines integrierten Systems aufrechterhalten.
author: sethmanheim
ms.topic: article
ms.date: 09/25/2020
ms.author: sethm
ms.reviewer: niy
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: ac110a3ee90cd0b1439e34a418bf7473b8487502
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899787"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack Hub-Wartungsrichtlinie

Dieser Artikel beschreibt die Wartungsrichtlinie für integrierte Azure Stack Hub-Systeme sowie die Schritte, die Sie ausführen müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten.

## <a name="download-update-packages-for-integrated-systems"></a>Herunterladen der Updatepakete für integrierte Systeme

Microsoft veröffentlicht sowohl vollständige Updatepakete als auch Hotfixpakete, um spezielle Probleme zu behandeln.

Vollständige Updatepakete werden an einem sicheren Azure-Endpunkt gehostet. Sie können sie manuell mithilfe des [Downloadtools für Azure Stack Hub-Updates](https://aka.ms/azurestackupdatedownload) herunterladen. Wenn Ihre Skalierungseinheit verbunden ist, wird das Update automatisch im Administratorportal als **Update verfügbar** angezeigt. Weitere Informationen zu jedem Release erhalten Sie, wenn Sie im Abschnitt [Updatepaketrelease-Intervall](#update-package-release-cadence) dieses Artikels auf das jeweilige Release klicken.

Hotfixupdatepakete werden in demselben sicheren Azure-Endpunkt gehostet. Sie können sie mithilfe der eingebetteten Links in jedem der jeweiligen Hotfix-KB-Artikel herunterladen, z. B. [Azure Stack Hub-Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Ähnlich wie bei den vollständigen, monatlichen Updatepaketen können Azure Stack Hub-Betreiber die XML- und ZIP-Dateien herunterladen und mithilfe des Verfahrens unter [Anwenden von Updates in Azure Stack Hub](azure-stack-apply-updates.md) importieren. Azure Stack Hub-Operators mit verbundenen Skalierungseinheiten werden die Hotfixes automatisch im Administratorportal mit der Meldung **Update verfügbar** angezeigt.

Wenn Ihre Skalierungseinheit nicht verbunden ist und Sie bei jedem Hotfixrelease benachrichtigt werden möchten, abonnieren Sie den [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)- oder [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)-Feed, der in jedem Release vermerkt ist.

## <a name="update-package-types"></a>Updatepakettypen

Es gibt zwei Arten von Updatepaketen für integrierte Systeme:

- **Softwareupdates von Microsoft**. Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack Hub-Features enthalten. Sie können diese Updatepakete direkt von Microsoft herunterladen.

- **Updates des OEM-Hardwareanbieters**. Azure Stack Hub-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack Hub-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="update-package-release-cadence"></a>Updatepaketrelease-Intervall

Microsoft veröffentlicht in der Regel mehrmals im Jahr Softwareupdatepakete.

OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf. Wenden Sie sich an Ihren OEM, um die neuesten Updates Ihrer Hardware zu erhalten.

Die Dokumentation zur Planung und Verwaltung von Updates sowie zur Ermittlung Ihrer aktuellen Version finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).

Informationen zu einem bestimmten Update und darüber, wie sie es herunterladen, finden Sie in den Versionshinweisen zu diesem Update:

- [Azure Stack Hub 2005-Update](./release-notes.md?view=azs-2005)
- [Azure Stack Hub 2002-Update](./release-notes.md?view=azs-2002)
- [Azure Stack Hub 1910-Update](./release-notes.md?view=azs-1910)

## <a name="hotfixes"></a>Hotfixes

Gelegentlich stellt Microsoft Hotfixes für Azure Stack Hub zur Verfügung, um Probleme präventiv oder zeitnah zu beheben. Jeder Hotfix wird mit einem entsprechenden Microsoft KB-Artikel (Knowledge Base) veröffentlicht, der die in dem Hotfix behandelten Probleme detailliert beschreibt.

Ab dem Build 2005 werden bei der Aktualisierung auf eine neue Hauptversion (z. B. von 1.2002.x auf 1.2005.x) die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert. Wenn danach ein Hotfix für Ihren Build veröffentlicht wird, sollten Sie ihn installieren.

Hotfixes werden wie die regulären Pakete mit vollständigen Updates für Azure Stack Hub heruntergeladen und installiert. Im Gegensatz zu einem vollständigen Update können Hotfixes jedoch innerhalb von Minuten installiert werden. Azure Stack Hub-Operators wird empfohlen, die Wartungsfenster bei der Installation von Hotfixes festzulegen. Hotfixes aktualisieren die Version Ihrer Azure Stack Hub-Cloud, sodass Sie leicht feststellen können, ob der Hotfix angewendet wurde. Für jede Version von Azure Stack Hub, die noch unterstützt wird, wird ein separater Hotfix bereitgestellt. **Jeder Hotfix für eine bestimmte Iteration ist kumulativ und enthält die vorherigen Hotfixes für dieselbe Version.** Wenn Sie mehr über die Anwendbarkeit eines bestimmten Hotfixes erfahren möchten, lesen Sie den entsprechenden KB-Artikel. Siehe die Links mit den Versionshinweisen im vorherigen Abschnitt.

Informationen zu den derzeit verfügbaren Hotfixes finden Sie in den Versionshinweisen für das jeweilige Update:

- [Azure Stack Hub 2005-Hotfix](./release-notes.md?view=azs-2005#hotfixes)
- [Azure Stack Hub 2002-Hotfix](./release-notes.md?view=azs-2002#hotfixes-1)
- [Azure Stack Hub 1910-Hotfix](./release-notes.md?view=azs-1910#hotfixes-2)

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

> [!IMPORTANT]  
> Mit dem [Release 2002](release-notes.md?view=azs-2002) von Azure Stack Hub und zur Unterstützung unserer Kunden auf der ganzen Welt, die auf COVID-19 reagieren und ggf. wichtige Entscheidungen für ihre Azure Stack Hub-Systeme treffen, hat Microsoft seine Supportrichtlinie vorübergehend um drei frühere Updateversionen (N-3) erweitert. Mit dem Release 2005 setzen wir diese Erweiterung für weitere 45 Tage fort (bis zum 25. September 2020). In diesem Rahmen werden das neu veröffentlichte Update 2005 sowie die drei letzten Updateversionen (2002, 1910 und 1908 bzw. N-3) unterstützt. Nach diesen 45 Tagen (nach dem 25. September 2020) kehren wir zu unserer Standardsupportrichtlinie zurück, sodass dann die Versionen 2005, 2002 und 1910 bzw. N-2 unterstützt werden.

Damit die Azure Stack Hub-Instanz auch weiterhin unterstützt wird, muss diese die zuletzt veröffentlichte Updateversion oder eine der beiden vorherigen Updateversionen ausführen.

Hotfixes werden nicht als Updatehauptversionen angesehen. Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten.

Beispiel: Wenn die neueste verfügbare Updateversion 1904 ist und die beiden vorherigen Updatepakete die Versionen 1903 und 1902 waren, besteht für 1902 und 1903 weiterhin Support. Allerdings gibt es für 1901 keinen Support mehr. Die Richtlinie gilt, wenn es für einen oder zwei Monate kein Release gab. Beispiel: Wenn die aktuelle Version 1807 ist und es keine Version 1806 gab, besteht für die vorherigen beiden Updatepakete 1805 und 1804 weiterhin Support.

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das jeweils vorherige Updatepaket bzw. den vorherigen Hotfix voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

### <a name="resource-provider-version-support"></a>Ressourcenanbieter-Versionsunterstützung

Für Azure Stack Hub-Ressourcenanbieter ist es wichtig zu beachten, dass nur die zuletzt veröffentlichte Version eines bestimmten Ressourcenanbieters unterstützt wird, die mit Ihrer unterstützten Version von Azure Stack Hub kompatibel ist, auch wenn Sie möglicherweise eine ältere Version von Azure Stack Hub verwenden, die sich noch im Supportfenster befindet.

Weitere Informationen zur Kompatibilität von Ressourcenanbietern finden Sie in den Anmerkungen zu dieser Version für den jeweiligen Ressourcenanbieter.

## <a name="get-support"></a>Support

Azure Stack Hub folgt demselben Supportprozess wie Azure. Unternehmenskunden können dem beschriebenen in [Gewusst wie: Erstellen einer Azure-Supportanfrage](/azure/azure-supportability/how-to-create-azure-support-request) beschriebenen Prozess folgen. Wenden Sie sich an Ihren CSP-Support, wenn Sie Kunde eines Cloudlösungsanbieters (Cloud Solution Provider, CSP) sind. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).

Informationen zum Beheben von Problemen bei der Aktualisierung finden Sie unter [Bewährte Methoden für die Behandlung von Problemen bei Azure Stack Hub-Patches und -Updates](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md)
- [Bewährte Methoden für die Behandlung von Problemen bei Azure Stack Hub-Patches und -Updates](azure-stack-troubleshooting.md)