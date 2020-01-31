---
title: Herstellen einer Verbindung mit dem ASDK
description: In diesem Artikel wird das Herstellen einer Verbindung mit dem ASDK (Azure Stack Development Kit) erläutert.
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: df4b6c770ef1dd93cddbeb748c0845f391305173
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874024"
---
# <a name="connect-to-the-asdk"></a>Herstellen einer Verbindung mit dem ASDK

Zum Verwalten von Ressourcen müssen Sie zunächst eine Verbindung mit dem Azure Stack Development Kit (ASDK) herstellen. In diesem Artikel werden die Schritte zum Herstellen einer Verbindung mit dem ASDK mithilfe der folgenden Verbindungsoptionen beschrieben:

* [Remotedesktopverbindung (RDP)](#connect-with-rdp): Über eine Remotedesktopverbindung kann ein einzelner Benutzer schnell eine Verbindung mit dem ASDK herstellen.
* [Virtuelles privates Netzwerk (VPN)](#connect-with-vpn): Bei Verwendung einer VPN-Verbindung können mehrere Benutzer parallel über Clients außerhalb der Azure Stack-Infrastruktur eine Verbindung mit den Azure Stack-Portalen herstellen. Eine VPN-Verbindung muss erst eingerichtet werden.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Herstellen einer Verbindung mit Azure Stack mithilfe von RDP

Über eine Remotedesktopverbindung kann ein einzelner Benutzer Ressourcen im Azure Stack-Administratorportal oder im Benutzerportal direkt vom ASDK-Hostcomputer aus verwalten.

> [!TIP]
> Diese Option ermöglicht Ihnen auch, RDP erneut zu verwenden, während Sie auf dem ASDK-Hostcomputer angemeldet sind, um sich bei auf dem ASDK-Hostcomputer erstellten virtuellen Computern (VMs) anzumelden.

1. Öffnen Sie Remotedesktopverbindung („mstc. exe“), und stellen Sie eine Verbindung mit der IP-Adresse des ASDK-Hostcomputers her. Sie müssen ein Konto verwenden, das für die Remoteanmeldung beim ASDK-Hostcomputer autorisiert ist. In der Standardeinstellung umfasst **AzureStack\AzureStackAdmin** Berechtigungen für eine Remoteverbindung mit dem ASDK-Hostcomputer.  

2. Öffnen Sie auf dem ASDK-Computer den Server-Manager („ServerManager.exe“). Wählen Sie **Lokaler Server** aus, deaktivieren Sie **Verstärkte Sicherheitskonfiguration für IE**, und schließen Sie den Server-Manager.

3. Melden Sie sich beim Administratorportal als **AzureStack\CloudAdmin** an, oder verwenden Sie andere Azure Stack-Operatoranmeldeinformationen. Die Adresse des ASDK-Administratorportals lautet [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Melden Sie sich beim Benutzerportal als **AzureStack\CloudAdmin** an, oder verwenden Sie andere Azure Stack-Benutzeranmeldeinformationen. Die Adresse des ASDK-Benutzerportals lautet [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Weitere Informationen zur Verwendung der einzelnen Konten finden Sie unter [Grundlagen der ASDK-Verwaltung](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Herstellen einer Verbindung mit Azure Stack mithilfe eines VPN

Sie können eine VPN-Verbindung vom Typ „Geteilter Tunnel“ mit dem ASDK-Hostcomputer herstellen, um auf die Azure Stack-Portale und lokal installierte Tools wie Visual Studio und PowerShell zuzugreifen. Mit VPN-Verbindungen können mehrere Benutzer gleichzeitig auf Azure Stack-Ressourcen zugreifen, die beim ASDK gehostet werden.

VPN-Verbindungen werden in Azure AD- und AD FS-Bereitstellungen (Active Directory Federation Services, Active Directory-Verbunddienste) unterstützt.

> [!NOTE]
> Eine VPN-Verbindung bietet *keine* Konnektivität mit virtuellen Azure Stack-Computern. Während Sie über VPN angemeldet sind, können Sie keine RDP-Verbindungen mit Azure Stack-VMs herstellen.

### <a name="prerequisites"></a>Voraussetzungen
Bevor Sie eine VPN-Verbindung mit dem ASDK herstellen, stellen Sie sicher, dass Sie folgende Voraussetzungen erfüllen:

- Installieren Sie auf Ihrem lokalen Computer das [Azure Stack-kompatible Azure PowerShell-Modul](asdk-post-deploy.md#install-azure-stack-powershell).  
- Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack benötigt werden](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Einrichten der VPN-Konnektivität

Wenn Sie eine VPN-Verbindung mit dem ASDK herstellen möchten, öffnen Sie PowerShell als Administrator auf Ihrem lokalen Windows-Computer. Führen Sie dann das folgende Skript mit der IP-Adresse und dem Kennwort für Ihre Umgebung aus:

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the ASDK host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Bei erfolgreicher Einrichtung wird **Azure Stack** in der Liste mit den VPN-Verbindungen angezeigt:

![Netzwerkverbindungen](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Herstellen einer Verbindung mit Azure Stack

  Stellen Sie mithilfe einer der folgenden Methoden eine Verbindung mit der Azure Stack-Instanz her:  

  * Verwenden Sie den Befehl `Connect-AzsVpn`:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * Klicken Sie auf Ihrem lokalen Computer auf **Netzwerkeinstellungen** > **VPN** > **Azure Stack** > **Verbinden**. Geben Sie an der Anmeldeeingabeaufforderung den Benutzernamen (**AzureStack\AzureStackAdmin**) und Ihr Kennwort ein.

Bei Ihrer ersten Anmeldung werden Sie aufgefordert, das Azure Stack-Stammzertifikat von **AzureStackCertificateAuthority** im Zertifikatspeicher Ihres lokalen Computers zu installieren. Durch diesen Schritt wird die ASDK-Zertifizierungsstelle der Liste der vertrauenswürdigen Hosts hinzugefügt. Klicken Sie auf **Ja**, um das Zertifikat zu installieren.

![Stammzertifikat](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Die Aufforderung wird unter Umständen durch das PowerShell-Fenster oder andere Anwendungen verdeckt.

### <a name="test-vpn-connectivity"></a>Testen der VPN-Konnektivität

Öffnen Sie zum Testen der Portalverbindung einen Browser, und navigieren Sie zum Benutzerportal (https://portal.local.azurestack.external/) oder zum Administratorportal (https://adminportal.local.azurestack.external/).

Melden Sie sich mit geeigneten Abonnementanmeldeinformationen an, um Ressourcen zu erstellen und zu verwalten.  

## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung](asdk-troubleshooting.md)
