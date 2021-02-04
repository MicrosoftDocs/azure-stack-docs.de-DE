---
title: Azure Stack-Update 1811 | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1811 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 76a9909ac35197d8e51115e07d977a0ee39442b5
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248697"
---
# <a name="azure-stack-1811-update"></a>Azure Stack-Update 1811

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt den Inhalt des Updatepakets 1811. Das Updatepaket enthält Verbesserungen, Fehlerbehebungen und neue Funktionen für diese Version von Azure Stack. In diesem Artikel werden auch bekannte Probleme in diesem Release beschrieben, und er enthält einen Link, damit Sie das Update herunterladen können. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1811 ist **1.1811.0.101**.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie das [neueste Azure Stack-Hotfix](#azure-stack-hotfixes) für 1809 installieren, bevor Sie Azure Stack auf 1811 aktualisieren.

### <a name="azure-stack-hotfixes"></a>Azure Stack-Hotfixes

- **1809**: [KB 4481548 – Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Es ist kein aktueller Hotfix verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Während der Installation des Updates 1811 müssen Sie sicherstellen, dass alle Instanzen des Administratorportals geschlossen sind. Das Benutzerportal kann geöffnet bleiben, aber das Administratorportal muss geschlossen sein.

- Bereiten Sie Ihre Azure Stack-Bereitstellung für den Azure Stack-Erweiterungshost vor. Bereiten Sie Ihr System anhand der folgenden Anleitung vor: [Vorbereiten auf den Erweiterungshost für Azure Stack](../azure-stack-extension-host-prepare.md). 
 
- Installieren Sie den [neuesten Azure Stack-Hotfix](#azure-stack-hotfixes) für 1809, bevor Sie auf 1811 aktualisieren.

- Bevor Sie mit der Installation dieses Updates beginnen, führen Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) mit den folgenden Parametern aus, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und lösen Sie solche auf, die eine Aktion erfordern.  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Wenn Sie die Anforderungen für den Erweiterungshost nicht erfüllen, wird in der Ausgabe von `Test-AzureStack` die folgende Meldung angezeigt: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare`

- Das Azure Stack-Update 1811 erfordert, dass Sie die obligatorischen Erweiterungshostzertifikate ordnungsgemäß in Ihre Azure Stack-Umgebung importiert haben. Um mit der Installation des Updates 1811 fortzufahren, müssen Sie die für den Erweiterungshost erforderlichen SSL-Zertifikate importieren. Informationen zum Importieren der Zertifikate finden Sie [in diesem Abschnitt](../azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Wenn Sie alle Warnungen ignorieren und trotzdem die Installation des Updates 1811 durchführen, schlägt das Update in etwa 1 Stunde mit der folgenden Meldung fehl:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Nachdem Sie die obligatorischen Erweiterungshostzertifikate ordnungsgemäß importiert haben, können Sie das Update 1811 aus dem Administratorportal fortsetzen. Zwar rät Microsoft Azure Stack-Betreibern, während des Updateprozesses ein Wartungsfenster zu planen, doch sollte ein Fehler aufgrund der fehlenden Erweiterungshostzertifikate keine Auswirkungen auf vorhandene Workloads oder Dienste haben.  

    Während der Installation dieses Updates ist das Azure Stack-Benutzerportal nicht verfügbar, solange der Erweiterungshost konfiguriert wird. Die Konfiguration des Erweiterungshosts kann bis zu fünf Stunden dauern. Während dieser Zeit können Sie den Status eines Updates überprüfen oder eine nicht erfolgreiche Updateinstallation über [PowerShell für Azure Stack-Administratoren oder über den privilegierten Endpunkt](../azure-stack-monitor-update.md) fortsetzen.

- Wenn Azure Stack von System Center Operations Manager (SCOM) verwaltet wird, müssen Sie das Management Pack für Microsoft Azure Stack auf Version 1.0.3.11 aktualisieren, bevor Sie das Update 1811 anwenden.

## <a name="new-features"></a>Neue Funktionen

Dieses Update enthält die folgenden neuen Funktionen und Verbesserungen für Azure Stack:

- In diesem Release ist der [Erweiterungshost](../azure-stack-extension-host-prepare.md) aktiviert. Der Erweiterungshost vereinfacht die Netzwerkintegration und verbessert die Sicherheitsausrichtung von Azure Stack.

- Unterstützung für Geräteauthentifizierung mit Active Directory-Verbunddiensten (AD FS) wurde hinzugefügt, insbesondere wenn die Azure-Befehlszeilenschnittstelle verwendet wird. Weitere Informationen finden Sie unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack](../../user/azure-stack-version-profiles-azurecli2.md).

- Unterstützung für Dienstprinzipale, die einen geheimen Clientschlüssel mit Active Directory-Verbunddiensten (AD FS) verwenden, wurde hinzugefügt. Weitere Informationen finden Sie unter [Erstellen von Dienstprinzipalen für AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Dieses Release fügt Unterstützung wurde die folgenden Versionen der Azure Storage Service-API hinzu: **2017-07-29**, **2017-11-09**. Unterstützung wird auch für die folgenden Versionen der Azure Storage-Ressourcenanbieter-API hinzugefügt: **2016-05-01**, **2016-12-01**, **2017-06-01** und **2017-10-01**. Weitere Informationen finden Sie unter [Azure Stack-Speicher: Unterschiede und Überlegungen](../../user/azure-stack-acs-differences.md).

- Neue privilegierte Endpunktbefehle zum Aktualisieren und Entfernen von Dienstprinzipalen für AD FS wurden hinzugefügt. Weitere Informationen finden Sie unter [Erstellen von Dienstprinzipalen für AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Neue Vorgänge für Skalierungseinheitknoten wurden hinzugefügt, die einem Azure Stack-Betreiber erlauben, einen Skalierungseinheitknoten zu starten, zu beenden und herunterzufahren. Weitere Informationen finden Sie unter [Knotenaktionen für Skalierungseinheiten in Azure Stack](../azure-stack-node-actions.md).

- Ein neues Blatt mit Regionseigenschaften wurde hinzugefügt, auf dem Registrierungsdetails der Umgebung angezeigt werden. Sie können diese Informationen anzeigen, indem Sie auf die Kachel **Regionsverwaltung** im Standarddashboard im Administratorportal klicken und dann **Eigenschaften** auswählen.

- Ein neuer privilegierter Endpunktbefehl wurde hinzugefügt, der die BMC-Anmeldeinformationen mit Benutzernamen und Kennwort aktualisiert, die zur Kommunikation mit dem physischen Computer verwendet werden. Weitere Informationen finden Sie unter [Aktualisieren der BMC\(-Anmeldeinformationen (Baseboard Management Controller, Baseboard-Verwaltungscontroller)](../azure-stack-rotate-secrets.md).

- Die Möglichkeit, über das Hilfe- und Supportsymbol (Fragezeichen) oben rechts im Administrator- und Benutzerportal auf die Azure-Roadmap zuzugreifen, wurde hinzugefügt, ähnlich wie dies auch im Azure-Portal verfügbar ist.

- Eine verbesserte Marketplace-Verwaltungserfahrung für getrennte Benutzer wurde hinzugefügt. Der Uploadprozess zum Veröffentlichen eines Marketplace-Elements in einer getrennten Umgebung wurde zu einem Schritt vereinfacht, sodass das Bild und das Marketplace-Paket nicht mehr getrennt hochgeladen werden müssen. Das hochgeladene Produkt wird auch auf dem Blatt für die Marketplace-Verwaltung angezeigt.

- Dieses Release verkürzt das erforderliche Wartungsfenster für die Geheimnisrotation durch Hinzufügen der Möglichkeit, nur externe Zertifikate während der [Geheimnisrotation von Azure Stack](../azure-stack-rotate-secrets.md) zu rotieren.

- [Azure Stack PowerShell](../azure-stack-powershell-install.md) wurde auf Version 1.6.0 aktualisiert. Das Update enthält Unterstützung für die neuen speicherbezogenen Funktionen in Azure Stack. Weitere Informationen finden Sie in den Anmerkungen zur Version für das [Azure Stack-Verwaltungsmodul 1.6.0 im PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureStack/1.6.0). Informationen zum Aktualisieren oder Installieren von Azure Stack-PowerShell finden Sie unter [Installieren von PowerShell für Azure Stack](../azure-stack-powershell-install.md).

- Managed Disks ist jetzt standardmäßig aktiviert, wenn virtuelle Computer über das Azure Stack-Portal erstellt werden. Zusätzliche Schritte, die erforderlich sind, damit Managed Disks Fehler bei der VM-Erstellung vermeidet, finden Sie im Abschnitt [Bekannte Probleme](#known-issues-post-installation).

- Dieses Release führt Warnungsaktionen **Reparieren** für Azure Stack-Bediener ein. Einige Warnungen in 1811 bieten eine Schaltfläche **Reparieren** in der Warnmeldung, die Sie auswählen können, um das Problem zu beheben. Weitere Informationen finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack](../azure-stack-monitor-health.md).

- Updates für die Updateerfahrung in Azure Stack. Die Updateverbesserungen umfassen: 
  - Registerkarten, die die Updates vom Updateverlauf trennen, damit sich in Ausführung befindliche Updates und abgeschlossene Updates besser nachverfolgen lassen.
  - Verbesserte Zustandsvisualisierungen im Abschnitt „Essentials“ mit neuen Symbole und Layout für aktuelle und OEM-Versionen sowie Datum der letzten Aktualisierung.
  - Über den Link **Anzeigen** für die Spalte mit den Versionshinweisen gelangt der Benutzer direkt zu der Dokumentation, die für dieses Update spezifisch ist, anstatt zur generischen Updateseite.
  - Auf der Registerkarte **Updateverlauf** wurden bisher Laufzeiten für jedes der Updates bestimmt sowie erweiterte Filterfunktionen.  
  - Azure Stack-Skalierungseinheiten, die verbunden sind, werden weiterhin automatisch mit **Update verfügbar** versehen, sobald sie verfügbar sind.
  - Azure Stack-Skalierungseinheiten, die nicht verbunden sind, können die Updates wie vorher auch importieren. 
  - Beim Prozess des Herunterladens der JSON-Protokolle aus dem Portal gibt es keine Änderungen. Azure Stack-Betreibern werden sich erweiternde Schritte angezeigt, die den Fortschritt darstellen.

    Weitere Informationen finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Behobene Probleme

<!-- TBD - IS ASDK --> 
- Ein Problem wurde behoben, bei dem die Ansicht für die Nutzungsdaten öffentlicher IP-Adressen denselben **EventDateTime**-Wert für jeden Datensatz anzeigte, statt den **TimeDate**-Stempel, der anzeigt, wann der Datensatz jeweils erstellt wurde. Sie können jetzt diese Daten nutzen, um eine genaue Buchhaltung über die Nutzung öffentlicher IP-Adressen durchzuführen.

<!-- 3099544 – IS, ASDK --> 
- Ein Problem wurde behoben, das auftrat, wenn ein neuer virtueller Computer (VM) mithilfe des Azure Stack-Portals erstellt wurde. Durch die Auswahl der VM-Größe wurde in der Spalte **USD/Monat** die Meldung **Nicht verfügbar** angezeigt. Diese Spalte wird nicht mehr angezeigt. Die Anzeige der VM-Preisspalte wird in Azure Stack nicht unterstützt.

<!-- 2930718 - IS ASDK --> 
- Ein Problem wurde behoben, bei dem im Administratorportal beim Zugriff auf die Details eines Benutzerabonnements nach dem Schließen des Blatts und dem Klicken auf **Aktuell** der Name des Benutzerabonnements nicht angezeigt wurde. Der Name des Benutzerabonnements wird jetzt angezeigt.

<!-- 3060156 - IS ASDK --> 
- Ein Problem wurde behoben, bei dem sowohl im Administrator- als auch im Benutzerportal das Klicken auf die Portaleinstellungen und das Auswählen von **Alle Einstellungen und private Dashboards löschen** nicht wie erwartet funktionierte. Es wurde auch keine Fehlerbenachrichtigung angezeigt. Diese Option funktioniert jetzt ordnungsgemäß.

<!-- 2930799 - IS ASDK --> 
- Ein Problem wurde behoben, bei dem in den Administrator- und Benutzerportalen unter **Alle Dienste** die Ressource **DDoS Protection-Pläne** nicht ordnungsgemäß aufgeführt wurde. Sie ist in Azure Stack nicht verfügbar. Die Auflistung wurde entfernt.
 
<!--2760466 – IS  ASDK --> 
- Ein Problem wurde behoben, bei dem bei der Installation einer neuen Azure Stack-Umgebung, die Warnung, dass eine **Aktivierung erforderlich** ist, nicht angezeigt wurde. Sie wird nun korrekt angezeigt.

<!--1236441 – IS  ASDK --> 
- Ein Problem wurde behoben, das die Anwendung von RBAC-Richtlinien auf eine Benutzergruppe verhinderte, wenn ADFS verwendet wurde.

<!--3463840 - IS, ASDK --> 
- Ein Problem wurde behoben, bei dem Infrastruktursicherungen fehlschlugen, weil vom öffentlichen VIP-Netzwerk nicht auf den Dateiserver zugegriffen werden konnte. Diese Korrektur verschiebt den Dienst für die Infrastruktursicherung zurück in das öffentliche Infrastrukturnetzwerk. Wenn Sie den neueste [Azure Stack-Hotfix für 1809](#azure-stack-hotfixes) angewendet haben, der dieses Problem behebt, nimmt das 1811-Update keine weiteren Änderungen vor. 

<!-- 2967387 – IS, ASDK --> 
- Ein Problem wurde behoben, bei dem das Konto, mit dem Sie sich beim Administrator- oder Benutzerportal von Azure Stack angemeldet hatten, als **Unbekannter Benutzer** angezeigt wurde. Diese Meldung wurde angezeigt, wenn für das Konto kein **Vorname** oder **Nachname** angegeben war.   

<!--  2873083 - IS ASDK --> 
- Ein Problem wurde behoben, bei dem durch die Verwendung des Portals zum Erstellen einer VM-Skalierungsgruppe das Dropdownmenü **Instanzgröße** bei Verwendung von Internet Explorer nicht ordnungsgemäß geladen wurde. Dieser Browser funktioniert jetzt ordnungsgemäß.  

<!-- 3190553 - IS ASDK -->
- Ein Problem wurde behoben, bei dem falsch positive Warnungen generiert wurden, die anzeigten, dass eine Infrastrukturrolleninstanz nicht verfügbar ist oder dass der Knoten „Skalierungseinheit“ offline ist.

<!-- 2724961 - IS ASDK -->
- Ein Problem wurde behoben, bei dem die VM-Übersichtsseite das VM-Metrikendiagramm nicht ordnungsgemäß anzeigen kann. 

## <a name="changes"></a>Änderungen

- Eine neue Methode zum Anzeigen und Bearbeiten der Kontingente in einem Plan wird in 1811 eingeführt. Weitere Informationen finden Sie unter [Anzeigen eines vorhandenen Kontingents](../azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Sicherheitsverbesserungen in diesem Update führen zu einem Anstieg der Größe der Sicherung der Verzeichnisdienstrolle. Aktualisierte Größenanpassungsinformationen für den externen Speicherort finden Sie in der [Dokumentation zur Sicherung der Infrastruktur](../azure-stack-backup-reference.md#storage-location-sizing). Diese Änderung führt dazu, dass die Sicherung wegen der größeren Menge zu übertragender Daten länger braucht bis zum Abschluss. Diese Änderung betrifft integrierte Systeme. 

- Das vorhandene PEP-Cmdlet zum Abrufen der BitLocker-Wiederherstellungsschlüssel wurde in 1811 von „Get-AzsCsvsRecoveryKeys“ in „Get-AzsRecoveryKeys“ umbenannt. Weitere Informationen zum Abrufen der BitLocker-Wiederherstellungsschlüssel finden Sie unter [Anleitungen zum Abrufen der Schlüssel](../azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures (CVE, allgemeine Sicherheitslücken und Schwachstellen)

Dieses Update installiert die folgenden Sicherheitsupdates:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Weitere Informationen zu diesen Sicherheitslücken erhalten Sie durch Klicken auf die vorhergehenden Links oder im Microsoft Knowledge Base-Artikel [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Wenn Sie das PowerShell-Cmdlet **Get-AzureStackLog** nach Ausführung von **Test-AzureStack** in derselben Sitzung für den privilegierten Endpunkt (PEP) ausführen, kommt es für **Get-AzureStackLog** zu einem Fehler. Um dieses Problem zu umgehen, schließen Sie die PEP-Sitzung, in der Sie **Test-AzureStack** ausgeführt haben, und öffnen Sie anschließend eine neue Sitzung zur Ausführung von **Get-AzureStackLog**.

- Stellen Sie sicher, dass während der Installation des Updates 1811 alle Instanzen des Administratorportals geschlossen sind. Das Benutzerportal kann geöffnet bleiben, aber das Administratorportal muss geschlossen sein.

- Wenn während der Ausführung von [Test-AzureStack](../azure-stack-diagnostic-test.md) entweder der Test **AzsInfraRoleSummary** oder der Test **AzsPortalApiSummary** fehlschlägt, werden Sie aufgefordert, **Test-AzureStack** mit dem Parameter `-Repair` auszuführen.  Wenn Sie diesen Befehl ausführen, schlägt er mit folgender Fehlermeldung fehl:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.` Dieses Problem wird in einem zukünftigen Release behoben.

- Während der Installation des Updates 1811 ist das Azure Stack-Benutzerportal nicht verfügbar, solange der Erweiterungshost konfiguriert wird. Die Konfiguration des Erweiterungshosts kann bis zu fünf Stunden dauern. Während dieser Zeit können Sie den Status eines Updates überprüfen oder eine nicht erfolgreiche Updateinstallation über [PowerShell für Azure Stack-Administratoren oder über den privilegierten Endpunkt](../azure-stack-monitor-update.md) fortsetzen. 

- Während der Installation des Updates 1811 steht möglicherweise das Benutzerportal-Dashboard nicht zur Verfügung, und Anpassungen können verloren gehen. Sie können das Dashboard nach Abschluss des Updates auf die Standardeinstellung zurücksetzen, indem Sie die Portaleinstellungen öffnen und **Standardeinstellungen wiederherstellen** auswählen.

- Wenn Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) ausführen, wird eine Warnmeldung des Baseboard-Verwaltungscontrollers (BMC) angezeigt. Sie können diese Warnung problemlos ignorieren.

- <!-- 2468613 - IS --> Während der Installation dieses Updates werden möglicherweise Warnungen mit dem Titel `Error – Template for FaultType UserAccounts.New is missing.` angezeigt. Diese Warnungen können ignoriert werden. Die Warnungen werden automatisch geschlossen, nachdem die Installation dieses Updates abgeschlossen wurde.

- <!-- 3139614 | IS --> Wenn Sie ein Update Ihres OEM auf Azure Stack angewendet haben, wird die Benachrichtigung **Update verfügbar** möglicherweise nicht im Azure Stack-Administratorportal angezeigt. Um das Microsoft-Update zu installieren, befolgen Sie die Anweisungen unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md), um es manuell herunterzuladen und zu installieren.

## <a name="post-update-steps"></a>Schritte nach dem Update

- Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie sowohl unter [Hotfixes](#hotfixes) als auch [Wartungsrichtlinie](../azure-stack-servicing-policy.md).  

- Rufen Sie die Verschlüsselungsschlüssel für ruhende Daten ab, und speichern Sie diese sicher außerhalb Ihrer Azure Stack-Bereitstellung. Befolgen Sie die [Anleitungen zum Abrufen der Schlüssel](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

Im Folgenden werden bekannte Probleme nach der Installation zu dieser Buildversion vorgestellt.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- Wenn Sie im Administrator- oder im Benutzerportal nach „Docker“ suchen, wird das Element nicht ordnungsgemäß zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, sie zu erstellen, wird ein Blatt mit einem Fehlerhinweis angezeigt. 

<!-- 2931230 – IS  ASDK --> 
- Pläne, die einem Benutzerabonnement als Add-On-Plan hinzugefügt wurden, können nicht gelöscht werden, auch wenn Sie den Plan aus dem Benutzerabonnement entfernen. Der Plan ist so lange vorhanden, bis die Abonnements gelöscht werden, die auf den Add-On-Plan verweisen. 

<!-- TBD - IS ASDK --> 
- Die zwei administrativen Abonnementtypen, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**. Diese Abonnementtypen sind in neuen Azure Stack-Umgebungen ab Version 1804 sichtbar, aber noch nicht zur Verwendung bereit. Sie sollten den Abonnementtyp **Standardanbieter** weiterhin verwenden.

<!-- TBD - IS ASDK --> 
- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

<!-- TBD - IS ASDK --> 
- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung [PowerShell, um Berechtigungen zu überprüfen](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Integrität und Überwachung

<!-- 1264761 - IS ASDK -->  
-  Möglicherweise werden Warnungen für die **Health Controller**-Komponente mit folgenden Details angezeigt:  

  - Warnung 1:
     - NAME: Infrastrukturrolle fehlerhaft
     - SCHWEREGRAD: Warnung
     - KOMPONENTE: Health Controller
     - BESCHREIBUNG: Heartbeat Scanner von Health Controller ist nicht verfügbar. Dies kann sich auf Integritätsberichte und Metriken auswirken.  

  - Warnung 2:
     - NAME: Infrastrukturrolle fehlerhaft
     - SCHWEREGRAD: Warnung
     - KOMPONENTE: Health Controller
     - BESCHREIBUNG: Fault Scanner von Health Controller ist nicht verfügbar. Dies kann sich auf Integritätsberichte und Metriken auswirken.

    Beide Warnungen können ignoriert werden. Sie werden nach einem bestimmten Zeitraum automatisch geschlossen.  

### <a name="compute"></a>Compute

- Beim Erstellen eines neuen virtuellen Windows-Computer s erfordert das Blatt **Einstellungen**, dass Sie einen öffentlichen eingehenden Port auswählen, um fortzufahren. In 1811 ist diese Einstellung zwar erforderlich, hat aber keine Auswirkungen. Dies liegt daran, dass die Funktion von Azure Firewall abhängig ist, die in Azure Stack nicht implementiert ist. Sie können **Keine öffentlichen Eingangsports** oder eine beliebige der anderen Optionen auswählen, um mit dem Erstellen des virtuellen Computers fortzufahren. Die Einstellung hat keine Auswirkungen.

- Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen. Um dieses Problem zu umgehen, erstellen Sie das Speicherkonto erneut mit demselben Namen wie zuvor.

- Beim Erstellen eines [virtuellen Computers der Dv2-Serie](../../user/azure-stack-vm-considerations.md#vm-sizes) gestatten Ihnen D11-14v2 VMs das Erstellen von 4, 8, 16 bzw. 32 Datenträgern. Im Bereich „VM erstellen“ werden jedoch 8, 16, 32 und 64 Datenträger angezeigt.

- Verwendungseinträge in Azure Stack können unerwartete Großschreibung enthalten, z. B.:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   In diesem Beispiel sollte der Name der Ressourcengruppe **AndrewRG** lauten. Sie können diese Inkonsistenz problemlos ignorieren.

<!-- 3235634 – IS, ASDK -->
- Um VMs mit Größen bereitzustellen, die das Suffix **v2** enthalten – z. B. **Standard_A2_v2** –, geben Sie das Suffix mit klein geschriebenem „v“ an: **Standard_A2_v2**. Verwenden Sie nicht **Standard_A2_V2** mit groß geschriebenem „V“. Dies funktioniert in der globalen Azure-Umgebung und ist eine Inkonsistenz in Azure Stack.

<!-- 2869209 – IS, ASDK --> 
-  Bei Verwendung des [**Add-AzsPlatformImage**-Cmdlets](/powershell/module/azs.compute.admin/add-azsplatformimage) müssen Sie den **-OsUri**-Parameter als Speicherkonto-URI beim Hochladen des Datenträgers verwenden. Wenn Sie den lokalen Pfad des Datenträgers verwenden, schlägt das Cmdlet mit der folgenden Fehlermeldung fehl: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Wenn Sie das Portal zum Erstellen virtueller Computer in einer Premium-VM-Größe (DS, Ds_v2, FS, FSv2) verwenden, wird der virtuelle Computer in einem Standardspeicherkonto erstellt. Die Erstellung in einem Standardspeicherkonto wirkt sich nicht auf die Funktionalität, IOPs oder die Abrechnung aus. Sie können die folgende Warnung problemlos ignorieren: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- Die Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen bietet „CentOS 7.2-basiert“ als Option für die Bereitstellung an. Da dieses Image in Azure Stack nicht verfügbar ist, wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Operator aus dem Marketplace heruntergeladen wurde.  

<!-- 2724873 - IS --> 
-  Wenn Sie die PowerShell-Cmdlets **Start-AzsScaleUnitNode** oder **Stop-AzsScaleunitNode** verwenden, um Skalierungseinheiten zu verwalten, tritt beim ersten Versuch, die Skalierungseinheit zu starten oder zu stoppen, möglicherweise ein Fehler auf. Wenn beim ersten Ausführen des Cmdlets ein Fehler auftritt, führen Sie das Cmdlet ein zweites Mal aus. Bei der zweiten Ausführung sollte der Vorgang erfolgreich abgeschlossen werden. 

<!-- TBD - IS ASDK --> 
- Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Sie eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.  

<!-- 1662991 IS ASDK --> 
- Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren.  

<!-- 3507629 - IS, ASDK --> 
- Managed Disks erstellt zwei neue [Computekontingenttypen](../azure-stack-quota-types.md#compute-quota-types) zur Begrenzung der maximalen Kapazität von verwalteten Datenträgern, die bereitgestellt werden können. Standardmäßig werden für jeden Kontingenttyp von verwalteten Datenträgern 2.048 GiB zugeordnet. Allerdings können die folgenden Probleme auftreten:

   - Bei Kontingenten, die vor dem Update 1808 erstellt wurden, werden im Administratorportal für das „Managed Disks“-Kontingent 0 Werte angezeigt, obwohl 2.048 GiB zugeordnet wurden. Sie können den Wert auf der Grundlage Ihrer tatsächlichen Anforderungen erhöhen oder verringern. Dann setzt der neu festgelegte Kontingentwert den Standardwert 2.048 GiB außer Kraft.
   - Wenn Sie den Kontingentwert auf „0“ aktualisieren, entspricht dies dem Standardwert 2.048 GiB. Als Problemumgehung können Sie den Kontingentwert auf „1“ festlegen.

<!-- TBD - IS ASDK --> 
- Nach dem Anwenden des Updates 1811 können beim Bereitstellen von virtuellen Computern mit Managed Disks die folgenden Probleme auftreten:

   - Wenn das Abonnement vor dem Update 1808 erstellt wurde, schlägt die Bereitstellung eines virtuellen Computers mit Managed Disks möglicherweise mit einer internen Fehlermeldung fehl. Um den Fehler zu beheben, führen Sie die folgenden Schritte für jedes Abonnement aus:
      1. Navigieren Sie im Mandantenportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Wählen Sie **Ressourcenanbieter** und dann **Microsoft.Compute** aus, und klicken Sie dann auf **Erneut registrieren**.
      2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)**, und überprüfen Sie, ob die Rolle **AzureStack-DiskRP-Client** aufgeführt wird.
   - Wenn Sie eine Umgebung mit mehreren Mandanten konfiguriert haben, schlägt die Bereitstellung von virtuellen Computern in einem Abonnement, dem ein Gastverzeichnis zugeordnet ist, möglicherweise mit einer internen Fehlermeldung fehl. Zum Beheben des Fehlers führen Sie die in [diesem Artikel](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) beschriebenen Schritte aus, um alle Gastverzeichnisse neu zu konfigurieren.

- Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden. Um dieses Problem zu umgehen, verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie kennwortbasierte Authentifizierung.

### <a name="networking"></a>Netzwerk  

<!-- 1766332 - IS ASDK --> 
-  Wenn Sie unter **Netzwerk** auf **VPN-Gateway erstellen** klicken, um eine VPN-Verbindung einzurichten, wird als VPN-Typ **Richtlinienbasiert** aufgeführt. Wählen Sie diese Option nicht aus. Nur die Option **Routenbasiert** wird in Azure Stack unterstützt.

<!-- 1902460 - IS ASDK --> 
- Azure Stack unterstützt ein einzelnes *Gateway eines lokalen Netzwerks* pro IP-Adresse. Dies gilt für alle Mandantenabonnements. Nach der Herstellung der Verbindung mit dem ersten Gateway eines lokalen Netzwerks werden nachfolgende Versuche zum Erstellen einer Ressource für Gateways lokaler Netzwerke mit der gleichen IP-Adresse zurückgewiesen.

<!-- 16309153 - IS ASDK --> 
- In einem virtuellen Netzwerk, das mit der DNS-Servereinstellung **Automatisch** erstellt wurde, tritt bei der Änderung eines benutzerdefinierten DNS-Servers ein Fehler auf. Die aktualisierten Einstellungen werden nicht per Pushvorgang auf VMs in diesem VNET übertragen.

<!-- 2529607 - IS ASDK --> 
- Während der *Geheimnisrotation* von Azure Stack sind öffentliche IP-Adressen für einen Zeitraum von zwei bis fünf Minuten nicht erreichbar.

<!-- 2664148 - IS ASDK --> 
- In Szenarien, in denen der Mandant über einen S2S-VPN-Tunnel auf VMs zugreift, kann es passieren, dass bei Verbindungsversuchen Fehler auftreten, wenn das lokale Subnetz dem lokalen Netzwerkgateway erst nach der Erstellung des Gateways hinzugefügt wurde. 

- Im Azure Stack-Portal wird, wenn Sie eine statische IP-Adresse für eine IP-Konfiguration ändern, die an einen Netzwerkadapter gebunden ist, die an eine VM-Instanz angefügt ist, eine Warnmeldung angezeigt, die besagt: 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Sie können diese Meldung gefahrlos ignorieren. Die IP-Adresse wird auch dann geändert, wenn die VM-Instanz nicht neu gestartet wird.

- Im Portal auf dem Blatt **Netzwerkeigenschaften** befindet sich ein Link für **Wirksame Sicherheitsregeln** für jeden Netzwerkadapter. Wenn Sie diesen Link auswählen, wird ein neues Blatt geöffnet, auf dem die Fehlermeldung `Not Found.` angezeigt wird. Dieser Fehler tritt auf, weil Azure Stack noch keine **Wirksamen Sicherheitsregeln** unterstützt.

- Wenn Sie im Portal eine eingehende Sicherheitsregel hinzufügen und als Quelle **Diensttag** auswählen, werden mehrere Optionen in der Liste **Quelltag** angezeigt, die für Azure Stack nicht verfügbar sind. Die einzigen Optionen, die in Azure Stack gültig sind, lauten folgendermaßen:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    Die anderen Optionen werden nicht als Quelltags in Azure Stack unterstützt. Auf ähnliche Weise wird, wenn Sie eine ausgehende Sicherheitsregel hinzufügen und als Ziel **Diensttag** auswählen, dieselbe Liste von Optionen für **Quelltag** angezeigt. Die einzigen gültigen Optionen sind dieselben wie für **Quelltag**, wie in der vorherigen Liste beschrieben.

- Das PowerShell-Cmdlet **New-AzureRmIpSecPolicy** unterstützt keine Einstellung **DHGroup24** für den Parameter `DHGroup`.

- Netzwerksicherheitsgruppen (NSGs) funktionieren in Azure Stack nicht auf die gleiche Weise wie in Azure weltweit. In Azure können Sie mehrere Ports für eine NSG-Regel festlegen (mit dem Portal, PowerShell und Resource Manager). In Azure Stack können Sie nicht mehrere Ports für eine NSG-Regel über das Portal festlegen. Um dieses Problem zu umgehen, verwenden Sie eine Resource Manager-Vorlage, um diese zusätzlichen Regeln festzulegen.

### <a name="infrastructure-backup"></a>Infrastructure Backup

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Nach dem Aktivieren automatischer Sicherungen wechselt der Planungsdienst unerwartet in den deaktivierten Zustand. Der Sicherungscontrollerdienst erkennt, dass automatische Sicherungen deaktiviert sind und löst eine Warnung im Administratorportal aus. Diese Warnung wird erwartet, wenn automatische Sicherungen deaktiviert sind. 
    - Ursache: Dieses Problem tritt aufgrund eines Fehlers im Dienst auf, der zum Verlust der Planerkonfiguration führt. Dieser Fehler ändert weder den Speicherort noch den Benutzernamen, das Kennwort oder den Verschlüsselungsschlüssel.   
    - Abhilfe: Um dieses Problem zu beheben, öffnen Sie das Blatt mit den Einstellungen des Sicherungscontrollers im Infrastructure Backup-Ressourcenanbieter, und wählen Sie **Automatische Sicherungen aktivieren** aus. Stellen Sie sicher, dass Sie die gewünschte Häufigkeit und den Aufbewahrungszeitraum festlegen.
    - Häufigkeit: Niedrig 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Sie müssen den Speicherressourcenanbieter vor dem Erstellen Ihrer ersten Azure-Funktion im Abonnement registrieren.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog

- Die syslog-Konfiguration wird bei einem Updatezyklus nicht beibehalten, sodass der syslog-Client seine Konfiguration verliert und die syslog-Nachrichten nicht mehr weitergeleitet werden. Dieses Problem betrifft alle Versionen von Azure Stack ab der allgemeinen Verfügbarkeit des syslog-Clients (1809). Um dieses Problem zu umgehen, konfigurieren Sie den syslog-Client nach Anwenden eines Azure Stack-Updates neu.

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket für das Azure Stack-Update 1811 [hier](https://aka.ms/azurestackupdatedownload) herunterladen. 

Nur in verbundenen Szenarios überprüfen Azure Stack-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Weitere Informationen finden Sie unter [Verwalten von Updates für Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](../azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](../azure-stack-monitor-update.md).  
- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).  