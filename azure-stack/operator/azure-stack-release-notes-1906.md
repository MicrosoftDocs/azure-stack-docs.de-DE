---
title: Versionshinweise zu Azure Stack 1906 | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen im Update 1906 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 07/01/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/01/2019
ms.openlocfilehash: cc348bba064ddb003bbe6a2a06d2694714ad7894
ms.sourcegitcommit: 068350a79805366e7e6536fb7df85a412bd0be99
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67511278"
---
# <a name="azure-stack-1906-update"></a>Azure Stack-Update 1906

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird der Inhalt des Updatepakets 1906 beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack.

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1906 ist **1.1906.0.30**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack 1906-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Das Update 1906 dauert etwa 10 Stunden, unabhängig von der Anzahl der physischen Knoten in Ihrer Azure Stack-Umgebung. Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl. Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1906 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ein **Set-TLSPolicy**-Cmdlet wurde im privilegierten Endpunkt (PEP) hinzugefügt, um TLS 1.2 für alle Endpunkte zu erzwingen. Weitere Informationen finden Sie unter [Azure Stack-Sicherheitskontrollen](azure-stack-security-configuration.md).

- Ein **Get-TLSPolicy**-Cmdlet wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die angewendete TLS-Richtlinie abzurufen. Weitere Informationen finden Sie unter [Azure Stack-Sicherheitskontrollen](azure-stack-security-configuration.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

- Eine Schutzvorrichtung wurde hinzugefügt, um das Ablaufen interner Geheimnisse zu verhindern, indem die Rotation interner Geheimnisse, falls eine kritische Warnung bei ablaufenden Geheimnissen ignoriert wird, zu erzwingen. Dies sollte nicht als normales Verfahren angesehen und auch nicht so verwendet werden. Die Geheimnisrotation sollte während eines Wartungsfensters eingeplant werden. Weitere Informationen finden Sie unter [Azure Stack: Geheimnisrotation](azure-stack-rotate-secrets.md).

- Visual Studio Code wird jetzt in Azure Stack-Bereitstellungen, die AD FS verwenden, unterstützt.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Das Cmdlet **Get-GraphApplication** im privilegierten Endpunkt zeigt jetzt den Fingerabdruck des aktuell verwendeten Zertifikats an. Dies verbessert die Zertifikatverwaltung für Dienstprinzipale, wenn Azure Stack mit AD FS bereitgestellt wird.

- Neue Regeln für die Systemüberwachung wurden hinzugefügt, um die Verfügbarkeit von AD Graph und AD FS zu überprüfen, einschließlich der Möglichkeit zum Auslösen von Warnungen.

- Verbesserungen an der Zuverlässigkeit des Sicherungsressourcenanbieters, wenn der Dienst für die Infrastruktursicherung auf eine andere Instanz verschoben wird.

- Optimierung der Leistung des externen Geheimnisrotationsverfahrens, um eine einheitliche Ausführungszeit bereitzustellen, um die Planung des Wartungsfensters zu erleichtern.

- Das Cmdlet **Test-AzureStack** berichtet jetzt zu internen Geheimnissen, die dabei sind, abzulaufen (kritische Warnungen).

- Ein neuer Parameter ist für das Cmdlet **Register-CustomAdfs** im privilegierten Endpunkt verfügbar, der es ermöglicht, die Überprüfung der Zertifikatssperrliste zu überspringen, wenn die Verbundvertrauensstellung für AD FS konfiguriert wird.

- Das Release 1906 führt bessere Sichtbarkeit des Updatestatus ein, sodass Sie sicher sein können, dass Updates nicht angehalten sind. Dies führt zu einer Erhöhung der Gesamtzahl der Updateschritte, die Operatoren auf dem Blatt **Update** angezeigt werden. Möglicherweise bemerken Sie auch, dass mehr Updateschritte gleichzeitig ausgeführt werden als bei früheren Updates.

#### <a name="networking-updates"></a>Netzwerkupdates

- Die im DHCP-Responder festgelegte Leasedauer wurde so aktualisiert, dass sie mit Azure konsistent ist.

- Verbesserte Wiederholungsraten für den Ressourcenanbieter im Falle der fehlerhaften Bereitstellung von Ressourcen.

- Die SKU-Option **Standard** wurde sowohl aus dem Load Balancer als auch der öffentlichen IP-Adresse entfernt, da dies zurzeit nicht unterstützt wird.

### <a name="changes"></a>Änderungen

- Das Erstellen eine Speicherkontoerfahrung ist jetzt mit Azure konsistent.

- Auslöser für Warnungen bei Ablauf interner Geheimnisse wurden geändert:
  - Warnungen werden jetzt 90 Tage vor dem Ablauf von Geheimnissen ausgelöst.
  - Kritische Warnungen werden jetzt 30 Tage vor dem Ablauf von Geheimnissen ausgelöst.

- Zeichenfolgen im Ressourcenanbieter für die Infrastruktursicherung wurden hinsichtlich konsistenter Terminologie aktualisiert.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Ein Problem wurde behoben, bei dem das Ändern der Größe einer VM mit verwaltetem Datenträger mit einem Fehler **Interner Vorgangsfehler** fehlschlug.

- Ein Problem wurde behoben, bei dem die fehlgeschlagene Erstellung eines Benutzerimages den Dienst, der Images verwaltet, in einen ungültigen Zustand versetzt. Hierdurch wird das Löschen des fehlerhaften Images und die Erstellung neuer Images blockiert. Dies ist auch im Hotfix 1905 behoben.

- Aktive Warnungen wegen abgelaufener interner Geheimnisse werden jetzt nach erfolgreicher Ausführung der internen Geheimnisrotation automatisch geschlossen.

- Ein Problem wurde behoben, bei dem die erste Ziffer der Updatedauer auf der Registerkarte „Updateverlauf“ abgeschnitten wurde, wenn das Update länger als 99 Stunden ausgeführt wurde.

- Das Blatt **Update** enthält eine Option **Fortsetzen** für fehlgeschlagene Updates.

- In den Administrator- und Benutzerportalen wurde das Problem im Marketplace behoben, bei dem die Docker-Erweiterung fehlerhaft von der Suche zurückgegeben wurde, aber keine weitere Maßnahme ergriffen werden konnte, da sie nicht in Azure Stack verfügbar war.

- Ein Problem in der Benutzeroberfläche für die Vorlagenbereitstellung wurde behoben, bei dem Parameter nicht aufgefüllt wurden, wenn der Vorlagenname mit „_“ (Unterstrich) begann.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](azure-stack-release-notes-security-updates-1906.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](azure-stack-release-notes-known-issues-1906.md)
- [Sicherheitsupdates](azure-stack-release-notes-security-updates-1906.md)
- [Azure Stack-Checkliste für Updateaktivitäten](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1906 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1905 installieren, bevor Sie Azure Stack auf 1906 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1906-update"></a>Vor dem Anwenden des Updates 1906

Version 1906 von Azure Stack muss auf Version 1905 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Nach erfolgreicher Anwendung des Updates 1906

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1906.2.37](https://support.microsoft.com/help/4511282)

## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Kunden mit Systemen, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

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
