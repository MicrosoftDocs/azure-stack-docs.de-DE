---
title: Azure Stack-Update 1809 | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1809 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 02/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1548ee295eddd2b386f637301abef208918c21ad
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009212"
---
# <a name="azure-stack-1809-update"></a>Azure Stack-Update 1809

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt den Inhalt des Updatepakets 1809. Das Updatepaket enthält Verbesserungen, Fehlerbehebungen und bekannten Probleme für diese Version von Azure Stack. Dieser Artikel enthält auch einen Link, damit Sie das Update herunterladen können. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1809 ist **1.1809.0.90**.  

### <a name="new-features"></a>Neue Funktionen

Dieses Update enthält die folgenden Verbesserungen für Azure Stack:

- Mit diesem Release unterstützen integrierte Azure Stack-Systeme Konfigurationen mit 4 bis 16 Knoten. Sie können den [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) verwenden, um Unterstützung bei der Planung der Azure Stack-Kapazität und -Konfiguration zu erhalten.

- <!--  2712869   | IS  ASDK -->  **Azure Stack-Syslog-Client (Allgemeine Verfügbarkeit)**: Dieser Client ermöglicht die Weiterleitung von Überwachungen, Warnungen und Sicherheitsprotokollen der Azure Stack-Infrastruktur an einen Syslog-Server oder an SIEM-Software (Security Information and Event Management) außerhalb von Azure Stack. Der Syslog-Client unterstützt jetzt die Angabe des Ports, an dem der Syslog-Server lauscht.

   Mit diesem Release ist der Syslog-Client allgemein verfügbar und kann in Produktionsumgebungen verwendet werden.

   Weitere Informationen finden Sie unter [Azure Stack-Syslog-Weiterleitung](../azure-stack-integrate-security.md).

- Sie können die [Registrierungsressource in Azure zwischen Ressourcengruppen verschieben](../azure-stack-registration.md#move-a-registration-resource), ohne sich erneut registrieren zu müssen. Cloudlösungsanbieter (Cloud Solution Providers, CSPs) können ebenfalls die Registrierungsressource zwischen Abonnements verschieben, sofern sowohl das alte als auch das neue Abonnement der gleichen CSP-Partner-ID zugeordnet sind. Dies wirkt sich nicht auf die vorhandenen Mandantenzuordnungen des Kunden aus. 

- Unterstützung für das Zuweisen mehrerer IP-Adressen pro Netzwerkschnittstelle wurde hinzugefügt.  Ausführlichere Informationen finden Sie unter [Zuweisen von mehreren IP-Adressen zu virtuellen Computern mithilfe von PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Behobene Probleme

<!-- TBD - IS ASDK -->
- Im Portal sind die Angaben zur freien/verwendeten Kapazität im Speicherdiagramm jetzt exakt. Sie können jetzt zuverlässiger prognostizieren, wie viele VMs Sie erstellen können.

<!-- TBD - IS ASDK --> 
- Folgendes Problem wurde behoben: Beim Erstellen von virtuellen Computern im Azure Stack-Benutzerportal zeigte das Portal eine falsche Anzahl von Datenträgern an, die an eine VM der Serie DS angefügt werden können. VMs der Serie DS können so viele Datenträger wie bei der Azure-Konfiguration aufnehmen.

- Die folgenden Probleme mit verwalteten Datenträgern wurden in Version 1809 und im [Azure Stack-Hotfix 1.1808.9.117](https://support.microsoft.com/help/4481066/) für Version 1808 behoben: 

   <!--  2966665 IS, ASDK --> 
  - Das Anfügen von SSD-Datenträgern an virtuelle Computer mit verwalteten Datenträgern von Premiumgröße (DS, DSv2, Fs, Fs_V2) schlug mit der folgenden Fehlermeldung fehl:  *Fehler beim Aktualisieren von Datenträgern für den virtuellen Computer „vmName“: Der angeforderte Vorgang kann nicht ausgeführt werden, weil der Speicherkontotyp „Premium_LRS“ für die VM-Größe („Standard_DS/Ds_V2/FS/Fs_v2“) nicht unterstützt wird*. 
   
  - Das Erstellen eines virtuellen Computers mit verwalteten Datenträgern mithilfe von **createOption**: **Attach** schlägt mit der folgenden Fehlermeldung fehl: *Fehler beim Vorgang mit langer Ausführungszeit mit dem Status ‚Fehler‘. Zusätzliche Information: "Interner Ausführungsfehler".*
    ErrorCode: InternalExecutionError ErrorMessage: Interner Ausführungsfehler.
   
    Dieses Problem wurde jetzt behoben.

- <!-- 2702741 -  IS, ASDK --> Es wurde ein Problem behoben, bei dem mit der dynamischen Zuordnungsmethode bereitgestellte öffentliche IP-Adressen nach der Ausgabe eines Befehls zum Beenden und Aufheben der Zuordnung nicht unbedingt beibehalten wurden. Sie werden jetzt beibehalten.

- <!-- 3078022 - IS, ASDK --> Wenn eine VM vor Version 1808 beendet (Zuordnung aufgehoben) wurde, konnte sie nach dem Update 1808 nicht neu zugeordnet werden.  Dieses Problem wurde in Version 1809 behoben. Instanzen, die sich in diesem Zustand befanden und nicht gestartet werden konnten, können in Version 1809 mit dieser Lösung gestartet werden. Die Lösung verhindert außerdem, dass dieses Problem erneut auftritt.

### <a name="changes"></a>Änderungen

<!-- 2635202 - IS, ASDK -->
- Der Infrastruktursicherungsdienst wird aus dem [öffentlichen Infrastrukturnetzwerk](https://docs.microsoft.com/azure/azure-stack/azure-stack-network) in das [öffentliche VIP-Netzwerk](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network) verschoben. Kunden müssen sicherstellen, dass der Dienst aus dem öffentlichen VIP-Netzwerk auf den Sicherungsspeicherort zugreifen kann.  

> [!IMPORTANT]  
> Sollten Sie über eine Firewall verfügen, die aus dem öffentlichen VIP-Netzwerk keine Verbindungen mit dem Dateiserver zulässt, führt diese Änderung dazu, dass bei Infrastruktursicherungen der Fehler 53 („Der Netzwerkpfad wurde nicht gefunden.“) auftritt. Hierbei handelt es sich um einen Breaking Change ohne sinnvolle Problemumgehung. Aufgrund von Kundenfeedback wird die Änderung von Microsoft per Hotfix zurückgesetzt. Weitere Informationen zu verfügbaren Hotfixes für 1809 finden Sie im [Abschnitt mit den Schritten nach dem Updatevorgang](#post-update-steps). Wenden Sie den bereitgestellten Hotfix nach dem Update auf 1809 nur an, wenn das öffentliche VIP-Netzwerk aufgrund Ihrer Netzwerkrichtlinien nicht auf Infrastrukturressourcen zugreifen kann. In 1811 wird diese Änderung auf alle Systeme angewendet. Wenn Sie den Hotfix in 1809 angewendet haben, ist keine weitere Aktion erforderlich.  

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures (CVE, allgemeine Sicherheitslücken und Schwachstellen)

Dieses Update installiert die folgenden Sicherheitsupdates:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Weitere Informationen zu diesen Sicherheitslücken erhalten Sie durch Klicken auf die Links oben oder in den Microsoft Knowledge Base-Artikeln [4457131](https://support.microsoft.com/help/4457131) und [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie das neueste Azure Stack-Hotfix für 1808, bevor Sie 1809 anwenden. Weitere Informationen finden Sie hier: [KB 4481066 – Azure Stack-Hotfix: Azure Stack-Hotfix 1.1808.9.117](https://support.microsoft.com/help/4481066/). Zwar empfiehlt Microsoft den neuesten verfügbaren Hotfix, doch ist die mindestens erforderliche Version für die Installation von 1809 „1.1808.5.110“.

  > [!TIP]  
  > Abonnieren Sie die folgenden *RRS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom


- Bevor Sie mit der Installation dieses Updates beginnen, führen Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) mit den folgenden Parametern aus, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und lösen Sie solche auf, die eine Aktion erfordern.  

  ```powershell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- Wenn Azure Stack von System Center Operations Manager (SCOM) verwaltet wird, müssen Sie das Management Pack für Microsoft Azure Stack auf Version 1.0.3.11 aktualisieren, bevor Sie das Update 1809 anwenden.

### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Wenn Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) nach dem Update 1809 ausführen, wird eine Warnmeldung des Baseboard-Verwaltungscontrollers angezeigt. Sie können diese Warnung problemlos ignorieren.

- <!-- 2468613 - IS --> Während der Installation des Updates werden unter Umständen Warnungen mit folgender Meldung angezeigt: *Fehler: Die Vorlage für FaultType UserAccounts.New fehlt.*  Sie können diese Warnungen ignorieren. Diese Warnungen werden automatisch nach der Installation dieses Updates geschlossen.

- <!-- 2489559 - IS --> Versuchen Sie nicht, während der Installation dieses Updates virtuelle Computer zu erstellen. Weitere Informationen zum Verwalten von Updates finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS -->  Wenn Sie ein Update Ihres OEM auf Azure Stack angewendet haben, wird die Benachrichtigung **Update verfügbar** im Azure Stack-Verwaltungsportal möglicherweise nicht angezeigt. Um das Microsoft-Update zu installieren, befolgen Sie die Anweisungen unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md), um es manuell herunterzuladen und zu installieren.

### <a name="post-update-steps"></a>Schritte nach dem Update

> [!Important]  
> Bereiten Sie Ihre Azure Stack-Bereitstellung für den Erweiterungshost vor, der mit dem nächsten Updatepaket aktiviert wird. Bereiten Sie Ihr System mithilfe der folgenden Anleitung vor: [Vorbereiten auf den Erweiterungshost für Azure Stack](../azure-stack-extension-host-prepare.md).

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in den folgenden Knowledge Base-Artikeln sowie in unserer [Wartungsrichtlinie](../azure-stack-servicing-policy.md).  
- [KB 4481548 – Azure Stack-Hotfix: Azure Stack-Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

Im Folgenden werden bekannte Probleme nach der Installation zu dieser Buildversion vorgestellt.

### <a name="portal"></a>Portal

- Die technische Dokumentation für Azure Stack konzentriert sich auf das neueste Release. Aufgrund von Portaländerungen zwischen den Releases kann das, was bei der Verwendung der Azure Stack-Portale angezeigt wird, von dem abweichen, was in der Dokumentation angezeigt wird.

<!-- 2930718 - IS ASDK --> 
-  Im Administratorportal wird beim Zugriff auf die Details eines Benutzerabonnements nach dem Schließen des Blatts und dem Klicken auf **Aktuell** der Name des Benutzerabonnements nicht angezeigt.

<!-- 3060156 - IS ASDK --> 
-  Sowohl im Administrator- als auch im Benutzerportal funktioniert das Klicken auf die Portaleinstellungen und das Auswählen von **Alle Einstellungen und private Dashboards löschen** nicht wie erwartet. Es wird eine Fehlerbenachrichtigung angezeigt. 

<!-- 2930799 - IS ASDK --> 
-  In der Administrator- und Benutzerportalen wird unter **Alle Dienste** die Ressource **DDoS-Schutzpläne** nicht ordnungsgemäß aufgeführt. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, sie zu erstellen, wird ein Fehler angezeigt, der besagt, dass das Portal das Marketplace-Element nicht erstellen konnte. 

<!-- 2930820 - IS ASDK --> 
- Wenn Sie im Administrator- oder im Benutzerportal nach „Docker“ suchen, wird das Element nicht ordnungsgemäß zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, sie zu erstellen, wird ein Blatt mit einem Fehlerhinweis angezeigt. 

<!-- 2967387 - IS, ASDK --> 
-  Das Konto, mit dem Sie sich am Azure Stack-Administrator- oder -Benutzerportal anmelden, wird als **Unidentifizierter Benutzer** angezeigt. Diese Meldung wird angezeigt, wenn für das Konto kein *Vorname* oder *Nachname* angegeben wurde. Um dieses Problem zu umgehen, bearbeiten Sie das Benutzerkonto so, dass der Vor- oder Nachname angegeben wird. Sie müssen sich dann abmelden und erneut am Portal anmelden.  

<!--  2873083 - IS ASDK --> 
-  Wenn Sie das Portal verwenden, um eine VM-Skalierungsgruppe zu erstellen, wird das Dropdownmenü *Instanzgröße* bei Verwendung von Internet Explorer nicht richtig geladen. Um dieses Problem zu umgehen, nutzen Sie einen anderen Browser beim Verwenden des Portals zum Erstellen einer VMSS.  

<!-- 2931230 - IS  ASDK --> 
- Pläne, die einem Benutzerabonnement als Add-On-Plan hinzugefügt wurden, können nicht gelöscht werden, auch wenn Sie den Plan aus dem Benutzerabonnement entfernen. Der Plan ist so lange vorhanden, bis die Abonnements gelöscht werden, die auf den Add-On-Plan verweisen. 

<!--2760466 - IS  ASDK --> 
- Bei der Installation einer neuen Azure Stack-Umgebung, die diese Version ausführt, wird die Warnung, dass eine *Aktivierung erforderlich* ist, möglicherweise nicht angezeigt. Die [Aktivierung](../azure-stack-registration.md) ist erforderlich, damit Sie die Marketplace-Syndikation verwenden können.  

<!-- TBD - IS ASDK --> 
- Die zwei administrativen Abonnementtypen, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**. Diese Abonnementtypen sind in neuen Azure Stack-Umgebungen ab Version 1804 sichtbar, aber noch nicht zur Verwendung bereit. Sie sollten den Abonnementtyp **Standardanbieter** weiterhin verwenden.

<!-- TBD - IS ASDK --> 
- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

<!-- TBD - IS ASDK --> 
- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung PowerShell, um Berechtigungen zu überprüfen.


### <a name="health-and-monitoring"></a>Integrität und Überwachung

<!-- TBD - IS -->
- Die folgenden Warnungen werden in Ihrem Azure Stack-System möglicherweise wiederholt angezeigt und dann nicht mehr angezeigt:
   - *Infrastrukturrolleninstanz nicht verfügbar*
   - *Skalierungseinheitknoten ist offline.*
   
  Führen Sie das Cmdlet [Test-AzureStack](../azure-stack-diagnostic-test.md) aus, um die Integrität der Infrastrukturrolleninstanzen und Skalierungseinheitenknoten zu überprüfen. Wenn von [Test-AzureStack](../azure-stack-diagnostic-test.md) keine Probleme gefunden werden, können Sie diese Warnungen ignorieren. Wenn ein Problem erkannt wird, können Sie versuchen, die Infrastrukturrolleninstanz oder den Knoten über das Verwaltungsportal oder PowerShell zu starten.

  Dieses Problem wurde im aktuellen [Hotfixrelease 1809](https://support.microsoft.com/help/4481548/) behoben. Sie sollten diesen Hotfix daher unbedingt installieren, falls das Problem auftritt. 

<!-- 1264761 - IS ASDK -->  
-  Möglicherweise werden Warnungen für die **Health Controller**-Komponente mit folgenden Details angezeigt:  

   Warnung 1:
   - NAME: Infrastrukturrolle fehlerhaft
   - SCHWEREGRAD: Warnung
   - KOMPONENTE: Health Controller
   - BESCHREIBUNG: Heartbeat Scanner von Health Controller ist nicht verfügbar. Dies kann sich auf Integritätsberichte und Metriken auswirken.  

  Warnung 2:
   - NAME: Infrastrukturrolle fehlerhaft
   - SCHWEREGRAD: Warnung
   - KOMPONENTE: Health Controller
   - BESCHREIBUNG: Fault Scanner von Health Controller ist nicht verfügbar. Dies kann sich auf Integritätsberichte und Metriken auswirken.

  Beide Alarme können ignoriert werden und werden nach einer gewissen Zeit automatisch geschlossen.  


<!-- 2812138 | IS --> 
- Möglicherweise wird eine Warnung für die **Storage**-Komponente mit den folgenden Details angezeigt:

   - NAME: Interner Kommunikationsfehler des Storage-Diensts  
   - SCHWEREGRAD: Kritisch  
   - KOMPONENTE: Storage  
   - BESCHREIBUNG: Beim Senden von Anforderungen an die folgenden Knoten ist ein interner Kommunikationsfehler des Storage-Diensts aufgetreten.  

    Die Warnung kann ignoriert werden, Sie müssen sie jedoch manuell schließen.

<!-- 2368581 - IS, ASDK --> 
-  Wenn Sie als Azure Stack-Bediener eine Fehlermeldung über unzureichenden Speicher erhalten und virtuelle Computer der Mandanten nicht mit einem **Fabric-VM-Erstellungsfehler** bereitgestellt werden können, verfügt der Azure Stack-Stempel möglicherweise nicht über genügend Arbeitsspeicher. Mit dem [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) können Sie die verfügbare Kapazität für Ihre Workloads besser bestimmen.

### <a name="compute"></a>Compute

- Beim Erstellen eines [virtuellen Computers der Dv2-Serie](../../user/azure-stack-vm-considerations.md#vm-sizes) gestatten Ihnen D11-14v2 VMs das Erstellen von 4, 8, 16 bzw. 32 Datenträgern. Im Bereich „VM erstellen“ werden jedoch 8, 16, 32 und 64 Datenträger angezeigt.

<!-- 3235634 - IS, ASDK -->
- Um VMs mit Größen bereitzustellen, die das Suffix **v2** enthalten – z.B. **Standard_A2_v2** –, geben Sie das Suffix mit klein geschriebenem „v“ an: **Standard_A2_v2**. Verwenden Sie nicht **Standard_A2_V2** mit groß geschriebenem „V“. Dies funktioniert in der globalen Azure-Umgebung und ist eine Inkonsistenz in Azure Stack.

<!-- 3099544 - IS, ASDK --> 
-  Wenn Sie im Azure Stack-Portal eine neue VM erstellen und die VM-Größe auswählen, wird die Spalte „USD/Monat“ mit der Meldung **Nicht verfügbar** angezeigt. Diese Spalte sollte nicht angezeigt werden. Die Anzeige der VM-Preisspalte wird in Azure Stack nicht unterstützt.

<!-- 2869209 - IS, ASDK --> 
-  Bei Verwendung des [**Add-AzsPlatformImage**-Cmdlets](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true) müssen Sie den **-OsUri**-Parameter als Speicherkonto-URI beim Hochladen des Datenträgers verwenden. Wenn Sie den lokalen Pfad des Datenträgers verwenden, schlägt das Cmdlet mit der folgenden Fehlermeldung fehl: *Fehler beim Vorgang mit langer Ausführungszeit mit dem Status „Fehler“* . 

<!--  2795678 - IS, ASDK --> 
- Wenn Sie das Portal zum Erstellen virtueller Computer in einer Premium-VM-Größe (DS, Ds_v2, FS, FSv2) verwenden, wird der virtuelle Computer in einem Standardspeicherkonto erstellt. Die Erstellung in einem Standardspeicherkonto wirkt sich nicht auf die Funktionalität, IOPs oder die Abrechnung aus. 

   Sie können die folgende Warnung problemlos ignorieren: *Sie haben sich dafür entschieden, einen Standarddatenträger mit einer Größe zu verwenden, die Premium-Datenträger unterstützt. Dies kann sich auf die Leistung des Betriebssystems auswirken und wird nicht empfohlen. Erwägen Sie stattdessen die Verwendung von Storage Premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Die Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen bietet „CentOS 7.2-basiert“ als Option für die Bereitstellung an. Da dieses Image in Azure Stack nicht verfügbar ist, wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Operator aus dem Marketplace heruntergeladen wurde.  

<!-- 2724873 - IS --> 
-  Wenn Sie die PowerShell-Cmdlets **Start-AzsScaleUnitNode** oder **Stop-AzsScaleunitNode** verwenden, um Skalierungseinheiten zu verwalten, tritt beim ersten Versuch, die Skalierungseinheit zu starten oder zu stoppen, möglicherweise ein Fehler auf. Wenn beim ersten Ausführen des Cmdlets ein Fehler auftritt, führen Sie das Cmdlet ein zweites Mal aus. Bei der zweiten Ausführung sollte der Vorgang erfolgreich sein. 

<!-- TBD - IS ASDK --> 
- Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Benutzer eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.  

<!-- 1662991 IS ASDK --> 
- Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren.  

<!-- 2724961- IS ASDK --> 
- Wenn Sie den **Microsoft.Insight**-Ressourcenanbieter in den Abonnementeinstellungen registrieren und einen virtuellen Windows-Computer mit aktivierter Gastbetriebssystemdiagnose erstellen, werden im Diagramm mit der CPU-Nutzung in Prozent auf der Übersichtsseite des virtuellen Computers keine Metrikdaten angezeigt.

   Navigieren Sie zum Anzeigen von Metrikdaten (etwa das Diagramm „CPU-Prozentsatz“ für den virtuellen Computer) zum Fenster „Metriken“, und zeigen Sie alle unterstützten Metriken für Windows-VM-Gäste an.

<!-- 3507629 - IS, ASDK --> 
- Managed Disks erstellt zwei neue [Computekontingenttypen](../azure-stack-quota-types.md#compute-quota-types) zur Begrenzung der maximalen Kapazität von verwalteten Datenträgern, die bereitgestellt werden können. Standardmäßig werden für jeden Kontingenttyp von verwalteten Datenträgern 2.048 GiB zugeordnet. Allerdings können die folgenden Probleme auftreten:

   - Bei Kontingenten, die vor dem Update 1808 erstellt wurden, werden im Administratorportal für das „Managed Disks“-Kontingent 0 Werte angezeigt, obwohl 2.048 GiB zugeordnet wurden. Sie können den Wert auf der Grundlage Ihrer tatsächlichen Anforderungen erhöhen oder verringern. Dann setzt der neu festgelegte Kontingentwert den Standardwert 2.048 GiB außer Kraft.
   - Wenn Sie den Kontingentwert auf „0“ aktualisieren, entspricht dies dem Standardwert 2.048 GiB. Als Problemumgehung können Sie den Kontingentwert auf „1“ festlegen.

<!-- TBD - IS ASDK --> 
- Nach dem Anwenden des Updates 1809 können beim Bereitstellen von virtuellen Computern mit Managed Disks die folgenden Probleme auftreten:

  - Wenn das Abonnement vor dem Update 1808 erstellt wurde, schlägt die Bereitstellung eines virtuellen Computers mit Managed Disks möglicherweise mit einer internen Fehlermeldung fehl. Um den Fehler zu beheben, führen Sie die folgenden Schritte für jedes Abonnement aus:
     1. Navigieren Sie im Mandantenportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Klicken Sie auf **Ressourcenanbieter**, klicken Sie dann auf **Microsoft.Compute**, und klicken Sie anschließend auf **Erneut registrieren**.
     2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)**, und überprüfen Sie, ob die Rolle **AzureStack-DiskRP-Client** aufgeführt wird.
  - Wenn Sie eine Umgebung mit mehreren Mandanten konfiguriert haben, schlägt die Bereitstellung von virtuellen Computern in einem Abonnement, dem ein Gastverzeichnis zugeordnet ist, möglicherweise mit einer internen Fehlermeldung fehl. Zum Beheben des Fehlers führen Sie die in [diesem Artikel](../azure-stack-enable-multitenancy.md#register-a-guest-directory) beschriebenen Schritte aus, um alle Gastverzeichnisse neu zu konfigurieren.

- Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden. Um dieses Problem zu umgehen, verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie kennwortbasierte Authentifizierung.

### <a name="networking"></a>Netzwerk  

<!-- 1766332 - IS ASDK --> 
-  Wenn Sie unter **Netzwerk** auf **VPN-Gateway erstellen** klicken, um eine VPN-Verbindung einzurichten, wird als VPN-Typ **Richtlinienbasiert** aufgeführt. Wählen Sie diese Option nicht aus. Nur die Option **Routenbasiert** wird in Azure Stack unterstützt.

<!-- 1902460 - IS ASDK --> 
- Azure Stack unterstützt ein einzelnes *Gateway eines lokalen Netzwerks* pro IP-Adresse. Dies gilt für alle Mandantenabonnements. Nach der Herstellung der Verbindung mit dem ersten Gateway eines lokalen Netzwerks werden nachfolgende Versuche zum Erstellen einer Ressource für Gateways lokaler Netzwerke mit der gleichen IP-Adresse blockiert.

<!-- 16309153 - IS ASDK --> 
- In einem virtuellen Netzwerk, das mit der DNS-Servereinstellung *Automatisch* erstellt wurde, tritt bei der Änderung eines benutzerdefinierten DNS-Servers ein Fehler auf. Die aktualisierten Einstellungen werden nicht per Pushvorgang auf VMs in diesem VNET übertragen.

<!-- 2529607 - IS ASDK --> 
-  Während der *Geheimnisrotation* von Azure Stack sind öffentliche IP-Adressen für einen Zeitraum von zwei bis fünf Minuten nicht erreichbar.

<!-- 2664148 - IS ASDK --> 
-   In Szenarien, in denen der Mandant über einen S2S-VPN-Tunnel auf seine VMs zugreift, kann es passieren, dass bei Verbindungsversuchen Fehler auftreten, wenn das lokale Subnetz dem lokalen Netzwerkgateway erst nach der Erstellung des Gateways hinzugefügt wurde. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Benutzer müssen den Speicherressourcenanbieter vor dem Erstellen seiner ersten Azure-Funktion im Abonnement registrieren.


### <a name="usage"></a>Verwendung  

<!-- TBD - IS ASDK --> 
- Die Ansicht für die Nutzungsdaten öffentlicher IP-Adressen zeigt den gleichen *EventDateTime*-Wert für jeden Datensatz an statt des *TimeDate*-Stempels, der anzeigt, wann der Datensatz jeweils erstellt wurde. Derzeit können Sie diese Daten nicht nutzen, um die Nutzung öffentlicher IP-Adressen exakt nachzuhalten.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket für das Azure Stack-Update 1809 [hier](https://aka.ms/azurestackupdatedownload) herunterladen.
  

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](../azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](../azure-stack-monitor-update.md).  
- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).  