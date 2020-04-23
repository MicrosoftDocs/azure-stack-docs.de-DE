---
title: Installieren des PowerShell AzureRM-Moduls für Azure Stack Hub
description: Erfahren Sie, wie Sie PowerShell für Azure Stack Hub installieren.
author: mattbriggs
ms.topic: article
ms.date: 04/14/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: 937a3610891a2ec532e5c95bc60a9b9c5a8cc684
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395000"
---
# <a name="install-powershell-azurerm-or-az-module-for-azure-stack-hub"></a>Installieren des PowerShell AzureRM- oder Az-Moduls für Azure Stack Hub

Azure PowerShell AzureRM bietet eine Reihe von Cmdlets, die das Azure Resource Manager-Modell für die Verwaltung von Azure Stack Hub-Ressourcen verwenden.

Darüber hinaus müssen Sie *API-Profile* verwenden, um die kompatiblen Endpunkte für die Azure Stack Hub-Ressourcenanbieter anzugeben.

API-Profile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack Hub zu verwalten. Ein API-Versionsprofil ist eine Gruppe von Azure Resource Manager-PowerShell-Modulen mit spezifischen API-Versionen. Jede Cloudplattform verfügt über eine Gruppe von unterstützten API-Versionsprofilen. Beispielsweise unterstützt Azure Stack Hub eine bestimmte Profilversion, z. B. **2019-03-01-hybrid**. Wenn Sie ein Profil installieren, werden die Azure Resource Manager-PowerShell-Module installiert, die dem angegebenen Profil entsprechen.

Sie können mit Azure Stack Hub kompatible PowerShell-Module in einem Szenario mit Internetverbindung, mit teilweiser Internetverbindung oder ohne Internetverbindung installieren. Dieser Artikel leitet Sie durch die detaillierten Anweisungen für diese Szenarien.

## <a name="1-verify-your-prerequisites"></a>1. Überprüfen der Voraussetzungen

Bevor Sie mit Azure Stack Hub und dem PowerShell AzureRM-Modul beginnen, müssen folgende Voraussetzungen erfüllt sein:

- **PowerShell-Version 5.1** <br>
Führen Sie zum Überprüfen Ihrer Version **$PSVersionTable.PSVersion** aus, und vergleichen Sie die **Hauptversion**. Wenn Sie nicht über PowerShell 5.1 verfügen, befolgen Sie die Anweisungen unter [Installieren von Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

  > [!Note]
  > Für PowerShell 5.1 muss ein Windows-Computer verwendet werden.

- **Führen Sie PowerShell in einer Eingabeaufforderung mit erhöhten Rechten aus.**

- **Zugriff auf den PowerShell-Katalog** <br>
  Sie benötigen Zugriff auf den [PowerShell-Katalog](https://www.powershellgallery.com). Der Katalog ist das zentrale Repository für PowerShell-Inhalte. Das Modul **PowerShellGet** enthält Cmdlets zum Ermitteln, Installieren, Aktualisieren und Veröffentlichen von PowerShell-Artefakten. Beispiele für diese Artefakte sind Module, DSC-Ressourcen, Rollenfunktionen und Skripts aus dem PowerShell-Katalog und anderen privaten Repositorys. Bei Verwendung von PowerShell in einem Szenario ohne Internetverbindung müssen Sie Ressourcen über einen Computer mit Internetverbindung abrufen und an einem Speicherort speichern, auf den Sie von Ihrem Computer ohne Internetverbindung aus zugreifen können.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Überprüfen der Verfügbarkeit des PowerShell-Katalogs

Überprüfen Sie, ob PSGallery als Repository registriert ist.

> [!Note]  
> Für diesen Schritt ist Internetzugriff erforderlich.

Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

```powershell
Install-module -Name PowerShellGet -Force
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Falls das Repository nicht registriert ist, öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. Deinstallieren vorhandener Versionen der Azure Stack Hub-PowerShell-Module

Deinstallieren Sie vor der Installation der erforderlichen Version unbedingt alle zuvor installierten AzureRM-PowerShell-Module von Azure Stack Hub. Deinstallieren Sie die Module mit einer der folgenden zwei Methoden:

1. Schließen Sie alle aktiven PowerShell-Sitzungen, und führen Sie die folgenden Cmdlets aus, um die vorhandenen AzureRM- und Azure PowerShell-Module zu deinstallieren:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```

    Wenn eine Fehlermeldung wie „Das Modul wird bereits verwendet“ auftritt, schließen Sie die PowerShell-Sitzungen, die die Module verwenden, und führen Sie das obige Skript erneut aus.

2. Löschen Sie aus den Ordnern `C:\Program Files\WindowsPowerShell\Modules` und `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` alle Ordner, die mit `Azure`, `Az` oder `Azs.` beginnen. Beim Löschen dieser Ordner werden alle vorhandenen PowerShell-Module entfernt.

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. Verbunden: Installieren von PowerShell für Azure Stack Hub mit Internetverbindung

Das API-Versionsprofil und die Azure Stack Hub-PowerShell-Module, die Sie benötigen, hängen von der jeweils ausgeführten Version von Azure Stack Hub ab.

### <a name="install-azure-stack-hub-powershell"></a>Installieren von Azure Stack Hub PowerShell

Führen Sie zum Installieren dieser Module auf der Entwicklungsarbeitsstation das folgende PowerShell-Skript aus:

::: moniker range=">=azs-2002"
Für Azure Stack Hub 2002 oder höher:

Sie können AzureRM-Module oder Az-Vorschaumodule verwenden. Für die Verwendung der Az-Module sind Azure Stack Hub 2002 und der aktuelle Hotfix erforderlich.

Befolgen Sie zum Verwenden von Az-Vorschaumodulen die Anweisungen unter [Installieren des PowerShell Az-Vorschaumoduls für Azure Stack Hub](powershell-install-az-module.md).

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.1
```

::: moniker-end
::: moniker range="azs-1910"
Für Azure Stack Hub 1910:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.0
```

> [!Note]  
> - Azure Stack Hub-Modulversion 1.8.0 ist ein Breaking Change-Release. Details finden Sie in den [Versionshinweisen](release-notes.md).

::: moniker-end
::: moniker range="<=azs-1908"
Für Azure Stack Hub 1908 oder früher:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> Azure Stack Hub-Modulversion 1.7.2 ist ein Breaking Change-Release. Informationen zum Migrieren von Azure Stack Hub 1.6.0 finden Sie im [Migrationsleitfaden](https://aka.ms/azspshmigration171).

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>Bestätigen der Installation der PowerShell

Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

War die Installation erfolgreich, werden die `AzureRm`- und `AzureStack`-Module in der Ausgabe angezeigt.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Nicht verbunden: Installieren von PowerShell ohne Internetverbindung

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

Sie können AzureRM- oder Az-Vorschaumodule verwenden. Informationen zu Az-Modulen finden Sie in den Anweisungen zum [Installieren des PowerShell Az-Vorschaumoduls für Azure Stack Hub](powershell-install-az-module.md).

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.1
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack Hub 1910:

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> Azure Stack Hub-Modulversion 1.8.0 ist ein Breaking Change-Release. Details finden Sie in den [Versionshinweisen](release-notes.md).

::: moniker-end
::: moniker range="<=azs-1908"
Für Azure Stack Hub 1908 oder früher:

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> Azure Stack Hub-Modulversion 1.7.1 ist ein Breaking Change. Informationen zum Migrieren von Azure Stack Hub 1.6.0 finden Sie im [Migrationsleitfaden](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

::: moniker-end

> [!NOTE]  
> Für Computer ohne Internetverbindung empfiehlt es sich, das folgende Cmdlet auszuführen, um das Sammeln von Telemetriedaten zu deaktivieren. Andernfalls kommt es bei den Cmdlets unter Umständen zu einer Leistungsbeeinträchtigung, wenn die Telemetriedatensammlung nicht deaktiviert wird. Dies betrifft nur Computer ohne Internetverbindung.
> ```powershell
> Disable-AzureRmDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Hinzufügen Ihrer Paketen auf Ihrer Arbeitsstation

1. Kopieren Sie die heruntergeladenen Pakete auf ein USB-Gerät.

2. Melden Sie sich bei der getrennten Arbeitsstation an, und kopieren Sie die Pakete vom USB-Gerät an einen Speicherort auf der Arbeitsstation.

3. Manuelles Bootstrapping des NuGet-Anbieters auf Ihrer getrennten Arbeitsstation Anweisungen finden Sie unter [Manuelles Bootstrapping des NuGet-Anbieters auf einem Computer, der nicht mit dem Internet verbunden ist](https://docs.microsoft.com/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Registrieren Sie diesen Speicherort als Standardrepository, und installieren Sie die Module AzureRM und `AzureStack` aus diesem Repository:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Bestätigen der Installation der PowerShell

Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

```powershell
Get-Module -Name "Azure*" -ListAvailable
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

## <a name="next-steps"></a>Nächste Schritte

- [Herunterladen von Azure Stack Hub-Tools aus GitHub](azure-stack-powershell-download.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](../user/azure-stack-powershell-configure-user.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Operators](azure-stack-powershell-configure-admin.md)
- [Verwalten von API-Versionsprofilen in Azure Stack Hub](../user/azure-stack-version-profiles.md)
