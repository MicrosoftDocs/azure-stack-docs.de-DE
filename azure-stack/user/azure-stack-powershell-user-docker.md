---
title: Verwenden von Docker zum Ausführen von PowerShell für Azure Stack | Microsoft-Dokumentation
description: Verwenden von Docker zum Ausführen von PowerShell in Azure Stack
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985543"
---
# <a name="use-docker-to-run-powershell"></a>Ausführen von PowerShell mithilfe von Docker

Sie können Docker verwenden, um Windows-basierte Container zu erstellen, in denen die für die verschiedenen Schnittstellen erforderlichen speziellen PowerShell-Versionen ausgeführt werden können. In Docker müssen Sie Windows-basierte Container verwenden.

## <a name="docker-prerequisites"></a>Docker-Voraussetzungen

1. Installieren Sie [Docker](https://docs.docker.com/install/).
2. Öffnen Sie eine Befehlszeile, wie PowerShell oder Bash, und geben Sie Folgendes ein:

    ```bash
        Docker -version
    ```

3. Sie müssen Docker mit Windows-Containern ausführen, die Windows 10 erfordern. Wenn Sie Docker ausführen, wechseln Sie zu Windows-Containern.

4. Sie müssen Docker von einem Computer ausführen, der sich in der gleichen Domäne wie Azure Stack befindet. Wenn Sie das ASDK verwenden, müssen Sie [das VPN auf Ihrem Remotecomputer](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) installieren.

## <a name="service-principals-for-using-powershell"></a>Dienstprinzipale für die Verwendung von PowerShell

Sie benötigen einen Dienstprinzipal in Ihrem Azure AD-Mandanten, um PowerShell für den Zugriff auf die Ressource in Azure Stack zu verwenden. Sie delegieren Berechtigungen mit einer rollenbasierten Zugriffskontrolle für Benutzer.

1. Richten Sie Ihren Prinzipal ein, indem Sie den Anweisungen im Artikel [Anwendungen durch Erstellen von Dienstprinzipalen Zugriff auf Azure Stack-Ressourcen gewähren](azure-stack-create-service-principals.md) folgen.
2. Notieren Sie sich die Anwendungs-ID, den geheimen Schlüssel und Ihre Mandanten-ID.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker – Modul „Profile“ der Azure Stack-API

Dockerfile öffnet das Microsoft-Image „microsoft/windowsservercore“, in dem Windows PowerShell 5.1 installiert ist. Die Datei lädt dann NuGet und die Azure Stack PowerShell-Module und lädt anschließend die Tools aus „Azure Stack-Tools“ herunter.

1. Laden Sie dieses Repository als ZIP-Datei herunter, oder klonen Sie das Repository:  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. Öffnen Sie den Repositoryordner von Ihrem Terminal aus.

3. Öffnen Sie eine Befehlszeilenschnittstelle in Ihrem Repository, und geben Sie Folgendes ein:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Wenn das Image erstellt wurde, können Sie einen interaktiven Container starten. Geben Sie Folgendes ein: 

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Die Shell ist bereit für Ihre Cmdlets bereit.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Stellen Sie über den Dienstprinzipal eine Verbindung mit Ihrer Azure Stack-Instanz her. Sie verwenden nun eine PowerShell-Eingabeaufforderung in Docker. 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell gibt Ihr Kontoobjekt zurück:

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Testen Sie Ihre Konnektivität, indem Sie eine Ressourcengruppe in Azure Stack erstellen.

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Nächste Schritte

-  Hier finden Sie eine Übersicht über [Azure Stack PowerShell in Azure Stack](azure-stack-powershell-overview.md).
- Erfahren Sie mehr über [API-Profile für PowerShell](azure-stack-version-profiles.md) in Azure Stack.
- [Installieren von Azure Stack-PowerShell](../operator/azure-stack-powershell-install.md).
- Erfahren Sie mehr über das Erstellen von [Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md) für cloudübergreifende Konsistenz.