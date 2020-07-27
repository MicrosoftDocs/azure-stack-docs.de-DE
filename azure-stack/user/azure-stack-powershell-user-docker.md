---
title: Verwenden von Docker zum Ausführen von PowerShell in Azure Stack Hub
description: Verwenden von Docker zum Ausführen von PowerShell in Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 7/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 7/20/2020
ms.openlocfilehash: f66eacdcd65feedaf52324dc84b32d84677ce80b
ms.sourcegitcommit: 16ff77f7157e5b04a8cd401b095f7b71f51d5a11
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86949524"
---
# <a name="use-docker-to-run-powershell-in-azure-stack-hub"></a>Verwenden von Docker zum Ausführen von PowerShell in Azure Stack Hub

In diesem Artikel nutzen Sie Docker zum Erstellen eines Containers, in dem die für die Nutzung der verschiedenen Schnittstellen benötigte Version von PowerShell ausgeführt wird. Hier finden Sie Anweisungen zur Verwendung von AzureRM-Modulen und den neuesten Az-Modulen. Für AzureRM ist ein Windows-basierter Container erforderlich. Az verwendet einen Linux-basierten Container.

## <a name="docker-prerequisites"></a>Docker-Voraussetzungen

### <a name="install-docker"></a>Installieren von Docker

1. Installieren Sie [Docker](https://docs.docker.com/install/).

1. Geben Sie in einem Befehlszeilenprogramm, z. B. PowerShell oder Bash, Folgendes ein:

    ```bash
    docker --version
    ```

### <a name="set-up-a-service-principal-for-using-powershell"></a>Einrichten eines Dienstprinzipals für die Verwendung von PowerShell

Sie benötigen einen Dienstprinzipal in Ihrem Azure AD-Mandanten (Azure Active Directory), um PowerShell für den Zugriff auf die Ressource in Azure Stack Hub zu verwenden. Sie delegieren Berechtigungen mit der rollenbasierten Zugriffskontrolle (RBAC) für Benutzer. Möglicherweise müssen Sie den Dienstprinzipal von Ihrem Cloudoperator anfordern.

1. Befolgen Sie zum Einrichten Ihres Dienstprinzipals die Anleitung unter [Gewähren des Anwendungszugriffs auf Azure Stack Hub-Ressourcen durch Erstellen von Dienstprinzipalen](azure-stack-create-service-principals.md).

2. Notieren Sie sich die Anwendungs-ID, das Geheimnis, Ihre Mandanten-ID und Ihre Objekt-ID zur späteren Verwendung.

1. Befolgen Sie zum Einrichten Ihres Dienstprinzipals die Anleitung unter [Gewähren des Anwendungszugriffs auf Azure Stack Hub-Ressourcen durch Erstellen von Dienstprinzipalen](../operator/azure-stack-create-service-principals.md?view=azs-2002).

## <a name="run-powershell-in-docker"></a>Ausführen von PowerShell in Docker

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/rm)

In diesen Anweisungen führen Sie ein Windows-basiertes Containerimage aus und installieren die PowerShell und die erforderlichen Module für Azure Stack Hub.

1. Sie müssen Docker mit Windows-Containern ausführen, für die Windows 10 erforderlich ist. Wechseln Sie zu Windows-Containern, wenn Sie Docker ausführen. Für die Images, die das Az-Modul unterstützen, ist Docker 17.05 oder höher erforderlich.

1. Führen Sie Docker von einem Computer aus, der sich in derselben Domäne wie Azure Stack Hub befindet. Wenn Sie das Azure Stack Development Kit (ASDK) verwenden, müssen Sie [das VPN auf Ihrem Remotecomputer](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn) installieren.

### <a name="install-azure-stack-hub-azurerm-module-on-a-windows-container"></a>Installieren des AzureRM-Moduls von Azure Stack Hub in einem Windows-Container

Die Dockerfile öffnet das Microsoft-Image *microsoft/windowsservercore*, in dem Windows PowerShell 5.1 installiert ist. Die Datei lädt dann NuGet und die Azure Stack Hub PowerShell-Module und lädt anschließend die Tools aus „Azure Stack Hub-Tools“ herunter.

1. Führen Sie den [Download des Repositorys „azure-stack-powershell“](https://github.com/Azure-Samples/azure-stack-hub-powershell-in-docker.git) als ZIP-Datei durch, oder klonen Sie das Repository.

2. Öffnen Sie den Repositoryordner von Ihrem Terminal aus.

3. Öffnen Sie in Ihrem Repository eine Befehlszeilenschnittstelle, und geben Sie dann den folgenden Befehl ein:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Starten Sie einen interaktiven Container, indem Sie Folgendes eingeben, nachdem das Image erstellt wurde:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Die Shell ist bereit für Ihre Cmdlets bereit.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Stellen Sie über den Dienstprinzipal eine Verbindung mit Ihrer Azure Stack Hub-Instanz her. Sie verwenden nun eine PowerShell-Eingabeaufforderung in Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell gibt Ihr Kontoobjekt zurück:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Testen Sie Ihre Konnektivität, indem Sie eine Ressourcengruppe in Azure Stack Hub erstellen.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

### <a name="az-modules"></a>[Az-Module](#tab/az)

In diesen Anweisungen führen Sie ein Linux-basiertes Containerimage aus, das die PowerShell und die erforderlichen Module für Azure Stack Hub enthält.

1. Docker muss in einem Linux-Container ausgeführt werden. Wechseln Sie zu Linux-Containern, wenn Sie Docker ausführen.

1. Führen Sie Docker von einem Computer aus, der sich in derselben Domäne wie Azure Stack Hub befindet. Wenn Sie das Azure Stack Development Kit (ASDK) verwenden, müssen Sie [das VPN auf Ihrem Remotecomputer](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn) installieren.


## <a name="install-azure-stack-hub-az-module-on-a-linux-container"></a>Installieren des Az-Moduls von Azure Stack Hub in einem Linux-Container

1. Führen Sie in der Befehlszeile den folgenden Docker-Befehl aus, um PowerShell in einem Ubuntu-Container auszuführen:

    ```bash
    docker run -it mcr.microsoft.com/azurestack/powershell
    ```

    Sie können Ubuntu, Debian oder CentOS ausführen. Die folgenden Docker-Dateien befinden sich im GitHub-Repository [azurestack-powershell](https://github.com/Azure/azurestack-powershell). Aktuelle Änderungen an den Docker-Dateien finden Sie im GitHub-Repository. Jedes Betriebssystem ist mit einem Tag versehen. Ersetzen Sie das Tag (den Abschnitt nach dem Doppelpunkt) durch das entsprechende Tag.

    | Linux | Docker-Image |
    | --- | --- |
    | Ubuntu | `docker run -it mcr.microsoft.com/azurestack/powershell:ubuntu-18.04` |
    | Debian | `docker run -it mcr.microsoft.com/azurestack/powershell:debian-9` |
    | Centos | `docker run -it mcr.microsoft.com/azurestack/powershell:centos-7` |

2. Die Shell ist bereit für Ihre Cmdlets bereit. Testen Sie Ihre Shellkonnektivität, indem Sie sich anmelden und dann `Test-AzureStack.ps1` ausführen.

    Erstellen Sie zuerst die Anmeldeinformationen für den Dienstprinzipal. Sie benötigen das **Geheimnis** und die **Anwendungs-ID**. Zudem ist die **Objekt-ID** erforderlich, wenn Sie `Test-AzureStack.ps1` zum Überprüfen des Containers ausführen. Möglicherweise müssen Sie einen Dienstprinzipal von Ihrem Cloudoperator anfordern.

    Geben Sie die folgenden Cmdlets ein, um ein Dienstprinzipalobjekt zu erstellen:

    ```powershell  
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    ```

5. Stellen Sie eine Verbindung mit Ihrer Umgebung her, indem Sie über Ihre Azure Stack Hub-Instanz das folgende Skript mit den folgenden Werten ausführen.

    | Wert | Beschreibung |
    | --- | --- |
    | Der Name der Umgebung. | Der Name Ihrer Azure Stack Hub-Umgebung. |
    | Resource Manager-Endpunkt | Die URL für den Resource Manager. Wenden Sie sich an Ihren Cloudoperator, wenn sie Ihnen nicht bekannt ist. Sie sieht ungefähr wie folgt aus: `https://management.region.domain.com`. | 
    | Verzeichnismandanten-ID | Die ID Ihres Azure Stack Hub-Mandantenverzeichnisses. | 
    | Anmeldeinformationen | Ein Objekt, das Ihren Dienstprinzipal enthält, in diesem Fall `$pscredential`.  |

    ```powershell
    ./Login-Environment.ps1 -Name <String> -ResourceManagerEndpoint <resource manager endpoint> -DirectoryTenantId <String> -Credential $pscredential
    ```

   PowerShell gibt Ihr Kontoobjekt zurück.

7. Testen Sie Ihre Umgebung, indem Sie das Skript `Test-AzureStack.ps1` im Container ausführen. Geben Sie die **Objekt-ID** des Dienstprinzipals an. Wenn Sie die Objekt-ID nicht angeben, wird das Skript dennoch ausgeführt. Es werden jedoch nur Mandantenmodule (Benutzermodule) getestet. Für Module, die Administratorrechte erfordern, tritt ein Fehler auf.

    ```powershell  
    ./Test-AzureStack.ps1 <Object ID>
    ```

---

## <a name="next-steps"></a>Nächste Schritte

- Hier finden Sie eine Übersicht über [Azure Stack Hub PowerShell in Azure Stack Hub](azure-stack-powershell-overview.md).
- Erfahren Sie mehr über [API-Profile für PowerShell](azure-stack-version-profiles.md) in Azure Stack Hub.
- Installieren Sie [Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md).
- Erfahren Sie mehr über das Erstellen von [Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md) für cloudübergreifende Konsistenz.
