---
title: Verwalten von Updates
description: Hier erfahren Sie, wie Sie Updates in Azure Stack Hub verwalten.
author: sethmanheim
ms.topic: how-to
ms.date: 07/16/2020
ms.author: sethm
ms.lastreviewed: 07/16/2020
ms.reviewer: niy
ms.openlocfilehash: b5aa356cfdb77cac5609b7e835add0d32a8e26cf
ms.sourcegitcommit: 52b33ea180c38a5ecce150f5a9ea4a026344cc3d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88073855"
---
# <a name="manage-updates-in-azure-stack-hub"></a>Verwalten von Updates in Azure Stack Hub

Mit vollständigen Updates und Express-Updates, Hotfixes sowie Treiber- und Firmwareupdates vom Originalgerätehersteller (Original Equipment Manufacturer, OEM) kann Azure Stack Hub auf dem aktuellen Stand gehalten werden. In diesem Artikel werden die unterschiedlichen Arten von Updates und die voraussichtlichen Veröffentlichungstermine beschrieben, und Sie erfahren, wo Sie weitere Informationen zum aktuellen Release erhalten.

> [!NOTE]  
> Sie können Azure Stack Hub-Updatepakete nicht auf das Azure Stack Development Kit (ASDK) anwenden. Die Updatepakete sind für integrierte Systeme vorgesehen. Weitere Informationen finden Sie unter [Erneutes Bereitstellen des ASDK](../asdk/asdk-redeploy.md).

## <a name="update-package-types"></a>Updatepakettypen

Es gibt drei Arten von Updatepaketen für integrierte Systeme:

- **Azure Stack Hub-Softwareupdates**. Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack Hub-Features enthalten. Sie laden diese Updatepakete direkt von Microsoft herunter.

    Jedes Updatepaket weist einen bestimmten Typ auf: **Vollständig** oder **Express**.

    Mit **vollständigen** Updatepaketen werden die Betriebssysteme des physischen Hosts in der Skalierungseinheit aktualisiert. Sie erfordern ein längeres Wartungsfenster.

    **Express**-Updatepakete sind bereichsbezogen und führen keine Aktualisierung der zugrunde liegenden Betriebssysteme des physischen Hosts durch.

- **Azure Stack Hub-Hotfixes**. Microsoft stellt [Hotfixes für Azure Stack Hub](azure-stack-servicing-policy.md#hotfixes) zur Verfügung, um ein bestimmtes Problem zu beheben, das häufig hinderlich oder zeitkritisch ist. Jeder Hotfix wird mit einem entsprechenden Microsoft Knowledge Base-Artikel veröffentlicht, der das Problem, die Ursache und die Lösung beschreibt. Hotfixes werden wie die regulären Pakete mit vollständigen Updatepaketen für Azure Stack Hub heruntergeladen und installiert. Hotfixes sind kumulativ und können innerhalb von Minuten installiert werden.

   Ab dem Build 2005 werden bei der Aktualisierung auf eine neue Hauptversion (z. B. von 1.2002.x auf 1.2005.x) die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert. Wenn danach ein Hotfix für Ihren Build veröffentlicht wird, sollten Sie ihn installieren.

- **Updates des OEM-Hardwareanbieters:** Azure Stack Hub-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack Hub-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="when-to-update"></a>Zeitpunkt des Updates

Die drei Updatetypen werden im folgenden Rhythmus veröffentlicht:

- **Azure Stack Hub-Softwareupdates**. Microsoft gibt in der Regel jeden Monat Softwareupdatepakete heraus.

- **Azure Stack Hub-Hotfixes**. Hotfixes sind zeitkritische Releases, die jederzeit veröffentlicht werden können. Bei einem Upgrade von einer Hauptversion auf eine andere Hauptversion (etwa von 1.2002.x auf 1.2005.x) werden die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert.

- **Updates des OEM-Hardwareanbieters**. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Um weiterhin Support zu erhalten, muss Ihre Azure Stack Hub-Umgebung eine unterstützte Azure Stack Hub-Softwareversion aufweisen. Weitere Informationen finden Sie in der [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md).

## <a name="how-to-know-an-update-is-available"></a>Wie erkenne ich, dass ein Update verfügbar ist?

Die Benachrichtigungen über Updates hängen von einigen Faktoren ab, z. B. der Internetverbindung und der Art des Updates.

- **Softwareupdates und Hotfixes von Microsoft**

    Eine Updatewarnung für Microsoft-Softwareupdates und -Hotfixes wird auf dem Blatt **Update** für Azure Stack Hub-Instanzen angezeigt, die mit dem Internet verbunden sind. Wird das Blatt **Update** nicht angezeigt, starten Sie die Controller-VM für die Infrastrukturverwaltung neu.

    Wenn Ihre Instanz nicht verbunden ist und Sie bei jedem Hotfixrelease benachrichtigt werden möchten, abonnieren Sie den [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)- oder [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)-Feed.

- **Updates des OEM-Hardwareanbieters**

    OEM-Updates hängen vom Hersteller ab. Sie müssen einen Kommunikationskanal mit Ihrem OEM einrichten, damit Sie wissen, wann Updates Ihres OEM angewendet werden müssen. Weitere Informationen zu OEMs und zum OEM-Updateprozess finden Sie unter [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub](azure-stack-update-oem.md).

### <a name="major-version-to-major-version"></a>Hauptversion auf Hauptversion

Ein Update von einer Hauptversion auf eine Hauptversion muss Schritt für Schritt erfolgen: Die aktuelle Umgebung kann nur auf die nächste Hauptversion aktualisiert werden, und Sie können kein Hauptversionsupdate überspringen.

Wenn Ihre Azure Stack Hub-Umgebung beispielsweise 1908.x ist, und die neueste verfügbare Updateversion ist 2002.x, sollten Sie von 1908 auf 1910 aktualisieren und dann auf 2002.

Ab dem Build 2005 werden bei der Aktualisierung auf eine neue Hauptversion (z. B. von 1.2002.x auf 1.2005.x) die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert.

### <a name="hotfixes-within-major-versions"></a>Hotfixes innerhalb von Hauptversionen

Innerhalb derselben Hauptversionsnummer kann Azure Stack Hub mehrere Hotfixes freigeben. Hotfixes sind kumulativ. Das neueste Hotfixpaket enthält alle früheren Hotfixes für diese Version. Weitere Informationen finden Sie unter [Hotfixes](azure-stack-servicing-policy.md#hotfixes).

## <a name="update-process"></a>Updateprozess

Wenn Sie wissen, dass ein Update verfügbar ist, können Sie es mit den folgenden Schritten anwenden.

![Azure Stack Hub-Updatevorgang](./media/azure-stack-updates/azure-stack-update-process.svg)

1. **Planen des Updates**

    Bereiten Sie Ihre Azure Stack Hub-Instanz vor, um den Aktualisierungsprozess so reibungslos wie möglich zu gestalten, damit die Benutzer nur minimal beeinträchtigt werden. Informieren Sie Ihre Benutzer über mögliche Dienstausfälle, und befolgen Sie dann die Schritte zum Vorbereiten der Instanz auf das Update. Führen Sie unbedingt alle Schritte der [Azure Stack Hub-Checkliste für Updateaktivitäten](release-notes-checklist.md) aus, um sicherzustellen, dass die Voraussetzungen zum Anwenden eines Updates erfüllt sind. Planen Sie darüber hinaus ein entsprechendes Wartungsfenster für den angewendeten Updatetyp.

2. **Hochladen und Vorbereiten des Updatepakets**

    Bei Azure Stack Hub-Umgebungen mit Internetverbindung werden Azure Stack Hub-Softwareupdates und -Hotfixes automatisch in das System importiert und für das Update vorbereitet.

    Für Azure Stack Hub-Umgebungen ohne Internetverbindung oder mit schwacher oder zeitweiliger Internetkonnektivität werden Updatepakete über das Azure Stack Hub-Administratorportal in den Azure Stack Hub-Speicher importiert. Weitere Schritte zum Hochladen und Vorbereiten des Updatepakets finden Sie unter [Hochladen und Vorbereiten eines Azure Stack Hub-Updatepakets](azure-stack-update-prepare-package.md).

    Alle OEM-Updatepakete werden unabhängig von der Internetkonnektivität Ihres Azure Stack Hub-Systems manuell in Ihre Umgebung importiert. Weitere Schritte zum Importieren und Vorbereiten des Updatepakets finden Sie unter [Hochladen und Vorbereiten eines Azure Stack Hub-Updatepakets](azure-stack-update-prepare-package.md).

3. **Anwenden des Updates**

    Wenden Sie das Update über das Blatt **Update** des Azure Stack Hub-Portals an. Überwachen Sie während des Updatevorgangs den Status, und führen Sie ggf. eine Problembehandlung aus. Weitere Informationen finden Sie unter [Anwenden eines Azure Stack Hub-Updates](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Der Updateressourcenanbieter

Azure Stack Hub umfasst einen Updateressourcenanbieter, der die Anwendung von Microsoft-Softwareupdates verarbeitet. Dieser Anbieter überprüft, ob Updates auf alle physischen Hosts, Service Fabric-Apps und -Laufzeiten sowie alle virtuellen Computer der Infrastruktur und die ihnen zugeordneten Dienste angewendet werden.

Bei der Installation der Updates können Sie den allgemeinen Status anzeigen, während der Updateprozess die verschiedenen Subsysteme in Azure Stack Hub (z. B. physische Hosts und virtuelle Computer der Infrastruktur) erfasst.

## <a name="next-steps"></a>Nächste Schritte

- Um den Updatevorgang zu starten, führen Sie die Schritte unter [Azure Stack Hub-Checkliste für Updateaktivitäten](release-notes-checklist.md) aus.
- Informationen zu den unterstützten Versionen von Azure Stack Hub finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Weitere Informationen zu den aktuellen und letzten Updates finden Sie in den [Azure Stack Hub-Versionshinweisen](release-notes.md).
