---
title: Azure Stack-Update 1902 | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1902 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.reviewer: adepue
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 53b33c7eb0dc71a65113ef8807de1c5b3c3c4c80
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691388"
---
# <a name="azure-stack-1902-update"></a>Azure Stack-Update 1902

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt den Inhalt des Updatepakets 1902. Das Update enthält Verbesserungen, Fehlerbehebungen und neue Funktionen für diese Version von Azure Stack. In diesem Artikel werden auch bekannte Probleme in diesem Release beschrieben, und er enthält einen Link zum Herunterladen des Updates. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="archived-release-notes"></a>Archivierte Versionshinweise

Sie können [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](http://aka.ms/azsarchivedrelnotes) anzeigen. Diese archivierten Versionshinweise werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1902 ist **1.1902.0.69**.

### <a name="update-type"></a>Updatetyp

Der Azure Stack 1902-Update-Buildtyp lautet **Vollständig**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie das [aktuelle Azure Stack-Hotfix](#azure-stack-hotfixes) für 1901 installieren, bevor Sie Azure Stack auf 1902 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack-Hotfixes

- **1901**: [KB 4500636 – Azure Stack-Hotfix 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902**: [KB 4500637 – Azure Stack-Hotfix 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Sie können 1902 direkt auf der Grundlage des Release **1.1901.0.95** oder **1.1901.0.99** installieren, ohne zuvor einen 1901-Hotfix zu installieren. Wenn Sie jedoch den älteren Hotfix **1901.2.103** installiert haben, müssen Sie den neueren [Hotfix 1901.3.105](https://support.microsoft.com/help/4495662) installieren, bevor Sie mit 1902 fortfahren.

- Bevor Sie mit der Installation dieses Updates beginnen, führen Sie [Test-AzureStack](azure-stack-diagnostic-test.md) mit den folgenden Parametern aus, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und führen Sie die Behebung für Einträge durch, für die eine Aktion erforderlich ist:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Wenn der Parameter `AzsControlPlane` bei der Ausführung von **Test-AzureStack** enthalten ist, wird in der Ausgabe von **Test-AzureStack** der folgende Fehler angezeigt: **FAIL Azure Stack Control Plane Websites Summary** (FEHLER: Websitezusammenfassung der Azure Stack-Steuerungsebene). Sie können diesen spezifischen Fehler ignorieren.

- Wenn Azure Stack von System Center Operations Manager (SCOM) verwaltet wird, müssen Sie das [Management Pack für Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) auf Version 1.0.3.11 aktualisieren, bevor Sie das Update 1902 anwenden.

- Das Paketformat für das Azure Stack-Update wurde ab Release 1902 von **.bin/.exe/.xml** in **.zip/.xml** geändert. Kunden mit verbundenen Azure Stack-Skalierungseinheiten sehen im Portal die Meldung **Update verfügbar**. Kunden ohne Verbindung können die ZIP-Datei mit der entsprechenden XML-Datei jetzt einfach herunterladen und importieren.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Verbesserungen

- In Build 1902 wird eine neue Benutzeroberfläche im Azure Stack-Administratorportal zum Erstellen von Plänen, Angeboten, Kontingenten und Add-On-Plänen eingeführt. Weitere Informationen (einschließlich Screenshots) finden Sie unter [Erstellen von Plänen, Angeboten und Kontingenten](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Verbesserungen der Zuverlässigkeit der Kapazitätserweiterung beim Hinzufügen eines Knotens, wenn die Skalierungseinheit vom Zustand der Speichererweiterung in den Ausführungszustand umgeschaltet wird.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Zum Verbessern der Paketintegrität und -sicherheit sowie zum Vereinfachen der Verwaltung für die Offlineerfassung hat Microsoft das Format des Updatepakets von EXE- und BIN-Dateien in eine ZIP-Datei geändert. Das neue Format verbessert die Zuverlässigkeit des Entpackungsprozesses, der mitunter Verzögerungen bei der Vorbereitung des Updates verursachen kann. Das gleiche Paketformat gilt auch für Updatepakete von Ihrem OEM.
- Um die Benutzerfreundlichkeit von Azure Stack bei der Ausführung von Test-AzureStack zu verbessern, können Benutzer nun einfach „Test-AzureStack -Group UpdateReadiness“ verwenden, anstatt zehn zusätzliche Parameter nach einer Include-Anweisung zu übergeben.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Zum Verbessern der allgemeinen Zuverlässigkeit und Verfügbarkeit von zentralen Infrastrukturdiensten während des Updateprozesses werden im Rahmen des Plans mit den Updateaktionen automatische globale Wartungen vom nativen Ressourcenanbieter erkannt und bei Bedarf aufgerufen. Workflows zur globalen Wartung vom Typ „Reparieren“ umfassen folgende Schritte:

  - Suchen nach VMs in der Infrastruktur, die sich in einem nicht optimalen Zustand befinden, und diese bei Bedarf reparieren
  - Suchen nach Problemen im Rahmen des Steuerungsplans mit dem SQL-Dienst und diese bei Bedarf reparieren
  - Überprüfen des Status des Software Load Balancer-Diensts (SLB) als Teil des Netzwerkcontrollers (NC) und diesen bei Bedarf reparieren
  - Überprüfen des Status des Netzwerkcontrollerdiensts (NC) und diesen bei Bedarf reparieren
  - Status der ERCS-Service Fabric-Knoten (Emergency Recovery Console Service) überprüfen und diese bei Bedarf reparieren
  - Überprüfen des Status der Infrastrukturrolle und diese bei Bedarf reparieren
  - Status der ACS-Service Fabric-Knoten (Azure Consistent Storage) überprüfen und diese bei Bedarf reparieren

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Verbesserungen der Azure Stack-Diagnosetools, um die Zuverlässigkeit und Leistung der Protokollsammlung zu verbessern. Zusätzliche Protokollierung für Netzwerk- und Identitätsdienste. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Verbesserungen der Zuverlässigkeit von Test-AzureStack für den Bereitschaftstest der Geheimnisrotation.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Verbesserungen zum Steigern der Zuverlässigkeit von AD Graph bei der Kommunikation mit der Active Directory-Umgebung eines Kunden

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Verbesserungen der Hardwarebestandssammlung in Get-AzureStackStampInformation.

- Zum Verbessern der Zuverlässigkeit von Vorgängen, die auf der ERCS-Infrastruktur ausgeführt werden, wird der Arbeitsspeicher für jede ERCS-Instanz von 8 GB auf 12 GB erhöht. Bei einer Installation integrierter Azure Stack-Systeme führt dies zu einer Erhöhung von insgesamt 12 GB.

<!-- 110303935 IcM Reported by HKEX -->
- Mit 1902 wird ein Problem im Netzwerkcontroller-VSwitch-Dienst behoben, bei dem alle VMs eines bestimmten Knotens in den Offlinezustand versetzt wurden.  Bei diesem Problem kam es zu einem Hängen in einem Verlustzustand der primären Instanz, bei dem die primäre Instanz nicht erreichbar ist und für die Rolle kein Failover auf eine andere fehlerfreie Instanz ausgeführt wird. Dieses Problem konnte nur behoben werden, indem der Microsoft-Support zurate gezogen wurde.

> [!IMPORTANT]
> Vergewissern Sie sich auf dem Blatt **Kapazität**, dass Ihr Azure Stack-Stempel mehr als 12 GB verfügbaren Speicherplatz aufweist, damit der Patch- und Updateprozess die geringstmögliche Downtime des Mandanten verursacht. Nach der erfolgreichen Installation des Updates wird die Erhöhung des Arbeitsspeichers auf dem Blatt **Kapazität** angezeigt.

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures (CVE, allgemeine Sicherheitslücken und Schwachstellen)

Dieses Update installiert die folgenden Sicherheitsupdates:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Weitere Informationen zu diesen Sicherheitsrisiken finden Sie unter den obigen Links oder im Microsoft Knowledge Base-Artikel [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Wenn Sie versuchen, ein Azure Stack-Update zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download erneut.

- Wenn Sie [Test-AzureStack](azure-stack-diagnostic-test.md) ausführen, wird eine Warnmeldung des Baseboard-Verwaltungscontrollers (BMC) angezeigt. Sie können diese Warnung problemlos ignorieren.

- <!-- 2468613 - IS --> Während der Installation dieses Updates werden möglicherweise Warnungen mit dem Titel `Error - Template for FaultType UserAccounts.New is missing.` angezeigt. Diese Warnungen können ignoriert werden. Die Warnungen werden automatisch geschlossen, nachdem die Installation dieses Updates abgeschlossen wurde.

## <a name="post-update-steps"></a>Schritte nach dem Update

- Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie sowohl unter [Hotfixes](#hotfixes) als auch [Wartungsrichtlinie](azure-stack-servicing-policy.md).  

- Rufen Sie die Verschlüsselungsschlüssel für ruhende Daten ab, und speichern Sie diese sicher außerhalb Ihrer Azure Stack-Bereitstellung. Befolgen Sie die [Anleitungen zum Abrufen der Schlüssel](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

Im Folgenden werden bekannte Probleme nach der Installation zu dieser Buildversion vorgestellt.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- Wenn Sie im Administrator- oder im Benutzerportal nach „Docker“ suchen, wird das Element nicht ordnungsgemäß zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, sie zu erstellen, wird ein Blatt mit einem Fehlerhinweis angezeigt. 

<!-- 2931230 - IS  ASDK --> 
- Pläne, die einem Benutzerabonnement als Add-On-Plan hinzugefügt wurden, können nicht gelöscht werden, auch wenn Sie den Plan aus dem Benutzerabonnement entfernen. Der Plan ist so lange vorhanden, bis die Abonnements gelöscht werden, die auf den Add-On-Plan verweisen. 

<!-- TBD - IS ASDK --> 
- Die zwei administrativen Abonnementtypen, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**. Diese Abonnementtypen sind in neuen Azure Stack-Umgebungen ab Version 1804 sichtbar, aber noch nicht zur Verwendung bereit. Sie sollten den Abonnementtyp **Standardanbieter** weiterhin verwenden.

<!-- 3557860 - IS ASDK --> 
- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

<!-- 1663805 - IS ASDK --> 
- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung [PowerShell, um Berechtigungen zu überprüfen](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- Daniel 3/28 -->
- Wenn Sie im Benutzerportal in einem Speicherkonto zu einem Blob navigieren und versuchen, in der Navigationsstruktur die Option **Zugriffsrichtlinie** zu öffnen, wird das nachfolgende Fenster nicht geladen. Zum Umgehen dieses Problems können mit den folgenden PowerShell-Cmdlets Zugriffsrichtlinien jeweils erstellt, abgerufen, festgelegt und gelöscht werden:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen. Um dieses Problem zu umgehen, erstellen Sie das Speicherkonto erneut mit demselben Namen wie zuvor.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Auf der Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen wird „CentOS 7.2-basiert“ als Option für die Bereitstellung angeboten. Da dieses Image in Azure Stack nicht verfügbar ist, wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Operator aus dem Marketplace heruntergeladen wurde.  

<!-- TBD - IS ASDK --> 
- Nach dem Anwenden des Updates 1902 können beim Bereitstellen von VMs mit Managed Disks die folgenden Probleme auftreten:

   - Wenn das Abonnement vor dem Update 1808 erstellt wurde, schlägt die Bereitstellung eines virtuellen Computers mit Managed Disks möglicherweise mit einer internen Fehlermeldung fehl. Um den Fehler zu beheben, führen Sie die folgenden Schritte für jedes Abonnement aus:
      1. Navigieren Sie im Mandantenportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Wählen Sie **Ressourcenanbieter** und dann **Microsoft.Compute** aus, und klicken Sie dann auf **Erneut registrieren**.
      2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)** , und überprüfen Sie, ob **Azure Stack – Verwalteter Datenträger** aufgeführt wird.
   - Wenn Sie eine Umgebung mit mehreren Mandanten konfiguriert haben, schlägt die Bereitstellung von virtuellen Computern in einem Abonnement, dem ein Gastverzeichnis zugeordnet ist, möglicherweise mit einer internen Fehlermeldung fehl. Zum Beheben des Fehlers führen Sie die in [diesem Artikel](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) beschriebenen Schritte aus, um alle Gastverzeichnisse neu zu konfigurieren.

- Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden. Um dieses Problem zu umgehen, verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie kennwortbasierte Authentifizierung.

- Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen. Wählen Sie zur Problemumgehung die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.

- Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf. Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="networking"></a>Netzwerk  

<!-- 3239127 - IS, ASDK -->
- Im Azure Stack-Portal wird, wenn Sie eine statische IP-Adresse für eine IP-Konfiguration ändern, die an einen Netzwerkadapter gebunden ist, die an eine VM-Instanz angefügt ist, eine Warnmeldung angezeigt, die besagt: 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Sie können diese Meldung gefahrlos ignorieren. Die IP-Adresse wird auch dann geändert, wenn die VM-Instanz nicht neu gestartet wird.

<!-- 3632798 - IS, ASDK -->
- Wenn Sie im Portal eine eingehende Sicherheitsregel hinzufügen und als Quelle **Diensttag** auswählen, werden mehrere Optionen in der Liste **Quelltag** angezeigt, die für Azure Stack nicht verfügbar sind. Die einzigen Optionen, die in Azure Stack gültig sind, lauten folgendermaßen:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Die anderen Optionen werden nicht als Quelltags in Azure Stack unterstützt. Auf ähnliche Weise wird, wenn Sie eine ausgehende Sicherheitsregel hinzufügen und als Ziel **Diensttag** auswählen, dieselbe Liste von Optionen für **Quelltag** angezeigt. Die einzigen gültigen Optionen sind dieselben wie für **Quelltag**, wie in der vorherigen Liste beschrieben.

- Netzwerksicherheitsgruppen (NSGs) funktionieren in Azure Stack nicht auf die gleiche Weise wie in Azure weltweit. In Azure können Sie mehrere Ports für eine NSG-Regel festlegen (mit dem Portal, PowerShell und Resource Manager). In Azure Stack können Sie jedoch nicht mehrere Ports für eine NSG-Regel über das Portal festlegen. Um dieses Problem zu umgehen, verwenden Sie eine Resource Manager-Vorlage oder PowerShell, um diese zusätzlichen Regeln festzulegen.

<!-- 3203799 - IS, ASDK -->
- Azure Stack unterstützt derzeit unabhängig von der Instanzgröße das Anfügen von höchstens vier Netzwerkschnittstellen (NICs) an eine VM-Instanz.

- Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.  Verwenden Sie für die Problemumgehung PowerShell, die Befehlszeilenschnittstelle (Command Line Interface, CLI) oder eine Azure Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.

- Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.  Verwenden Sie für die Problemumgehung PowerShell, die Befehlszeilenschnittstelle (Command Line Interface, CLI) oder eine Azure Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.

- Im Benutzerportal wird im Fenster **Lastenausgleich erstellen** eine Option zum Erstellen einer Lastenausgleichs-SKU vom Typ **Standard** angezeigt. Diese Option wird in Azure Stack nicht unterstützt.

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

Sie können das Paket für das Azure Stack-Update 1902 [hier](https://aka.ms/azurestackupdatedownload) herunterladen. 

Nur in verbundenen Szenarios überprüfen Azure Stack-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Weitere Informationen finden Sie unter [Verwalten von Updates für Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).  
