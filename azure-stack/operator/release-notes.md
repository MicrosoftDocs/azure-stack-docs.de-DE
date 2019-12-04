---
title: Versionshinweise zu Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen in den Updates für integrierte Azure Stack-Systeme enthalten sind, und wo das Update heruntergeladen werden kann.
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
ms.date: 11/25/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: 75f1c4cae33987a7a2c662ced7806ed094c6ca82
ms.sourcegitcommit: 3a8e116fd0b16e1201e55e2088dde2e581004045
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74557702"
---
# <a name="azure-stack-updates-release-notes"></a>Azure Stack-Updates: Versionshinweise

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel werden die Inhalte der Azure Stack-Updatepakete beschrieben. Das Update enthält Neuerungen, Verbesserungen und Fehlerbehebungen für diese Version von Azure Stack.

Um auf Versionshinweise für eine andere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Dieses Updatepaket gilt nur für integrierte Azure Stack-Systeme. Wenden Sie dieses Updatepaket nicht auf das Azure Stack Development Kit an.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Wenn die Version der Azure Stack-Instanz mehr als zwei Updates zurückliegt, wird sie als nicht konform eingestuft. Sie müssen [mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

Unterstützung bei der Problembehandlung von Updates und dem Updateprozess finden Sie unter [Problembehandlung bei Patch- und Updateproblemen für Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1910 lautet **1.1910.0.58**.

### <a name="update-type"></a>Updatetyp

Ab 1908 wurde das zugrunde liegende Betriebssystem, unter dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.

Beim Azure Stack-Update 1910 handelt es sich um einen Build vom Typ **Express**.

Das Paket des Updates 1910 ist größer als bei vorherigen Updates. Dies führt zu längeren Downloadzeiten. Das Update bleibt lange im Zustand **Wird vorbereitet**, und es ist damit zur rechnen, dass dieser Prozess länger dauert als bei vorherigen Updates. Das Update 1910 dauert etwa zehn Stunden – unabhängig von der Anzahl physischer Knoten in Ihrer Azure Stack-Umgebung. Die genauen Laufzeiten des Updates hängen in der Regel von der Kapazität ab, die auf Ihrem System durch Mandantenworkloads beansprucht wird, sowie von der Netzwerkkonnektivität Ihres Systems (falls eine Verbindung mit dem Internet besteht) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl. Diese geschätzte Laufzeit ist spezifisch für das Update 1910 und nicht auf andere Azure Stack-Updates übertragbar.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neuigkeiten

<!-- What's new, also net new experiences and features. -->

- Im Administratorportal werden nun die IP-Adressen des privilegierten Endpunkts im Menü mit den Regionseigenschaften angezeigt, um die Ermittlung zu vereinfachen. Darüber hinaus werden der derzeit konfigurierte Zeitserver sowie DNS-Weiterleitungen angezeigt.

- Das Integritäts- und Überwachungssystem von Azure Stack kann nun im Falle eines Fehlers Warnungen für verschiedene Hardwarekomponenten auslösen. Hierzu sind zusätzliche Konfigurationsschritte erforderlich. Weitere Informationen finden Sie unter [Überwachen von Azure Stack-Hardwarekomponenten](azure-stack-hardware-monitoring.md).

- [cloud-init-Unterstützung für virtuelle Computer in Azure](/azure/virtual-machines/linux/using-cloud-init): cloud-init ist eine gängige Methode für die Anpassung eines virtuellen Linux-Computers beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs aufgerufen wird, müssen Sie keine zusätzlichen Schritte oder Agents auf Ihre Konfiguration anwenden. Die Ubuntu-Images im Marketplace wurden aktualisiert, um cloud-init für die Bereitstellung zu unterstützen.

- Azure Stack unterstützt jetzt alle Microsoft Azure Linux-Agents als Azure.

- Es ist eine neue Version von Azure Stack-PowerShell-Modulen für Administratoren verfügbar. <!-- For more information, see -->

- Das Cmdlet **Set-AzSDefenderManualUpdate** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die manuelle Aktualisierung für Windows Defender-Definitionen in der Azure Stack-Infrastruktur zu konfigurieren. Weitere Informationen finden Sie unter [Aktualisieren von Windows Defender Antivirus in Azure Stack](azure-stack-security-av.md).

- Das Cmdlet **Get-AzSDefenderManualUpdate** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Konfiguration der manuellen Aktualisierung für Windows Defender-Definitionen in der Azure Stack-Infrastruktur abzurufen. Weitere Informationen finden Sie unter [Aktualisieren von Windows Defender Antivirus in Azure Stack](azure-stack-security-av.md).

- Das Cmdlet **Set-AzSDnsForwarder** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Weiterleitungseinstellungen der DNS-Server in Azure Stack zu ändern. Weitere Informationen finden Sie unter [DNS-Integration in Azure Stack-Datencenter](azure-stack-integrate-dns.md).

- Das Cmdlet **Get-AzSDnsForwarder** wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die Weiterleitungseinstellungen der DNS-Server in Azure Stack abzurufen. Weitere Informationen finden Sie unter [DNS-Integration in Azure Stack-Datencenter](azure-stack-integrate-dns.md).

- Die Unterstützung der Verwaltung von **Kubernetes-Clustern** per [AKS-Engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) wurde hinzugefügt. Ab diesem Update können Kunden Kubernetes-Produktionscluster bereitstellen. Die AKS-Engine ermöglicht Benutzern Folgendes:
   - Verwalten des Lebenszyklus ihrer Kubernetes-Cluster. Sie können Cluster erstellen, aktualisieren und skalieren.
   - Verwalten ihrer Cluster mithilfe verwalteter Images, die von den AKS- und Azure Stack-Teams erstellt wurden.
   - Nutzen eines Kubernetes-Cloudanbieters mit Azure Resource Manager-Integration, der Cluster mit nativen Azure-Ressourcen erstellt
   - Bereitstellen und Verwalten ihrer Cluster in verbundenen oder getrennten Azure Stack-Stempeln
   - Verwenden von Azure-Hybridfeatures:
      * Azure Arc-Integration (private Vorschau in Kürze verfügbar)
      * Integration mit Azure Monitor für Container (derzeit in der Public Preview-Phase)
   - Verwenden von Windows-Containern mit AKS-Engine (derzeit in der Public Preview-Phase)
   - Erhalten von CSS- und PG-Unterstützung für ihre Bereitstellungen

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Es wurde eine Überwachungsregel hinzugefügt, um eine Meldung zu generieren, wenn ein externes Gerät (beispielsweise ein USB-Schlüssel) in einen Knoten der Azure Stack-Infrastruktur eingebunden wird. Das Überwachungsprotokoll wird über Syslog ausgegeben und als **Microsoft-Windows-Security-Auditing: 6416|Plug & Play-Ereignisse** angezeigt. Weitere Informationen zum Konfigurieren des Syslog-Clients finden Sie unter [Integrieren von Azure Stack in Überwachungslösungen mithilfe der Syslog-Weiterleitung](azure-stack-integrate-security.md).

- Azure Stack wird für die internen Zertifikate auf RSA-Schlüssel mit 4.096 Bits umgestellt. Im Zuge der internen Geheimnisrotation werden alte 2.048-Bit-Zertifikate durch Zertifikate mit einer Länge von 4.096 Bits ersetzt. Weitere Informationen zur Geheimnisrotation in Azure Stack finden Sie unter [Rotieren von Geheimnissen in Azure Stack](azure-stack-rotate-secrets.md).

- Bei mehreren internen Komponenten wurde ein Upgrade der Komplexität von Kryptografiealgorithmen und der Schlüsselstärke durchgeführt, um der Richtlinie 15 des Ausschusses für nationale Sicherheitssysteme (Committee on National Security Systems – Policy 15, CNSSP-15) zu entsprechen, die Best Practices für die Verwendung öffentlicher Standards für einen sicheren Informationsaustausch enthält. Die Verbesserungen umfassen unter anderem AES256 für die Kerberos-Authentifizierung und SHA384 für die VPN-Verschlüsselung. Weitere Informationen zu CNSSP-15 finden Sie auf der [Richtlinienseite](http://www.cnss.gov/CNSS/issuances/Policies.cfm) des Ausschusses für nationale Sicherheitssysteme.

- Infolge des obigen Upgrades verfügt Azure Stack nun über neue Standardwerte für IPsec-/IKEv2-Konfigurationen. Die neuen Standardwerte aufseiten von Azure Stack lauten wie folgt:

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

- Der Dienst für die Infrastruktursicherung verbessert die Logik, die den gewünschten freien Speicherplatz für Sicherungen berechnet, anstatt einen festen Schwellenwert zu verwenden. Der Dienst ermittelt anhand der Größe einer Sicherung, der Aufbewahrungsrichtlinie, der Reserve und der aktuellen Auslastung des externen Speicherorts, ob eine Warnung für den Bediener ausgelöst werden muss. 

### <a name="changes"></a>Änderungen

- Beim Herunterladen von Marketplace-Elementen von Azure in Azure Stack gibt es eine neue Benutzeroberfläche, über die Sie eine Version des Elements angeben können, wenn mehrere Versionen vorhanden sind. Die neue Benutzeroberfläche steht in Szenarien mit und ohne Verbindung zur Verfügung. Weitere Informationen finden Sie unter [Herunterladen vorhandener Marketplace-Elemente aus Azure und Veröffentlichen in Azure Stack](azure-stack-download-azure-marketplace-item.md).  

- Ab dem Release 1910 benötigt das Azure Stack-System einen zusätzlichen privaten internen IP-Adressraum der Größe „/20“. Dieses Netzwerk ist ein privates Netzwerk für das Azure Stack-System und kann für mehrere Azure Stack-Systeme innerhalb Ihres Rechenzentrums wiederverwendet werden. Das Netzwerk ist zwar ein privates Netzwerk für Azure Stack, es darf sich aber nicht mit einem Netzwerk in Ihrem Rechenzentrum überschneiden. Der private IP-Adressbereich „/20“ ist in mehrere Netzwerke unterteilt, die die Ausführung der Azure Stack-Infrastruktur in Containern ermöglichen (wie zuvor in den [Versionshinweisen für 1905](release-notes.md?view=azs-1905) erwähnt). Die Ausführung der Azure Stack-Infrastruktur in Containern dient zur Optimierung der Auslastung sowie zur Verbesserung der Leistung. Darüber hinaus wird der private IP-Adressbereich „/20“ auch verwendet, um kontinuierliche Maßnahmen zur Verringerung des erforderlichen routingfähigen IP-Adressraums vor der Bereitstellung zu ermöglichen.

  - Beachten Sie, dass die Eingabe „/20“ eine Voraussetzung für das nächste Azure Stack-Update nach 1910 ist. Wenn das nächste Azure Stack-Update nach 1910 veröffentlicht wurde und versucht wird, das Update zu installieren, ist das Update nicht erfolgreich, wenn die Eingabe „/20“ nicht vorgenommen wurde, wie weiter unten in den Problembehandlungsschritten beschrieben. Im Administratorportal wird eine Warnung angezeigt, bis die oben erwähnten Problembehandlungsschritte ausgeführt wurden. Informationen zur Nutzung dieses neuen privaten Adressraums finden Sie im Artikel [Planen der Netzwerkintegration für Azure Stack](azure-stack-network.md#private-network). 

  - Problembehandlungsschritte: Gehen Sie zur Problembehandlung wie unter [Zugreifen auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) beschrieben vor. Bereiten Sie einen [privaten internen IP-Adressbereich](azure-stack-network.md#logical-networks) der Größe „/20“ vor, und führen Sie in der PEP-Sitzung das folgende Cmdlet (erst verfügbar ab 1910) unter Verwendung des folgenden Formats aus: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Wenn der Vorgang erfolgreich ausgeführt wurde, erhalten Sie eine Meldung mit dem Hinweis, dass der interne Azs-Netzwerkbereich der Konfiguration hinzugefügt wurde (**Azs Internal Network range added to the config**). Nach erfolgreichem Abschluss wird die Warnung im Administratorportal geschlossen. Das Azure Stack-System kann nun auf die nächste Version aktualisiert werden.
  
- Der Dienst für die Infrastruktursicherung löscht unvollständig hochgeladene Sicherungsdaten, wenn die Kapazität des externen Speicherorts während des Uploadvorgangs nicht ausreicht.  

- Der Dienst für die Infrastruktursicherung fügt der Sicherungsnutzlast für AAD-Bereitstellungen den Identitätsdienst hinzu.  

- Das Azure Stack-PowerShell-Modul wurde für das Release 1910 auf die Version 1.8.0 aktualisiert.<br>Die Änderungen umfassen Folgendes:
   - **Neues DRP-Administratormodul:** Der Bereitstellungsressourcenanbieter (Deployment Resource Provider, DRP) ermöglicht orchestrierte Bereitstellungen von Ressourcenanbietern in Azure Stack. Diese Befehle interagieren mit der Azure Resource Manager-Ebene, um mit DRP zu interagieren.
   - **BRP:** <br />
           - Unterstützung der Wiederherstellung einzelner Rollen für die Sicherung der Azure Stack-Infrastruktur <br />
           - Hinzufügung des Parameters `RoleName` zum Cmdlet `Restore-AzsBackup`
   - **FRP:** Breaking Changes für Ressourcen vom Typ **Laufwerk** und **Volume** mit der API-Version `2019-05-01`. Die Features werden ab Azure Stack 1910 unterstützt: <br />
            - Der Wert von `ID`, `Name`, `HealthStatus` und `OperationalStatus` wurde geändert. <br />
            - Unterstützte neue Eigenschaften `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer` und `StoragePool` für Ressourcen vom Typ **Laufwerk** <br />
            - Die Eigenschaften `CanPool` und `CannotPoolReason` von Ressourcen des Typs **Laufwerk** sind veraltet. Verwenden Sie stattdessen `OperationalStatus`.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Es wurde ein Problem behoben, das die Erzwingung der TLS 1.2-Richtlinie in Umgebungen verhindert hat, die vor dem Azure Stack-Release 1904 bereitgestellt wurden.
- Es wurde ein Problem behoben, das dazu führte, dass ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, keine Verwendung der SSH-Schlüssel für die Anmeldung zuließ. 
- **Kennwort zurücksetzen** wurde von der Benutzeroberfläche für VM-Skalierungsgruppen entfernt.
- Es wurde ein Problem behoben, das dazu führte, dass beim Löschen des Lastenausgleichs im Portal das Objekt auf der Infrastrukturebene nicht gelöscht wurde.
- Es wurde ein Problem behoben, durch das in der Warnung für die Gatewaypoolauslastung im Administratorportal ein falscher Prozentsatz angezeigt wurde.
- Es wurde ein Problem behoben, das zu Problemen mit der Internetverbindung führte, wenn mehrere öffentliche IP-Adressen für die gleiche NIC auf einem virtuellen Computer hinzugefügt wurden. Eine NIC mit zwei öffentlichen IP-Adressen sollte nun ordnungsgemäß funktionieren.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1910 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Installieren Sie den aktuellen Azure Stack-Hotfix für 1908, bevor Sie Azure Stack auf 1910 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1910-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1910

Das Release 1910 von Azure Stack muss auf das Release 1908 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1908.9.43](https://support.microsoft.com/help/4531007)

### <a name="after-successfully-applying-the-1910-update"></a>Nach erfolgreicher Anwendung des Updates 1910

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Für 1910 ist kein Hotfix verfügbar.
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1908 lautet **1.1908.4.33**.

### <a name="update-type"></a>Updatetyp

Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.

Der Buildtyp des Azure Stack-Updates 1908 lautet **Vollständig**. Daher hat das Update 1908 eine längere Laufzeit als Express-Updates wie 1906 und 1907. Genaue Laufzeiten für vollständige Updates hängen in der Regel von der Anzahl der Knoten ab, die Ihre Azure Stack-Instanz enthält, der auf Ihrem System von Mandantenworkloads verbrauchten Kapazität, der Netzwerkkonnektivität Ihres Systems (falls es mit dem Internet verbunden ist) und Ihrer Hardwarekonfiguration. Das Update 1908 hatte bei unseren internen Tests die folgenden erwarteten Laufzeiten: 4 Knoten: 42 Stunden, 8 Knoten: 50 Stunden, 12 Knoten: 60 Stunden, 16 Knoten: 70 Stunden. Updatelaufzeiten, die länger als diese erwarteten Werte dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen seitens des Azure Stack-Betreibers, sofern es für das Update nicht zu einem Fehler kommt.

Weitere Informationen zu Update-Buildtypen finden Sie unter [Verwalten von Updates in Azure Stack](azure-stack-updates.md).

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl.
- Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1908 und nicht auf andere Azure Stack-Updates übertragbar.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neuigkeiten

<!-- What's new, also net new experiences and features. -->

- Beachten Sie für 1908, dass das zugrunde liegende Betriebssystem, auf dem Azure Stack ausgeführt wird, auf Windows Server 2019 aktualisiert wurde. So werden wichtige grundlegende Verbesserungen ermöglicht, und in naher Zukunft kann Azure Stack um weitere Funktionen erweitert werden.
- Für alle Komponenten der Azure Stack-Infrastruktur wird jetzt der FIPS 140-2-Modus verwendet.
- Azure Stack-Operatoren können jetzt Portalbenutzerdaten entfernen. Weitere Informationen finden Sie unter [Löschen von Portalbenutzerdaten aus Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Es wurden Verbesserungen an der Azure Stack-Verschlüsselung von ruhenden Daten vorgenommen, um Geheimnisse dauerhaft im Hardware-TPM (Trusted Platform Module) der physischen Knoten zu speichern.

### <a name="changes"></a>Änderungen

- Hardwareanbieter veröffentlichen das OEM-Erweiterungspaket 2.1 oder höher zur gleichen Zeit wie Azure Stack, Version 1908. Das OEM-Erweiterungspaket 2.1 oder höher ist eine Voraussetzung für Version 1908 von Azure Stack. Weitere Informationen zum Herunterladen des OEM-Erweiterungspakets 2.1 oder höher erhalten Sie von Hardwareanbieter Ihres Systems und im Artikel zu [OEM-Updates](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Fehlerbehebungen

- Es wurden ein Problem mit der Kompatibilität zukünftiger Azure Stack-OEM-Updates und ein Problem mit der VM-Bereitstellung bei Verwendung von Kundenbenutzerimages behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) behoben.  
- Es wurde ein Problem mit einem OEM-Firmwareupdate behoben und in Test-AzureStack eine Fehldiagnose zur Integrität des Fabric-Rings korrigiert. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) behoben.
- Es wurde ein Problem mit dem Prozess für OEM-Firmwareupdates behoben. Dieses Problem wurde in Version 1907 gefunden und mit dem Hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) behoben.

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1908 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1907 installieren, bevor Sie Azure Stack auf 1908 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="prerequisites-before-applying-the-1908-update"></a>Voraussetzungen: Vor dem Anwenden des Updates 1908

Version 1908 von Azure Stack muss auf Version 1907 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1907.18.56](https://support.microsoft.com/help/4528552)

Für das Azure Stack-Update 1908 ist **Azure Stack-OEM-Version 2.1 oder höher** vom Hardwareanbieter Ihres Systems erforderlich. OEM-Updates enthalten Treiber- und Firmwareupdates Ihrer Azure Stack-Systemhardware. Weitere Informationen zum Anwenden von OEM-Updates finden Sie unter [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md) (Anwenden von Azure Stack-OEM-Updates).

### <a name="after-successfully-applying-the-1908-update"></a>Nach erfolgreicher Anwendung des Updates 1908

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1908.9.43](https://support.microsoft.com/help/4531007)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1907 ist **1.1907.0.20**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack 1907-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Auf der Grundlage interner Tests lässt sich sagen, dass das Update 1907 etwa 13 Stunden dauert.

- Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und der Konfiguration Ihrer Systemhardware.
- Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl.
- Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1907 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Neuigkeiten

<!-- What's new, also net new experiences and features. -->

- Allgemein verfügbare Version des Azure Stack-Sammlungsdiensts für Diagnoseprotokolle, um die Sammlung von Diagnoseprotokollen zu vereinfachen und zu verbessern. Der Azure Stack-Sammlungsdienst für Diagnoseprotokolle stellt ein vereinfachtes Verfahren zum Sammeln und Teilen von Diagnoseprotokollen mit Microsoft Customer Support Services (CSS) zur Verfügung. Dieser Sammlungsdienst für Diagnoseprotokolle bietet eine neue Benutzeroberfläche im Azure Stack-Verwaltungsportal, mit der Operatoren beim Erreichen bestimmter kritischer Warnschwellen Diagnoseprotokolle automatisch auf einen Speicherblob hochladen oder den gleichen Vorgang auf Anforderung ausführen können. Weitere Informationen finden Sie im Artikel [Sammlung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview.md).

- Allgemein verfügbare Version der Azure Stack-Netzwerkinfrastruktur-Überprüfung als Teil des Azure Stack-Überprüfungstools **Test-AzureStack**. Die Azure Stack-Netzwerkinfrastrukur stellt einen Teil von **Test-AzureStack** dar und ermöglicht das Bestimmen eines Fehlers in der Netzwerkinfrastruktur von Azure Stack. Der Test überprüft die Konnektivität der Netzwerkinfrastruktur, indem er das softwaredefinierte Netzwerk von Azure Stack umgeht. Er veranschaulicht Verbindungen von einer öffentlichen VIP mit den konfigurierten DNS-Forwardern, NTP-Servern und Identitätsendpunkten. Außerdem überprüft er die Konnektivität mit Azure, wenn Azure AD als Identitätsanbieter verwendet wird, oder mit dem Partnerserver, wenn ADFS verwendet wird. Weitere Informationen finden Sie im Artikel zum [Azure Stack-Überprüfungstool](azure-stack-diagnostic-test.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne SQL-TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Auf dem Azure Stack-Updateblatt wird jetzt eine Zeit für den **Letzten abgeschlossenen Schritt** für aktive Updates angezeigt. Diese kann angezeigt werden, indem auf dem Updateblatt auf ein laufendes Update geklickt wird. Der **Letzte abgeschlossene Schritt** ist dann im Abschnitt **Update-Ausführungsdetails** verfügbar.

- Verbesserungen an den Operatoraktionen **Start-AzureStack** und **Stop-AzureStack**. Die für den Start von Azure Stack erforderliche Zeit wurde um durchschnittlich 50 % reduziert. Die für das Herunterfahren von Azure Stack erforderliche Zeit wurde um durchschnittlich 30 % reduziert. Die durchschnittlichen Zeiten für Start und Herunterfahren bleiben gleich, wenn sich die Anzahl der Knoten in einer Skalierungseinheit erhöht.

- Verbesserte Fehlerbehandlung für das Marketplace-Tool bei getrennter Verbindung. Bei einem Downloadfehler oder einem Teilerfolg wird bei der Verwendung von **Export-AzSOfflineMarketplaceItem** eine detaillierte Fehlermeldung mit weiteren Details zum Fehler und Schritten zur Entschärfung angezeigt, sofern vorhanden.

- Verbesserte Leistung bei der Erstellung verwalteter Datenträger aus einem großen Seitenblob/einer großen Momentaufnahme. Bisher wurde beim Erstellen eines großen Datenträgers ein Zeitlimit ausgelöst.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Verbesserte Integritätsprüfung von virtuellen Datenträgern vor dem Herunterfahren eines Knotens, um ein unerwartetes Trennen virtueller Datenträger zu verhindern.

- Verbesserte Speicherung interner Protokolle für Administratorvorgänge. Dies führt zu einer verbesserten Leistung und Zuverlässigkeit bei Administratorvorgängen, indem die Arbeitsspeicher- und Speicherplatznutzung durch interne Protokollprozesse minimiert wird. Möglicherweise bemerken Sie darüber hinaus verbesserte Seitenladezeiten des Updateblatts im Verwaltungsportal. Im Rahmen dieser Verbesserung stehen Updateprotokolle, die älter als 6 Monate sind, im System nicht mehr zur Verfügung. Wenn Sie Protokolle für diese Updates benötigen, achten Sie darauf, dass Sie für alle Updateausführungen, die älter als 6 Monate sind, [die Zusammenfassung herunterladen](azure-stack-apply-updates.md), bevor Sie das Update 1907 durchführen.

### <a name="changes"></a>Änderungen

- Version 1907 von Azure Stack enthält eine Warnmeldung, in der Operatoren darauf hingewiesen werden, dass sie das OEM-Paket des Systems auf Version 2.1 oder höher aktualisieren müssen, bevor sie ein Update auf Version 1908 ausführen können. Weitere Informationen zum Anwenden von Azure Stack-OEM-Updates finden Sie unter [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md) (Anwenden von Azure Stack-OEM-Updates).

- Es wurde eine neue Ausgangsregel (HTTPS) hinzugefügt, um dem Azure Stack-Sammlungsdienst für Diagnoseprotokolle die Kommunikation zu ermöglichen. Weitere Informationen finden Sie unter [Integration des Azure Stack-Rechenzentrums – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Der Infrastruktur-Sicherungsdienst löscht jetzt teilweise hochgeladene Sicherungen, wenn der externe Speicherort an seine Kapazitätsgrenze stößt.

- Infrastruktursicherungen enthalten keine Sicherung von Domänendienstdaten mehr. Dies betrifft nur Systeme, die Azure Active Directory als Identitätsanbieter verwenden.

- Wir überprüfen jetzt, ob ein Image, das auf dem Blatt **Compute -> VM-Images** erfasst wird, den Typ Seitenblob aufweist.

- Der Befehl **Set-BmcCredential** des privilegierten Endpunkts aktualisiert nun die Anmeldeinformationen im Baseboard-Verwaltungscontroller.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Es wurde ein Problem behoben, bei dem für Herausgeber, Angebote und SKUs in Resource Manager-Vorlagen Groß-/Kleinschreibung unterschieden wurde: Das Image wurde nicht für die Bereitstellung abgerufen, wenn die Imageparameter nicht die identische Groß-/Kleinschreibung wie Herausgeber, Angebot und SKU aufwiesen.

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

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1907 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1906 installieren, bevor Sie Azure Stack auf 1907 aktualisieren.

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1907-update"></a>Vor dem Anwenden des Updates 1907

Version 1907 von Azure Stack muss auf Version 1906 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Nach erfolgreicher Anwendung des Updates 1907

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1907.18.56](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906 – Buildreferenz

Die Buildnummer des Azure Stack-Updates 1906 ist **1.1906.0.30**.

### <a name="update-type"></a>Updatetyp

Der Buildtyp des Azure Stack 1906-Updates lautet **Express**. Weitere Informationen zu Update-Buildtypen finden Sie im Artikel [Verwalten von Updates in Azure Stack](azure-stack-updates.md). Das Update 1906 dauert etwa 10 Stunden, unabhängig von der Anzahl der physischen Knoten in Ihrer Azure Stack-Umgebung. Genaue Laufzeiten des Updates hängen in der Regel von der auf Ihrem System durch Mandantenworkloads verwendeten Kapazität ab, der Netzwerkkonnektivität Ihres Systems (falls mit dem Internet verbunden) und den Spezifikationen Ihrer Systemhardware. Laufzeiten, die länger als den erwarteten Wert dauern, sind nicht ungewöhnlich und erfordern kein Eingreifen durch den Azure Stack-Operator, es sei denn, das Update schlägt fehl. Dieser Näherungswert der Laufzeit ist spezifisch für das Update 1906 und nicht auf andere Azure Stack-Updates übertragbar.

## <a name="whats-in-this-update"></a>Inhalt des Updates

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ein **Set-TLSPolicy**-Cmdlet wurde im privilegierten Endpunkt (PEP) hinzugefügt, um TLS 1.2 für alle Endpunkte zu erzwingen. Weitere Informationen finden Sie unter [Azure Stack-Sicherheitskontrollen](azure-stack-security-configuration.md).

- Ein **Get-TLSPolicy**-Cmdlet wurde im privilegierten Endpunkt (PEP) hinzugefügt, um die angewendete TLS-Richtlinie abzurufen. Weitere Informationen finden Sie unter [Azure Stack-Sicherheitskontrollen](azure-stack-security-configuration.md).

- Ein internes Rotationsverfahren für Geheimnisse wurde hinzugefügt, um interne TLS-Zertifikate nach Bedarf während eines Systemupdates zu rotieren.

- Eine Schutzvorrichtung wurde hinzugefügt, um das Ablaufen interner Geheimnisse zu verhindern, indem die Rotation interner Geheimnisse, falls eine kritische Warnung bei ablaufenden Geheimnissen ignoriert wird, zu erzwingen. Dies sollte nicht als normales Verfahren angesehen und auch nicht so verwendet werden. Die Geheimnisrotation sollte während eines Wartungsfensters eingeplant werden. Weitere Informationen finden Sie unter [Azure Stack: Geheimnisrotation](azure-stack-rotate-secrets.md).

- Visual Studio Code wird jetzt in Azure Stack-Bereitstellungen, die AD FS verwenden, unterstützt.

### <a name="improvements"></a>Verbesserungen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Das Cmdlet **Get-GraphApplication** im privilegierten Endpunkt zeigt jetzt den Fingerabdruck des aktuell verwendeten Zertifikats an. Dies verbessert die Zertifikatverwaltung für Dienstprinzipale, wenn Azure Stack mit AD FS bereitgestellt wird.

- Neue Regeln für die Systemüberwachung wurden hinzugefügt, um die Verfügbarkeit von AD Graph und AD FS zu überprüfen, einschließlich der Möglichkeit zum Auslösen von Warnungen.

- Verbesserungen an der Zuverlässigkeit des Sicherungsressourcenanbieters, wenn der Dienst für die Infrastruktursicherung auf eine andere Instanz verschoben wird.

- Optimierung der Leistung des externen Geheimnisrotationsverfahrens, um eine einheitliche Ausführungszeit bereitzustellen, um die Planung des Wartungsfensters zu erleichtern.

- Das Cmdlet **Test-AzureStack** berichtet jetzt zu internen Geheimnissen, die dabei sind, abzulaufen (kritische Warnungen).

- Ein neuer Parameter ist für das Cmdlet **Register-CustomAdfs** im privilegierten Endpunkt verfügbar, der es ermöglicht, die Überprüfung der Zertifikatssperrliste zu überspringen, wenn die Verbundvertrauensstellung für AD FS konfiguriert wird.

- Das Release 1906 führt bessere Sichtbarkeit des Updatestatus ein, sodass Sie sicher sein können, dass Updates nicht angehalten sind. Dies führt zu einer Erhöhung der Gesamtzahl der Updateschritte, die Operatoren auf dem Blatt **Update** angezeigt werden. Möglicherweise bemerken Sie auch, dass mehr Updateschritte gleichzeitig ausgeführt werden als bei früheren Updates.

#### <a name="networking-updates"></a>Netzwerkupdates

- Die im DHCP-Responder festgelegte Leasedauer wurde so aktualisiert, dass sie mit Azure konsistent ist.

- Verbesserte Wiederholungsraten für den Ressourcenanbieter im Falle der fehlerhaften Bereitstellung von Ressourcen.

- Die SKU-Option **Standard** wurde sowohl aus dem Load Balancer als auch der öffentlichen IP-Adresse entfernt, da dies zurzeit nicht unterstützt wird.

### <a name="changes"></a>Änderungen

- Das Erstellen eine Speicherkontoerfahrung ist jetzt mit Azure konsistent.

- Auslöser für Warnungen bei Ablauf interner Geheimnisse wurden geändert:
  - Warnungen werden jetzt 90 Tage vor dem Ablauf von Geheimnissen ausgelöst.
  - Kritische Warnungen werden jetzt 30 Tage vor dem Ablauf von Geheimnissen ausgelöst.

- Zeichenfolgen im Ressourcenanbieter für die Infrastruktursicherung wurden hinsichtlich konsistenter Terminologie aktualisiert.

### <a name="fixes"></a>Fehlerbehebungen

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Ein Problem wurde behoben, bei dem das Ändern der Größe einer VM mit verwaltetem Datenträger mit einem Fehler **Interner Vorgangsfehler** fehlschlug.

- Ein Problem wurde behoben, bei dem die fehlgeschlagene Erstellung eines Benutzerimages den Dienst, der Images verwaltet, in einen ungültigen Zustand versetzt. Hierdurch wird das Löschen des fehlerhaften Images und die Erstellung neuer Images blockiert. Dies ist auch im Hotfix 1905 behoben.

- Aktive Warnungen wegen abgelaufener interner Geheimnisse werden jetzt nach erfolgreicher Ausführung der internen Geheimnisrotation automatisch geschlossen.

- Ein Problem wurde behoben, bei dem die erste Ziffer der Updatedauer auf der Registerkarte „Updateverlauf“ abgeschnitten wurde, wenn das Update länger als 99 Stunden ausgeführt wurde.

- Das Blatt **Update** enthält eine Option **Fortsetzen** für fehlgeschlagene Updates.

- In den Administrator- und Benutzerportalen wurde das Problem im Marketplace behoben, bei dem die Docker-Erweiterung fehlerhaft von der Suche zurückgegeben wurde, aber keine weitere Maßnahme ergriffen werden konnte, da sie nicht in Azure Stack verfügbar war.

- Ein Problem in der Benutzeroberfläche für die Vorlagenbereitstellung wurde behoben, bei dem Parameter nicht aufgefüllt wurden, wenn der Vorlagenname mit „_“ (Unterstrich) begann.

- Ein Problem auf der Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen wurde behoben, bei dem „CentOS 7.2-basiert“ als Option für die Bereitstellung angeboten wird. CentOS 7.2 ist in Azure Stack nicht verfügbar. Wir stellen jetzt CentOS 7.5 als Option für die Bereitstellung zur Verfügung.

- Sie können jetzt eine Skalierungsgruppe über das Blatt **VM-Skalierungsgruppen** entfernen.

## <a name="security-updates"></a>Sicherheitsupdates

Informationen zu Sicherheitsupdates in diesem Update von Azure Stack finden Sie unter [Azure Stack-Sicherheitsupdates](release-notes-security-updates.md).

## <a name="update-planning"></a>Updateplanung

Stellen Sie vor dem Anwenden des Updates sicher, dass Sie die folgenden Informationen überprüfen:

- [Bekannte Probleme](known-issues.md)
- [Sicherheitsupdates](release-notes-security-updates.md)
- [Azure Stack-Checkliste für Updateaktivitäten](release-notes-checklist.md)

## <a name="download-the-update"></a>Herunterladen des Updates

Sie können das Paket mit dem Azure Stack-Update 1906 auf der [Azure Stack-Downloadseite](https://aka.ms/azurestackupdatedownload) herunterladen.

## <a name="hotfixes"></a>Hotfixes

Azure Stack veröffentlicht regelmäßig Hotfixes. Stellen Sie sicher, dass Sie den aktuellen Azure Stack-Hotfix für 1905 installieren, bevor Sie Azure Stack auf 1906 aktualisieren. Installieren Sie nach der Aktualisierung alle [verfügbaren Hotfixes für Version 1906](#after-successfully-applying-the-1906-update).

Azure Stack-Hotfixes gelten nur für integrierte Azure Stack-Systeme. Versuchen Sie nicht, Hotfixes auf dem ASDK zu installieren.

### <a name="before-applying-the-1906-update"></a>Vor dem Anwenden des Updates 1906

Version 1906 von Azure Stack muss auf Version 1905 mit den folgenden Hotfixes angewendet werden:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Nach erfolgreicher Anwendung des Updates 1906

Installieren Sie nach der Installation dieses Updates alle entsprechenden Hotfixes. Weitere Informationen finden Sie in unserer [Wartungsrichtlinie](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack-Hotfix 1.1906.15.60](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>Automatische Updatebenachrichtigungen

Für Systeme, die über das Infrastrukturnetzwerk auf das Internet zugreifen können, wird im Bedienerportal die Meldung **Update verfügbar** angezeigt. Für Systeme ohne Internetzugriff kann die ZIP-Datei mit der entsprechenden XML-Datei heruntergeladen und installiert werden.

> [!TIP]  
> Abonnieren Sie die folgenden *RSS*- oder *Atom*-Feeds, um im Hinblick auf Azure Stack-Hotfixes auf dem neuesten Stand zu bleiben:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivieren

Um auf archivierte Versionshinweise für eine ältere Version zuzugreifen, verwenden Sie die Dropdown-Auswahlliste oberhalb des Inhaltsverzeichnisses auf der linken Seite, und wählen Sie die gewünschte Version aus.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Updateverwaltung in Azure Stack finden Sie unter [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md).  
- Informationen zur Anwendung von Updates mit Azure Stack finden Sie unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md).
- Informationen zur Wartungsrichtlinie für integrierte Azure Stack-Systeme und dazu, wie Sie vorgehen müssen, um den unterstützten Zustand des Systems aufrechtzuerhalten, finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md).  
- Informationen zum Überwachen und Fortsetzen von Updates mithilfe des privilegierten Endpunkts (PEP) finden Sie unter [Überwachen von Änderungen in Azure Stack mithilfe des privilegierten Endpunkts](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1906"
Sie können auf [ältere Versionen der Azure Stack-Versionshinweise im TechNet-Katalog](https://aka.ms/azsarchivedrelnotes) zugreifen. Diese archivierten Dokumente werden nur zu Referenzzwecken bereitgestellt und bedeuten nicht, dass Support für diese Versionen geleistet wird. Informationen zum Azure Stack-Support finden Sie unter [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md). Weitere Hilfe erhalten Sie beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS).
::: moniker-end


