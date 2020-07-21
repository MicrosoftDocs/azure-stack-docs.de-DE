---
title: Referenz zu privilegierten Azure Stack Hub-Endpunkten
description: Referenz zu privilegierten Azure Stack-Endpunkten für PowerShell
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 76eb340da04e9254bcf8d8a626822c65362f44d2
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486035"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Referenz zu privilegierten Azure Stack Hub-Endpunkten

Der privilegierte Azure Stack-Endpunkt (PEP) für PowerShell ist eine vorkonfigurierte Remote-PowerShell-Konsole, die Ihnen gerade ausreichende Funktionen zur Verfügung stellt, damit Sie eine erforderliche Aufgabe ausführen können. Der Endpunkt nutzt PowerShell JEA (Just Enough Administration, minimale Verwaltung), um nur einen eingeschränkten Satz von Cmdlets verfügbar zu machen.

## <a name="privilege-endpoint-cmdlets"></a>Cmdlets für privilegierte Endpunkte

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [Close-PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | Keine Beschreibung |
| [Get-ActionStatus](Get-ActionStatus.md) | Dient zum Abrufen des Status der letzten Aktion für den Vorgang mit dem angegebenen Funktionsnamen. |
| [Get-AzureStackLog](Get-AzureStackLog.md) | Dient zum Abrufen von Protokollen aus verschiedenen AzureStack-Rollen mit Timeout. |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | Ruft die Stempelinformationen ab. |
| [Get-AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | Ruft die Konfigurationseinstellungen des Supportdiensts ab. |
| [Get-CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | Keine Beschreibung |
| [Get-CloudAdminUserList](Get-CloudAdminUserList.md) | Keine Beschreibung |
| [Get-ClusterLog](Get-ClusterLog.md) | Keine Beschreibung |
| [Get-GraphApplication](Get-GraphApplication.md) | „Get-GraphApplication“ ist eine Wrapperfunktion zum Abrufen der Graph-Anwendungsinformationen für den angegebenen Anwendungsnamen oder -bezeichner. |
| [Get-StorageJob](Get-StorageJob.md) | Keine Beschreibung |
| [Get-SupportSessionInfo](Get-SupportSessionInfo.md) | Keine Beschreibung |
| [Get-SupportSessionToken](Get-SupportSessionToken.md) | Keine Beschreibung |
| [Get-SyslogClient](Get-SyslogClient.md) | Ruft die Einstellungen des Syslog-Clients ab. |
| [Get-SyslogServer](Get-SyslogServer.md) | Ruft den Syslog-Serverendpunkt ab. |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | Keine Beschreibung |
| [Get-TLSPolicy](Get-TLSPolicy.md) | Keine Beschreibung |
| [Get-VirtualDisk](Get-VirtualDisk.md) | Keine Beschreibung |
| [Invoke-AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | Generiert ggf. bedarfsgesteuerte Protokolle aus AzureStack-Rollen. |
| [New-AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | Erstellt einen neuen Dienstprinzipal in Azure Active Directory. |
| [New-AzureStackActivation](New-AzureStackActivation.md) | Dient zum Aktivieren von Azure Stack. |
| [New-CloudAdminUser](New-CloudAdminUser.md) | Keine Beschreibung |
| [New-GraphApplication](New-GraphApplication.md) | „New-GraphApplication“ ist eine Wrapperfunktion zum Aufrufen von AD FS-Graph-Cmdlets in AD FS. |
| [New-RegistrationToken](New-RegistrationToken.md) | Erstellt ein neues Registrierungstoken. |
| [Register-CustomAdfs](Register-CustomAdfs.md) | Skript zum Registrieren einer benutzerdefinierten AD FS-Instanz (Active Directory Federation Services, Active Directory-Verbunddienste) als Anspruchsanbieter mit Azure Stack AD FS |
| [Register-CustomDnsServer](Register-CustomDnsServer.md) | Skript zum Registrieren von benutzerdefinierten DNS-Servern mit Azure Stack-DNS |
| [Register-DirectoryService](Register-DirectoryService.md) | Skript zum Registrieren einer benutzerdefinierten Active Directory-Instanz beim Graph-Dienst |
| [Remove-AzureStackActivation](Remove-AzureStackActivation.md) | Keine Beschreibung |
| [Remove-CloudAdminUser](Remove-CloudAdminUser.md) | Keine Beschreibung |
| [Remove-GraphApplication](Remove-GraphApplication.md) | „Remove-GraphApplication“ ist eine Wrapperfunktion zum Aufrufen von AD FS-Graph-Cmdlets in AD FS. |
| [Repair-VirtualDisk](Repair-VirtualDisk.md) | Keine Beschreibung |
| [Reset-DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | Skript zum Zurücksetzen von Änderungen der Rechenzentrumsintegration |
| [Send-AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | Sendet Azure Stack-Diagnoseprotokolle an Microsoft. |
| [Set-CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | Keine Beschreibung |
| [Set-GraphApplication](Set-GraphApplication.md) | „Set-GraphApplication“ ist eine Wrapperfunktion zum Aufrufen von AD FS-Graph-Cmdlets in AD FS. |
| [Set-ServiceAdminOwner](Set-ServiceAdminOwner.md) | Skript zum Aktualisieren des Dienstadministrators |
| [Set-SyslogClient](Set-SyslogClient.md) | Importiert das Endpunktzertifikat für den Syslog-Client und wendet es an. |
| [Set-SyslogServer](Set-SyslogServer.md) | Legt den Syslog-Serverendpunkt fest. |
| [Set-Telemetry](Set-Telemetry.md) | Aktiviert oder deaktiviert die Übertragung von Telemetriedaten an Microsoft. |
| [Set-TLSPolicy](Set-TLSPolicy.md) | Keine Beschreibung |
| [Start-AzureStack](Start-AzureStack.md) | Startet alle Azure Stack-Dienste. |
| [Start-SecretRotation](Start-SecretRotation.md) | Löst die Geheimnisrotation für einen Stempel aus. |
| [Stop-AzureStack](Stop-AzureStack.md) | Beendet alle Azure Stack-Dienste. |
| [Test-AzureStack](Test-AzureStack.md) | Überprüft den Status von Azure Stack. |
| [Unlock-SupportSession](Unlock-SupportSession.md) | Keine Beschreibung |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum privilegierten Endpunkt in Azure Stack Hub finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
