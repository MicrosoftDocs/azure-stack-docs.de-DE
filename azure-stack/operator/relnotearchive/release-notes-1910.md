---
title: 'Azure Stack Hub 1910: Versionshinweise'
description: Versionshinweise für Version 1910 für integrierte Azure Stack Hub-Systeme, einschließlich Updates und Fehlerbehebungen.
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: ced055a2983fb4aabb1c31f314ccb4587f3801cc
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248711"
---
# <a name="azure-stack-hub-1910-release-notes"></a>Azure Stack Hub 1910: Versionshinweise

In diesem Artikel werden die Inhalte der Azure Stack Hub-Updatepakete beschrieben. Das Update enthält Verbesserungen und Fehlerbehebungen für die aktuelle Version von Azure Stack Hub.

> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack Hub-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit (ASDK) an.

> [!IMPORTANT]  
> Wenn die Version der Azure Stack Hub-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Azure Stack-Checkliste für Updateaktivitäten](../release-notes-checklist.md)
- [Bekannte Probleme](../known-issues.md)
- [Hotfixes](#hotfixes)
- [Sicherheitsupdates](../release-notes-security-updates.md)

Unterstützung bei der Problembehandlung von Updates und dem Updateprozess finden Sie unter [Problembehandlung bei Patch- und Updateproblemen für Azure Stack Hub](../azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Azure Stack Hub-Updatepaket mit dem [Downloadprogramm für das Azure Stack Hub-Update](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="1910-build-reference"></a>1910 – Buildreferenz

Die Buildnummer des Azure Stack Hub-Updates 1910 lautet **1.1910.0.58**.

### <a name="update-type"></a>Updatetyp

Ab Release 1908 wurde das zugrunde liegende Betriebssystem, unter dem Azure Stack Hub ausgeführt wird, auf Windows Server 2019 aktualisiert. Dieses Update ermöglicht wichtige grundlegende Verbesserungen und die Option, Azure Stack Hub um weitere Funktionen zu erweitern.

Beim Azure Stack Hub-Update 1910 handelt es sich um einen Build vom Typ **Express**.

Das Paket des Updates 1910 ist größer als bei vorherigen Updates. Der Download dauert dadurch länger. Das Update bleibt lange im Zustand **Wird vorbereitet**, und es ist damit zu rechnen, dass dieser Prozess länger dauert als bei vorherigen Updates. Das Update 1910 dauert etwa zehn Stunden – unabhängig von der Anzahl physischer Knoten in Ihrer Azure Stack Hub-Umgebung. Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die länger als der erwartete Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack Hub-Betreiber, es sei denn, das Update schlägt fehl. Diese geschätzte Laufzeit ist spezifisch für das Update 1910 und nicht auf andere Azure Stack Hub-Updates übertragbar.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack Hub](../azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neues

<!-- What's new, also net new experiences and features. -->

- Im Administratorportal werden nun die IP-Adressen des privilegierten Endpunkts im Menü mit den Regionseigenschaften angezeigt, um die Ermittlung zu vereinfachen. Darüber hinaus werden der derzeit konfigurierte Zeitserver sowie DNS-Weiterleitungen angezeigt. Weitere Informationen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../azure-stack-privileged-endpoint.md).

- Das Integritäts- und Überwachungssystem von Azure Stack Hub kann nun im Falle eines Fehlers Warnungen für verschiedene Hardwarekomponenten auslösen. Für diese Warnungen ist möglicherweise eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [Überwachen von Azure Stack Hub-Hardwarekomponenten](../azure-stack-hardware-monitoring.md).

- [cloud-init-Unterstützung für Azure Stack Hub](/azure/virtual-machines/linux/using-cloud-init): cloud-init ist eine gängige Methode für die Anpassung eines virtuellen Linux-Computers beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs aufgerufen wird, müssen Sie keine zusätzlichen Schritte oder Agents auf Ihre Konfiguration anwenden. Die Ubuntu-Images im Marketplace wurden aktualisiert, um cloud-init für die Bereitstellung zu unterstützen.

- Azure Stack Hub unterstützt jetzt alle Microsoft Azure Linux-Agents als Azure.

- Es ist eine neue Version von Azure Stack Hub-PowerShell-Modulen für Administratoren verfügbar. <!-- For more information, see -->

- Neue Azure PowerShell-Mandantenmodule wurden am 15. April 2020 für Azure Stack Hub veröffentlicht. Die derzeit verwendeten Azure RM-Module funktionieren weiterhin, werden aber nach Build 2002 nicht mehr aktualisiert.

- Das Cmdlet **Set-AzSDefenderManualUpdate** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die manuelle Aktualisierung für Windows Defender-Definitionen in der Azure Stack Hub-Infrastruktur zu konfigurieren. Weitere Informationen finden Sie unter [Aktualisieren von Windows Defender Antivirus in Azure Stack Hub](../azure-stack-security-av.md).

- Das Cmdlet **Set-AzSDnsForwarder** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Weiterleitungseinstellungen der DNS-Server in Azure Stack Hub zu ändern. Weitere Informationen finden Sie unter [DNS-Integration in Azure Stack Hub-Datencenter](../azure-stack-integrate-dns.md).

- Die Unterstützung der Verwaltung von **Kubernetes-Clustern** per [AKS-Engine](../../user/azure-stack-kubernetes-aks-engine-overview.md) wurde hinzugefügt. Ab diesem Update können Kunden Kubernetes-Produktionscluster bereitstellen. Die AKS-Engine ermöglicht Benutzern Folgendes:
  - Verwalten des Lebenszyklus ihrer Kubernetes-Cluster. Sie können Cluster erstellen, aktualisieren und skalieren.
  - Verwalten ihrer Cluster mithilfe verwalteter Images, die von den AKS- und Azure Stack Hub-Teams erstellt wurden.
  - Nutzen eines Kubernetes-Cloudanbieters mit Azure Resource Manager-Integration, der Cluster mit nativen Azure-Ressourcen erstellt
  - Bereitstellen und Verwalten ihrer Cluster in verbundenen oder getrennten Azure Stack Hub-Stempeln
  - Verwenden von Azure-Hybridfeatures:
    - Integration mit Azure Arc
    - Integration mit Azure Monitor für Container
  - Verwenden von Windows-Containern mit AKS-Engine
  - Erhalten von Microsoft- und Engineering-Support für ihre Bereitstellungen.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- In Azure Stack Hub wurde die Funktion für die automatische Behebung von Patch- und Updateproblemen verbessert, die zuvor Updatefehler verursachten oder verhinderten, dass Betreiber ein Azure Stack Hub-Update initiieren konnten. Daher sind in der Gruppe **Test-AzureStack -UpdateReadiness** weniger Tests enthalten. Weitere Informationen finden Sie unter [Überprüfen des Azure Stack Hub-Systemstatus](../azure-stack-diagnostic-test.md#groups). Die folgenden drei Tests bleiben in der Gruppe **UpdateReadiness**:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Es wurde eine Überwachungsregel hinzugefügt, um eine Meldung zu generieren, wenn ein externes Gerät (beispielsweise ein USB-Schlüssel) in einen Knoten der Azure Stack Hub-Infrastruktur eingebunden wird. Das Überwachungsprotokoll wird über Syslog ausgegeben und als **Microsoft-Windows-Security-Auditing: 6416|Plug & Play-Ereignisse** angezeigt. Weitere Informationen zum Konfigurieren des Syslog-Clients finden Sie unter [Integrieren von Azure Stack in Überwachungslösungen mithilfe der Syslog-Weiterleitung](../azure-stack-integrate-security.md).

- Azure Stack Hub wird für die internen Zertifikate auf RSA-Schlüssel mit 4096 Bit umgestellt. Im Zuge der internen Geheimnisrotation werden alte 2048-Bit-Zertifikate durch Zertifikate mit einer Länge von 4096 Bit ersetzt. Weitere Informationen zur Geheimnisrotation in Azure Stack Hub finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](../azure-stack-rotate-secrets.md).

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

   Diese Änderungen wurden auch in der Dokumentation der [IPsec-/IKE-Parameter](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) berücksichtigt.

- Der Dienst für die Infrastruktursicherung verbessert die Logik, die den gewünschten freien Speicherplatz für Sicherungen berechnet, anstatt einen festen Schwellenwert zu verwenden. Der Dienst ermittelt anhand der Größe einer Sicherung, der Aufbewahrungsrichtlinie, der Reserve und der aktuellen Auslastung des externen Speicherorts, ob eine Warnung für den Betreiber ausgelöst werden muss.

### <a name="changes"></a>Änderungen

- Beim Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub gibt es eine neue Benutzeroberfläche, über die Sie eine Version des Elements angeben können, wenn mehrere Versionen vorhanden sind. Die neue Benutzeroberfläche steht in Szenarien mit und ohne Verbindung zur Verfügung. Weitere Informationen finden Sie unter [Herunterladen vorhandener Marketplace-Elemente aus Azure und Veröffentlichen in Azure Stack Hub](../azure-stack-download-azure-marketplace-item.md).  

- Ab dem Release 1910 **benötigt** das Azure Stack Hub-System einen zusätzlichen privaten internen IP-Adressraum der Größe „/20“. Weitere Informationen finden Sie unter [Planen der Netzwerkintegration für Azure Stack](../azure-stack-network.md).
  
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

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack Hub finden Sie unter [Azure Stack Hub-Sicherheitsupdates](../release-notes-security-updates.md).

Der Bericht zu den Qualys-Sicherheitsrisiken für diese Version kann von der [Qualys-Website](https://www.qualys.com/azure-stack/) heruntergeladen werden.

## <a name="hotfixes"></a>Hotfixes

Azure Stack Hub veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack Hub-Hotfix für 1908, bevor Sie Azure Stack Hub auf 1910 aktualisieren.

> [!NOTE]
> Hotfixreleases für Azure Stack Hub sind kumulativ. Sie müssen also nur den neuesten Hotfix installieren, um alle Korrekturen zu erhalten, die in den vorherigen Hotfixreleases für diese Version enthalten waren.

Azure Stack Hub-Hotfixes gelten nur für integrierte Azure Stack Hub-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1910-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1910

Das Release 1910 von Azure Stack Hub muss auf das Release 1908 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1908.51.133](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>Nach erfolgreicher Anwendung des Updates 1910

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub-Hotfix 1.1910.63.186](https://support.microsoft.com/help/4574735)
