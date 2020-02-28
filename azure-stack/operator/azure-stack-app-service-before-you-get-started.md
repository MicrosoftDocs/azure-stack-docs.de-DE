---
title: Voraussetzungen für das Bereitstellen von Azure App Service unter Azure Stack Hub
description: Es werden die vorbereitenden Schritte beschrieben, die ausgeführt werden müssen, bevor Sie Azure App Service unter Azure Stack Hub bereitstellen können.
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: 8d995550ead87f31a9024cc9c87ba45f0800a78d
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568451"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack-hub"></a>Voraussetzungen für das Bereitstellen von App Service unter Azure Stack Hub

Bevor Sie Azure App Service in Azure Stack Hub bereitstellen, müssen die erforderlichen vorbereitenden Schritte in diesem Artikel ausgeführt werden.

> [!IMPORTANT]
> Wenden Sie das Update 1910 auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie Azure App Service 1.8 bereitstellen.

## <a name="download-the-installer-and-helper-scripts"></a>Herunterladen des Installationsprogramms und der Hilfsskripts

1. Laden Sie die [Hilfsskripts für die Bereitstellung von App Service in Azure Stack Hub](https://aka.ms/appsvconmashelpers) herunter.
2. Laden Sie das [Installationsprogramm für App Service in Azure Stack Hub](https://aka.ms/appsvconmasinstaller) herunter.
3. Extrahieren Sie die Dateien aus der ZIP-Datei der Hilfsskripts. Die folgenden Dateien und Ordner werden extrahiert:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Ordner „Modules“
     - GraphAPI.psm1

## <a name="download-items-from-the-azure-marketplace"></a>Herunterladen von Elementen aus dem Azure Marketplace

Für Azure App Service in Azure Stack Hub müssen Elemente aus dem [Azure Marketplace heruntergeladen werden](azure-stack-download-azure-marketplace-item.md), sodass sie im Azure Stack Hub-Marketplace verfügbar sind. Die Elemente müssen heruntergeladen werden, bevor mit der Bereitstellung oder dem Upgrade von Azure App Service in Azure Stack Hub begonnen wird:

1. Die aktuelle Version des Windows Server 2016 Datacenter-VM-Images.
2. Benutzerdefinierte Skripterweiterung v1.9.1 oder höher. Dies ist eine VM-Erweiterung.

## <a name="get-certificates"></a>Abrufen von Zertifikaten

### <a name="azure-resource-manager-root-certificate-for-azure-stack-hub"></a>Azure Resource Manager-Stammzertifikat für Azure Stack Hub

Öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten auf einem Computer, der den privilegierten Endpunkt für das in Azure Stack Hub integrierte System oder den ASDK-Host erreichen kann.

Führen Sie das Skript *Get-AzureStackRootCert.ps1* in dem Ordner aus, in den Sie die Hilfsskripts extrahiert haben. Das Skript erstellt in dem Ordner des Skripts, das App Service zum Erstellen von Zertifikaten benötigt, ein Stammzertifikat.

Wenn Sie den folgenden PowerShell-Befehl ausführen, müssen Sie den privilegierten Endpunkt und die Anmeldeinformationen für „AzureStack\CloudAdmin“ angeben.

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Parameter des Skripts „Get-AzureStackRootCert.ps1“

| Parameter | Erforderlich oder optional | Standardwert | BESCHREIBUNG |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Erforderlich | AzS-ERCS01 | Privilegierter Endpunkt |
| CloudAdminCredential | Erforderlich | AzureStack\CloudAdmin | Anmeldeinformationen des Domänenkontos für Azure Stack Hub-Cloudadministratoren |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Erforderliche Zertifikate für die ASDK-Bereitstellung von Azure App Service

Das Skript *Create-AppServiceCerts.ps1* erstellt zusammen mit der Azure Stack Hub-Zertifizierungsstelle die vier Zertifikate, die App Service benötigt.

| Dateiname | Zweck |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Standard-SSL-Zertifikat für App Service |
| api.appservice.local.azurestack.external.pfx | SSL-Zertifikat für App Service-API |
| ftp.appservice.local.azurestack.external.pfx | SSL-Zertifikat für App Service-Herausgeber |
| sso.appservice.local.azurestack.external.pfx | Zertifikat der App Service-Identitätsanwendung |

Um die Zertifikate zu erstellen, gehen Sie folgendermaßen vor:

1. Melden Sie sich am ASDK-Host an, indem Sie das Konto „AzureStack\AzureStackAdmin“ verwenden.
2. Öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten.
3. Führen Sie das Skript *Create-AppServiceCerts.ps1* in dem Ordner aus, in den Sie die Hilfsskripts extrahiert haben. Dieses Skript erstellt im gleichen Ordner wie das Skript vier Zertifikate, das App Service zum Erstellen von Zertifikaten benötigt.
4. Geben Sie ein Kennwort ein, um die PFX-Dateien zu schützen, und notieren Sie das Kennwort. Sie müssen es im Installationsprogramm für App Service in Azure Stack Hub eingeben.

#### <a name="create-appservicecertsps1-script-parameters"></a>Parameter des Skripts „Create-AppServiceCerts.ps1“

| Parameter | Erforderlich oder optional | Standardwert | BESCHREIBUNG |
| --- | --- | --- | --- |
| pfxPassword | Erforderlich | Null | Kennwort zum Schutz des privaten Zertifikatschlüssels |
| DomainName | Erforderlich | local.azurestack.external | Azure Stack Hub-Region und Domänensuffix |

### <a name="certificates-required-for-azure-stack-hub-production-deployment-of-azure-app-service"></a>Erforderliche Zertifikate für eine Azure Stack Hub-Produktionsbereitstellung von Azure App Service

Um den Ressourcenanbieter in der Produktion ausführen zu können, müssen Sie die folgenden Zertifikate bereitstellen:

- Standarddomänenzertifikat
- API-Zertifikat
- Veröffentlichungszertifikat
- Identitätszertifikat

#### <a name="default-domain-certificate"></a>Standarddomänenzertifikat

Das Standarddomänenzertifikat wird der Front-End-Rolle zugeordnet. Benutzer-Apps für Platzhalter- oder Standarddomänenanforderungen an Azure App Service verwenden dieses Zertifikat. Das Zertifikat wird auch für Quellcodeverwaltungsvorgänge (Kudu) verwendet.

Das Zertifikat muss das PFX-Format aufweisen und sollte ein Platzhalterzertifikat mit drei Antragstellern sein. Durch diese Anforderung können sowohl die Standarddomäne als auch der SCM-Endpunkt für Quellcodeverwaltungsvorgänge durch ein einziges Zertifikat abgedeckt werden.

| Format | Beispiel |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>API-Zertifikat

Das API-Zertifikat wird der Verwaltungsrolle zugeordnet. Der Ressourcenanbieter verwendet es zum Schützen von API-Aufrufen. Das Zertifikat für die Veröffentlichung muss einen Antragsteller enthalten, der dem API-DNS-Eintrag entspricht.

| Format | Beispiel |
| --- | --- |
| api.appservice.\<Region\>.\<Domänenname\>.\<Erweiterung\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Veröffentlichungszertifikat

Das Zertifikat für die Herausgeberrolle schützt den FTPS-Datenverkehr für App-Besitzer, wenn diese Inhalte hochladen. Das Zertifikat für die Veröffentlichung muss einen Antragsteller enthalten, der dem FTPS-DNS-Eintrag entspricht.

| Format | Beispiel |
| --- | --- |
| ftp.appservice.\<Region\>.\<Domänenname\>.\<Erweiterung\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitätszertifikat

Das Zertifikat für die Identitäts-App ermöglicht Folgendes:

- Integration zwischen dem Azure AD/AD FS-Verzeichnis (Azure Active Directory/Active Directory-Verbunddienste), Azure Stack Hub und App Service zur Unterstützung der Integration mit dem Computeressourcenanbieter.
- Szenarien mit einmaligem Anmelden für die erweiterten Entwicklertools in Azure App Service bei Azure Stack Hub.

Das Zertifikat für die Identität muss einen Antragsteller enthalten und dem folgenden Format entsprechen.

| Format | Beispiel |
| --- | --- |
| sso.appservice.\<Region\>.\<Domänenname\>.\<Erweiterung\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>Überprüfen von Zertifikaten

Vor der Bereitstellung des App Service-Ressourcenanbieters sollten Sie [die zu verwendenden Zertifikate überprüfen](azure-stack-validate-pki-certs.md). Verwenden Sie hierfür das Azure Stack Hub Readiness Checker-Tool, das im [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) zur Verfügung steht. Das Azure Stack Hub Readiness Checker-Tool überprüft, ob die generierten PKI-Zertifikate für die App Service-Bereitstellung geeignet sind.

Als bewährte Methode sollten Sie bei der Arbeit mit einem der erforderlichen [Azure Stack Hub-PKI-Zertifikate](azure-stack-pki-certs.md) ausreichend Zeit zum Testen und (falls erforderlich) erneuten Ausstellen von Zertifikaten einplanen.

## <a name="virtual-network"></a>Virtuelles Netzwerk

> [!NOTE]
> Die Voraberstellung eines benutzerdefinierten virtuellen Netzwerks ist optional. Azure App Service in Azure Stack Hub kann das erforderliche virtuelle Netzwerk erstellen, muss dann aber über öffentliche IP-Adressen mit SQL und dem Dateiserver kommunizieren.

Mit Azure App Service in Azure Stack Hub können Sie den Ressourcenanbieter in einem vorhandenen virtuellen Netzwerk bereitstellen oder ein virtuelles Netzwerk als Teil der Bereitstellung erstellen. Die Nutzung eines vorhandenen virtuellen Netzwerks ermöglicht die Verwendung von internen IP-Adressen zum Herstellen der Verbindung mit dem Dateiserver und dem Computer mit SQL Server, die für Azure App Service in Azure Stack Hub erforderlich sind. Das virtuelle Netzwerk muss vor der Installation von Azure App Service in Azure Stack Hub mit dem folgenden Adressbereich und den folgenden Subnetzen konfiguriert werden:

Virtual Network /16

Subnetze

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>Lizenzierungsaspekte im Zusammenhang mit erforderlichem Dateiserver und SQL Server

Für Azure App Service in Azure Stack Hub sind ein Dateiserver und eine SQL Server-Instanz erforderlich.  Sie können bereits vorhandene Ressourcen außerhalb Ihrer Azure Stack Hub-Bereitstellung verwenden oder Ressourcen unter dem zugehörigen Azure Stack Hub-Standardabonnement des Anbieters bereitstellen.

Wenn Sie sich für die Bereitstellung der Ressourcen innerhalb ihres Azure Stack Hub-Standardabonnements des Anbieters entscheiden, sind die Lizenzen für diese Ressourcen (Windows Server-Lizenzen und SQL Server-Lizenzen) unter folgenden Voraussetzungen in den Kosten von Azure App Service in Azure Stack Hub enthalten:

- Die Infrastruktur wurde im **Standardabonnements des Anbieters** bereitgestellt.
- Die Infrastruktur wird ausschließlich von Azure App Service im Azure Stack Hub-Ressourcenanbieter verwendet.  Keine anderen Workloads – ob administrativer Art (andere Ressourcenanbieter, z. B. SQL-RP) oder mandantenbezogen (z. B. Mandanten-Apps, für die eine Datenbank benötigt wird) – können diese Infrastruktur nutzen.

## <a name="prepare-the-file-server"></a>Vorbereiten des Dateiservers

Azure App Service erfordert die Verwendung eines Dateiservers. Für Produktionsbereitstellungen muss der Dateiserver für Hochverfügbarkeit konfiguriert und in der Lage sein, Fehler zu beheben.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Schnellstartvorlage für den Dateiserver für Bereitstellungen von Azure App Service im ASDK.

Nur für ASDK-Bereitstellungen können Sie die [Azure Resource Manager-Bereitstellungsbeispielvorlage](https://aka.ms/appsvconmasdkfstemplate) verwenden, um einen konfigurierten Einzelknoten-Dateiserver bereitzustellen. Der Einzelknoten-Dateiserver wird sich in einer Arbeitsgruppe befinden.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Schnellstartvorlage für hochverfügbare Dateiserver und SQL Server

Eine [Referenzarchitektur-Schnellstartvorlage](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) ist jetzt verfügbar, mit der ein Dateiserver und eine SQL Server-Instanz bereitgestellt werden. Diese Vorlage unterstützt die Active Directory-Infrastruktur in einem virtuellen Netzwerk, das für die Unterstützung einer hoch verfügbaren Bereitstellung von Azure App Service unter Azure Stack Hub konfiguriert ist.

### <a name="steps-to-deploy-a-custom-file-server"></a>Schritte zum Bereitstellen eines benutzerdefinierten Dateiservers

>[!IMPORTANT]
> Wenn Sie App Service in einem vorhandenen virtuellen Netzwerk bereitstellen möchten, muss der Dateiserver in einem anderen Subnetz als App Service bereitgestellt werden.

>[!NOTE]
> Wenn Sie sich für die Bereitstellung eines Dateiservers mit einer der oben erwähnten Schnellstartvorlagen entschieden haben, können Sie diesen Abschnitt überspringen, da die Dateiserver im Rahmen der Vorlagenbereitstellung konfiguriert werden.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Bereitstellen von Gruppen und Konten in Active Directory

1. Erstellen Sie die folgenden globalen Active Directory-Sicherheitsgruppen:

   - FileShareOwners
   - FileShareUsers

2. Erstellen Sie die folgenden Active Directory-Konten als Dienstkonten:

   - FileShareOwner
   - FileShareUser

   Als bewährte Sicherheitsmethode wird empfohlen, eindeutige Benutzer für diese Konten (und für alle Webrollen) sowie sichere Benutzernamen und Kennwörter zu verwenden. Legen Sie die Kennwörter mit den folgenden Bedingungen fest:

   - Aktivieren Sie **Kennwort läuft nie ab**.
   - Aktivieren Sie **Benutzer kann Kennwort nicht ändern**.
   - Deaktivieren Sie **Benutzer muss Kennwort bei der nächsten Anmeldung ändern**.

3. Fügen Sie die Konten wie folgt Gruppen als Mitglieder hinzu:

   - Fügen Sie **FileShareOwner** der Gruppe **FileShareOwners** hinzu.
   - Fügen Sie **FileShareUser** der Gruppe **FileShareUsers** hinzu.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Bereitstellen von Gruppen und Konten in einer Arbeitsgruppe

>[!NOTE]
> Wenn Sie einen Dateiserver konfigurieren, führen Sie die folgenden Befehle an einer **Administratoreingabeaufforderung** aus. <br>***Verwenden Sie nicht PowerShell.***

Wenn Sie die Azure Resource Manager-Vorlage verwenden, wurden die Benutzer bereits erstellt.

1. Führen Sie die folgenden Befehle aus, um die Konten FileShareOwner und FileShareUser zu erstellen. Ersetzen Sie `<password>` durch Ihre eigenen Werte.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Legen Sie für die Kennwörter der Konten fest, dass sie nie ablaufen, indem Sie die folgenden WMIC-Befehle ausführen:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Erstellen Sie die lokalen Gruppen „FileShareUsers“ und „FileShareOwners“, und fügen Sie diesen die Konten aus dem ersten Schritt hinzu:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Bereitstellen der Inhaltsfreigabe

Die Inhaltsfreigabe enthält die Websiteinhalte des Mandanten. Das Verfahren zum Bereitstellen der Inhaltsfreigabe auf einem einzelnen Dateiserver ist für Active Directory- und Arbeitsgruppenumgebungen identisch. Für einen Failovercluster in Active Directory wird jedoch ein anderes Verfahren verwendet.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Bereitstellen der Inhaltsfreigabe auf einem einzelnen Dateiserver (Active Directory oder Arbeitsgruppe)

Führen Sie auf einem einzelnen Dateiserver die folgenden Befehle an einer Eingabeaufforderung mit erhöhten Rechten aus. Ersetzen Sie den Wert für `C:\WebSites` durch die entsprechenden Pfade in Ihrer Umgebung.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurieren der Zugriffssteuerung für die Freigaben

Führen Sie die folgenden Befehle auf dem Dateiserver an einer Eingabeaufforderung mit erhöhten Rechten oder auf dem Failoverclusterknoten aus, der zurzeit der Besitzer der Clusterressource ist. Ersetzen Sie die kursiven Werte durch die Werte für Ihre Umgebung.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Arbeitsgruppe

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Vorbereiten der SQL Server-Instanz

>[!NOTE]
> Wenn Sie sich für die Bereitstellung der Schnellstartvorlage für hochverfügbare Dateiserver und SQL Server entschieden haben, können Sie diesen Abschnitt überspringen, da SQL Server mit der Vorlage in einer Konfiguration für Hochverfügbarkeit bereitgestellt und konfiguriert wird.

Für die Hosting- und Messdatenbanken von Azure App Service in Azure Stack Hub müssen Sie eine SQL Server-Instanz für die App Service-Datenbanken vorbereiten.

Für ASDK-Bereitstellungen können Sie SQL Server Express 2014 SP2 oder höher verwenden. SQL Server muss so konfiguriert sein, dass die Authentifizierung im **gemischten Modus** unterstützt wird, weil App Service unter Azure Stack Hub die Windows-Authentifizierung **NICHT** unterstützt.

Für die Produktion und Lösungen mit Hochverfügbarkeit sollten Sie eine Vollversion von SQL Server 2014 SP2 oder höher verwenden, Authentifizierung im gemischten Modus aktivieren und die Bereitstellung in einer [Hochverfügbarkeitskonfiguration](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server) durchführen.

Auf die SQL Server-Instanz für Azure App Service in Azure Stack Hub muss von allen App Service-Rollen zugegriffen werden können. Sie können SQL Server im Standardabonnement des Anbieters in Azure Stack Hub bereitstellen. Alternativ können Sie die vorhandene Infrastruktur in Ihrer Organisation nutzen (sofern eine Verbindung mit Azure Stack Hub besteht). Denken Sie bei Verwendung eines Azure Marketplace-Images daran, die Firewall entsprechend zu konfigurieren.

> [!NOTE]
> Einige Images für SQL-IaaS-VMs sind über das Marketplace-Verwaltungsfeature verfügbar. Achten Sie darauf, immer die neueste Version der SQL-IaaS-Erweiterung herunterzuladen, bevor Sie einen virtuellen Computer mit einem Marketplace-Artikel bereitstellen. Die SQL-Images sind mit den in Azure verfügbaren SQL-VMs identisch. Für virtuelle SQL-Computer, die mit diesen Images erstellt werden, stellen die IaaS-Erweiterung und die zugehörigen Portalerweiterungen Features wie das automatische Patchen und Sicherungsfunktionen bereit.
>
> Sie können für alle SQL Server-Rollen eine Standardinstanz oder eine benannte Instanz verwenden. Wenn Sie eine benannte Instanz verwenden, sollten Sie den SQL Server-Browserdienst manuell starten und Port 1434 öffnen.

Das App Service-Installationsprogramm überprüft, ob für die SQL Server-Instanz die Datenbankeigenständigkeit aktiviert ist. Führen Sie die folgenden SQL-Befehle aus, um die Datenbankeigenständigkeit für die SQL Server-Instanz zu aktivieren, die als Host für die App Service-Datenbanken fungiert:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> Wenn Sie App Service in einem vorhandenen virtuellen Netzwerk bereitstellen möchten, muss die SQL Server-Instanz in einem anderen Subnetz als für App Service und den Dateiserver bereitgestellt werden.
>

## <a name="create-an-azure-active-directory-app"></a>Erstellen einer Azure Active Directory-App

Konfigurieren Sie einen Azure AD-Dienstprinzipal zur Unterstützung folgender Vorgänge:

- Integration von VM-Skalierungsgruppen auf Workerebenen
- Einmaliges Anmelden (SSO) für das Azure Functions-Portal und erweiterte Entwicklertools

Diese Schritte gelten nur für durch Azure AD gesicherte Azure Stack Hub-Umgebungen.

Administratoren müssen einmaliges Anmelden konfigurieren, um Folgendes zu ermöglichen:

- Aktivieren der erweiterten Entwicklertools in App Service (Kudu)
- Aktivieren der Verwendung des Azure Functions-Portals

Führen Sie die folgenden Schritte aus, um den Dienstprinzipal in Ihrem Azure AD-Mandanten erstellen:

1. Öffnen Sie eine PowerShell-Instanz als „azurestack\AzureStackAdmin“.
2. Wechseln Sie zum Speicherort der Skripts, die im [Vorbereitungsschritt](azure-stack-app-service-before-you-get-started.md) heruntergeladen und extrahiert wurden.
3. [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md)
4. Führen Sie das Skript **Create-AADIdentityApp.ps1** aus. Geben Sie bei entsprechender Aufforderung die Azure AD-Mandanten-ID ein, die Sie für Ihre Azure Stack Hub-Bereitstellung verwenden. Geben Sie beispielsweise **myazurestack.onmicrosoft.com** ein.
5. Geben Sie im Fenster **Anmeldeinformationen** das Administratorkonto und -kennwort Ihres Azure AD-Diensts ein. Klicken Sie auf **OK**.
6. Geben Sie den Zertifikatdateipfad und das Zertifikatkennwort für das [zuvor erstellte Zertifikat](azure-stack-app-service-before-you-get-started.md) ein. Das für diesen Schritt standardmäßig erstellte Zertifikat lautet **sso.appservice.local.azurestack.external.pfx**.
7. Notieren Sie sich die Anwendungs-ID, die in der PowerShell-Ausgabe zurückgegeben wird. Mit der ID in den folgenden Schritten erteilen Sie die Zustimmung für die Berechtigungen der Anwendung und während der Installation. 
8. Öffnen Sie ein neues Browserfenster, und melden Sie sich als Azure Active Directory-Dienstadministrator beim [Azure-Portal](https://portal.azure.com) an.
9. Öffnen Sie den Dienst Azure Active Directory.
10. Wählen Sie im linken Bereich die Option **App-Registrierungen** aus.
11. Suchen Sie nach der Anwendungs-ID, die Sie in Schritt 7 notiert haben. 
12. Wählen Sie in der Liste die App Service-Anwendungsregistrierung aus.
13. Wählen Sie im linken Bereich **API-Berechtigungen** aus.
14. Wählen Sie **Administratorzustimmung für \<Mandant\> erteilen** aus, wobei \<Mandant\> der Name Ihres Azure AD Mandanten ist. Bestätigen Sie die Gewährung der Zustimmung, indem Sie **Ja** auswählen.

```powershell
    Create-AADIdentityApp.ps1
```

| Parameter | Erforderlich oder optional | Standardwert | BESCHREIBUNG |
| --- | --- | --- | --- |
| DirectoryTenantName | Erforderlich | Null | Azure AD-Mandanten-ID; Geben Sie die GUID oder Zeichenfolge an. Beispiel: myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | Erforderlich | Null | Azure Resource Manager-Endpunkt des Administrators. Beispiel: adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Erforderlich | Null | Azure Resource Manager-Endpunkt des Mandanten Beispiel: management.local.azurestack.external |
| AzureStackAdminCredential | Erforderlich | Null | Administratoranmeldeinformationen für den Azure AD-Dienst |
| CertificateFilePath | Erforderlich | Null | **Vollständiger Pfad** zur zuvor generierten Zertifikatsdatei für die Identitätsanwendung |
| CertificatePassword | Erforderlich | Null | Kennwort zum Schutz des privaten Zertifikatschlüssels |
| Environment | Optional | AzureCloud | Der Name der unterstützten Cloudumgebung, in welcher der Azure Active Directory-Zielgraphdienst verfügbar ist  Zulässige Werte: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-app"></a>Erstellen einer Active Directory-Verbunddienste-App

Für Azure Stack Hub-Umgebungen, die durch AD FS geschützt werden, müssen Sie einen AD FS-Dienstprinzipal zur Unterstützung folgender Vorgänge konfigurieren:

- Integration von VM-Skalierungsgruppen auf Workerebenen
- Einmaliges Anmelden (SSO) für das Azure Functions-Portal und erweiterte Entwicklertools

Administratoren müssen einmaliges Anmelden konfigurieren, um Folgendes zu ermöglichen:

- Konfigurieren eines Dienstprinzipals für die Integration von VM-Skalierungsgruppen auf Workerebenen
- Aktivieren der erweiterten Entwicklertools in App Service (Kudu)
- Aktivieren der Verwendung des Azure Functions-Portals

Folgen Sie diesen Schritten:

1. Öffnen Sie eine PowerShell-Instanz als „azurestack\AzureStackAdmin“.
2. Wechseln Sie zum Speicherort der Skripts, die im [Vorbereitungsschritt](azure-stack-app-service-before-you-get-started.md) heruntergeladen und extrahiert wurden.
3. [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md)
4. Führen Sie das Skript **Create-ADFSIdentityApp.ps1** aus.
5. Geben Sie im Fenster **Anmeldeinformationen** das Administratorkonto und -kennwort Ihrer AD FS-Cloud ein. Klicken Sie auf **OK**.
6. Geben Sie den Zertifikatdateipfad und das Zertifikatkennwort für das [zuvor erstellte Zertifikat](azure-stack-app-service-before-you-get-started.md) ein. Das für diesen Schritt standardmäßig erstellte Zertifikat lautet **sso.appservice.local.azurestack.external.pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Parameter | Erforderlich oder optional | Standardwert | BESCHREIBUNG |
| --- | --- | --- | --- |
| AdminArmEndpoint | Erforderlich | Null | Azure Resource Manager-Endpunkt des Administrators. Beispiel: adminmanagement.local.azurestack.external |
| PrivilegedEndpoint | Erforderlich | Null | Privilegierter Endpunkt, Beispiel: AzS-ERCS01 |
| CloudAdminCredential | Erforderlich | Null | Anmeldeinformationen des Domänenkontos für Azure Stack Hub-Cloudadministratoren. Beispiel: Azurestack\CloudAdmin |
| CertificateFilePath | Erforderlich | Null | **Vollständiger Pfad** zur PFX-Zertifikatsdatei der Identitätsanwendung |
| CertificatePassword | Erforderlich | Null | Kennwort zum Schutz des privaten Zertifikatschlüssels |

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen eines App Service-Ressourcenanbieters zu Azure Stack](azure-stack-app-service-deploy.md)
