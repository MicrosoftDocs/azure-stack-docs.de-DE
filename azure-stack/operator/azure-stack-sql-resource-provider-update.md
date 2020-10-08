---
title: Aktualisieren des SQL-Ressourcenanbieters in Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie den SQL-Ressourcenanbieter in Azure Stack Hub aktualisieren.
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 60d9ce421ce4cdede89dd9f0fa9ff4ee4746d039
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572856"
---
# <a name="update-the-sql-resource-provider"></a>Aktualisieren des SQL-Ressourcenanbieters

> [!IMPORTANT]
> Überprüfen Sie vor der Aktualisierung des Ressourcenanbieters die Anmerkungen zu dieser Version auf Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen, die sich auf die Bereitstellung auswirken können. Die Versionshinweise geben auch die minimale Azure Stack Hub-Version an, die für den Ressourcenanbieter erforderlich ist.

Unter Umständen wird ein neuer SQL-Ressourcenanbieter veröffentlicht, wenn Azure Stack Hub auf einen neuen Build aktualisiert wird. Auch wenn der vorhandene Ressourcenanbieter weiterhin funktioniert, ist es ratsam, so schnell wie möglich das Update auf den aktuellen Build durchzuführen.

|Unterstützte Azure Stack Hub-Version|SQL RP Version|Windows Server-Instanz, auf der der RP-Dienst ausgeführt wird
  |-----|-----|-----|
  |2005|[SQL-RP-Version 1.1.93.0](https://aka.ms/azshsqlrp11930)|Microsoft AzureStack Add-on RP Windows Server INTERNAL ONLY
  |2005, 2002, 1910|[SQL RP Version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|Windows Server 2016 Datacenter – Server Core|
  |1908|[SQL RP Version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|Windows Server 2016 Datacenter – Server Core|
  |     |     |     |

Das Update des SQL-Ressourcenanbieters ist kumulativ. Sie können ein Update einer alten Version direkt auf die neueste Version durchführen. 

Verwenden Sie zum Aktualisieren des Ressourcenanbieters das Skript **UpdateSQLProvider.ps1**. Verwenden Sie Ihr Dienstkonto mit lokalen Administratorrechten, das ein **Besitzer** des Abonnements ist. Dieses Updateskript ist im Download des Ressourcenanbieters enthalten. 

Der Updateprozess ähnelt dem Prozess zum [Bereitstellen des Ressourcenanbieters](./azure-stack-sql-resource-provider-deploy.md). Das Updateskript verwendet die gleichen Argumente wie das Skript „DeploySqlProvider.ps1“, und Sie müssen Zertifikatinformationen angeben.

## <a name="update-script-processes"></a>Aktualisieren von Skriptprozessen

Das Skript **UpdateSQLProvider.ps1** erstellt eine neue VM mit dem neuesten Betriebssystemimage, stellt den aktuellen Ressourcenanbietercode bereit und migriert die Einstellungen vom alten zum neuen Ressourcenanbieter. 

> [!NOTE]
>Es wird empfohlen, das neueste Windows Server 2016 Core-Image oder das Microsoft AzureStack Add-on RP Windows Server-Image von der Marketplace-Verwaltung herunterzuladen. Wenn Sie ein Update installieren müssen, können Sie unter dem lokalen Abhängigkeitspfad ein **einzelnes** MSU-Paket platzieren. Beim Skript tritt ein Fehler auf, wenn an diesem Speicherort mehrere MSU-Dateien vorhanden sind.

Nach dem Erstellen einer neuen VM migriert das Skript *UpdateSQLProvider.ps1* die folgenden Einstellungen von der alten Ressourcenanbieter-VM:

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
| **Deinstallieren** | Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen. | Nein  |
| **DebugMode** | Verhindert die automatische Bereinigung nach einem Fehler. | Nein  |

## <a name="update-script-powershell-example"></a>Beispiel für ein PowerShell-Aktualisierungsskript

Wenn Sie den SQL-Ressourcenanbieter auf Version 1.1.33.0 oder früher aktualisieren, müssen Sie bestimmte Versionen von AzureRm.BootStrapper und den Azure Stack Hub-Modulen in PowerShell installieren. 

Wenn Sie den SQL-Ressourcenanbieter auf Version 1.1.47.0 oder höher aktualisieren, können Sie diesen Schritt überspringen. Das Bereitstellungsskript lädt die erforderlichen PowerShell-Module automatisch herunter und installiert sie im Pfad „C:\Programme\SqlMySqlPsh“.

>[!NOTE]
>Wenn der Ordner „C:\Programme\SqlMySqlPsh“ bereits vorhanden und ein PowerShell-Modul darin enthalten ist, empfiehlt es sich, diesen Ordner vor Ausführung des Updateskripts zu bereinigen. Dadurch wird sichergestellt, dass die richtige Version des PowerShell-Moduls heruntergeladen und verwendet wird.

```powershell
# Run the following scripts when updating to version 1.1.33.0 only.
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

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
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
