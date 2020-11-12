---
title: Herstellen einer Verbindung mit Azure Stack Hub
description: Enthält Informationen dazu, wie Sie eine Verbindung mit Azure Stack Hub herstellen.
author: mattbriggs
ms.topic: conceptual
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 8661666b4f953d07a96f1a2402782970e32c140b
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545750"
---
# <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

Zum Verwalten von Ressourcen müssen Sie eine Verbindung mit dem Azure Stack Development Kit herstellen. Dieser Artikel enthält Informationen zu den Schritten, die zum Herstellen einer Verbindung mit dem Development Kit erforderlich sind. Sie können eine der folgenden Verbindungsoptionen verwenden:

* Remotedesktop: Ermöglicht einem einzelnen gleichzeitigen Benutzer die schnelle Verbindungsherstellung über das Development Kit.
* Virtuelles privates Netzwerk (VPN): Ermöglicht mehreren gleichzeitigen Benutzern die Verbindungsherstellung von Clients, die außerhalb der Azure Stack Hub-Infrastruktur angeordnet sind (Konfiguration erforderlich).

## <a name="connect-to-azure-stack-hub-with-remote-desktop"></a>Herstellen einer Verbindung mit Azure Stack Hub per Remotedesktop
Bei einer Remotedesktopverbindung kann ein einzelner gleichzeitiger Benutzer das Portal verwenden, um Ressourcen zu verwalten.

1. Öffnen Sie eine Remotedesktopverbindung, und stellen Sie eine Verbindung mit dem Development Kit her. Geben Sie **AzureStack\AzureStackAdmin** als Benutzernamen sowie das Administratorkennwort ein, das Sie bei der Einrichtung von Azure Stack Hub angegeben haben.  

2. Öffnen Sie auf dem Development Kit-Computer den Server-Manager, klicken Sie auf **Lokaler Server** , deaktivieren Sie die Verstärkte Sicherheitskonfiguration für Internet Explorer, und schließen Sie den Server-Manager.

3. Um das Portal zu öffnen, wechseln Sie zu `https://portal.local.azurestack.external/`, und melden Sie sich mit Ihren Benutzeranmeldeinformationen an.


## <a name="connect-to-azure-stack-hub-with-vpn"></a>Herstellen einer Verbindung mit Azure Stack Hub per VPN

Sie können eine VPN-Verbindung vom Typ „Geteilter Tunnel“ mit einem Azure Stack Development Kit herstellen. Über die VPN-Verbindung können Sie auf das Administratorportal, das Benutzerportal und lokal installierte Tools wie z. B. Visual Studio und PowerShell zugreifen, um Azure Stack Hub-Ressourcen zu verwalten. Die VPN-Konnektivität wird in Bereitstellungen sowohl auf Azure AD-Basis (Azure Active Directory) als auch auf AD FS-Basis (Active Directory-Verbunddienste) unterstützt. Mit VPN-Verbindungen können mehrere Clients gleichzeitig eine Verbindung mit Azure Stack Hub herstellen. 

> [!NOTE] 
> Diese VPN-Verbindung stellt keine Konnektivität mit VMs der Azure Stack Hub-Infrastruktur bereit. 

### <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie auf Ihrem lokalen Computer das [Azure Stack Hub-kompatible Azure PowerShell-Modul](../operator/powershell-install-az-module.md).  
* Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurieren von VPN-Konnektivität

Öffnen Sie zum Erstellen einer VPN-Verbindung mit dem Development Kit auf Ihrem lokalen Windows-basierten Computer eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie das folgende Skript aus (aktualisieren Sie die Werte für die IP-Adresse und das Kennwort für Ihre Umgebung):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack Hub host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack Hub>" `
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

Wenn die Einrichtung erfolgreich ist, wird in Ihrer Liste mit den VPN-Verbindungen der Eintrag `azurestack` angezeigt.

![Netzwerkverbindungen](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

Stellen Sie eine Verbindung mit der Azure Stack Hub-Instanz her, indem Sie eines der beiden folgenden Verfahren verwenden:  

* Führen Sie den Befehl `Connect-AzsVpn` aus: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Stufen Sie den Azure Stack Hub-Host bei entsprechender Aufforderung als vertrauenswürdig ein, und installieren Sie das Zertifikat über **AzureStackCertificateAuthority** im Zertifikatspeicher Ihres lokalen Computers. Die Aufforderung wird unter Umständen hinter dem Fenster mit der PowerShell-Sitzung angezeigt. 

* Navigieren Sie auf Ihrem lokalen Computer zu **Netzwerkeinstellungen** > **VPN** , und wählen Sie `azurestack` > **Verbinden**. Geben Sie an der Eingabeaufforderung für die Anmeldung den Benutzernamen (AzureStack\AzureStackAdmin) und das Kennwort ein.

### <a name="test-the-vpn-connectivity"></a>Testen der VPN-Konnektivität

Öffnen Sie zum Testen der Portalverbindung einen Browser, und wechseln Sie zum Benutzerportal `https://portal.local.azurestack.external/`, melden Sie sich an, und erstellen Sie anschließend Ressourcen.  

## <a name="next-steps"></a>Nächste Schritte



