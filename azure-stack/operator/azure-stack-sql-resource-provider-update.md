---
title: Aktualisieren des SQL-Ressourcenanbieters in Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie den SQL-Ressourcenanbieter in Azure Stack Hub aktualisieren.
author: bryanla
ms.topic: article
ms.date: 11/11/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 09a9b190594eb4d737b54c971fc2e3dbd03cd5f9
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491746"
---
# <a name="update-the-sql-resource-provider"></a>Aktualisieren des SQL-Ressourcenanbieters

Unter Umständen wird ein neuer SQL-Ressourcenanbieter veröffentlicht, wenn Azure Stack Hub auf einen neuen Build aktualisiert wird. Auch wenn der vorhandene Ressourcenanbieter weiterhin funktioniert, ist es ratsam, so schnell wie möglich das Update auf den aktuellen Build durchzuführen.

Ab der Version 1.1.33.0 des SQL-Ressourcenanbieters sind Updates kumulativ und müssen nicht mehr in der Reihenfolge installiert werden, in der sie veröffentlicht wurden (vorausgesetzt, Sie verfügen mindestens über die Version 1.1.24.0). Wenn Sie also beispielsweise die Version 1.1.24.0 des SQL-Ressourcenanbieters verwenden, können Sie auf die Version 1.1.33.0 oder höher upgraden, ohne zuvor die Version 1.1.30.0 installieren zu müssen. In der Versionsliste unter [Bereitstellen des Ressourcenanbieters – Voraussetzungen](./azure-stack-sql-resource-provider-deploy.md#prerequisites) finden Sie die verfügbaren Ressourcenanbieterversionen und die jeweilige Azure Stack Hub-Version, unter der sie unterstützt werden.

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
| **AzCredential** | Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub. | _Erforderlich_ |
| **VMLocalCredential** | Die Anmeldeinformationen für das lokale Administratorkonto des virtuellen Computers mit dem SQL-Ressourcenanbieter. | _Erforderlich_ |
| **PrivilegedEndpoint** | Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts. |  _Erforderlich_ |
| **AzureEnvironment** | Die zum Bereitstellen von Azure Stack Hub verwendete Azure-Umgebung des Dienstadministratorkontos. Nur für Azure AD-Bereitstellungen erforderlich. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein chinesisches Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Ihre Zertifikatdatei (.pfx) muss ebenfalls in diesem Verzeichnis abgelegt werden. | _Optional für einzelne Knoten, aber obligatorisch für mehrere Knoten_ |
| **DefaultSSLCertificatePassword** | Das Kennwort für das PFX-Zertifikat. | _Erforderlich_ |
| **MaxRetryCount** | Die Anzahl von Wiederholungsversuchen für jeden Vorgang, wenn ein Fehler auftritt.| 2 |
| **RetryDuration** |Das Timeoutintervall zwischen Wiederholungen in Sekunden. | 120 |
| **Deinstallieren** | Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen. | Nein |
| **DebugMode** | Verhindert die automatische Bereinigung nach einem Fehler. | Nein |

## <a name="update-script-powershell-example"></a>Beispiel für ein PowerShell-Aktualisierungsskript
> [!NOTE]
> Dieser Updateprozess gilt nur für integrierte Azure Stack Hub-Systeme.

Wenn Sie auf die Version 1.1.33.0 oder auf frühere Versionen des SQL-Ressourcenanbieters aktualisieren, müssen Sie bestimmte Versionen von AzureRm.BootStrapper und Azure Stack Hub-Modulen in PowerShell installieren. Wenn Sie die Version 1.1.47.0 des SQL-Ressourcenanbieters aktualisieren, lädt das Bereitstellungsskript die erforderlichen PowerShell-Module automatisch herunter und installiert sie im Pfad „C:\Programme\SqlMySqlPsh“.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> In einem nicht verbundenen Szenario müssen Sie vor der Bereitstellung die erforderlichen PowerShell-Module manuell herunterladen und das Repository registrieren. Weitere Informationen finden Sie unter [Bereitstellen des SQL-Ressourcenanbieters in Azure Stack Hub](azure-stack-sql-resource-provider-deploy.md).

Nachstehend finden Sie ein Beispiel für das Skript *UpdateSQLProvider.ps1*, das Sie an einer PowerShell-Konsole mit erhöhten Rechten ausführen können. Achten Sie darauf, die Variableninformationen und Kennwörter wie erforderlich zu ändern:  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
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

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

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

Wenn das Updateskript für den Ressourcenanbieter ausgeführt ist, schließen Sie die aktuelle PowerShell-Sitzung.

## <a name="next-steps"></a>Nächste Schritte

[Wartungsvorgänge](azure-stack-sql-resource-provider-maintain.md)
