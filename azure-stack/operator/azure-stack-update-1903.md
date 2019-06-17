---
title: Azure Stack-Update 1903 | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1903 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 972b47e5c8787d41d918544f220e082b9ac2d69d
ms.sourcegitcommit: a427e72e4f3b6cd6000b1459af9bbf221e049e08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66506288"
---
# <a name="azure-stack-1903-update"></a>Azure Stack-Update 1903

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt den Inhalt des Updatepakets 1903. Das Update enthält Verbesserungen, Fehlerbehebungen und neue Funktionen für diese Version von Azure Stack. In diesem Artikel werden auch bekannte Probleme in diesem Release beschrieben, und er enthält einen Link zum Herunterladen des Updates. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="archived-release-notes"></a>Archivierte Versionshinweise

Sie können [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](http://aka.ms/azsarchivedrelnotes) anzeigen. Diese archivierten Versionshinweise werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1903 ist **1.1903.0.35**.

### <a name="update-type"></a>Updatetyp

Der Azure Stack 1903-Update-Buildtyp lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Die zu erwartende Dauer bis zum Abschluss von Update 1903 beträgt ca. 16 Stunden, aber die genauen Zeiten können variieren. Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1903 und sollte nicht auf andere Azure Stack-Updates übertragen werden.

> [!IMPORTANT]
> Die Nutzlast 1903 umfasst kein ASDK-Release.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den [aktuellen Azure Stack-Hotfix](#azure-stack-hotfixes) für 1902 installieren, bevor Sie Azure Stack auf 1903 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

> [!TIP]
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack-Hotfixes

- **1902**: [KB 4500637 – Azure Stack-Hotfix 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**: [KB 4500638 – Azure Stack-Hotfix 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>Verbesserungen

- Es wurde ein Netzwerkfehler behoben, durch den verhindert wurde, dass Änderungen des Werts **Leerlauftimeout (Minuten)** für eine **Öffentliche IP-Adresse** wirksam wurden. Bisher wurden Änderungen dieses Werts ignoriert, damit unabhängig von allen Änderungen, die Sie durchgeführt haben, immer der Standardwert „4 Minuten“ verwendet wurde. Mit dieser Einstellung wird gesteuert, wie viele Minuten eine TCP-Verbindung geöffnet bleiben soll, ohne dass Clients Keep-Alive-Meldungen senden müssen. Beachten Sie hierbei Folgendes: Dieser Fehler hat sich nur auf öffentliche IP-Adressen der Instanzebene und nicht auf öffentliche IP-Adressen ausgewirkt, die einem Lastenausgleich zugewiesen waren.

- Es wurden Verbesserungen an der Zuverlässigkeit des Updatemoduls vorgenommen, z. B. automatische Behebung von häufigen Problemen, damit Updates ohne Unterbrechung angewendet werden.

- Es wurden Verbesserungen an der Erkennung und Behebung von Zuständen mit wenig freiem Speicherplatz vorgenommen.

- Azure Stack unterstützt jetzt Microsoft Azure-Linux-Agents mit einer höheren Version als 2.2.35. Aufgrund dieser Unterstützung können Kunden einheitliche Linux-Images zwischen Azure und Azure Stack nutzen. Dies wurde im Rahmen der Hotfixes 1901 und 1902 hinzugefügt.

### <a name="secret-management"></a>Verwaltung von Geheimnissen

- Azure Stack unterstützt jetzt die Rotation des Stammzertifikats, das von Zertifikaten für die Rotation externer Geheimnisse verwendet wird. Weitere Informationen finden Sie in [diesem Artikel](azure-stack-rotate-secrets.md).

- 1903 enthält Leistungsverbesserungen für die Geheimnisrotation, mit der die Zeit zum Ausführen der Rotation interner Geheimnisse reduziert wird.

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Installieren Sie den [aktuellen Azure Stack-Hotfix](#azure-stack-hotfixes) für 1902 (falls vorhanden), bevor Sie auf 1903 aktualisieren.

- Achten Sie darauf, dass Sie für die Planung und Größenanpassung Ihrer Workloads die neueste Version von [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) verwenden. Die neueste Version umfasst Fehlerbehebungen und neue Features, die mit jedem Azure Stack-Update veröffentlicht werden.

- Bevor Sie mit der Installation dieses Updates beginnen, sollten Sie [Test-AzureStack](azure-stack-diagnostic-test.md) mit dem folgenden Parameter ausführen, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und führen Sie die Behebung für Einträge durch, für die eine Aktion erforderlich ist:

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Wenn Azure Stack von System Center Operations Manager verwaltet wird, müssen Sie das [Management Pack für Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) auf Version 1.0.3.11 aktualisieren, bevor Sie das Update 1903 anwenden.

- Das Paketformat für das Azure Stack-Update wurde ab Release 1902 von **.bin/.exe/.xml** in **.zip/.xml** geändert. Kunden mit verbundenen Azure Stack-Skalierungseinheiten sehen im Portal die Meldung **Update verfügbar**. Kunden ohne Verbindung können die ZIP-Datei mit der entsprechenden XML-Datei jetzt einfach herunterladen und importieren.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Wenn Sie versuchen, ein Azure Stack-Update zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download erneut.

- Wenn Sie [Test-AzureStack](azure-stack-diagnostic-test.md) ausführen, wird eine Warnmeldung des Baseboard-Verwaltungscontrollers (BMC) angezeigt. Sie können diese Warnung problemlos ignorieren.

<!-- 2468613 - IS -->
- Während der Installation des Updates werden unter Umständen Warnungen mit folgender Meldung angezeigt: **Fehler: Die Vorlage für FaultType UserAccounts. New fehlt**. Sie können diese Warnungen ignorieren. Die Warnungen werden automatisch geschlossen, nachdem die Installation dieses Updates abgeschlossen wurde.

## <a name="post-update-steps"></a>Schritte nach dem Update

- Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie sowohl unter [Hotfixes](#hotfixes) als auch [Wartungsrichtlinie](azure-stack-servicing-policy.md).

- Rufen Sie die Verschlüsselungsschlüssel für ruhende Daten ab, und speichern Sie diese sicher außerhalb Ihrer Azure Stack-Bereitstellung. Befolgen Sie die [Anleitungen zum Abrufen der Schlüssel](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

Im Folgenden werden bekannte Probleme nach der Installation zu dieser Buildversion vorgestellt.

### <a name="portal"></a>Portal

- Wenn Sie im Benutzerportal im Dashboard versuchen, auf die Kachel **Feedback** zu klicken, wird eine leere Browserregisterkarte geöffnet. Zur Problemumgehung können Sie über [Azure Stack UserVoice](https://aka.ms/azurestackuservoice) eine UserVoice-Anfrage erstellen.

<!-- 2930820 - IS ASDK -->
- Wenn Sie im Administrator- oder im Benutzerportal nach „Docker“ suchen, wird das Element nicht ordnungsgemäß zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, sie zu erstellen, wird ein Blatt mit einem Fehlerhinweis angezeigt.

<!-- 2931230 - IS  ASDK -->
- Pläne, die einem Benutzerabonnement als Add-On-Plan hinzugefügt wurden, können nicht gelöscht werden, auch wenn Sie den Plan aus dem Benutzerabonnement entfernen. Der Plan ist so lange vorhanden, bis die Abonnements gelöscht werden, die auf den Add-On-Plan verweisen.

<!-- TBD - IS ASDK -->
- Die zwei administrativen Abonnementtypen, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**. Diese Abonnementtypen sind in neuen Azure Stack-Umgebungen ab Version 1804 sichtbar, aber noch nicht zur Verwendung bereit. Sie sollten den Abonnementtyp **Standardanbieter** weiterhin verwenden.

<!-- 3557860 - IS ASDK -->
- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

<!-- 1663805 - IS ASDK --> 
- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung [PowerShell, um Berechtigungen zu überprüfen](/powershell/module/azurerm.resources/get-azurermroleassignment).

<!-- Daniel 3/28 -->
- Wenn Sie im Benutzerportal in einem Speicherkonto zu einem Blob navigieren und versuchen, in der Navigationsstruktur die Option **Zugriffsrichtlinie** zu öffnen, wird das nachfolgende Fenster nicht geladen. Zum Umgehen dieses Problems können mit den folgenden PowerShell-Cmdlets Zugriffsrichtlinien jeweils erstellt, abgerufen, festgelegt und gelöscht werden:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt. Laden Sie das Blob mit der Option **SAS** hoch, um dieses Problem zu umgehen.

- Wenn Sie an den Azure Stack-Portalen angemeldet sind, werden ggf. Benachrichtigungen zum öffentlichen Azure-Portal angezeigt. Sie können diese Benachrichtigungen ignorieren, da sie für Azure Stack derzeit nicht gelten (z. B. „1 neues Update – Folgende Updates stehen jetzt zur Verfügung: Update des Azure-Portals von April 2019“).

- Wenn Sie im Benutzerportal im Dashboard versuchen, die Kachel **Feedback** auszuwählen, wird eine leere Browserregisterkarte geöffnet. Zur Problemumgehung können Sie über [Azure Stack UserVoice](https://aka.ms/azurestackuservoice) eine UserVoice-Anfrage erstellen.

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen. Um dieses Problem zu umgehen, erstellen Sie das Speicherkonto erneut mit demselben Namen wie zuvor.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Die Benutzeroberfläche zum Erstellen von Virtual Machine Scale Sets bietet „CentOS 7.2-basiert“ als Option für die Bereitstellung an. Da dieses Image in Azure Stack nicht verfügbar ist, wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Operator aus dem Marketplace heruntergeladen wurde.

<!-- TBD - IS ASDK -->
- Nach dem Anwenden des Updates 1903 können beim Bereitstellen von VMs mit Managed Disks die folgenden Probleme auftreten:

   - Wenn das Abonnement vor dem Update 1808 erstellt wurde, schlägt die Bereitstellung eines virtuellen Computers mit Managed Disks möglicherweise mit einer internen Fehlermeldung fehl. Um den Fehler zu beheben, führen Sie die folgenden Schritte für jedes Abonnement aus:
      1. Navigieren Sie im Mandantenportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Wählen Sie **Ressourcenanbieter** und dann **Microsoft.Compute** aus, und klicken Sie dann auf **Erneut registrieren**.
      2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)** , und überprüfen Sie, ob **Azure Stack – Verwalteter Datenträger** aufgeführt wird.
   - Wenn Sie eine Umgebung mit mehreren Mandanten konfiguriert haben, schlägt die Bereitstellung von virtuellen Computern in einem Abonnement, dem ein Gastverzeichnis zugeordnet ist, möglicherweise mit einer internen Fehlermeldung fehl. Zum Beheben des Fehlers führen Sie die in [diesem Artikel](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) beschriebenen Schritte aus, um alle Gastverzeichnisse neu zu konfigurieren.

- Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden. Um dieses Problem zu umgehen, verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie kennwortbasierte Authentifizierung.

- Wenn Sie nicht über einen Hardwarelebenszyklus-Host (Hardware Lifecycle Host, HLH) verfügen: Vor Build 1902 mussten Sie die Gruppenrichtlinie **Computerkonfiguration\Windows-Einstellungen\Sicherheitseinstellungen\Lokale Richtlinien\Sicherheitsoptionen** auf **LM- und NTLM-Antworten senden (NTLMv2-Sitzungssicherheit verwenden, wenn ausgehandelt)** festlegen. Seit Build 1902 müssen Sie den Wert **Nicht definiert** beibehalten oder die Richtlinie auf **Nur NTLMv2-Antworten senden** (Standardwert) festlegen. Andernfalls können Sie keine PowerShell-Remotesitzung einrichten, und der Fehler **Zugriff verweigert** wird angezeigt:

   ```powershell
   $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint -Credential $Cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

- Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen. Wählen Sie zur Problemumgehung die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.

- Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf. Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="networking"></a>Netzwerk

<!-- 3239127 - IS, ASDK -->
- Im Azure Stack-Portal wird, wenn Sie eine statische IP-Adresse für eine IP-Konfiguration ändern, die an einen Netzwerkadapter gebunden ist, die an eine VM-Instanz angefügt ist, eine Warnmeldung angezeigt, die besagt:

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK -->
- Mandanten müssen den Speicherressourcenanbieter vor dem Erstellen ihrer ersten Azure-Funktion im Abonnement registrieren.
- Einige Benutzeroberflächen im Mandantenportal sind aufgrund einer Inkompatibilität mit dem Portalframework in 1903 fehlerhaft, z. B. die Benutzeroberfläche für Bereitstellungsslots, Tests in der Produktion und Websiteerweiterungen. Verwenden Sie das [Azure App Service PowerShell-Modul](/azure/app-service/deploy-staging-slots#automate-with-powershell) oder die [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest), um dieses Problem zu umgehen. Die Portalumgebung wird im bevorstehenden Release von Azure App Service unter Azure Stack 1.6 (Update 6) wiederhergestellt.

<!-- ### Usage -->


<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog

- Die syslog-Konfiguration wird bei einem Updatezyklus nicht beibehalten, sodass der syslog-Client seine Konfiguration verliert und die syslog-Nachrichten nicht mehr weitergeleitet werden. Dieses Problem betrifft alle Versionen von Azure Stack ab der allgemeinen Verfügbarkeit des syslog-Clients (1809). Um dieses Problem zu umgehen, konfigurieren Sie den syslog-Client nach Anwenden eines Azure Stack-Updates neu.

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket für das Azure Stack-Update 1903 [hier](https://aka.ms/azurestackupdatedownload) herunterladen.

Nur in verbundenen Szenarios überprüfen Azure Stack-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Weitere Informationen finden Sie unter [Verwalten von Updates für Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).
