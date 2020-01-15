---
title: Verwalten von Updates in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Updates in Azure Stack verwalten.
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
ms.date: 10/01/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: f84a781979deaa8f3879abc2d964599e6f1087ae
ms.sourcegitcommit: b2418661bfa3a791e65b9b487e20982dba3e4c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75756847"
---
# <a name="manage-updates-in-azure-stack-hub-overview"></a>Übersicht über das Verwalten von Updates in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

Mit vollständigen und Express-Updates, Hotfixes und Treiber- und Firmwareupdates vom Originalgerätehersteller (Original Equipment Manufacturer, OEM) kann Azure Stack Hub auf dem aktuellen Stand gehalten werden. In diesem Artikel werden die unterschiedlichen Arten von Updates und die voraussichtlichen Veröffentlichungstermine beschrieben, und Sie erfahren, wo Sie weitere Informationen zum aktuellen Release erhalten.

> [!Note]  
> Sie können Azure Stack Hub-Updatepakete nicht auf das Azure Stack Development Kit (ASDK) anwenden. Die Updatepakete sind für integrierte Systeme vorgesehen. Weitere Informationen finden Sie unter [Erneutes Bereitstellen des ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Updatepakettypen

Es gibt drei Arten von Updatepaketen für integrierte Systeme:

-   **Azure Stack Hub-Softwareupdates**. Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack-Feature enthalten. Sie laden diese Updatepakete direkt von Microsoft herunter.

    Jedes Updatepaket weist einen der Typen **Vollständig** oder **Express** auf. 
 
    Mit **vollständigen** Updatepaketen werden die Betriebssysteme des physischen Hosts in der Skalierungseinheit aktualisiert. Sie erfordern ein längeres Wartungsfenster. 

    **Express**-Updatepakete sind bereichsbezogen und führen keine Aktualisierung der zugrunde liegenden Betriebssysteme des physischen Hosts durch.

-   **Azure Stack Hub-Hotfixes**. Microsoft stellt Hotfixes für Azure Stack zur Verfügung, die ein bestimmtes Problem beheben, das oft präventiv oder zeitkritisch ist. Jeder Hotfix wird mit einem entsprechenden Microsoft Knowledge Base-Artikel veröffentlicht, der das Problem, die Ursache und die Lösung beschreibt. Hotfixes werden wie die regulären Pakete mit vollständigen Updatepaketen für Azure Stack heruntergeladen und installiert. Hotfixes sind kumulativ und können innerhalb von Minuten installiert werden.

-   **Updates des OEM-Hardwareanbieters:** Azure Stack Hub-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack Hub-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="when-to-update"></a>Zeitpunkt des Updates

Die drei Updatetypen werden im folgenden Rhythmus veröffentlicht:

-   **Azure Stack Hub-Softwareupdates**. Microsoft gibt in der Regel jeden Monat Softwareupdatepakete heraus.

-   **Azure Stack Hub-Hotfixes**. Hotfixes sind zeitkritische Releases, die jederzeit veröffentlicht werden können.

-   **Updates des OEM-Hardwareanbieters**. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Um weiterhin Support zu erhalten, muss Ihre Azure Stack-Umgebung eine unterstützte Azure Stack Hub-Softwareversion aufweisen. Weitere Informationen finden Sie in der [Azure Stack Hub-Wartungsrichtlinie](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Benachrichtigungen über Updates

Die Benachrichtigungen über Updates hängen von einigen Faktoren ab, z. B. der Internetverbindung und der Art des Updates.

- **Softwareupdates und Hotfixes von Microsoft** 

    Eine Updatewarnung für Microsoft-Softwareupdates und -Hotfixes wird auf dem Blatt „Update“ für Azure Stack Instanzen angezeigt, die mit dem Internet verbunden sind. Wird das Blatt „Update“ nicht angezeigt, starten Sie die Controller-VM für die Infrastrukturverwaltung neu.

    Wenn Ihre Instanz nicht verbunden ist und Sie bei jedem Hotfixrelease benachrichtigt werden möchten, abonnieren Sie den [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)- oder [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)-Feed.

- **Updates des OEM-Hardwareanbieters**

    OEM-Updates hängen vom Hersteller ab. Sie müssen einen Kommunikationskanal mit Ihrem OEM einrichten, damit Sie wissen, wann Updates Ihres OEM angewendet werden müssen. Weitere Informationen zu OEMs und zum OEM-Updateprozess finden Sie unter [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack](azure-stack-update-oem.md).

## <a name="update-processes"></a>Updateprozesse

Wenn Sie wissen, dass ein Update verfügbar ist, können Sie es mit den folgenden Schritten anwenden.

![Azure Stack Hub-Updatevorgang](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Planen des Updates**

    Bereiten Sie Ihre Azure Stack Hub-Instanz vor, um den Aktualisierungsprozess so reibungslos wie möglich zu gestalten, damit die Benutzer nur minimal beeinträchtigt werden. Informieren Sie Ihre Benutzer über mögliche Dienstausfälle, und befolgen Sie dann die Schritte zum Vorbereiten der Instanz auf das Update. Weitere Schritte zum Planen des Updates finden Sie unter [Azure Stack Hub-Checkliste für Updateaktivitäten](release-notes-checklist.md).

2. **Hochladen und Vorbereiten des Updatepakets**

    Bei Azure Stack Hub-Umgebungen mit Internetverbindung werden Azure Stack Hub-Softwareupdates und -Hotfixes automatisch in das System importiert und für das Update vorbereitet.

    Für Azure Stack Hub-Umgebungen ohne Internetverbindung oder mit schwacher oder zeitweiliger Internetkonnektivität werden die Updatepakete über das Azure Stack Hub-Administratorportal in den Azure Stack-Speicher importiert. Weitere Schritte zum Hochladen und Vorbereiten des Updatepakets finden Sie unter [Hochladen und Vorbereiten eines Azure Stack Hub-Updatepakets](azure-stack-update-prepare-package.md).

    Alle OEM-Updatepakete werden unabhängig von der Internetkonnektivität Ihres Azure Stack Hub-Systems manuell in Ihre Umgebung importiert. Weitere Schritte zum Importieren und Vorbereiten des Updatepakets finden Sie unter [Hochladen und Vorbereiten eines Azure Stack Hub-Updatepakets](azure-stack-update-prepare-package.md).

3. **Anwenden des Updates**

    Wenden Sie das Update an, indem Sie in Azure Stack Hub das Blatt **Update** verwenden. Verfolgen Sie während des Updatevorgangs den Status und die Problembehandlung. Weitere Informationen finden Sie unter [Anwenden eines Azure Stack Hub-Updates](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Der Updateressourcenanbieter

Azure Stack Hub umfasst einen Updateressourcenanbieter, der die Anwendung von Microsoft-Softwareupdates verarbeitet. Dieser Anbieter überprüft, ob Updates auf alle physischen Hosts, Service Fabric-Anwendungen und -Laufzeiten sowie alle virtuellen Computer der Infrastruktur und die ihnen zugeordneten Dienste angewandt werden.

Bei der Installation der Updates können Sie den allgemeinen Status anzeigen, während der Updateprozess die verschiedenen Subsysteme in Azure Stack (z.B. physische Hosts und virtuelle Computer der Infrastruktur) erfasst.

## <a name="next-steps"></a>Nächste Schritte

- Um den Updatevorgang zu starten, führen Sie die Schritte unter [Azure Stack Hub-Checkliste für Updateaktivitäten](release-notes-checklist.md) aus.
- Informationen zu den unterstützten Versionen von Azure Stack finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Weitere Informationen zu den aktuellen und letzten Updates finden Sie in den [Azure Stack Hub-Versionshinweisen](release-notes.md).
