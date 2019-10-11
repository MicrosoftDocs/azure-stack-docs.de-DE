---
title: Versionshinweise zu Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen in den Updates für integrierte Azure Stack-Systeme enthalten sind, und wo das Update heruntergeladen werden kann.
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
ms.date: 09/27/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 62440c61ee637a0092b1e956b08ba0a9c94d8bab
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342933"
---
# <a name="azure-stack-updates-release-notes"></a>Azure Stack-Updates: Versionshinweise

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel werden die Inhalte der Azure Stack-Updatepakete beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack.

Um auf Versionshinweise für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

Unterstützung bei der Problembehandlung von Updates und dem Updateprozess finden Sie unter [Problembehandlung bei Patch- und Updateproblemen für Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1908 lautet **1.1908.4.33**.

### <a name="update-type-1908"></a>Updatetyp

Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.

Der Buildtyp des Azure Stack-Updates 1908 lautet **Vollständig**. Daher hat das Update 1908 eine längere Laufzeit als Express-Updates wie 1906 und 1907. Genaue Laufzeiten für vollständige Updates hängen in der Regel von der Anzahl der Knoten ab, die Ihre Azure Stack-Instanz enthält, der auf Ihrem System von Mandantenworkloads verbrauchten Kapazität, der Netzwerkkonnektivität Ihres Systems (falls es mit dem Internet verbunden ist) und Ihrer Hardwarekonfiguration. Das Update 1908 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 42 Stunden, 8 Knoten: 50 Stunden, 12 Knoten: 60 Stunden, 16 Knoten: 70 Stunden. Updatelaufzeiten, die länger als diese erwarteten Werte dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen seitens des Azure Stack-Betreibers, sofern es für das Update nicht zu einem Fehler kommt.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl.
- Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1908 und nicht auf andere Azure Stack-Updates übertragbar.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1908"></a>Neuerungen

<!-- What's new, also net new experiences and features. -->

- Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.
- Für alle Komponenten der Azure Stack-Infrastruktur wird jetzt der FIPS 140-2-Modus verwendet.
- Azure Stack-Operatoren können jetzt Portalbenutzerdaten entfernen. Weitere Informationen finden Sie unter [Löschen von Portalbenutzerdaten aus Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements-1908"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Es wurden Verbesserungen an der Azure Stack-Verschlüsselung von ruhenden Daten vorgenommen, um Geheimnisse dauerhaft im Hardware-TPM (Trusted Platform Module) der physischen Knoten zu speichern.

### <a name="changes-1908"></a>Änderungen

- Hardwareanbieter veröffentlichen das OEM-Erweiterungspaket 2.1 oder höher zur gleichen Zeit wie Azure Stack, Version 1908. Das OEM-Erweiterungspaket 2.1 oder höher ist eine Voraussetzung für Version 1908 von Azure Stack. Weitere Informationen zum Herunterladen des OEM-Erweiterungspakets 2.1 oder höher erhalten Sie von Hardwareanbieter Ihres Systems und im Artikel zu [OEM-Updates](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes-1908"></a>Fehlerbehebungen

- Es wurde ein Problem mit der Kompatibilität zukünftiger Azure Stack-OEM-Updates und ein Problem mit der VM-Bereitstellung bei Verwendung von Kundenbenutzerimages behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) behoben.  
- Es wurde ein Problem mit einem OEM-Firmwareupdate behoben und in Test-AzureStack eine Fehldiagnose zur Integrität des Fabric-Rings korrigiert. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) behoben.
- Es wurde ein Problem mit dem Prozess für OEM-Firmwareupdates behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) behoben.


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates-1908"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1908 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes-1908"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1907 installieren, bevor Sie Azure Stack auf 1908 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1908-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1908

Version 1908 von Azure Stack muss auf Version 1907 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1907.17.54](https://support.microsoft.com/help/4523826)

Für das Azure Stack-Update 1908 ist **Azure Stack-OEM-Version 2.1 oder höher** vom Hardwareanbieter Ihres Systems erforderlich. OEM-Updates enthalten Treiber- und Firmwareupdates Ihrer Azure Stack-Systemhardware. Weitere Informationen zum Anwenden von OEM-Updates finden Sie unter [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md) (Anwenden von Azure Stack-OEM-Updates).

### <a name="after-successfully-applying-the-1908-update"></a>Nach erfolgreicher Anwendung des Updates 1908

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Für 1908 ist kein Hotfix verfügbar.
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1907 ist **1.1907.0.20**.

### <a name="update-type-1907"></a>Updatetyp

Der Buildtyp des Azure Stack 1907-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Auf der Grundlage interner Tests lässt sich sagen, dass das Update 1907 etwa 13 Stunden dauert.

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl.
- Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1907 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update-1907"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1907"></a>Neuerungen

<!-- What's new, also net new experiences and features. -->

- Allgemein verfügbare Version des Azure Stack-Sammlungsdiensts für Diagnoseprotokolle, um die Sammlung von Diagnoseprotokollen zu vereinfachen und zu verbessern. Der Azure Stack-Sammlungsdienst für Diagnoseprotokolle stellt ein vereinfachtes Verfahren zum Sammeln und Teilen von Diagnoseprotokollen mit Microsoft Customer Support Services (CSS) zur Verfügung. Dieser Sammlungsdienst für Diagnoseprotokolle bietet eine neue Benutzeroberfläche im Azure Stack-Verwaltungsportal, mit der Operatoren beim Erreichen bestimmter kritischer Warnschwellen Diagnoseprotokolle automatisch auf einen Speicherblob hochladen oder den gleichen Vorgang auf Anforderung ausführen können. Weitere Informationen finden Sie im Artikel [Sammlung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview.md).

- Allgemein verfügbare Version der Azure Stack-Netzwerkinfrastruktur-Überprüfung als Teil des Azure Stack-Überprüfungstools **Test-AzureStack**. Die Azure Stack-Netzwerkinfrastrukur stellt einen Teil von **Test-AzureStack** dar und ermöglicht das Bestimmen eines Fehlers in der Netzwerkinfrastruktur von Azure Stack. Der Test überprüft die Konnektivität der Netzwerkinfrastruktur, indem er das softwaredefinierte Netzwerk von Azure Stack umgeht. Er veranschaulicht Verbindungen von einer öffentlichen VIP mit den konfigurierten DNS-Forwardern, NTP-Servern und Identitätsendpunkten. Außerdem überprüft er die Konnektivität mit Azure, wenn Azure AD als Identitätsanbieter verwendet wird, oder mit dem Partnerserver, wenn ADFS verwendet wird. Weitere Informationen finden Sie im Artikel zum [Azure Stack-Überprüfungstool](azure-stack-diagnostic-test.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne SQL-TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

### <a name="improvements-1907"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Auf dem Azure Stack-Updateblatt wird jetzt eine Zeit für den **Letzten abgeschlossenen Schritt** für aktive Updates angezeigt. Diese kann angezeigt werden, indem auf dem Updateblatt auf ein laufendes Update geklickt wird. Der **Letzte abgeschlossene Schritt** ist dann im Abschnitt **Update-Ausführungsdetails** verfügbar.

- Verbesserungen an den Operatoraktionen **Start-AzureStack** und **Stop-AzureStack**. Die für den Start von Azure Stack erforderliche Zeit wurde um durchschnittlich 50 % reduziert. Die für das Herunterfahren von Azure Stack erforderliche Zeit wurde um durchschnittlich 30 % reduziert. Die durchschnittlichen Zeiten für Start und Herunterfahren bleiben gleich, wenn sich die Anzahl der Knoten in einer Skalierungseinheit erhöht.

- Verbesserte Fehlerbehandlung für das Marketplace-Tool bei getrennter Verbindung. Bei einem Downloadfehler oder einem Teilerfolg wird bei der Verwendung von **Export-AzSOfflineMarketplaceItem** eine detaillierte Fehlermeldung mit weiteren Details zum Fehler und Schritten zur Entschärfung angezeigt, sofern vorhanden.

- Verbesserte Leistung bei der Erstellung verwalteter Datenträger aus einem großen Seitenblob/einer großen Momentaufnahme. Bisher wurde beim Erstellen eines großen Datenträgers ein Zeitlimit ausgelöst.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Verbesserte Integritätsprüfung von virtuellen Datenträgern vor dem Herunterfahren eines Knotens, um ein unerwartetes Trennen virtueller Datenträger zu verhindern.

- Verbesserte Speicherung interner Protokolle für Administratorvorgänge. Dies führt zu einer verbesserten Leistung und Zuverlässigkeit bei Administratorvorgängen, indem die Arbeitsspeicher- und Speicherplatznutzung durch interne Protokollprozesse minimiert wird. Möglicherweise bemerken Sie darüber hinaus verbesserte Seitenladezeiten des Updateblatts im Verwaltungsportal. Im Rahmen dieser Verbesserung stehen Updateprotokolle, die älter als 6 Monate sind, im System nicht mehr zur Verfügung. Wenn Sie Protokolle für diese Updates benötigen, achten Sie darauf, dass Sie für alle Updateausführungen, die älter als 6 Monate sind, [die Zusammenfassung herunterladen](azure-stack-apply-updates.md), bevor Sie das Update 1907 durchführen.

### <a name="changes-1907"></a>Änderungen

- Version 1907 von Azure Stack enthält eine Warnmeldung, in der Operatoren darauf hingewiesen werden, dass sie das OEM-Paket des Systems auf Version 2.1 oder höher aktualisieren müssen, bevor sie ein Update auf Version 1908 ausführen können. Weitere Informationen zum Anwenden von Azure Stack-OEM-Updates finden Sie unter [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md) (Anwenden von Azure Stack-OEM-Updates).

- Es wurde eine neue Ausgangsregel (HTTPS) hinzugefügt, um dem Azure Stack-Sammlungsdienst für Diagnoseprotokolle die Kommunikation zu ermöglichen. Weitere Informationen finden Sie unter [Integration des Azure Stack-Rechenzentrums – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Der Infrastruktur-Sicherungsdienst löscht jetzt teilweise hochgeladene Sicherungen, wenn der externe Speicherort an seine Kapazitätsgrenze stößt.

- Infrastruktursicherungen enthalten keine Sicherung von Domänendienstdaten mehr. Dies betrifft nur Systeme, die Azure Active Directory als Identitätsanbieter verwenden.

- Wir überprüfen jetzt, ob ein Image, das auf dem Blatt **Compute -> VM-Images** erfasst wird, den Typ Seitenblob aufweist.

### <a name="fixes-1907"></a>Fehlerbehebungen

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

## <a name="security-updates-1907"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning-1907"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update-1907"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1907 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes-1907"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1906 installieren, bevor Sie Azure Stack auf 1907 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1907-update"></a>Vor dem Anwenden des Updates 1907

Version 1907 von Azure Stack muss auf Version 1906 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1906.13.56](https://support.microsoft.com/help/4520375)

### <a name="after-successfully-applying-the-1907-update"></a>Nach erfolgreicher Anwendung des Updates 1907

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1907.17.54](https://support.microsoft.com/help/4523826)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1906 ist **1.1906.0.30**.

### <a name="update-type-1906"></a>Updatetyp

Der Buildtyp des Azure Stack 1906-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Das Update 1906 dauert etwa 10 Stunden, unabhängig von der Anzahl der physischen Knoten in Ihrer Azure Stack-Umgebung. Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl. Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1906 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update-1906"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ein **Set-TLSPolicy**-Cmdlet wurde im privilegierten Endpunkt (PEP) hinzugefügt, um TLS 1.2 für alle Endpunkte zu erzwingen. Weitere Informationen finden Sie unter [Azure Stack-Sicherheitskontrollen](azure-stack-security-configuration.md).

- Ein **Get-TLSPolicy**-Cmdlet wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die angewendete TLS-Richtlinie abzurufen. Weitere Informationen finden Sie unter [Azure Stack-Sicherheitskontrollen](azure-stack-security-configuration.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

- Eine Schutzvorrichtung wurde hinzugefügt, um das Ablaufen interner Geheimnisse zu verhindern, indem die Rotation interner Geheimnisse, falls eine kritische Warnung bei ablaufenden Geheimnissen ignoriert wird, zu erzwingen. Dies sollte nicht als normales Verfahren angesehen und auch nicht so verwendet werden. Die Geheimnisrotation sollte während eines Wartungsfensters eingeplant werden. Weitere Informationen finden Sie unter [Azure Stack: Geheimnisrotation](azure-stack-rotate-secrets.md).

- Visual Studio Code wird jetzt in Azure Stack-Bereitstellungen, die AD FS verwenden, unterstützt.

### <a name="improvements-1906"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Das Cmdlet **Get-GraphApplication** im privilegierten Endpunkt zeigt jetzt den Fingerabdruck des aktuell verwendeten Zertifikats an. Dies verbessert die Zertifikatverwaltung für Dienstprinzipale, wenn Azure Stack mit AD FS bereitgestellt wird.

- Neue Regeln für die Systemüberwachung wurden hinzugefügt, um die Verfügbarkeit von AD Graph und AD FS zu überprüfen, einschließlich der Möglichkeit zum Auslösen von Warnungen.

- Verbesserungen an der Zuverlässigkeit des Sicherungsressourcenanbieters, wenn der Dienst für die Infrastruktursicherung auf eine andere Instanz verschoben wird.

- Optimierung der Leistung des externen Geheimnisrotationsverfahrens, um eine einheitliche Ausführungszeit bereitzustellen, um die Planung des Wartungsfensters zu erleichtern.

- Das Cmdlet **Test-AzureStack** berichtet jetzt zu internen Geheimnissen, die dabei sind, abzulaufen (kritische Warnungen).

- Ein neuer Parameter ist für das Cmdlet **Register-CustomAdfs** im privilegierten Endpunkt verfügbar, der es ermöglicht, die Überprüfung der Zertifikatssperrliste zu überspringen, wenn die Verbundvertrauensstellung für AD FS konfiguriert wird.

- Das Release 1906 führt bessere Sichtbarkeit des Updatestatus ein, sodass Sie sicher sein können, dass Updates nicht angehalten sind. Dies führt zu einer Erhöhung der Gesamtzahl der Updateschritte, die Operatoren auf dem Blatt **Update** angezeigt werden. Möglicherweise bemerken Sie auch, dass mehr Updateschritte gleichzeitig ausgeführt werden als bei früheren Updates.

#### <a name="networking-updates-1906"></a>Netzwerkupdates

- Die im DHCP-Responder festgelegte Leasedauer wurde so aktualisiert, dass sie mit Azure konsistent ist.

- Verbesserte Wiederholungsraten für den Ressourcenanbieter im Falle der fehlerhaften Bereitstellung von Ressourcen.

- Die SKU-Option **Standard** wurde sowohl aus dem Load Balancer als auch der öffentlichen IP-Adresse entfernt, da dies zurzeit nicht unterstützt wird.

### <a name="changes-1906"></a>Änderungen

- Das Erstellen eine Speicherkontoerfahrung ist jetzt mit Azure konsistent.

- Auslöser für Warnungen bei Ablauf interner Geheimnisse wurden geändert:
  - Warnungen werden jetzt 90 Tage vor dem Ablauf von Geheimnissen ausgelöst.
  - Kritische Warnungen werden jetzt 30 Tage vor dem Ablauf von Geheimnissen ausgelöst.

- Zeichenfolgen im Ressourcenanbieter für die Infrastruktursicherung wurden hinsichtlich konsistenter Terminologie aktualisiert.

### <a name="fixes-1906"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Ein Problem wurde behoben, bei dem das Ändern der Größe einer VM mit verwaltetem Datenträger mit einem Fehler **Interner Vorgangsfehler** fehlschlug.

- Ein Problem wurde behoben, bei dem die fehlgeschlagene Erstellung eines Benutzerimages den Dienst, der Images verwaltet, in einen ungültigen Zustand versetzt. Hierdurch wird das Löschen des fehlerhaften Images und die Erstellung neuer Images blockiert. Dies ist auch im Hotfix 1905 behoben.

- Aktive Warnungen wegen abgelaufener interner Geheimnisse werden jetzt nach erfolgreicher Ausführung der internen Geheimnisrotation automatisch geschlossen.

- Ein Problem wurde behoben, bei dem die erste Ziffer der Updatedauer auf der Registerkarte „Updateverlauf“ abgeschnitten wurde, wenn das Update länger als 99 Stunden ausgeführt wurde.

- Das Blatt **Update** enthält eine Option **Fortsetzen** für fehlgeschlagene Updates.

- In den Administrator- und Benutzerportalen wurde das Problem im Marketplace behoben, bei dem die Docker-Erweiterung fehlerhaft von der Suche zurückgegeben wurde, aber keine weitere Maßnahme ergriffen werden konnte, da sie nicht in Azure Stack verfügbar war.

- Ein Problem in der Benutzeroberfläche für die Vorlagenbereitstellung wurde behoben, bei dem Parameter nicht aufgefüllt wurden, wenn der Vorlagenname mit „_“ (Unterstrich) begann.

- Ein Problem auf der Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen wurde behoben, bei dem „CentOS 7.2-basiert“ als Option für die Bereitstellung angeboten wird. CentOS 7.2 ist in Azure Stack nicht verfügbar. Wir stellen jetzt CentOS 7.5 als Option für die Bereitstellung zur Verfügung.

- Sie können jetzt eine Skalierungsgruppe über das Blatt **VM-Skalierungsgruppen** entfernen.

## <a name="security-updates-1906"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning-1906"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update-1906"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1906 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes-1906"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1905 installieren, bevor Sie Azure Stack auf 1906 aktualisieren. Installieren Sie nach der Aktualisierung alle [verfügbaren Hotfixes für Version 1906](#after-successfully-applying-the-1906-update).

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1906-update"></a>Vor dem Anwenden des Updates 1906

Version 1906 von Azure Stack muss auf Version 1905 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Nach erfolgreicher Anwendung des Updates 1906

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1906.13.56](https://support.microsoft.com/help/4520375)
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-build-reference"></a>1905 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1905 ist **1.1905.0.40**.

### <a name="update-type-1905"></a>Updatetyp

Der Azure Stack 1905-Update-Buildtyp lautet **Vollständig**. Daher hat das Update 1905 eine längere Laufzeit als Express-Updates wie 1903 und 1904. Genaue Laufzeiten für vollständige Updates hängen in der Regel von der Anzahl der Knoten ab, die Ihre Azure Stack-Instanz enthält, der auf Ihrem System von Mandantenworkloads verbrauchten Kapazität, der Netzwerkkonnektivität Ihres Systems (falls es mit dem Internet verbunden ist) und Ihrer Hardwarekonfiguration. Das Update 1905 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 35 Stunden, 8 Knoten: 45 Stunden, 12 Knoten: 55 Stunden, 16 Knoten: 70 Stunden. Laufzeiten von 1905, die länger als diese erwarteten Werte sind, sind nicht ungewöhnlich und erfordern kein Eingreifen seitens des Azure Stack-Operators, es sei denn, das Update schlägt fehl. Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update-1905"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Mit diesem Update kann das Updatemodul in Azure Stack die Firmware der Knoten von Skalierungseinheiten aktualisieren. Hierfür ist ein kompatibles Updatepaket von den Hardwarepartnern erforderlich. Ausführlichere Informationen zur Verfügbarkeit erhalten Sie bei Ihrem Hardwarepartner.

- Windows Server 2019 wird jetzt unterstützt und ist für die Syndizierung über den Azure Stack-Marketplace verfügbar.
Mit diesem Update kann Windows Server 2019 auf einem 2016-Host jetzt erfolgreich aktiviert werden.

- Eine neue [Azure-Kontoerweiterung für Visual Studio Code](../user/azure-stack-dev-start-vscode-azure.md) ermöglicht es Entwicklern, Azure Stack zu nutzen, indem sie sich anmelden und Abonnements anzeigen. Darüber hinaus können sie auch noch einige andere Dienste nutzen. Die neue Azure-Kontoerweiterung funktioniert sowohl in Azure AD- (Azure Active Directory) als auch in AD FS-Umgebungen und erfordert nur eine kleine Änderung an den Benutzereinstellungen von Visual Studio Code. Für Visual Studio Code ist es erforderlich, dass einem Dienstprinzipal eine Berechtigung erteilt wird, um in dieser Umgebung ausgeführt zu werden. Importieren Sie zu diesem Zweck das Identitätsskript, und führen Sie die in [Mehrinstanzenfähigkeit in Azure Stack](../operator/azure-stack-enable-multitenancy.md) angegebenen Cmdlets aus. Dafür sind eine Aktualisierung des Basisverzeichnisses und die Registrierung des Gastmandantenverzeichnisses für jedes Verzeichnis erforderlich. Nach dem Update auf 1905 oder höher wird eine Benachrichtigung angezeigt, dass der Basisverzeichnismandant, für den der Dienstprinzipal von Visual Studio Code enthalten ist, aktualisiert werden muss. 

### <a name="improvements-1905"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Im Rahmen der Erzwingung von TLS 1.2 in Azure Stack wurden die folgenden Erweiterungen auf diese Versionen aktualisiert:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Laden Sie diese Versionen der Erweiterungen sofort herunter, damit für neue Bereitstellungen der Erweiterung keine Fehler auftreten, wenn TLS 1.2 in einem zukünftigen Release erzwungen wird. Legen Sie immer **autoUpgradeMinorVersion=true** fest, damit Nebenversionsupdates (z. B. von 1.8 auf 1.9) automatisch durchgeführt werden.

- Eine neue Übersicht für **Hilfe und Support** im Azure Stack-Portal erleichtert Betreibern das Prüfen der Supportoptionen, das Inanspruchnehmen von Expertenhilfe und Erhalten von weiteren Informationen zu Azure Stack. In integrierten Systemen wird beim Erstellen einer Supportanfrage der Azure Stack-Dienst vorab ausgewählt. Wir empfehlen dringend, dass Kunden anstelle des globalen Azure-Portals diese Vorgehensweise zum Übermitteln von Tickets nutzen. Weitere Informationen finden Sie unter [Microsoft Azure Stack: Hilfe und Support](azure-stack-help-and-support-overview.md).

- Wenn das Onboarding für mehrere Azure Active Directory-Instanzen durchgeführt wird (über [diesen Prozess](azure-stack-enable-multitenancy.md)), kann auf das erneute Ausführen des Skripts ggf. verzichtet werden, falls bestimmte Updates auftreten oder Änderungen an der Autorisierung mit dem Azure AD-Dienstprinzipal zu fehlenden Rechten führen. Dies kann zu verschiedenen Problemen führen: von einer Sperrung des Zugriffs für bestimmte Features bis zu diskreteren Ausfällen, die sich nur schwer zum ursprünglichen Problem nachverfolgen lassen. Als Gegenmaßnahme wurde mit Version 1905 ein neues Feature eingeführt, mit dem diese Berechtigungen überprüft werden und eine Warnung erstellt wird, wenn bestimmte Konfigurationsprobleme ermittelt werden. Diese Überprüfung wird einmal pro Stunde durchgeführt, und es werden die Aktionen zur Behebung angezeigt, die zum Lösen des Problems erforderlich sind. Die Warnung wird geschlossen, wenn sich alle Mandanten in einem fehlerfreien Zustand befinden.

- Die Zuverlässigkeit von Vorgängen für Infrastruktursicherungen während des Dienstfailovers wurde verbessert.

- Eine neue Version des [Azure Stack-Nagios-Plug-Ins](azure-stack-integrate-monitor.md#integrate-with-nagios) ist verfügbar, für das die [Azure Active Directory-Authentifizierungsbibliotheken](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) zur Authentifizierung genutzt werden. Das Plug-In unterstützt für Azure Stack jetzt auch Bereitstellungen vom Typ „Azure AD“ und „Active Directory-Verbunddienste (AD FS)“. Weitere Informationen finden Sie auf der Website zum [Nagios-Plug-In](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details).

- Das neue Hybridprofil **2019-03-01-Hybrid** wurde veröffentlicht. Es unterstützt alle aktuellen Features in Azure Stack. Sowohl Azure PowerShell als auch Azure CLI unterstützen das Profil **2019-03-01-Hybrid**. Für .NET, Ruby, Node.js, Go und Python SDKs wurden Pakete veröffentlicht, die das Profil **2019-03-01-Hybrid** unterstützen. Die entsprechende Dokumentation und einige Beispiele wurden aktualisiert, um die Änderungen widerzuspiegeln.

- Das [Node.js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid) unterstützt jetzt API-Profile. Pakete, die das Profil **2019-03-01-Hybrid** unterstützen, werden veröffentlicht.

- Das Azure Stack-Update 1905 fügt zwei neue Infrastrukturrollen hinzu, um die Zuverlässigkeit und Unterstützbarkeit der Plattform zu verbessern:

  - **Infrastrukturring**: In Zukunft wird der Infrastrukturring Versionen von vorhandenen Infrastrukturrollen in Containern hosten – z. B. xrp, die derzeit ihre eigenen dedizierten Infrastruktur-VMs erfordern. Dies verbessert die Zuverlässigkeit der Plattform und verringert die Anzahl von Infrastruktur-VMs, die Azure Stack benötigt. Dies wiederum verringert in der Folge den Gesamtressourcenverbrauch von Azure Stack-Infrastrukturrollen in der Zukunft.
  - **Supportring**: In Zukunft wird der Supportring verwendet, um erweiterte Supportszenarien für Kunden zu verarbeiten.  

  Darüber hinaus haben wir eine zusätzliche Instanz der Domänencontroller-VM hinzugefügt, um die Verfügbarkeit dieser Rolle zu verbessern.

  Diese Änderungen erhöht den Ressourcenverbrauch der Azure Stack-Infrastruktur auf folgende Weise:
  
    | Azure Stack-SKU | Anstieg des Computeverbrauchs | Anstieg des Arbeitsspeicherverbrauchs |
    | -- | -- | -- |
    |4 Knoten|22 vCPU|28 GB|
    |8 Knoten|38 vCPU|44 GB|
    |12 Knoten|54 vCPU|60 GB|
    |16 Knoten|70 vCPU|76 GB|
  
### <a name="changes-1905"></a>Änderungen

- Mit Azure Stack wird eine zusätzliche Infrastrukturrolleninstanz für Domänendienste hinzugefügt, um die Zuverlässigkeit und Verfügbarkeit bei der geplanten und ungeplanten Wartung zu erhöhen.

- Mit diesem Update wird die Hardware bei Vorgängen zum Reparieren und Hinzufügen von Knoten überprüft, um sicherzustellen, dass in einer Skalierungseinheit homogene Skalierungseinheitknoten verwendet werden.

- Wenn geplante Sicherungen nicht durchgeführt werden können und der definierte Aufbewahrungszeitraum überschritten wird, wird mit dem Infrastructure Backup Controller dafür gesorgt, dass mindestens eine erfolgreiche Sicherung aufbewahrt wird. 

### <a name="fixes-1905"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Fehler behoben, bei dem eine Warnung zum **Computehost-Agent** angezeigt wird, nachdem ein Knoten in der Skalierungseinheit neu gestartet wurde.

- Es wurden Probleme mit der Marketplace-Verwaltung im Administratorportal behoben, bei denen nach dem Anwenden von Filtern falsche Ergebnisse und im Herausgeberfilter doppelte Herausgebernamen angezeigt wurden. Außerdem wurde die Leistung verbessert, damit Ergebnisse schneller angezeigt werden.

- Es wurde ein Problem auf dem Blatt mit den verfügbaren Sicherungen behoben, bei dem eine neue verfügbare Sicherung aufgeführt wurde, bevor der Upload an den externen Speicherort abgeschlossen war. Die verfügbare Sicherung wird jetzt erst dann in der Liste angezeigt, nachdem der Upload an den Speicherort erfolgreich abgeschlossen wurde. 

<!-- ICM: 114819337; Task: 4408136 -->
- Es wurde ein Problem mit dem Abrufen von Wiederherstellungsschlüsseln während eines Sicherungsvorgangs behoben. 

<!-- Bug: 4525587 -->
- Es wurde ein Problem behoben, bei dem für die Version des OEM-Updates im Betreiberportal „Undefiniert“ angezeigt wurde.

### <a name="security-updates-1905"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning-1905"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update-1905"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1905 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen. Stellen Sie bei Verwendung des Downloadtools sicher, dass Sie die neueste Version und keine zwischengespeicherte Kopie aus Ihrem Verzeichnis „Downloads“ verwenden.

## <a name="hotfixes-1905"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1904 installieren, bevor Sie Azure Stack auf 1905 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1905-update"></a>Vor dem Anwenden des Updates 1905

Version 1905 von Azure Stack muss auf Version 1904 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Nach erfolgreicher Anwendung des Updates 1905

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Systeme, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivieren

Um auf archivierte Versionshinweise für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 – archivierte Versionshinweise
::: moniker-end

::: moniker range="<azs-1905"
Sie können auf [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack-Support finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end


