---
title: Bereitstellen des MySQL-Ressourcenanbieters in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieteradapter und MySQL-Databases as a Service in Azure Stack bereitstellen.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: c3b3c30eb10e767cf20336af67bd094994def2f9
ms.sourcegitcommit: a23b80b57668615c341c370b70d0a106a37a02da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72682118"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Bereitstellen des MySQL-Ressourcenanbieters in Azure Stack

Verwenden Sie den MySQL Server-Ressourcenanbieter, um MySQL-Datenbanken als Azure Stack-Dienst verfügbar zu machen. Der MySQL Server-Ressourcenanbieter wird als Dienst auf einem virtuellen Windows Server 2016 Server Core-Computer ausgeführt.

> [!IMPORTANT]
> Auf Servern, die SQL oder MySQL hosten, kann nur der Ressourcenanbieter Elemente erstellen. Die auf einem Hostserver erstellten Elemente, die nicht vom Ressourcenanbieter erstellt wurden, könnten zu einem Zustand ohne Entsprechung führen.

## <a name="prerequisites"></a>Voraussetzungen

Es gibt mehrere Voraussetzungen, die erfüllt werden müssen, bevor Sie den Azure Stack-MySQL-Ressourcenanbieter bereitstellen können. Führen Sie zum Erfüllen dieser Anforderungen die Schritte in diesem Artikel auf einem Computer aus, der auf die privilegierte Endpunkt-VM zugreifen kann.

* Falls Sie dies noch nicht getan haben, [registrieren Sie Azure Stack](./azure-stack-registration.md) bei Azure, damit Sie Azure Marketplace-Elemente herunterladen können.
* Installieren Sie die Azure- und Azure Stack PowerShell-Module auf dem System, auf dem Sie diese Installation ausführen. Das System muss ein Windows 10- oder Windows Server 2016-Image mit der neuesten Version der .NET Runtime sein. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack](./azure-stack-powershell-install.md).
* Fügen Sie dem Azure Stack-Marketplace die erforderliche Windows Server Core-VM hinzu, indem Sie das Image **Windows Server 2016 Datacenter – Server Core** herunterladen.

* Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren.

  >[!NOTE]
  >Zum Bereitstellen des MySQL-Anbieters in einem System ohne Internetzugriff kopieren Sie die Datei [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) in einen lokalen Pfad. Geben Sie den Pfadnamen mit dem Parameter **DependencyFilesLocalPath** an.

* Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack.

  |Azure Stack-Mindestversion|MySQL RP Version|
  |-----|-----|
  |Version 1808 (1.1808.0.97)|[MySQL RP-Version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Version 1808 (1.1808.0.97)|[MySQL RP Version 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Version 1804 (1.0.180513.1)|[MySQL RP Version 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Stellen Sie sicher, dass die Voraussetzungen der Datencenterintegration erfüllt sind:

    |Voraussetzung|Verweis|
    |-----|-----|
    |Bedingte DNS-Weiterleitung ist ordnungsgemäß festgelegt.|[Integration des Azure Stack-Datencenters – DNS](azure-stack-integrate-dns.md)|
    |Eingangsports für Ressourcenanbieter sind geöffnet.|[Integration des Azure Stack-Datencenters – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI-Zertifikatantragsteller und SAN sind ordnungsgemäß festgelegt.|[Obligatorische PKI-Voraussetzungen für die Azure Stack-Bereitstellung](azure-stack-pki-certs.md#mandatory-certificates)[PaaS-Zertifikatanforderungen für die Azure Stack-Bereitstellung](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Zertifikate

_Nur bei Installationen in integrierten Systemen_. Sie müssen das SQL-PaaS-PKI-Zertifikat bereitstellen, das im Abschnitt „Optionale PaaS-Zertifikate“ der [PKI-Anforderungen für die Azure Stack-Bereitstellung](./azure-stack-pki-certs.md#optional-paas-certificates) beschrieben ist. Platzieren Sie die .pfx-Datei an dem durch den **DependencyFilesLocalPath**-Parameter festgelegten Speicherort. Geben Sie für ASDK-Systeme kein Zertifikat an.

## <a name="deploy-the-resource-provider"></a>Bereitstellen des Ressourcenanbieters

Nachdem Sie alle Voraussetzungen installiert haben, können Sie das Skript **DeployMySqlProvider.ps1** ausführen, um den MySQL-Ressourcenanbieter bereitzustellen. Das Skript „DeployMySqlProvider.ps1“ wird als Teil der Installationsdateien vom MySQL-Ressourcenanbieter extrahiert, die Sie entsprechend Ihrer Azure Stack-Version heruntergeladen haben.

 > [!IMPORTANT]
 > Überprüfen Sie vor der Bereitstellung des Ressourcenanbieters die Anmerkungen zu dieser Version auf Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen, die sich auf die Bereitstellung auswirken können.

Öffnen Sie ein neues PowerShell-Fenster mit erhöhten Rechten (nicht PowerShell ISE), und wechseln Sie zu dem Verzeichnis, in das Sie die Binärdateien des MySQL-Ressourcenanbieters extrahiert haben, um den MySQL-Ressourcenanbieter bereitzustellen. Die Verwendung eines neuen PowerShell-Fensters wird empfohlen, um mögliche Probleme durch bereits geladene PowerShell-Module zu vermeiden.

Führen Sie das Skript **DeployMySqlProvider.ps1** aus, das die folgenden Aufgaben ausführt:

* Lädt die Zertifikate und andere Artefakte in ein Azure Stack-Speicherkonto hoch.
* Veröffentlicht Katalogpakete, damit Sie MySQL-Datenbank-Instanzen über den Katalog bereitstellen können.
* Veröffentlicht ein Katalogpaket für die Bereitstellung von Hostservern.
* Implementiert eine VM unter Verwendung des heruntergeladenen Windows Server 2016 Core-Images und installiert dann den MySQL-Ressourcenanbieter.
* Registriert einen lokalen DNS-Eintrag, der dem virtuellen Computer mit dem Ressourcenanbieter zugeordnet wird.
* Registriert Ihren Ressourcenanbieter beim lokalen Azure Resource Manager für das Operatorkonto.

> [!NOTE]
> Sobald die Bereitstellung des MySQL-Ressourcenanbieters beginnt, wird die Ressourcengruppe **system.local.mysqladapter** erstellt. Es kann bis zu 75 Minuten dauern, bis die erforderlichen Bereitstellungen für diese Ressourcengruppe fertig gestellt sind.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1-Parameter

Sie können diese Parameter in der Befehlszeile angeben. Wenn Sie keine Parameter angeben oder bei der Überprüfung eines Parameters ein Fehler auftritt, werden Sie aufgefordert, die erforderlichen Parameter anzugeben.

| Parametername | BESCHREIBUNG | Kommentar oder Standardwert |
| --- | --- | --- |
| **CloudAdminCredential** | Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind. | _Erforderlich_ |
| **AzCredential** | Die Anmeldeinformationen für das Azure Stack-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack. | _Erforderlich_ |
| **VMLocalCredential** | Die Anmeldeinformationen für das lokale Administratorkonto des virtuellen Computers mit dem MySQL-Ressourcenanbieter. | _Erforderlich_ |
| **PrivilegedEndpoint** | Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts. |  _Erforderlich_ |
| **AzureEnvironment** | Die zum Bereitstellen von Azure Stack verwendete Azure-Umgebung des Dienstadministratorkontos. Nur für Azure AD-Bereitstellungen erforderlich. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein chinesisches Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Ihre Zertifikatdatei (PFX-Datei) muss nur für integrierte Systeme ebenfalls in diesem Verzeichnis abgelegt werden. Laden Sie für nicht verbundene Umgebungen [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) in dieses Verzeichnis herunter. Sie können optional ein Windows Update MSU-Paket in dieses Verzeichnis kopieren. | _Optional_ (_obligatorisch_ für integrierte Systeme oder nicht verbundene Umgebungen) |
| **DefaultSSLCertificatePassword** | Das Kennwort für das PFX-Zertifikat. | _Erforderlich_ |
| **MaxRetryCount** | Die Anzahl von Wiederholungsversuchen für jeden Vorgang, wenn ein Fehler auftritt.| 2 |
| **RetryDuration** | Das Timeoutintervall zwischen Wiederholungen in Sekunden. | 120 |
| **Deinstallieren** | Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen (siehe folgende Hinweise). | Nein |
| **DebugMode** | Verhindert die automatische Bereinigung nach einem Fehler. | Nein |
| **AcceptLicense** | Überspringt die Aufforderung zum Akzeptieren der GPL-Lizenz.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Bereitstellen des MySQL-Ressourcenanbieters mithilfe eines benutzerdefinierten Skripts

Um jegliche manuelle Konfiguration bei der Bereitstellung des Ressourcenanbieters zu vermeiden, können Sie das folgende Skript anpassen. Ändern Sie die Standardkontoinformationen und -kennwörter für Ihre Azure Stack-Bereitstellung nach Bedarf.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

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

Wenn das Skript zur Installation des Ressourcenanbieters abgeschlossen ist, aktualisieren Sie Ihren Browser, um sicherzustellen, dass die neuesten Updates angezeigt werden.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Überprüfung der Bereitstellung mithilfe des Azure Stack-Portals

1. Melden Sie sich als Dienstadministrator beim Verwaltungsportal an.
2. Wählen Sie **Ressourcengruppen** aus.
3. Wählen Sie die Ressourcengruppe **system.\<Speicherort\>.mysqladapter** aus.
4. Auf der Zusammenfassungsseite der Ressourcengruppenübersicht sollten keine fehlerhaften Bereitstellungen angezeigt werden.
5. Wählen Sie abschließend im Verwaltungsportal die Option **Virtuelle Computer** aus, um zu überprüfen, ob der VM des MySQL-Ressourcenanbieters erfolgreich erstellt wurde und ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Hostservern](azure-stack-mysql-resource-provider-hosting-servers.md)
