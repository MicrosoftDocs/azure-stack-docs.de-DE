---
title: Vorbereiten auf den Erweiterungshost in Azure Stack Hub
description: Es wird beschrieben, wie Sie die Vorbereitung auf den Erweiterungshost in Azure Stack Hub durchführen, der mit einem Azure Stack Hub-Updatepaket nach Version 1808 automatisch aktiviert wird.
author: ihenkel
ms.author: inhenkel
ms.date: 1/22/2020
ms.topic: article
ms.reviewer: thoroet
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: c28f47930ea7fa9913ae339ea379562477694492
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882268"
---
# <a name="prepare-for-extension-host-in-azure-stack-hub"></a>Vorbereiten auf den Erweiterungshost in Azure Stack Hub

Der Erweiterungshost trägt zum Schutz von Azure Stack Hub bei, indem die Anzahl erforderlicher TCP/IP-Ports verringert wird. In diesem Artikel erfahren Sie, wie Sie Azure Stack Hub für den Erweiterungshost vorbereiten, der nach dem Update 1808 automatisch über ein Azure Stack Hub-Updatepaket aktiviert wird. Dieser Artikel gilt für die Updates 1808, 1809 und 1811 von Azure Stack Hub.

## <a name="certificate-requirements"></a>Zertifikatanforderungen

Der Erweiterungshost implementiert zwei neue Domänennamespaces, um eindeutige Hosteinträge für jede Portalerweiterung zu gewährleisten. Die neuen Domänennamespaces erfordern zwei zusätzliche Platzhalterzertifikate, um eine sichere Kommunikation sicherzustellen.

Die Tabelle zeigt die neuen Namespaces und die zugehörigen Zertifikate:

| Bereitstellungsordner | Erforderlicher Zertifikatantragsteller und alternative Antragstellernamen | Bereich (pro Region) | Namespace der Unterdomäne |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Administrator-Erweiterungshost | *.adminhosting.\<Region>.\<FQDN> (SSL-Platzhalterzertifikate) | Administrator-Erweiterungshost | adminhosting.\<Region>.\<FQDN> |
| Öffentlicher Erweiterungshost | *.hosting.\<Region>.\<FQDN> (SSL-Platzhalterzertifikate) | Öffentlicher Erweiterungshost | hosting.\<region>.\<fqdn> |

Ausführlichere Informationen zu den Zertifikatanforderungen finden Sie unter [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Erstellen der Zertifikatsignieranforderung

Mit dem Tool Azure Stack Hub Readiness Checker können Sie eine Zertifikatsignieranforderung für die beiden neuen und erforderlichen SSL-Zertifikate erstellen. Befolgen Sie die Schritte im Artikel [Generieren von Signieranforderungen für Azure Stack Hub-Zertifikate](azure-stack-get-pki-certs.md).

> [!Note]  
> Sie können diesen Schritt abhängig davon überspringen, wie Sie Ihre SSL-Zertifikate angefordert haben.

## <a name="validate-new-certificates"></a>Überprüfen neuer Zertifikate

1. Öffnen Sie PowerShell mit erhöhten Berechtigungen auf dem Hardware-Lebenszyklushost oder dem Azure Stack Hub-Verwaltungscomputer.
2. Führen Sie das folgende Cmdlet aus, um das Tool Azure Stack Hub Readiness Checker zu installieren:

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Führen Sie das folgende Skript aus, um die erforderliche Ordnerstruktur zu erstellen:

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Wenn Sie die Bereitstellung mit Azure Active Directory-Verbunddiensten (AD FS) durchführen, müssen dem Skript unter **$directories** die folgenden Verzeichnisse hinzugefügt werden: `ADFS`, `Graph`.

4. Platzieren Sie die Zertifikate, die Sie derzeit in Azure Stack Hub verwenden, in den entsprechenden Verzeichnissen. Legen Sie also beispielsweise das **ARM-Administratorzertifikat** im Ordner `Arm Admin` ab. Platzieren Sie anschließend die neu erstellten Hostingzertifikate in den Verzeichnissen `Admin extension host` und `Public extension host`.
5. Führen Sie das folgende Cmdlet aus, um die Überprüfung der Zertifikate zu starten:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Überprüfen Sie die Ausgabe und den erfolgreichen Ausgang aller Tests für alle Zertifikate.


## <a name="import-extension-host-certificates"></a>Importieren von Zertifikaten für den Erweiterungshost

Verwenden Sie für die folgenden Schritte einen Computer, der eine Verbindung mit dem für Azure Stack Hub berechtigten Endpunkt herstellen kann. Vergewissern Sie sich, dass Sie Zugriff auf die neuen Zertifikatsdateien auf diesem Computer haben.

1. Verwenden Sie für die folgenden Schritte einen Computer, der eine Verbindung mit dem für Azure Stack Hub berechtigten Endpunkt herstellen kann. Vergewissern Sie sich, dass Sie auf die neuen Zertifikatsdateien auf diesem Computer zugreifen.
2. Öffnen Sie die PowerShell-ISE zum Ausführen der nächsten Skriptblöcke.
3. Importieren Sie das Zertifikat für den Administrator-Hostingendpunkt.

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importieren Sie das Zertifikat für den Hostingendpunkt.
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Aktualisieren der DNS-Konfiguration

> [!Note]  
> Dieser Schritt ist nicht erforderlich, wenn Sie die DNS-Zonendelegierung für die DNS-Integration verwenden.
Wenn einzelne A-Datensätze des Hosts zum Veröffentlichen von Azure Stack Hub-Endpunkten konfiguriert wurden, müssen Sie zwei zusätzliche A-Datensätze für den Host erstellen:

| IP | Hostname | type |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | Ein |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | Ein |

Die zugeordneten IP-Adressen können mithilfe des berechtigten Endpunkts abgerufen werden, indem das Cmdlet **Get-AzureStackStampInformation** ausgeführt wird.

### <a name="ports-and-protocols"></a>Ports und Protokolle

Im Artikel [Integration des Azure Stack Hub-Datencenters – Veröffentlichen von Endpunkten](azure-stack-integrate-endpoints.md) werden die Ports und Protokolle behandelt, für die vor dem Rollout des Erweiterungshosts eine eingehende Kommunikation zum Veröffentlichen von Azure Stack Hub erforderlich ist.

### <a name="publish-new-endpoints"></a>Veröffentlichen neuer Endpunkte

Es gibt zwei neue Endpunkte, die über die Firewall hinweg veröffentlicht werden müssen. Die zugeordneten IP-Adressen aus dem öffentlichen VIP-Pool können mit dem folgenden Code abgerufen werden. Dieser muss über den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) Ihrer Azure Stack Hub-Umgebung ausgeführt werden.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and extension host information from Azure Stack Hub Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Beispielausgabe

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Nehmen Sie diese Änderung vor dem Aktivieren des Erweiterungshosts vor. Dadurch sind die Azure Stack Hub-Portale ohne Unterbrechung erreichbar.

| Endpunkt (VIP) | Protocol | Ports |
|----------------|----------|-------|
| Administratorhosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Aktualisieren vorhandener Veröffentlichungsregeln (nach der Aktivierung des Erweiterungshosts)

> [!Note]  
> Das Paket für das Azure Stack Hub-Update 1808 aktiviert den Erweiterungshost **nicht**. Es ermöglicht Ihnen die Vorbereitung für den Erweiterungshost durch das Importieren der erforderlichen Zertifikate. Schließen Sie keine Ports, bevor der Erweiterungshost nicht automatisch über ein Azure Stack Hub-Updatepaket nach dem Update 1808 aktiviert wurde.

Die folgenden vorhandenen Endpunktports müssen in vorhandenen Firewallregeln geschlossen werden.

> [!Note]  
> Wir empfehlen Ihnen, die Ports nach der erfolgreichen Validierung zu schließen.

| Endpunkt (VIP) | Protocol | Ports |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (Administrator) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portal (Benutzer) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (Administrator) | HTTPS | 30024 |
| Azure Resource Manager (Benutzer) | HTTPS | 30024 |

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Firewallintegration](azure-stack-firewall.md).
- Informieren Sie sich über das [Generieren von Signieranforderungen für Azure Stack Hub-Zertifikate](azure-stack-get-pki-certs.md).
