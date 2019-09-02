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
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0cd83c7a16ef56e3432de7dcba39cc11ca20a379
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008476"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Übersicht zum Verwalten von Updates in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Sie müssen Azure Stack auf dem neuesten Stand halten, indem Sie vollständige und Express-Updates, Hotfixes und OEM-Paketupdates (Originalgereätehersteller) auf Azure Stack anwenden. Indem Sie Azure Stack mit den aktuellen Updates auf dem neuesten Stand halten, werden jeweils die neuesten Sicherheitspatches sowie Produkt-, Treiber- und Firmwareupdates auf Ihr System angewandt. Dieser Artikel bietet eine Übersicht über die verschiedenen Updatepakete, den Rhythmus für diese Releasepakete und zum allgemeinen Aktualisierungsprozess sowie dazu, wo Sie Informationen zu aktuellen Releases finden.

> [!Note]  
> Sie können Azure Stack-Updatepakete nicht auf Azure Stack Development Kit (ASDK) anwenden. Die Updatepakete sind für integrierte Systeme vorgesehen. Weitere Informationen finden Sie unter [Erneutes Bereitstellen des ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Updatepakettypen

Es gibt drei Arten von Updatepaketen für integrierte Systeme:

-   **Azure Stack-Softwareupdates:** Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack-Feature enthalten. Sie laden diese Updatepakete direkt von Microsoft herunter.

    Jedes Updatepaket weist einen der Typen **Vollständig** oder **Express** auf. 
 
    Mit **vollständigen** Updatepaketen werden die Betriebssysteme des physischen Hosts in der Skalierungseinheit aktualisiert. Sie erfordern ein längeres Wartungsfenster. 

    **Express**-Updatepakete sind bereichsbezogen und führen keine Aktualisierung der zugrunde liegenden Betriebssysteme des physischen Hosts durch.

-   **Azure Stack-Hotfixes:** Microsoft stellt Hotfixes für Azure Stack zur Verfügung, die ein bestimmtes Problem beheben, das oft präventiv oder zeitkritisch ist. Jeder Hotfix wird mit einem entsprechenden Microsoft Knowledge Base-Artikel veröffentlicht, der das Problem, die Ursache und die Lösung beschreibt. Hotfixes werden wie die regulären Pakete mit vollständigen Updatepaketen für Azure Stack heruntergeladen und installiert. Hotfixes sind kumulativ und können innerhalb von Minuten installiert werden.

-   **Updates des OEM-Hardwareanbieters:** Azure Stack-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="when-to-update"></a>Zeitpunkt des Updates

Die drei Updatetypen werden im folgenden Rhythmus veröffentlicht:

-   **Azure Stack-Softwareupdates:** Microsoft gibt in der Regel jeden Monat Softwareupdatepakete heraus.

-   **Azure Stack-Hotfixes:** Hotfixes sind zeitkritische Releases, die jederzeit veröffentlicht werden können.

-   **Updates des OEM-Hardwareanbieters**. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Um weiterhin Support zu erhalten, muss Ihre Azure Stack-Umgebung eine unterstützte Azure Stack Softwareversion aufweisen. Weitere Informationen finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Benachrichtigungen über Updates

Die Benachrichtigungen über Updates hängen von einigen Faktoren ab, z. B. der Internetverbindung und der Art des Updates.

- **Softwareupdates und Hotfixes von Microsoft** 

    Eine Updatewarnung für Microsoft-Softwareupdates und -Hotfixes wird auf dem Blatt „Update“ für Azure Stack Instanzen angezeigt, die mit dem Internet verbunden sind.

    Wenn Ihre Instanz nicht verbunden ist und Sie bei jedem Hotfixrelease benachrichtigt werden möchten, abonnieren Sie den [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/rss)- oder [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/atom)-Feed.

- **Updates des OEM-Hardwareanbieters**

    OEM-Updates hängen vom Hersteller ab. Sie müssen einen Kommunikationskanal mit Ihrem OEM einrichten, damit Sie wissen, wann Updates Ihres OEM angewandt werden müssen. Weitere Informationen zu OEMs und zum OEM-Updateprozess finden Sie unter [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack](azure-stack-update-oem.md).

## <a name="update-processes"></a>Updateprozesse

Wenn Sie wissen, dass ein Update verfügbar ist, wenden Sie das Update anhand der folgenden Schritte an.

![Azure Stack-Updatevorgang](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Planen des Updates**

    Bereiten Sie Ihre Azure Stack-Instanz vor, um den Aktualisierungsprozess so reibungslos wie möglich zu gestalten, damit die Benutzer nur minimal beeinträchtigt werden. Informieren Sie Ihre Benutzer über mögliche Dienstausfälle, und befolgen Sie dann die Schritte zum Vorbereiten der Instanz auf das Update. Weitere Schritte zum Planen des Updates finden Sie unter [Planen eines Azure Stack-Updates](azure-stack-update-plan.md).

2. **Hochladen und Vorbereiten des Updatepakets**

    Bei Azure Stack-Umgebungen mit Internetverbindung werden Azure Stack-Softwareupdates und -Hotfixes automatisch in das System importiert und für das Update vorbereitet.

    Für Azure Stack-Umgebungen ohne Internetverbindung oder mit schwacher oder zeitweiliger Internetkonnektivität werden die Updatepakete über das Azure Stack-Administratorportal in den Azure Stack-Speicher importiert. Weitere Schritte zum Hochladen und Vorbereiten des Updatepakets finden Sie unter [Hochladen und Vorbereiten eines Azure Stack-Updatepakets](azure-stack-update-prepare-package.md).

    Alle OEM-Updatepakete werden unabhängig von der Internetkonnektivität Ihres Azure Stack-Systems manuell in Ihre Umgebung importiert. Weitere Schritte zum Importieren und Vorbereiten des Updatepakets finden Sie unter [Hochladen und Vorbereiten eines Azure Stack-Updatepakets](azure-stack-update-prepare-package.md).

3. **Anwenden des Updates**

    Sie wenden das Update über das Blatt **Update** in Azure Stack an. Überwachen Sie während des Updates den Updatefortschritt, und beheben Sie ggf. Probleme beim Updatevorgang. Weitere Schritte zum Planen des Updates finden Sie unter [Anwenden eines Azure Stack-Updates](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Der Updateressourcenanbieter

Azure Stack umfasst einen Updateressourcenanbieter, der die Anwendung von Microsoft-Softwareupdates verarbeitet. Dieser Anbieter überprüft, ob Updates auf alle physischen Hosts, Service Fabric-Anwendungen und -Laufzeiten sowie alle virtuellen Computer der Infrastruktur und die ihnen zugeordneten Dienste angewandt werden.

Bei der Installation der Updates können Sie den allgemeinen Status anzeigen, während der Updateprozess die verschiedenen Subsysteme in Azure Stack (z.B. physische Hosts und virtuelle Computer der Infrastruktur) erfasst.

## <a name="next-steps"></a>Nächste Schritte

- Um den Updatevorgang zu starten, führen Sie die Schritte unter [Planen eines Azure Stack-Updates](azure-stack-update-plan.md) aus.
- Informationen zu den unterstützten Versionen von Azure Stack finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Weitere Informationen zu den aktuellen und letzten Updates finden Sie in den [Azure Stack-Versionshinweisen](azure-stack-release-notes-security-updates-1907.md).
