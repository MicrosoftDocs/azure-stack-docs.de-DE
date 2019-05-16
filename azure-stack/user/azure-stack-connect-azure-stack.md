---
title: Herstellen einer Verbindung mit Azure Stack | Microsoft-Dokumentation
description: Enthält Informationen dazu, wie Sie eine Verbindung mit Azure Stack herstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cbffe391c6295ede6c985f38a836198a1ffba84c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986202"
---
# <a name="connect-to-azure-stack"></a>Herstellen einer Verbindung mit Azure Stack

Zum Verwalten von Ressourcen müssen Sie eine Verbindung mit dem Azure Stack Development Kit herstellen. Dieser Artikel enthält Informationen zu den Schritten, die zum Herstellen einer Verbindung mit dem Development Kit erforderlich sind. Sie können eine der folgenden Verbindungsoptionen verwenden:

* Remotedesktop: Ermöglicht einem einzelnen gleichzeitigen Benutzer die schnelle Verbindungsherstellung über das Development Kit.
* Virtuelles privates Netzwerk (VPN): Ermöglicht mehreren gleichzeitigen Benutzern die Verbindungsherstellung von Clients, die außerhalb der Azure Stack-Infrastruktur angeordnet sind (Konfiguration erforderlich).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Herstellen einer Verbindung mit Azure Stack per Remotedesktop
Bei einer Remotedesktopverbindung kann ein einzelner gleichzeitiger Benutzer das Portal verwenden, um Ressourcen zu verwalten.

1. Öffnen Sie eine Remotedesktopverbindung, und stellen Sie eine Verbindung mit dem Development Kit her. Geben Sie **AzureStack\AzureStackAdmin** als Benutzernamen sowie das Administratorkennwort ein, das Sie bei der Einrichtung von Azure Stack angegeben haben.  

2. Öffnen Sie auf dem Development Kit-Computer den Server-Manager, klicken Sie auf **Lokaler Server**, deaktivieren Sie die Verstärkte Sicherheitskonfiguration für Internet Explorer, und schließen Sie den Server-Manager.

3. Navigieren Sie zu (https://portal.local.azurestack.external/), und melden Sie sich mit Ihren Benutzeranmeldeinformationen an, um das Portal zu öffnen.


## <a name="connect-to-azure-stack-with-vpn"></a>Herstellen einer Verbindung mit Azure Stack per VPN

Sie können eine VPN-Verbindung vom Typ „Geteilter Tunnel“ mit einem Azure Stack Development Kit herstellen. Über die VPN-Verbindung können Sie auf das Administratorportal, das Benutzerportal und lokal installierte Tools wie z.B. Visual Studio und PowerShell zugreifen, um Azure Stack-Ressourcen zu verwalten. Die VPN-Konnektivität wird sowohl für Bereitstellungen auf AAD-Basis (Azure Active Directory) als auch auf AD FS-Basis (Active Directory-Verbunddienste) unterstützt. Mit VPN-Verbindungen können mehrere Clients gleichzeitig eine Verbindung mit Azure Stack herstellen. 

> [!NOTE] 
> Diese VPN-Verbindung stellt keine Konnektivität mit VMs der Azure Stack-Infrastruktur bereit. 

### <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie auf Ihrem lokalen Computer das [Azure Stack-kompatible Azure PowerShell-Modul](../operator/azure-stack-powershell-install.md).  
* Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack benötigt werden](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurieren von VPN-Konnektivität

Öffnen Sie zum Erstellen einer VPN-Verbindung mit dem Development Kit auf Ihrem lokalen Windows-basierten Computer eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie das folgende Skript aus (aktualisieren Sie die Werte für die IP-Adresse und das Kennwort für Ihre Umgebung):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Wenn die Einrichtung erfolgreich ist, sollte in Ihrer Liste mit den VPN-Verbindungen der Eintrag **azurestack** enthalten sein.

![Netzwerkverbindungen](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Herstellen einer Verbindung mit Azure Stack

Stellen Sie eine Verbindung mit der Azure Stack-Instanz her, indem Sie eines der beiden folgenden Verfahren verwenden:  

* Führen Sie den Befehl `Connect-AzsVpn` aus: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Stufen Sie den Azure Stack-Host bei entsprechender Aufforderung als vertrauenswürdig ein, und installieren Sie das Zertifikat über **AzureStackCertificateAuthority** im Zertifikatspeicher Ihres lokalen Computers. (Die Aufforderung wird unter Umständen hinter dem Fenster mit der PowerShell-Sitzung angezeigt.) 

* Öffnen Sie für Ihren lokalen Computer **Netzwerkeinstellungen** > **VPN**, und klicken Sie auf **azurestack** > **connect**. Geben Sie an der Eingabeaufforderung für die Anmeldung den Benutzernamen (AzureStack\AzureStackAdmin) und das Kennwort ein.

### <a name="test-the-vpn-connectivity"></a>Testen der VPN-Konnektivität

Öffnen Sie zum Testen der Portalverbindung einen Internetbrowser, und navigieren Sie zum Benutzerportal (https://portal.local.azurestack.external/). Melden Sie sich an, und erstellen Sie Ressourcen.  

## <a name="next-steps"></a>Nächste Schritte



