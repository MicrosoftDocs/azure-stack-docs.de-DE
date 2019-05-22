---
title: 'Azure Stack-Datencenterintegration: Identität'
description: Erfahren Sie, wie Sie Azure Stack AD FS in AD FS des Datencenters integrieren.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: ec0a4b0dc020d9c2c977c7b75b1785cfc98446f6
ms.sourcegitcommit: 61de365c93eb7435891867f9808ec37486882d7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65097105"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure Stack-Datencenterintegration: Identität

Azure Stack kann mithilfe von Azure Active Directory (Azure AD) oder den Active Directory-Verbunddiensten (AD FS) als Identitätsanbieter bereitgestellt werden. Sie müssen die entsprechende Entscheidung treffen, bevor Sie Azure Stack bereitstellen. In einem verbundenen Szenario können Sie zwischen Azure AD und AD FS wählen. In einem nicht verbundenen Szenario wird nur AD FS unterstützt.

> [!IMPORTANT]
> Sie können den Identitätsanbieter nicht wechseln, ohne die gesamte Azure Stack-Lösung erneut bereitzustellen.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory-Verbunddienste (AD FS) und Graph

Die Bereitstellung mit AD FS gestattet Identitäten in einer vorhandenen Active Directory-Gesamtstruktur die Authentifizierung mit Ressourcen in Azure Stack. Diese vorhandene Active Directory-Gesamtstruktur erfordert eine Bereitstellung von AD FS, um das Erstellen einer AD FS-Verbundvertrauensstellung zu ermöglichen.

Authentifizierung ist ein Teil der Identität. Damit rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) in Azure-Stack verwaltet werden kann, muss die Graph-Komponente konfiguriert werden. Wenn der Zugriff auf eine Ressource delegiert wird, sucht die Graph-Komponente das Benutzerkonto mithilfe des LDAP-Protokolls in der vorhandenen Active Directory-Gesamtstruktur.

![AD FS-Architektur von Azure Stack](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Die vorhandenen AD FS sind der Konto-Sicherheitstokendienst (STS), der Ansprüche an Azure Stack AD FS (den Ressourcen-STS) sendet. In Azure Stack erstellt die Automatisierung die Anspruchsanbieter-Vertrauensstellung mit dem Metadatenendpunkt für die vorhandenen AD FS.

Für die vorhandenen AD FS muss eine Vertrauensstellung der vertrauenden Seite konfiguriert werden. Dieser Schritt erfolgt nicht durch die Automatisierung und muss vom Operator konfiguriert werden. Der Azure Stack-VIP-Endpunkt für AD FS kann mithilfe des Musters `https://adfs.<Region>.<ExternalFQDN>/` erstellt werden.

Die Vertrauensstellungskonfiguration der vertrauenden Seite erfordert außerdem, dass die Regeln für die Anspruchstransformation konfiguriert werden, die von Microsoft bereitgestellt werden.

Für die Graph-Konfiguration muss ein Dienstkonto bereitgestellt werden, das über Leseberechtigung im vorhandenen Active Directory verfügt. Dieses Konto ist als Eingabe für die Automatisierung erforderlich, um RBAC-Szenarien zu ermöglichen.

Im letzten Schritt wird ein neuer Besitzer für das Anbieterstandardabonnement konfiguriert. Dieses Konto verfügt über Vollzugriff auf alle Ressourcen, wenn es am Azure Stack-Verwaltungsportal angemeldet ist.

Anforderungen:

|Komponente|Anforderung|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Einrichten der Graph-Integration

Graph unterstützt nur die Integration in eine einzelne Active Directory-Gesamtstruktur. Wenn mehrere Gesamtstrukturen vorhanden sind, wird nur die in der Konfiguration angegebene Gesamtstruktur verwendet, um Benutzer und Gruppen abzurufen.

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

|Parameter|Parameter für das Arbeitsblatt für die Bereitstellung|BESCHREIBUNG|Beispiel|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|FQDN der ADFS-Gesamtstruktur|FQDN der Active Directory-Zielgesamtstruktur,<br>mit der die Integration erfolgen soll.|Contoso.com|
|`CustomADAdminCredentials`| |Ein Benutzer mit LDAP-Leseberechtigung.|IHREDOMAENE\graphservice|

### <a name="configure-active-directory-sites"></a>Konfigurieren von Active Directory-Standorten

Bei Active Directory-Bereitstellungen mit mehreren Standorten sollten Sie den Active Directory-Standort konfigurieren, der Ihrer Azure Stack-Bereitstellung am nächsten gelegen ist. Durch die Konfiguration wird vermieden, dass der Azure Stack-Graph-Dienst Abfragen über einen globalen Katalogserver von einem Remotestandort auflöst.

Fügen Sie das Azure Stack-Subnetz [mit öffentlicher VIP](azure-stack-network.md#public-vip-network) dem Active Directory-Standort hinzu, der Azure Stack am nächsten gelegen ist. Wenn Ihr Active Directory beispielsweise die beiden Standorte Seattle und Redmond aufweist und Azure Stack am Standort Seattle bereitgestellt ist, fügen Sie das Azure Stack-Subnetz mit öffentlicher VIP dem Azure Active Directory-Standort für Seattle hinzu.

Weitere Informationen zu Active Directory-Standorten finden Sie unter [Entwerfen der Standorttopologie](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Wenn Ihr Active Directory aus einem einzigen Standort besteht, können Sie diesen Schritt überspringen. Wenn Sie ein Catch-All-Subnetz konfiguriert haben, vergewissern Sie sich, dass das Azure Stack-Subnetz mit öffentlicher VIP-Adresse kein Teil davon ist.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Erstellen eines Benutzerkontos im vorhandenen Active Directory (optional)

Optional können Sie ein Konto für den Graph-Dienst im vorhandenen Active Directory erstellen. Führen Sie diesen Schritt aus, wenn Sie noch nicht über ein Konto verfügen, das Sie verwenden möchten.

1. Erstellen Sie im vorhandenen Active Directory das folgende Benutzerkonto (Empfehlung):
   - **Benutzername**: graphservice
   - **Kennwort**: Verwenden Sie ein sicheres Kennwort.<br>Konfigurieren Sie das Kennwort so, dass es nie abläuft.

   Es sind keine speziellen Berechtigungen oder eine Mitgliedschaft erforderlich.

#### <a name="trigger-automation-to-configure-graph"></a>Auslösen der Automatisierung zum Konfigurieren von Graph

Verwenden Sie für diesen Vorgang einen Computer in Ihrem Datencenternetzwerk, der mit dem privilegierten Endpunkt in Azure Stack kommunizieren kann.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (Als Administrator ausführen), und stellen Sie eine Verbindung zur IP-Adresse des privilegierten Endpunkts her. Verwenden Sie die Anmeldeinformationen für die **CloudAdmin**-Authentifizierung.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Da Sie nun eine Verbindung mit dem privilegierten Endpunkt hergestellt haben, führen Sie folgenden Befehl aus. 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für das Benutzerkonto ein, das Sie für die Graph-Dienst verwenden möchten (z.B. „graphservice“). Die Eingabe für das Cmdlet Register-DirectoryService muss der Gesamtstrukturname / die Stammdomäne in der Gesamtstruktur sein statt einer anderen Domäne in der Gesamtstruktur.

   > [!IMPORTANT]
   > Warten Sie, bis die Anmeldeinformationen angezeigt werden („Get-Credential“ wird im privilegierten Endpunkt nicht unterstützt), und geben Sie die Anmeldeinformationen des Graph-Dienstkontos ein.

3. Das Cmdlet **Register-DirectoryService** verfügt über optionale Parameter, die Sie in bestimmten Szenarien verwenden können, in denen die vorhandene Active Directory-Überprüfung einen Fehler ergibt. Bei Ausführung dieses Cmdlets wird überprüft, ob die angegebene Domäne die Stammdomäne ist, ein globaler Katalogserver erreichbar ist und für das angegebene Konto Lesezugriff gewährt wird.

   |Parameter|BESCHREIBUNG|
   |---------|---------|
   |`-SkipRootDomainValidation`|Gibt an, dass anstelle der empfohlenen Stammdomäne eine untergeordnete Domäne verwendet werden muss.|
   |`-Force`|Alle Überprüfungen werden umgangen.|

#### <a name="graph-protocols-and-ports"></a>Graph-Protokolle und -Ports

Der Graph-Dienst in Azure Stack verwendet die folgenden Protokolle und Ports für die Kommunikation mit einem beschreibbaren globalen Katalog Server (Global Catalog Server, GC) und Schlüsselverteilungscenter (Key Distribution Center, KDC), die Anmeldeanforderungen in der gewünschten Active Directory-Gesamtstruktur verarbeiten können.

Der Graph-Dienst in Azure Stack verwendet die folgenden Protokolle und Ports für die Kommunikation mit dem Ziel-Active Directory:

|Type|Port|Protocol|
|---------|---------|---------|
|LDAP|389|TCP und UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Einrichten der AD FS-Integration durch Herunterladen von Verbundmetadaten

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

|Parameter|Parameter für das Arbeitsblatt für die Bereitstellung|BESCHREIBUNG|Beispiel|
|---------|---------|---------|---------|
|CustomAdfsName|Name des ADFS-Anbieters|Der Name der Anspruchsanbieter-Vertrauensstellung.<br>Er wird wie hier angegeben auf der AD FS-Startseite angezeigt.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|ADFS-Metadaten-URI|Verbundmetadatenlink| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Auslösen der Automatisierung zum Konfigurieren der Anspruchsanbieter-Vertrauensstellung in Azure Stack

Verwenden Sie für diesen Vorgang einen Computer, der mit dem privilegierten Endpunkt in Azure Stack kommunizieren kann. Es wird erwartet, dass Azure Stack dem vom Konto **STS AD FS** verwendeten Zertifikat vertraut.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und stellen Sie eine Verbindung mit dem privilegierten Endpunkt her.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Da Sie nun mit dem privilegierten Endpunkt verbunden sind, führen Sie den folgenden Befehl mit den Parametern aus, die für Ihre Umgebung geeignet sind:

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Führen Sie den folgenden Befehl aus, um den Besitzer des Anbieterstandardabonnements zu aktualisieren. Verwenden Sie dabei für Ihre Umgebung geeignete Parameter:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Einrichten der AD FS-Integration durch Bereitstellen einer Verbundmetadatendatei

Verwenden Sie ab Version 1807 diese Methode, wenn eine der folgenden Bedingungen erfüllt ist:

- Die Zertifikatskette ist für AD FS im Vergleich zu allen anderen Endpunkten in Azure Stack unterschiedlich.
- Es gibt keine Netzwerkkonnektivität mit dem vorhandenen AD FS-Server aus der AD FS-Instanz von Azure Stack.

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:


|Parameter|BESCHREIBUNG|Beispiel|
|---------|---------|---------|
|CustomAdfsName|Der Name der Anspruchsanbieter-Vertrauensstellung. Er wird wie hier angegeben auf der AD FS-Startseite angezeigt.|Contoso|
|CustomADFSFederationMetadataFileContent|Metadateninhalte|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Erstellen der Verbundmetadatendatei

Für das folgende Verfahren müssen Sie einen Computer verwenden, der über eine Netzwerkverbindung mit der vorhandenen AD FS-Bereitstellung verfügt, die zum Konto-STS wird. Darüber hinaus müssen die erforderlichen Zertifikate installiert sein.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie den folgenden Befehl mit den Parametern aus, die für Ihre Umgebung geeignet sind:

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Kopieren Sie die Metadatendatei auf einen Computer, der mit dem privilegierten Endpunkt kommunizieren kann.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Auslösen der Automatisierung zum Konfigurieren der Anspruchsanbieter-Vertrauensstellung in Azure Stack

Verwenden Sie für diese Prozedur einen Computer, der mit dem privilegierten Endpunkt in Azure Stack kommunizieren kann und Zugriff auf die Metadatendatei hat, die Sie zuvor erstellt haben.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und stellen Sie eine Verbindung mit dem privilegierten Endpunkt her.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Da Sie nun mit dem privilegierten Endpunkt verbunden sind, führen Sie den folgenden Befehl mit den Parametern aus, die für Ihre Umgebung geeignet sind:

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Führen Sie den folgenden Befehl aus, um den Besitzer des Anbieterstandardabonnements zu aktualisieren. Verwenden Sie dabei für Ihre Umgebung geeignete Parameter:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Wenn Sie das Zertifikat auf dem vorhandenen AD FS (Konto-STS) rotieren, müssen Sie die AD FS-Integration erneut einrichten. Sie müssen die Integration auch dann einrichten, wenn der Metadatenendpunkt erreichbar ist oder durch Bereitstellen der Metadatendatei konfiguriert wurde.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Konfigurieren der vertrauenden Seite für eine vorhandene AD FS-Bereitstellung (Konto-STS)

Microsoft stellt ein Skript zur Verfügung, das die Vertrauensstellung der vertrauenden Seite (einschließlich der Anspruchstransformationsregeln) konfiguriert. Die Verwendung des Skripts ist optional, da Sie die Befehle auch manuell ausführen können.

Sie können das Hilfsskript unter [Azure Stack-Tools](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) auf GitHub herunterladen.

Wenn Sie die Befehle manuell ausführen möchten, gehen Sie folgendermaßen vor:

1. Kopieren Sie den folgenden Inhalt in eine TXT-Datei (z.B. unter „C:\ClaimRules.txt“ gespeichert) in Ihrer AD FS-Instanz oder Ihrem Farmmitglied des Datencenters:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Stellen Sie sicher, dass die Windows Forms-basierte Authentifizierung für Extranet und Intranet aktiviert ist. Überprüfen Sie zunächst, ob sie bereits aktiviert ist, indem Sie das folgende Cmdlet ausführen:

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Die von der integrierten Windows-Authentifizierung (WIA) unterstützten Benutzer-Agenten-Zeichenfolgen können für Ihre AD FS-Bereitstellung veraltet sein und müssen möglicherweise aktualisiert werden, um neueste Clients zu unterstützen. Weitere Informationen zum Aktualisieren der von WIA unterstützten Zeichenfolgen für den Benutzer-Agent finden Sie im Artikel [Konfigurieren der formularbasierten Authentifizierung für Geräte im Intranet, die WIA nicht unterstützen](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>Die Schritte zum Aktivieren der formularbasierten Authentifizierungsrichtlinie werden im Artikel [Konfigurieren von Authentifizierungsrichtlinien](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies) beschrieben.

3. Zum Hinzufügen der Vertrauensstellung der vertrauenden Seite führen Sie den folgenden Windows PowerShell-Befehl für Ihre AD FS-Instanz oder Ihr Farmmitglied aus. Stellen Sie sicher, dass Sie den AD FS-Endpunkt aktualisieren, und verweisen Sie auf die Datei, die Sie in Schritt 1 erstellt haben.

   **Für AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Für AD FS 2012/2012 R2**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Sie müssen das AD FS MMC-Snap-In verwenden, um die Ausstellungsautorisierungsregeln zu konfigurieren, wenn Windows Server 2012 oder 2012 R2 AD FS verwendet wird.

4. Wenn Sie einen der Browser Internet Explorer oder Microsoft Edge verwenden, um auf Azure Stack zuzugreifen, müssen Sie Tokenbindungen ignorieren. Andernfalls tritt beim Anmeldeversuch ein Fehler auf. Führen Sie für Ihre AD FS-Instanz oder Ihr Farmmitglied den folgenden Befehl aus:

   > [!note]  
   > Dies gilt nicht, wenn Sie Windows Server 2012 oder 2012 R2 AD FS verwenden. Sie können diesen Befehl problemlos überspringen und die Integration fortsetzen.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>SPN-Erstellung

Es gibt viele Szenarien, die die Verwendung eines Dienstprinzipalnamens (SPN) für die Authentifizierung erfordern. Hier einige Beispiele:

- CLI-Verwendung mit der AD FS-Bereitstellung von Azure Stack
- System Center Management Pack für Azure Stack bei der Bereitstellung mit AD FS
- Ressourcenanbieter in Azure Stack bei der Bereitstellung mit AD FS
- Verschiedene Anwendungen
- Sie benötigen eine nicht interaktive Anmeldung

> [!Important]  
> AD FS unterstützt nur interaktive Anmeldesitzungen. Wenn Sie eine nicht-interaktive Anmeldung für ein automatisiertes Szenario benötigen, müssen Sie einen SPN verwenden.

Weitere Informationen zum Erstellen eines SPN finden Sie unter [Erstellen eines Dienstprinzipals für AD FS](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>Problembehandlung

### <a name="configuration-rollback"></a>Konfigurationsrollback

Wenn ein Fehler auftritt, der die Umgebung in einem Zustand hinterlässt, in dem keine Authentifizierung mehr erfolgen kann, ist eine Rollbackoption verfügbar.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Befehle aus:

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Führen Sie dann das folgende Cmdlet aus:

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Nachdem die Rollbackaktion ausgeführt wurde, wird für alle Änderungen an der Konfiguration ein Rollback ausgeführt. Authentifizierung ist nur mit dem integrierten **CloudAdmin**-Benutzer möglich.

   > [!IMPORTANT]
   > Sie müssen den ursprünglichen Besitzer des Anbieterstandardabonnements konfigurieren.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Erfassen zusätzlicher Protokolle

Wenn für eines der Cmdlets ein Fehler auftritt, können Sie zusätzliche Protokolle erfassen, indem Sie das Cmdlet `Get-Azurestacklogs` verwenden.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Befehle aus:

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Führen Sie dann das folgende Cmdlet aus:

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Nächste Schritte

[Integrieren einer externen Überwachungslösung in Azure Stack](azure-stack-integrate-monitor.md)
