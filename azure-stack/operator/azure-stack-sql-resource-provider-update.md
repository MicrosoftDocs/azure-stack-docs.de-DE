---
title: Aktualisieren des SQL-Ressourcenanbieters in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den SQL-Ressourcenanbieter in Azure Stack aktualisieren können.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 6554a18ee881f053eebc2fc3df8d846cd8d9d12e
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417957"
---
# <a name="update-the-sql-resource-provider"></a>Aktualisieren des SQL-Ressourcenanbieters

*Anwendungsbereich: Integrierte Azure Stack-Systeme.*

Unter Umständen wird ein neuer SQL-Ressourcenanbieter veröffentlicht, wenn Azure Stack auf einen neuen Build aktualisiert wird. Auch wenn der vorhandene Ressourcenanbieter weiterhin funktioniert, ist es ratsam, so schnell wie möglich das Update auf den aktuellen Build durchzuführen. 

Ab Ressourcenanbieter SQL RP Version 1.1.33.0 sind die Updates kumulativ und müssen nicht in der Reihenfolge installiert werden, in der sie veröffentlicht wurden, sofern Sie mit Version 1.1.24.0 oder höher beginnen. Wenn Sie beispielsweise Version 1.1.24.0 des SQL-Ressourcenanbieters ausführen, können Sie dann ein Upgrade auf Version 1.1.33.0 oder höher ausführen, ohne zuerst Version 1.1.30.0 installieren zu müssen. In der Versionsliste unter [Bereitstellen des Ressourcenanbieters – Voraussetzungen](./azure-stack-sql-resource-provider-deploy.md#prerequisites) finden Sie die verfügbaren Ressourcenanbieterversionen und die jeweilige Azure Stack-Version, auf der sie unterstützt werden.

Verwenden Sie zum Aktualisieren des Ressourcenanbieters das Skript *UpdateSQLProvider.ps1*. Das Skript ist im Download des neuen SQL-Ressourcenanbieters enthalten. Der Updateprozess ähnelt dem Prozess zum [Bereitstellen des Ressourcenanbieters](./azure-stack-sql-resource-provider-deploy.md). Das Updateskript verwendet die gleichen Argumente wie das Skript „DeploySqlProvider.ps1“, und Sie müssen Zertifikatinformationen angeben.

 > [!IMPORTANT]
 > Überprüfen Sie vor dem Upgrade des Ressourcenanbieters die Versionshinweise auf Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen, die sich auf die Bereitstellung auswirken können.

## <a name="update-script-processes"></a>Aktualisieren von Skriptprozessen

Das Skript *UpdateSQLProvider.ps1* erstellt einen neuen virtuellen Computer (VM) mit dem neuesten Ressourcenanbietercode.

> [!NOTE]
> Wir empfehlen Ihnen, das aktuelle Windows Server 2016 Core-Image aus der Marketplace-Verwaltung herunterzuladen. Wenn Sie ein Update installieren müssen, können Sie unter dem lokalen Abhängigkeitspfad ein **einzelnes** MSU-Paket platzieren. Beim Skript tritt ein Fehler auf, wenn an diesem Speicherort mehrere MSU-Dateien vorhanden sind.

Nachdem das Skript *UpdateSQLProvider.ps1* eine neue VM erstellt hat, werden die folgenden Einstellungen aus der alten Anbieter-VM migriert:

* Datenbankinformationen
* Hostserverinformationen
* Erforderlicher DNS-Eintrag

## <a name="update-script-parameters"></a>Aktualisieren von Skriptparametern

Wenn Sie das PowerShell-Skript **UpdateSQLProvider.ps1** ausführen, können Sie die folgenden Parameter in der Befehlszeile angeben. Wenn Sie keine Parameter angeben oder bei der Überprüfung eines Parameters ein Fehler auftritt, werden Sie aufgefordert, die erforderlichen Parameter anzugeben.

| Parametername | BESCHREIBUNG | Kommentar oder Standardwert |
| --- | --- | --- |
| **CloudAdminCredential** | Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind. | _Erforderlich_ |
| **AzCredential** | Die Anmeldeinformationen für das Azure Stack-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack. | _Erforderlich_ |
| **VMLocalCredential** | Die Anmeldeinformationen für das lokale Administratorkonto des virtuellen Computers mit dem SQL-Ressourcenanbieter. | _Erforderlich_ |
| **PrivilegedEndpoint** | Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts. |  _Erforderlich_ |
| **AzureEnvironment** | Die zum Bereitstellen von Azure Stack verwendete Azure-Umgebung des Dienstadministratorkontos. Nur für Azure AD-Bereitstellungen erforderlich. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein chinesisches Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Ihre Zertifikatdatei (.pfx) muss ebenfalls in diesem Verzeichnis abgelegt werden. | _Optional für einzelne Knoten, aber obligatorisch für mehrere Knoten_ |
| **DefaultSSLCertificatePassword** | Das Kennwort für das PFX-Zertifikat. | _Erforderlich_ |
| **MaxRetryCount** | Die Anzahl von Wiederholungsversuchen für jeden Vorgang, wenn ein Fehler auftritt.| 2 |
| **RetryDuration** |Das Timeoutintervall zwischen Wiederholungen in Sekunden. | 120 |
| **Deinstallieren** | Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen. | Nein |
| **DebugMode** | Verhindert die automatische Bereinigung nach einem Fehler. | Nein |

## <a name="update-script-powershell-example"></a>Beispiel für ein PowerShell-Aktualisierungsskript
Nachstehend finden Sie ein Beispiel für das Skript *UpdateSQLProvider.ps1*, das Sie an einer PowerShell-Konsole mit erhöhten Rechten ausführen können. Achten Sie darauf, die Variableninformationen und Kennwörter wie erforderlich zu ändern:  

> [!NOTE]
> Dieser Updateprozess gilt nur für integrierte Azure Stack-Systeme.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert 

 ```

## <a name="next-steps"></a>Nächste Schritte

[Wartungsvorgänge](azure-stack-sql-resource-provider-maintain.md)
