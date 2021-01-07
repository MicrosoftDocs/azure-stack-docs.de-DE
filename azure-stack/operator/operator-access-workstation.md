---
title: Arbeitsstation mit Azure Stack Hub-Operatorzugriff
description: Hier erfahren Sie, wie Sie eine Arbeitsstation mit Azure Stack Hub-Operatorzugriff herunterladen und konfigurieren.
author: ashika789
ms.topic: article
ms.date: 11/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: bd94503efe97eb30955b0fd9deb7a5c41e263e7b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868589"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Arbeitsstation mit Azure Stack Hub-Operatorzugriff 

Die Arbeitsstation mit Operatorzugriff (Operator Access Workstation, OAW) wird verwendet, um eine Jumpbox-VM auf einem Hardwarelebenszyklus-Host (Hardware Lifecycle Host, HLH) bereitzustellen, der Version 2005 oder höher ausführt, sodass ein Azure Stack Hub-Operator in Supportszenarien auf den privilegierten Endpunkt (PEP) und das Administratorportal zugreifen kann. 

Die OAW-VM sollte erstellt werden, wenn ein Operator eine neue Aufgabe ausführt. Nachdem eine erforderliche Aufgabe auf der VM abgeschlossen wurde, sollte die VM heruntergefahren und entfernt werden, da sie nicht immer von Azure Stack Hub ausgeführt werden muss.  

## <a name="oaw-scenarios"></a>OAW-Szenarien

In den folgenden Tabellen sind häufige Szenarien für die OAW aufgeführt, aber diese sind nicht ausschließlich. Es wird empfohlen, Remotedesktop zum Herstellen einer Verbindung mit der OAW zu verwenden. 

|Szenario                                                                                                                          |Beschreibung                 |
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
|[Zugreifen auf das Verwaltungsportal](./azure-stack-manage-portals.md)                     |Ausführen von Verwaltungsvorgängen                                                                           |
|[Zugreifen auf PEP](./azure-stack-privileged-endpoint.md)                                     |Protokollsammlung und -upload:<br>-[Erstellen einer SMB-Freigabe](#transfer-files-between-the-hlh-and-oaw) auf dem HLH für die Dateiübertragung von Azure Stack Hub<br>\- Verwenden von Azure Storage-Explorer zum Hochladen von Protokollen, die auf der SMB-Freigabe gespeichert wurden |
|[Registrieren von Azure Stack Hub](./azure-stack-registration.md#renew-or-change-registration) |Zur Neuregistrierung rufen Sie den Namen und die Ressourcengruppe der vorherigen Registrierung über das Verwaltungsportal ab.                               |
|[Marketplace-Syndikation](./azure-stack-download-azure-marketplace-item.md)            |[Erstellen einer SMB-Freigabe](#transfer-files-between-the-hlh-and-oaw) auf dem HLH zum Speichern des heruntergeladenen Images oder der heruntergeladenen Erweiterung                                                        |

## <a name="download-files"></a>Herunterladen von Dateien

Die Dateien zum Erstellen der OAW-VM können Sie [**hier**](https://aka.ms/OAWDownload) herunterladen. Lesen Sie vor dem Download die [Microsoft-Datenschutzbestimmungen](https://privacy.microsoft.com/privacystatement) und [rechtlichen Bedingungen](/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms).

Da die Lösung zustandslos ist, gibt es keine Updates für die OAW-VM. Für jeden Meilenstein wird eine neue Version der VM-Imagedatei veröffentlicht. Verwenden Sie zum Erstellen einer neuen OAW-VM die aktuelle Version. Die Imagedatei basiert auf der aktuellen Version von Windows Server 2019. Nach der Installation können Sie mit Windows Update Updates anwenden (einschließlich kritischer Updates). 

Überprüfen Sie den Hash der heruntergeladenen Datei „OAW.zip“, um sicherzustellen, dass sie nicht geändert wurde, bevor Sie sie zum Erstellen der OAW-VM verwenden. Führen Sie das folgende PowerShell-Skript aus. Wenn der Rückgabewert „True“ ist, können Sie die heruntergeladene „OAW.zip“ verwenden:

```powershell
param(
    [Parameter(Mandatory=$True)]
    [ValidateNotNullOrEmpty()]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [string]
    $DownloadedOAWZipFilePath
)

$expectedHash = '459D8BA232E4315372FCE7CDD705057D051C1BD60772FC36E0136C741C27A273'
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash

Write-Host "Expected hash: $expectedHash"

if ($expectedHash -eq $actualHash)
{
    Write-Host 'SUCCESS: OAW.zip file hash matches.'
}
else
{
    Write-Error 'ERROR: OAW.zip file hash does not match! It is not safe to use it, please download it again.'
    Write-Error "Actual hash: $actualHash"
}
```

## <a name="user-account-policy"></a>Benutzerkontorichtlinie 
Die folgende Benutzerkontorichtlinie wird auf die OAW-VM angewendet:

- Benutzername für das integrierte Administratorkonto: AdminUser
- MinimumPasswordLength = 14
- „PasswordComplexity“ aktiviert
- MinimumPasswordAge = 1 (Tag)
- MaximumPasswordAge = 42 (Tage)
- NewGuestName = GUser (standardmäßig deaktiviert)

## <a name="pre-installed-software"></a>Vorinstallierte Software
In der folgenden Tabelle ist die vorinstallierte Software auf der OAW-VM aufgeführt.

| Softwarename           | Standort                                                                                       |
|--------------------------|------------------------------------------------------------------------------------------------|
| [Microsoft Edge for Business](https://www.microsoft.com/edge/business/)                                            | \[Systemlaufwerk\]\Programme (x86)\Microsoft\Edge\Application                                                                                        |
| [Az-Module](./powershell-install-az-module.md)                         | \[Systemlaufwerk\]\Programme\WindowsPowerShell\Modules                                         |  
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)| \[Systemlaufwerk\]\Programme\PowerShell\7                                                                       |
| [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/?view=azure-cli-latest) | \[Systemlaufwerk\]\Programme (x86)\Microsoft SDKs\Azure\CLI2 |
| [Microsoft Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)   | \[Systemlaufwerk\]\Programme (x86)\Microsoft Azure Storage-Explorer                                                                       |
| [AzCopy](/azure/storage/common/storage-use-azcopy-v10)                             | \[Systemlaufwerk\]\VMSoftware\azcopy_windows_amd64_10.3.4                                         |
| [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools/tree/az)                  | \[Systemlaufwerk\]\VMSoftware\AzureStack-Tools                                                    |

## <a name="check-hlh-version"></a>Überprüfen der HLH-Version

1. Melden Sie sich mit Ihren Anmeldeinformationen beim HLH an.
1. Öffnen Sie Windows PowerShell ISE, und führen Sie folgendes Skript aus:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Beispiel:

   ![Screenshot des PowerShell-Cmdlets zum Überprüfen der Version der OAW-VM](./media/operator-access-workstation/check-hardware-lifecycle-host-version.png)

## <a name="create-the-oaw-vm-using-a-script"></a>Erstellen der OAW-VM mithilfe eines Skripts

Das folgende Skript bereitet die VM als die Arbeitsstation mit Operatorzugriff (OAW) vor, mit der Sie zu Verwaltungs- und Diagnosezwecken auf Microsoft Azure Stack Hub zugreifen.

1. Melden Sie sich mit Ihren Anmeldeinformationen beim HLH an.
1. Laden Sie die Datei „OAW.zip“ herunter, und extrahieren Sie die Dateien.
1. Öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten.
1. Navigieren Sie zum extrahierten Inhalt der Datei „OAW. zip“.
1. Führen Sie das Skript „New-OAW.ps1“ aus. 

Um die OAW-VM unter Verwendung von Azure Stack Hub-Version 2005 oder höher ohne Anpassung auf dem HLH zu erstellen, führen Sie das Skript „New-OAW.ps1“ beispielsweise nur mit dem Parameter **-LocalAdministratorPassword** aus:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword  
```

So erstellen Sie die OAW-VM auf einem Host mit Netzwerkverbindung mit Azure Stack Hub

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -IPAddress '192.168.0.20' `
   -SubnetMask '255.255.255.0' `
   -DefaultGateway '192.168.0.1' `
   -DNS '192.168.0.10'
```

So rufen Sie die IP-Adresse der ERCS-VM aus der Datei „AzureStackStampInformation.json“ ab:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

So erstellen Sie die OAW-VM auf dem HLH mit „DeploymentData.json“

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -DeploymentDataFilePath 'D:\AzureStack\DeploymentData.json'
```

Wenn die Datei „DeploymentData.json“ den Namenspräfix für die OAW-VM enthält, wird dieser Wert für den Parameter **VirtualMachineName** verwendet. Andernfalls ist der Standardname **AzSOAW** oder der vom Benutzer angegebene Name.

Für New-OAW stehen zwei Parametersätze zur Verfügung. Optionale Parameter werden in eckigen Klammern angezeigt.

```powershell
New-OAW 
-LocalAdministratorPassword <Security.SecureString> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-DNS <String[]>] `
[-DeploymentDataFilePath <String>] `
[-SkipNetworkConfiguration] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

```powershell
New-OAW
-LocalAdministratorPassword <Security.SecureString> `
-IPAddress <String> `
-SubnetMask <String> `
-DefaultGateway <String> `
-DNS <String[]> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

In der folgenden Tabelle finden Sie eine Beschreibung der einzelnen Parameter.

| Parameter   | Erforderlich/Optional  | Beschreibung       |
|-------------|--------------------|-------------------|
| LocalAdministratorPassword | Erforderlich | Das Kennwort für das lokale Administratorkonto „AdminUser“ der VM. |
| IPAddress                  | Erforderlich | Die statische IPv4-Adresse zum Konfigurieren von TCP/IP auf der VM.                                                |
| SubnetMask                 | Erforderlich | Die IPv4-Subnetzmaske zum Konfigurieren von TCP/IP auf der VM.                                                   |
| DefaultGateway             | Erforderlich | Die IPv4-Adresse des Standardgateways zum Konfigurieren von TCP/IP auf der VM.                                    |
| DNS                        | Erforderlich | DNS-Server zum Konfigurieren von TCP/IP auf der VM.                                                          |
| ImageFilePath              | Optional | Der Pfad der von Microsoft bereitgestellten Datei „OAW.vhdx“. Der Standardwert ist **OAW.vhdx** unter demselben übergeordneten Ordner dieses Skripts. |
| VirtualMachineName         | Optional | Der Name, der der VM zugewiesen werden soll. Wenn das Namenspräfix in der Datei „DeploymentData.json“ gefunden werden kann, wird es als Standardname verwendet. Andernfalls wird **AzSOAW** als Standardname verwendet. Sie können einen anderen Namen angeben, um den Standardwert zu überschreiben. |
| VirtualMachineMemory       | Optional | Der Arbeitsspeicher, der der VM zugewiesen werden soll. Der Standardwert ist **4 GB**.                            |
| VirtualProcessorCount      | Optional | Die Anzahl virtueller Prozessoren, die der VM zugewiesen werden sollen. Der Standardwert ist **8**.        |
| VirtualMachineDiffDiskPath | Optional | Der Pfad, an dem temporäre differenzierende Datenträgerdateien gespeichert werden, während die Verwaltungs-VM aktiv ist. Der Standardwert ist das Unterverzeichnis **DiffDisks** unter demselben übergeordneten Ordner dieses Skripts. |
| AzureStackCertificatePath  | Optional | Der Pfad der Zertifikate, die für den Azure Stack Hub-Zugriff in die VM importiert werden sollen. |
| AzSStampInfoFilePath       | Optional | Der Pfad der Datei „AzureStackStampInformation.json“, aus der das Skript die IPs der ERCS-VM abrufen kann. |
| CertificatePassword        | Optional | Das Kennwort oder Zertifikat, das für den Azure Stack Hub-Zugriff auf den virtuellen Computer importiert werden soll. |
| ERCSVMIP                   | Optional | Die IP-Adresse von Azure Stack Hub-ERCS-VMs, die der Liste vertrauenswürdiger Hosts der VM hinzugefügt werden sollen. Wird nicht angewendet, wenn **-SkipNetworkConfiguration** festgelegt ist. |
SkipNetworkConfiguration     | Optional | Überspringt die Netzwerkkonfiguration für die VM, sodass sie später vom Benutzer vorgenommen werden kann. |
| DeploymentDataFilePath     | Optional | Der Pfad der Datei „DeploymentData.json“. Wird nicht angewendet, wenn **-SkipNetworkConfiguration** festgelegt ist.            |
| PhysicalAdapterMACAddress  | Optional | Die MAC-Adresse des Netzwerkadapters des Hosts, der zum Herstellen einer Verbindung mit der VM verwendet wird.<br>- Wenn nur ein physischer Netzwerkadapter vorhanden ist, wird dieser verwendet, und der Parameter ist nicht erforderlich.<br>- Wenn mehrere physische Netzwerkadapter vorhanden sind, ist der Parameter erforderlich, um den gewünschten Netzwerkadapter anzugeben.<br> |
| VirtualSwitchName          | Optional | Der Name des virtuellen Switches (VMSwitch), der in Hyper-V für die VM konfiguriert werden muss.<br>- Wenn ein VMSwitch mit dem angegebenen Namen vorhanden ist, wird dieser ausgewählt.<br>- Wenn kein VMSwitch mit dem angegebenen Namen vorhanden ist, wird ein VMSwitch mit diesem Namen erstellt.<br> |
| ReCreate                   | Optional | Entfernt die VM und erstellt sie neu, wenn bereits eine VM mit demselben Namen vorhanden ist. |

## <a name="check-the-oaw-vm-version"></a>Überprüfen der Version der OAW-VM

1. Melden Sie sich mit Ihren Anmeldeinformationen bei der OAW-VM an.
1. Öffnen Sie Windows PowerShell ISE, und führen Sie folgendes Skript aus:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Beispiel:

   ![Screenshot des PowerShell-Cmdlets zum Überprüfen der Version des Hardwarelebenszyklus-Hosts (HLH)](./media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Übertragen von Dateien zwischen dem HLH und der OAW

Wenn Sie Dateien zwischen dem HLH und der OAW übertragen müssen, erstellen Sie mit dem Cmdlet [New-SmbShare](/powershell/module/smbshare/new-smbshare?view=win10-ps) eine SMB-Freigabe. „New-SmbShare“ macht einen Dateisystemordner für Remoteclients als SMB-Freigabe (Server Message Block) verfügbar. Beispiel:

Verwenden Sie das Cmdlet [Remove-SmbShare](/powershell/module/smbshare/remove-smbshare?view=win10-ps), um eine mit diesem Cmdlet erstellte Freigabe zu löschen. Beispiel:

## <a name="remove-the-oaw-vm"></a>Entfernen der OAW-VM

Das folgende Skript entfernt die OAW-VM, mit der Sie zu Verwaltungs- und Diagnosezwecken auf Azure Stack Hub zugreifen. Das Skript entfernt auch die Datenträgerdateien und den zugeordneten Wächter der VM.

1. Melden Sie sich mit Ihren Anmeldeinformationen beim HLH an.
1. Öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten. 
1. Navigieren Sie zum extrahierten Inhalt der installierten Datei „OAW. zip“.
1. Entfernen Sie die VM, indem Sie das Skript „Remove-OAW.ps1“ ausführen: 

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName <name>
   ```

   \<name\> ist der Name der VM, die entfernt werden soll. Standardmäßig lautet der Name **AzSOAW**.

   Beispiel:

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName AzSOAW
   ```

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Verwaltungsaufgaben](azure-stack-manage-basics.md)
