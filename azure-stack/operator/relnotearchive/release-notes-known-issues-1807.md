---
title: Azure Stack 1807-Update | Microsoft-Dokumentation
description: Erfahren Sie, welche Neuerungen im Update 1807 für integrierte Azure Stack-Systeme enthalten sind, welche bekannten Probleme es gibt und wo das Update heruntergeladen werden kann.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 9dd3647f607dc743c6eff5907ae4a47fa097e764
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248646"
---
# <a name="azure-stack-1807-update"></a>Azure Stack-Update 1807

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt den Inhalt des Updatepakets 1807. Dieses Update enthält Verbesserungen, Fehlerbehebungen und bekannten Probleme für diese Version von Azure Stack sowie Informationen darüber, wo das Update heruntergeladen werden kann. Die bekannten Probleme sind in Probleme unterteilt, die sich direkt auf den Updateprozess beziehen, und in Probleme mit dem Build (nach der Installation).

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.

## <a name="build-reference"></a>Buildreferenz

Die Buildnummer des Azure Stack-Updates 1807 ist **1.1807.0.76**.  

### <a name="new-features"></a>Neue Funktionen

Dieses Update enthält die folgenden Verbesserungen für Azure Stack.

<!-- 1658937 | ASDK, IS --> 
- **Starten von Sicherungen anhand eines vordefinierten Zeitplans**: Als Appliance kann Azure Stack nun automatisch Infrastruktursicherungen regelmäßig auslösen, um Benutzereingriffe zu eliminieren. Zudem bereinigt Azure Stack auch automatisch die externe Freigabe für Sicherungen, die älter als die definierte Aufbewahrungsdauer sind. Weitere Informationen finden Sie unter [Aktivieren der Sicherung für Azure Stack mit PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Zur Gesamtsicherungszeit wurde die Datenübertragungszeit addiert.** Weitere Informationen finden Sie unter [Aktivieren der Sicherung für Azure Stack mit PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **Die externe Kapazität für Sicherungen zeigt jetzt die richtige Kapazität der externen Freigabe an.** (Zuvor war diese hardcodiert und zeigte 10 GB an.) Weitere Informationen finden Sie unter [Aktivieren der Sicherung für Azure Stack mit PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Erweitern der Kapazität** durch [Hinzufügen zusätzlicher Knoten von Skalierungseinheiten](../azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Azure Resource Manager-Vorlagen unterstützen jetzt das condition-Element:** Sie können jetzt eine Ressource in einer Azure Resource Manager-Vorlage mit einer Bedingung bereitstellen. Sie können Ihre Vorlage für die Bereitstellung einer Ressource auf Grundlage einer Bedingung (beispielsweise das Vorhandensein eines Parameterwerts) entwerfen. Informationen zur Verwendung einer Vorlage als Bedingung finden Sie unter [Bedingtes Bereitstellen einer Ressource](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) und [Abschnitt „Variablen“ von Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) in der Azure-Dokumentation. 

   Sie können Vorlagen auch zum [Bereitstellen von Azure-Ressourcen für mehrere Abonnements oder Ressourcengruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment) verwenden.  

<!--2753073 | IS, ASDK -->  
- **Die Unterstützung der Microsoft.Network-API-Ressourcenversion wurde aktualisiert** und umfasst nun Unterstützung für die API-Version 2017-10-01 von 2015-06-15 für Azure Stack-Netzwerkressourcen.  Die Unterstützung für Ressourcenversionen zwischen 2017-10-01 und 2015-06-15 ist nicht in diesem Release enthalten.  Weitere Informationen zu den Funktionsunterschieden finden Sie unter [Überlegungen zu Azure Stack-Netzwerken](../../user/azure-stack-network-differences.md).

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack bietet jetzt Unterstützung für Reverse-DNS-Lookups für extern zugängliche Azure Stack-Infrastrukturendpunkte** (d.h. für Portal, Verwaltungsportal, Verwaltung und Administratorverwaltung). Dies ermöglicht die Auflösung der Namen von externen Azure Stack-Endpunkten über eine IP-Adresse.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack unterstützt jetzt das Hinzufügen zusätzlicher Netzwerkschnittstellen zu einer vorhandenen VM.**  Diese Funktion ist über das Portal, PowerShell und die Befehlszeilenschnittstelle verfügbar. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Netzwerkschnittstellen](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) in der Azure-Dokumentation. 

<!-- 2222444 | IS, ASDK   -->  
- **An Netzwerkverbrauchseinheiten wurden Verbesserungen bezüglich der Genauigkeit und Resilienz vorgenommen**.  Netzwerkverbrauchseinheiten sind jetzt genauer und berücksichtigen angehaltene Abonnements, Ausfallzeiten und Racebedingungen.

<!-- 2753080 | IS -->  
- **Benachrichtigung über verfügbares Update.** In verbundenen Azure Stack-Bereitstellungen wird nun in regelmäßigen Abständen ein gesicherter Endpunkt überprüft und ermittelt, ob ein Update für Ihre Cloud verfügbar ist. Diese Benachrichtigung wird auf der Kachel „Update“ angezeigt, wie dies auch nach dem manuellen Überprüfen und Importieren eines neuen Updates der Fall ist. Weitere Informationen finden Sie unter [Verwalten von Updates für Azure Stack](../azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Verbesserungen am Azure Stack-Syslog-Client (Vorschaufeature)**. Dieser Client ermöglicht die Weiterleitung von Überwachungen und Protokollen der Azure Stack-Infrastruktur an einen Syslog-Server oder eine SIEM-Software (Security Information & Event Management) außerhalb von Azure Stack. Der Syslog-Client unterstützt jetzt das TCP-Protokoll mit Nur-Text- oder die TLS 1.2-Verschlüsselung (Letzteres ist die Standardkonfiguration). Sie können die TLS-Verbindung nur mit Serverauthentifizierung oder mit gegenseitiger Authentifizierung konfigurieren.

  Verwenden Sie zum Konfigurieren der Kommunikation des Syslog-Clients mit dem Syslog-Server (beispielsweise Protokoll, Verschlüsselung und Authentifizierung) das Cmdlet *Set-SyslogServer*. Dieses Cmdlet ist über den privilegierten Endpunkt (PEP) verfügbar.

  Verwenden Sie zum Hinzufügen des clientseitigen Zertifikats für die gegenseitige TLS 1.2-Authentifizierung des Syslog-Clients das Cmdlet „Set-SyslogClient“ in der PEP-Sitzung.

  Mit diesem Vorschaufeature können Sie eine deutlich größere Anzahl von Überwachungen und Warnungen anzeigen. 

  Da sich dieses Feature noch in der Vorschauphase befindet, sollten Sie es nicht in Produktionsumgebungen verwenden.

  Weitere Informationen finden Sie unter [Azure Stack-Syslog-Weiterleitung](../azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager enthält den Namen der Region.** In diesem Release enthalten aus der Azure Resource Manager-Vorlage abgerufene Objekte jetzt das Attribut „Regionsname“. Wenn ein vorhandenes PowerShell-Skript das Objekt direkt an ein anderes Cmdlet übergibt, kann bei der Ausführung des Skripts ein Fehler auftreten. Dies ist das mit Azure Resource Manager konforme Verhalten und erfordert, dass der aufrufende Client das Attribut „Region“ subtrahiert. Weitere Informationen zum Azure Resource Manager finden Sie in der [Dokumentation zum Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/). Überprüfen 8 bis 10 MDB -->

<!-- TBD | IS, ASDK -->  
- **Änderungen an der Funktion „Delegierte Anbieter“.** Beginnend mit dem Update 1807 wird das Modell für delegierte Anbieter vereinfacht, damit es besser auf das Modell für Azure-Handelspartner ausgerichtet ist, und delegierte Anbieter können keine anderen delegierten Anbieter erstellen. Dadurch vereinfacht sich das Modell wesentlich, und die Funktion „Delegierte Anbieter“ wird auf einer einzelnen Ebene verfügbar. Um den Übergang zum neuen Modell und die Verwaltung der Abonnements zu ermöglichen, können die Benutzerabonnements jetzt zwischen neuen oder vorhandenen Abonnements für delegierte Anbieter verschoben werden, die zum selben Verzeichnismandanten gehören. Benutzerabonnements, die zum Abonnement für Standardanbieter gehören, können auch in die Abonnements für delegierte Anbieter im gleichen Verzeichnismandanten verschoben werden.  Weitere Informationen finden Sie unter [Delegieren von Angeboten in Azure Stack](../azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Kürzere VM-Erstellungszeit** für VMs, die mit aus dem Azure Marketplace heruntergeladenen Images erstellt werden.

<!-- TBD | IS, ASDK -->  
- **Verbesserte Benutzerfreundlichkeit von Azure Stack Capacity Planner**. Der Azure Stack [Capacity Planner](https://aka.ms/azstackcapacityplanner) umfasst jetzt eine vereinfachte Umgebung für die Eingabe von S2D-Caches und S2D-Kapazitäten beim Definieren von Lösungs-SKUs. Die Beschränkung auf 1.000 virtuelle Computer wurde aufgehoben.


### <a name="fixed-issues"></a>Behobene Probleme

<!-- TBD | ASDK, IS --> 
- Die Zuverlässigkeit des Updateprozesses wurde durch verschiedene Verbesserungen erhöht. Zudem wurde durch die Behebung von Fehlern in der zugrunde liegenden Infrastruktur die potenzielle Downtime für Workloads während der Aktualisierung minimiert.

<!--2292271 | ASDK, IS --> 
- Es wurde ein Problem behoben, bei dem ein geänderter Kontingentgrenzwert nicht für bestehende Abonnements galt. Wenn Sie nun einen Kontingentgrenzwert für eine Netzwerkressource erhöhen, die Teil eines mit einem Benutzerabonnement verknüpften Angebots und Plans ist, gilt der neue Grenzwert sowohl für die bereits bestehenden als auch für neue Abonnements.

<!-- 448955 | IS ASDK --> 
- Sie können jetzt erfolgreich Aktivitätsprotokolle für Systeme abfragen, die in einer UTC+N-Zeitzone bereitgestellt sind.    

<!-- 2319627 |  ASDK, IS --> 
- Die Vorprüfung der Sicherungskonfigurationsparameter (Pfad/Benutzername/Kennwort/Verschlüsselungsschlüssel) legt keine falschen Einstellungen mehr in der Sicherungskonfiguration fest. (Früher wurden für die Sicherung falsche Einstellungen festgelegt, wodurch beim Auslösen der Sicherung ein Fehler aufgetreten ist.)

<!-- 2215948 |  ASDK, IS -->
- Die Sicherungsliste wird nun aktualisiert, wenn Sie die Sicherung manuell von der externen Freigabe löschen.

<!-- 2332636 | IS -->  
- Das Update auf diese Version setzt den Standardbesitzer des Standardanbieterabonnements nicht mehr auf den integrierten **CloudAdmin**-Benutzer zurück, wenn es mit AD FS bereitgestellt wird.

<!-- 2360715 |  ASDK, IS -->  
- Wenn Sie die Datencenterintegration einrichten, greifen Sie nicht mehr über eine Azure-Dateifreigabe auf die AD FS-Metadatendatei zu. Weitere Informationen finden Sie unter [Einrichten der AD FS-Integration durch Bereitstellen einer Verbundmetadatendatei](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Es wurde ein Problem behoben, das verhinderte, dass Benutzer eine vorhandene öffentliche IP-Adresse, die zuvor einer Netzwerkschnittstelle oder einem Load Balancer zugewiesen war, einer neuen Netzwerkschnittstelle oder einem neuen Load Balancer zuweisen.  

<!-- 2551834 - IS, ASDK --> 
-  Wenn Sie im Verwaltungs- oder Benutzerportal für ein Speicherkonto die Option *Übersicht* auswählen, werden alle erwarteten Informationen korrekt im Bereich *Zusammenfassung* angezeigt. 

<!-- 2551834 - IS, ASDK --> 
-  Wenn Sie im Verwaltungs- oder Benutzerportal für ein Speicherkonto die Option *Tags* auswählen, werden die Informationen jetzt korrekt angezeigt.

<!-- TBD - IS ASDK --> 
- In dieser Version von Azure Stack wurde das Problem behoben, durch das die Anwendung von Treiberupdates aus OEM-Erweiterungspaketen verhindert wurde.

<!-- 2055809- IS ASDK --> 
- Wir haben ein Problem behoben, aufgrund dessen Sie keine VMs auf dem Blatt „Compute“ löschen konnten, wenn beim Erstellen der VM ein Fehler auftrat.  

<!--  2643962 IS ASDK -->  
- Die Warnung für *niedrige Arbeitsspeicherkapazität* wird nicht mehr fälschlicherweise angezeigt.

- Es wurden **verschiedene Fehlerbehebungen** hinsichtlich der Leistung, Stabilität, Sicherheit und des von Azure Stack eingesetzten Betriebssystems vorgenommen.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures (CVE, allgemeine Sicherheitslücken und Schwachstellen)
Azure Stack verwendet die Server Core-Installationen von Windows Server 2016 zum Hosten der wichtigsten Infrastrukturkomponenten. Mit diesem Release werden die folgenden Windows Server 2016-Updates auf den Infrastrukturservern für Azure Stack installiert: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Weitere Informationen zu diesen Sicherheitslücken erhalten Sie durch Klicken auf die vorhergehenden Links oder in den Microsoft Knowledge Base-Artikeln [4338814](https://support.microsoft.com/help/4338814) und [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Voraussetzungen

### <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie das Azure Stack 1805-Update, bevor Sie das Azure Stack 1807-Update anwenden.  Es gab kein Update 1806.  

- Installieren Sie das neueste verfügbare Update oder Hotfix für Version 1805.  

- Bevor Sie mit der Installation dieses Updates beginnen, führen Sie [Test-AzureStack](../azure-stack-diagnostic-test.md) mit den folgenden Parametern aus, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und lösen Sie solche auf, die eine Aktion erfordern.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Bekannte Probleme mit dem Updateprozess

<!-- 2468613 - IS --> 
- Während der Installation dieses Updates werden unter Umständen Warnungen mit folgender Meldung angezeigt: *Fehler: Die Vorlage für FaultType UserAccounts.New fehlt.*  Sie können diese Warnungen ignorieren. Diese Warnungen werden automatisch nach der Installation dieses Updates geschlossen.

<!-- 2489559 - IS --> 
- Versuchen Sie nicht, während der Installation dieses Updates virtuelle Computer zu erstellen. Weitere Informationen zum Verwalten von Updates finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- Wenn ein Update eine Aktion erfordert, wird die entsprechende Warnung unter bestimmten Umständen möglicherweise nicht generiert. Der genaue Status wird im Portal dennoch angegeben und nicht beeinträchtigt.

### <a name="post-update-steps"></a>Schritte nach dem Update
Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in den folgenden Knowledge Base-Artikeln sowie in unserer [Wartungsrichtlinie](../azure-stack-servicing-policy.md). 
- [KB 4464231 – Azure Stack-Hotfix: Azure Stack-Hotfix 1.1807.1.78](https://support.microsoft.com/help/4464231)

<!-- 2933866 IS -->  Nach der Installation dieses Updates können Sie den **verbesserten Status für fehlgeschlagene Update-Installationen anzeigen.** Dies kann Informationen zu früheren Fehlern bei der Installation von Updates beinhalten, die an die beiden neuen ZUSTAND-Kategorien angepasst werden. Bei diesen neuen ZUSTAND-Kategorien handelt es sich um *PreparationFailed* und *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Bekannte Probleme (nach der Installation)

Im Folgenden werden bekannte Probleme nach der Installation zu dieser Buildversion vorgestellt.

### <a name="portal"></a>Portal

- Die technische Dokumentation für Azure Stack konzentriert sich auf das neueste Release. Aufgrund von Portaländerungen zwischen den Releases kann das, was bei der Verwendung der Azure Stack-Portale angezeigt wird, von dem abweichen, was in der Dokumentation angezeigt wird. 

- Die Funktion, mit der im Administratorportal [eine neue Supportanfrage über die Dropdownliste geöffnet wird](../azure-stack-manage-portals.md#quick-access-to-help-and-support), ist nicht verfügbar. Verwenden Sie für in Azure Stack integrierte Systeme stattdessen den folgenden Link: [https://aka.ms/newsupportrequest](https://aka.ms/newsupportrequest).

<!-- 2931230 IS  ASDK --> 
- Pläne, die einem Benutzerabonnement als Add-On-Plan hinzugefügt wurden, können nicht gelöscht werden, auch wenn Sie den Plan aus dem Benutzerabonnement entfernen. Der Plan ist so lange vorhanden, bis die Abonnements gelöscht werden, die auf den Add-On-Plan verweisen. 

<!--2760466 IS  ASDK --> 
- Bei der Installation einer neuen Azure Stack-Umgebung, die diese Version ausführt, wird die Warnung, dass eine *Aktivierung erforderlich* ist, möglicherweise nicht angezeigt. Die [Aktivierung](../azure-stack-registration.md) ist erforderlich, damit Sie die Marketplace-Syndikation verwenden können.  

<!-- TBD - IS ASDK --> 
- Die zwei administrativen Abonnementtypen, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**. Diese Abonnementtypen sind in neuen Azure Stack-Umgebungen ab Version 1804 sichtbar, aber noch nicht zur Verwendung bereit. Sie sollten den Abonnementtyp **Standardanbieter** weiterhin verwenden.

<!-- 2403291 - IS ASDK --> 
- Möglicherweise haben Sie die horizontale Scrollleiste am unteren Rand des Verwaltungs- und Benutzerportals nicht verwendet. Wenn Sie nicht auf die horizontale Bildlaufleiste zugreifen können, verwenden Sie die Breadcrumb-Leiste, um zu einem vorherigen Blatt im Portal zu navigieren. Wählen Sie hierzu den Namen des Blattes, das Sie anzeigen möchten, aus der Breadcrumb-Liste oben links im Portal aus.

<!-- TBD - IS --> 
- Es ist es u.U. nicht möglich, Compute- oder Speicherressourcen im Administratorportal anzuzeigen. Grund für dieses Problem ist ein Fehler bei der Installation des Updates, der dazu führt, dass das Update fälschlicherweise als erfolgreich gemeldet wird. Wenn dieses Problem auftritt, wenden Sie sich an Microsoft Customer Support Services, um Unterstützung zu erhalten.

<!-- TBD - IS --> 
- Es kann sein, dass im Portal ein leeres Dashboard angezeigt wird. Wählen Sie zum Wiederherstellen des Dashboards oben rechts im Portal das Zahnradsymbol und dann die Option **Standardeinstellungen wiederherstellen**.

<!-- TBD - IS ASDK --> 
- Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen. Eine Problemumgehung besteht darin, zuerst Benutzerressourcen oder die gesamte Ressourcengruppe zu löschen und anschließend Benutzerabonnements zu löschen.

<!-- TBD - IS ASDK --> 
- Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen. Verwenden Sie für die Problemumgehung PowerShell, um Berechtigungen zu überprüfen.


### <a name="health-and-monitoring"></a>Integrität und Überwachung
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

<!-- 2368581 - IS. ASDK --> 
-  Wenn Sie als Azure Stack-Bediener eine Fehlermeldung über unzureichenden Speicher erhalten und virtuelle Computer der Mandanten nicht mit einem **Fabric-VM-Erstellungsfehler** bereitgestellt werden können, verfügt der Azure Stack-Stempel möglicherweise nicht über genügend Arbeitsspeicher. Mit dem [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) können Sie die verfügbare Kapazität für Ihre Workloads besser bestimmen.


### <a name="compute"></a>Compute

<!-- 2724873 - IS --> 
-  Wenn Sie die PowerShell-Cmdlets **Start-AzsScaleUnitNode** oder **Stop-AzsScaleunitNode** verwenden, um Skalierungseinheiten zu verwalten, tritt beim ersten Versuch, die Skalierungseinheit zu starten oder zu stoppen, möglicherweise ein Fehler auf. Wenn beim ersten Ausführen des Cmdlets ein Fehler auftritt, führen Sie das Cmdlet ein zweites Mal aus. Bei der zweiten Ausführung sollte der Vorgang erfolgreich sein. 

<!-- 2494144 - IS, ASDK --> 
- Bei der Auswahl der Größe des virtuellen Computers für die Bereitstellung eines virtuellen Computers sind einige VM-Größen der Serie F beim Erstellen einer VM nicht als Teil der Größenauswahl sichtbar. Folgende VM-Größen werden im Selektor nicht angezeigt: *F8s_v2*, *F16s_v2*, *F32s_v2* und *F64s_v2*.  
  Um das Problem zu umgehen, verwenden Sie eine der folgenden Methoden zum Bereitstellen einer VM. Bei jeder Methode müssen Sie die gewünschte VM-Größe angeben.

  - **Azure Resource Manager-Vorlage**: Wenn Sie eine Vorlage verwenden, legen Sie *vmSize* in der Vorlage entsprechend der gewünschten VM-Größe fest. Der folgende Eintrag z.B. wird zum Bereitstellen einer VM mit der Größe *F32s_v2* verwendet:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI**: Sie können den Befehl [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) verwenden und die VM-Größe als Parameter angeben, ähnlich wie `--size "Standard_F32s_v2"`.

  - **PowerShell**: Mit PowerShell können Sie [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) mit dem Parameter verwenden, der die VM-Größe angibt, ähnlich wie `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Die Skalierungseinstellungen für Skalierungsgruppen für virtuelle Computer sind im Portal nicht verfügbar. Dieses Problem können Sie umgehen, indem Sie [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) verwenden. Aufgrund der Versionsunterschiede bei PowerShell müssen Sie den `-Name`-Parameter statt des `-VMScaleSetName`-Parameters verwenden.

<!-- TBD - IS --> 
- Wenn Sie im Portal unter **Neu** > **Berechnen** > **Verfügbarkeitsgruppe** eine Verfügbarkeitsgruppe erstellen, können Sie nur eine Verfügbarkeitsgruppe mit einer Fehlerdomäne und einer Updatedomäne erstellen. Erstellen Sie bei der Erstellung eines neuen virtuellen Computers zur Problemumgehung die Verfügbarkeitsgruppe mithilfe von PowerShell, der CLI oder im Portal.

<!-- TBD - IS ASDK --> 
- Beim Erstellen von virtuellen Computern im Azure Stack-Benutzerportal zeigt das Portal eine falsche Anzahl von Datenträgern an, die an eine VM der Serie DS angefügt werden können. VMs der Serie DS können so viele Datenträger wie bei der Azure-Konfiguration aufnehmen.

<!-- TBD - IS ASDK --> 
- Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Benutzer eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.  

<!-- 1662991 IS ASDK --> 
- Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren.  

<!-- 2724961- IS ASDK --> 
-  Wenn Sie den **Microsoft.Insight**-Ressourcenanbieter in den Abonnementeinstellungen registrieren und einen virtuellen Windows-Computer mit aktivierter Gastbetriebssystemdiagnose erstellen, werden auf der Übersichtsseite des virtuellen Computers keine Metrikdaten angezeigt. 

   Navigieren Sie zum Anzeigen von Metrikdaten (etwa das Diagramm „CPU-Prozentsatz“ für den virtuellen Computer) zum Blatt **Metriken**, und zeigen Sie alle unterstützten Gastbetriebssystemmetriken für virtuelle Windows-Computer an.

### <a name="networking"></a>Netzwerk  

<!-- 1766332 - IS ASDK --> 
-  Wenn Sie unter **Netzwerk** auf **VPN-Gateway erstellen** klicken, um eine VPN-Verbindung einzurichten, wird als VPN-Typ **Richtlinienbasiert** aufgeführt. Wählen Sie diese Option nicht aus. Nur die Option **Routenbasiert** wird in Azure Stack unterstützt.

<!-- 1902460 - IS ASDK --> 
- Azure Stack unterstützt ein einzelnes *Gateway eines lokalen Netzwerks* pro IP-Adresse. Dies gilt für alle Mandantenabonnements. Nach der Herstellung der Verbindung mit dem ersten Gateway eines lokalen Netzwerks werden nachfolgende Versuche zum Erstellen einer Ressource für Gateways lokaler Netzwerke mit der gleichen IP-Adresse blockiert.

<!-- 16309153 - IS ASDK --> 
- In einem virtuellen Netzwerk, das mit der DNS-Servereinstellung *Automatisch* erstellt wurde, tritt bei der Änderung eines benutzerdefinierten DNS-Servers ein Fehler auf. Die aktualisierten Einstellungen werden nicht per Pushvorgang auf VMs in diesem VNET übertragen.

<!-- 2702741 -  IS ASDK --> 
- Öffentliche IP-Adressen, die mithilfe der dynamischen Zuordnungsmethode bereitgestellt wurden, werden nach der Ausgabe eines Befehls zum Beenden und Aufheben der Zuordnung nicht unbedingt beibehalten.

<!-- 2529607 - IS ASDK --> 
-  Während der *Geheimnisrotation* von Azure Stack sind öffentliche IP-Adressen für einen Zeitraum von zwei bis fünf Minuten nicht erreichbar.

<!-- 2664148 - IS ASDK --> 
- In Szenarien, in denen der Mandant über einen S2S-VPN-Tunnel auf seine VMs zugreift, kann es passieren, dass bei Verbindungsversuchen Fehler auftreten, wenn das lokale Subnetz dem lokalen Netzwerkgateway erst nach der Erstellung des Gateways hinzugefügt wurde. 


### <a name="sql-and-mysql"></a>SQL und MySQL



<!-- No Number - IS, ASDK -->  
- Sonderzeichen, einschließlich Leerzeichen und Interpunktion, werden im Namen für die **Familie** nicht unterstützt, wenn Sie die SKU für die SQL- und MySQL-Ressourcenanbieter erstellen. 

<!-- TBD - IS --> 
- Auf Servern, die SQL oder MySQL hosten, kann nur der Ressourcenanbieter Elemente erstellen. Die auf einem Hostserver erstellten Elemente, die nicht vom Ressourcenanbieter erstellt wurden, könnten zu einem Zustand ohne Entsprechung führen.  

<!-- TBD - IS -->
> [!NOTE]   
> Nachdem Sie ein Update auf diese Version von Azure Stack ausgeführt haben, können Sie die zuvor bereitgestellten SQL- und MySQL-Ressourcenanbieter weiterhin verwenden.  Es wird empfohlen, SQL und MySQL zu aktualisieren, sobald ein neues Release verfügbar ist. Wenden Sie Updates wie bei Azure Stack sequenziell auf die Ressourcenanbieter SQL und MySQL an. Wenn Sie z.B. Version 1804 verwenden, wenden Sie zuerst Version 1805 an, und aktualisieren Sie sie dann auf 1807.  
>  
> Die Installation dieses Updates wirkt sich nicht auf die aktuelle Verwendung der von Ihren Benutzern verwendeten Ressourcenanbieter SQL oder MySQL aus. 
> Unabhängig von der Version der Ressourcenanbieter, die Sie verwenden, bleiben Ihre Benutzerdaten in deren Datenbanken davon unberührt und stehen weiterhin für den Zugriff zur Verfügung.  

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Benutzer müssen den Speicherressourcenanbieter vor dem Erstellen seiner ersten Azure-Funktion im Abonnement registrieren.

<!-- 2489178 - IS ASDK --> 
- Um die Infrastruktur (Worker-, Verwaltungs-, Front-End-Rollen) horizontal hochzuskalieren, müssen Sie PowerShell verwenden, wie in den Anmerkungen zu dieser Version für die Berechnung beschrieben.

<!-- TBD - IS ASDK --> 
- App Service kann zurzeit nur im *Abonnement für Standardanbieter* bereitgestellt werden. 


### <a name="usage"></a>Verwendung  
<!-- TBD - IS ASDK --> 
-  Die Ansicht „Verwendung“ für die Nutzungsdaten öffentlicher IP-Adressen zeigt den gleichen *EventDateTime*-Wert für jeden Datensatz anstatt des *TimeDate*-Stempels, der anzeigt, wann dieser jeweils erstellt wurde. Derzeit können Sie diese Daten nicht nutzen, um eine genaue Buchhaltung über die Nutzung öffentlicher IP-Adressen durchzuführen.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Herunterladen des Updates
Sie können das Paket für das Azure Stack-Update 1807 [hier](https://aka.ms/azurestackupdatedownload) herunterladen.


## <a name="next-steps"></a>Nächste Schritte
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](../azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](../azure-stack-monitor-update.md).  
- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](../azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](../azure-stack-apply-updates.md).  