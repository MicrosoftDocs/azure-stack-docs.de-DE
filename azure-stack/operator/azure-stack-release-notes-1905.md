---
title: Versionshinweise zu Azure Stack 1905 | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen im Update 1905 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 6f178d0208f5111a7ae60d23e1d914fcf8e3aba5
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397271"
---
# <a name="azure-stack-1905-update"></a>Azure Stack-Update 1905

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird der Inhalt des Updatepakets 1905 beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack. Der Artikel enthält folgende Informationen:

- [Beschreibung der Neuerungen, Verbesserungen, Fehlerbehebungen und Sicherheitsupdates](#whats-in-this-update)
- [Updateplanung](#update-planning)

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1905 ist **1.1905.0.40**.

### <a name="update-type"></a>Updatetyp

Der Azure Stack 1905-Update-Buildtyp lautet **Vollständig**. Daher hat das Update 1905 eine längere Laufzeit als Express-Updates wie 1903 und 1904. Genaue Laufzeiten für vollständige Updates hängen in der Regel von der Anzahl der Knoten ab, die Ihre Azure Stack-Instanz enthält, der auf Ihrem System von Mandantenworkloads verbrauchten Kapazität, der Netzwerkkonnektivität Ihres Systems (falls es mit dem Internet verbunden ist) und Ihrer Hardwarekonfiguration. Das Update 1905 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 35 Stunden, 8 Knoten: 45 Stunden, 12 Knoten: 55 Stunden, 16 Knoten: 70 Stunden. Laufzeiten von 1905, die länger als diese erwarteten Werte sind, sind nicht ungewöhnlich und erfordern kein Eingreifen seitens des Azure Stack-Operators, es sei denn, das Update schlägt fehl. Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Mit diesem Update kann das Updatemodul in Azure Stack die Firmware der Knoten von Skalierungseinheiten aktualisieren. Hierfür ist ein kompatibles Updatepaket von den Hardwarepartnern erforderlich. Ausführlichere Informationen zur Verfügbarkeit erhalten Sie bei Ihrem Hardwarepartner.

- Windows Server 2019 wird jetzt unterstützt und ist für die Syndizierung über den Azure Stack-Marketplace verfügbar.
Mit diesem Update kann Windows Server 2019 auf einem 2016-Host jetzt erfolgreich aktiviert werden.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Im Rahmen der Erzwingung von TLS 1.2 in Azure Stack wurden die folgenden Erweiterungen auf diese Versionen aktualisiert:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Laden Sie diese Versionen der Erweiterungen sofort herunter, damit für neue Bereitstellungen der Erweiterung keine Fehler auftreten, wenn TLS 1.2 in einem zukünftigen Release erzwungen wird. Legen Sie immer **autoUpgradeMinorVersion=true** fest, damit Nebenversionsupdates (z. B. von 1.8 auf 1.9) automatisch durchgeführt werden.

- Eine neue Übersicht für **Hilfe und Support** im Azure Stack-Portal erleichtert Betreibern das Prüfen der Supportoptionen, das Inanspruchnehmen von Expertenhilfe und Erhalten von weiteren Informationen zu Azure Stack. In integrierten Systemen wird beim Erstellen einer Supportanfrage der Azure Stack-Dienst vorab ausgewählt. Wir empfehlen dringend, dass Kunden anstelle des globalen Azure-Portals diese Vorgehensweise zum Übermitteln von Tickets nutzen. Weitere Informationen finden Sie unter [Microsoft Azure Stack: Hilfe und Support](azure-stack-help-and-support-overview.md).

- Wenn das Onboarding für mehrere Azure Active Directory-Instanzen durchgeführt wird (über [diesen Prozess](azure-stack-enable-multitenancy.md)), kann auf das erneute Ausführen des Skripts ggf. verzichtet werden, falls bestimmte Updates auftreten oder Änderungen an der Autorisierung mit dem AAD-Dienstprinzipal zu fehlenden Rechten führen. Dies kann zu verschiedenen Problemen führen: von einer Sperrung des Zugriffs für bestimmte Features bis zu diskreteren Ausfällen, die sich nur schwer zum ursprünglichen Problem nachverfolgen lassen. Als Gegenmaßnahme wurde mit Version 1905 ein neues Feature eingeführt, mit dem diese Berechtigungen überprüft werden und eine Warnung erstellt wird, wenn bestimmte Konfigurationsprobleme ermittelt werden. Diese Überprüfung wird einmal pro Stunde durchgeführt, und es werden die Aktionen zur Behebung angezeigt, die zum Lösen des Problems erforderlich sind. Die Warnung wird geschlossen, wenn sich alle Mandanten in einem fehlerfreien Zustand befinden.

- Die Zuverlässigkeit von Vorgängen für Infrastruktursicherungen während des Dienstfailovers wurde verbessert.

- Eine neue Version des [Azure Stack-Nagios-Plug-Ins](azure-stack-integrate-monitor.md#integrate-with-nagios) ist verfügbar, für das die [Azure Active Directory-Authentifizierungsbibliotheken](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) zur Authentifizierung genutzt werden. Das Plug-In unterstützt für Azure Stack jetzt auch Bereitstellungen vom Typ „Azure Active Directory (AAD)“ und „Active Directory-Verbunddienste (AD FS)“. Weitere Informationen finden Sie auf der Website zum [Nagios-Plug-In](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details).

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
  
- Es gibt jetzt eine Azure Stack-Erweiterung, die in Visual Studio Code funktioniert. Mit der Erweiterung **Azure-Konto** können Entwickler Azure Stack nutzen, indem sie sich anmelden und Abonnements anzeigen. Darüber hinaus können sie auch noch einige andere Dienste nutzen. Die Erweiterung „Azure-Konto“ funktioniert sowohl für AAD-Umgebungen als auch für AD FS-Umgebungen und erfordert nur eine geringfügige Änderung in den Benutzereinstellungen von Visual Studio Code, die das Eingeben von Azure Stack-Metadatenwerten umfasst. Weitere Informationen finden Sie in [dieser Dokumentation](../user/azure-stack-dev-start-vscode-azure.md).

### <a name="changes"></a>Änderungen

- Mit Azure Stack wird eine zusätzliche Infrastrukturrolleninstanz für Domänendienste hinzugefügt, um die Zuverlässigkeit und Verfügbarkeit bei der geplanten und ungeplanten Wartung zu erhöhen.

- Mit diesem Update wird die Hardware bei Vorgängen zum Reparieren und Hinzufügen von Knoten überprüft, um sicherzustellen, dass in einer Skalierungseinheit homogene Skalierungseinheitknoten verwendet werden.

- Wenn geplante Sicherungen nicht durchgeführt werden können und der definierte Aufbewahrungszeitraum überschritten wird, wird mit dem Infrastructure Backup Controller dafür gesorgt, dass mindestens eine erfolgreiche Sicherung aufbewahrt wird. 

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Fehler behoben, bei dem eine Warnung zum **Computehost-Agent** angezeigt wird, nachdem ein Knoten in der Skalierungseinheit neu gestartet wurde.

- Es wurden Probleme mit der Marketplace-Verwaltung im Administratorportal behoben, bei denen nach dem Anwenden von Filtern falsche Ergebnisse und im Herausgeberfilter doppelte Herausgebernamen angezeigt wurden. Außerdem wurde die Leistung verbessert, damit Ergebnisse schneller angezeigt werden.

- Es wurde ein Problem auf dem Blatt mit den verfügbaren Sicherungen behoben, bei dem eine neue verfügbare Sicherung aufgeführt wurde, bevor der Upload an den externen Speicherort abgeschlossen war. Die verfügbare Sicherung wird jetzt erst dann in der Liste angezeigt, nachdem der Upload an den Speicherort erfolgreich abgeschlossen wurde. 

<!-- ICM: 114819337; Task: 4408136 -->
- Es wurde ein Problem mit dem Abrufen von Wiederherstellungsschlüsseln während eines Sicherungsvorgangs behoben. 

<!-- Bug: 4525587 -->
- Es wurde ein Problem behoben, bei dem für die Version des OEM-Updates im Betreiberportal „Undefiniert“ angezeigt wurde.

### <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](azure-stack-release-notes-security-updates-1905.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](azure-stack-release-notes-known-issues-1905.md)
- [Sicherheitsupdates](azure-stack-release-notes-security-updates-1905.md)
- [Azure Stack-Checkliste für Updateaktivitäten](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1905 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen. Stellen Sie bei Verwendung des Downloadtools sicher, dass Sie die neueste Version und keine zwischengespeicherte Kopie aus Ihrem Verzeichnis „Downloads“ verwenden.

## <a name="hotfixes"></a>Hotfixes

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

## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Kunden mit Systemen, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivierte Versionshinweise

Sie können [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](http://aka.ms/azsarchivedrelnotes) anzeigen. Diese archivierten Versionshinweise werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack-Support finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).  

