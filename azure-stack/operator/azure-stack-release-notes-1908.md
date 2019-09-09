---
title: Versionshinweise zu Azure Stack 1908 | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen im Update 1908 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
monikerRange: azs-1908
ms.openlocfilehash: fbf4983f6f7d2f88da70552ae182792ee316994a
ms.sourcegitcommit: 314fd74caf356b157583d38d2b8b1dee30408b7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234959"
---
# <a name="azure-stack-1908-update"></a>Azure Stack-Update 1908

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird der Inhalt des Updatepakets 1908 beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack.

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="previous-release-notes"></a>Vorherige Versionshinweise

Ab Version 1908 werden die vorherigen Versionen der Versionshinweise links im Inhaltsverzeichnis nicht mehr angezeigt. Wählen Sie zum Zugreifen auf frühere Versionen der Versionshinweise einen anderen Artikel (z. B. [Azure Stack-Übersicht](azure-stack-overview.md)) und dann in der Versionsauswahl oben im Inhaltsverzeichnis auf der linken Seite die Version 1905, 1906, 1907 oder 1908 aus. Frühere Versionen von Versionshinweisen finden Sie im Abschnitt [Archivierte Versionshinweise](#archived-release-notes).

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1908 ist **1.1908.0.20**.

### <a name="update-type"></a>Updatetyp

Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.

Der Buildtyp des Azure Stack-Updates 1908 lautet **Vollständig**. Daher hat das Update 1908 eine längere Laufzeit als Express-Updates wie 1906 und 1907. Genaue Laufzeiten für vollständige Updates hängen in der Regel von der Anzahl der Knoten ab, die Ihre Azure Stack-Instanz enthält, der auf Ihrem System von Mandantenworkloads verbrauchten Kapazität, der Netzwerkkonnektivität Ihres Systems (falls es mit dem Internet verbunden ist) und Ihrer Hardwarekonfiguration. Das Update 1908 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 42 Stunden, 8 Knoten: 50 Stunden, 12 Knoten: 60 Stunden, 16 Knoten: 70 Stunden. Updatelaufzeiten, die länger als diese erwarteten Werte dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen seitens des Azure Stack-Betreibers, sofern es für das Update nicht zu einem Fehler kommt.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl.
- Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1908 und nicht auf andere Azure Stack-Updates übertragbar.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neuigkeiten

<!-- What's new, also net new experiences and features. -->

- Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.
- Für alle Komponenten der Azure Stack-Infrastruktur wird jetzt der FIPS 140-2-Modus verwendet.


### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Es wurden Verbesserungen an der Azure Stack-Verschlüsselung von ruhenden Daten vorgenommen, um Geheimnisse dauerhaft im Hardware-TPM (Trusted Platform Module) der physischen Knoten zu speichern.

### <a name="changes"></a>Änderungen

- Hardwareanbieter veröffentlichen das OEM-Erweiterungspaket 2.1 oder höher zur gleichen Zeit wie Azure Stack, Version 1908. Das OEM-Erweiterungspaket 2.1 oder höher ist eine Voraussetzung für Version 1908 von Azure Stack. Weitere Informationen zum Herunterladen des OEM-Erweiterungspakets 2.1 oder höher erhalten Sie von Hardwareanbieter Ihres Systems und im Artikel zu [OEM-Updates](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Fehlerbehebungen

- Es wurde ein Problem mit der Kompatibilität zukünftiger Azure Stack-OEM-Updates und ein Problem mit der VM-Bereitstellung bei Verwendung von Kundenbenutzerimages behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) behoben.  
- Es wurde ein Problem mit einem OEM-Firmwareupdate behoben und in Test-AzureStack eine Fehldiagnose zur Integrität des Fabric-Rings korrigiert. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) behoben.
- Es wurde ein Problem mit dem Prozess für OEM-Firmwareupdates behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) behoben.


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](azure-stack-release-notes-known-issues-1908.md)
- [Sicherheitsupdates](azure-stack-release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1908 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1907 installieren, bevor Sie Azure Stack auf 1908 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1908-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1908

Version 1908 von Azure Stack muss auf Version 1907 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1907.12.44](https://support.microsoft.com/help/4517473)

Für das Azure Stack-Update 1908 ist **Azure Stack-OEM-Version 2.1 oder höher** vom Hardwareanbieter Ihres Systems erforderlich. OEM-Updates enthalten Treiber- und Firmwareupdates Ihrer Azure Stack-Systemhardware. Weitere Informationen zum Anwenden von OEM-Updates finden Sie unter [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md) (Anwenden von Azure Stack-OEM-Updates).

### <a name="after-successfully-applying-the-1908-update"></a>Nach erfolgreicher Anwendung des Updates 1908

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Für 1908 ist kein Hotfix verfügbar.

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
