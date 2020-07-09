---
title: Überprüfen der Azure-Registrierung
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie eine Azure-Registrierung mit dem Azure Stack Hub Readiness Checker-Tool überprüfen.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 06/25/2020
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/25/2020
ms.openlocfilehash: 24bec6b10017dd824800f860273c003dc8b53f0b
ms.sourcegitcommit: 28850ae18844213ee410cfe96fc936655b5f6412
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86148148"
---
# <a name="validate-azure-registration"></a>Überprüfen der Azure-Registrierung

Verwenden Sie das Tool „Azure Stack Hub Readiness Checker“ (**AzsReadinessChecker**), um zu überprüfen, ob Ihr Azure-Abonnement mit Azure Stack Hub verwendet werden kann, bevor Sie mit einer Azure Stack Hub-Bereitstellung beginnen. Bei der Überprüfung der Bereitschaft wird Folgendes geprüft:

- Bei dem Azure-Abonnement, das Sie verwenden, handelt es sich um einen unterstützten Typ. Bei Abonnements muss es sich um einen Cloudlösungsanbieter (Cloud Solution Provider, CSP) oder ein Enterprise Agreement (EA) handeln.
- Das Konto, mit dem Sie Ihr Abonnement bei Azure registrieren, kann sich bei Azure anmelden und ist der Besitzer eines Abonnements.

Weitere Informationen zur Azure Stack Hub-Registrierung finden Sie unter [Registrieren von Azure Stack Hub in Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Beziehen des Tools zur Bereitschaftsüberprüfung (Readiness Checker)

Laden Sie die neueste Version von **AzsReadinessChecker** aus dem [PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) herunter.  

## <a name="install-and-configure"></a>Installieren und konfigurieren

### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

### <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein:

#### <a name="azurerm-powershell-modules"></a>AzureRM PowerShell-Module

Die Az PowerShell-Module müssen installiert sein. Anleitungen finden Sie unter [Installieren des PowerShell AzureRM-Moduls](azure-stack-powershell-install.md).

#### <a name="the-computer-on-which-the-tool-runs"></a>Auf dem Computer, auf dem das Tool ausgeführt wird, muss Folgendes installiert sein:

- Windows 10 oder Windows Server 2016 mit Internetkonnektivität
- PowerShell 5.1 oder höher Um Ihre Version zu überprüfen, führen Sie das folgende PowerShell-Cmdlet aus, und überprüfen Sie dann die **Hauptversion** und die **Nebenversionen**:  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [PowerShell, konfiguriert für Azure Stack Hub](azure-stack-powershell-install.md).
- Die neueste Version des Tools [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).  

#### <a name="azure-active-directory-azure-ad-environment"></a>Azure Active Directory-Umgebung (Azure AD)

- Sie müssen über den Benutzernamen und das Kennwort eines Kontos verfügen, das der Besitzer eines Azure-Abonnements ist, unter dem Sie Azure Stack Hub verwenden.  
- Ermitteln Sie die Abonnement-ID für das Azure-Abonnement, das Sie verwenden möchten.
- Legen Sie die Azure-Umgebung (**AzureEnvironment**) fest, die Sie verwenden möchten. Unterstützte Werte für den Umgebungsnamensparameter sind je nach Ihrem verwendeten Azure-Abonnement **AzureCloud**, **AzureChinaCloud** oder **AzureUSGovernment**.

### <a name="steps-to-validate-the-azure-registration"></a>Schritte zum Überprüfen der Azure-Registrierung

1. Öffnen Sie auf einem Computer, der die Voraussetzungen erfüllt, eine administrative PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl zum Installieren von **AzsReadinessChecker** aus:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um `$registrationCredential` als Abonnementbesitzerkonto festzulegen. Ersetzen Sie `subscriptionowner@contoso.onmicrosoft.com` durch Ihr Konto und den Namen Ihres Mandanten:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Wenn Sie als CSP ein Shared Services- oder IUR-Abonnement nutzen, müssen Sie die Anmeldeinformationen eines Benutzers aus der entsprechenden Azure AD-Instanz angeben. Diese sehen in der Regel etwa wie folgt aus: `subscriptionowner@iurcontoso.onmicrosoft.com`. Dieser Benutzer benötigt die entsprechenden Anmeldeinformationen, wie im vorherigen Schritt beschrieben.

3. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um `$subscriptionID` als zu verwendendes Azure-Abonnement festzulegen. Ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch Ihre eigene Abonnement-ID:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. Führen Sie an der PowerShell-Eingabeaufforderung Folgendes aus, um die Überprüfung Ihres Abonnements zu starten:

   - Legen Sie den Wert für `AzureEnvironment` als **AzureCloud**, **AzureGermanCloud** oder **AzureChinaCloud** fest.  
   - Geben Sie den Namen Ihres Azure AD-Administrators und Ihres Azure AD-Mandanten an.
      ```powershell
      Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
      ```

5. Wenn das Tool ausgeführt wird, überprüfen Sie die Ausgabe. Vergewissern Sie sich, dass der Status für die Anmelde- und Registrierungsanforderungen richtig ist. Eine erfolgreiche Validierungsausgabe sieht in etwa wie folgt aus:

   ```powershell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein:

#### <a name="az-powershell-modules"></a>Az PowerShell-Module

Die Az PowerShell-Module müssen installiert sein. Anleitungen finden Sie unter [Installieren des PowerShell Az-Vorschaumoduls](powershell-install-az-module.md).

#### <a name="azure-active-directory-aad-environment"></a>AAD-Umgebung (Azure Active Directory)

- Sie müssen über den Benutzernamen und das Kennwort eines Kontos verfügen, das der Besitzer eines Azure-Abonnements ist, unter dem Sie Azure Stack Hub verwenden.  
- Ermitteln Sie die Abonnement-ID für das Azure-Abonnement, das Sie verwenden möchten.

### <a name="steps-to-validate-the-azure-registration"></a>Schritte zum Überprüfen der Azure-Registrierung

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Installieren von **AzsReadinessChecker** aus:

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um `$subscriptionID` als zu verwendendes Azure-Abonnement festzulegen. Ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch Ihre eigene Abonnement-ID:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

3. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus: 

   ```powershell
   Connect-AzAccount -subscription $subscriptionID
   ```

4. Führen Sie an der PowerShell-Eingabeaufforderung den folgenden Befehl aus, um die Überprüfung Ihres Abonnements zu starten. Geben Sie den Namen Ihres Azure AD-Administrators und Ihres Azure AD-Mandanten an:

   ```powershell
   Invoke-AzsRegistrationValidation  -RegistrationSubscriptionID $subscriptionID
   ```

5. Wenn das Tool ausgeführt wird, überprüfen Sie die Ausgabe. Vergewissern Sie sich, dass der Status für die Anmelde- und Registrierungsanforderungen richtig ist. Eine erfolgreiche Validierungsausgabe sieht in etwa wie folgt aus:

   ```powershell
   Invoke-AzsRegistrationValidation v1.2005.1269 started.
   Checking Registration Requirements: OK

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```
---

## <a name="report-and-log-file"></a>Berichts- und Protokolldatei

Bei jeder Ausführung einer Überprüfung werden Ergebnisse in den Dateien **AzsReadinessChecker.log** und **AzsReadinessCheckerReport.json** protokolliert. Der Speicherort dieser Dateien wird zusammen mit den Überprüfungsergebnissen in PowerShell angezeigt.

Anhand dieser Dateien können Sie den Überprüfungsstatus freigeben, bevor Sie Azure Stack Hub bereitstellen oder Probleme mit der Überprüfung untersuchen. Die Ergebnisse aller nachfolgenden Überprüfungen werden in beiden Dateien gespeichert. Der Bericht enthält die Bestätigung Ihres Bereitstellungsteams über die Identitätskonfiguration. Mithilfe der Protokolldatei kann Ihr Bereitstellungs- oder Supportteam Probleme bei der Überprüfung untersuchen.

Standardmäßig werden beide Dateien in `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json` geschrieben.  

- Verwenden Sie den Parameter `-OutputPath <path>` am Ende der Ausführungsbefehlszeile, um einen anderen Berichtsspeicherort anzugeben.
- Verwenden Sie den Parameter `-CleanReport` am Ende des Ausführungsbefehls, um Informationen zu früheren Ausführungen des Tools aus **AzsReadinessCheckerReport.json** zu löschen.

Weitere Informationen finden Sie unter [Azure Stack Hub-Überprüfungsbericht](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Fehler bei der Überprüfung

Wenn bei der Überprüfung Fehler auftreten, werden Details zum Fehler im PowerShell-Fenster angezeigt. Das Tool protokolliert Informationen außerdem in der Datei **AzsReadinessChecker.log**.

Die folgenden Beispiele enthalten weitere Informationen zu häufigen Überprüfungsfehlern.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Der Benutzer muss der Besitzer eines Abonnements sein.

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Ursache**: Das Dienstkonto gehört nicht einem Administrator des Azure-Abonnements.

**Lösung**: Verwenden Sie ein Konto, das einem Administrator des Azure-Abonnements gehört und bei dem die Abrechnung für die Nutzung über die Azure Stack Hub-Bereitstellung erfolgt.

### <a name="expired-or-temporary-password"></a>Abgelaufenes oder temporäres Kennwort

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Ursache:** Das Konto kann sich nicht anmelden, da das Kennwort entweder abgelaufen oder temporär ist.

**Lösung**: Führen Sie in PowerShell folgenden Befehl aus, und befolgen Sie die Eingabeaufforderungen zum Zurücksetzen des Kennworts.

```powershell
Login-AzureRMAccount
```

Alternativ können Sie sich beim [Azure-Portal](https://portal.azure.com) als Kontobesitzer anmelden, und der Benutzer wird gezwungen, das Kennwort zu ändern.

### <a name="unknown-user-type"></a>Unbekannter Benutzertyp  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Ursache:** Das Konto kann sich nicht bei der angegebenen Azure AD-Umgebung anmelden. In diesem Beispiel ist **AzureChinaCloud** für **AzureEnvironment** angegeben.  

**Lösung**: Vergewissern Sie sich, dass das Konto für die angegebene Azure-Umgebung gültig ist. Führen Sie in PowerShell folgenden Befehl aus, um zu überprüfen, ob das Konto für eine bestimmte Umgebung gültig ist:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Azure-Identität](azure-stack-validate-identity.md)
- [Anzeigen des Bereitschaftsberichts](azure-stack-validation-report.md)
- [Allgemeine Überlegungen zur Azure Stack Hub-Integration](azure-stack-datacenter-integration.md)
