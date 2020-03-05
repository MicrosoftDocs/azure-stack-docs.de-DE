---
title: Installieren von PowerShell für Azure Stack Hub
description: Erfahren Sie, wie Sie PowerShell für Azure Stack Hub installieren.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 40cd0cd279d98773e97bbae816dbd40ea2d1eba8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698197"
---
# <a name="install-powershell-for-azure-stack-hub"></a>Installieren von PowerShell für Azure Stack Hub

Azure PowerShell bietet eine Reihe von Cmdlets, die das Azure Resource Manager-Modell für die Verwaltung von Azure Stack Hub-Ressourcen verwenden.

Um mit Ihrer Cloud arbeiten zu können, müssen Sie Azure Stack Hub-kompatible PowerShell-Module installieren. Azure Stack Hub verwendet das **AzureRM**-Modul statt des neueren **AzureAZ**-Moduls, das im globalen Azure verwendet wird. Darüber hinaus müssen Sie *API-Profile* verwenden, um die kompatiblen Endpunkte für die Azure Stack Hub-Ressourcenanbieter anzugeben.

API-Profile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack Hub zu verwalten. Ein API-Versionsprofil ist eine Gruppe von Azure Resource Manager-PowerShell-Modulen mit spezifischen API-Versionen. Jede Cloudplattform verfügt über eine Gruppe von unterstützten API-Versionsprofilen. Beispielsweise unterstützt Azure Stack Hub eine bestimmte Profilversion, z. B. **2019-03-01-hybrid**. Wenn Sie ein Profil installieren, werden die Azure Resource Manager-PowerShell-Module installiert, die dem angegebenen Profil entsprechen.

Sie können mit Azure Stack Hub kompatible PowerShell-Module in einem Szenario mit Internetverbindung, mit teilweiser Internetverbindung oder ohne Internetverbindung installieren. Dieser Artikel leitet Sie durch die detaillierten Anweisungen für diese Szenarien.

## <a name="1-verify-your-prerequisites"></a>1. Überprüfen der Voraussetzungen

Bevor Sie mit Azure Stack Hub und PowerShell beginnen, müssen folgende Voraussetzungen erfüllt sein:

- **PowerShell-Version 5.0** <br>
Führen Sie zum Überprüfen Ihrer Version **$PSVersionTable.PSVersion** aus, und vergleichen Sie die **Hauptversion**. Wenn Sie nicht über PowerShell 5.0 verfügen, befolgen Sie die Anweisungen unter [Installieren von Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

  > [!Note]
  > Für PowerShell 5.0 muss ein Windows-Computer verwendet werden.

- **Führen Sie PowerShell in einer Eingabeaufforderung mit erhöhten Rechten aus.**

- **Zugriff auf den PowerShell-Katalog** <br>
  Sie benötigen Zugriff auf den [PowerShell-Katalog](https://www.powershellgallery.com). Der Katalog ist das zentrale Repository für PowerShell-Inhalte. Das Modul **PowerShellGet** enthält Cmdlets zum Ermitteln, Installieren, Aktualisieren und Veröffentlichen von PowerShell-Artefakten. Beispiele für diese Artefakte sind Module, DSC-Ressourcen, Rollenfunktionen und Skripts aus dem PowerShell-Katalog und anderen privaten Repositorys. Bei Verwendung von PowerShell in einem Szenario ohne Internetverbindung müssen Sie Ressourcen über einen Computer mit Internetverbindung abrufen und an einem Speicherort speichern, auf den Sie von Ihrem Computer ohne Internetverbindung aus zugreifen können.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Überprüfen der Verfügbarkeit des PowerShell-Katalogs

Überprüfen Sie, ob PSGallery als Repository registriert ist.

> [!Note]  
> Für diesen Schritt ist Internetzugriff erforderlich.

Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
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

1. Schließen Sie alle aktiven PowerShell-Sitzungen, und führen Sie die folgenden Cmdlets aus, um die vorhandenen AzureRM-PowerShell-Module zu deinstallieren:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Wenn eine Fehlermeldung wie „Das Modul wird bereits verwendet“ auftritt, schließen Sie die PowerShell-Sitzungen, die die Module verwenden, und führen Sie das obige Skript erneut aus.

2. Löschen Sie aus den Ordnern `C:\Program Files\WindowsPowerShell\Modules` und `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` alle Ordner, die mit `Azure` oder `Azs.` beginnen. Beim Löschen dieser Ordner werden alle vorhandenen PowerShell-Module entfernt.

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. Verbunden: Installieren von PowerShell für Azure Stack Hub mit Internetverbindung

Das API-Versionsprofil und die Azure Stack Hub-PowerShell-Module, die Sie benötigen, hängen von der jeweils ausgeführten Version von Azure Stack Hub ab.

### <a name="install-azure-stack-hub-powershell"></a>Installieren von Azure Stack Hub PowerShell

Führen Sie zum Installieren dieser Module auf der Entwicklungsarbeitsstation das folgende PowerShell-Skript aus:

- Für Azure Stack Hub 1910 oder höher:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.8.0
    ```

- Für Azure Stack Hub 1908 oder höher als 1903:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- Für Azure Stack Hub-Version 1903 oder früher installieren Sie nur die beiden folgenden Module:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.

    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Azure Stack Hub-Modulversion 1.8.0 ist ein Breaking Change-Release. Details finden Sie in den [Versionshinweisen](release-notes.md).
    > - Azure Stack Hub-Modulversion 1.7.2 ist ein Breaking Change-Release. Informationen zum Migrieren von Azure Stack Hub 1.6.0 finden Sie im [Migrationsleitfaden](https://aka.ms/azspshmigration171).
    > - Die AzureRM-Modulversion 2.4.0 enthält einen Breaking Change für das Cmdlet „Remove-AzureRmStorageAccount“. Von diesem Cmdlet wird die Angabe des Parameters `-Force` erwartet, um das Speicherkonto ohne Bestätigung zu entfernen.
    > - Zum Installieren der Module für Azure Stack Hub-Versionen ab 1901 müssen Sie **AzureRM.BootStrapper** nicht installieren.
    > - Installieren Sie das 2018-03-01-Hybridprofil nicht zusätzlich zur Verwendung der oben genannten AzureRM-Module für Azure Stack Hub-Versionen ab 1901.

### <a name="confirm-the-installation-of-powershell"></a>Bestätigen der Installation der PowerShell

Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

War die Installation erfolgreich, werden die `AzureRM`- und `AzureStack`-Module in der Ausgabe angezeigt.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Nicht verbunden: Installieren von PowerShell ohne Internetverbindung

In einem Szenario ohne Internetverbindung müssen Sie zuerst die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen. Anschließend übertragen Sie sie für den Installationsvorgang in das Azure Stack Development Kit (ASDK).

Melden Sie sich bei einem Computer mit Internetverbindung an, und verwenden Sie die folgenden Skripts, um die Pakete „Azure Resource Manager“ und „Azure Stack Hub“ abhängig von Ihrer Version von Azure Stack Hub herunterzuladen.

Die Installation umfasst vier Schritte:

1. Installieren von Azure Stack Hub-PowerShell auf einem verbundenen Computer.
2. Aktivieren zusätzlicher Speicherfunktionen
3. Übertragen der PowerShell-Pakete auf Ihre getrennte Arbeitsstation
4. Manuelles Bootstrapping des NuGet-Anbieters auf Ihrer getrennten Arbeitsstation
5. Bestätigen der Installation von PowerShell

### <a name="install-azure-stack-hub-powershell"></a>Installieren von Azure Stack Hub PowerShell

- Azure Stack Hub 1910 oder höher.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
    ```

- Für Azure Stack Hub 1908 oder höher als 1903:

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack Hub 1903 oder früher.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Azure Stack Hub-Modulversion 1.8.0 ist ein Breaking Change-Release. Details finden Sie in den [Versionshinweisen](release-notes.md).
    > Azure Stack Hub-Modulversion 1.7.1 ist ein Breaking Change. Informationen zum Migrieren von Azure Stack Hub 1.6.0 finden Sie im [Migrationsleitfaden](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

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
