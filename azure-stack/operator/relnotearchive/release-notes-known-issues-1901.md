---
title: Azure Stack-Update 1901 | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1901 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: fe321ff69f3ef17f590dd026886e29ed3100d107
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248701"
---
# <a name="azure-stack-1901-update"></a>Azure Stack-Update 1901

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt den Inhalt des Updatepakets 1901. Das Update enthält Verbesserungen, Fehlerbehebungen und neue Funktionen für diese Version von Azure Stack. In diesem Artikel werden auch bekannte Probleme in diesem Release beschrieben, und er enthält einen Link zum Herunterladen des Updates. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Nach dem 26. Februar 2019 ist die Buildnummer des Azure Stack-Updates 1901 **1.1901.0.95.XX.X** oder **1.1901.0.99**. Hinweis:

> [!IMPORTANT]  
> Microsoft hat ein Problem festgestellt, das Kunden betreffen kann, die von 1811 (1.1811.0.101) auf 1901 aktualisieren, und hat zur Behebung des Problems ein aktualisiertes Paket für das Update 1901 veröffentlicht: Build 1.1901.0.99 (aktualisiert von 1.1901.0.95). Für Kunden, die bereits auf 1.1901.0.95 aktualisiert haben, ist keine weitere Aktion erforderlich.
>
> Kunden mit Verbindung, die derzeit 1811 verwenden, sehen das neue Paket für 1901 (1.1901.0.99) automatisch im Administratorportal und sollten es installieren, sobald sie dazu bereit sind. Kunden ohne Verbindung können das neue Paket für 1901 wie [hier beschrieben](../azure-stack-apply-updates.md) herunterladen und importieren.
>
> Kunden mit einer Version des Updates 1901 sind nicht betroffen, wenn sie das nächste vollständige Paket oder Hotfixpaket installieren.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie das [neueste Azure Stack-Hotfix](#azure-stack-hotfixes) für 1811 installieren, bevor Sie Azure Stack auf 1901 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack-Hotfixes

Wenn Sie bereits über 1901 verfügen und noch keine Hotfixes installiert haben, können Sie 1902 direkt installieren, ohne zuerst Hotfix 1901 zu installieren.

- **1811**: Es ist kein aktueller Hotfix verfügbar.
- **1901**: [KB 4500636 – Azure Stack-Hotfix 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Installieren Sie den [neuesten Azure Stack-Hotfix](#azure-stack-hotfixes) für 1811 (falls vorhanden), bevor Sie auf 1901 aktualisieren. Wenn Sie bereits über 1901 verfügen und noch keine Hotfixes installiert haben, können Sie 1902 direkt installieren, ohne zuerst Hotfix 1901 zu installieren.

- Bevor Sie mit der Installation dieses Updates beginnen, führen Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) mit den folgenden Parametern aus, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und führen Sie die Behebung für Einträge durch, für die eine Aktion erforderlich ist:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Wenn Azure Stack von System Center Operations Manager (SCOM) verwaltet wird, müssen Sie das Management Pack für Microsoft Azure Stack auf Version 1.0.3.11 aktualisieren, bevor Sie das Update 1901 anwenden.

## <a name="new-features"></a>Neue Funktionen

Dieses Update enthält die folgenden neuen Funktionen und Verbesserungen für Azure Stack:

- Mit verwalteten Images in Azure Stack können Sie ein verwaltetes Image-Objekt auf einem generalisierten virtuellen Computer (sowohl nicht verwaltet als auch verwaltet) erstellen, der fortan nur noch virtuelle Computer auf verwalteten Datenträgern erstellen kann. Weitere Informationen finden Sie unter [Azure Stack Managed Disks](../../user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Fehlerbehebung: `Import-AzureRmContext` zum korrekten Deserialisieren des gespeicherten Tokens.  
   * **AzureRm.Resources**  
         Fehlerbehebung: `Get-AzureRmResource` zum Abfragen nach Ressourcentyp ohne Berücksichtigung von Groß-/Kleinschreibung.  
   * **Azure.Storage**  
         Das AzureRm-Rollupmodul enthält nun die bereits veröffentlichte Version 4.5.0, welche **api-version 2017-07-29** unterstützt.  
   * **AzureRm.Storage**  
         Das AzureRm-Rollupmodul enthält nun die bereits veröffentlichte Version 5.0.4, welche **api-version 2017-10-01** unterstützt.  
   * **AzureRm.Compute**  
         Zusätzliche einfache Parametersätze in `New-AzureRmVM` und `New-AzureRmVmss` hinzugefügt, Parameter `-Image` unterstützt Angabe von Benutzerimages.  
   * **AzureRm.Insights**  
         Das AzureRm-Rollupmodul enthält nun die bereits veröffentlichte Version 5.1.5, welche **api-version 2018-01-01** für Metriken und Ressourcentypen für Metrikdefinitionen unterstützt.

- **AzureStack 1.7.1**: Hierbei handelt es sich um ein Breaking Change-Release. Details zu Breaking Changes finden Sie unter https://aka.ms/azspshmigration171.
   * **Azs.Backup.Admin Module**  
         Wichtige Änderung: Änderungen am zertifikatbasierten Verschlüsselungsmodus werden gesichert. Unterstützung für symmetrische Schlüssel ist veraltet.  
   * **Azs.Fabric.Admin Module**  
         `Get-AzsInfrastructureVolume` ist veraltet. Verwenden Sie das neue Cmdlet `Get-AzsVolume`.  
         `Get-AzsStorageSystem` ist veraltet.  Verwenden Sie das neue Cmdlet `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` ist veraltet. Das `StorageSubSystem`-Objekt enthält die Kapazitätseigenschaft.  
   * **Azs.Compute.Admin-Modul**  
         Fehlerbehebung: `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: `ConvertTo-PlatformImageObject` wird nur im Erfolgspfad aufgerufen.  
         Fehlerbehebung: `Add-AzsVmExtension`, `Get-AzsVmExtension`: „ConvertTo-VmExtensionObject“ wird nur im Erfolgspfad aufgerufen.  
   * **Azs.Storage.Admin Module**  
         Fehlerbehebung: Neues Speicherplatzkontingent verwendet Standardwerte, wenn keine angegeben werden.

Die Referenz für die aktualisierten Module finden Sie in der [Referenz zum Azure Stack-Modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0&preserve-view=true).

## <a name="fixed-issues"></a>Behobene Probleme

- Ein Problem wurde behoben, bei dem das Portal eine Option zum Erstellen von richtlinienbasierten VPN-Gateways angezeigt hat, die in Azure Stack nicht unterstützt werden. Diese Option wurde aus dem Portal entfernt.

<!-- 16523695 - IS, ASDK -->
- Ein Problem wurde behoben, bei dem nach der Aktualisierung der DNS-Einstellungen von **Azure Stack-DNS** zu **Benutzerdefiniertes DNS** die Instanzen nicht mit der neuen Einstellung aktualisiert wurden.

- <!-- 3235634 - IS, ASDK -->
  Ein Problem wurde behoben, bei dem bei der Bereitstellung von VMs mit Größen, die das Suffix **v2** enthalten (z. B. **Standard_A2_v2**), das Suffix mit klein geschriebenem „v“ angegeben werden musste: **Standard_A2_v2**. Wie in globalen Azure-Umgebungen können Sie nun **Standard_A2_V2** (groß geschriebenes „V“) verwenden.

<!--  2795678 - IS, ASDK --> 
- Ein Problem wurde behoben, bei dem Portal eine Warnung ausgegeben hat, wenn Sie es zum Erstellen virtueller Computer (VMs) in einer Premium-VM-Größe (DS, Ds_v2, FS, FSv2) verwendet haben. Der virtuelle Computer wurde in einem Standardspeicherkonto erstellt. Funktionen, IOPs oder die Abrechnung waren zwar nicht beeinträchtigt, die Warnung wurde aber dennoch behoben.

<!-- 1264761 - IS ASDK -->  
- Ein Problem wurde behoben, bei dem die **Health Controller**-Komponente die folgenden Warnungen generiert hat. Die Warnungen konnten sicher ignoriert werden:

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


<!-- 3507629 - IS, ASDK --> 
- Ein Fehler wurde behoben, bei dem der unter [Computekontingenttypen](../azure-stack-quota-types.md#compute-quota-types) festgelegte Wert 0 für Managed Disks-Kontingenten dem Standardwert 2048 GiB entsprach. Der Nullwert für Kontingente wird jetzt berücksichtigt.

<!-- 2724873 - IS --> 
- Ein Fehler wurde behoben, bei dem bei Verwendung der PowerShell-Cmdlets **Start-AzsScaleUnitNode** oder **Stop-AzsScaleunitNode** zum Verwalten von Skalierungseinheiten beim ersten Versuch, die Skalierungseinheit zu starten oder zu beenden, ein Fehler auftreten konnte.

<!-- 2724961- IS ASDK --> 
- Ein Fehler wurde behoben, bei dem beim Registrieren des Ressourcenanbieters **Microsoft.Insight** in den Abonnementeinstellungen und beim Erstellen eines virtuellen Windows-Computers mit aktivierter Gastbetriebssystemdiagnose im Diagramm mit der CPU-Nutzung in Prozent auf der Übersichtsseite des virtuellen Computers keine Metrikdaten angezeigt wurden. Die Daten werden nun korrekt angezeigt.

- Ein Fehler wurde behoben, bei dem das Ausführen des Cmdlets **Get-AzureStackLog** nach Ausführung von **Test-AzureStack** in derselben Sitzung für den privilegierten Endpunkt (PEP) einen Fehler verursacht hat. Sie können jetzt dieselbe PEP-Sitzung verwenden, in der Sie **Test-AzureStack** ausgeführt haben.

<!-- bug 3615401, IS -->
- Ein Fehler in automatischen Sicherungen wurde behoben, bei dem der Planungsdienst unerwartet in den deaktivierten Zustand gewechselt ist. 

<!--2850083, IS ASDK -->
- Die Schaltfläche **Gateway zurücksetzen**, die einen Fehler verursacht hat, wenn sie angeklickt wurde, wurde aus dem Azure Stack-Portal entfernt. Diese Schaltfläche hat in Azure Stack keine Funktion, da Azure Stack ein mehrinstanzenfähiges Gateway anstatt dedizierte VM-Instanzen für jedes Mandanten-VPN-Gateway verwendet. Daher wurde sie entfernt, um Verwirrung zu vermeiden. 

<!-- 3209594, IS ASDK -->
- Der Link **Wirksame Sicherheitsregel** wurde vom Blatt **Netzwerkeigenschaften** entfernt, da dieses Feature in Azure Stack nicht unterstützt wird. Durch das Vorhandensein des Links entstand der Eindruck, dass dieses Feature unterstützt wird, jedoch nicht funktioniert. Um Verwirrung zu vermeiden, haben wir den Link entfernt.

<!-- 3139614 | IS -->
- Ein Fehler wurde behoben, bei dem nach dem Anwenden eines Updates von einem OEM auf Azure Stack die Benachrichtigung **Update verfügbar** im Azure Stack-Administratorportal nicht angezeigt wurde.

## <a name="changes"></a>Änderungen

<!-- 3083238 IS -->
- Sicherheitsverbesserungen in diesem Update führen zu einem Anstieg der Größe der Sicherung der Verzeichnisdienstrolle. Eine aktualisierte Anleitung zur Dimensionierung für den externen Speicherort finden Sie in der [Dokumentation zur Sicherung der Infrastruktur../azure-stack-backup-reference.md#storage-location-sizing). Diese Änderung führt dazu, dass die Sicherung wegen der größeren Menge zu übertragender Daten länger braucht bis zum Abschluss. Diese Änderung betrifft integrierte Systeme. 

- Ab Januar 2019, können Sie Kubernetes-Cluster in verbundenen Azure Stack-Stapeln bereitstellen, die bei Active Directory-Verbunddiensten (AD FS) registriert sind (Internetzugriff erforderlich). Befolgen Sie die Anweisungen [hier](../azure-stack-solution-template-kubernetes-cluster-add.md), um das neue Kubernetes-Marketplace-Element herunterzuladen. Befolgen Sie die Anweisungen [hier](../../user/azure-stack-solution-template-kubernetes-adfs.md), um einen Kubernetes-Cluster bereitzustellen. Beachten Sie die neuen Parameter, mit denen angegeben wird, ob das Zielsystem bei ADD oder AD FS registriert ist. Bei AD FS sind neue Felder verfügbar, um die Parameter der Key Vault-Instanz einzugeben, in der das Bereitstellungszertifikat gespeichert ist.

   Beachten Sie, dass für die Bereitstellung von Kubernetes-Clustern auch mit AD FS-Unterstützung Internetzugriff erforderlich ist.

- Nach der Installation von Updates oder Hotfixes für Azure Stack können neue Funktionen eingeführt werden, die u. U. das Gewähren neuer Berechtigungen für eine oder mehrere Identitätsanwendungen erfordern. Das Gewähren dieser Berechtigungen erfordert Administratorzugriff auf das Stammverzeichnis und kann daher nicht automatisch erfolgen. Beispiel:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Es gibt eine neue Überlegung für die genaue Planung von Azure Stack-Kapazität. Mit dem Update 1901 gibt es jetzt ein Limit für die Gesamtzahl virtueller Computer, die erstellt werden können.  Dieses Limit soll temporär sein, um Lösungsinstabilitäten zu vermeiden. Die Ursache des Stabilitätsproblems bei einer höheren Anzahl von VMs wird behandelt, aber es steht noch kein genauer Zeitplan für die Korrektur fest. Mit dem Update 1901 gibt es jetzt ein Limit pro Server von 60 VMs bei einem Gesamtlösungslimit von 700.  Beispielsweise wäre ein Azure Stack-VM-Limit bei 8 Servern 480 (8 * 60).  Bei einer Azure Stack-Lösung mit 12 bis 16 Servern wäre das Limit 700. Dieses Limit wurde unter Berücksichtigung aller Überlegungen hinsichtlich der Computekapazität eingerichtet, z. B. der Resilienzreserve und des CPU-Verhältnisses virtuell zu physisch, die ein Operator bei dem Stamp erhalten möchte. Weitere Informationen finden Sie in der neuen Version von Capacity Planner.  
Falls das VM-Skalierungslimit erreicht wurde, würden die folgenden Fehlercodes als Ergebnis zurückgegeben: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- Die Compute-API-Version wurde auf 2017-12-01 erhöht.

- Die Infrastruktursicherung erfordert jetzt nur ein Zertifikat mit einem öffentlichen Schlüssel (. CER) für die Verschlüsselung von Sicherungsdaten. Die Unterstützung für symmetrische Verschlüsselungsschlüssel ist ab 1901 veraltet. Wenn die Infrastruktursicherung vor der Aktualisierung auf 1901 konfiguriert wurde, werden die Verschlüsselungsschlüssel beibehalten. Es gibt mindestens zwei weitere Updates mit Unterstützung für Abwärtskompatibilität, um Sicherungseinstellungen zu aktualisieren. Weitere Informationen finden Sie unter [Bewährte Methoden für den Infrastructure Backup-Dienst](../azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures (CVE, allgemeine Sicherheitslücken und Schwachstellen)

Dieses Update installiert die folgenden Sicherheitsupdates:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Weitere Informationen zu diesen Sicherheitslücken erhalten Sie durch Klicken auf die vorhergehenden Links oder im Microsoft Knowledge Base-Artikel [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

- Wenn Sie versuchen, ein Azure Stack-Update zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download erneut.

- Wenn während der Ausführung von [Test-AzureStack](../azure-stack-diagnostic-test.md) entweder der Test **AzsInfraRoleSummary** oder der Test **AzsPortalApiSummary** fehlschlägt, werden Sie aufgefordert, **Test-AzureStack** mit dem Parameter `-Repair` auszuführen.  Wenn Sie diesen Befehl ausführen, schlägt er mit folgender Fehlermeldung fehl: `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- Wenn Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) ausführen, wird eine Warnmeldung des Baseboard-Verwaltungscontrollers (BMC) angezeigt. Sie können diese Warnung problemlos ignorieren.

- <!-- 2468613 - IS --> Während der Installation dieses Updates werden möglicherweise Warnungen mit dem Titel `Error - Template for FaultType UserAccounts.New is missing.` angezeigt. Diese Warnungen können ignoriert werden. Die Warnungen werden automatisch geschlossen, nachdem die Installation dieses Updates abgeschlossen wurde.

## <a name="post-update-steps"></a>Schritte nach dem Update

- Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie sowohl unter [Hotfixes](#hotfixes) als auch [Wartungsrichtlinie](../azure-stack-servicing-policy.md).  

- Rufen Sie die Verschlüsselungsschlüssel für ruhende Daten ab, und speichern Sie diese sicher außerhalb Ihrer Azure Stack-Bereitstellung. Befolgen Sie die [Anleitungen zum Abrufen der Schlüssel](../azure-stack-security-bitlocker.md).

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

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen. Um dieses Problem zu umgehen, erstellen Sie das Speicherkonto erneut mit demselben Namen wie zuvor.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Die Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen bietet „CentOS 7.2-basiert“ als Option für die Bereitstellung an. Da dieses Image in Azure Stack nicht verfügbar ist, wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Operator aus dem Marketplace heruntergeladen wurde.  

<!-- TBD - IS ASDK --> 
- Nach dem Anwenden des Updates 1901 können beim Bereitstellen von virtuellen Computern mit Managed Disks die folgenden Probleme auftreten:

   - Wenn das Abonnement vor dem Update 1808 erstellt wurde, schlägt die Bereitstellung eines virtuellen Computers mit Managed Disks möglicherweise mit einer internen Fehlermeldung fehl. Um den Fehler zu beheben, führen Sie die folgenden Schritte für jedes Abonnement aus:
      1. Navigieren Sie im Mandantenportal zu **Abonnements**, und suchen Sie nach dem Abonnement. Wählen Sie **Ressourcenanbieter** und dann **Microsoft.Compute** aus, und klicken Sie dann auf **Erneut registrieren**.
      2. Navigieren Sie unter dem gleichen Abonnement zu **Zugriffssteuerung (IAM)**, und überprüfen Sie, ob **AzureStack-DiskRP-Client** aufgeführt ist.
   - Wenn Sie eine Umgebung mit mehreren Mandanten konfiguriert haben, schlägt die Bereitstellung von virtuellen Computern in einem Abonnement, dem ein Gastverzeichnis zugeordnet ist, möglicherweise mit einer internen Fehlermeldung fehl. Zum Beheben des Fehlers führen Sie die in [diesem Artikel](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) beschriebenen Schritte aus, um alle Gastverzeichnisse neu zu konfigurieren.

- Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden. Um dieses Problem zu umgehen, verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie kennwortbasierte Authentifizierung.

- Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen. Wählen Sie zur Problemumgehung die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.

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

Sie können das Paket für das Azure Stack-Update 1901 [hier](https://aka.ms/azurestackupdatedownload) herunterladen. 

Nur in verbundenen Szenarios überprüfen Azure Stack-Bereitstellungen in regelmäßigen Abständen einen gesicherten Endpunkt und benachrichtigen Sie automatisch, wenn ein Update für Ihre Cloud verfügbar ist. Weitere Informationen finden Sie unter [Verwalten von Updates für Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](../azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](../azure-stack-monitor-update.md).  
