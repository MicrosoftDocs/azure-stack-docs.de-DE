---
title: Integrieren einer externen Überwachungslösung mit Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Stack Hub mit einer externen Überwachungslösung in Ihr Rechenzentrum integrieren können.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: e9e66908cd42a72629caa12163c0c56790cae82f
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811361"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack-hub"></a>Integrieren einer externen Überwachungslösung mit Azure Stack Hub

Für die externe Überwachung der Azure Stack Hub-Infrastruktur müssen Sie die Azure Stack Hub-Software, die physischen Computer und die physischen Netzwerkswitches überwachen. Jeder dieser Bereiche bietet eine Methode zum Abrufen von Integritäts- und Warnungsinformationen:

- Die Azure Stack Hub-Software bietet eine REST-basierte API zum Abrufen der Integrität und von Warnungen. Die Verwendung von softwaredefinierten Technologien wie direkten Speicherplätzen, Speicherintegrität und Warnungen ist Teil der Softwareüberwachung.
- Physische Computer können Integritäts- und Warnungsinformationen über die Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMCs) zur Verfügung stellen.
- Physische Netzwerkgeräte können Integritäts- und Warnungsinformationen über das SNMP-Protokoll zur Verfügung stellen.

Jede Azure Stack Hub-Lösung wird mit einem Hardwarelebenszyklushost ausgeliefert. Auf diesem Host wird die Überwachungssoftware des OEM-Hardwareanbieters (Original Equipment Manufacturer, Originalgerätehersteller) für die physischen Server und Netzwerkgeräte ausgeführt. Informieren Sie sich bei Ihrem OEM-Anbieter, ob dessen Überwachungslösungen in bestehende Überwachungslösungen in Ihrem Rechenzentrum integriert werden können.

> [!IMPORTANT]
> Die von Ihnen verwendete externe Überwachungslösung darf keine Agenten verwenden. Sie können keine Agents von Drittanbietern in Azure Stack Hub-Komponenten installieren.

Das folgende Diagramm zeigt den Datenverkehrsfluss zwischen einem in Azure Stack Hub integrierten System, dem Hardwarelebenszyklushost, einer externen Überwachungslösung und einem externen Ticketausstellungs-/Datensammlungssystem.

![Diagramm zur Anzeige des Datenverkehrs zwischen Azure Stack Hub, der Überwachungs- und der Ticketausstellungslösung](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Die direkte Integration externer Überwachung mit physischen Servern ist nicht zulässig und wird aktiv durch Zugriffssteuerungslisten (Access Control Lists, ACLs) blockiert. Die direkte Integration externer Überwachung mit physischen Netzwerkgeräten wird unterstützt. Erkundigen Sie sich bei Ihrem OEM-Anbieter, wie Sie dieses Feature aktivieren können.

In diesem Artikel wird beschrieben, wie Sie Azure Stack Hub in externe Überwachungslösungen wie System Center Operations Manager und Nagios integrieren können. Außerdem wird beschrieben, wie Sie mithilfe von PowerShell oder über REST-API-Aufrufe programmgesteuert mit Warnungen arbeiten können.

## <a name="integrate-with-operations-manager"></a>Integrieren in Operations Manager

Sie können Operations Manager für die externe Überwachung von Azure Stack Hub verwenden. Mit dem System Center-Management Pack für Microsoft Azure Stack Hub können Sie mehrere Azure Stack Hub-Bereitstellungen mit einer einzigen Operations Manager-Instanz überwachen. Das Management Pack verwendet die REST-APIs des Integritätsressourcenanbieters und des Updateressourcenanbieters, um mit Azure Stack Hub zu kommunizieren. Wenn Sie planen, die auf dem Hardwarelebenszyklushost ausgeführte OEM-Überwachungssoftware zu umgehen, können Sie Vendor Management Packs zur Überwachung physischer Server installieren. Sie können die Operations Manager-Netzwerkgeräteerkennung auch zur Überwachung von Netzwerkswitches verwenden.

Das Management Pack für Azure Stack Hub bietet die folgenden Funktionen:

- Sie können mehrere Azure Stack Hub-Bereitstellungen verwalten.
- Es werden Azure Active Directory (Azure AD) und Active Directory-Verbunddienste (AD FS) unterstützt.
- Sie können Warnungen abrufen und schließen.
- Es gibt jeweils ein Dashboard für Integrität und Kapazität.
- Umfasst die Erkennung des automatischen Wartungsmodus, wenn Patch und Update (P&U) ausgeführt wird.
- Umfasst Aufgaben zum Erzwingen von Updates für die Bereitstellung und die Region.
- Sie können benutzerdefinierte Informationen zu einer Region hinzufügen.
- Unterstützt die Benachrichtigung und Berichterstellung.

Sie können das System Center Management Pack für Microsoft Azure Stack Hub und den zugehörige Benutzerleitfaden [hier](https://www.microsoft.com/en-us/download/details.aspx?id=55184) herunterladen. Sie können es auch direkt über Operations Manager herunterladen.

Für eine Ticketausstellungslösung können Sie Operations Manager in System Center Service Manager integrieren. Der integrierte Produktconnector ermöglicht eine bidirektionale Kommunikation, wodurch Sie eine Warnung in Azure Stack Hub und Operations Manager schließen können, nachdem Sie eine Service Request in Service Manager gelöst haben.

Das folgende Diagramm zeigt die Integration von Azure Stack Hub in eine vorhandene System Center-Bereitstellung. Sie können Service Manager mit System Center Orchestrator oder Service Management Automation (SMA) weiter automatisieren, um Vorgänge in Azure Stack Hub auszuführen.

![Das Diagramm zeigt die Integration in OM, Service Manager und SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrieren in Nagios

Sie können das Nagios-Plug-In für Microsoft Azure Stack Hub einrichten und konfigurieren.

Zusammen mit den Cloudbase-Partnerlösungen wurde ein Plug-In für die Nagios-Überwachung entwickelt, das unter der freien Softwarelizenz – MIT (Massachusetts Institute of Technology) – verfügbar ist.

Das Plug-In ist in Python geschrieben und nutzt die REST-API des Integritätsressourcenanbieters. Es bietet grundlegende Funktionen zum Abrufen und Schließen von Warnungen in Azure Stack Hub. Ähnlich wie beim System Center Management Pack können Sie damit mehrere Azure Stack Hub-Bereitstellungen hinzufügen und Benachrichtigungen versenden.

Für Version 1.2 des Azure Stack Hub-Nagios-Plug-Ins wird die Active Directory-Authentifizierungsbibliothek von Microsoft genutzt und die Authentifizierung per Dienstprinzipal mit einem Geheimnis oder Zertifikat unterstützt. Außerdem wurde die Konfiguration vereinfacht, indem eine einfache Konfigurationsdatei mit neuen Parametern verwendet wird. Es werden ab sofort Azure Stack Hub-Bereitstellungen unter Verwendung von Azure AD und AD FS als Identitätssystem unterstützt.

Das Plug-In funktioniert für Nagios 4x und XI. Informationen zum Herunterladen des Plug-Ins finden Sie unter [Überwachen von Azure Stack Hub-Warnungen](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Die Downloadwebsite enthält auch Installations- und Konfigurationsdetails.

### <a name="requirements-for-nagios"></a>Anforderungen für Nagios

1. Nagios-Mindestversion: 4.x

2. Microsoft Azure Active Directory-Python-Bibliothek. Für die Installation dieser Bibliothek kann Python PIP verwendet werden.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Installieren des Plug-Ins

In diesem Abschnitt wird beschrieben, wie Sie das Azure Stack Hub-Plug-In installieren. Hierbei wird vorausgesetzt, dass eine Standardinstallation von Nagios vorhanden ist.

Das Plug-In-Paket enthält die folgenden Dateien:

```
azurestack_plugin.py
azurestack_handler.sh
samples/etc/azurestack.cfg
samples/etc/azurestack_commands.cfg
samples/etc/azurestack_contacts.cfg
samples/etc/azurestack_hosts.cfg
samples/etc/azurestack_services.cfg
```

1. Kopieren Sie das Plug-In `azurestack_plugin.py` in das Verzeichnis `/usr/local/nagios/libexec`.

2. Kopieren Sie den Handler `azurestack_handler.sh` in das Verzeichnis `/usr/local/nagios/libexec/eventhandlers`.

3. Legen Sie unbedingt fest, dass die Plug-In-Datei ausführbar ist:

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Konfigurieren des Plug-Ins

Die folgenden Parameter können in der Datei „azurestack.cfg“ konfiguriert werden. Fett formatierte Parameter müssen unabhängig vom gewählten Authentifizierungsmodell konfiguriert werden.

Weitere Informationen zum Erstellen eines SPN finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](azure-stack-create-service-principals.md).

| Parameter | Beschreibung | Authentifizierung |
| --- | --- | --- |
| **External_domain_fqdn ** | Externer vollqualifizierter Domänenname |    |
| **region: ** | Name der Region |    |
| **tenant_id: ** | Mandanten-ID\* |    |
| client_id: | Client-ID | SPN mit Geheimnis |
| client_secret: | Clientkennwort | SPN mit Geheimnis |
| client_cert\*\*: | Pfad zum Zertifikat | SPN mit Zertifikat |
| client_cert_thumbprint\*\*: | Zertifikatfingerabdruck | SPN mit Zertifikat |

\*Die Mandanten-ID ist für Azure Stack Hub-Bereitstellungen mit AD FS nicht erforderlich.

\*\* Der geheime Clientschlüssel und das Clientzertifikat schließen sich gegenseitig aus.

Die anderen Konfigurationsdateien enthalten optionale Konfigurationseinstellungen, da sie auch in Nagios konfiguriert werden können.

> [!Note]  
> Prüfen Sie den Zielspeicherort in „azurestack_hosts.cfg“ und „azurestack_services.cfg“.

| Konfiguration | Beschreibung |
| --- | --- |
| azurestack_commands.cfg | Handlerkonfiguration: Anforderung „Keine Änderungen“ |
| azurestack_contacts.cfg | Benachrichtigungseinstellungen |
| azurestack_hosts.cfg | Azure Stack Hub-Bereitstellungsbenennung |
| azurestack_services.cfg | Konfiguration des Diensts |

### <a name="setup-steps"></a>Einrichtungsschritte

1. Ändern Sie die Konfigurationsdatei.

2. Kopieren Sie die geänderten Konfigurationsdateien in den Ordner `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>Aktualisieren der Nagios-Konfiguration

Die Nagios-Konfiguration muss aktualisiert werden, um sicherzustellen, dass das Azure Stack Hub-Nagios-Plug-In geladen wird.

1. Öffnen Sie die folgende Datei:

   ```bash  
   /usr/local/nagios/etc/nagios.cfg
   ```

2. Fügen Sie den folgenden Eintrag hinzu:

   ```bash  
   # Load the Azure Stack Hub Plugin Configuration
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
   ```

3. Laden Sie Nagios neu.

   ```bash  
   sudo service nagios reload
   ```

### <a name="manually-close-active-alerts"></a>Manuelles Schließen von aktiven Warnungen

Aktive Warnungen können in Nagios mit der benutzerdefinierten Benachrichtigungsfunktion geschlossen werden. Für die benutzerdefinierte Benachrichtigung muss Folgendes gelten:

```
/close-alert <ALERT_GUID>
```

Eine Warnung kann auch geschlossen werden, indem in einem Terminal der folgende Befehl verwendet wird:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>Problembehandlung

Die Problembehandlung für das Plug-In wird durchgeführt, indem das Plug-In in einem Terminal manuell aufgerufen wird. Verwenden Sie die folgende Methode:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Verwenden von PowerShell zum Überwachen von Integrität und Warnungen

Wenn Sie weder Operations Manager, Nagios noch eine auf Nagios basierende Lösung verwenden, können Sie mit PowerShell eine umfangreiche Palette von Überwachungslösungen für die Integration in Azure Stack Hub unterstützen.

1. Um PowerShell zu verwenden, stellen Sie sicher, dass [PowerShell für eine Azure Stack Hub-Operatorumgebung installiert und konfiguriert ist](azure-stack-powershell-install.md). Installieren Sie PowerShell auf einem lokalen Computer, der den Resource Manager-Endpunkt (Administrator) erreichen kann (https://adminmanagement.[Region].[Externer_FQDN]).

2. Führen Sie die folgenden Befehle aus, um sich mit der Azure Stack Hub-Umgebung als Azure Stack Hub-Operator zu verbinden:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Verwenden Sie Befehle wie die folgenden Beispiele, um mit Warnungen zu arbeiten:
   ```powershell
    # Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    # Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    # Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    # Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Weitere Informationen

Informationen zur integrierten Integritätsüberwachung finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack Hub](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Nächste Schritte

[Integrieren von Azure Stack-Sicherheitsfunktionen in Datencenter-Sicherheitslösungen](azure-stack-integrate-security.md)
