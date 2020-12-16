---
title: Bereitstellen des MySQL-Ressourcenanbieters in Azure Stack Hub
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieteradapter und MySQL-Datenbanken als Dienst in Azure Stack Hub bereitstellen.
author: bryanla
ms.topic: article
ms.date: 12/07/2020
ms.author: bryanla
ms.reviewer: caoyang
ms.lastreviewed: 12/07/2020
ms.openlocfilehash: b6d345ecfecaa3859420087bc7cff051b39fbb36
ms.sourcegitcommit: 62eb5964a824adf7faee58c1636b17fedf4347e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778154"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack-hub"></a>Bereitstellen des MySQL-Ressourcenanbieters in Azure Stack Hub

Verwenden Sie den MySQL-Server-Ressourcenanbieter, um MySQL-Datenbanken als Azure Stack Hub-Dienst verfügbar zu machen. Der MySQL-Ressourcenanbieter wird als Dienst auf einer Windows Server 2016 Server Core-VM (für Adapterversionen <= 1.1.47.0) oder als spezielles Ressourcenanbieter-Add-On für Windows Server (für Adapterversionen >= 1.1.93.0) ausgeführt.

> [!IMPORTANT]
> Auf Servern, die SQL oder MySQL hosten, sollte nur der Ressourcenanbieter Elemente erstellen. Die auf einem Hostserver erstellten Elemente, die nicht vom Ressourcenanbieter erstellt wurden, werden nicht unterstützt und können zu einem Konfliktzustand führen.

## <a name="prerequisites"></a>Voraussetzungen

Es gibt mehrere Voraussetzungen, die erfüllt werden müssen, bevor Sie den Azure Stack Hub-MySQL-Ressourcenanbieter bereitstellen können:

- Sie benötigen einen Computer und ein Konto mit Zugriff auf folgende Komponenten:
   - Das [Azure Stack Hub-Administratorportal](azure-stack-manage-portals.md).
   - Den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md).
   - Den Azure Resource Manager-Administratorendpunkt `https://management.region.<fqdn>`, wobei `<fqdn>` der vollqualifizierte Domänenname ist (oder `https://management.local.azurestack.external` bei Verwendung des ASDK).
   - Das Internet, wenn Azure Stack Hub so bereitgestellt wurde, dass Azure Active Directory (AD) als Identitätsanbieter verwendet wird.

- Falls Sie dies noch nicht getan haben, [registrieren Sie Azure Stack Hub](azure-stack-registration.md) bei Azure, damit Sie Azure Marketplace-Elemente herunterladen können.

- Fügen Sie die erforderliche Windows Server-VM zum Azure Stack Hub-Marketplace hinzu.
  - Laden Sie für MySQL-Ressourcenanbieterversionen <= 1.1.47.0 das Image **Windows Server 2016 Datacenter – Server Core** herunter.
  - Laden Sie für MySQL-Ressourcenanbieterversionen >= 1.1.93.0 das Image **Microsoft AzureStack Add-On RP Windows Server INTERNAL ONLY** herunter. Diese Windows Server-Version wurde speziell für die Azure Stack-Add-On-RP-Infrastruktur konzipiert und wird im Mandanten-Marketplace nicht angezeigt.

- Laden Sie die unterstützte Version des MySQL-Ressourcenanbieters in binärer Form gemäß der Versionszuordnungstabelle unten herunter. Führen Sie das selbstextrahierende Programm aus, um die heruntergeladenen Inhalte in ein temporäres Verzeichnis zu extrahieren. 

  |Unterstützte Azure Stack Hub-Version|MySQL RP Version|Windows Server-Instanz, auf der der RP-Dienst ausgeführt wird
  |-----|-----|-----|
  |2008, 2005|[MySQL-RP-Version 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Microsoft AzureStack Add-on RP Windows Server INTERNAL ONLY
  |2005, 2002, 1910|[MySQL RP-Version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter – Server Core|
  |1908|[MySQL RP-Version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter – Server Core|
  |     |     |     |

>[!NOTE]
>Zum Bereitstellen des MySQL-Anbieters in einem System ohne Internetzugriff kopieren Sie die Datei [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) in einen lokalen Pfad. Geben Sie den Pfadnamen mit dem Parameter **DependencyFilesLocalPath** an.


- Stellen Sie sicher, dass die Voraussetzungen der Datencenterintegration erfüllt sind:

    |Voraussetzung|Verweis|
    |-----|-----|
    |Bedingte DNS-Weiterleitung ist ordnungsgemäß festgelegt.|[Azure Stack Hub-Rechenzentrumsintegration – DNS](azure-stack-integrate-dns.md)|
    |Eingangsports für Ressourcenanbieter sind geöffnet.|[Integration des Azure Stack Hub-Rechenzentrums – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI-Zertifikatantragsteller und SAN sind ordnungsgemäß festgelegt.|[Obligatorische PKI-Voraussetzungen für die Azure Stack Hub-Bereitstellung](azure-stack-pki-certs.md)[PaaS-Zertifikatanforderungen für die Azure Stack Hub-Bereitstellung](azure-stack-pki-certs.md)|
    |     |     |

Führen Sie in einem nicht verbundenen Szenario die folgenden Schritte aus, um die erforderlichen PowerShell-Module manuell herunterzuladen und das Repository zu registrieren.

1. Melden Sie sich bei einem Computer mit Internetverbindung an, und verwenden Sie die folgenden Skripts, um die PowerShell-Module herunterzuladen.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
```

2. Je nachdem, welche Version des Ressourcenanbieters Sie bereitstellen, führen Sie das entsprechende Skript aus.

```powershell
# for resource provider version >= 1.1.93.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```
```powershell
# for resource provider version <= 1.1.47.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

3. Kopieren Sie dann die heruntergeladenen Pakete auf ein USB-Gerät.

4. Melden Sie sich bei der getrennten Arbeitsstation an, und kopieren Sie die Pakete vom USB-Gerät an einen Speicherort auf der Arbeitsstation.

5. Registrieren Sie diesen Speicherort als lokales Repository.

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory" 
```

### <a name="certificates"></a>Zertifikate

_Nur bei Installationen in integrierten Systemen_. Sie müssen das SQL-PaaS-PKI-Zertifikat bereitstellen, das im Abschnitt „Optionale PaaS-Zertifikate“ der [PKI-Anforderungen für die Azure Stack Hub-Bereitstellung](./azure-stack-pki-certs.md) beschrieben ist. Platzieren Sie die .pfx-Datei an dem durch den **DependencyFilesLocalPath**-Parameter festgelegten Speicherort. Geben Sie für ASDK-Systeme kein Zertifikat an.

## <a name="deploy-the-resource-provider"></a>Bereitstellen des Ressourcenanbieters

Wenn alle Voraussetzungen erfüllt sind, können Sie das Skript **DeployMySqlProvider.ps1** auf einem Computer ausführen, der sowohl auf den Resource Manager-Administratorendpunkt für Azure Stack Hub als auch auf den privilegierten Endpunkt zugreifen kann, um den MySQL-Ressourcenanbieter bereitzustellen. Das Skript „DeployMySqlProvider.ps1“ wird als Teil der Installationsdateien vom MySQL-Ressourcenanbieter extrahiert, die Sie entsprechend Ihrer Azure Stack Hub-Version heruntergeladen haben.

 > [!IMPORTANT]
 > Überprüfen Sie vor der Bereitstellung des Ressourcenanbieters die Anmerkungen zu dieser Version auf Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen, die sich auf die Bereitstellung auswirken können.

Um den MySQL-Ressourcenanbieter bereitzustellen, öffnen Sie ein **neues** PowerShell-Fenster mit erhöhten Rechten (nicht PowerShell ISE), und wechseln Sie zu dem Verzeichnis, in das Sie die Binärdateien des MySQL-Ressourcenanbieters extrahiert haben. 

> [!IMPORTANT]
> Es wird dringend empfohlen, **Clear-AzureRmContext -Scope CurrentUser** und **Clear-AzureRmContext -Scope Process** zu verwenden, um den Cache vor dem Ausführen des Aktualisierungsskripts zu löschen.

Führen Sie das Skript **DeployMySqlProvider.ps1** aus, das die folgenden Aufgaben ausführt:

* Lädt die Zertifikate und andere Artefakte in ein Azure Stack Hub-Speicherkonto hoch.
* Veröffentlicht Katalogpakete, damit Sie MySQL-Datenbank-Instanzen über den Katalog bereitstellen können.
* Veröffentlicht ein Katalogpaket für die Bereitstellung von Hostservern.
* Stellt VM unter Verwendung des heruntergeladenen Images – „Windows Server 2016 Core“ oder „Microsoft AzureStack Add-on RP Windows Server“ – eine bereit und installiert dann den MySQL-Ressourcenanbieter.
* Registriert einen lokalen DNS-Eintrag, der dem virtuellen Computer mit dem Ressourcenanbieter zugeordnet wird.
* Registriert Ihren Ressourcenanbieter beim lokalen Azure Resource Manager für das Operatorkonto.

> [!NOTE]
> Sobald die Bereitstellung des MySQL-Ressourcenanbieters beginnt, wird die Ressourcengruppe **system.local.mysqladapter** erstellt. Es kann bis zu 75 Minuten dauern, bis die erforderlichen Bereitstellungen für diese Ressourcengruppe fertig gestellt sind. Sie sollten keine weiteren Ressourcen in der Ressourcengruppe **system.local.mysqladapter** platzieren.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1-Parameter

Sie können diese Parameter in der Befehlszeile angeben. Wenn Sie keine Parameter angeben oder bei der Überprüfung eines Parameters ein Fehler auftritt, werden Sie aufgefordert, die erforderlichen Parameter anzugeben.

| Parametername | BESCHREIBUNG | Kommentar oder Standardwert |
| --- | --- | --- |
| **CloudAdminCredential** | Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind. | _Erforderlich_ |
| **AzCredential** | Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub. Beim Skript tritt ein Fehler auf, wenn für das Konto, das Sie mit AzCredential verwenden, mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist. | _Erforderlich_ |
| **VMLocalCredential** | Die Anmeldeinformationen für das lokale Administratorkonto des virtuellen Computers mit dem MySQL-Ressourcenanbieter. | _Erforderlich_ |
| **PrivilegedEndpoint** | Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts. |  _Erforderlich_ |
| **AzureEnvironment** | Die zum Bereitstellen von Azure Stack Hub verwendete Azure-Umgebung des Dienstadministratorkontos. Nur für Azure AD-Bereitstellungen erforderlich. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein chinesisches Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Ihre Zertifikatdatei (PFX-Datei) muss nur für integrierte Systeme ebenfalls in diesem Verzeichnis abgelegt werden. Laden Sie für nicht verbundene Umgebungen [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) in dieses Verzeichnis herunter. Sie können optional ein Windows Update MSU-Paket in dieses Verzeichnis kopieren. | _Optional_ (_obligatorisch_ für integrierte Systeme oder nicht verbundene Umgebungen) |
| **DefaultSSLCertificatePassword** | Das Kennwort für das PFX-Zertifikat. | _Erforderlich_ |
| **MaxRetryCount** | Die Anzahl von Wiederholungsversuchen für jeden Vorgang, wenn ein Fehler auftritt.| 2 |
| **RetryDuration** | Das Timeoutintervall zwischen Wiederholungen in Sekunden. | 120 |
| **Deinstallieren** | Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen (siehe folgende Hinweise). | Nein |
| **DebugMode** | Verhindert die automatische Bereinigung nach einem Fehler. | Nein |
| **AcceptLicense** | Überspringt die Aufforderung zum Akzeptieren der GPL-Lizenz.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Bereitstellen des MySQL-Ressourcenanbieters mithilfe eines benutzerdefinierten Skripts

Wenn Sie 1.1.33.0 oder frühere Versionen des MySQL-Ressourcenanbieters bereitstellen, müssen Sie bestimmte Versionen von „AzureRm.BootStrapper“ und Azure Stack Hub-Modulen in PowerShell installieren. Wenn Sie Version 1.1.47.0 oder höher des MySQL-Ressourcenanbieters bereitstellen, lädt das Bereitstellungsskript die erforderlichen PowerShell-Module automatisch herunter und installiert sie im Pfad „C:\Programme\SqlMySqlPsh“.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> In einem nicht verbundenen Szenario müssen Sie vor der Bereitstellung die erforderlichen PowerShell-Module manuell herunterladen und das Repository registrieren.

Um jegliche manuelle Konfiguration bei der Bereitstellung des Ressourcenanbieters zu vermeiden, können Sie das folgende Skript anpassen. Ändern Sie die Standardkontoinformationen und -kennwörter entsprechend den Anforderungen für Ihre Azure Stack Hub-Bereitstellung.

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh,
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Wenn das Skript zur Installation des Ressourcenanbieters abgeschlossen ist, aktualisieren Sie Ihren Browser, um sicherzustellen, dass die neuesten Updates angezeigt werden, und um die aktuelle PowerShell-Sitzung zu schließen.

## <a name="verify-the-deployment-by-using-the-azure-stack-hub-portal"></a>Überprüfung der Bereitstellung mithilfe des Azure Stack Hub-Portals

1. Melden Sie sich als Dienstadministrator beim Verwaltungsportal an.
2. Wählen Sie **Ressourcengruppen** aus.
3. Wählen Sie die Ressourcengruppe **system.\<location\>.mysqladapter** aus.
4. Auf der Zusammenfassungsseite der Ressourcengruppenübersicht sollten keine fehlerhaften Bereitstellungen angezeigt werden.
5. Wählen Sie abschließend im Verwaltungsportal die Option **Virtuelle Computer** aus, um zu überprüfen, ob der VM des MySQL-Ressourcenanbieters erfolgreich erstellt wurde und ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Hostservern](azure-stack-mysql-resource-provider-hosting-servers.md)
