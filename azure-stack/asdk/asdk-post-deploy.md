---
title: Konfigurationen nach der Bereitstellung für das ASDK
description: Erfahren Sie etwas über die empfohlenen Konfigurationsänderungen, die nach der Installation des Azure Stack Development Kits (ASDK) vorgenommen werden müssen.
author: justinha
ms.topic: article
ms.date: 12/03/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 12/03/2020
ms.openlocfilehash: 3a9852cfa650df8d533b40508ceaf74b747e5c69
ms.sourcegitcommit: 076ece88c3177db321f0ae32cba1d05179ffc393
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97794174"
---
# <a name="post-deployment-configurations-for-asdk"></a>Konfigurationen nach der Bereitstellung für das ASDK

Nach dem [Installieren des Azure Stack Development Kit (ASDK)](asdk-install.md) sollten Sie einige nach der Bereitstellung empfohlene Konfigurationsänderungen vornehmen, während Sie auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ angemeldet sind.

## <a name="install-azure-stack-powershell"></a>Installieren von Azure Stack-PowerShell

Für die Verwendung von Azure Stack sind mit Azure Stack kompatible Azure PowerShell-Module erforderlich.

PowerShell-Befehle für Azure Stack werden über den PowerShell-Katalog installiert. Öffnen Sie zum Registrieren des PSGallery-Repositorys eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Verwenden Sie API-Versionsprofile, um mit Azure Stack kompatible Az-Module anzugeben.  API-Versionsprofile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack zu verwalten. Ein API-Versionsprofil ist eine Gruppe von Az-PowerShell-Modulen mit speziellen API-Versionen. Das über den PowerShell-Katalog verfügbare Modul **Az.BootStrapper** stellt PowerShell-Cmdlets bereit, die für die Arbeit mit API-Versionsprofilen erforderlich sind.

Sie können das aktuelle Azure Stack PowerShell-Modul mit oder ohne Internetverbindung mit dem ASDK-Hostcomputer installieren.

1.  Überprüfen Sie die Voraussetzungen auf Ihrem Windows-Computer. Anweisungen hierzu finden Sie unter [Voraussetzungen für Windows](../operator/powershell-install-az-module.md#prerequisites-for-windows).
2. Vor der Installation der erforderlichen PowerShell-Version [deinstallieren Sie unbedingt alle vorhandenen Azure PowerShell-Module](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules). 

- **Mit Internetverbindung** auf dem ASDK-Hostcomputer: Führen Sie zum Installieren dieser Module in Ihrer ASDK-Installation das folgende PowerShell-Skript aus:

### <a name="az-modules"></a>[Az-Module](#tab/az1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease

    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

War die Installation erfolgreich, werden die Module Az und AzureStack in der Ausgabe angezeigt.

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.8.2
    
    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

War die Installation erfolgreich, werden die AzureRM- und AzureStack-Module in der Ausgabe angezeigt.

---

- **Ohne Internetverbindung** auf dem ASDK-Hostcomputer: In einem Szenario ohne Internetverbindung müssen Sie zuerst mithilfe der folgenden PowerShell-Befehle die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen:

### <a name="az-modules"></a>[Az-Module](#tab/az2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Kopieren Sie danach die heruntergeladenen Pakete auf den ASDK-Computer, registrieren Sie den Speicherort als Standardrepository, und installieren Sie die Module Az und AzureStack aus diesem Repository:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module Az -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
  ```

  Kopieren Sie danach die heruntergeladenen Pakete auf den ASDK-Computer, registrieren Sie den Speicherort als Standardrepository, und installieren Sie die Module AzureRM und AzureStack aus diesem Repository:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module AzureRM -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

---

## <a name="download-the-azure-stack-tools"></a>Herunterladen der Azure Stack-Tools

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) ist ein GitHub-Repository, das PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack hostet. Diese Tools verwenden Sie mithilfe der Az PowerShell-Module oder der AzureRM-Module.

### <a name="az-modules"></a>[Az-Module](#tab/az3)

Um diese Tools zu erhalten, klonen Sie das GitHub-Repository aus dem Branch `az`, oder laden Sie den Ordner **AzureStack-Tools** herunter, indem Sie das folgende Skript ausführen:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm3)

Um diese Tools zu erhalten, klonen Sie das GitHub-Repository aus dem Branch `master`, oder laden Sie den Ordner **AzureStack-Tools** herunter, indem Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten das folgende Skript ausführen:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Weitere Informationen zur Verwendung des AzureRM-Moduls für Azure Stack Hub finden Sie unter [Installieren des PowerShell AzureRM-Moduls für Azure Stack Hub](../operator/azure-stack-powershell-install.md).

---

## <a name="validate-the-asdk-installation"></a>Überprüfen der ASDK-Installation

Um sicherzustellen, dass Ihre ASDK-Bereitstellung erfolgreich durchgeführt wurde, führen Sie mit dem Cmdlet Test-AzureStack folgende Schritte durch:

1. Melden Sie sich auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ an.
2. Öffnen Sie PowerShell als Administrator (nicht PowerShell ISE).
3. Führen Sie `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint` aus.
4. Führen Sie `Test-AzureStack` aus.

Die Tests dauern einige Minuten. Wenn die Installation erfolgreich war, sieht die Ausgabe in etwa wie folgt aus:

![Testen von Azure Stack – Installation erfolgreich](media/asdk-post-deploy/test-azurestack.png)

Wenn ein Fehler aufgetreten ist, führen Sie die Schritte zur Problembehandlung aus, um Hilfe zu erhalten.

## <a name="enable-multi-tenancy"></a>Aktivieren der Mehrinstanzenfähigkeit

Für Bereitstellungen mithilfe von Azure AD müssen Sie [Mehrinstanzenfähigkeit](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) für die ASDK-Installation aktivieren.

> [!NOTE]
> Wenn Administrator- oder Benutzerkonten aus anderen Domänen als die bei der Registrierung von Azure Stack verwendeten für die Anmeldung bei einem Azure Stack-Portal verwendet werden, muss der Domänenname, mit dem Azure Stack registriert wird, an die Portal-URL angefügt werden. Wenn also beispielsweise Azure Stack mit fabrikam.onmicrosoft.com registriert wurde und für die Anmeldung das Benutzerkonto admin@contoso.com verwendet wird, lautet die URL für die Anmeldung beim Benutzerportal wie folgt: https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Nächste Schritte

[Registrieren des ASDK bei Azure](asdk-register.md)
