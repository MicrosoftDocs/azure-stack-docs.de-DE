---
title: Aktualisieren des MySQL-Ressourcenanbieters in Azure Stack Hub
description: Erfahren Sie, wie Sie den MySQL-Ressourcenanbieter in Azure Stack Hub aktualisieren können.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: a617efab5516fb2f6bad96e36c99f1b84852237c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698639"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>Aktualisieren des MySQL-Ressourcenanbieters in Azure Stack Hub

Unter Umständen wird ein neuer MySQL-Ressourcenanbieteradapter veröffentlicht, wenn Azure Stack Hub-Builds aktualisiert werden. Der vorhandene Adapter funktioniert zwar weiterhin, aber es ist ratsam, so schnell wie möglich das Update auf den aktuellen Build durchzuführen.

Ab Ressourcenanbieter MySQL RP Version 1.1.33.0 sind die Updates kumulativ und müssen nicht in der Reihenfolge installiert werden, in der sie veröffentlicht wurden, sofern Sie mit Version 1.1.24.0 oder höher beginnen. Wenn Sie beispielsweise Version 1.1.24.0 des MySQL-Ressourcenanbieters ausführen, können Sie dann ein Upgrade auf Version 1.1.33.0 oder höher ausführen, ohne zuerst Version 1.1.30.0 installieren zu müssen. In der Versionsliste unter [Bereitstellen des Ressourcenanbieters – Voraussetzungen](./azure-stack-mysql-resource-provider-deploy.md#prerequisites) finden Sie die verfügbaren Ressourcenanbieterversionen und die jeweilige Azure Stack Hub-Version, auf der sie unterstützt werden.

Verwenden Sie zum Aktualisieren des Ressourcenanbieters das Skript **UpdateMySQLProvider.ps1**. Der Prozess ähnelt dem Prozess zum Installieren eines Ressourcenanbieters, wie im Abschnitt „Bereitstellen des Ressourcenanbieters“ dieses Artikels beschrieben. Das Skript ist im Download des Ressourcenanbieters enthalten. 

 > [!IMPORTANT]
 > Überprüfen Sie vor dem Upgrade des Ressourcenanbieters die Versionshinweise auf Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen, die sich auf die Bereitstellung auswirken können.

## <a name="update-script-processes"></a>Aktualisieren von Skriptprozessen

Mit dem Skript **UpdateMySQLProvider.ps1** wird ein neuer virtueller Computer (virtual machine, VM) mit dem aktuellen Ressourcenanbietercode erstellt, und die Einstellungen werden von der alten VM zur neuen VM migriert. Zu den migrierten Einstellungen gehören Datenbank- und Hostserverinformationen und der erforderliche DNS-Eintrag.

>[!NOTE]
>Wir empfehlen Ihnen, das aktuelle Windows Server 2016 Core-Image aus der Marketplace-Verwaltung herunterzuladen. Wenn Sie ein Update installieren müssen, können Sie unter dem lokalen Abhängigkeitspfad ein **einzelnes** MSU-Paket platzieren. Beim Skript tritt ein Fehler auf, wenn an diesem Speicherort mehrere MSU-Dateien vorhanden sind.

Für das Skript ist die Verwendung der Argumente erforderlich, die für das Skript „DeployMySqlProvider.ps1“ beschrieben wurden. Geben Sie hier auch das Zertifikat an.  


## <a name="update-script-parameters"></a>Aktualisieren von Skriptparametern 
Wenn Sie das PowerShell-Skript **UpdateMySQLProvider.ps1** ausführen, geben Sie die folgenden Parameter in der Befehlszeile an. Wenn Sie keine Parameter angeben oder bei der Überprüfung eines Parameters ein Fehler auftritt, werden Sie aufgefordert, die erforderlichen Parameter anzugeben.

| Parametername | BESCHREIBUNG | Kommentar oder Standardwert | 
| --- | --- | --- | 
| **CloudAdminCredential** | Die Anmeldeinformationen für den Cloudadministrator, die für den Zugriff auf den privilegierten Endpunkt erforderlich sind. | _Erforderlich_ | 
| **AzCredential** | Die Anmeldeinformationen für das Azure Stack Hub-Dienstadministratorkonto. Verwenden Sie die gleichen Anmeldeinformationen wie bei der Bereitstellung von Azure Stack Hub. | _Erforderlich_ | 
| **VMLocalCredential** |Die Anmeldeinformationen für das lokale Administratorkonto des virtuellen Computers mit dem SQL-Ressourcenanbieter. | _Erforderlich_ | 
| **PrivilegedEndpoint** | Die IP-Adresse oder der DNS-Name des privilegierten Endpunkts. |  _Erforderlich_ | 
| **AzureEnvironment** | Die zum Bereitstellen von Azure Stack Hub verwendete Azure-Umgebung des Dienstadministratorkontos. Nur für Azure AD-Bereitstellungen erforderlich. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein chinesisches Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Ihre Zertifikatdatei (PFX) muss ebenfalls in diesem Verzeichnis abgelegt werden. | _Optional_ (_obligatorisch_ bei mehreren Knoten) | 
| **DefaultSSLCertificatePassword** | Das Kennwort für das PFX-Zertifikat. | _Erforderlich_ | 
| **MaxRetryCount** | Die Anzahl von Wiederholungsversuchen für jeden Vorgang, wenn ein Fehler auftritt.| 2 | 
| **RetryDuration** | Das Timeoutintervall zwischen Wiederholungen in Sekunden. | 120 | 
| **Deinstallieren** | Entfernt den Ressourcenanbieter und alle zugeordneten Ressourcen (siehe folgende Hinweise). | Nein | 
| **DebugMode** | Verhindert die automatische Bereinigung nach einem Fehler. | Nein | 
| **AcceptLicense** | Überspringt die Aufforderung zum Akzeptieren der GPL-Lizenz.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Beispiel für ein Updateskript

> [!NOTE] 
> Der Updateprozess gilt nur für integrierte Systeme.

Wenn Sie auf 1.1.33.0 oder frühere Versionen des MySQL-Ressourcenanbieters aktualisieren, müssen Sie bestimmte Versionen von AzureRm.BootStrapper und Azure Stack Hub-Modulen in PowerShell installieren. Wenn Sie auf die Version 1.1.47.0 des MySQL-Ressourcenanbieters aktualisieren, lädt das Bereitstellungsskript die erforderlichen PowerShell-Module automatisch herunter und installiert sie im Pfad “C:\Programme\SqlMySqlPsh“.

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> In einem nicht verbundenen Szenario müssen Sie vor der Bereitstellung die erforderlichen PowerShell-Module manuell herunterladen und das Repository registrieren. Weitere Informationen finden Sie unter [Bereitstellen des MySQL-Ressourcenanbieters in Azure Stack Hub](azure-stack-mysql-resource-provider-deploy.md).

Nachstehend finden Sie ein Beispiel für die Verwendung des Skripts *UpdateMySQLProvider.ps1*, das Sie an einer PowerShell-Konsole mit erhöhten Rechten ausführen können. Achten Sie darauf, die Variableninformationen und Kennwörter wie erforderlich zu ändern:

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
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
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
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

Wenn das Updateskript für den Ressourcenanbieter ausgeführt ist, schließen Sie die aktuelle PowerShell-Sitzung.

## <a name="next-steps"></a>Nächste Schritte
[Warten des MySQL-Ressourcenanbieters](azure-stack-mysql-resource-provider-maintain.md)
