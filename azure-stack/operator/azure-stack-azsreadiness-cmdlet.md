---
title: Referenz zum Cmdlet „Start-AzsReadinessChecker“ | Microsoft-Dokumentation
description: Hilfe zum PowerShell-Cmdlet für das Azure Stack-Modul für die Bereitschaftsüberprüfung.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: f60ee96673b5574f0cd0393dc6a53a2d7937c04f
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159157"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referenz zum Cmdlet „Start-AzsReadinessChecker“

Modul: **Microsoft.AzureStack.ReadinessChecker**

Dieses Modul enthält nur ein einzelnes Cmdlet. Das Cmdlet führt eine oder mehrere Funktionen vor der Bereitstellung oder Wartung für Azure Stack durch.

## <a name="syntax"></a>Syntax

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>BESCHREIBUNG

Das Cmdlet **Start-AzsReadinessChecker** überprüft Zertifikate, Azure-Konten, Azure-Abonnements und Azure Active Directory-Instanzen (Azure AD). Führen Sie vor dem Bereitstellen von Azure Stack oder vor Azure Stack-Wartungsaktionen wie etwa der Geheimnisrotation eine Überprüfung aus. Das Cmdlet kann auch zum Generieren von Zertifikatsignieranforderungen für Infrastrukturzertifikate (und optional für PaaS-Zertifikate) verwendet werden. Zu guter Letzt kann das Cmdlet PFX-Zertifikate neu packen, um häufige Probleme mit der Paketerstellung zu beheben.

## <a name="examples"></a>Beispiele

### <a name="example-generate-certificate-signing-request"></a>Beispiel: Generieren einer Zertifikatsignieranforderung

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

In diesem Beispiel generiert `Start-AzsReadinessChecker` mehrere Zertifikatsignieranforderungen (Certificate Signing Requests, CSRs) für Zertifikate, die für eine AD FS-Azure Stack-Bereitstellung mit dem Regionsnamen **east** und dem externen FQDN **azurestack.contoso.com** geeignet sind.

### <a name="example-validate-certificates"></a>Beispiel: Überprüfen von Zertifikaten

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

In diesem Beispiel wird aus Sicherheitsgründen das PFX-Kennwort benötigt, und `Start-AzsReadinessChecker` überprüft den relativen Ordner **Certificates** auf Zertifikate, die für eine Azure AD-Bereitstellung mit dem Regionsnamen **east** und dem externen FQDN **azurestack.contoso.com** gültig sind.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Beispiel: Überprüfen von Zertifikaten mit Bereitstellungsdaten (Bereitstellung und Support)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

In diesem Bereitstellungs- und Supportbeispiel wird aus Sicherheitsgründen das PFX-Kennwort benötigt, und `Start-AzsReadinessChecker` überprüft den relativen Ordner **Certificates** auf Zertifikate, die für eine Bereitstellung geeignet sind, bei der Identität, Region und externer FQDN aus der für die Bereitstellung generierten JSON-Datei mit Bereitstellungsdaten gelesen werden.

### <a name="example-validate-paas-certificates"></a>Beispiel: Überprüfen von PaaS-Zertifikaten

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

In diesem Beispiel wird eine Hashtabelle mit Pfaden und Kennwörtern für jedes PaaS-Zertifikat erstellt. Zertifikate können weggelassen werden. `Start-AzsReadinessChecker` prüft, ob die einzelnen PFX-Pfade vorhanden sind, und überprüft sie anhand der Region **east** und des externen FQDN **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Beispiel: Überprüfen von PaaS-Zertifikaten mit Bereitstellungsdaten

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

In diesem Beispiel wird eine Hashtabelle mit Pfaden und Kennwörtern für jedes PaaS-Zertifikat erstellt. Zertifikate können weggelassen werden. `Start-AzsReadinessChecker` prüft, ob die einzelnen PFX-Pfade vorhanden sind, und überprüft sie anhand der Region und des externen FQDN, die aus der für die Bereitstellung generierten JSON-Datei mit Bereitstellungsdaten gelesen werden.

### <a name="example-validate-azure-identity"></a>Beispiel: Überprüfen der Azure-Identität

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

In diesem Beispiel werden aus Sicherheitsgründen die Anmeldeinformationen des Dienstadministratorkontos benötigt, und `Start-AzsReadinessChecker` überprüft, ob das Azure-Konto und Azure AD für eine Azure AD-Bereitstellung mit dem Mandantenverzeichnisnamen **azurestack.contoso.com** gültig sind.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Beispiel: Überprüfen der Azure-Identität mit Bereitstellungsdaten (Bereitstellungsunterstützung)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

In diesem Beispiel werden aus Sicherheitsgründen die Anmeldeinformationen des Dienstadministratorkontos benötigt, und `Start-AzsReadinessChecker` überprüft, ob das Azure-Konto und Azure AD für eine Azure AD-Bereitstellung gültig sind, bei der **AzureCloud** und **TenantName** aus der für die Bereitstellung generierten JSON-Datei mit Bereitstellungsdaten gelesen werden.

### <a name="example-validate-azure-registration"></a>Beispiel: Überprüfen der Azure-Registrierung

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

In diesem Beispiel werden aus Sicherheitsgründen die Anmeldeinformationen des Abonnementbesitzers benötigt, und `Start-AzsReadinessChecker` führt eine Überprüfung anhand des angegebenen Kontos und Abonnements aus, um sicherzustellen, dass diese für die Azure Stack-Registrierung verwendet werden können.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Beispiel: Überprüfen der Azure-Registrierung mit Bereitstellungsdaten (Bereitstellungsteam)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

In diesem Beispiel werden aus Sicherheitsgründen die Anmeldeinformationen des Abonnementbesitzers benötigt, und `Start-AzsReadinessChecker` führt eine Überprüfung anhand des angegebenes Kontos und Abonnements aus, um sicherzustellen, dass diese für die Azure Stack-Registrierung verwendet werden können, wobei zusätzliche Details aus der für die Bereitstellung generierten JSON-Datei mit Bereitstellungsdaten gelesen werden.

### <a name="example-importexport-pfx-package"></a>Beispiel: Importieren/Exportieren eines PFX-Pakets

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

In diesem Beispiel wird aus Sicherheitsgründen das PFX-Kennwort benötigt. Die Datei „Ssl.pfx“ wird in den Zertifikatspeicher des lokalen Computers importiert, mit dem gleichen Kennwort wieder exportiert und als „Ssl_new.pfx“ gespeichert. Diese Vorgehensweise wird verwendet, wenn die Zertifikatüberprüfung feststellt, dass ein privater Schlüssel nicht über den Attributsatz **Lokaler Computer** verfügt, die Zertifikatkette unterbrochen ist, irrelevante Zertifikate in der PFX-Datei enthalten sind oder die Zertifikatkette eine falsche Reihenfolge aufweist.

### <a name="example-view-validation-report-deployment-and-support"></a>Beispiel: Anzeigen des Überprüfungsberichts (Bereitstellung und Unterstützung)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

In diesem Beispiel erhält das Bereitstellungs- oder Supportteam den Bereitschaftsbericht vom Kunden (Contoso), und `Start-AzsReadinessChecker` wird verwendet, um den Status der von Contoso ausgeführten Überprüfungen anzuzeigen.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Beispiel: Anzeigen der Zusammenfassung des Überprüfungsberichts ausschließlich zur Zertifikatüberprüfung (Bereitstellung und Support)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

In diesem Beispiel erhält das Bereitstellungs- oder Supportteam den Bereitschaftsbericht vom Kunden (Contoso), und `Start-AzsReadinessChecker` wird verwendet, um einen zusammengefassten Status der von Contoso ausgeführten Zertifikatüberprüfungen anzuzeigen.

## <a name="required-parameters"></a>Erforderliche Parameter

### <a name="-regionname"></a>-RegionName

Gibt den Regionsnamen der Azure Stack-Bereitstellung an.

|  |  |
|----------------------------|--------------|
|Geben Sie Folgendes ein:                       |Zeichenfolge        |
|Position:                   |benannt         |
|Standardwert:              |Keine          |
|Pipelineeingabe akzeptieren:      |False         |
|Platzhalterzeichen akzeptieren: |False         |

### <a name="-fqdn"></a>-FQDN

Gibt den externen FQDN der Azure Stack-Bereitstellung an, der auch als Alias für **ExternalFQDN** und **ExternalDomainName** verwendet wird.

|  |  |
|----------------------------|--------------|
|Geben Sie Folgendes ein:                       |Zeichenfolge        |
|Position:                   |benannt         |
|Standardwert:              |ExternalFQDN, ExternalDomainName |
|Pipelineeingabe akzeptieren:      |False         |
|Platzhalterzeichen akzeptieren: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Gibt die gültigen Werte des Identitätssystems der Azure Stack-Bereitstellung an: „AAD“ für Azure Active Directory oder „ADFS“ für Active Directory-Verbunddienste.

|  |  |
|----------------------------|--------------|
|Geben Sie Folgendes ein:                       |Zeichenfolge        |
|Position:                   |benannt         |
|Standardwert:              |Keine          |
|Gültige Werte:               |'AAD','ADFS'  |
|Pipelineeingabe akzeptieren:      |False         |
|Platzhalterzeichen akzeptieren: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Gibt das den PFX-Zertifikatdateien zugeordnete Kennwort an.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |SecureString |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Gibt die Hashtabelle an, die Pfade und Kennwörter für PaaS-Zertifikate enthält.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Hashtable |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Gibt die JSON-Konfigurationsdatei mit Azure Stack-Bereitstellungsdaten an. Diese Datei wird für die Bereitstellung generiert.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-pfxpath"></a>-PfxPath

Gibt den Pfad zu einem problematischen Zertifikat an, für dessen Korrektur eine Import-/Exportroutine erforderlich ist (gemäß Angabe der Zertifikatüberprüfung in diesem Tool).

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Gibt den Zielpfad für die resultierende PFX-Datei der Import-/Exportroutine an.  

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-subject"></a>-Subject

Gibt ein sortiertes Wörterbuch des Fachgebiets für die Generierung von Zertifikatanforderungen an.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |OrderedDictionary   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-requesttype"></a>-RequestType

Gibt den SAN-Typ der Zertifikatanforderung an. Gültige Werte: **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** generiert mehrere Zertifikatanforderungen, eine für jeden Dienst.
- **SingleCSR** generiert eine Zertifikatanforderung für alle Dienste.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Gültige Werte:               |'MultipleCSR','SingleCSR' |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Gibt den Zielpfad für die Zertifikatanforderungsdateien an. Das Verzeichnis muss bereits vorhanden sein.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Gibt den für die Azure Stack-Bereitstellung zu verwendenden Azure AD-Dienstadministrator an.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |PSCredential   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Gibt den für die Azure Stack-Bereitstellung zu verwendenden Azure AD-Namen an.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Gibt die Instanz der Azure-Dienste mit den Konten, Verzeichnissen und Abonnements an, die für die Azure Stack-Bereitstellung und -Registrierung verwendet werden sollen.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Gültige Werte:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Gibt das Registrierungskonto an, das für die Azure Stack-Registrierung verwendet werden soll.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Gibt die Registrierungsabonnement-ID an, die für die Azure Stack-Registrierung verwendet werden soll.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Guid     |
|Position:                   |benannt    |
|Standardwert:              |Keine     |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-reportpath"></a>-ReportPath

Gibt den Pfad für den Bereitschaftsbericht an. Standardmäßig werden das aktuelle Verzeichnis und der Standardberichtsname verwendet.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Alle      |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

## <a name="optional-parameters"></a>Optionale Parameter

### <a name="-certificatepath"></a>-CertificatePath

Gibt den Pfad an, unter dem nur die für das Zertifikat erforderlichen Zertifikatordner enthalten sind.

Folgende Ordner sind für die Azure Stack-Bereitstellung mit dem Azure AD-Identitätssystem erforderlich:

- ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Zu den erforderlichen Ordnern für die Azure Stack-Bereitstellung mit dem Identitätssystem der Active Directory-Verbunddienste (AD FS) zählen Folgende:

- ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |.\Certificates |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Gibt an, ob PaaS-Dienste/-Hostnamen zu Zertifikatanforderungen hinzugefügt werden sollen.

|  |  |
|----------------------------|------------------|
|Geben Sie Folgendes ein:                       |SwitchParameter   |
|Position:                   |benannt             |
|Standardwert:              |False             |
|Pipelineeingabe akzeptieren:      |False             |
|Platzhalterzeichen akzeptieren: |False             |

### <a name="-reportsections"></a>-ReportSections

Gibt an, ob nur die Berichtszusammenfassung angezeigt werden soll und Details ausgelassen werden sollen.

|  |  |
|----------------------------|---------|
|Geben Sie Folgendes ein:                       |Zeichenfolge   |
|Position:                   |benannt    |
|Standardwert:              |Alle      |
|Gültige Werte:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Pipelineeingabe akzeptieren:      |False    |
|Platzhalterzeichen akzeptieren: |False    |

### <a name="-summary"></a>-Summary

Gibt an, ob nur die Berichtszusammenfassung angezeigt werden soll und Details ausgelassen werden sollen.

|  |  |
|----------------------------|------------------|
|Geben Sie Folgendes ein:                       |SwitchParameter   |
|Position:                   |benannt             |
|Standardwert:              |False             |
|Pipelineeingabe akzeptieren:      |False             |
|Platzhalterzeichen akzeptieren: |False             |

### <a name="-cleanreport"></a>-CleanReport

Entfernt die vorherige Ausführung und den Überprüfungsverlauf und schreibt Überprüfungen in einen neuen Bericht.

|  |  |
|----------------------------|------------------|
|Geben Sie Folgendes ein:                       |SwitchParameter   |
|Aliase:                    |cf                |
|Position:                   |benannt             |
|Standardwert:              |False             |
|Pipelineeingabe akzeptieren:      |False             |
|Platzhalterzeichen akzeptieren: |False             |

### <a name="-outputpath"></a>-OutputPath

Gibt einen benutzerdefinierte Pfad zum Speichern des JSON-Bereitschaftsberichts und der ausführlichen Protokolldatei an. Ist der Pfad noch nicht vorhanden, wird versucht, das Verzeichnis zu erstellen.

|  |  |
|----------------------------|------------------|
|Geben Sie Folgendes ein:                       |Zeichenfolge            |
|Position:                   |benannt             |
|Standardwert:              |$ENV:TEMP\AzsReadinessChecker  |
|Pipelineeingabe akzeptieren:      |False             |
|Platzhalterzeichen akzeptieren: |False             |

### <a name="-confirm"></a>-Confirm

Fordert vor der Ausführung des Cmdlets zur Bestätigung auf.

|  |  |
|----------------------------|------------------|
|Geben Sie Folgendes ein:                       |SwitchParameter   |
|Aliase:                    |cf                |
|Position:                   |benannt             |
|Standardwert:              |False             |
|Pipelineeingabe akzeptieren:      |False             |
|Platzhalterzeichen akzeptieren: |False             |

### <a name="-whatif"></a>-WhatIf

Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird. Das Cmdlet wird nicht ausgeführt.

|  |  |
|----------------------------|------------------|
|Geben Sie Folgendes ein:                       |SwitchParameter   |
|Aliase:                    |wi                |
|Position:                   |benannt             |
|Standardwert:              |False             |
|Pipelineeingabe akzeptieren:      |False             |
|Platzhalterzeichen akzeptieren: |False             |
