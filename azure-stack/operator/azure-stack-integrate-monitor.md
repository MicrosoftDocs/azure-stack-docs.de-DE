---
title: Integrieren einer externen Überwachungslösung mit Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Stack mit einer externen Überwachungslösung in Ihr Rechenzentrum integrieren können.
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
ms.date: 02/06/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 2871b5183833830368307c5d2b5152e3909fd3ea
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618834"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrieren einer externen Überwachungslösung mit Azure Stack

Für die externe Überwachung der Azure Stack-Infrastruktur müssen Sie die Azure Stack-Software, die physischen Computer und die physischen Netzwerkswitches überwachen. Jeder dieser Bereiche bietet eine Methode zum Abrufen von Integritäts- und Warnungsinformationen:

- Die Azure Stack-Software bietet eine REST-basierte API zum Abrufen der Integrität und von Warnungen. Die Verwendung von softwaredefinierten Technologien wie direkten Speicherplätzen, Speicherintegrität und Warnungen ist Teil der Softwareüberwachung.
- Physische Computer können Integritäts- und Warnungsinformationen über die Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMCs) zur Verfügung stellen.
- Physische Netzwerkgeräte können Integritäts- und Warnungsinformationen über das SNMP-Protokoll zur Verfügung stellen.

Jede Azure Stack-Lösung wird mit einem Hardwarelebenszyklushost ausgeliefert. Auf diesem Host wird die Überwachungssoftware des OEM-Hardwareanbieters (Originalgerätehersteller) für die physischen Server und Netzwerkgeräte ausgeführt. Informieren Sie sich bei Ihrem OEM-Anbieter, ob dessen Überwachungslösungen in bestehende Überwachungslösungen in Ihrem Rechenzentrum integriert werden können.

> [!IMPORTANT]
> Die von Ihnen verwendete externe Überwachungslösung darf keine Agenten verwenden. Sie können keine Agenten von Drittanbietern in Azure Stack-Komponenten installieren.

Das folgende Diagramm zeigt den Datenverkehrsfluss zwischen einem integrierten Azure Stack-System, dem Hardwarelebenszyklushost, einer externen Überwachungslösung und einem externen Ticketausstellungs-/Datensammlungssystem.

![Das Diagramm zeigt den Datenverkehr zwischen Azure Stack, der Überwachungs- und der Ticketausstellungslösung.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Die direkte Integration externer Überwachung mit physischen Servern ist nicht zulässig und wird aktiv durch Zugriffssteuerungslisten (ACLs) blockiert.  Die direkte Integration externer Überwachung mit physischen Netzwerkgeräten wird unterstützt. Informieren Sie sich bei Ihrem OEM-Anbieter, wie dieses Feature aktiviert wird.

In diesem Artikel wird beschrieben, wie Sie Azure Stack in externe Überwachungslösungen wie System Center Operations Manager und Nagios integrieren können. Außerdem wird beschrieben, wie Sie mithilfe von PowerShell oder über REST-API-Aufrufe programmgesteuert mit Warnungen arbeiten können.

## <a name="integrate-with-operations-manager"></a>Integrieren in Operations Manager

Sie können Operations Manager für die externe Überwachung von Azure Stack verwenden. Mit dem System Center-Management Pack für Microsoft Azure Stack können Sie mehrere Azure Stack-Implementierungen mit einer einzigen Operations Manager-Instanz überwachen. Das Management Pack verwendet die REST-APIs des Integritätsressourcenanbieters und des Updateressourcenanbieters, um mit Azure Stack zu kommunizieren. Wenn Sie planen, die auf dem Hardwarelebenszyklushost ausgeführte OEM-Überwachungssoftware zu umgehen, können Sie Vendor Management Packs zur Überwachung physischer Server installieren. Sie können die Operations Manager-Netzwerkgeräteerkennung auch zur Überwachung von Netzwerkswitches verwenden.

Das Management Pack für Azure Stack bietet die folgenden Funktionen:

- Sie können mehrere Azure Stack-Bereitstellungen verwalten.
- Azure Active Directory (Azure AD) und Active Directory-Verbunddienste (AD FS) werden unterstützt.
- Sie können Warnungen abrufen und schließen.
- Es gibt jeweils ein Dashboard für Integrität und Kapazität.
- Umfasst die Erkennung des automatischen Wartungsmodus, wenn Patch und Update (P&U) ausgeführt wird.
- Umfasst Aufgaben zum Erzwingen von Updates für die Bereitstellung und die Region.
- Sie können benutzerdefinierte Informationen zu einer Region hinzufügen.
- Unterstützt die Benachrichtigung und Berichterstellung.

Sie können das System Center Management Pack für Microsoft Azure Stack und das zugehörige Benutzerhandbuch [hier](https://www.microsoft.com/en-us/download/details.aspx?id=55184) oder direkt über Operations Manager herunterladen.

Für eine Ticketausstellungslösung können Sie Operations Manager in System Center Service Manager integrieren. Der integrierte Produktconnektor ermöglicht eine bidirektionale Kommunikation, die es Ihnen ermöglicht, eine Warnung in Azure Stack und Operations Manager zu schließen, nachdem Sie eine Serviceanfrage in Service Manager aufgelöst haben.

Das folgende Diagramm zeigt die Integration von Azure Stack in eine bestehende System Center-Bereitstellung. Sie können Service Manager mit System Center Orchestrator oder Service Management Automation (SMA) weiter automatisieren, um Vorgänge in Azure Stack auszuführen.

![Das Diagramm zeigt die Integration in OM, Service Manager und SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrieren in Nagios

Zusammen mit den Cloudbase-Partnerlösungen wurde ein Plug-In für die Nagios-Überwachung entwickelt, das unter der freien Softwarelizenz – MIT (Massachusetts Institute of Technology) – verfügbar ist.

Das Plug-In ist in Python geschrieben und nutzt die REST-API des Integritätsressourcenanbieters. Es bietet grundlegende Funktionen zum Abrufen und Schließen von Warnungen in Azure Stack. Ähnlich wie beim System Center Management Pack können Sie damit mehrere Azure Stack-Implementierungen hinzufügen und Benachrichtigungen versenden.

Das Plug-In funktioniert mit Nagios Enterprise und Nagios Core. Sie können es [hier](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)herunterladen. Die Downloadwebsite enthält auch Installations- und Konfigurationsdetails.

### <a name="plugin-parameters"></a>Plug-In-Parameter

Konfigurieren Sie die Plug-In-Datei „Azurestack_plugin.py“ mit den folgenden Parametern:

| Parameter | BESCHREIBUNG | Beispiel |
|---------|---------|---------|
| *ARM_Endpunkt* | Azure Resource Manager-Endpunkt (Administrator) | https://adminmanagement.local.azurestack.external |
| *API_Endpunkt* | Azure Resource Manager-Endpunkt (Administrator)  | https://adminmanagement.local.azurestack.external |
| *Mandanten_ID* | Admin-Abonnement-ID | Abruf über das Administratorportal oder PowerShell |
| *Benutzername* | Benutzername für Operatorabonnement | operator@myazuredirectory.onmicrosoft.com |
| *Benutzerkennwort* | Kennwort für Operatorabonnement | mypassword |
| *Client_ID* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *Region* |  Name der Azure Stack-Region | local |
|  |  |

*Die bereitgestellte PowerShell-GUID ist universell. Sie können sie für jede Bereitstellung verwenden.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Verwenden von PowerShell zum Überwachen von Integrität und Warnungen

Wenn Sie weder Operations Manager, Nagios noch eine auf Nagios basierende Lösung verwenden, können Sie mit PowerShell eine breite Palette von Überwachungslösungen für die Integration in Azure Stack ermöglichen.

1. Um PowerShell zu verwenden, stellen Sie sicher, dass [PowerShell für eine Azure Stack-Operatorumgebung installiert und konfiguriert ist](azure-stack-powershell-install.md). Installieren Sie PowerShell auf einem lokalen Computer, der den Resource Manager-Endpunkt (Administrator) erreichen kann (https://adminmanagement.[Region].[Externer_FQDN]).

2. Führen Sie die folgenden Befehle aus, um sich mit der Azure Stack-Umgebung als Azure Stack-Operator zu verbinden:

   ```powershell
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Verwenden Sie Befehle wie die folgenden Beispiele, um mit Warnungen zu arbeiten:
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Weitere Informationen

Informationen zur integrierten Integritätsüberwachung finden Sie unter [Überwachen von Integrität und Warnungen in Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Nächste Schritte

[Integrieren von Azure Stack-Sicherheitsfunktionen in Datencenter-Sicherheitslösungen](azure-stack-integrate-security.md)
