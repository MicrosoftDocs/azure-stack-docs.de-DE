---
title: Installieren von PowerShell für Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie PowerShell für Azure Stack installieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/09/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 1c555f39bdf37314bae05666d39daca50cde2c4e
ms.sourcegitcommit: 426380a3a27954cd609ba52d1066d9d69f5267fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65532241"
---
# <a name="install-powershell-for-azure-stack"></a>Installieren von PowerShell für Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Um mit Ihrer Cloud arbeiten zu können, müssen Sie Azure Stack-kompatible PowerShell-Module installieren. Die Kompatibilität wird über die Funktion *API profiles* (API-Profile) aktiviert.

API-Profile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack zu verwalten. Ein API-Versionsprofil ist eine Gruppe von Azure Resource Manager-PowerShell-Modulen mit spezifischen API-Versionen. Jede Cloudplattform verfügt über eine Gruppe von unterstützten API-Versionsprofilen. Beispielsweise unterstützt Azure Stack eine bestimmte Profilversion, z. B. **2018-03-01-hybrid**. Wenn Sie ein Profil installieren, werden die Azure Resource Manager-PowerShell-Module installiert, die dem angegebenen Profil entsprechen.

Sie können mit Azure Stack kompatible PowerShell-Module in einem Szenario mit Internetverbindung, mit teilweiser Internetverbindung oder ohne Internetverbindung installieren. Dieser Artikel leitet Sie durch die detaillierten Anweisungen für diese Szenarien.

## <a name="1-verify-your-prerequisites"></a>1. Überprüfen der Voraussetzungen

Bevor Sie mit Azure Stack und PowerShell beginnen, müssen folgende Voraussetzungen erfüllt sein:

- **PowerShell Version 5.0**: Führen Sie zum Überprüfen Ihrer Version **$PSVersionTable.PSVersion** aus, und vergleichen Sie die **Hauptversion**. Wenn Sie nicht über PowerShell 5.0 verfügen, befolgen Sie die Anweisungen unter [Installieren von Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > Für PowerShell 5.0 muss ein Windows-Computer verwendet werden.

- **Führen Sie PowerShell in einer Eingabeaufforderung mit erhöhten Rechten aus.**

- **PowerShell-Katalogzugriff**: Sie benötigen Zugriff auf den [PowerShell-Katalog](https://www.powershellgallery.com). Der Katalog ist das zentrale Repository für PowerShell-Inhalte. Das **PowerShellGet**-Modul enthält Cmdlets zum Ermitteln, Installieren, Aktualisieren und Veröffentlichen von PowerShell-Artefakten, z.B. Modulen, DSC-Ressourcen, Rollenfunktionen und Skripts aus dem PowerShell-Katalog und anderen privaten Repositorys. Bei Verwendung von PowerShell in einem Szenario ohne Internetverbindung müssen Sie Ressourcen über einen Computer mit Internetverbindung abrufen und an einem Speicherort speichern, auf den Sie von Ihrem Computer ohne Internetverbindung aus zugreifen können.

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

Öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus, falls das Repository nicht registriert ist:

```powershell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Deinstallieren vorhandener Versionen der Azure Stack-PowerShell-Module

Deinstallieren Sie vor der Installation der erforderlichen Version unbedingt alle zuvor installierten AzureRM-PowerShell-Module von Azure Stack. Verwenden Sie zum Deinstallieren eine folgenden Methoden:

1. Schließen Sie alle aktiven PowerShell-Sitzungen, und führen Sie die folgenden Cmdlets aus, um die vorhandenen AzureRM-PowerShell-Module zu deinstallieren:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Wenn eine Fehlermeldung wie „Das Modul wird bereits verwendet“ auftritt, schließen Sie die PowerShell-Sitzungen, die die Module verwenden, und führen Sie das obige Skript erneut aus.

2. Löschen Sie aus den Ordnern `C:\Program Files\WindowsPowerShell\Modules` und `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` alle Ordner, die mit `Azure` oder `Azs.` beginnen. Beim Löschen dieser Ordner werden alle vorhandenen PowerShell-Module entfernt.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Verbunden: Installieren von PowerShell für Azure Stack mit Internetverbindung

Verwenden Sie AzureRM Version 2.4.0 für Azure Stack Version 1901 oder höher. Installieren Sie zusätzlich zu den AzureRM-Modulen die Azure Stack-spezifischen PowerShell-Module. Das API-Versionsprofil und die Azure Stack-PowerShell-Module, die Sie benötigen, hängen von der Version von Azure Stack ab, die Sie ausführen.

Die Installation umfasst drei Schritte:

1. Installieren der Azure Stack PowerShell, abhängig von Ihrer Version von Azure Stack
2. Aktivieren zusätzlicher Speicherfunktionen
3. Bestätigen der Installation der PowerShell

### <a name="install-azure-stack-powershell"></a>Installieren von Azure Stack-PowerShell

Führen Sie zum Installieren dieser Module auf der Entwicklungsarbeitsstation das folgende PowerShell-Skript aus:

- Für Azure Stack 1904 oder höher:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Get-AzureRMProfile -Update
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- Für Azure Stack-Versionen bis 1903 installieren Sie nur die beiden folgenden Module:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Die Azure Stack-Modulversion 1.7.1 ist ein Breaking Change-Release. Informationen zum Migrieren von Azure Stack 1.6.0 finden Sie im [Migrationsleitfaden](https://aka.ms/azspshmigration171).
    > - Die AzureRm-Modulversion 2.4.0 enthält einen Breaking Change für das Cmdlet „Remove-AzureRmStorageAccount“. Von diesem Cmdlet wird die Angabe des Parameters „-Force“ erwartet, um das Speicherkonto ohne Bestätigung zu entfernen.
    > - Zum Installieren der Module für Azure Stack-Versionen ab 1901 brauchen Sie **AzureRM.Bootstrapper** nicht zu installieren.
    > - Installieren Sie das 2018-03-01-Hybridprofil nicht zusätzlich zur Verwendung der oben genannten AzureRM-Module für Azure Stack-Versionen ab 1901.

- Für Azure Stack-Version 1811 installieren Sie das Profil mit **AzureRM.Bootstrapper**, zusätzlich zu den in den Cmdlets angegebenen Versionen:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

### <a name="enable-additional-storage-features"></a>Aktivieren zusätzlicher Speicherfunktionen

Um die zusätzlichen Speicherfunktionen (im Abschnitt „Verbunden“ erwähnt) zu verwenden, laden Sie ebenfalls die folgenden Pakete herunter, und installieren Sie sie.

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Bestätigen der Installation der PowerShell

Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

War die Installation erfolgreich, werden die AzureRM- und AzureStack-Module in der Ausgabe angezeigt.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Nicht verbunden: Installieren von PowerShell ohne Internetverbindung

In einem nicht verbundenen Szenario müssen Sie zuerst die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen und sie dann für die Installation in das Azure Stack Development Kit übertragen.

Melden Sie sich bei einem Computer mit Internetverbindung an, und verwenden Sie die folgenden Skripts, um die Pakete „Azure Resource Manager“ und „Azure Stack“ abhängig von Ihrer Version von Azure Stack herunterzuladen.

Die Installation umfasst vier Schritte:

1. Installieren der Azure Stack-PowerShell auf einem verbundenen Computer
2. Aktivieren zusätzlicher Speicherfunktionen
3. Übertragen der PowerShell-Pakete auf Ihre getrennte Arbeitsstation
4. Bestätigen der Installation der PowerShell

### <a name="install-azure-stack-powershell"></a>Installieren von Azure Stack-PowerShell

- Azure Stack 1904 oder höher

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack 1903 oder früher

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Die Azure Stack-Modulversion 1.7.1 ist ein Breaking Change. Informationen zum Migrieren von Azure Stack 1.6.0 finden Sie im [Migrationsleitfaden](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

  - Azure Stack 1811 oder früher.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 oder früher.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > Für Computer ohne Internetverbindung empfiehlt es sich, das folgende Cmdlet auszuführen, um das Sammeln von Telemetriedaten zu deaktivieren. Andernfalls kommt es bei den Cmdlets unter Umständen zu einer Leistungsbeeinträchtigung, wenn die Telemetriedatensammlung nicht deaktiviert wird. Dies betrifft nur Computer ohne Internetverbindung.
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>Aktivieren zusätzlicher Speicherfunktionen

Um die zusätzlichen Speicherfunktionen (im Abschnitt „Verbunden“ erwähnt) zu verwenden, laden Sie ebenfalls die folgenden Pakete herunter, und installieren Sie sie.

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>Hinzufügen Ihrer Paketen auf Ihrer Arbeitsstation

1. Kopieren Sie die heruntergeladenen Pakete auf ein USB-Gerät.

2. Melden Sie sich bei der getrennten Arbeitsstation an, und kopieren Sie die Pakete vom USB-Gerät an einen Speicherort auf der Arbeitsstation.

3. Nun registrieren Sie diesen Speicherort als Standardrepository und installieren die AzureRM- und AzureStack-Module aus diesem Repository:

   ```powershell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

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

In Szenarien, für die ein Proxyserver für den Zugriff auf das Internet erforderlich ist, müssen Sie zuerst PowerShell für die Verwendung eines vorhandenen Proxyservers konfigurieren:

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.
2. Führen Sie die folgenden Befehle aus:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Herunterladen von Azure Stack-Tools von GitHub](azure-stack-powershell-download.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](../user/azure-stack-powershell-configure-user.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md)
- [Verwalten von API-Versionsprofilen in Azure Stack](../user/azure-stack-version-profiles.md)
