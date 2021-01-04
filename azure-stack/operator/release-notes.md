---
title: 'Azure Stack Hub: Versionshinweise'
description: Versionshinweise für integrierte Azure Stack Hub-Systeme, einschließlich Updates und Fehlerbehebungen.
author: sethmanheim
ms.topic: article
ms.date: 12/11/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: c94b91d2144248dd154da5b630b99470461dda67
ms.sourcegitcommit: 56d7da84672261e2490138011bd8dd4eaf81851d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97492771"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack Hub: Versionshinweise

In diesem Artikel werden die Inhalte der Azure Stack Hub-Updatepakete beschrieben. Das Update enthält Verbesserungen und Fehlerbehebungen für die aktuelle Version von Azure Stack Hub.

Um auf Versionshinweise für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack Hub-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit (ASDK) an.
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)
- [Bekannte Probleme](known-issues.md)
- [Hotfixes](#hotfixes)
- [Sicherheitsupdates](release-notes-security-updates.md)

Unterstützung bei der Problembehandlung von Updates und dem Updateprozess finden Sie unter [Problembehandlung bei Patch- und Updateproblemen für Azure Stack Hub](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Azure Stack Hub-Updatepaket mit dem [Downloadprogramm für das Azure Stack Hub-Update](https://aka.ms/azurestackupdatedownload) herunterladen.

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2008"
## <a name="2008-build-reference"></a>2008 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 2008 lautet **1.2008.13.88**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack Hub-Updates 2008 lautet **Vollständig**.

Das Paket des Updates 2008 ist größer als vorherige Updates. Dies führt zu längeren Downloadzeiten. Das Update bleibt lange im Zustand **Wird vorbereitet**, und es ist damit zur rechnen, dass dieser Prozess länger dauert als bei vorherigen Updates. Das Update 2008 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 13 – 20 Stunden, 8 Knoten: 16 – 26 Stunden, 12 Knoten: 19 – 32 Stunden, 16 Knoten: 22 – 38 Stunden Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die kürzer oder länger als erwartet sind, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Operator, sofern das Update nicht fehlschlägt. Diese geschätzte Laufzeit ist spezifisch für das Update 2008 und nicht auf andere Azure Stack Hub-Updates übertragbar.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->
- Azure Stack Hub unterstützt jetzt VNET-Peering, mit dem VNETs verbunden werden können, ohne dass ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) vorhanden ist. Weitere Informationen finden Sie in der [neuen VNET-Peering-Dokumentation](../user/virtual-network-peering.md).
- Azure Stack Hub-Blobspeicher ermöglicht es Benutzern jetzt, ein unveränderliches Blob zu verwenden. Durch Festlegen von unveränderlichen Richtlinien für einen Container können Sie unternehmenskritische Datenobjekte in einem WORM-Zustand (Write Once, Read Many) speichern. In diesem Release können unveränderliche Richtlinien nur über die REST-API oder Client-SDKs festgelegt werden. Anfügeblob-Schreibvorgänge sind in diesem Release ebenfalls nicht möglich. Weitere Informationen zu unveränderlichen Blobs finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](/azure/storage/blobs/storage-blob-immutable-storage).
- Azure Stack Hub-Speicher unterstützt jetzt Version **2019-07-07** der Azure Storage-Dienste-APIs. Informationen zu den Azure-Clientbibliotheken, die mit der neuen REST-API-Version kompatibel sind, finden Sie unter [Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools](../user/azure-stack-storage-dev.md#azure-client-libraries). Die verfügbaren Features der Verwaltungs-APIs für Azure Storage-Dienste, Version **2018-02-01**, werden jetzt zum Teil unterstützt.
- Azure Stack Hub-Compute unterstützt jetzt Azure Compute-APIs, Version **2020-06-01**, mit einer Teilmenge der insgesamt verfügbaren Features.
- Verwaltete Azure Stack Hub-Datenträger unterstützen jetzt Azure Disk-APIs, Version **2019-03-01**, mit einer Teilmenge der verfügbaren Features.
- Vorschau von Windows Admin Center, das nun eine Verbindung mit Azure Stack Hub herstellen kann, um detaillierte Einblicke in die Infrastruktur während Supportvorgängen zu erhalten (Notfallzugriff erforderlich).
- Die Möglichkeit, zum Zeitpunkt der Bereitstellung ein Anmeldebanner zum privilegierten Endpunkt (PEP) hinzuzufügen.
- Es wurden weitere Banner für **Exklusive Vorgänge** veröffentlicht, die die Sichtbarkeit von Vorgängen verbessern, die derzeit auf dem System ausgeführt werden, und Benutzer daran hindern, irgendeinen anderen exklusiven Vorgang zu initiieren (und spätere Fehler zu verursachen).
- Auf jeder Produktseite des Marketplace-Elements für Azure Stack Hub wurden zwei neue Banner eingeführt. Gibt es einen Marketplace-Downloadfehler, können Operatoren Fehlerdetails anzeigen und empfohlene Schritte zum Beheben des Problems ausführen.
- Wir haben ein Bewertungstool für Kunden veröffentlicht, damit sie Feedback geben können. Dadurch wird das Azure Stack Hub-Team in die Lage versetzt, die Benutzerfreundlichkeit zu messen und zu optimieren.
- Dieses Release von Azure Stack Hub umfasst eine private Vorschau von Azure Kubernetes Service (AKS) und Azure Container Registry (ACR). Der Zweck der privaten Vorschau besteht darin, Feedback zur Qualität, zu den Features und zur Benutzerfreundlichkeit von AKS und ACR in Azure Stack Hub zu erfassen.
- Dieses Release enthält eine öffentliche Vorschau von Azure CNI und Windows-Containern mit [AKS Engine v0.55.4](../user/kubernetes-aks-engine-release-notes.md). Ein Beispiel dazu, wie Sie diese in Ihrem API-Modell verwenden können, [finden Sie in diesem Beispiel auf GitHub](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-windows.json).
- [Istio 1.3-Bereitstellung](https://github.com/Azure/aks-engine/tree/master/examples/service-mesh) in Clustern, die von [AKS Engine v0.55.4](../user/kubernetes-aks-engine-release-notes.md) bereitgestellt werden, wird nun unterstützt. Weitere Informationen [finden Sie in den Anweisungen hier](../user/kubernetes-aks-engine-service-account.md).
- Die Bereitstellung von [privaten Clustern](https://github.com/Azure/aks-engine/blob/master/docs/topics/features.md#private-cluster) mit [AKS Engine v0.55.4](../user/kubernetes-aks-engine-release-notes.md) wird jetzt unterstützt.
- Dieses Release bietet Unterstützung für die [Beschaffung von Kubernetes-Konfigurationsgeheimnissen](https://github.com/Azure/aks-engine/blob/master/docs/topics/keyvault-secrets.md#use-key-vault-as-the-source-of-cluster-configuration-secrets) aus Azure- und Azure Stack Hub-Key Vault-Instanzen.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Es wurde eine interne Überwachung für den Netzwerkcontroller und SLB-Host-Agents implementiert, sodass Dienste automatisch wiederhergestellt werden, sofern sie in den Zustand „Angehalten“ gewechselt haben.
- Active Directory-Verbunddienste (AD FS) ruft nun das neue Tokensignaturzertifikat ab, nachdem der Kunde es auf seinem eigenen AD FS-Server geändert hat. Um diese neue Funktionalität für bereits konfigurierte Systeme nutzen zu können, muss die AD FS-Integration erneut konfiguriert werden. Weitere Informationen finden Sie unter [Integrieren der AD FS-Identität in Ihr Azure Stack Hub-Rechenzentrum](azure-stack-integrate-identity.md).
- Änderungen am Start- und Herunterfahren-Prozess von Infrastrukturrolleninstanzen und deren Abhängigkeiten auf Skalierungseinheitknoten. Dies erhöht die Zuverlässigkeit für Starten und Beenden von Azure Stack Hub.
- Die **AzSScenarios**-Suite des **Test-AzureStack**-Validierungstools wurde aktualisiert, damit Cloud-Dienstanbieter diese Suite erfolgreich ausführen können, wenn mehrstufige Authentifizierung für alle Kundenkonten erzwungen wird.
- Verbesserte Warnungszuverlässigkeit durch Hinzufügen von Unterdrückungslogik für 29 kundenbezogene Warnungen während Lebenszyklusvorgängen.
- Sie können jetzt einen ausführlichen HTML-Bericht der Protokollsammlung anzeigen, der Details zu den Rollen, der Dauer und dem Status der Protokollsammlung enthält. Dieser Bericht soll Benutzern dabei helfen, eine Zusammenfassung der gesammelten Protokolle bereitzustellen. Der Microsoft-Kundendienst kann den Bericht dann schnell auswerten, um die Protokolldaten zu evaluieren und bei der Behebung und Minimierung von Systemproblemen zu helfen.
- Die Abdeckung zur Fehlererkennung für die Infrastruktur wurde durch Hinzufügen von sieben neuen Monitoren für Benutzerszenarien, z. B. CPU-Auslastung und Arbeitsspeichernutzung, erweitert, wodurch die Zuverlässigkeit der Fehlererkennung erhöht wird.

### <a name="changes"></a>Änderungen

- Die Ressourcentypeigenschaft **supportHttpsTrafficOnly** für Speicherkonten wurde in den SRP-API-Versionen **2016-01-01** und **2016-05-01** aktiviert, aber diese Eigenschaft wird in Azure Stack Hub nicht unterstützt.
- Der Schwellenwert für die Auslastung der Volumekapazität wurde von 80 % (Warnung) und 90 % (kritisch) auf 90 % (Warnung) und 95 % (kritisch) erhöht. Weitere Informationen finden Sie unter [Speicherplatzbenachrichtigungen](azure-stack-manage-storage-shares.md#storage-space-alerts).
- Die AD Graph-Konfigurationsschritte haben sich in diesem Release geändert. Weitere Informationen finden Sie unter [Integrieren der AD FS-Identität in Ihr Azure Stack Hub-Rechenzentrum](azure-stack-integrate-identity.md).
- Zum Abgleich mit den aktuellen, für Windows Server 2019 definierten bewährten Methoden wechselt Azure Stack Hub zur Verwendung einer zusätzlichen Datenverkehrsklasse oder Priorität, um die Kommunikation zwischen den Servern weiter zu trennen und dadurch die Failovercluster-Steuerungskommunikation zu unterstützen. Das Ergebnis dieser Änderungen bietet eine bessere Resilienz für die Failoverclusterkommunikation. Diese Konfiguration von Datenverkehrsklassen und Bandbreitenreservierung wird durch eine Änderung an den Top-of-Rack (ToR)-Switches der Azure Stack Hub-Lösung und am Host oder an den Servern von Azure Stack Hub erreicht.

  Beachten Sie, dass diese Änderungen auf der Hostebene eines Azure Stack Hub-Systems hinzugefügt werden. Wenden Sie sich an ihren OEM, um die erforderlichen Änderungen an den ToR-Netzwerkswitches (Top-of-Rack) vornehmen zu lassen. Diese ToR-Änderung kann entweder vor der Aktualisierung auf Release 2008 oder nach dem Aktualisieren auf 2008 ausgeführt werden. Weitere Informationen finden Sie unter [Planen der Netzwerkintegration für Azure Stack](azure-stack-network.md).

- Die GPU-fähigen VM-Größen **NCas_v4 (NVIDIA T4)** wurden in diesem Build durch die VM-Größen **NCasT4_v3** ersetzt, damit sie mit Azure konsistent sind. Beachten Sie, dass diese im Portal noch nicht sichtbar sind und nur über Azure Resouce Manager-Vorlagen verwendet werden können.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Es wurde ein Problem behoben, bei dem das Löschen einer NSG einer Netzwerkkarte, die keiner aktiven VM zugeordnet, fehlschlägt.
- Es wurde ein Problem behoben, bei dem das Ändern des **IdleTimeoutInMinutes**-Werts für eine öffentliche IP-Adresse, die einem Lastenausgleich zugeordnet ist, die öffentliche IP-Adresse in einen fehlerhaften Zustand versetzt hat.
- Das **Get-AzsDisk**-Cmdlet wurde korrigiert, um den korrekten **Attached**-Status anstelle von **OnlineMigration** für angefügte verwaltete Datenträger zurückzugeben.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Ab Release 2005 werden bei der Aktualisierung auf eine neue Hauptversion (z. B. von 1.2005.x auf 1.2008.x) die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert. Wenn danach ein Hotfix für Ihren Build veröffentlicht wird, sollten Sie ihn installieren.

> [!NOTE]
> Hotfixreleases für Azure Stack Hub sind kumulativ. Sie müssen also nur den neuesten Hotfix installieren, um alle Korrekturen zu erhalten, die in den vorherigen Hotfixreleases für diese Version enthalten waren.

Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="hotfix-prerequisites-before-applying-the-2008-update"></a>Hotfixvoraussetzungen: vor dem Anwenden des Updates 2008

Das Release 2008 von Azure Stack Hub muss auf das Release 2005 mit den folgenden Hotfixes angewendet werden:

- [Azure Stack Hub-Hotfix 1.2005.25.92](https://support.microsoft.com/help/4595074)

### <a name="after-successfully-applying-the-2008-update"></a>Nach erfolgreicher Anwendung des Updates 2008

Wenn auf eine neue Hauptversion (z. B. von 1.2008.x auf 1.2005.x) aktualisieren, werden die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert. Wenn danach ein Hotfix für Ihren Build veröffentlicht wird, sollten Sie ihn installieren.

Wenn nach der Installation des Updates 2008 Hotfixes für 2008 veröffentlicht werden, sollten Sie sie installieren:

- [Azure Stack Hub-Hotfix 1.2008.20.102](https://support.microsoft.com/help/4595075)
::: moniker-end

::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>2005 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 2005 lautet **1.2005.6.53**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack Hub-Updates 2005 lautet **Vollständig**.

Das Paket des Updates 2005 ist größer als vorherige Updates. Dies führt zu längeren Downloadzeiten. Das Update bleibt lange im Zustand **Wird vorbereitet**, und es ist damit zur rechnen, dass dieser Prozess länger dauert als bei vorherigen Updates. Das Update 2005 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 13 – 20 Stunden, 8 Knoten: 16 – 26 Stunden, 12 Knoten: 19 – 32 Stunden, 16 Knoten: 22 – 38 Stunden Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die kürzer oder länger als erwartet sind, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Operator, sofern das Update nicht fehlschlägt. Diese geschätzte Laufzeit ist spezifisch für das Update 2005 und nicht auf andere Azure Stack Hub-Updates übertragbar.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->
- Dieser Build bietet Unterstützung für drei neue GPU-VM-Typen: die VM-Größen NCv3 (Nvidia V100), NVv4 (AMD MI25) und NCas_v4 (NVIDIA T4). Kunden, die über die richtige Hardware verfügen und am Azure Stack Hub-GPU-Vorschauprogramm teilnehmen, können VM-Bereitstellungen erfolgreich durchführen. Wenn Sie Interesse haben, können Sie sich unter https://aka.ms/azurestackhubgpupreview für das GPU-Vorschauprogramm registrieren. Weitere Informationen finden Sie [hier](../user/gpu-vms-about.md).
- Dieses Release bietet ein neues Feature für autonome Reparaturen, bei denen Fehler erkannt, ihre Auswirkungen bewertet und Systemprobleme sicher behoben werden. Dieses Feature verbessert die Verfügbarkeit des Systems, ohne manuelle Eingriffe zu erfordern. Ab Release 2005 erhalten Kunden daher weniger Warnungen. Bei Fehlern in dieser Pipeline ist nur dann eine Aktion des Azure Stack Hub-Operators erforderlich, wenn er eine entsprechende Benachrichtigung erhält.
- Im Azure Stack Hub-Verwaltungsportal ist eine neue Option zum lokalen Speichern von Protokollen für Azure Stack Hub-Kunden verfügbar, deren Systeme per Air Gap isoliert/nicht verbunden sind. Sie können die Protokolle in einer lokalen SMB-Freigabe speichern, wenn Azure Stack Hub nicht mit Azure verbunden ist.
- Das Azure Stack Hub-Verwaltungsportal blockiert jetzt bestimmte Vorgänge, wenn bereits ein Systemvorgang ausgeführt wird. Während eines Updates ist es beispielsweise nicht möglich, einen neuen Skalierungseinheitenknoten hinzuzufügen.
- Dieses Release bietet auf VMs, die vor Release 1910 erstellt wurden, eine bessere Konsistenz des Fabrics mit Azure. Beim Release 1910 kündigte Microsoft an, dass alle neu erstellten VMs das WireServer-Protokoll verwenden. Dies ermöglicht es Kunden, den gleichen WALA-Agent und Windows-Gast-Agent wie Azure zu verwenden, und vereinfacht die Verwendung von Azure-Images in Azure Stack Hub. Mit diesem Release werden alle VMs, die vor Release 1910 erstellt wurden, automatisch zum WireServer-Protokoll migriert. Dies führt auch zu einer höheren Zuverlässigkeit bei der VM-Erstellung und der Bereitstellung von VM-Erweiterungen sowie einer stabileren Uptime.
- Azure Stack Hub-Speicher unterstützt jetzt Version 2019-02-02 der Azure Storage-Dienste-APIs. Bei Azure-Clientbibliotheken ist diese Version mit der neuen REST-API-Version kompatibel. Weitere Informationen finden Sie unter [Azure Stack Hub-Speicherentwicklungstools](../user/azure-stack-storage-dev.md#azure-client-libraries).
- Azure Stack Hub unterstützt jetzt die neue Version von [CreateUiDefinition (Version 2)](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview).
- Neuer Leitfaden für VM-Batchbereitstellungen. Weitere Informationen finden Sie [in diesem Artikel](../operator/azure-stack-capacity-planning-compute.md).
- Der Azure Stack Hub Marketplace CoreOS Container Linux-Element [nähert sich dem Ende seiner Lebensdauer](https://azure.microsoft.com/updates/flatcar-in-azure/). Weitere Informationen finden Sie unter [Migrieren von CoreOS Container Linux](https://docs.flatcar-linux.org/os/migrate-from-container-linux/).

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Verbesserungen der Protokolle und Ereignisse des Speicherinfrastruktur-Clusterdiensts. Protokolle und Ereignisse des Speicherinfrastruktur-Clusterdiensts werden bis zu 14 Tage aufbewahrt, um eine bessere Diagnose und Problembehandlung zu ermöglichen.
- Verbesserungen, die die Zuverlässigkeit beim Starten und Beenden von Azure Stack Hub erhöhen.
- Verbesserungen, die die Laufzeit des Updates durch Dezentralisierung und Entfernung von Abhängigkeiten verringern. Im Vergleich zum Update 2002 verkürzt sich die Laufzeit bei einem Stempel mit vier Knoten von 15 – 42 Stunden auf 13 – 20 Stunden, bei acht Knoten von 20 – 50 auf 16 – 26 Stunden, bei 12 Knoten von 20 – 60 auf 19 – 32 Stunden und bei 16 Knoten von 25 – 70 auf 22 – 38 Stunden. Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware.
- Das Update schlägt nun frühzeitig fehl, wenn bestimmte nicht behebbare Fehler vorliegen.
- Verbesserte Resilienz des Updatepakets während des Downloads aus dem Internet.
- Verbesserte Resilienz beim Beenden und Aufheben der Zuordnung einer VM.
- Verbesserte Resilienz des Netzwerkcontroller-Host-Agents.
- Der CEF-Nutzlast der Syslog-Nachrichten wurden zusätzliche Felder zur Anzeige der Quell-IP-Adresse und des Kontos hinzugefügt, die zum Verbinden des privilegierten Endpunkts und Wiederherstellungsendpunkts verwendet werden. Weitere Informationen finden Sie unter [Integrieren von Azure Stack Hub in Überwachungslösungen mithilfe der Syslog-Weiterleitung](azure-stack-integrate-security.md).
- Der Liste von Ereignissen, die über den Syslog-Client ausgegeben werden, wurden Windows Defender-Ereignisse (Ereignis-IDs 5001, 5010, 5012) hinzugefügt.
- Im Azure Stack-Administratorportal wurden Warnungen für Windows Defender-bezogene Ereignisse hinzugefügt, um Informationen zur Defender-Plattform, Inkonsistenzen der Signaturversion und nicht behandelte erkannte Schadsoftware zu melden.
- Es wurde Unterstützung für vier Border-Geräte für die Integration von Azure Stack Hub in Ihr Rechenzentrum hinzugefügt.

### <a name="changes"></a>Änderungen

- Die Aktionen zum Beenden, Herunterfahren und Neustarten einer Infrastrukturrolleninstanz wurden aus dem Verwaltungsportal entfernt. Die entsprechenden APIs wurden auch im Fabric-Ressourcenanbieter entfernt. Die folgenden PowerShell-Cmdlets im RM-Administratormodul und AZ-Vorschaumodul für Azure Stack Hub funktionieren nicht mehr: **Stop-AzsInfrastructureRoleInstance**, **Disable-InfrastructureRoleInstance** und **Restart-InfrastructureRoleInstance**. Diese Cmdlets werden aus dem nächsten Release des AZ-Administratormoduls für Azure Stack Hub entfernt.
- Azure Stack Hub 2005 unterstützt jetzt nur [App Service in Azure Stack Hub 2020 (Version 87.x)](app-service-release-notes-2020-Q2.md).
- Die für die Hardwareüberwachung erforderliche Benutzerverschlüsselungseinstellung wurde von DES in AES geändert, um die Sicherheit zu erhöhen. Wenden Sie sich an Ihren Hardwarepartner, um zu erfahren, wie Sie die Einstellung im Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) ändern. Nachdem die Änderung im BMC erfolgt ist, fordert dieser möglicherweise, dass Sie den Befehl **Set-BmcCredential** noch mal mit dem privilegierten Endpunkt ausführen. Weitere Informationen finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md).

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Problem behoben, das zu einem Fehler bei der Reparatur eines Skalierungseinheitenknoten führen konnte, weil der Pfad zum Betriebssystem-Basisimage nicht gefunden wurde.
- Es wurde ein Problem beim horizontalen Herunter- und Hochskalieren für die unterstützende Infrastrukturrolle behoben, das einen kaskadierenden Effekt auf die Reparatur von Skalierungseinheitenknoten hat.
- Es wurde ein Problem behoben, bei dem die Erweiterung „.VHD“ (anstelle von „.vhd“) nicht zulässig war, wenn Operatoren im Azure Stack Hub-Administratorportal unter **Alle Diensten > Compute > VM-Images > Hinzufügen** eigene Images hinzufügten.
- Es wurde ein Problem behoben, bei dem ein vorheriger VM-Neustart nach einem anderen VM-Aktualisierungsvorgang (Hinzufügen von Datenträgern, Tags usw.) einen nachfolgenden unerwarteten Neustart verursachte.
- Es wurde ein Problem behoben, bei dem die Erstellung einer doppelten DNS-Zone dazu führte, dass das Portal nicht mehr reagierte. Nun sollte ein entsprechender Fehler angezeigt werden.
- Es wurde ein Problem behoben, bei dem von **Get-AzureStackLogs** nicht die erforderlichen Protokolle zum Beheben von Netzwerkproblemen gesammelt wurden. 
- Es wurde ein Problem behoben, bei dem im Portal weniger Netzwerkschnittstellenkarten angefügt werden konnten, als tatsächlich zulässig sind. 
- Die Codeintegritätsrichtlinie wurde korrigiert, damit für bestimmte interne Software keine Verstoßereignisse ausgegeben werden. Dadurch werden irrelevante Daten in den über den Syslog-Client ausgegebenen Ereignissen zu Verstößen gegen die Codeintegrität reduziert.
- Das **Set-TLSPolicy**-Cmdlet wurde korrigiert, um eine neue Richtlinie ohne Neustart des HTTPS-Diensts oder Hosts zu erzwingen.
- Es wurde ein Problem behoben, bei dem die Verwendung eines Linux-NTP-Servers fälschlicherweise zu Warnungen im Verwaltungsportal führt.  
- Es wurde ein Problem behoben, bei dem ein Failover der Backup Controller-Dienstinstanz zur Deaktivierung automatischer Sicherungen führte.
- Es wurde ein Problem behoben, bei dem die interne Geheimnisrotation fehlschlägt, wenn Infrastrukturdienste keine Internetverbindung haben.
- Es wurde ein Problem behoben, bei dem Benutzer Abonnementberechtigungen nicht über die Azure Stack Hub-Portale anzeigen konnten.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Ab Release 2005 werden bei der Aktualisierung auf eine neue Hauptversion (z. B. von 1.2002.x auf 1.2005.x) die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert. Wenn danach ein Hotfix für Ihren Build veröffentlicht wird, sollten Sie ihn installieren.

> [!NOTE]
> Hotfixreleases für Azure Stack Hub sind kumulativ. Sie müssen also nur den neuesten Hotfix installieren, um alle Korrekturen zu erhalten, die in den vorherigen Hotfixreleases für diese Version enthalten waren.

Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-2005-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 2005

Das Release 2005 von Azure Stack Hub muss auf das Release 2002 mit den folgenden Hotfixes angewendet werden:

- [Azure Stack Hub-Hotfix 1.2002.63.167](https://support.microsoft.com/help/4595072)

### <a name="after-successfully-applying-the-2005-update"></a>Nach erfolgreicher Anwendung des Updates 2005

Ab Release 2005 werden bei der Aktualisierung auf eine neue Hauptversion (z. B. von 1.2002.x auf 1.2005.x) die aktuellen Hotfixes (sofern verfügbar) in der neuen Hauptversion automatisch installiert.

Wenn nach der Installation des Updates 2005 Hotfixes für 2005 veröffentlicht werden, sollten Sie sie installieren:

- [Azure Stack Hub-Hotfix 1.2005.25.92](https://support.microsoft.com/help/4595074)
::: moniker-end

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
- Eine neue Version der Azure Stack Hub-Administrator-REST-API ist verfügbar. Details zu Endpunkten und Breaking Changes finden Sie in der [API-Referenz](/rest/api/azure-stack/).
- Neue Azure PowerShell-Mandantenmodule werden am 15. April 2020 für Azure Stack Hub veröffentlicht. Die derzeit verwendeten Azure RM-Module funktionieren weiterhin, werden aber nach Build 2002 nicht mehr aktualisiert.
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
- Verbesserungen in Bezug auf die [Erfassung von Diagnoseprotokollen](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002). Mit der neuen Umgebung wird die Erfassung von Diagnoseprotokollen optimiert und vereinfacht, indem die Anforderung zum Vorabkonfigurieren eines Blobspeicherkontos beseitigt wird. Die Speicherumgebung ist so vorkonfiguriert, dass Sie vor dem Erstellen einer Supportanfrage Protokolle senden können und so weniger Zeit für einen Anruf beim Support verloren geht.
- Die Zeit für die [proaktive und bedarfsabhängige Sammlung von Protokollen](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002) konnte jeweils um 80 % reduziert werden. Die Protokollerfassung kann länger als erwartet dauern, aber von Azure Stack Hub-Operators muss keine Aktion durchgeführt werden, sofern die Protokollerfassung nicht fehlschlägt.
- Der Downloadstatus eines Azure Stack Hub-Updatepakets wird jetzt auf dem Updateblatt angezeigt, nachdem ein Update initiiert wurde. Dies gilt nur für verbundene Azure Stack Hub-Systeme, bei denen [Updatepakete per automatischem Download vorbereitet werden](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages).
- Verbesserungen in Bezug auf die Zuverlässigkeit für den Netzwerkcontroller-Host-Agent.
- Es wurde ein neuer Microservice mit dem Namen DNS Orchestrator eingeführt, mit dem die Resilienzlogik für die internen DNS-Dienste bei Patch- und Updatevorgängen verbessert wird.
- Es wurde eine neue Anforderungsüberprüfung hinzugefügt, damit beim Erstellen von VMs für ungültige Blob-URIs für den Parameters des Startdiagnose-Speicherkontos ggf. ein Fehler erkannt wird.
- Für Rdagent und den Host-Agent wurden Verbesserungen in Bezug auf die automatische Wiederherstellung und Protokollierung vorgenommen. Dies sind zwei Dienste auf dem Host, mit denen CRUD-Vorgänge für VMs durchgeführt werden.
- Der Marketplace-Verwaltung wurde ein neues Feature hinzugefügt, mit dem Microsoft Attribute hinzufügen kann, die für Administratoren das Herunterladen von Marketplace-Produkten blockieren, die mit ihrer Azure Stack-Instanz inkompatibel sind (aufgrund verschiedener Eigenschaften, z. B. Azure Stack-Version oder Abrechnungsmodell). Diese Attribute können nur von Microsoft hinzugefügt werden. Weitere Informationen finden Sie unter [Herunterladen von Marketplace-Elementen mithilfe des Portals](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

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

- Wenn Sie einen virtuellen Windows-Computer mithilfe von PowerShell erstellen, müssen Sie das Flag `provisionvmagent` hinzufügen, wenn vom virtuellen Computer Erweiterungen bereitgestellt werden sollen. Ohne dieses Flag wird der virtuelle Computer ohne den Gast-Agent erstellt, sodass keine VM-Erweiterungen bereitgestellt werden können:

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Problem behoben, das zu Problemen mit der Internetverbindung führte, wenn mehrere öffentliche IP-Adressen für die gleiche NIC auf einem virtuellen Computer hinzugefügt wurden. Eine NIC mit zwei öffentlichen IP-Adressen funktioniert jetzt wie erwartet.
- Es wurde ein Problem behoben, bei dem eine Warnung mit dem Hinweis ausgelöst wurde, dass das Azure AD-Basisverzeichnis konfiguriert werden muss.
- Es wurde ein Problem behoben, bei dem eine Warnung nicht automatisch geschlossen wurde. Die Warnung hat den Hinweis enthalten, dass das Azure AD-Basisverzeichnis konfiguriert werden muss, aber sie wurde nach dem Beheben des Problems nicht geschlossen.
- Es wurde ein Problem behoben, bei dem für Updates während der Vorbereitungsphase ein Fehler aufgetreten ist, weil es zu internen Fehlern beim Updateressourcenanbieter gekommen ist.
- Es wurde ein Problem behoben, bei dem für Vorgänge des Add-On-Ressourcenanbieters ein Fehler aufgetreten ist, nachdem die Azure Stack Hub-Geheimnisrotation durchgeführt wurde.
- Es wurde ein Problem behoben, das aufgrund einer hohen Arbeitsspeicherauslastung für die ERCS-Rolle eine häufige Ursache von Azure Stack Hub-Updatefehlern war.
- Es wurde ein Fehler auf dem Updateblatt behoben, bei dem der Updatestatus während der Vorbereitungsphase eines Azure Stack Hub-Updates nicht als **Wird vorbereitet**, sondern als **Wird installiert** angezeigt wurde.
- Es wurde ein Problem behoben, bei dem das RSC-Feature auf den virtuellen Switches zu Inkonsistenzen geführt hat und der Datenverkehr verworfen wurde, der über ein Lastenausgleichsmodul geflossen ist. Das RSC-Feature ist jetzt standardmäßig deaktiviert.
- Es wurde ein Problem behoben, aufgrund dessen mehrere IP-Konfigurationen auf einer Netzwerkschnittstelle (Network Interface, NIC) zu einer fehlerhaften Weiterleitung von Datenverkehr und Unterbrechung der ausgehenden Verbindung geführt haben. 
- Es wurde ein Problem behoben, bei dem die MAC-Adresse einer NIC zwischengespeichert wurde und die Zuweisung dieser Adresse zu einer anderen Ressource zu VM-Bereitstellungsfehlern geführt hat.
- Es wurde ein Problem behoben, bei dem für Windows-VM-Images aus dem RETAIL-Kanal die Lizenz per AVMA nicht aktiviert werden konnte.
- Es wurde ein Problem behoben, bei dem VMs nicht erstellt werden konnten, wenn die Anzahl von virtuellen Kernen, die von der VM angefordert wurden, der Anzahl von physischen Kernen des Knoten entsprochen hat. Es ist jetzt zulässig, dass VMs über eine Anzahl von virtuellen Kernen verfügen, die kleiner oder gleich der Anzahl von physischen Kernen des Knotens ist.
- Es wurde ein Problem behoben, bei dem es nicht zugelassen wurde, dass der Lizenztyp auf „Null“ festgelegt wird, um Images mit nutzungsbasierter Bezahlung auf BYOL umzustellen.
- Es wurde ein Problem behoben, damit Erweiterungen einer VM-Skalierungsgruppe hinzugefügt werden können.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack Hub-Hotfix für 1910, bevor Sie Azure Stack Hub auf 2002 aktualisieren.

> [!NOTE]
> Hotfixreleases für Azure Stack Hub sind kumulativ. Sie müssen also nur den neuesten Hotfix installieren, um alle Korrekturen zu erhalten, die in den vorherigen Hotfixreleases für diese Version enthalten waren.

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

Weitere Informationen zu Hotfixes finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md#hotfixes).

### <a name="prerequisites-before-applying-the-2002-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 2002

Das Release 2002 von Azure Stack Hub muss auf das Release 1910 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1910.84.230](https://support.microsoft.com/help/4592243)

### <a name="after-successfully-applying-the-2002-update"></a>Nach erfolgreicher Anwendung des Updates 2002

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.2002.63.167](https://support.microsoft.com/help/4595072)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-release-notes"></a>1910 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>1908 – archivierte Versionshinweise
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>1907 – archivierte Versionshinweise
::: moniker-end
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

::: moniker range="<azs-2002"
Sie können auf [ältere Versionen der Azure Stack Hub-Versionshinweise im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack Hub-Support finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end
