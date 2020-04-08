---
title: 'Azure Stack Hub: Versionshinweise'
description: Versionshinweise für integrierte Azure Stack Hub-Systeme, einschließlich Updates und Fehlerbehebungen.
author: sethmanheim
ms.topic: article
ms.date: 03/20/2020
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 33c620624feca5b2d416ff1173741209b99011cb
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402821"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack Hub: Versionshinweise

In diesem Artikel werden die Inhalte der Azure Stack Hub-Updatepakete beschrieben. Das Update enthält Verbesserungen und Fehlerbehebungen für die aktuelle Version von Azure Stack Hub.

Um auf Versionshinweise für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack Hub-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit (ASDK) an.
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

Unterstützung bei der Problembehandlung von Updates und dem Updateprozess finden Sie unter [Problembehandlung bei Patch- und Updateproblemen für Azure Stack Hub](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 2002 lautet **1.2002.0.35**.

> [!IMPORTANT]  
> Mit dem Azure Stack Hub 2002-Update erweitert Microsoft vorübergehend die [Azure Stack Hub-Supportrichtlinienanweisungen](azure-stack-servicing-policy.md).  Wir arbeiten weltweit mit Kunden zusammen, die auf COVID-19 reagieren. In diesem Zusammenhang müssen die Kunden ggf. wichtige Entscheidungen für ihre Azure Stack Hub-Systeme und deren Aktualisierung und Verwaltung treffen und daher sicherstellen, dass die Geschäftsabläufe in ihrem Rechenzentrum weiterhin normal erfolgen. Zur Unterstützung der Kunden bietet Microsoft eine temporäre Erweiterung zur Änderung der Supportrichtlinien an, die die drei letzten Updateversionen umfasst.  In diesem Rahmen werden das neu veröffentlichte Update 2002 sowie die drei letzten Updateversionen (z. B. 1910, 1908 und 1907) unterstützt.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack Hub 2002-Updates lautet **Vollständig**.

Das Paket des Updates 2002 ist größer als bei vorherigen Updates. Dies führt zu längeren Downloadzeiten. Das Update bleibt lange im Zustand **Wird vorbereitet**, und es ist damit zur rechnen, dass dieser Prozess länger dauert als bei vorherigen Updates. Das Update 2002 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 15 - 42 Stunden, 8 Knoten: 20 - 50 Stunden, 12 Knoten: 20 - 60 Stunden, 16 Knoten: 25 - 70 Stunden. Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die kürzer oder länger als erwartet sind, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Operator, sofern das Update nicht fehlschlägt. Diese geschätzte Laufzeit ist spezifisch für das Update 2002 und nicht auf andere Azure Stack Hub-Updates übertragbar.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->

- Es ist eine neue Version (1.8.1) von Azure Stack Hub-PowerShell-Modulen für Administratoren basierend auf AzureRM verfügbar.
- Im Azure Stack Hub-Administratorportal wurde eine neue Warnung hinzugefügt, um Konnektivitätsprobleme mit dem konfigurierten Syslog-Server zu melden. Der Titel der Warnung lautet **The Syslog client encountered a networking issue while sending a Syslog message** (Der Syslog-Client hat beim Senden einer Syslog-Nachricht ein Netzwerkproblem erkannt).
- Im Azure Stack Hub-Administratorportal wurde eine neue Warnung hinzugefügt, um Konnektivitätsprobleme mit dem NTP-Server (Network Time Protocol) zu melden. Der Titel der Warnung lautet **Invalid Time Source on [node name]** (Ungültige Zeitquelle auf [Knotenname]).
- Für das [Java-SDK](https://azure.microsoft.com/develop/java/) wurden aufgrund einer grundlegenden Änderung in Update 2002 in Bezug auf TLS-Einschränkungen neue Pakete veröffentlicht. Sie müssen die neue Java-SDK-Abhängigkeit installieren. Sie finden die Anleitung unter [Java und API-Versionsprofile](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles).
- Eine neue Version (1.0.5.10) von System Center Operations Manager – Azure Stack Hub MP ist verfügbar und ist aufgrund von grundlegenden API-Änderungen für alle Systeme erforderlich, auf denen 2002 ausgeführt wird. Die API-Änderungen wirken sich auf die Dashboards zur Sicherungs- und Speicherleistung aus. Wir empfehlen Ihnen, zuerst alle Systeme auf 2002 zu aktualisieren, bevor Sie das MP-Update durchführen.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Dieses Update enthält Änderungen des Updateprozesses, mit denen die Leistung der zukünftigen vollständigen Updates erheblich verbessert wird. Diese Änderungen werden mit dem nächsten vollständigen Update nach Release 2002 wirksam. Sie sind vor allem auf die Verbesserung der Phase ausgerichtet, in der ein vollständiges Update durchgeführt wird und die Hostbetriebssysteme aktualisiert werden. Die Verbesserung der Updateleistung für Hostbetriebssysteme führt zu einer erheblichen Reduzierung des Zeitfensters, in dem sich bei vollständigen Updates Beeinträchtigungen für Mandantenworkloads ergeben.
- Mit dem Azure Stack Hub Readiness Checker-Tool wird jetzt die AD Graph-Integration überprüft, indem alle TCP/IP-Ports verwendet werden, die AD Graph zugeordnet sind.
- Für das Offlinesyndikationstool wurden Verbesserungen in Bezug auf die Zuverlässigkeit durchgeführt. Das Tool ist nicht mehr auf GitHub verfügbar und wurde [in den PowerShell-Katalog verschoben](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). Weitere Informationen finden Sie unter [Herunterladen von Marketplace-Elementen in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).
- Eine neue Überwachungsfunktion wird eingeführt. Die Warnung bei wenig freiem Speicherplatz für physische Hosts und Infrastruktur-VMs wird von der Plattform automatisch bereinigt. Nur wenn bei dieser Aktion ein Fehler auftritt, wird die Warnung im Azure Stack Hub-Administratorportal angezeigt, damit der Operator entsprechende Maßnahmen ergreifen kann.
- Verbesserungen in Bezug auf die [Erfassung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview-tzl.md). Mit der neuen Umgebung wird die Erfassung von Diagnoseprotokollen optimiert und vereinfacht, indem die Anforderung zum Vorabkonfigurieren eines Blobspeicherkontos beseitigt wird. Die Speicherumgebung ist so vorkonfiguriert, dass Sie vor dem Erstellen einer Supportanfrage Protokolle senden können und so weniger Zeit für einen Anruf beim Support verloren geht.
- Die Zeit für die [proaktive und bedarfsabhängige Erfassung von Protokollen](azure-stack-diagnostic-log-collection-overview-tzl.md) konnte jeweils um 80 % reduziert werden. Die Protokollerfassung kann länger als erwartet dauern, aber von Azure Stack Hub-Operators muss keine Aktion durchgeführt werden, sofern die Protokollerfassung nicht fehlschlägt.
- Der Downloadstatus eines Azure Stack Hub-Updatepakets wird jetzt auf dem Updateblatt angezeigt, nachdem ein Update initiiert wurde. Dies gilt nur für verbundene Azure Stack Hub-Systeme, bei denen [Updatepakete per automatischem Download vorbereitet werden](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages).
- Verbesserungen in Bezug auf die Zuverlässigkeit für den Netzwerkcontroller-Host-Agent.
- Es wurde ein neuer Microservice mit dem Namen DNS Orchestrator eingeführt, mit dem die Resilienzlogik für die internen DNS-Dienste bei Patch- und Updatevorgängen verbessert wird.
- Es wurde eine neue Anforderungsüberprüfung hinzugefügt, damit beim Erstellen von VMs für ungültige Blob-URIs für den Parameters des Startdiagnose-Speicherkontos ggf. ein Fehler erkannt wird.
- Für Rdagent und den Host-Agent wurden Verbesserungen in Bezug auf die automatische Wiederherstellung und Protokollierung vorgenommen. Dies sind zwei Dienste auf dem Host, mit denen CRUD-Vorgänge für VMs durchgeführt werden.
- Der Marketplace-Verwaltung wurde ein neues Feature hinzugefügt, mit dem für Administratoren das Herunterladen von Marketplace-Produkten blockiert werden kann, die mit ihrem Azure Stack inkompatibel sind (aufgrund verschiedener Attribute, z. B. Azure Stack-Version oder Abrechnungsmodell).

### <a name="changes"></a>Änderungen

- Im Administratorportal wird mit einem Symbol neben der Azure Stack-Region jetzt angezeigt, ob ein Vorgang aktiv ist. Wenn Sie den Mauszeiger auf das Symbol bewegen, wird der Name des Vorgangs angezeigt. So können Sie ermitteln, welche Systemvorgänge im Hintergrund ausgeführt werden, z. B. ein Sicherungsauftrag oder eine Speichererweiterung. Diese Vorgänge können ggf. mehrere Stunden dauern.

- Die folgenden Administrator-APIs sind veraltet und wurden eingestellt:

  | Ressourcenanbieter       | Resource              | Version            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft.Storage.Admin | Bauernhöfe                 | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/acquisitions    | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/shares          | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/storageaccounts | 2015-12-01-preview |

- Die folgenden Administrator-APIs wurden durch eine neuere Version (2018-09-01) ersetzt:

  | Ressourcenanbieter      | Resource              | Version    |
  |------------------------|-----------------------|------------|
  | Microsoft.Backup.Admin | backupLocation         | 2016-05-01 |
  | Microsoft.Backup.Admin | backups                | 2016-05-01 |
  | Microsoft.Backup.Admin | Vorgänge             | 2016-05-01 |
  
### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Problem behoben, das zu Problemen mit der Internetverbindung führte, wenn mehrere öffentliche IP-Adressen für die gleiche NIC auf einem virtuellen Computer hinzugefügt wurden. Eine NIC mit zwei öffentlichen IP-Adressen funktioniert jetzt wie erwartet.
- Es wurde ein Problem behoben, bei dem eine Warnung mit dem Hinweis ausgelöst wurde, dass das Azure AD-Basisverzeichnis konfiguriert werden muss.
- Es wurde ein Problem behoben, bei dem eine Warnung nicht automatisch geschlossen wurde. Die Warnung hat den Hinweis enthalten, dass das Azure AD-Basisverzeichnis konfiguriert werden muss, aber sie wurde nach dem Beheben des Problems nicht geschlossen.
- Es wurde ein Problem behoben, bei dem für Updates während der Vorbereitungsphase ein Fehler aufgetreten ist, weil es zu internen Fehlern beim Updateressourcenanbieter gekommen ist.
- Es wurde ein Problem behoben, bei dem für Vorgänge des Add-On-Ressourcenanbieters ein Fehler aufgetreten ist, nachdem die Azure Stack Hub-Geheimnisrotation durchgeführt wurde.
- Es wurde ein Problem behoben, das aufgrund einer hohen Arbeitsspeicherauslastung für die ERCS-Rolle eine häufige Ursache von Azure Stack Hub-Updatefehlern war.
- Es wurde ein Fehler auf dem Updateblatt behoben, bei dem der Updatestatus während der Vorbereitungsphase eines Azure Stack Hub-Updates nicht als **Wird vorbereitet**, sondern als **Wird installiert** angezeigt wurde.
- Es wurde ein Problem behoben, bei dem das RSC-Feature auf den physischen Switches zu Inkonsistenzen geführt hat und der Datenverkehr verworfen wurde, der über ein Lastenausgleichsmodul geflossen ist. Das RSC-Feature ist jetzt standardmäßig deaktiviert.
- Es wurde ein Problem behoben, bei dem das Hinzufügen einer sekundären IP-Adresse zur VM zu RDP-Problemen geführt hat.
- Es wurde ein Problem behoben, bei dem die MAC-Adresse einer NIC zwischengespeichert wurde und die Zuweisung dieser Adresse zu einer anderen Ressource zu VM-Bereitstellungsfehlern geführt hat.
- Es wurde ein Problem behoben, bei dem für Windows-VM-Images aus dem RETAIL-Kanal die Lizenz per AVMA nicht aktiviert werden konnte.
- Es wurde ein Problem behoben, bei dem VMs nicht erstellt werden konnten, wenn die Anzahl von virtuellen Kernen, die von der VM angefordert wurden, der Anzahl von physischen Kernen des Knoten entsprochen hat. Es ist jetzt zulässig, dass VMs über eine Anzahl von virtuellen Kernen verfügen, die kleiner oder gleich der Anzahl von physischen Kernen des Knotens ist.
- Es wurde ein Problem behoben, bei dem es nicht zugelassen wurde, dass der Lizenztyp auf „Null“ festgelegt wird, um Images mit nutzungsbasierter Bezahlung auf BYOL umzustellen.
- Es wurde ein Problem behoben, damit Erweiterungen einer VM-Skalierungsgruppe hinzugefügt werden können.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack Hub-Update 2002 auf der [Azure Stack Hub-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack Hub-Hotfix für 1910, bevor Sie Azure Stack Hub auf 2002 aktualisieren.

> [!NOTE]
> Hotfixreleases für Azure Stack Hub sind kumulativ. Sie müssen also nur den neuesten Hotfix installieren, um alle Korrekturen zu erhalten, die in den vorherigen Hotfixreleases für diese Version enthalten waren.

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-2002-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 2002

Das Release 2002 von Azure Stack Hub muss auf das Release 1910 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1910.24.108](https://support.microsoft.com/help/4541350)

### <a name="after-successfully-applying-the-2002-update"></a>Nach erfolgreicher Anwendung des Updates 2002

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Es ist kein Azure Stack Hub-Hotfix für 2002 verfügbar.
::: moniker-end

::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 1910 lautet **1.1910.0.58**.

### <a name="update-type"></a>Updatetyp

Ab Release 1908 wurde das zugrunde liegende Betriebssystem, unter dem Azure Stack Hub ausgeführt wird, auf Windows Server 2019 aktualisiert. Dieses Update ermöglicht wichtige grundlegende Verbesserungen und die Option, Azure Stack Hub um weitere Funktionen zu erweitern.

Beim Azure Stack Hub-Update 1910 handelt es sich um einen Build vom Typ **Express**.

Das Paket des Updates 1910 ist größer als bei vorherigen Updates. Der Download dauert dadurch länger. Das Update bleibt lange im Zustand **Wird vorbereitet**, und es ist damit zu rechnen, dass dieser Prozess länger dauert als bei vorherigen Updates. Das Update 1910 dauert etwa zehn Stunden – unabhängig von der Anzahl physischer Knoten in Ihrer Azure Stack Hub-Umgebung. Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die länger als der erwartete Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Betreiber, es sei denn, das Update schlägt fehl. Diese geschätzte Laufzeit ist spezifisch für das Update 1910 und nicht auf andere Azure Stack Hub-Updates übertragbar.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->

- Im Administratorportal werden nun die IP-Adressen des privilegierten Endpunkts im Menü mit den Regionseigenschaften angezeigt, um die Ermittlung zu vereinfachen. Darüber hinaus werden der derzeit konfigurierte Zeitserver sowie DNS-Weiterleitungen angezeigt. Weitere Informationen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](azure-stack-privileged-endpoint.md).

- Das Integritäts- und Überwachungssystem von Azure Stack Hub kann nun im Falle eines Fehlers Warnungen für verschiedene Hardwarekomponenten auslösen. Für diese Warnungen ist möglicherweise eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [Überwachen von Azure Stack Hub-Hardwarekomponenten](azure-stack-hardware-monitoring.md).

- [cloud-init-Unterstützung für Azure Stack Hub](/azure/virtual-machines/linux/using-cloud-init): cloud-init ist eine gängige Methode für die Anpassung eines virtuellen Linux-Computers beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs aufgerufen wird, müssen Sie keine zusätzlichen Schritte oder Agents auf Ihre Konfiguration anwenden. Die Ubuntu-Images im Marketplace wurden aktualisiert, um cloud-init für die Bereitstellung zu unterstützen.

- Azure Stack Hub unterstützt jetzt alle Microsoft Azure Linux-Agents als Azure.

- Es ist eine neue Version von Azure Stack Hub-PowerShell-Modulen für Administratoren verfügbar. <!-- For more information, see -->

- Das Cmdlet **Set-AzSDefenderManualUpdate** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die manuelle Aktualisierung für Windows Defender-Definitionen in der Azure Stack Hub-Infrastruktur zu konfigurieren. Weitere Informationen finden Sie unter [Aktualisieren von Windows Defender Antivirus in Azure Stack Hub](azure-stack-security-av.md).

- Das Cmdlet **Get-AzSDefenderManualUpdate** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Konfiguration der manuellen Aktualisierung für Windows Defender-Definitionen in der Azure Stack Hub-Infrastruktur abzurufen. Weitere Informationen finden Sie unter [Aktualisieren von Windows Defender Antivirus in Azure Stack Hub](azure-stack-security-av.md).

- Das Cmdlet **Set-AzSDnsForwarder** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Weiterleitungseinstellungen der DNS-Server in Azure Stack Hub zu ändern. Weitere Informationen finden Sie unter [DNS-Integration in Azure Stack Hub-Datencenter](azure-stack-integrate-dns.md).

- Das Cmdlet **Get-AzSDnsForwarder** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Weiterleitungseinstellungen der DNS-Server in Azure Stack Hub abzurufen. Weitere Informationen finden Sie unter [DNS-Integration in Azure Stack Hub-Datencenter](azure-stack-integrate-dns.md).

- Die Unterstützung der Verwaltung von **Kubernetes-Clustern** per [AKS-Engine](../user/azure-stack-kubernetes-aks-engine-overview.md) wurde hinzugefügt. Ab diesem Update können Kunden Kubernetes-Produktionscluster bereitstellen. Die AKS-Engine ermöglicht Benutzern Folgendes:
  - Verwalten des Lebenszyklus ihrer Kubernetes-Cluster. Sie können Cluster erstellen, aktualisieren und skalieren.
  - Verwalten ihrer Cluster mithilfe verwalteter Images, die von den AKS- und Azure Stack Hub-Teams erstellt wurden.
  - Nutzen eines Kubernetes-Cloudanbieters mit Azure Resource Manager-Integration, der Cluster mit nativen Azure-Ressourcen erstellt
  - Bereitstellen und Verwalten ihrer Cluster in verbundenen oder getrennten Azure Stack Hub-Stempeln
  - Verwenden von Azure-Hybridfeatures:
    - Integration mit Azure Arc
    - Integration mit Azure Monitor für Container
  - Verwenden von Windows-Containern mit AKS-Engine
  - Erhalten von CSS- und Engineering-Support für ihre Bereitstellungen

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- In Azure Stack Hub wurde die Funktion für die automatische Behebung von Patch- und Updateproblemen verbessert, die zuvor Updatefehler verursachten oder verhinderten, dass Betreiber ein Azure Stack Hub-Update initiieren konnten. Daher sind in der Gruppe **Test-AzureStack -UpdateReadiness** weniger Tests enthalten. Weitere Informationen finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md#groups). Die folgenden drei Tests bleiben in der Gruppe **UpdateReadiness**:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Es wurde eine Überwachungsregel hinzugefügt, um eine Meldung zu generieren, wenn ein externes Gerät (beispielsweise ein USB-Schlüssel) in einen Knoten der Azure Stack Hub-Infrastruktur eingebunden wird. Das Überwachungsprotokoll wird über Syslog ausgegeben und als **Microsoft-Windows-Security-Auditing: 6416|Plug & Play-Ereignisse** angezeigt. Weitere Informationen zum Konfigurieren des Syslog-Clients finden Sie unter [Integrieren von Azure Stack in Überwachungslösungen mithilfe der Syslog-Weiterleitung](azure-stack-integrate-security.md).

- Azure Stack Hub wird für die internen Zertifikate auf RSA-Schlüssel mit 4096 Bit umgestellt. Im Zuge der internen Geheimnisrotation werden alte 2048-Bit-Zertifikate durch Zertifikate mit einer Länge von 4096 Bit ersetzt. Weitere Informationen zur Geheimnisrotation in Azure Stack Hub finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md).

- Bei mehreren internen Komponenten wurde ein Upgrade der Komplexität von Kryptografiealgorithmen und der Schlüsselstärke durchgeführt, um der Richtlinie 15 des Ausschusses für nationale Sicherheitssysteme (Committee on National Security Systems – Policy 15, CNSSP-15) zu entsprechen, die Best Practices für die Verwendung öffentlicher Standards für einen sicheren Informationsaustausch enthält. Die Verbesserungen umfassen unter anderem AES256 für die Kerberos-Authentifizierung und SHA384 für die VPN-Verschlüsselung. Weitere Informationen zu CNSSP-15 finden Sie auf der [Richtlinienseite](http://www.cnss.gov/CNSS/issuances/Policies.cfm) des Ausschusses für nationale Sicherheitssysteme.

- Aufgrund des obigen Upgrades verfügt Azure Stack Hub nun über neue Standardwerte für IPsec-/IKEv2-Konfigurationen. Die neuen Standardwerte aufseiten von Azure Stack Hub lauten wie folgt:

   **Parameter der IKE-Phase 1 (Hauptmodus)**

   | Eigenschaft              | Wert|
   |-|-|
   | IKE-Version           | IKEv2 |
   |Diffie-Hellman-Gruppe   | ECP384 |
   | Authentifizierungsmethode | Vorinstallierter Schlüssel |
   |Verschlüsselung und Hashalgorithmen | AES256, SHA384 |
   |SA-Gültigkeitsdauer (Zeit)     | 28.800 Sekunden|

   **Parameter der IKE-Phase 2 (Schnellmodus)**

   | Eigenschaft| Wert|
   |-|-|
   |IKE-Version |IKEv2 |
   |Verschlüsselung und Hashalgorithmen (Verschlüsselung)     | GCMAES256|
   |Verschlüsselung und Hashalgorithmen (Authentifizierung) | GCMAES256|
   |SA-Gültigkeitsdauer (Zeit)  | 27.000 Sekunden  |
   |SA-Gültigkeitsdauer (KB) | 33.553.408     |
   |Perfect Forward Secrecy (PFS) | ECP384 |
   |Dead Peer Detection | Unterstützt|

   Diese Änderungen wurden auch in der Dokumentation der [IPsec-/IKE-Parameter](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) berücksichtigt.

- Der Dienst für die Infrastruktursicherung verbessert die Logik, die den gewünschten freien Speicherplatz für Sicherungen berechnet, anstatt einen festen Schwellenwert zu verwenden. Der Dienst ermittelt anhand der Größe einer Sicherung, der Aufbewahrungsrichtlinie, der Reserve und der aktuellen Auslastung des externen Speicherorts, ob eine Warnung für den Betreiber ausgelöst werden muss.

### <a name="changes"></a>Änderungen

- Beim Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub gibt es eine neue Benutzeroberfläche, über die Sie eine Version des Elements angeben können, wenn mehrere Versionen vorhanden sind. Die neue Benutzeroberfläche steht in Szenarien mit und ohne Verbindung zur Verfügung. Weitere Informationen finden Sie unter [Herunterladen vorhandener Marketplace-Elemente aus Azure und Veröffentlichen in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).  

- Ab dem Release 1910 **benötigt** das Azure Stack Hub-System einen zusätzlichen privaten internen IP-Adressraum der Größe „/20“.  Weitere Informationen finden Sie unter (Planen der Netzwerkintegration für Azure Stack) [azure-stack-network.md].
  
- Der Dienst für die Infrastruktursicherung löscht unvollständig hochgeladene Sicherungsdaten, wenn die Kapazität des externen Speicherorts während des Uploadvorgangs nicht ausreicht.  

- Der Dienst für die Infrastruktursicherung fügt der Sicherungsnutzlast für AAD-Bereitstellungen den Identitätsdienst hinzu.  

- Das Azure Stack Hub-PowerShell-Modul wurde für das Release 1910 auf die Version 1.8.0 aktualisiert.<br>Die Änderungen umfassen Folgendes:
   - **Neues DRP-Administratormodul:** Der Bereitstellungsressourcenanbieter (Deployment Resource Provider, DRP) ermöglicht orchestrierte Bereitstellungen von Ressourcenanbietern in Azure Stack Hub. Diese Befehle interagieren mit der Azure Resource Manager-Ebene, um mit DRP zu interagieren.
   - **BRP:** <br />
           - Unterstützung der Wiederherstellung einzelner Rollen für die Sicherung der Azure Stack-Infrastruktur <br />
           - Hinzufügung des Parameters `RoleName` zum Cmdlet `Restore-AzsBackup`
   - **FRP:** Breaking Changes für Ressourcen vom Typ **Laufwerk** und **Volume** mit der API-Version `2019-05-01`. Die Features werden ab Azure Stack Hub 1910 unterstützt: <br />
            - Der Wert von `ID`, `Name`, `HealthStatus` und `OperationalStatus` wurde geändert. <br />
            - Unterstützte neue Eigenschaften `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer` und `StoragePool` für Ressourcen vom Typ **Laufwerk** <br />
            - Die Eigenschaften `CanPool` und `CannotPoolReason` von Ressourcen des Typs **Laufwerk** sind veraltet. Verwenden Sie stattdessen `OperationalStatus`.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Es wurde ein Problem behoben, das die Erzwingung der TLS 1.2-Richtlinie in Umgebungen verhindert hat, die vor dem Azure Stack Hub-Release 1904 bereitgestellt wurden.
- Es wurde ein Problem behoben, das dazu führte, dass ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, keine Verwendung der SSH-Schlüssel für die Anmeldung zuließ.
- **Kennwort zurücksetzen** wurde von der Benutzeroberfläche für VM-Skalierungsgruppen entfernt.
- Es wurde ein Problem behoben, das dazu führte, dass beim Löschen des Lastenausgleichs im Portal das Objekt auf der Infrastrukturebene nicht gelöscht wurde.
- Es wurde ein Problem behoben, durch das in der Warnung für die Gatewaypoolauslastung im Administratorportal ein falscher Prozentsatz angezeigt wurde.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack Hub-Update 1910 auf der [Azure Stack Hub-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack Hub-Hotfix für 1908, bevor Sie Azure Stack Hub auf 1910 aktualisieren.

> [!NOTE]
> Hotfixreleases für Azure Stack Hub sind kumulativ. Sie müssen also nur den neuesten Hotfix installieren, um alle Korrekturen zu erhalten, die in den vorherigen Hotfixreleases für diese Version enthalten waren.

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1910-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1910

Das Release 1910 von Azure Stack Hub muss auf das Release 1908 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1908.19.62](https://support.microsoft.com/help/4541349)

### <a name="after-successfully-applying-the-1910-update"></a>Nach erfolgreicher Anwendung des Updates 1910

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1910.24.108](https://support.microsoft.com/help/4541350)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 1908 lautet **1.1908.4.33**.

### <a name="update-type"></a>Updatetyp

Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack Hub ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. Dieses Update ermöglicht wichtige grundlegende Verbesserungen und die Option, Azure Stack Hub um weitere Funktionen zu erweitern.

Der Azure Stack Hub 1908-Update-Buildtyp lautet **Vollständig**. Daher hat das Update 1908 eine längere Laufzeit als Express-Updates wie 1906 und 1907. Genaue Laufzeiten für vollständige Updates hängen in der Regel von der Anzahl der Knoten ab, die Ihre Azure Stack Hub-Instanz enthält, der auf Ihrem System von Mandantenworkloads verbrauchten Kapazität, der Netzwerkkonnektivität Ihres Systems (falls es mit dem Internet verbunden ist) und Ihrer Hardwarekonfiguration. Das Update 1908 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 42 Stunden, 8 Knoten: 50 Stunden, 12 Knoten: 60 Stunden, 16 Knoten: 70 Stunden. Updatelaufzeiten, die länger als diese erwarteten Werte dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen seitens des Azure Stack Hub-Bedieners, sofern es für das Update nicht zu einem Fehler kommt.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md).

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als erwartet dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Bediener, es sei denn, das Update schlägt fehl.
- Diese geschätzte Laufzeit ist spezifisch für das Update 1908 und nicht auf andere Azure Stack Hub-Updates übertragbar.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->

- Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack Hub ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. Dieses Update ermöglicht wichtige grundlegende Verbesserungen und die Option, Azure Stack Hub um weitere Funktionen zu erweitern.
- Für alle Komponenten der Azure Stack Hub-Infrastruktur wird jetzt der FIPS 140-2-Modus verwendet.
- Azure Stack Hub-Bediener können jetzt Portalbenutzerdaten entfernen. Weitere Informationen finden Sie unter [Löschen von Portalbenutzerdaten aus Azure Stack Hub](azure-stack-portal-clear.md).

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Es wurden Verbesserungen an der Azure Stack Hub-Verschlüsselung von ruhenden Daten vorgenommen, um Geheimnisse dauerhaft im Hardware-TPM (Trusted Platform Module) der physischen Knoten zu speichern.

### <a name="changes"></a>Änderungen

- Hardwareanbieter veröffentlichen das OEM-Erweiterungspaket 2.1 oder höher zur gleichen Zeit wie Azure Stack Hub, Version 1908. Das OEM-Erweiterungspaket 2.1 oder höher ist eine Voraussetzung für Version 1908 von Azure Stack Hub. Weitere Informationen zum Herunterladen des OEM-Erweiterungspakets 2.1 oder höher erhalten Sie von Hardwareanbieter Ihres Systems und im Artikel zu [OEM-Updates](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Fehlerbehebungen

- Es wurden ein Problem mit der Kompatibilität zukünftiger Azure Stack Hub-OEM-Updates und ein Problem mit der VM-Bereitstellung bei Verwendung von Kundenbenutzerimages behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) behoben.  
- Es wurde ein Problem mit einem OEM-Firmwareupdate behoben und in Test-AzureStack eine Fehldiagnose zur Integrität des Fabric-Rings korrigiert. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) behoben.
- Es wurde ein Problem mit dem Prozess für OEM-Firmwareupdates behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) behoben.

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack Hub-Update 1908 auf der [Azure Stack Hub-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack Hub-Hotfix für 1907, bevor Sie Azure Stack Hub auf 1908 aktualisieren.

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes für das ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1908-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1908

Das Release 1908 von Azure Stack Hub muss auf das Release 1907 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1907.26.70](https://support.microsoft.com/help/4541348)

Für das Azure Stack Hub-Update 1908 ist **Azure Stack Hub-OEM-Version 2.1 oder höher** vom Hardwareanbieter Ihres Systems erforderlich. OEM-Updates enthalten Treiber- und Firmwareupdates Ihrer Azure Stack Hub-Systemhardware. Weitere Informationen zum Anwenden von OEM-Updates finden Sie unter [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub](azure-stack-update-oem.md).

### <a name="after-successfully-applying-the-1908-update"></a>Nach erfolgreicher Anwendung des Updates 1908

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1908.19.62](https://support.microsoft.com/help/4541349)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 1907 lautet **1.1907.0.20**.

### <a name="update-type"></a>Updatetyp

Beim Azure Stack Hub-Update 1907 handelt es sich um einen Build vom Typ **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md). Auf der Grundlage interner Tests lässt sich sagen, dass das Update 1907 etwa 13 Stunden dauert.

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als erwartet dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Bediener, es sei denn, das Update schlägt fehl.
- Diese geschätzte Laufzeit ist spezifisch für das Update 1907 und nicht auf andere Azure Stack Hub-Updates übertragbar.

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->

- Allgemein verfügbare Version des Azure Stack Hub-Sammlungsdiensts für Diagnoseprotokolle, um die Sammlung von Diagnoseprotokollen zu vereinfachen und zu verbessern. Der Azure Stack Hub-Sammlungsdienst für Diagnoseprotokolle stellt ein vereinfachtes Verfahren zum Sammeln und Teilen von Diagnoseprotokollen mit Microsoft Customer Support Services (CSS) zur Verfügung. Dieser Sammlungsdienst für Diagnoseprotokolle bietet eine neue Benutzeroberfläche im Azure Stack Hub-Administratorportal, über die Operatoren beim Erreichen bestimmter kritischer Warnschwellen den automatischen Upload von Diagnoseprotokollen in einen Speicherblob einrichten können. Der Dienst kann auch dazu verwendet werden, den gleichen Vorgang bedarfsgesteuert auszuführen. Weitere Informationen finden Sie im Artikel [Sammlung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview.md).

- Allgemein verfügbare Version der Azure Stack Hub-Netzwerkinfrastruktur-Überprüfung als Teil des Azure Stack Hub-Überprüfungstools **Test-AzureStack**. Die Azure Stack Hub-Netzwerkinfrastruktur stellt einen Teil von **Test-AzureStack** dar und ermöglicht das Bestimmen eines Fehlers in der Netzwerkinfrastruktur von Azure Stack Hub. Der Test überprüft die Konnektivität der Netzwerkinfrastruktur, indem er das softwaredefinierte Netzwerk von Azure Stack Hub umgeht. Er veranschaulicht Verbindungen von einer öffentlichen VIP mit den konfigurierten DNS-Forwardern, NTP-Servern und Identitätsendpunkten. Außerdem überprüft er die Konnektivität mit Azure, wenn Azure AD als Identitätsanbieter verwendet wird, oder mit dem Partnerserver, wenn ADFS verwendet wird. Weitere Informationen finden Sie im Artikel zum [Azure Stack Hub-Überprüfungstool](azure-stack-diagnostic-test.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne SQL-TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Auf dem Azure Stack Hub-Updateblatt wird jetzt eine Zeit für den **Letzten abgeschlossenen Schritt** für aktive Updates angezeigt. Diese kann angezeigt werden, indem auf dem Updateblatt auf ein laufendes Update geklickt wird. Der **Letzte abgeschlossene Schritt** ist dann im Abschnitt **Update-Ausführungsdetails** verfügbar.

- Verbesserungen an den Operatoraktionen **Start-AzureStack** und **Stop-AzureStack**. Die für den Start von Azure Stack Hub erforderliche Zeit wurde um durchschnittlich 50 % reduziert. Die für das Herunterfahren von Azure Stack Hub erforderliche Zeit wurde um durchschnittlich 30 % reduziert. Die durchschnittlichen Zeiten für Start und Herunterfahren bleiben gleich, wenn sich die Anzahl der Knoten in einer Skalierungseinheit erhöht.

- Verbesserte Fehlerbehandlung für das Marketplace-Tool bei getrennter Verbindung. Bei einem Downloadfehler oder einem Teilerfolg wird bei der Verwendung von **Export-AzSOfflineMarketplaceItem** eine detaillierte Fehlermeldung mit weiteren Details zum Fehler und möglichen Schritten zur Entschärfung angezeigt.

- Verbesserte Leistung bei der Erstellung verwalteter Datenträger aus einem großen Seitenblob/einer großen Momentaufnahme. Bisher wurde beim Erstellen eines großen Datenträgers ein Zeitlimit ausgelöst.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Verbesserte Integritätsprüfung von virtuellen Datenträgern vor dem Herunterfahren eines Knotens, um ein unerwartetes Trennen virtueller Datenträger zu verhindern.

- Verbesserte Speicherung interner Protokolle für Administratorvorgänge. Dies führt zu einer verbesserten Leistung und Zuverlässigkeit bei Administratorvorgängen, indem die Arbeitsspeicher- und Speicherplatznutzung durch interne Protokollprozesse minimiert wird. Möglicherweise bemerken Sie darüber hinaus verbesserte Seitenladezeiten des Updateblatts im Verwaltungsportal. Im Rahmen dieser Verbesserung stehen Updateprotokolle, die älter als sechs Monate sind, im System nicht mehr zur Verfügung. Wenn Sie Protokolle für diese Updates benötigen, achten Sie darauf, dass Sie für alle Updateausführungen, die älter als sechs Monate sind, [die Zusammenfassung herunterladen](azure-stack-apply-updates.md), bevor Sie das Update 1907 durchführen.

### <a name="changes"></a>Änderungen

- Version 1907 von Azure Stack Hub enthält eine Warnmeldung, in der Operatoren darauf hingewiesen werden, dass sie das OEM-Paket des Systems auf Version 2.1 oder höher aktualisieren müssen, bevor sie ein Update auf Version 1908 ausführen können. Weitere Informationen zum Anwenden von Azure Stack Hub-OEM-Updates finden Sie unter [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub](azure-stack-update-oem.md).

- Es wurde eine neue Ausgangsregel (HTTPS) hinzugefügt, um dem Azure Stack Hub-Sammlungsdienst für Diagnoseprotokolle die Kommunikation zu ermöglichen. Weitere Informationen finden Sie unter [Integration des Azure Stack Hub-Rechenzentrums – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Der Infrastruktur-Sicherungsdienst löscht jetzt teilweise hochgeladene Sicherungen, wenn der externe Speicherort an seine Kapazitätsgrenze stößt.

- Infrastruktursicherungen enthalten keine Sicherung von Domänendienstdaten mehr. Diese Änderung betrifft nur Systeme, die Azure Active Directory als Identitätsanbieter verwenden.

- Wir überprüfen jetzt, ob ein Image, das auf dem Blatt **Compute -> VM-Images** erfasst wird, den Typ Seitenblob aufweist.

- Der Befehl **Set-BmcCredential** des privilegierten Endpunkts aktualisiert nun die Anmeldeinformationen im Baseboard-Verwaltungscontroller.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->
- Es wurde ein Problem behoben, bei dem für Herausgeber, Angebote und SKUs in Resource Manager-Vorlagen Groß-/Kleinschreibung unterschieden wurde: Das Image wurde nicht für die Bereitstellung abgerufen, wenn die Imageparameter nicht die gleiche Groß-/Kleinschreibung wie Herausgeber, Angebot und SKU aufwiesen.

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

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack Hub-Update 1907 auf der [Azure Stack Hub-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack Hub-Hotfix für 1906, bevor Sie Azure Stack Hub auf 1907 aktualisieren.

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes für das ASDK zu installieren.

### <a name="before-applying-the-1907-update"></a>Vor dem Anwenden des Updates 1907

Das Release 1907 von Azure Stack Hub muss auf das Release 1906 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Nach erfolgreicher Anwendung des Updates 1907

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1907.26.70](https://support.microsoft.com/help/4541348)
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Systeme, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack Hub-Hotfixes auf dem neuesten Stand zu bleiben:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivieren

Um auf archivierte Versionshinweise für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack Hub finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack Hub finden Sie unter [Anwenden von Updates in Azure Stack Hub](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für Azure Stack Hub und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack Hub mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 – archivierte Versionshinweise
::: moniker-end
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

::: moniker range="<azs-1907"
Sie können auf [ältere Versionen der Azure Stack Hub-Versionshinweise im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack Hub-Support finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end


