---
title: Konfigurationen nach der Bereitstellung für das ASDK
description: Erfahren Sie etwas über die empfohlenen Konfigurationsänderungen, die nach der Installation des Azure Stack Development Kits (ASDK) vorgenommen werden müssen.
author: justinha
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 8c0cd14dbeda4e2b7844aefaf10f4a8cf685d60d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700594"
---
# <a name="post-deployment-configurations-for-asdk"></a>Konfigurationen nach der Bereitstellung für das ASDK

Nach dem [Installieren des Azure Stack Development Kit (ASDK)](asdk-install.md) sollten Sie einige nach der Bereitstellung empfohlene Konfigurationsänderungen vornehmen, während Sie auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ angemeldet sind.

## <a name="install-azure-stack-powershell"></a>Installieren von Azure Stack-PowerShell

Für die Verwendung von Azure Stack sind mit Azure Stack kompatible Azure PowerShell-Module erforderlich.

PowerShell-Befehle für Azure Stack werden über den PowerShell-Katalog installiert. Öffnen Sie zum Registrieren des PSGallery-Repositorys eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Verwenden Sie API-Versionsprofile, um mit Azure Stack kompatible AzureRM-Module anzugeben.  API-Versionsprofile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack zu verwalten. Ein API-Versionsprofil ist eine Gruppe von AzureRM-PowerShell-Modulen mit spezifischen API-Versionen. Das über den PowerShell-Katalog verfügbare Modul **AzureRM.BootStrapper** stellt PowerShell-Cmdlets bereit, die für die Arbeit mit API-Versionsprofilen erforderlich sind.

Sie können das aktuelle Azure Stack PowerShell-Modul mit oder ohne Internetverbindung mit dem ASDK-Hostcomputer installieren:

> [!IMPORTANT]
> Vor der Installation der erforderlichen Version [deinstallieren Sie unbedingt alle vorhandenen Azure PowerShell-Module](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

- **Mit Internetverbindung** auf dem ASDK-Hostcomputer: Führen Sie zum Installieren dieser Module in Ihrer ASDK-Installation das folgende PowerShell-Skript aus:


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.8.0
  ```

  War die Installation erfolgreich, werden die AzureRM- und AzureStack-Module in der Ausgabe angezeigt.

- **Ohne Internetverbindung** auf dem ASDK-Hostcomputer: In einem Szenario ohne Internetverbindung müssen Sie zuerst mithilfe der folgenden PowerShell-Befehle die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Kopieren Sie danach die heruntergeladenen Pakete auf den ASDK-Computer, registrieren Sie den Speicherort als Standardrepository, und installieren Sie die Module AzureRM und AzureStack aus diesem Repository:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Herunterladen der Azure Stack-Tools

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) ist ein GitHub-Repository, das PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack hostet. Um diese Tools zu erhalten, klonen Sie das GitHub-Repository, oder laden Sie den Ordner „AzureStack-Tools“ herunter. Führen Sie dazu das folgende Skript aus:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

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
