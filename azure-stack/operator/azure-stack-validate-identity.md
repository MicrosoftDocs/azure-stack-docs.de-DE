---
title: Überprüfen der Azure-Identität
titleSuffix: Azure Stack Hub
description: Verwenden Sie Azure Stack Hub Readiness Checker, um eine Azure-Identität zu überprüfen.
author: BryanLa
ms.topic: how-to
ms.date: 10/19/2020
ms.author: bryanla
ms.reviewer: jerskine
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: c0fa7d69f0941a3a4867f8bec2d60ebc20c079b7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545583"
---
# <a name="validate-azure-identity"></a>Überprüfen der Azure-Identität

Verwenden Sie das Tool „Azure Stack Hub Readiness Checker“ ( **AzsReadinessChecker** ), um zu überprüfen, ob Ihre Azure Active Directory-Instanz (Azure AD) mit Azure Stack Hub verwendet werden kann. Überprüfen Sie Ihre Azure-Identitätslösung, bevor Sie mit einer Azure Stack Hub-Bereitstellung beginnen.  

Bei der Überprüfung der Bereitschaft wird Folgendes geprüft:

- Azure AD funktioniert als Identitätsanbieter für Azure Stack Hub.
- Das Azure AD-Konto, das Sie verwenden möchten, kann sich als globaler Administrator Ihrer Azure AD-Instanz anmelden.

Durch eine Überprüfung wird sichergestellt, dass Ihre Umgebung von Azure Stack Hub zum Speichern von Informationen zu Benutzern, Anwendungen, Gruppen und Dienstprinzipalen aus Azure Stack Hub in Ihrer Azure AD-Instanz verwendet werden kann.

## <a name="get-the-readiness-checker-tool"></a>Beziehen des Tools zur Bereitschaftsüberprüfung

Laden Sie die neueste Version des Azure Stack Hub Readiness Checker (AzsReadinessChecker) aus dem [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) herunter.

## <a name="install-and-configure"></a>Installieren und konfigurieren

### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein:

#### <a name="az-powershell-modules"></a>Az PowerShell-Module

Die Az PowerShell-Module müssen installiert sein. Anleitungen finden Sie unter [Installieren des PowerShell Az-Vorschaumoduls](powershell-install-az-module.md).

#### <a name="azure-active-directory-azure-ad-environment"></a>Azure Active Directory-Umgebung (Azure AD)

- Bestimmen Sie das Azure AD-Konto, das Sie für Azure Stack Hub verwenden möchten, und stellen Sie sicher, dass es sich um das Konto eines globalen Azure AD-Administrators handelt.
- Ermitteln Sie den Namen Ihres Azure AD-Mandanten. Der Mandantenname muss der primäre Domänenname für Ihre Azure AD-Instanz sein. Beispiel: **contoso.onmicrosoft.com**.

### <a name="steps-to-validate-azure-identity"></a>Schritte zum Überprüfen der Azure-Identität

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine administrative PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl zum Installieren von **AzsReadinessChecker** aus:  

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus. Ersetzen Sie `contoso.onmicrosoft.com` durch den Azure AD-Mandantennamen:

   ```powershell
   Connect-AzAccount -tenant contoso.onmicrosoft.com
   ```

3. Führen Sie an der PowerShell-Eingabeaufforderung folgenden Befehl aus, um die Überprüfung Ihrer Azure AD-Instanz zu starten. Ersetzen Sie `contoso.onmicrosoft.com` durch den Azure AD-Mandantennamen:

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADDirectoryTenantName contoso.onmicrosoft.com 
   ```

4. Wenn das Tool ausgeführt wird, überprüfen Sie die Ausgabe. Vergewissern Sie sich, dass der Status für die Installationsanforderungen **OK** lautet. Eine erfolgreiche Überprüfung wie etwa im folgenden Beispiel wird angezeigt:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.2005.1269 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```


### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein:

#### <a name="azurerm-powershell-modules"></a>AzureRM PowerShell-Module

Die Az PowerShell-Module müssen installiert sein. Anleitungen finden Sie unter [Installieren des PowerShell AzureRM-Moduls](powershell-install-az-module.md).

#### <a name="the-computer-on-which-the-tool-runs"></a>Auf dem Computer, auf dem das Tool ausgeführt wird, muss Folgendes installiert sein:

- Windows 10 oder Windows Server 2016 mit Internetkonnektivität
- PowerShell 5.1 oder höher Um Ihre Version zu überprüfen, führen Sie den folgenden PowerShell-Befehl aus, und überprüfen Sie dann die **Hauptversion** und die **Nebenversionen** :  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [PowerShell, konfiguriert für Azure Stack Hub](powershell-install-az-module.md).
- Die neueste Version des Tools [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).

#### <a name="azure-ad-environment"></a>Azure AD-Umgebung

- Bestimmen Sie das Azure AD-Konto, das Sie für Azure Stack Hub verwenden möchten, und stellen Sie sicher, dass es sich um das Konto eines globalen Azure AD-Administrators handelt.
- Ermitteln Sie den Namen Ihres Azure AD-Mandanten. Der Mandantenname muss der primäre Domänenname für Ihre Azure AD-Instanz sein. Beispiel: **contoso.onmicrosoft.com**.
- Bestimmen Sie die Azure-Umgebung, die Sie verwenden möchten. Unterstützte Werte für den Umgebungsnamensparameter sind je nach Ihrem verwendeten Azure-Abonnement **AzureCloud** , **AzureChinaCloud** oder **AzureUSGovernment**.

## <a name="steps-to-validate-azure-identity"></a>Schritte zum Überprüfen der Azure-Identität

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine administrative PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl zum Installieren von **AzsReadinessChecker** aus:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um `$serviceAdminCredential` als Dienstadministrator für Ihren Azure AD-Mandanten festzulegen.  Ersetzen Sie `serviceadmin\@contoso.onmicrosoft.com` durch Ihr Konto und den Namen Ihres Mandanten:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. Führen Sie in der PowerShell-Eingabeaufforderung folgenden Befehl aus, um die Überprüfung Ihrer Azure AD-Instanz zu starten:

   - Geben Sie den Umgebungsnamenswert für **AzureEnvironment** an. Unterstützte Werte für den Umgebungsnamensparameter sind je nach Ihrem verwendeten Azure-Abonnement **AzureCloud** , **AzureChinaCloud** oder **AzureUSGovernment**.
   - Ersetzen Sie `contoso.onmicrosoft.com` durch den Azure AD-Mandantennamen.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Wenn das Tool ausgeführt wird, überprüfen Sie die Ausgabe. Vergewissern Sie sich, dass der Status für die Installationsanforderungen **OK** lautet. Eine erfolgreiche Überprüfung wie etwa im folgenden Beispiel wird angezeigt:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```
--- 


## <a name="report-and-log-file"></a>Berichts- und Protokolldatei

Bei jeder Ausführung einer Überprüfung werden Ergebnisse in den Dateien **AzsReadinessChecker.log** und **AzsReadinessCheckerReport.json** protokolliert. Der Speicherort dieser Dateien wird mit den Überprüfungsergebnissen in PowerShell angezeigt.

Anhand dieser Dateien können Sie den Überprüfungsstatus freigeben, bevor Sie Azure Stack Hub bereitstellen oder Probleme mit der Überprüfung untersuchen. Die Ergebnisse aller nachfolgenden Überprüfungen werden in beiden Dateien gespeichert. Der Bericht enthält die Bestätigung Ihres Bereitstellungsteams über die Identitätskonfiguration. Mithilfe der Protokolldatei kann Ihr Bereitstellungs- oder Supportteam Probleme bei der Überprüfung untersuchen.

Standardmäßig werden beide Dateien in `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json` geschrieben.  

- Verwenden Sie den Parameter `-OutputPath <path>` am Ende der Ausführungsbefehlszeile, um einen anderen Berichtsspeicherort anzugeben.
- Verwenden Sie den Parameter `-CleanReport` am Ende des Ausführungsbefehls, um Informationen zu früheren Ausführungen des Tools aus **AzsReadinessCheckerReport.json** zu löschen.

Weitere Informationen finden Sie unter [Azure Stack Hub-Überprüfungsbericht](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Fehler bei der Überprüfung

Wenn bei der Überprüfung Fehler auftreten, werden Details zum Fehler im PowerShell-Fenster angezeigt. Das Tool protokolliert Informationen außerdem in der Datei „AzsReadinessChecker.log“.

Die folgenden Beispiele veranschaulichen, wie häufige Fehler bei der Überprüfung behoben werden.

### <a name="expired-or-temporary-password"></a>Abgelaufenes oder temporäres Kennwort

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Ursache:** Das Konto kann sich nicht anmelden, da das Kennwort entweder abgelaufen oder temporär ist.

**Lösung:** Führen Sie in PowerShell den folgenden Befehl aus, und befolgen Sie die Eingabeaufforderungen zum Zurücksetzen des Kennworts:

```powershell
Login-AzureRMAccount
```

Alternativ können Sie sich beim [Azure-Portal](https://portal.azure.com) als Kontobesitzer anmelden, und der Benutzer wird gezwungen, das Kennwort zu ändern.

### <a name="unknown-user-type"></a>Unbekannter Benutzertyp 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Ursache:** Das Konto kann sich nicht bei der angegebenen Azure AD-Instanz ( **AADDirectoryTenantName** ) anmelden. In diesem Beispiel ist **AzureChinaCloud** für **AzureEnvironment** angegeben.

**Lösung** : Vergewissern Sie sich, dass das Konto für die angegebene Azure-Umgebung gültig ist. Führen Sie in PowerShell folgenden Befehl aus, um zu überprüfen, ob das Konto für eine bestimmte Umgebung gültig ist:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Konto ohne Administratorrolle

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Ursache:** Auch wenn sich das Konto erfolgreich anmelden kann, gehört das Konto nicht einem Administrator von Azure AD ( **AADDirectoryTenantName** ).  

**Lösung:** Melden Sie sich als Kontoinhaber beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Azure Active Directory** > **Benutzer** > **Auswahl des Benutzers**. Wählen Sie dann **Verzeichnisrolle** aus, und vergewissern Sie sich, dass der Benutzer die Rolle **Globaler Administrator** besitzt. Wenn das Konto der jeweilige **Benutzer** ist, navigieren Sie zu **Azure Active Directory** > **Benutzerdefinierte Domänennamen** , und vergewissern Sie sich, dass der für **AADDirectoryTenantName** angegebene Name als primärer Domänenname für dieses Verzeichnis gekennzeichnet ist. In diesem Beispiel ist dies **contoso.onmicrosoft.com**.

Bei Azure Stack Hub muss der Domänenname der primäre Domänenname sein.

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen der Azure-Registrierung](azure-stack-validate-registration.md)  
[Anzeigen des Bereitschaftsberichts](azure-stack-validation-report.md)  
[Allgemeine Überlegungen zur Azure Stack Hub-Integration](azure-stack-datacenter-integration.md)  
