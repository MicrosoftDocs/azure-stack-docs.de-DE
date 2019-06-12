---
title: Konfigurationen nach der Bereitstellung für das Azure Stack Development Kit (ASDK) | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die empfohlenen Konfigurationsänderungen, die nach der Installation des Azure Stack Development Kits (ASDK) vorgenommen werden müssen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: aac9bb8edce4b15d3d058cdb3b6cc6e23aa58493
ms.sourcegitcommit: 23816ec68f67f3ac51f78de925b7631590743a29
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66835006"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Konfigurationsaufgaben nach der Installation des ASDK

Nach dem [Installieren des Azure Stack Development Kit (ASDK)](asdk-install.md) sollten Sie einige nach der Installation empfohlene Konfigurationsänderungen vornehmen, während Sie auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ angemeldet sind.

## <a name="install-azure-stack-powershell"></a>Installieren von Azure Stack-PowerShell

Für die Verwendung von Azure Stack sind mit Azure Stack kompatible Azure PowerShell-Module erforderlich.

PowerShell-Befehle für Azure Stack werden über den PowerShell-Katalog installiert. Öffnen Sie zum Registrieren des PSGallery-Repositorys eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Sie können API-Versionsprofile verwenden, um mit Azure Stack kompatible AzureRM-Module anzugeben.  API-Versionsprofile bieten eine Möglichkeit, Versionsunterschiede zwischen Azure und Azure Stack zu verwalten. Ein API-Versionsprofil ist eine Gruppe von AzureRM-PowerShell-Modulen mit spezifischen API-Versionen. Das über den PowerShell-Katalog verfügbare Modul **AzureRM.BootStrapper** stellt PowerShell-Cmdlets bereit, die für die Arbeit mit API-Versionsprofilen erforderlich sind.

Sie können das aktuelle Azure Stack PowerShell-Modul mit oder ohne Internetverbindung mit dem ASDK-Hostcomputer installieren:

> [!IMPORTANT]
> Vor der Installation der erforderlichen Version [deinstallieren Sie unbedingt alle vorhandenen Azure PowerShell-Module](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Mit Internetverbindung** auf dem ASDK-Hostcomputer. Führen Sie zum Installieren dieser Module in Ihrer Development Kit-Installation das folgende PowerShell-Skript aus:

  - Für Builds ab 1904:

    ```powershell  
      Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
      Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

      # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
      Install-Module -Name AzureRM.BootStrapper

      # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
      Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
      Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```

  - Für Azure Stack-Versionen bis 1903 installieren Sie nur die beiden folgenden Module:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Die Azure Stack-Modulversion 1.7.1 ist ein Breaking Change. Informationen zum Migrieren von Azure Stack 1.6.0 finden Sie im [Migrationsleitfaden](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  War die Installation erfolgreich, werden die AzureRM- und AzureStack-Module in der Ausgabe angezeigt.

- **Ohne Internetverbindung** auf dem ASDK-Hostcomputer. In einem Szenario ohne Internetverbindung müssen Sie zuerst mithilfe der folgenden PowerShell-Befehle die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen:

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

Um sicherzustellen, dass Ihre ASDK-Bereitstellung erfolgreich durchgeführt wurde, können Sie folgende Schritte durchführen und das Cmdlet „Test-AzureStack“ verwenden:

1. Melden Sie sich auf dem ASDK-Hostcomputer als „AzureStack\AzureStackAdmin“ an.
2. Starten Sie PowerShell als Administrator (nicht PowerShell ISE).
3. Führen Sie diesen Befehl aus: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Führen Sie diesen Befehl aus: `Test-AzureStack`

Die Tests dauern einige Minuten. Wenn die Installation erfolgreich war, sieht die Ausgabe in etwa wie folgt aus:

![Test-AzureStack](media/asdk-post-deploy/test-azurestack.png)

Wenn ein Fehler aufgetreten ist, führen Sie die Schritte zur Problembehandlung aus, um Hilfe zu erhalten.

## <a name="enable-multi-tenancy"></a>Aktivieren der Mehrinstanzenfähigkeit

Für Bereitstellungen mithilfe von Azure AD müssen Sie [Mehrinstanzenfähigkeit](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) für die ASDK-Installation aktivieren.

> [!NOTE]
> Wenn Administrator- oder Benutzerkonten aus anderen Domänen als derjenigen, die zur Registrierung von Azure Stack verwendet haben, für die Anmeldung an einem Azure Stack-Portal verwendet werden, muss der Domänenname, mit dem Azure Stack registriert wird, an die Portal-URL angefügt werden. Wenn beispielsweise Azure Stack mit „fabrikam.onmicrosoft.com“ registriert wurde und das Benutzerkonto für die Anmeldung admin@contoso.com ist, lautet die URL für die Anmeldung am Benutzerportal wie folgt: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Nächste Schritte

[Registrieren des ASDK bei Azure](asdk-register.md)
