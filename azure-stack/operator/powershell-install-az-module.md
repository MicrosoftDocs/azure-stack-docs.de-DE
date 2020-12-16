---
title: Installieren des PowerShell Az-Moduls für Azure Stack Hub
description: Erfahren Sie, wie Sie PowerShell für Azure Stack Hub installieren.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 91ddbc5aeb9c10b49b21db331e6e7c71a8a15764
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935201"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>Installieren des PowerShell Az-Moduls für Azure Stack Hub

In diesem Artikel wird erläutert, wie Sie mithilfe von PowerShellGet das Azure PowerShell Az-Modul und kompatible Azure Stack Hub-Administratormodule installieren. Die Az-Module können auf Windows-, macOS- und Linux-Plattformen installiert werden.

Sie können die Az-Module für Azure Stack Hub auch in einem Docker-Container ausführen. Anleitungen finden Sie unter [Verwenden von Docker zum Ausführen von PowerShell für Azure Stack Hub](../user/azure-stack-powershell-user-docker.md).

Wenn Sie das PowerShell Resource Modules-Modul (AzureRM-Modul) für Azure Stack Hub installieren möchten, lesen Sie die Informationen unter [Installieren des PowerShell AzureRM-Moduls für Azure Stack Hub](azure-stack-powershell-install.md).

> [!IMPORTANT]
> Wahrscheinlich gibt es keine neuen Releases der Azure Resource Modules-Module. Für die Azure Resource Modules-Module wird nur noch Support für kritische Hotfixes bereitgestellt. In Zukunft wird es nur Az-Releases für Azure Stack Hub geben.

Sie können *API-Profile* verwenden, um die kompatiblen Endpunkte für die Azure Stack Hub-Ressourcenanbieter anzugeben.

API-Profile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack Hub zu verwalten. Ein API-Versionsprofil ist eine Gruppe von Azure Resource Manager-PowerShell-Modulen mit spezifischen API-Versionen. Jede Cloudplattform verfügt über eine Gruppe von unterstützten API-Versionsprofilen. Beispielsweise unterstützt Azure Stack Hub eine bestimmte Profilversion, z. B. **2019-03-01-hybrid**. Wenn Sie ein Profil installieren, werden die Azure Resource Manager-PowerShell-Module installiert, die dem angegebenen Profil entsprechen.

Sie können mit Azure Stack Hub kompatible PowerShell Az-Module in einem Szenario mit Internetverbindung, mit teilweiser Internetverbindung oder ohne Internetverbindung installieren. Dieser Artikel leitet Sie durch die detaillierten Anweisungen für diese Szenarien.

## <a name="1-verify-your-prerequisites"></a>1. Überprüfen der Voraussetzungen

AZ-Module werden in Azure Stack Hub mit Update 2002 oder höher unterstützt, wenn die aktuellen Hotfixes installiert sind. Weitere Informationen finden Sie in den [Versionshinweisen zu Azure Stack Hub](release-notes.md).

Die Azure PowerShell-Az-Module können mit PowerShell 5.1 oder höher unter Windows oder mit PowerShell Core 6.x und höher auf allen Plattformen verwendet werden. Sie sollten die [letzte Version von PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) installieren, die für Ihr Betriebssystem verfügbar ist. Bei der Ausführung in PowerShell Core gibt es keine zusätzlichen Anforderungen an Azure PowerShell.

Führen Sie den Befehl aus, um Ihre PowerShell-Version zu überprüfen:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Voraussetzungen für Windows
So verwenden Sie Azure PowerShell in PowerShell 5.1 unter Windows:

1. Führen Sie bei Bedarf ein Update auf [Windows PowerShell 5.1](/powershell/scripting/windows-powershell/install/installing-windows-powershell#upgrading-existing-windows-powershell) aus. Unter Windows 10 ist PowerShell 5.1 bereits installiert.
2. Installieren Sie [.NET Framework 4.7.2 oder höher](/dotnet/framework/install).
3. Vergewissern Sie sich, dass die aktuelle Version von PowerShellGet installiert ist. Führen Sie `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force` aus. 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. Voraussetzungen für Linux und Mac
Mindestens PowerShell Core 6.x erforderlich. Anweisungen finden Sie unter [diesem Link](/powershell/scripting/install/installing-powershell-core-on-windows).

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. Deinstallieren vorhandener Versionen der Azure Stack Hub-PowerShell-Module

Deinstallieren Sie vor der Installation der erforderlichen Version unbedingt alle zuvor installierten Azure Resource Manager- oder Az-PowerShell-Module für Azure Stack Hub. Deinstallieren Sie die Module mit einer der folgenden zwei Methoden:

1. Schließen Sie alle aktiven PowerShell-Sitzungen, und führen Sie die folgenden Cmdlets aus, um die vorhandenen Azure Resource Manager- und Az PowerShell-Module zu deinstallieren:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    Wenn eine Fehlermeldung wie „Das Modul wird bereits verwendet“ auftritt, schließen Sie die PowerShell-Sitzungen, die die Module verwenden, und führen Sie das obige Skript erneut aus.

2. Löschen Sie aus den Ordnern `C:\Program Files\WindowsPowerShell\Modules` und `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` alle Ordner, die mit `Azure`, `Az` oder `Azs.` beginnen. Beim Löschen dieser Ordner werden alle vorhandenen PowerShell-Module entfernt.

## <a name="4-connected-install-with-internet-connectivity"></a>4. Verbunden: Installieren mit Internetverbindung

Das Azure Stack Az-Modul kann mit Azure Stack Hub 2002 oder höher verwendet werden. Darüber hinaus kann das Azure Stack Az-Modul auf einem Windows-Computer mit PowerShell 5.1 oder höher oder auf einer Linux- bzw. macOS-Plattform mit PowerShell 6.x oder höher verwendet werden. Die Verwendung der PowerShellGet-Cmdlets ist die bevorzugte Installationsmethode. Die Funktionsweise dieser Methode ist auf den unterstützten Plattformen gleich.

Führen Sie den folgenden Befehl in einer PowerShell-Sitzung aus:

```powershell  
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
```

> [!Note]  
> Azure Stack Hub-Modulversion 2.0.0 ist ein Breaking Change. Ausführliche Informationen finden Sie unter [Migrieren von AzureRM zum Az-Modul von Azure PowerShell in Azure Stack Hub](migrate-azurerm-az.md).

> [!WARNING]
> Die Module Azure Resource Manager (AzureRM) und Az können für PowerShell 5.1 für Windows nicht gleichzeitig installiert sein. Wenn Azure Resource Manager weiterhin auf Ihrem System verfügbar sein soll, installieren Sie das Az-Modul für PowerShell Core 6.x oder höher. Installieren Sie dazu [PowerShell Core 6.x oder höher](/powershell/scripting/install/installing-powershell-core-on-windows), und befolgen Sie dann diese Anweisungen in einem PowerShell Core-Terminal.

## <a name="5-disconnected-install-without-internet-connection"></a>5. Nicht verbunden: Installieren ohne Internetverbindung

In einem Szenario ohne Internetverbindung müssen Sie zuerst die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen. Anschließend übertragen Sie sie für den Installationsvorgang in das Azure Stack Development Kit (ASDK).

Melden Sie sich bei einem Computer mit Internetverbindung an, und verwenden Sie die folgenden Skripts, um die Pakete „Azure Resource Manager“ und „Azure Stack Hub“ abhängig von Ihrer Version von Azure Stack Hub herunterzuladen.

Die Installation umfasst fünf Schritte:

1. Installieren von Azure Stack Hub-PowerShell auf einem verbundenen Computer.
2. Aktivieren zusätzlicher Speicherfunktionen
3. Übertragen der PowerShell-Pakete auf Ihre getrennte Arbeitsstation
4. Manuelles Bootstrapping des NuGet-Anbieters auf Ihrer getrennten Arbeitsstation
5. Bestätigen der Installation von PowerShell

### <a name="install-azure-stack-hub-powershell"></a>Installieren von Azure Stack Hub PowerShell

::: moniker range=">=azs-2002"
Azure Stack Hub 2002 oder höher:

Sie können Azure Resource Manager- oder Az-Module verwenden. Informationen zu Azure Resource Manager finden Sie in den Anweisungen zum [Installieren des PowerShell AzureRM-Moduls](powershell-install-az-module.md). Der folgende Code speichert Module aus dem vertrauenswürdigen Onlinerepository https://www.powershellgallery.com/.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$savedModulesPath = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $savedModulesPath -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $savedModulesPath -Force -RequiredVersion 2.0.2-preview
```
::: moniker-end

> [!NOTE]  
> Für Computer ohne Internetverbindung empfiehlt es sich, das folgende Cmdlet auszuführen, um das Sammeln von Telemetriedaten zu deaktivieren. Andernfalls kommt es bei den Cmdlets unter Umständen zu einer Leistungsbeeinträchtigung, wenn die Telemetriedatensammlung nicht deaktiviert wird. Dies betrifft nur Computer ohne Internetverbindung.
> ```powershell
> Disable-AzDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Hinzufügen Ihrer Paketen auf Ihrer Arbeitsstation

1. Kopieren Sie die heruntergeladenen Pakete auf ein USB-Gerät.

2. Melden Sie sich bei der getrennten Arbeitsstation an, und kopieren Sie die Pakete vom USB-Gerät an einen Speicherort auf der Arbeitsstation.

3. Manuelles Bootstrapping des NuGet-Anbieters auf Ihrer getrennten Arbeitsstation Anweisungen finden Sie unter [Manuelles Bootstrapping des NuGet-Anbieters auf einem Computer, der nicht mit dem Internet verbunden ist](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Registrieren Sie diesen Speicherort als Standardrepository, und installieren Sie die Module `AzureRM` und `AzureStack` aus diesem Repository:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.2-preview -AllowPrerelease -Scope AllUsers

   Install-Module -Name Az -Repository $RepoName -RequiredVersion 0.10.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>Bestätigen der Installation der PowerShell

Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

```powershell
Get-Module -Name "Az*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Konfigurieren von PowerShell für die Verwendung eines Proxyservers

In Szenarien, für die ein Proxyserver für den Zugriff auf das Internet erforderlich ist, konfigurieren Sie zuerst PowerShell für die Verwendung eines vorhandenen Proxyservers:

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.
2. Führen Sie die folgenden Befehle aus:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="7-use-the-az-module"></a>7. Verwenden des Az-Moduls

Sie können die auf Azure Resource Manager basierenden Cmdlets und Codebeispiele verwenden. Allerdings sollten Sie den Namen der Module und Cmdlets ändern. Die Modulnamen haben sich geändert, sodass `AzureRM` und Azure zu `Az` werden. Gleiches gilt für die Cmdlets. So wurde beispielsweise das Modul `AzureRM.Compute` in `Az.Compute` umbenannt.` New-AzureRMVM` wurde zu ` New-AzVM`, und `Get-AzureStorageBlob` ist jetzt `Get-AzStorageBlob`.

Eine ausführlichere Erläuterung und Anleitungen zum Verschieben eines AzureRM-Skripts in Az sowie Informationen zu Breaking Changes im Az-Modul von Azure Stack Hub finden Sie unter [Migrieren von AzureRM zum Az-Modul von Azure PowerShell](migrate-azurerm-az.md).

## <a name="next-steps"></a>Nächste Schritte

- [Herunterladen von Azure Stack Hub-Tools aus GitHub](azure-stack-powershell-download.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](../user/azure-stack-powershell-configure-user.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Operators](azure-stack-powershell-configure-admin.md)
- [Verwalten von API-Versionsprofilen in Azure Stack Hub](../user/azure-stack-version-profiles.md)
