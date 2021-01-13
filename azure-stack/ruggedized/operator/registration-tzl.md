---
title: Registrieren von Azure Stack Hub bei Azure – Ruggedized
description: Hier erfahren Sie, wie Sie Azure Stack Hub bei Azure registrieren, um Azure Marketplace-Elemente herunterladen und die Datenberichterstellung einrichten zu können.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: 3d24ba3a714c8ea8f17189a7c63a134dab0f8d12
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909802"
---
# <a name="register-azure-stack-hub-with-azure---azure-stack-hub-ruggedized"></a>Registrieren von Azure Stack Hub in Azure: Azure Stack Hub Ruggedized

Zum Einrichten der Marketplace-Syndikation und zum Verwenden von PaaS-Diensten müssen Sie die Modular Data Center-Appliance (MDC) oder Azure Stack Hub Ruggedized-Appliance nach Abschluss der Bereitstellung basierend auf Azure Stack Hub registrieren und aktivieren. Mit Marketplace-Syndikation füllt ein Administrator den lokalen Azure Stack Hub-Marketplace mit aus dem Azure Marketplace heruntergeladenen Images.

Eine Registrierung ist sowohl für Systeme erforderlich, die eine Verbindung mit der Azure-Cloud herstellen, als auch für getrennte Systeme.

## <a name="overview"></a>Übersicht

Der Registrierungsprozess umfasst die folgenden Schritte:

1. Exportieren Sie in Azure Stack Hub ein Registrierungstoken.
2. Registrieren Sie Azure Stack Hub bei Azure.
3. Exportieren Sie in Azure einen Aktivierungsschlüssel.
4. Aktiviert Azure Stack Hub.

Wenn ein System mit der Azure-Cloud verbunden ist, können alle vier Schritte mit einem einzigen Skript ausgeführt werden. Wenn das System getrennt ist, muss der Administrator die Schritte einzeln ausführen.

> [!NOTE]
> Bei einer MDC-Appliance müssen alle Azure Stack Hub-Systeme separat registriert werden. Daher müssen die folgenden Anweisungen in diesem Fall dreimal ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

Vor der Registrierung müssen Sie die folgenden Aktionen ausgeführt haben:

- Überprüfen Ihrer Anmeldeinformationen
- Festlegen des PowerShell-Sprachmodus
- Installieren von PowerShell für Azure Stack Hub
- Installieren des Azure Stack Hub-Toolmoduls
- Bestimmen Ihres Registrierungsszenarios

### <a name="verify-your-credentials"></a>Überprüfen Ihrer Anmeldeinformationen

Vor der Registrierung von Azure Stack Hub in Azure müssen Sie folgende Bedingungen erfüllen:

- Sie müssen über eine Abonnement-ID eines Azure-Abonnements verfügen.  

    > [!NOTE]  
    > Azure-Abonnements sind Azure-Cloudumgebungen zugeordnet (z. B. Azure Commercial oder Azure Government). Dadurch wird festgelegt, mit welcher Cloud Sie für den Zugriff auf Marketplace-Inhalte eine Verbindung herstellen.

- Sie müssen über den Benutzernamen und das Kennwort für ein Konto verfügen, bei dem es sich um einen Besitzer des Abonnements handelt. 
- Das Benutzerkonto muss Zugriff auf das Azure-Abonnement haben und berechtigt sein, Identitäts-Apps und Dienstprinzipale in dem Verzeichnis zu erstellen, das diesem Abonnement zugeordnet ist. Es wird empfohlen, Azure Stack Hub unter Verwendung einer Verwaltung mit geringstmöglichen Berechtigungen bei Azure zu registrieren. Weitere Informationen zum Erstellen einer benutzerdefinierten Rollendefinition, die den Zugriff auf Ihr Abonnement für die Registrierung beschränkt, finden Sie unter [Erstellen einer benutzerdefinierten Rolle für die Azure Stack Hub-Registrierung](../../operator/azure-stack-registration-role.md).
- Registrieren Sie den Azure Stack Hub-Ressourcenanbieter (weitere Informationen in den folgenden Abschnitten).

Nach der Registrierung sind nicht die Berechtigungen eines globalen Administrators in Azure Active Directory (Azure AD) erforderlich. Für einige Vorgänge werden jedoch möglicherweise die Anmeldeinformationen eines globalen Administrators benötigt (etwa für ein Ressourcenanbieter-Installationsskript oder zum Gewähren einer für ein neues Feature erforderlichen Berechtigung). Sie können entweder die Berechtigungen eines globalen Administrators für das Konto vorübergehend wiederherstellen oder ein separates Konto eines globalen Administrators verwenden, das Besitzer des *Standardanbieterabonnements* ist.

Der Benutzer, der Azure Stack Hub registriert, ist der Besitzer des Dienstprinzipals in Azure AD. Die Azure Stack Hub-Registrierung kann nur von dem Benutzer geändert werden, der Azure Stack Hub registriert hat. Versucht ein Benutzer, der über keine Administratorberechtigungen verfügt und kein Besitzer des Dienstprinzipals für die Registrierung ist, Azure Stack Hub zu registrieren oder erneut zu registrieren, wird möglicherweise eine Antwort mit dem Code 403 zurückgegeben. Eine Antwort mit dem Code 403 bedeutet, dass der Benutzer nicht über die für den Vorgang erforderlichen Berechtigungen verfügt.

Durch das Registrieren von Azure Stack Hub fallen keine zusätzlichen Kosten in Ihrem Azure-Abonnement an.

### <a name="powershell-language-mode"></a>PowerShell-Sprachmodus

Zur erfolgreichen Registrierung von Azure Stack Hub muss der PowerShell-Sprachmodus auf **FullLanguageMode** festgelegt werden. Um zu überprüfen, ob der aktuelle Sprachmodus auf vollständig festgelegt ist, öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie die folgenden PowerShell-Cmdlets aus:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vergewissern Sie sich, dass in der Ausgabe **FullLanguageMode** zurückgegeben wird. Wenn ein anderer Sprachmodus zurückgegeben wird, muss die Registrierung auf einem anderen Computer ausgeführt oder der Sprachmodus auf **FullLanguageMode** festgelegt werden, damit Sie fortfahren können.

### <a name="install-powershell-for-azure-stack-hub"></a>Installieren von PowerShell für Azure Stack Hub

Verwenden Sie für die Registrierung bei Azure die neueste Version von PowerShell für Azure Stack Hub.

Sollte die aktuelle Version noch nicht installiert sein, lesen Sie [Installieren des PowerShell AzureRM-Moduls für Azure Stack Hub](../../operator/azure-stack-powershell-install.md).

### <a name="install-the-azure-stack-hub-tools-module"></a>Installieren des Azure Stack Hub-Toolmoduls

Das [Azure Stack Hub-Toolmodul](https://www.powershellgallery.com/packages/azs.tools.admin/0.1.0) enthält PowerShell-Module, die Azure Stack Hub-Funktionen unterstützen (einschließlich der Registrierung). Während des Registrierungsprozesses müssen Sie das Submodul **RegisterWithAzure.psm1** importieren und verwenden, um Ihre Azure Stack Hub-Instanz bei Azure zu registrieren.

So installieren Sie die neuesten Azure Stack Hub-Tools:

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.
2. Führen Sie das folgende Cmdlet aus:

   ```powershell  
   Install-Module -Name Azs.Tools.Admin
   ```

### <a name="determine-your-registration-scenario"></a>Bestimmen Ihres Registrierungsszenarios

Ihre Azure Stack Hub-Bereitstellung kann *verbunden* oder *nicht verbunden* sein.

- **Verbunden**: „Verbunden“ bedeutet, dass Sie die Azure Stack Hub-Instanz so bereitgestellt haben, dass sie eine Verbindung mit dem Internet und mit Azure herstellen kann. Als Identitätsspeicher können Sie entweder Azure AD oder die Active Directory-Verbunddienste (AD FS) verwenden.

- **Getrennt**: Bei der Bereitstellungsoption ohne Verbindung mit Azure können Sie Azure Stack Hub auch ohne Verbindung mit dem Internet bereitstellen und nutzen. Da nicht verbundene Systeme die PaaS-Nutzung nicht an Azure zurückmelden können, müssen sie mit dem **kapazitätsbasierten** Abrechnungsmodell registriert werden, um PaaS-Dienste verwenden zu können.

### <a name="determine-a-unique-registration-name-to-use"></a>Bestimmen eines eindeutigen zu verwendenden Registrierungsnamens

Wenn Sie Azure Stack Hub in Azure registrieren, müssen Sie einen eindeutige Registrierungsnamen angeben. Eine einfache Möglichkeit, Ihr Azure Stack Hub-Abonnement mit einer Azure-Registrierung zu verknüpfen, ist die Verwendung Ihrer **Cloud-ID** von Azure Stack Hub.

Um die Cloud-ID für Ihre Azure Stack Hub-Bereitstellung zu bestimmen, öffnen Sie PowerShell als Administrator auf einem Computer, der auf den privilegierten Endpunkt zugreifen kann, führen Sie die folgenden Befehle aus, und erfassen Sie anschließend den Wert für **CloudID**:

```PowerShell
Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Get-AzureStackStampInformation
```

## <a name="registration-and-activation-for-systems-connected-to-the-azure-cloud"></a>Registrierung und Aktivierung für mit der Azure-Cloud verbundene Systeme

Führen Sie die folgenden Schritte aus, um ein Azure Stack Hub-System zu registrieren, das über Konnektivität mit Azure verfügt.

> [!NOTE]  
> Alle diese Schritte müssen von einem Computer ausgeführt werden, der Zugriff auf den privilegierten Endpunkt (PEP) hat. Details zum PEP finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).

In mit Azure verbundenen Umgebungen kann auf das Internet und Azure zugegriffen werden. Registrieren Sie für diese Umgebungen den Azure Stack Hub-Ressourcenanbieter bei Azure, und konfigurieren Sie dann Ihr Abrechnungsmodell.

1. Zum Registrieren des Azure Stack Hub-Ressourcenanbieters bei Azure starten Sie die PowerShell ISE als Administrator und verwenden die folgenden PowerShell-Cmdlets mit dem auf den entsprechenden Azure-Abonnementtyp festgelegten Parameter **EnvironmentName** (Parameter siehe unten).

2. Stellen Sie in derselben PowerShell-Sitzung sicher, dass Sie beim richtigen Azure PowerShell-Kontext angemeldet sind. Bei diesem Kontext handelt es sich um das Azure-Konto, das zuvor zum Registrieren des Azure Stack Hub-Ressourcenanbieters verwendet wurde:

   ```powershell
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   Für **AzureUSSec** müssen Sie zunächst die `CustomCloud`-Umgebung initialisieren und dann **Connect-AzureRmAccount** aufrufen:

   ```powershell
   Initialize-AzureRmEnvironment -Name 'CustomCloud' -CloudManifestFilePath $CloudManifestFilePath
   Connect-AzureRmAccount -Environment 'CustomCloud'
   ```

   | Parameter | BESCHREIBUNG |  
   |-----|-----|
   | EnvironmentName | Der Umgebungsname des Azure-Cloudabonnements. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** und **AzureUSSec**.   |

   > [!NOTE]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie zu einem anderen Konto wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit **Add-AzureRmAccount** anmelden: **Remove-AzureRmAccount-Scope Process**.

3. Wenn Sie über mehrere Abonnements verfügen, führen Sie den folgenden Befehl aus, um das zu verwendende Abonnement auszuwählen:

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Führen Sie den folgenden Befehl zum Registrieren des Azure Stack Hub-Ressourcenanbieters bei Ihrem Azure-Abonnement aus:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

   Alternativ können Sie den Azure Stack Hub-Ressourcenanbieter über das Azure-Portal bei Ihrem Azure-Abonnement registrieren. Melden Sie sich mit dem Konto beim Azure-Portal an, das dem Azure-Abonnement zugeordnet ist. Wechseln Sie zu **Kategorien** > **Allgemein** > **Abonnements**, und klicken Sie auf die ID des Abonnements, bei dem Sie den Azure Stack Hub-Ressourcenanbieter registrieren müssen. Navigieren Sie im linken Bereich zu **Einstellungen** > **Ressourcenanbieter**. Wählen Sie den Ressourcenanbieter **Microsoft.AzureStack** aus, und klicken Sie auf **Registrieren**.

   ![Registrieren des Azure Stack Hub-Ressourcenanbieters](./media/registration-tzl/register-azure-resource-provider-portal.png)

5. Führen Sie in derselben PowerShell-Sitzung das Cmdlet **Set-AzsRegistration** aus:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $msAssetTag = "Enter the value printed on the product"
   $RegistrationName = "<unique-registration-name>"

   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Ruggedized `
      -RegistrationName $RegistrationName `
      -msAssetTag $msAssetTagName `
      -UsageReportingEnabled: $false
   ```

   Das MS-Bestandskennzeichen (`msAssetTag`) ist bei der Registrierung mit dem Ruggedized-Abrechnungsmodell obligatorisch und auf dem Produkt aufgedruckt.

   Der Vorgang dauert zwischen 10 und 15 Minuten. Nach dem Ausführen des Befehls wird die folgende Meldung angezeigt: **Ihre Umgebung ist nun registriert und mit den angegebenen Parametern aktiviert.**

## <a name="registration-and-activation-for-systems-not-connected-to-the-azure-cloud"></a>Registrierung und Aktivierung für nicht mit der Azure-Cloud verbundene Systeme 

Wenn Sie Azure Stack Hub in einer getrennten Umgebung registrieren (ohne Internetverbindung), führen Sie die folgenden Schritte aus:

1. Rufen Sie ein Registrierungstoken aus der Azure Stack Hub-Umgebung ab.

2. Stellen Sie eine Verbindung mit Azure her, und registrieren Sie sich mit dem Registrierungstoken aus Schritt 1. Verwenden Sie hierfür einen Computer, der eine Verbindung mit Azure herstellen kann und auf dem PowerShell für Azure Stack Hub installiert ist.

3. Rufen Sie einen Aktivierungsschlüssel aus der Azure-Registrierung ab.

4. Aktivieren Sie Azure Stack Hub mit dem Aktivierungsschlüssel aus Azure.

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Abrufen eines Registrierungstokens aus der Azure Stack Hub-Umgebung

Rufen Sie ein Registrierungstoken aus der Azure Stack Hub-Umgebung ab. Verwenden Sie dieses Token dann auf einem Computer, der eine Verbindung mit Azure herstellen kann und auf dem PowerShell für Azure Stack Hub und die Azure Stack Hub-Tools installiert sind.

1. Führen Sie zum Abrufen des Registrierungstokens die folgenden PowerShell-Cmdlets in einer Eingabeaufforderung mit erhöhten Rechten aus:

   ```PowerShell
    $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt" 
    
    $RegistrationToken = Get-AzsRegistrationToken `
      -PrivilegedEndpointCredential $YourCloudAdminCredential `
      -UsageReportingEnabled:$False `
      -PrivilegedEndpoint $YourPrivilegedEndpoint `
      -BillingModel Capacity -AgreementNumber '<EA agreement number>' -msAssetTag '<MS Asset tag>' `
      -TokenOutputFilePath $FilePathForRegistrationToken 
   ```

   > [!TIP]  
   > Das Registrierungstoken wird in der für `$FilePathForRegistrationToken` angegebenen Datei gespeichert. Sie können den Pfad oder den Dateinamen nach Bedarf ändern.

2. Speichern Sie dieses Registrierungstoken für die Verwendung auf dem mit Azure verbundenen Computer. Sie können die Datei oder den Text aus `$FilePathForRegistrationToken` kopieren.

### <a name="connect-to-azure-and-register"></a>Verbinden mit Azure und Registrieren

Melden Sie sich auf dem Computer, der mit dem Internet verbunden ist, beim richtigen Azure PowerShell-Kontext an. Rufen Sie dann **Register-AzsEnvironment** auf. Geben Sie das Registrierungstoken für die Registrierung bei Azure an. Wenn Sie mehrere Instanzen von Azure Stack Hub mit derselben Azure-Abonnement-ID registrieren, legen Sie einen eindeutigen Namen für die Registrierung fest.

Sie benötigen Ihr Registrierungstoken und einen eindeutigen Tokennamen.

1. Führen Sie in einer PowerShell-Eingabeaufforderung mit erhöhten Rechten die folgenden Cmdlets aus, um sicherzustellen, dass Sie das richtige Abonnement verwenden.

    ```powershell  
    Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
    ```

2. Führen Sie anschließend die folgenden PowerShell-Cmdlets aus:

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

3. Optional können Sie mit dem Cmdlet **Get-Content** auf eine Datei verweisen, die Ihr Registrierungstoken enthält.

   Sie benötigen Ihr Registrierungstoken und einen eindeutigen Tokennamen.

   1. Starten Sie PowerShell ISE als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-master**, das beim Herunterladen der Azure Stack Hub-Tools erstellt wurde. Importieren Sie das Modul **RegisterWithAzure.psm1**:

      ```powershell  
      Import-Module .\RegisterWithAzure.psm1
      ```

   2. Führen Sie anschließend die folgenden PowerShell-Cmdlets aus:

      ```powershell  
      $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
      Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
      ```

> [!NOTE]  
> Speichern Sie den Namen der Registrierungsressource und das Registrierungstoken zur späteren Bezugnahme.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Abrufen eines Aktivierungsschlüssels aus der Azure-Registrierungsressource

Rufen Sie als Nächstes aus der beim Ausführen von **Register-AzsEnvironment** in Azure erstellten Registrierungsressource einen Aktivierungsschlüssel ab.

Führen Sie die folgenden PowerShell-Cmdlets zum Abrufen des Aktivierungsschlüssels aus:

```PowerShell
$RegistrationResourceName = "<unique-registration-name>"
$KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
$ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
```

> [!TIP]  
> Der Aktivierungsschlüssel wird in der für `$KeyOutputFilePath` angegebenen Datei gespeichert. Sie können den Pfad oder den Dateinamen nach Bedarf ändern.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Erstellen einer Aktivierungsressource in Azure Stack Hub

Kehren Sie mit der Datei oder dem Text des Aktivierungsschlüssels, der von **Get-AzsActivationKey** erstellt wurde, zur Azure Stack Hub-Umgebung zurück. Erstellen Sie als Nächstes in Azure Stack Hub mit diesem Aktivierungsschlüssel eine Aktivierungsressource. Führen Sie die folgenden PowerShell-Cmdlets zum Erstellen einer Aktivierungsressource aus:

```PowerShell
$ActivationKey = "<activation key>"
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

Optional können Sie mit dem Cmdlet **Get-Content** auf eine Datei verweisen, die Ihr Registrierungstoken enthält:

```PowerShell
$ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

## <a name="verify-azure-stack-hub-registration"></a>Überprüfen der Azure Stack Hub-Registrierung

Sie können die Kachel **Regionsverwaltung** verwenden, um zu überprüfen, ob die Azure Stack Hub-Registrierung erfolgreich war. Diese Kachel steht im Standarddashboard im Administratorportal zur Verfügung. Der Status kann „registriert“ oder „nicht registriert“ lauten. Beim Status „registriert“ wird außerdem die ID des Azure-Abonnements angezeigt, mit dem Sie Ihre Azure Stack Hub-Instanz registriert haben, sowie die Gruppe und der Name der Registrierungsressource.

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an. Die URL variiert je nach Region und dem externen Domänennamen des Betreibers und besitzt das Format `https://adminportal.<region>.<FQDN>`.

2. Wählen Sie im Dashboard **Regionsverwaltung** aus.

3. Wählen Sie **Eigenschaften** aus. Auf diesem Blatt werden der Status und Details Ihrer Umgebung angezeigt. Der Status kann **Registriert**, **Nicht registriert** oder **Abgelaufen** sein.

   [![Kachel „Regionsverwaltung“ im Azure Stack Hub-Administratorportal](media/registration-tzl/admin1sm.png "Kachel „Regionsverwaltung“")](media/registration-tzl/admin1.png#lightbox)

   Wenn Sie die Registrierung durchgeführt haben, finden Sie folgende Eigenschaften vor:

    - **Abonnement-ID für die Registrierung**: Die bei Azure Stack Hub registrierte und mit Azure Stack Hub verknüpfte Azure-Abonnement-ID.
    - **Ressourcengruppe für die Registrierung**: Die Azure-Ressourcengruppe im zugeordneten Abonnement mit den Azure Stack Hub-Ressourcen.

4. Sie können das Azure-Portal verwenden, um Azure Stack Hub-Registrierungsressourcen anzuzeigen, und dann überprüfen, ob die Registrierung erfolgreich war. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Konto an, das dem Abonnement zugeordnet ist, mit dem Sie die Registrierung von Azure Stack Hub durchgeführt haben. Klicken Sie auf **Alle Ressourcen**, aktivieren Sie das Kontrollkästchen bei **Ausgeblendete Typen anzeigen**, und klicken Sie dann auf den Registrierungsnamen.

5. Wenn die Registrierung nicht erfolgreich war, müssen Sie die Registrierung erneut durchführen, indem Sie [das verwendete Abonnement ändern](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration#change-the-subscription-you-use), um das Problem zu beheben.

Alternativ dazu können Sie mit dem Marketplace-Verwaltungsfeature überprüfen, ob Ihre Registrierung erfolgreich war. Wenn auf dem **Marketplace-Verwaltungsblatt** eine Liste mit Marketplace-Elementen angezeigt wird, war Ihre Registrierung erfolgreich. In getrennten Umgebungen werden bei der Marketplace-Verwaltung allerdings keine Marketplace-Elemente angezeigt.

> [!NOTE]
> Nach Abschluss der Registrierung wird die aktive Warnung wegen noch nicht erfolgter Registrierung nicht mehr angezeigt.

## <a name="next-steps"></a>Nächste Schritte

[Grundlagen zur Verwaltung von Azure Stack Hub](../../operator/azure-stack-manage-basics.md)  
