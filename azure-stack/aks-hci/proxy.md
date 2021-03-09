---
title: Konfigurieren von Proxyeinstellungen für AKS in Azure Stack HCI
description: Erfahren Sie, wie Sie Proxyunterstützung planen und konfigurieren, um eine Verbindung mit dem Internet herzustellen.
author: abha
ms.topic: how-to
ms.date: 01/27/2021
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 83a3a4928f5df1b6b4520cdc6ffd277e497425ff
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874484"
---
# <a name="configure-proxy-settings-on-aks-on-azure-stack-hci"></a>Konfigurieren von Proxyeinstellungen für AKS in Azure Stack HCI

Wenn Ihr Netzwerk die Verwendung eines Proxyservers erfordert, um eine Verbindung mit dem Internet herzustellen, führt Sie dieses Dokument durch die Schritte, die zum Einrichten der Proxyunterstützung für AKS in Azure Stack HCI mithilfe der PowerShell-Module AksHci und WinInetProxy erforderlich sind. 

Wenn Sie WinInetProxy nicht verwenden möchten, können Sie auch die **Interneteigenschaften** („inetcpl.cpl“) verwenden und auf die Registerkarte **Verbindungen** und **LAN-Einstellungen** klicken, um Ihre Proxyservereinstellungen zu konfigurieren und zu überprüfen.


## <a name="before-you-begin"></a>Voraussetzungen

Installieren Sie das PowerShell-Modul [WinInetProxy](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0), um einen Proxyserver zu konfigurieren, indem Sie den folgenden Befehl in einem PowerShell-Verwaltungsfenster ausführen:

```Powershell
Install-Module -Name WinInetProxy -Repository PSGallery
```

Weitere Voraussetzungen finden Sie unter [Systemanforderungen](./system-requirements.md). Informationen zum Herunterladen des PowerShell-Moduls AksHci finden Sie unter [Einrichten des PowerShell-Moduls AksHci](./setup-powershell.md).

## <a name="configure-a-proxy-server-without-authentication"></a>Konfigurieren eines Proxyservers ohne Authentifizierung

Um einen Proxyserver zu konfigurieren, der keine Authentifizierung erfordert, führen Sie den folgenden Befehl auf jedem Azure Stack HCI-Cluster Knoten aus:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
```

## <a name="configure-a-proxy-server-with-authentication"></a>Konfigurieren eines Proxyservers mit Authentifizierung

Um einen Proxyserver zu konfigurieren, für den Authentifizierung erforderlich ist, müssen Sie Anmeldeinformationen für den AKS in Azure Stack-Host für die Verwendung mit dem WinINet-Proxy konfigurieren. Dies hängt davon ab, welche Art von Authentifizierung der Proxy benötigt, und kann entweder NTLM/Kerberos- oder Standardauthentifizierung sein.

> [!NOTE]
> Wenn Sie ein Zertifikat verwenden möchten, um eine Verbindung mit dem Proxyserver herzustellen, müssen Sie dieses Zertifikat für den entsprechenden Zertifikatspeicher auf Ihren Hosts bereitstellen, um sicherzustellen, dass es vertrauenswürdig ist.

### <a name="configure-a-proxy-server-with-ntlmkerberos-authentication"></a>Konfigurieren eines Proxyservers mit NTLM/Kerberos-Authentifizierung

Fügen Sie mithilfe des folgenden Befehls neue Windows-Anmeldeinformationen hinzu. Geben Sie Ihr sicheres Kennwort ein, wenn Sie dazu aufgefordert werden. Alternativ dazu können Sie auch Windows Credential Manager unter **Windows-Anmeldeinformationen** verwenden, um einen neuen Eintrag für Windows-Anmeldeinformationen hinzuzufügen. 

```powershell
cmdkey /generic:proxy.contoso.com /user:username /pass
```
Wenn Sie diesen Befehl ausführen, werden Sie aufgefordert, das Kennwort einzugeben.

Fügen Sie im PowerShell-Profil den folgenden Befehl hinzu, damit AKS-HCI-Agents die zwischengespeicherten Anmeldeinformationen verwenden können:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $PROFILE
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials
```  

### <a name="configure-a-proxy-server-with-basic-authentication"></a>Konfigurieren eines Proxyservers mit Standardauthentifizierung

Sie müssen dem PowerShell-Profil Anmeldeinformationen für Standardauthentifizierung hinzufügen, damit das Download-Agent-Modul diese verwenden kann. 

> [!NOTE]
> Die Anmeldeinformationen im PowerShell-Profil werden nicht verschlüsselt und als Klartext angezeigt. Stellen Sie sicher, dass das PowerShell-Profil durch Zugriffssteuerungslisten (Access Control Lists, ACLs) geschützt ist, sodass nur Administratoren und das LocalSystem-Konto diese anzeigen können.

Bearbeiten Sie das PowerShell-Profil, um den Benutzernamen und das Kennwort wie unten gezeigt durch die Anmeldeinformationen für Standardauthentifizierung zu ersetzen:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $profile
[System.Net.WebRequest]::DefaultWebProxy.Credentials = new-object System.Net.NetworkCredential("username", "password")
```

## <a name="deploy-aks-on-azure-stack-hci-host-using-a-proxy-server"></a>Bereitstellen von AKS auf einem Azure Stack HCI-Host mithilfe eines Proxyservers

Nachdem der Proxyserver konfiguriert wurde, können Sie mithilfe des Befehls `Set-AksHciConfig` Proxykonfigurationen für eine AKS-Hostinstallation festlegen. Je nachdem, ob der Proxyserver Authentifizierung erfordert, sind unterschiedliche Schritte erforderlich.

Nachdem Sie die Bereitstellung mithilfe der unten aufgeführten Optionen konfiguriert haben, können Sie einen [AKS-Host in Azure Stack HCI installieren](./setup-powershell.md) und [AKS-Cluster mithilfe von PowerShell erstellen](./create-kubernetes-cluster-powershell.md).

### <a name="configure-an-aks-host-for-a-proxy-server-with-basic-authentication"></a>Konfigurieren eines AKS-Hosts für einen Proxyserver mit Standardauthentifizierung  

Wenn Ihr Proxyserver Authentifizierung erfordert, führen Sie die folgenden Befehle aus, um die Anmeldeinformationen abzurufen, und legen Sie dann die Konfigurationsdetails fest.

```powershell
$proxyCred = Get-Credential
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCredential $ProxyCred
```

## <a name="configure-an-aks-host-for-a-proxy-server-with-ntlmkerberos-authentication"></a>Konfigurieren eines AKS-Hosts für einen Proxyserver mit NTLM/Kerberos-Authentifizierung

```powershell
$credential = Get-Credential # get the credential for the proxy server
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerCredential $credential
```

### <a name="configure-an-aks-host-for-a-proxy-server-without-authentication"></a>Konfigurieren eines AKS-Hosts für einen Proxyserver ohne Authentifizierung  

Wenn Ihr Proxyserver keine Authentifizierung erfordert, öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus:

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888"
```

### <a name="configure-an-aks-host-for-a-proxy-server-with-a-trusted-certificate"></a>Konfigurieren eines AKS-Hosts für einen Proxyserver mit einem vertrauenswürdigen Zertifikat

Wenn Ihr Proxyserver erfordert, dass Proxyclients einem Zertifikat vertrauen, geben Sie die Zertifikatdatei an, wenn Sie `Set-AksHciConfig` ausführen. Das Format der Zertifikatdatei ist *Base-64-codiert X.509*. Dies ermöglicht es uns, das Zertifikat im gesamten Stapel bereitzustellen und ihm zu vertrauen:

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCertFile "C:\proxycertificate.crt"
```

> [!NOTE]
> Proxyzertifikate werden noch nicht für Windows Kubernetes-Workerknoten bereitgestellt bzw. gelten noch nicht als vertrauenswürdig. Unterstützung für Windows-Worker wird in einem zukünftigen Release aktiviert.


## <a name="exclude-specific-hosts-or-domains-from-using-the-proxy-server"></a>Ausschließen bestimmter Hosts oder Domänen von der Verwendung des Proxyservers

In den meisten Netzwerken müssen Sie bestimmte Netzwerke, Hosts oder Domänen vom Zugriff über den Proxyserver ausschließen. Dies können Sie erreichen, indem Sie Adresszeichenfolgen mithilfe des Parameters `-proxyServerNoProxy` in `Set-AksHciConfig` ausschließen.

Der Standardwert für `proxyServerNoProxy` ist `localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16`.

Wenn Sie diesen Befehl ausführen, wird Folgendes ausgeschlossen:

- Der localhost-Datenverkehr (localhost, 127.0.0.1)
- Interner Datenverkehr des Kubernetes-Diensts (.svc), wobei _.svc_ einen Platzhalternamen darstellt. Dies ist vergleichbar mit der Aussage *.svc, aber kein* wird in diesem Schema verwendet.
- Der Adressraum des privaten Netzwerks (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). Beachten Sie, dass der Adressraum des privaten Netzwerks wichtige Netzwerke enthält, z. B. Kubernetes Service CIDR (10.96.0.0/12) und Kubernetes POD CIDR (10.244.0.0/16).

Wenngleich diese Standardwerte für viele Netzwerke funktionieren, müssen Sie möglicherweise weitere Subnetzbereiche und/oder Namen zur Ausschlussliste hinzufügen. Sie können z. B. Ihren Unternehmensnamespace (.contoso.com) davon ausschließen, durch den Proxy geleitet zu werden. Dies können Sie erreichen, indem Sie die Werte in der proxyServerNoProxy-Liste angeben:

```powershell
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,.contoso.com"
```

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eine Linux-Anwendung in Ihrem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows-Anwendung in Ihrem Kubernetes-Cluster](./deploy-windows-application.md).
