---
title: Versionshinweise zu Azure Stack 1904 | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1904 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update und archivierte Azure Stack-Versionshinweise heruntergeladen werden können.
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 71ad5812472534ae4a7e3dc34feabf7a1a02faa0
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494049"
---
# <a name="azure-stack-1904-update"></a>Azure Stack-Update 1904

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird der Inhalt des Updatepakets 1904 beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack. Der Artikel enthält folgende Informationen:

- [Beschreibung der Neuerungen, Verbesserungen, Fehlerbehebungen und Sicherheitsupdates](azure-stack-release-notes-1904.md#whats-in-this-update)
- [Updateplanung](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="archived-release-notes"></a>Archivierte Versionshinweise

Sie können [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](http://aka.ms/azsarchivedrelnotes) anzeigen. Diese archivierten Versionshinweise werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1904 ist **1.1904.0.36**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack 1904-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Das Update 1904 dauert etwa 16 Stunden, die genauen Zeiten können jedoch variieren. Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1904 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- In 1904 wurden signifikante Verbesserungen am SDN-Stapel (Software-Defined Networking) vorgenommen. Dank dieser Maßnahmen wird die allgemeine Wartung und Zuverlässigkeit des SDN-Stapels in Azure Stack vereinfacht bzw. verbessert.

- Im Administratorportal wurde eine Benachrichtigung für den Fall hinzugefügt, dass der derzeit angemeldete Benutzer nicht über die erforderlichen Berechtigungen verfügt, damit das Dashboard richtig geladen werden kann. Außerdem ist ein Link zur Dokumentation vorhanden, in der beschrieben ist, welche Konten über die passenden Berechtigungen verfügen. Dies hängt vom Identitätsanbieter ab, der während der Bereitstellung verwendet wird.

- Es wurden Verbesserungen in Bezug auf die Resilienz und Betriebszeit von VMs durchgeführt. Dies bedeutet auch eine Lösung für das Szenario, bei dem alle VMs in den Offlinezustand versetzt werden, wenn das Speichervolume mit den VM-Konfigurationsdateien offline geschaltet wird.

<!-- 1901,2,3 related hotfix -->
- Es wurde eine Optimierung in Bezug auf die Anzahl von gleichzeitig evakuierten VMs hinzugefügt und der Bandbreitenverbrauch beschränkt, um die Gefahr von Spannungsabfällen oder Stromausfällen für VMs zu minimieren, wenn das Netzwerk stark ausgelastet ist. Durch diese Änderung wird die VM-Betriebszeit erhöht, wenn ein System aktualisiert wird.

<!-- 1901,2,3 related hotfix -->
- Es wurde eine Verbesserung der Ressourcendrosselung bei einem System unter Volllast durchgeführt, um zu verhindern, dass die Plattformressourcen durch interne Prozesse erschöpft werden und es im Portal zu Fehlern bei Vorgängen kommt.

- Aufgrund der verbesserten Filterfunktionen können Bediener mehrere Filter gleichzeitig anwenden. Sie können auf der neuen Benutzeroberfläche nur nach der Spalte **Name** sortieren.

- Verbesserungen des Prozesses zum Löschen von Angeboten, Plänen, Kontingenten und Abonnements. Sie können über das Administratorportal jetzt Angebote, Kontingente, Pläne und Abonnements löschen, wenn das betreffende Objekt nicht über Abhängigkeiten verfügt. [hier finden Sie weitere Informationen](azure-stack-delete-offer.md)  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Das Volume für syslog-Nachrichten wurde verbessert. Nicht erforderliche Ereignisse werden herausgefiltert, und es wird ein Konfigurationsparameter angegeben, um den gewünschten Schweregrad für weitergeleitete Nachrichten auszuwählen. Weitere Informationen zum Konfigurieren des Schweregrads finden Sie unter [Integration des Azure Stack-Datencenters: syslog-Weiterleitung](azure-stack-integrate-security.md).

<!--this applied to Bug 1473487 -->
- Dem Cmdlet **Get-AzureStackLog** wurde eine neue Funktion hinzugefügt, indem der zusätzliche Parameter `-OutputSASUri` bereitgestellt wurde. Sie können jetzt Azure Stack-Protokolle aus Ihrer Umgebung erfassen und im angegebenen Azure Storage-Blobcontainer speichern. Weitere Informationen finden Sie unter [Azure Stack-Diagnose](azure-stack-configure-on-demand-diagnostic-log-collection.md#examples).

- In der Gruppe „**Test-AzureStack** `UpdateReadiness`“ wurde eine neue Speicherüberprüfung hinzugefügt, mit der ermittelt wird, ob für die erfolgreiche Durchführung des Updates im Stapel genügend Speicher verfügbar ist.

<!-- Bug/Task 4311058 -->
- Es wurden Verbesserungen an **Test-AzureStack** in Bezug auf die Auswertung der Service Fabric-Integrität vorgenommen.

<!-- feature: 2976966 -->
- Hardwareupdates wurden verbessert, um die Dauer für die Durchführung von Updates der Laufwerkfirmware auf zwei bis vier Stunden zu verringern. Mit dem Updatemodul wird basierend auf dem Inhalt des Pakets dynamisch ermittelt, welche Teile des Updates ausgeführt werden müssen.

<!-- Feature 3906611 -->
- Es wurden stabile Vorabüberprüfungen von Vorgängen hinzugefügt, um störende Vorgänge von Infrastrukturrolleninstanzen zu verhindern, die sich auf die Verfügbarkeit auswirken.

<!-- Feature 3780326 -->
- Verbesserungen der Idempotenz des Sicherungsaktionsplans der Infrastruktur.

<!--Bug/Task 3139609 -->
- Verbesserungen der Azure Stack-Protokollsammlung. Diese Verbesserungen führen zu einem verringerten Zeitaufwand für das Abrufen der Protokolle. Darüber hinaus generiert das Cmdlet [Get-AzureStackLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) keine Standardprotokolle für die OEM-Rolle mehr. Sie müssen das Cmdlet [Invoke-AzureStackOnDemandLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#invoke-azurestackondemandlog) ausführen und die Rolle zum Abrufen der OEM-Protokolle angeben. Weitere Informationen finden Sie unter [Azure Stack-Diagnose](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- In Azure Stack wird jetzt die Verbunddaten-URL für die Datencenterintegration mit ADFS überwacht. Auf diese Weise wird die Zuverlässigkeit während der Geheimnisrotation der ADFS-Instanz bzw. -Farm des Kunden verbessert.

### <a name="changes"></a>Änderungen

<!-- Feature 3906611 -->
- Die Option, mit der Azure Stack-Bediener Infrastrukturrolleninstanzen über das Administratorportal herunterfahren konnten, wurde entfernt. Mit der Neustartfunktionalität wird sichergestellt, dass ein korrekter Versuch zum Herunterfahren durchgeführt wird, bevor die Infrastrukturrolleninstanz neu gestartet wird. Für erweiterte Szenarien bleibt die API- und PowerShell-Funktionalität verfügbar.

<!-- Feature ## 4199257 -->
- Es gibt eine neue Marketplace-Verwaltungsoberfläche mit separaten Bildschirmen für Marketplace-Images und Ressourcenanbieter. Bisher ist das Fenster **Ressourcenanbieter** noch leer, aber in zukünftigen Versionen werden neue PaaS-Dienstangebote angezeigt und können im Fenster **Ressourcenanbieter** verwaltet werden.

<!-- Feature ## 4199257 -->
- Änderungen an der Updateoberfläche im Betreiberportal. Es ist ein neues Raster für Ressourcenanbieterupdates vorhanden. Die Möglichkeit zum Aktualisieren von Ressourcenanbietern ist noch nicht verfügbar.

<!-- Task ## 3748423  -->
- Änderungen an der Benutzeroberfläche für die Updateinstallation im Bedienerportal. Damit Azure Stack-Bediener auf ein Updateproblem richtig reagieren können, werden im Portal jetzt basierend auf der Integrität der Skalierungseinheit spezifischere Empfehlungen bereitgestellt. Dies erfolgt automatisch, indem **Test-AzureStack** ausgeführt wird und die Ergebnisse analysiert werden. Anhand des Ergebnisses wird der Bediener angewiesen, eine von zwei Maßnahmen zu ergreifen:

  - Im Portal wird eine nicht kritische Warnung mit einem Hinweis der folgenden Art angezeigt: „Für das letzte Update ist ein Eingreifen erforderlich. Microsoft empfiehlt das Erstellen einer Serviceanfrage während der normalen Geschäftszeiten. Im Rahmen des Updateprozesses wird der Vorgang „Test-AzureStack“ durchgeführt, und basierend auf der Ausgabe wird die am besten geeignete Warnung generiert. In diesem Fall wurde der Test „Test-AzureStack“ bestanden.“

  - Im Portal wird die folgende kritische Warnung angezeigt: „Beim letzten Update ist ein Fehler aufgetreten. Microsoft empfiehlt, so schnell wie möglich eine Serviceanfrage zu erstellen. Im Rahmen des Updateprozesses wird der Vorgang „Test-AzureStack“ durchgeführt, und basierend auf der Ausgabe wird die am besten geeignete Warnung generiert. In diesem Fall wurde der Test „Test-AzureStack“ nicht bestanden.“

- Es wurde ein Update auf Version 2.2.38.0 des Azure Linux-Agents durchgeführt. Aufgrund dieser Unterstützung können Kunden einheitliche Linux-Images zwischen Azure und Azure Stack nutzen.

- Änderungen an den Updateprotokollen im Bedienerportal. Anforderungen zum Abrufen der Protokolle für erfolgreiche Updates sind nicht mehr verfügbar. Protokolle für fehlerhafte Updates sind weiterhin zum Download verfügbar, da sie zur Diagnose verwendet werden können.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Problem behoben, bei dem die syslog-Konfiguration bei einem Updatezyklus nicht beibehalten wurde, sodass der syslog-Client seine Konfiguration verliert und die syslog-Nachrichten nicht mehr weitergeleitet werden. Die syslog-Konfiguration wird jetzt beibehalten.

- Es wurde ein CRP-Problem behoben, bei dem die Aufhebung der Zuordnung von VMs blockiert wurde. Wenn eine VM viele große verwaltete Datenträger enthalten hat, konnte es bisher vorkommen, dass beim Aufheben der Zuordnung der VM ein Zeitlimitfehler aufgetreten ist.

- Es wurde ein Problem mit dem Windows Defender-Modul behoben, bei dem der Zugriff auf die Speicherung von Skalierungseinheiten beeinträchtigt wurde.

- Es wurde ein Problem mit dem Benutzerportal behoben, bei dem das Zugriffsrichtlinienfenster für Blobspeicherkonten nicht geladen werden konnte.

- Es wurde ein Problem im Administrator- und im Benutzerportal behoben, bei dem fehlerhafte Benachrichtigungen zum globalen Azure-Portal angezeigt wurden.

- Es wurde ein Problem im Benutzerportal behoben, bei dem die Auswahl der Kachel **Feedback** dazu geführt hat, dass eine leere Browserregisterkarte geöffnet wurde.

- Es wurde ein Portalproblem behoben, bei dem eine Änderung einer statischen IP-Adresse für eine IP-Konfiguration, die an den Netzwerkadapter einer VM-Instanz gebunden war, zur Anzeige einer Fehlermeldung geführt hat.

- Es wurde ein Problem im Benutzerportal behoben, bei dem beim Vorgang **Netzwerkschnittstelle anfügen** für eine vorhandene VM über das Fenster **Netzwerk** ein Fehler mit einer Fehlermeldung aufgetreten ist.

- Es wurde ein Problem behoben, bei dem Azure Stack das Anfügen von mehr als vier Netzwerkschnittstellen (NICs) an eine VM-Instanz nicht unterstützt hat.

- Es wurde ein Problem im Portal behoben, bei dem das Hinzufügen einer Eingangssicherheitsregel und das Auswählen von **Diensttag** als Quelle dazu geführt hat, dass mehrere Optionen angezeigt wurden, die für Azure Stack nicht verfügbar sind.

- Es wurde ein Problem behoben, bei dem Netzwerksicherheitsgruppen (NSGs) in Azure Stack nicht auf die gleiche Weise wie in der globalen Azure-Umgebung funktioniert haben.

- Es wurde ein Problem mit der Marketplace-Verwaltung behoben, bei dem alle heruntergeladenen Produkte ausgeblendet werden, wenn die Registrierung abläuft oder entfernt wird.

- Es wurde ein Problem behoben, bei dem beim Ausführen des Befehls **Set-AzureRmVirtualNetworkGatewayConnection** in PowerShell für eine vorhandene Verbindung eines Gateways für virtuelle Netzwerke die Fehlermeldung **Ungültiger gemeinsam verwendeter Schlüssel konfiguriert...** angezeigt wurde.

- Es wurde ein Problem behoben, bei dem der Netzwerkressourcenanbieter nicht mit dem Netzwerkcontroller synchron war, sodass eine doppelte Ressourcenanforderung durchgeführt wurde. In einigen Fällen ist die übergeordnete Ressource hierbei in einem Fehlerzustand verblieben.

- Es wurde ein Problem behoben, bei dem Folgendes aufgetreten ist: Wenn einem Benutzer die Rolle „Mitwirkender“ für ein Abonnement zugewiesen wurde, aber nicht explizit Leseberechtigungen, wurde beim Versuch, eine Änderung einer Ressource zu speichern, der Fehler **...Client „somelogonaccount@domain.com“ mit der Objekt-ID „{GUID}“ hat keine Berechtigung zum Ausführen der Aktion...** generiert.

- Es wurde ein Problem behoben, bei dem der Marketplace-Verwaltungsbildschirm leer war, wenn das Offlinesyndikationstool zum Hochladen von Images verwendet wurde und für ein oder mehrere Images die Symbol-URI(s) gefehlt haben.

- Es wurde ein Problem behoben, aufgrund dessen Produkte, bei deren Download ein Fehler aufgetreten ist, nicht aus der Marketplace-Verwaltung gelöscht werden konnten.

### <a name="security-updates"></a>Sicherheitsupdates

Dieses Update von Azure Stack enthält keine Sicherheitsupdates für das zugrunde liegende Betriebssystem, von dem Azure Stack gehostet wird. Informationen hierzu finden Sie unter [Azure Stack-Sicherheitsupdates](azure-stack-release-notes-security-updates-1904.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](azure-stack-release-notes-known-issues-1904.md)
- [Sicherheitsupdates](azure-stack-release-notes-security-updates-1904.md)
- [Azure Stack-Checkliste für Updateaktivitäten](azure-stack-release-notes-checklist.md)

> [!NOTE]
> Achten Sie darauf, dass Sie für die Planung und Größenanpassung Ihrer Workloads die neueste Version des Tools [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) verwenden. Die neueste Version umfasst Fehlerbehebungen und neue Features, die mit jedem Azure Stack-Update veröffentlicht werden.

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1904 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1903 installieren, bevor Sie Azure Stack auf 1904 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1904-update"></a>Vor dem Anwenden des Updates 1904

Version 1904 von Azure Stack muss auf Version 1903 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Nach erfolgreicher Anwendung des Updates 1904

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

- [Azure Stack-Hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Kunden mit Systemen, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).  

