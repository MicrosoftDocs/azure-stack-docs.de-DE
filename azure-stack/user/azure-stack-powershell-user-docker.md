---
title: Verwenden von Docker zum Ausführen von PowerShell in Azure Stack Hub | Microsoft-Dokumentation
description: Verwenden von Docker zum Ausführen von PowerShell in Azure Stack Hub
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: e55fd18babea30d0b004c1219d8ce4842f4750bd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819470"
---
# <a name="use-docker-to-run-powershell-in-azure-stack-hub"></a>Verwenden von Docker zum Ausführen von PowerShell in Azure Stack Hub

In diesem Artikel nutzen Sie Docker zum Erstellen von Windows-basierten Containern, in denen die Version von PowerShell ausgeführt wird, die für die Nutzung der verschiedenen Schnittstellen benötigt wird. In Docker müssen Sie Windows-basierte Container verwenden.

## <a name="docker-prerequisites"></a>Docker-Voraussetzungen

1. Installieren Sie [Docker](https://docs.docker.com/install/).

1. Geben Sie in einem Befehlszeilenprogramm, z. B. PowerShell oder Bash, Folgendes ein:

    ```bash
        Docker --version
    ```

1. Sie müssen Docker mit Windows-Containern ausführen, für die Windows 10 erforderlich ist. Wechseln Sie zu Windows-Containern, wenn Sie Docker ausführen.

1. Führen Sie Docker von einem Computer aus, der sich in derselben Domäne wie Azure Stack Hub befindet. Wenn Sie das Azure Stack Development Kit (ASDK) verwenden, müssen Sie [das VPN auf Ihrem Remotecomputer](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn) installieren.

## <a name="set-up-a-service-principal-for-using-powershell"></a>Einrichten eines Dienstprinzipals für die Verwendung von PowerShell

Sie benötigen einen Dienstprinzipal in Ihrem Azure AD-Mandanten (Azure Active Directory), um PowerShell für den Zugriff auf die Ressource in Azure Stack Hub zu verwenden. Sie delegieren Berechtigungen mit der rollenbasierten Zugriffskontrolle (RBAC) für Benutzer.

1. Befolgen Sie zum Einrichten Ihres Dienstprinzipals die Anleitung unter [Gewähren des Anwendungszugriffs auf Azure Stack Hub-Ressourcen durch Erstellen von Dienstprinzipalen](azure-stack-create-service-principals.md).

2. Notieren Sie sich die Anwendungs-ID, das Geheimnis und Ihre Mandanten-ID zur späteren Verwendung.

## <a name="docker---azure-stack-hub-api-profiles-module"></a>Docker – Modul „Profile“ der Azure Stack Hub-API

Die Dockerfile öffnet das Microsoft-Image *microsoft/windowsservercore*, in dem Windows PowerShell 5.1 installiert ist. Die Datei lädt dann NuGet und die Azure Stack Hub PowerShell-Module und lädt anschließend die Tools aus „Azure Stack Hub-Tools“ herunter.

1. Führen Sie den [Download des Repositorys „azure-stack-powershell“](https://github.com/mattbriggs/azure-stack-powershell) als ZIP-Datei durch, oder klonen Sie das Repository.

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

## <a name="next-steps"></a>Nächste Schritte

-  Hier finden Sie eine Übersicht über [Azure Stack Hub PowerShell in Azure Stack Hub](azure-stack-powershell-overview.md).
- Erfahren Sie mehr über [API-Profile für PowerShell](azure-stack-version-profiles.md) in Azure Stack Hub.
- Installieren Sie [Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md).
- Erfahren Sie mehr über das Erstellen von [Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md) für cloudübergreifende Konsistenz.
