---
title: Registrieren von Azure Stack in Azure
titleSuffix: Azure Stack
description: Hier erfahren Sie, wie Sie integrierte Azure Stack-Systeme bei Azure registrieren, um Azure Marketplace-Elemente herunterladen und die Datenberichterstellung einrichten zu können.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: mabrigg
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 1ab0e33d894b1856e1769a0f6780f5d0b9df3474
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780761"
---
# <a name="register-azure-stack-with-azure"></a>Registrieren von Azure Stack in Azure

Registrieren Sie Azure Stack bei Azure, um Azure Marketplace-Elemente aus Azure herunterladen und die Übermittlung von Geschäftsdatenberichten an Microsoft einrichten zu können. Nach der Registrierung von Azure Stack wird die Nutzung an Azure Commerce gemeldet und kann unter der für die Registrierung verwendeten Azure-Abrechnungsabonnement-ID angezeigt werden.

In diesem Artikel wird das Registrieren von in Azure Stack integrierten Systemen bei Azure beschrieben. Informationen zum Registrieren des ASDK bei Azure finden Sie in der ASDK-Dokumentation unter [Azure Stack-Registrierung](../asdk/asdk-register.md).

> [!IMPORTANT]  
> Die Registrierung ist für die uneingeschränkte Unterstützung aller Azure Stack-Funktionen einschließlich des Angebots von Elementen im Marketplace erforderlich. Sie verstoßen gegen die Lizenzbedingungen von Azure Stack, wenn Sie das nutzungsbasierte Abrechnungsmodell verwenden, ohne sich zu registrieren. Weitere Informationen zu den Azure Stack-Lizenzierungsmodellen finden Sie auf der Seite [Azure Stack erwerben](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Voraussetzungen

Vor der Registrierung müssen Sie folgende Voraussetzungen erfüllt haben:

- Überprüfen Ihrer Anmeldeinformationen
- Festlegen des PowerShell-Sprachmodus
- Installieren von PowerShell für Azure Stack
- Herunterladen der Azure Stack-Tools
- Bestimmen Ihres Registrierungsszenarios

### <a name="verify-your-credentials"></a>Überprüfen Ihrer Anmeldeinformationen

Vor der Registrierung von Azure Stack in Azure müssen Sie folgende Bedingungen erfüllen:

- Sie müssen über eine Abonnement-ID eines Azure-Abonnements verfügen. Nur Abonnements freigegebener EA-, CSP- oder CSP-Dienste werden für die Registrierung unterstützt. CSPs müssen entscheiden, ob ein [CSP- oder APSS-Abonnement verwendet werden soll](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Um die ID abzurufen, melden Sie sich bei Azure an, und klicken Sie auf **Alle Dienste**. Wählen Sie dann unter der Kategorie **ALLGEMEIN** die Option **Abonnements** aus, und klicken Sie auf das gewünschte Abonnement. Unter **Zusammenfassung** finden Sie die Abonnement-ID. Verwenden Sie als bewährte Methode separate Abonnements für Produktions- und Entwicklungs-/Testumgebungen. 

  > [!Note]  
  > Cloudabonnements für Deutschland werden derzeit nicht unterstützt.

- Sie müssen über den Benutzernamen und das Kennwort für ein Konto verfügen, bei dem es sich um einen Besitzer des Abonnements handelt.

- Das Benutzerkonto muss Zugriff auf das Azure-Abonnement haben und berechtigt sein, Identitäts-Apps und Dienstprinzipale in dem Verzeichnis zu erstellen, das diesem Abonnement zugeordnet ist. Es wird empfohlen, Azure Stack unter Verwendung einer Verwaltung mit geringstmöglichen Berechtigungen bei Azure zu registrieren. Weitere Informationen zum Erstellen einer benutzerdefinierten Rollendefinition, die den Zugriff auf Ihr Abonnement für die Registrierung einschränkt, finden Sie unter [Erstellen einer Registrierungsrolle für Azure Stack](azure-stack-registration-role.md).

- Sie müssen den Azure Stack-Ressourcenanbieter registriert haben. (Weitere Informationen finden Sie im folgenden Abschnitt „Registrieren des Azure Stack-Ressourcenanbieters“.)

Nach der Registrierung wird keine globale Azure AD-Administratorberechtigung (Azure Active Directory) benötigt. Für einige Vorgänge werden aber ggf. die Anmeldeinformationen des globalen Administrators benötigt (etwa für ein Ressourcenanbieter-Installationsskript oder zum Gewähren einer Berechtigung für ein neues Feature). Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.

Der Benutzer, der Azure Stack registriert, ist der Besitzer des Dienstprinzipals in Azure AD. Die Azure Stack-Registrierung kann nur von dem Benutzer geändert werden, der Azure Stack registriert hat. Versucht ein Benutzer, der über keine Administratorrechte verfügt und nicht der Besitzer des Dienstprinzipals für die Registrierung ist, Azure Stack zu registrieren oder erneut zu registrieren, wird möglicherweise eine Antwort mit dem Code 403 zurückgegeben. Eine Antwort mit dem Code 403 bedeutet, dass der Benutzer nicht über die erforderlichen Berechtigungen für den Vorgang verfügt.

Wenn Ihr Azure-Abonnement diese Anforderungen nicht erfüllt, können Sie [hier kostenlos ein neues Azure-Konto erstellen](https://azure.microsoft.com/free/?b=17.06). Durch das Registrieren von Azure Stack fallen keine zusätzlichen Kosten in Ihrem Azure-Abonnement an.

> [!NOTE]
> Wenn Sie mehrere Azure Stack-Instanzen verwenden, ist es empfehlenswert, jede Azure Stack-Instanz in einem eigenen Abonnement zu registrieren. So können Sie die Nutzung besser nachverfolgen.

### <a name="powershell-language-mode"></a>PowerShell-Sprachmodus

Zur erfolgreichen Registrierung von Azure Stack muss der PowerShell-Sprachmodus auf **FullLanguageMode** festgelegt werden.  Um zu überprüfen, ob der aktuelle Sprachmodus auf vollständig festgelegt ist, öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie die folgenden PowerShell-Cmdlets aus:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vergewissern Sie sich, dass in der Ausgabe **FullLanguageMode** zurückgegeben wird. Wenn ein anderer Sprachmodus zurückgegeben wird, muss die Registrierung auf einem anderen Computer ausgeführt oder der Sprachmodus auf **FullLanguageMode** festgelegt werden, damit Sie fortfahren können.

### <a name="install-powershell-for-azure-stack"></a>Installieren von PowerShell für Azure Stack

Verwenden Sie für die Registrierung bei Azure die neueste Version von PowerShell für Azure Stack.

Sollte die aktuelle Version noch nicht installiert sein, lesen Sie [Installieren von PowerShell für Azure Stack](azure-stack-powershell-install.md).

### <a name="download-the-azure-stack-tools"></a>Herunterladen der Azure Stack-Tools

Das GitHub-Repository mit den Azure Stack-Tools enthält PowerShell-Module, die Azure Stack-Funktionen unterstützen (einschließlich der Registrierung). Während des Registrierungsprozesses müssen Sie das PowerShell-Modul **RegisterWithAzure.psm1** aus dem Repository mit den Azure Stack-Tools importieren und verwenden, um Ihre Azure Stack-Instanz bei Azure zu registrieren.

Löschen Sie vor der Registrierung bei Azure alle vorhandenen Versionen der Azure Stack-Tools, und laden Sie die [neueste Version von GitHub](azure-stack-powershell-download.md) herunter, um sicherzustellen, dass Sie die neueste Version verwenden.

### <a name="determine-your-registration-scenario"></a>Bestimmen Ihres Registrierungsszenarios

Ihre Azure Stack-Bereitstellung kann *verbunden* oder *nicht verbunden* sein.

- **Verbunden**  
 „Verbunden“ bedeutet, dass Sie die Azure Stack-Instanz so bereitgestellt haben, dass sie eine Verbindung mit dem Internet und mit Azure herstellen kann. Als Identitätsspeicher wird entweder Azure AD oder Active Directory-Verbunddienste (AD FS) verwendet. Bei einer verbundenen Bereitstellung können Sie zwischen zwei Abrechnungsmodellen wählen: nutzungsbasiert oder kapazitätsbasiert.
  - [Registrieren einer verbundenen Azure Stack-Instanz bei Azure unter Verwendung des **nutzungsbasierten** Abrechnungsmodells](#register-connected-with-pay-as-you-go-billing)
  - [Registrieren einer verbundenen Azure Stack-Instanz bei Azure unter Verwendung des **kapazitätsbasierten** Abrechnungsmodells](#register-connected-with-capacity-billing)

- **Nicht verbunden**  
 Bei der Bereitstellungsoption ohne Verbindung mit Azure können Sie Azure Stack auch ohne Verbindung mit dem Internet bereitstellen und nutzen. Nicht mit Azure verbundene Bereitstellungen sind jedoch auf einen AD FS-Identitätsspeicher und das kapazitätsbasierte Abrechnungsmodell beschränkt.
  - [Registrieren einer nicht verbundenen Azure Stack-Instanz unter Verwendung des **kapazitätsbasierten** Abrechnungsmodells](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Bestimmen eines eindeutigen zu verwendenden Registrierungsnamens

Wenn Sie Azure Stack in Azure registrieren, müssen Sie einen eindeutige Registrierungsnamen angeben. Eine einfache Möglichkeit, Ihr Azure Stack-Abonnement mit einer Azure-Registrierung zu verknüpfen, ist die Verwendung der **Cloud-ID** Ihres Azure Stack.

> [!NOTE]
> Azure Stack-Registrierungen, die das kapazitätsbasierte Abrechnungsmodell verwenden, müssen den eindeutigen Namen ändern, wenn sie sich nach Ablauf dieser jährlichen Abonnements erneut registrieren, es sei denn, Sie [löschen die abgelaufene Registrierung](azure-stack-registration.md#change-the-subscription-you-use) und führen die Registrierung bei Azure erneut aus.

Um die Cloud-ID für Ihre Azure Stack-Bereitstellung zu bestimmen, öffnen Sie PowerShell als Administrator auf einem Computer, der auf den privilegierten Endpunkt zugreifen kann, führen Sie die folgenden Befehle aus, und erfassen Sie anschließend den Wert für **CloudID**:

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registrieren einer verbundenen Instanz mit nutzungsbasierter Abrechnung

Befolgen Sie diese Schritte zum Registrieren von Azure Stack bei Azure unter Verwendung des nutzungsbasierten Abrechnungsmodells.

> [!Note]  
> Alle diese Schritte müssen von einem Computer ausgeführt werden, der Zugriff auf den privilegierten Endpunkt (PEP) hat. Details zum PEP finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

In mit Azure verbundenen Umgebungen kann auf das Internet und Azure zugegriffen werden. Für diese Umgebungen müssen Sie den Azure Stack-Ressourcenanbieter bei Azure registrieren und dann Ihr Abrechnungsmodell konfigurieren.

1. Zum Registrieren des Azure Stack-Ressourcenanbieters bei Azure starten Sie PowerShell ISE als Administrator, und verwenden Sie die folgenden PowerShell-Cmdlets mit dem auf den entsprechenden Azure-Abonnementtyp festgelegten Parameter **EnvironmentName** (siehe die Parameter unten).

2. Fügen Sie das Azure-Konto hinzu, das für die Registrierung von Azure Stack verwendet wurde. Führen Sie zum Hinzufügen des Kontos das Cmdlet **Add-AzureRmAccount** aus. Sie werden aufgefordert, Ihre Anmeldeinformationen für das Azure-Konto einzugeben. Je nach Konfiguration Ihres Kontos müssen Sie möglicherweise die zweistufige Authentifizierung verwenden.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parameter | BESCHREIBUNG |  
   |-----|-----|
   | EnvironmentName | Der Umgebungsname des Azure-Cloudabonnements. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein Azure-Abonnement für China **AzureChinaCloud**.  |

   >[!Note]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie lediglich Konten wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit Add-AzureRmAccount anmelden: `Remove-AzureRmAccount-Scope Process`

3. Wenn Sie über mehrere Abonnements verfügen, führen Sie den folgenden Befehl aus, um das zu verwendende Abonnement auszuwählen:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Führen Sie den folgenden Befehl zum Registrieren des Azure Stack-Ressourcenanbieters in Ihrem Azure-Abonnement aus:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Starten Sie PowerShell ISE als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-master**, das beim Herunterladen der Azure Stack-Tools erstellt wurde. Importieren Sie das Modul **RegisterWithAzure.psm1** mit PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Stellen Sie als Nächstes in der gleichen PowerShell-Sitzung sicher, dass Sie im richtigen Azure PowerShell-Kontext angemeldet sind. Bei diesem Kontext handelt es sich um das Azure-Konto, das zuvor verwendet wurde, um den Azure Stack-Ressourcenanbieter zu registrieren. Führen Sie diesen PowerShell-Befehl aus:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parameter | BESCHREIBUNG |  
   |-----|-----|
   | EnvironmentName | Der Umgebungsname des Azure-Cloudabonnements. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein Azure-Abonnement für China **AzureChinaCloud**.  |

7. Führen Sie in der gleichen PowerShell-Sitzung das Cmdlet **Set-AzsRegistration** aus. Führen Sie diesen PowerShell-Befehl aus:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Weitere Informationen zum Cmdlet „Set-AzsRegistration“ finden Sie in der [Referenz zur Registrierung](#registration-reference).

   Der Vorgang dauert zwischen 10 und 15 Minuten. Wenn der Befehl abgeschlossen ist, sehen Sie die Meldung, dass **Ihre Umgebung nun registriert und mit den angegebenen Parametern aktiviert wurde**.

## <a name="register-connected-with-capacity-billing"></a>Registrieren einer verbundenen Instanz mit kapazitätsbasierter Abrechnung

Befolgen Sie diese Schritte zum Registrieren von Azure Stack bei Azure unter Verwendung des nutzungsbasierten Abrechnungsmodells.

> [!Note]  
> Alle diese Schritte müssen von einem Computer ausgeführt werden, der Zugriff auf den privilegierten Endpunkt (PEP) hat. Details zum PEP finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

In mit Azure verbundenen Umgebungen kann auf das Internet und Azure zugegriffen werden. Für diese Umgebungen müssen Sie den Azure Stack-Ressourcenanbieter bei Azure registrieren und dann Ihr Abrechnungsmodell konfigurieren.

1. Zum Registrieren des Azure Stack-Ressourcenanbieters bei Azure starten Sie PowerShell ISE als Administrator, und verwenden Sie die folgenden PowerShell-Cmdlets mit dem auf den entsprechenden Azure-Abonnementtyp festgelegten Parameter **EnvironmentName** (siehe die Parameter unten).

2. Fügen Sie das Azure-Konto hinzu, das für die Registrierung von Azure Stack verwendet wurde. Führen Sie zum Hinzufügen des Kontos das Cmdlet **Add-AzureRmAccount** aus. Sie werden aufgefordert, Ihre Anmeldeinformationen für das Azure-Konto einzugeben. Je nach Konfiguration Ihres Kontos müssen Sie möglicherweise die zweistufige Authentifizierung verwenden.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parameter | BESCHREIBUNG |  
   |-----|-----|
   | EnvironmentName | Der Umgebungsname des Azure-Cloudabonnements. Unterstützte Umgebungsnamen sind **AzureCloud**, **AzureUSGovernment** oder für ein Azure-Abonnement für China **AzureChinaCloud**.  |

3. Wenn Sie über mehrere Abonnements verfügen, führen Sie den folgenden Befehl aus, um das zu verwendende Abonnement auszuwählen:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Führen Sie den folgenden Befehl zum Registrieren des Azure Stack-Ressourcenanbieters in Ihrem Azure-Abonnement aus:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Starten Sie PowerShell ISE als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-master**, das beim Herunterladen der Azure Stack-Tools erstellt wurde. Importieren Sie das Modul **RegisterWithAzure.psm1** mit PowerShell:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Sie können Nutzungsberichte mit dem UsageReportingEnabled-Parameter für das Cmdlet **Set-AzsRegistration** deaktivieren, indem Sie den Parameter auf FALSE festlegen. 
   
   Weitere Informationen zum Cmdlet „Set-AzsRegistration“ finden Sie in der [Referenz zur Registrierung](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrieren einer nicht verbundenen Instanz mit kapazitätsbasierter Abrechnung

Wenn Sie Azure Stack in einer nicht verbundenen Umgebung (ohne Internetverbindung) registrieren, müssen Sie ein Registrierungstoken aus der Azure Stack-Umgebung abrufen. Dieses Token muss dann auf einem Computer verwendet werden, der eine Verbindung mit Azure herstellen kann und auf dem PowerShell für Azure Stack installiert ist.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Abrufen eines Registrierungstokens aus der Azure Stack-Umgebung

1. Starten Sie PowerShell ISE als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-master**, das beim Herunterladen der Azure Stack-Tools erstellt wurde. Importieren Sie das Modul **RegisterWithAzure.psm1**:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Führen Sie die folgenden PowerShell-Cmdlets zum Abrufen des Registrierungstokens aus:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Weitere Informationen zum Cmdlet „Get-AzsRegistrationToken“ finden Sie in der [Referenz zur Registrierung](#registration-reference).

   > [!Tip]  
   > Das Registrierungstoken wird in der Datei gespeichert, die für *$FilePathForRegistrationToken* angegeben ist. Sie können den Dateipfad oder den Dateinamen nach eigenem Ermessen ändern.

3. Speichern Sie dieses Registrierungstoken für die Verwendung auf dem mit Azure verbundenen Computer. Sie können die Datei oder den Text aus *$FilePathForRegistrationToken* kopieren.

### <a name="connect-to-azure-and-register"></a>Verbinden mit Azure und Registrieren

Führen Sie auf dem Computer, der mit dem Internet verbunden ist, die gleichen Schritte aus, um das Modul „RegisterWithAzure.psm1“ zu importieren und sich im richtigen Azure PowerShell-Kontext anzumelden. Rufen Sie dann „Register-AzsEnvironment“ auf. Geben Sie das Registrierungstoken für die Registrierung bei Azure an. Wenn Sie mehrere Instanzen von Azure Stack mit der gleichen Azure-Abonnement-ID registrieren, legen Sie einen eindeutigen Namen für die Registrierung fest.

Sie benötigen Ihr Registrierungstoken und einen eindeutigen Tokennamen.

1. Starten Sie PowerShell ISE als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-master**, das beim Herunterladen der Azure Stack-Tools erstellt wurde. Importieren Sie das Modul **RegisterWithAzure.psm1**:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Führen Sie anschließend die folgenden PowerShell-Cmdlets aus:  

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

Optional können Sie mit dem Cmdlet „Get-Content“ auf eine Datei verweisen, die Ihr Registrierungstoken enthält.

Sie benötigen Ihr Registrierungstoken und einen eindeutigen Tokennamen.

1. Starten Sie PowerShell ISE als Administrator, und navigieren Sie zum Ordner **Registration** im Verzeichnis **AzureStack-Tools-master**, das beim Herunterladen der Azure Stack-Tools erstellt wurde. Importieren Sie das Modul **RegisterWithAzure.psm1**:  

    ```powershell  
    Import-Module .\RegisterWithAzure.psm1
    ```

2. Führen Sie anschließend die folgenden PowerShell-Cmdlets aus:  

    ```powershell  
    $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

  > [!Note]  
  > Speichern Sie den Namen der Registrierungsressource und das Registrierungstoken zur späteren Bezugnahme.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Abrufen eines Aktivierungsschlüssels aus der Azure-Registrierungsressource

Als Nächstes müssen Sie einen Aktivierungsschlüssel aus der Registrierungsressource abrufen, die in Azure während der Ausführung von „Register-AzsEnvironment“ erstellt wurde.

Führen Sie die folgenden PowerShell-Cmdlets zum Abrufen des Aktivierungsschlüssels aus:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Der Aktivierungsschlüssel wird in der für *$KeyOutputFilePath* angegebenen Datei gespeichert. Sie können den Dateipfad oder den Dateinamen nach eigenem Ermessen ändern.

### <a name="create-an-activation-resource-in-azure-stack"></a>Erstellen einer Aktivierungsressource in Azure Stack

Kehren Sie mit der Datei oder dem Text aus dem Aktivierungsschlüssel, der von „Get-AzsActivationKey“ erstellt wurde, zur Azure Stack-Umgebung zurück. Erstellen Sie als Nächstes in Azure Stack eine Aktivierungsressource mit diesem Aktivierungsschlüssel. Führen Sie die folgenden PowerShell-Cmdlets zum Erstellen einer Aktivierungsressource aus:

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Optional können Sie mit dem Cmdlet „Get-Content“ auf eine Datei zeigen, die Ihr Registrierungstoken enthält:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Überprüfen der Azure Stack-Registrierung

Sie können die Kachel **Regionsverwaltung** verwenden, um zu überprüfen, ob die Azure Stack-Registrierung erfolgreich war. Diese Kachel steht im Standarddashboard im Administratorportal zur Verfügung. Der Status kann „registriert“ oder „nicht registriert“ lauten. Beim Status „registriert“ wird außerdem die ID des Azure-Abonnements angezeigt, mit dem Sie Ihren Azure Stack registriert haben, sowie die Gruppe und der Name der Registrierungsressource.

1. Melden Sie sich beim [Azure Stack-Administratorportal](https://adminportal.local.azurestack.external) an.

2. Wählen Sie im Dashboard **Regionsverwaltung** aus.

3. Wählen Sie **Eigenschaften** aus. Auf diesem Blatt werden der Status und Details Ihrer Umgebung angezeigt. Der Status kann **Registriert**, **Nicht registriert** oder **Abgelaufen** sein.

    [![Kachel „Regionsverwaltung“ im Azure Stack-Administratorportal](media/azure-stack-registration/admin1sm.png "Kachel „Regionsverwaltung“")](media/azure-stack-registration/admin1.png#lightbox)

    Wenn Sie die Registrierung durchgeführt haben, finden Sie folgende Eigenschaften vor:
    
    - **Abonnement-ID für die Registrierung**: Die bei Azure Stack registrierte und mit Azure Stack verknüpfte Azure-Abonnement-ID.
    - **Ressourcengruppe für die Registrierung**: Die Azure-Ressourcengruppe im zugeordneten Abonnement mit den Azure Stack-Ressourcen.

4. Sie können das Azure-Portal verwenden, um Azure Stack-Registrierungsressourcen anzuzeigen, und dann überprüfen, ob die Registrierung erfolgreich war. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Abonnement zugeordnet ist, mit dem Sie die Registrierung von Azure Stack durchgeführt haben. Wählen Sie **Alle Ressourcen** aus, aktivieren Sie das Kontrollkästchen **Ausgeblendete Typen anzeigen**, und wählen Sie den Registrierungsnamen aus.

5. War die Registrierung nicht erfolgreich, müssen Sie die Registrierung wiederholen und dabei die [hier beschriebenen Schritte](#change-the-subscription-you-use) zur Behebung des Problems ausführen.  

Alternativ dazu können Sie mit dem Marketplace-Verwaltungsfeature überprüfen, ob Ihre Registrierung erfolgreich war. Wenn auf dem Marketplace-Verwaltungsblatt eine Liste mit Marketplace-Elementen angezeigt wird, war Ihre Registrierung erfolgreich. In nicht verbundenen Umgebungen werden unter der Marketplace-Verwaltung allerdings keine Marketplace-Elemente angezeigt.

> [!NOTE]
> Nachdem die Registrierung abgeschlossen ist, wird die aktive Warnung für die Nichtregistrierung nicht mehr angezeigt. Bis zum Azure Stack-Release 1904 wird in nicht verbundenen Umgebungen eine Meldung in der Marketplace-Verwaltung angezeigt, in der Sie aufgefordert werden, Ihre Azure Stack-Instanz zu registrieren und zu aktivieren, auch wenn die Registrierung erfolgreich war. Ab dem Release 1904 wird diese Meldung nicht mehr angezeigt.

## <a name="renew-or-change-registration"></a>Erneuern oder Ändern der Registrierung

### <a name="renew-or-change-registration-in-connected-environments"></a>Erneuern oder Ändern der Registrierung in verbundenen Umgebungen

In folgenden Fällen müssen Sie Ihre Registrierung aktualisieren oder verlängern:

- Nachdem Sie Ihr kapazitätsbasiertes Jahresabonnement verlängert haben.
- Wenn Sie Ihr Abrechnungsmodell ändern.
- Wenn Sie Änderungen für die kapazitätsbasierte Abrechnung vornehmen (Knoten hinzufügen/entfernen).

#### <a name="change-the-subscription-you-use"></a>Ändern des verwendeten Abonnements

Wenn Sie das von Ihnen verwendete Abonnement ändern möchten, müssen Sie zuerst das Cmdlet **Remove-AzsRegistration** ausführen und sich anschließend vergewissern, dass Sie im richtigen Azure PowerShell-Kontext angemeldet sind. Führen Sie dann **Set-AzsRegistration** mit allen geänderten Parametern (einschließlich `<billing model>`) aus:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Ändern des Abrechnungsmodells oder des Angebots von Features

Wenn Sie das Abrechnungsmodell oder das Angebot von Features für Ihre Installation ändern möchten, können Sie die Registrierungsfunktion aufrufen, um die neuen Werte festzulegen. Die aktuelle Registrierung muss zuvor nicht entfernt werden:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Erneuern oder Ändern der Registrierung in nicht verbundenen Umgebungen

In folgenden Fällen müssen Sie Ihre Registrierung aktualisieren oder verlängern:

- Nachdem Sie Ihr kapazitätsbasiertes Jahresabonnement verlängert haben.
- Wenn Sie Ihr Abrechnungsmodell ändern.
- Wenn Sie Änderungen für die kapazitätsbasierte Abrechnung vornehmen (Knoten hinzufügen/entfernen).

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Entfernen der Aktivierungsressource aus Azure Stack

Sie müssen zuerst die Aktivierungsressource aus Azure Stack und dann die Registrierungsressource in Azure entfernen.  

Zum Entfernen der Aktivierungsressource in Azure Stack führen Sie die folgenden PowerShell-Cmdlets in Ihrer Azure Stack-Umgebung aus:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Um die Registrierungsressource in Azure zu entfernen, vergewissern Sie sich als Nächstes, dass Sie einen mit Azure verbundenen Computer verwenden. Melden Sie sich dann im richtigen Azure PowerShell-Kontext an, und führen Sie die entsprechenden PowerShell-Cmdlets aus, wie weiter unten beschrieben.

Sie können das Registrierungstoken verwenden, mit dem die Ressource erstellt wurde:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Alternativ können Sie den Registrierungsnamen nutzen:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Erneutes Registrieren in einem nicht verbundenen Szenario

Sie haben nun die Registrierung in einem nicht verbundenen Szenario vollständig aufgehoben und müssen die Schritte zum Registrieren einer Azure Stack-Umgebung in einem nicht verbundenen Szenario wiederholen.

### <a name="disable-or-enable-usage-reporting"></a>Deaktivieren oder Aktivieren von Nutzungsberichten

Für Azure Stack-Umgebungen, die ein kapazitätsbasiertes Abrechnungsmodell verwenden, deaktivieren Sie Nutzungsberichte mit dem **UsageReportingEnabled**-Parameter im Cmdlet **Set-AzsRegistration** oder im Cmdlet **Get-AzsRegistrationToken**. Azure Stack erstellt standardmäßig Berichte zu Nutzungsmetriken. Betreiber, die ein kapazitätsbasiertes Modell verwenden oder eine nicht verbundene Umgebung unterstützen, müssen Nutzungsberichte deaktivieren.

#### <a name="with-a-connected-azure-stack"></a>Mit einer verbundenen Azure Stack-Instanz

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Mit einer nicht verbundenen Azure Stack-Instanz

1. Führen Sie die folgenden PowerShell-Cmdlets aus, um das Registrierungstoken zu ändern:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Das Registrierungstoken wird in der Datei gespeichert, die für *$FilePathForRegistrationToken* angegeben ist. Sie können den Dateipfad oder den Dateinamen nach eigenem Ermessen ändern.

2. Speichern Sie dieses Registrierungstoken für die Verwendung auf einem mit Azure verbundenen Computer. Sie können die Datei oder den Text aus *$FilePathForRegistrationToken* kopieren.

## <a name="move-a-registration-resource"></a>Verschieben einer Registrierungsressource

Das Verschieben einer Registrierungsressource zwischen Ressourcengruppen im gleichen Abonnement **wird** für alle Umgebungen unterstützt. Das Verschieben einer Registrierungsressource zwischen Abonnements wird jedoch nur für CSPs unterstützt, wenn beide Abonnements zur gleichen Partner-ID aufgelöst werden. Weitere Informationen zum Verschieben von Ressourcen in eine neue Ressourcengruppe finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](/azure/azure-resource-manager/resource-group-move-resources).

> [!IMPORTANT]
> Um das versehentliche Löschen von Registrierungsressourcen im Portal zu verhindern, fügt das Registrierungsskript der Ressource automatisch eine Sperre hinzu. Sie müssen diese Sperre entfernen, bevor Sie sie verschieben oder löschen. Es empfiehlt sich, Ihrer Registrierungsressource eine Sperre hinzufügen, um eine versehentliche Löschung zu verhindern.

## <a name="registration-reference"></a>Referenz zur Registrierung

### <a name="set-azsregistration"></a>Set-AzsRegistration

Sie können **Set-AzsRegistration** zum Registrieren von Azure Stack bei Azure und zum Aktivieren oder Deaktivieren des Angebots von Elementen im Marketplace sowie Nutzungsberichten verwenden.

Voraussetzungen für die Ausführung des Cmdlets:

- Ein globales Azure-Abonnement beliebigen Typs.
- Sie müssen bei Azure PowerShell mit einem Konto angemeldet sein, bei dem es sich um einen Besitzer oder Mitwirkenden für dieses Abonnement handelt.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parameter | type | BESCHREIBUNG |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Die Anmeldeinformationen, die für den [Zugriff auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) verwendet werden. Der Benutzername hat das Format **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Zeichenfolge | Eine vorkonfigurierte Remote-PowerShell-Konsole, die Ihnen Funktionen wie die Protokollsammlung und andere Aufgaben nach der Bereitstellung zur Verfügung stellt. Weitere Informationen finden Sie im Artikel [Verwenden des privilegierten Endpunkts](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | Zeichenfolge |  |
| ResourceGroupLocation | Zeichenfolge |  |
| BillingModel | Zeichenfolge | Das Abrechnungsmodell, das von Ihrem Abonnement verwendet wird. Gültige Werte für diesen Parameter: „Capacity“, „PayAsYouUse“ und „Development“. |
| MarketplaceSyndicationEnabled | True/False | Steuert, ob das Marketplace-Verwaltungsfeature im Portal verfügbar ist. Legen Sie den Wert auf „true“ fest, wenn bei der Registrierung eine Internetverbindung verfügbar ist. Legen Sie den Wert auf „false“ fest, wenn die Registrierung in einer Umgebung ohne Verbindung erfolgt. In Umgebungen ohne Verbindung können Marketplace-Elemente mithilfe des [Offlinesyndikationstools](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) heruntergeladen werden. |
| UsageReportingEnabled | True/False | Azure Stack erstellt standardmäßig Berichte zu Nutzungsmetriken. Betreiber, die ein kapazitätsbasiertes Modell verwenden oder eine nicht verbundene Umgebung unterstützen, müssen Nutzungsberichte deaktivieren. Gültige Werte für diesen Parameter: True, False. |
| AgreementNumber | Zeichenfolge |  |
| RegistrationName | Zeichenfolge | Legen Sie einen eindeutigen Namen für die Registrierung fest, wenn Sie das Registrierungsskript in mehreren Instanzen von Azure Stack mit der gleichen Azure-Abonnement-ID ausführen. Der Parameter hat den Standardwert **AzureStackRegistration**. Wenn Sie den gleichen Namen jedoch in mehreren Instanzen von Azure Stack verwenden, tritt beim Ausführen des Skripts ein Fehler auf. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Mit „Get-AzsRegistrationToken“ wird aus den Eingabeparametern ein Registrierungstoken generiert.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parameter | type | BESCHREIBUNG |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Die Anmeldeinformationen, die für den [Zugriff auf den privilegierten Endpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) verwendet werden. Der Benutzername hat das Format **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Zeichenfolge |  Eine vorkonfigurierte Remote-PowerShell-Konsole, die Ihnen Funktionen wie die Protokollsammlung und andere Aufgaben nach der Bereitstellung zur Verfügung stellt. Weitere Informationen finden Sie im Artikel [Verwenden des privilegierten Endpunkts](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | Zeichenfolge |  |
| ResourceGroupLocation | Zeichenfolge |  |
| BillingModel | Zeichenfolge | Das Abrechnungsmodell, das von Ihrem Abonnement verwendet wird. Gültige Werte für diesen Parameter: „Capacity“, „PayAsYouUse“ und „Development“. |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | Azure Stack erstellt standardmäßig Berichte zu Nutzungsmetriken. Betreiber, die ein kapazitätsbasiertes Modell verwenden oder eine nicht verbundene Umgebung unterstützen, müssen Nutzungsberichte deaktivieren. Gültige Werte für diesen Parameter: True, False. |
| AgreementNumber | Zeichenfolge |  |

## <a name="registration-failures"></a>Fehler bei der Registrierung

Bei der Registrierung von Azure Stack können unter Umständen Fehler wie die folgenden auftreten:

- Erforderliche Hardwareinformationen für „$hostName“ konnten nicht abgerufen werden. Überprüfen Sie den physischen Host und die Konnektivität, und wiederholen Sie anschließend die Registrierung.

- Mit „$hostName“ konnte keine Verbindung hergestellt werden, um Hardwareinformationen abzurufen. Überprüfen Sie den physischen Host und die Konnektivität, und wiederholen Sie anschließend die Registrierung.

> Ursache: Dieses Problem tritt in der Regel auf, wenn versucht wird, Hardwaredetails wie UUID, BIOS und CPU vom Host abzurufen, um die Aktivierung durchzuführen, aber keine Verbindung mit dem physischen Host hergestellt werden kann.

Beim Zugriff auf die Marketplace-Verwaltung tritt ein Fehler bei der Produktsyndizierung auf. 
> Ursache: Dieses Problem tritt für gewöhnlich auf, wenn Azure Stack nicht auf die Registrierungsressource zugreifen kann. Das liegt häufig daran, dass bei einer Änderung des Verzeichnismandanten eines Azure-Abonnements die Registrierung zurückgesetzt wird. Sie können nicht auf den Azure Stack-Marketplace zugreifen und keine Nutzungsberichte erstellen, wenn Sie den Verzeichnismandanten des Abonnements geändert haben. Zur Behebung dieses Problems ist eine erneute Registrierung erforderlich.

Sie werden von der Marketplace-Verwaltung weiterhin zur Registrierung und Aktivierung Ihrer Azure Stack-Instanz aufgefordert, obwohl Sie Ihren Stamp bereits mithilfe des nicht verbundenen Prozesses registriert haben.
> Ursache: Hierbei handelt es sich um ein bekanntes Problem für nicht verbundene Umgebungen. Die Schritte zum Überprüfen Ihres Registrierungsstatus finden Sie [hier](azure-stack-registration.md#verify-azure-stack-registration). Wenn Sie die Marketplace-Verwaltung verwenden möchten, müssen Sie das [Offlinetool](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) verwenden.

## <a name="next-steps"></a>Nächste Schritte

[Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md)
