---
title: Versionshinweise zu Azure Stack 1907 | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen im Update 1907 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 08/30/2019
monikerRange: azs-1907
ms.openlocfilehash: 4dc9c69479066aad3f2b4ebd08646e09da89ba3a
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188120"
---
# <a name="azure-stack-1907-update"></a>Azure Stack-Update 1907

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird der Inhalt des Updatepakets 1907 beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack.

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1907 ist **1.1907.0.20**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack 1907-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Auf der Grundlage interner Tests lässt sich sagen, dass das Update 1907 etwa 13 Stunden dauert.

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl.
- Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1907 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neuigkeiten

<!-- What's new, also net new experiences and features. -->

- Allgemein verfügbare Version des Azure Stack-Sammlungsdiensts für Diagnoseprotokolle, um die Sammlung von Diagnoseprotokollen zu vereinfachen und zu verbessern. Der Azure Stack-Sammlungsdienst für Diagnoseprotokolle stellt ein vereinfachtes Verfahren zum Sammeln und Teilen von Diagnoseprotokollen mit Microsoft Customer Support Services (CSS) zur Verfügung. Dieser Sammlungsdienst für Diagnoseprotokolle bietet eine neue Benutzeroberfläche im Azure Stack-Verwaltungsportal, mit der Operatoren beim Erreichen bestimmter kritischer Warnschwellen Diagnoseprotokolle automatisch auf einen Speicherblob hochladen oder den gleichen Vorgang auf Anforderung ausführen können. Weitere Informationen finden Sie im Artikel [Sammlung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview.md).

- Allgemein verfügbare Version der Azure Stack-Netzwerkinfrastruktur-Überprüfung als Teil des Azure Stack-Überprüfungstools **Test-AzureStack**. Die Azure Stack-Netzwerkinfrastrukur stellt einen Teil von **Test-AzureStack** dar und ermöglicht das Bestimmen eines Fehlers in der Netzwerkinfrastruktur von Azure Stack. Der Test überprüft die Konnektivität der Netzwerkinfrastruktur, indem er das softwaredefinierte Netzwerk von Azure Stack umgeht. Er veranschaulicht Verbindungen von einer öffentlichen VIP mit den konfigurierten DNS-Forwardern, NTP-Servern und Identitätsendpunkten. Außerdem überprüft er die Konnektivität mit Azure, wenn Azure AD als Identitätsanbieter verwendet wird, oder mit dem Partnerserver, wenn ADFS verwendet wird. Weitere Informationen finden Sie im Artikel zum [Azure Stack-Überprüfungstool](azure-stack-diagnostic-test.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne SQL-TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Auf dem Azure Stack-Updateblatt wird jetzt eine Zeit für den **Letzten abgeschlossenen Schritt** für aktive Updates angezeigt. Diese kann angezeigt werden, indem auf dem Updateblatt auf ein laufendes Update geklickt wird. Der **Letzte abgeschlossene Schritt** ist dann im Abschnitt **Update-Ausführungsdetails** verfügbar.

- Verbesserungen an den Operatoraktionen **Start-AzureStack** und **Stop-AzureStack**. Die für den Start von Azure Stack erforderliche Zeit wurde um durchschnittlich 50 % reduziert. Die für das Herunterfahren von Azure Stack erforderliche Zeit wurde um durchschnittlich 30 % reduziert. Die durchschnittlichen Zeiten für Start und Herunterfahren bleiben gleich, wenn sich die Anzahl der Knoten in einer Skalierungseinheit erhöht.

- Verbesserte Fehlerbehandlung für das Marketplace-Tool bei getrennter Verbindung. Bei einem Downloadfehler oder einem Teilerfolg wird bei der Verwendung von **Export-AzSOfflineMarketplaceItem** eine detaillierte Fehlermeldung mit weiteren Details zum Fehler und Schritten zur Entschärfung angezeigt, sofern vorhanden.

- Verbesserte Leistung bei der Erstellung verwalteter Datenträger aus einem großen Seitenblob/einer großen Momentaufnahme. Bisher wurde beim Erstellen eines großen Datenträgers ein Zeitlimit ausgelöst.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Verbesserte Integritätsprüfung von virtuellen Datenträgern vor dem Herunterfahren eines Knotens, um ein unerwartetes Trennen virtueller Datenträger zu verhindern.

- Verbesserte Speicherung interner Protokolle für Administratorvorgänge. Dies führt zu einer verbesserten Leistung und Zuverlässigkeit bei Administratorvorgängen, indem die Arbeitsspeicher- und Speicherplatznutzung durch interne Protokollprozesse minimiert wird. Möglicherweise bemerken Sie darüber hinaus verbesserte Seitenladezeiten des Updateblatts im Verwaltungsportal. Im Rahmen dieser Verbesserung stehen Updateprotokolle, die älter als 6 Monate sind, im System nicht mehr zur Verfügung. Wenn Sie Protokolle für diese Updates benötigen, achten Sie darauf, dass Sie für alle Updateausführungen, die älter als 6 Monate sind, [die Zusammenfassung herunterladen](azure-stack-apply-updates.md), bevor Sie das Update 1907 durchführen.

### <a name="changes"></a>Änderungen

- Version 1907 von Azure Stack enthält eine Warnmeldung, in der Operatoren darauf hingewiesen werden, dass sie das OEM-Paket des Systems auf Version 2.1 oder höher aktualisieren müssen, bevor sie ein Update auf Version 1908 ausführen können. Weitere Informationen zum Anwenden von Azure Stack-OEM-Updates finden Sie unter [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md) (Anwenden von Azure Stack-OEM-Updates).

- Es wurde eine neue Ausgangsregel (HTTPS) hinzugefügt, um dem Azure Stack-Sammlungsdienst für Diagnoseprotokolle die Kommunikation zu ermöglichen. Weitere Informationen finden Sie unter [Integration des Azure Stack-Rechenzentrums – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Der Infrastruktur-Sicherungsdienst löscht jetzt teilweise hochgeladene Sicherungen, wenn der externe Speicherort an seine Kapazitätsgrenze stößt.

- Infrastruktursicherungen enthalten keine Sicherung von Domänendienstdaten mehr. Dies betrifft nur Systeme, die Azure Active Directory als Identitätsanbieter verwenden.

- Wir überprüfen jetzt, ob ein Image, das auf dem Blatt **Compute -> VM-Images** erfasst wird, den Typ Seitenblob aufweist.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Es wurde ein Problem behoben, bei dem für Herausgeber, Angebote und SKUs in Resource Manager-Vorlagen Groß-/Kleinschreibung unterschieden wurde: Das Image wurde nicht für die Bereitstellung abgerufen, wenn die Imageparameter nicht die identische Groß-/Kleinschreibung wie Herausgeber, Angebot und SKU aufwiesen.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Es wurde ein Problem mit Fehlern bei Sicherungen mit einer Fehlermeldung **PartialSucceeded** behoben, bei denen während der Sicherung von Metadaten des Speicherdiensts ein Zeitlimit auftrat.  

- Es wurde ein Problem behoben, bei dem das Löschen von Benutzerabonnements zu verwaisten Ressourcen führte.

- Es wurde ein Problem behoben, bei dem das Beschreibungsfeld beim Erstellen eines Angebots nicht gespeichert wurde.

- Es wurde ein Problem behoben, bei dem ein Benutzer mit **Nur Lesen**-Berechtigungen Ressourcen erstellen, bearbeiten und löschen konnte. Nun kann der Benutzer nur dann Ressourcen erstellen, wenn die Berechtigung **Mitwirkender** zugewiesen ist. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Es wurde ein Problem behoben, bei dem aufgrund einer vom WMI-Anbieterhost gesperrten DLL-Datei ein Fehler beim Update auftritt.

- Es wurde ein Problem beim Updatedienst behoben, das die Anzeige von verfügbaren Updates auf der Updatekachel oder im Ressourcenanbieter verhinderte. Dieses Problem wurde in Version 1906 gefunden und mit dem Hotfix [KB4511282](https://support.microsoft.com/help/4511282/) behoben.

- Es wurde ein Problem behoben, das zu Updatefehlern führen konnte, weil die Verwaltungsebene aufgrund einer falschen Konfiguration fehlerhaft wurde. Dieses Problem wurde in Version 1906 gefunden und mit dem Hotfix [KB4512794](https://support.microsoft.com/help/4512794/) behoben.

- Es wurde ein Problem behoben, das Benutzer daran hinderte, die Bereitstellung von Images von Drittanbietern aus dem Marketplace abzuschließen. Dieses Problem wurde in Version 1906 gefunden und mit dem Hotfix [KB4511259](https://support.microsoft.com/help/4511259/) behoben.

- Es wurde ein Problem behoben, das dazu führen konnte, dass aufgrund des Absturzes des Benutzerimage-Managerdiensts Fehler bei der Erstellung von VMs aus verwalteten Images auftraten. Dieses Problem wurde in Version 1906 gefunden und mit dem Hotfix [KB4512794](https://support.microsoft.com/help/4512794/) behoben.

- Es wurde ein Problem behoben, bei dem bei VM-CRUD-Vorgängen ein Fehler auftreten konnte, weil der Cache des App-Gateways nicht wie erwartet aktualisiert wurde. Dieses Problem wurde in Version 1906 gefunden und mit dem Hotfix [KB4513119](https://support.microsoft.com/en-us/help/4513119/) behoben.

- Es wurde ein Problem beim Integritätsressourcenanbieter behoben, das sich auf die Verfügbarkeit der Blätter „Region“ und „Benachrichtigung“ im Verwaltungsportal auswirkte. Dieses Problem wurde in Version 1906 gefunden und mit dem Hotfix [KB4512794](https://support.microsoft.com/help/4512794) behoben.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](azure-stack-release-notes-known-issues-1907.md)
- [Sicherheitsupdates](azure-stack-release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1907 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1906 installieren, bevor Sie Azure Stack auf 1907 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1907-update"></a>Vor dem Anwenden des Updates 1907

Version 1907 von Azure Stack muss auf Version 1906 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1906.12.54](https://support.microsoft.com/help/4518365)

### <a name="after-successfully-applying-the-1907-update"></a>Nach erfolgreicher Anwendung des Updates 1907

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1907.12.44](https://support.microsoft.com/help/4517473)

## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Systeme, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivierte Versionshinweise

Sie können [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) anzeigen. Diese archivierten Versionshinweise werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack-Support finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).
