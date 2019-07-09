---
title: Verwenden einer App-Identität für den Ressourcenzugriff
description: Beschreibt das Verwalten eines Dienstprinzipals, der mit der rollenbasierten Zugriffssteuerung zum Anmelden und für den Zugriff auf Ressourcen verwendet werden kann.
services: azure-resource-manager
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2019
ms.author: bryanla
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 8c27948185df5f98926a3500db0981a1ccddc321
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397314"
---
# <a name="use-an-app-identity-to-access-resources"></a>Verwenden einer App-Identität für den Ressourcenzugriff

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit (ASDK)*

Eine Anwendung, Ressourcen über Azure Resource Manager bereitstellen oder konfigurieren muss, muss von einem Dienstprinzipal dargestellt werden. Genau wie ein Benutzer durch einen Benutzerprinzipal dargestellt wird, ist ein Dienstprinzipal eine Art von Sicherheitsprinzipal, der eine Anwendung darstellt. Der Dienstprinzipal bietet eine Identität für Ihre Anwendung, was es Ihnen erlaubt, nur die erforderlichen Berechtigungen an diesen Dienstprinzipal zu delegieren.  

Beispiel: Sie verfügen über eine Anwendung für die Konfigurationsverwaltung, das Azure Resource Manager zum Inventarisieren von Azure-Ressourcen einsetzt. In diesem Szenario können Sie einen Dienstprinzipal erstellen, dem Dienstprinzipal die Rolle „Leser“ gewähren und die App für die Konfigurationsverwaltung auf den schreibgeschützten Zugriff beschränken. 

## <a name="overview"></a>Übersicht

Ähnlich wie bei einem Benutzerprinzipal, muss ein Dienstprinzipal während der Authentifizierung Anmeldeinformationen bereitstellen, die aus zwei Elementen bestehen:

- Einer **Anwendungs-ID**, manchmal auch als „Client-ID“ bezeichnet. Dies ist eine GUID, die die Registrierung der Anwendung in Ihrem Active Directory-Mandanten eindeutig identifiziert.
- Ein der Anwendungs-ID zugeordnetes **Geheimnis**. Sie können entweder eine Clientgeheimnis-Zeichenfolge (ähnlich wie ein Kennwort) generieren, oder Sie geben eine X.509 Zertifikat an (das seinen öffentlichen Schlüssel verwendet). 

Das Ausführen einer App unter der Identität eines Dienstprinzipals ist der Ausführung unter einem Benutzerprinzipal aus folgenden Gründen vorzuziehen:

 - Ein Dienstprinzipal kann ein X.509-Zertifikat für **Anmeldeinformationen mit höherer Sicherheit** verwenden.  
 - Sie können einem Dienstprinzipal **restriktivere Berechtigungen** zuweisen. In der Regel sind diese Berechtigungen genau auf die von der App zu erfüllenden Aufgaben beschränkt, bekannt als das *Prinzip der geringsten Rechte*.
 - Die **Anmeldeinformationen und Berechtigungen eines Dienstprinzipals ändern sich nicht so häufig**, wie die Anmeldeinformationen eines Benutzers. Wenn sich beispielsweise die Verantwortlichkeiten des Benutzers ändern, Kennwortanforderungen eine Änderung erforderlich machen, oder ein Benutzer das Unternehmen verlässt.

Sie beginnen damit, dass Sie eine neue App-Registrierung erstellen, die ein zugeordnetes [Dienstprinzipalobjekt](/azure/active-directory/develop/developer-glossary#service-principal-object) erstellt, um die Identität der App innerhalb des Verzeichnisses darzustellen. Dieses Dokument beschreibt den Prozess zum Erstellen und Verwalten eines Dienstprinzipals, je nach dem Verzeichnis, das Sie für Ihre Azure Stack-Instanz ausgewählt haben:

- Azure Active Directory (Azure AD): Azure AD ist ein mehrinstanzenfähiger cloudbasierter Verzeichnis- und Identitätsverwaltungsdienst. Sie können Azure AD mit einer verbundenen Azure Stack-Instanz verwenden.
- Active Directory-Verbunddienste (AD FS). AD FS verfügt über Funktionen für den vereinfachten, geschützten Identitätsverbund und die einmalige Webanmeldung (SSO). Sie können AD FS sowohl mit verbundenen als auch mit getrennten Azure Stack-Instanzen verwenden.

Zuerst erfahren Sie, wie Sie einen Dienstprinzipal verwalten, dann wie Sie den Dienstprinzipal einer Rolle zuweisen und so seinen Zugriff auf Ressourcen einschränken.

## <a name="manage-an-azure-ad-service-principal"></a>Verwalten eines Azure AD-Dienstprinzipals 

Wenn Sie Azure Stack mit Azure Active Directory (Azure AD) als Identitätsverwaltungsdienst bereitgestellt haben, können Sie Dienstprinzipale genauso wie für Azure erstellen. In diesem Abschnitt erfahren Sie, wie die Schritte über das Azure-Portal ausgeführt werden. Vergewissern Sie sich vorher, ob Sie über die [erforderlichen Azure AD-Berechtigungen](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) verfügen.

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Erstellen eines Dienstprinzipals, der Anmeldeinformationen mit einem geheimen Clientschlüssel verwendet

In diesem Abschnitt registrieren Sie Ihre Anwendung mithilfe des Azure-Portals, das das Dienstprinzipalobjekt in Ihrem Azure AD-Mandanten erstellt. In diesem Beispiel wird der Dienstprinzipal mit Anmeldeinformationen mit einem geheimen Clientschlüssel erstellt, aber das Portal unterstützt auch auf X.509-Zertifikaten basierende Anmeldeinformationen.

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.
3. Geben Sie einen **Namen** für die Anwendung an. 
4. Wählen Sie die geeigneten **unterstützten Kontotypen** aus.
5. Wählen Sie unter **Umleitungs-URI** die Option **Web** als Anwendungstyp aus, und geben Sie (optional) einen Umleitungs-URI ein, falls Ihre Anwendung dies erfordert. 
6. Wählen Sie nach dem Festlegen der Werte **Registrieren** aus. Die Anwendungsregistrierung wird erstellt, und die Seite **Übersicht** wird angezeigt.
7. Kopieren Sie die **Anwendungs-ID** zur Verwendung in Ihrem Anwendungscode. Dieser Wert wird auch als „Client-ID“ bezeichnet.
8. Um einen geheimen Clientschlüssel zu generieren, wählen Sie die Seite **Zertifikate und Geheimnisse** aus. Wählen Sie **Neuer geheimer Clientschlüssel**.
9. Geben Sie eine **Beschreibung** für das Geheimnis und eine **Ablauf**dauer an. 
10. Wählen Sie anschließend **Hinzufügen** aus.
11. Der Wert des Geheimnisses wird angezeigt. Kopieren und speichern Sie diesen Wert an einem anderen Speicherort, da Sie ihn später nicht mehr abrufen können. Sie geben das Geheimnis mit der Anwendungs-ID in Ihrer Clientanwendung während der Dienstprinzipalanmeldung an. 

    ![Gespeicherter Schlüssel](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Verwalten eines AD FS-Dienstprinzipals

Wenn Sie Azure Stack mit Azure Directory-Verbunddiensten (AD FS) als Identitätsverwaltungsdienst bereitgestellt haben, müssen Sie PowerShell verwenden, um den Dienstprinzipal zu verwalten. Beispiele für das Verwalten von Anmeldeinformationen des Dienstprinzipals finden Sie im Folgenden. Es werden sowohl ein X.509-Zertifikat als auch ein geheimer Clientschlüssel demonstriert.

Die Skripts müssen in einer PowerShell-Konsole mit erhöhten Rechten („Als Administrator ausführen“) ausgeführt werden, wodurch eine weitere Sitzung auf einer VM geöffnet wird, die einen privilegierten Endpunkt für Ihre Azure Stack-Instanz hostet. Sobald die Sitzung des privilegierten Endpunkts eingerichtet wurde, werden zusätzliche Cmdlets ausgeführt und damit der Dienstprinzipal verwaltet. Weitere Informationen zum privilegierten Endpunkt finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Erstellen eines Dienstprinzipals, der Zertifikatanmeldeinformationen verwendet

Wenn Sie ein Zertifikat für Anmeldeinformationen eines Dienstprinzipals erstellen, müssen die folgenden Anforderungen erfüllt sein:

 - Der Kryptografiedienstanbieter (Cryptographic Service Provider, CSP) muss ein Legacyschlüsselanbieter sein.
 - Das Zertifikatformat muss eine PFX-Datei sein, da sowohl der öffentliche als auch der private Schlüssel benötigt wird. Windows-Server verwenden PFX-Dateien, die die Datei für den öffentlichen Schlüssel (SSL-Zertifikatdatei) und die zugehörige Datei für den privaten Schlüssel enthalten.
 - Zertifikate müssen für die Produktion von einer internen oder einer öffentlichen Zertifizierungsstelle ausgestellt werden. Wenn Sie eine öffentliche Zertifizierungsstelle verwenden, muss die Zertifizierungsstelle im Rahmen des Microsoft Trusted Root Authority Program in das Basisbetriebssystem-Image aufgenommen werden. Sie finden die vollständige Liste unter [Microsoft Trusted Root Certificate Program: Participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Ihre Azure Stack-Infrastruktur muss über Netzwerkzugriff auf den im Zertifikat veröffentlichten Speicherort der Zertifikatsperrliste (Certificate Revocation List, CRL) der Zertifizierungsstelle verfügen. Bei dieser CRL muss es sich um einen HTTP-Endpunkt handeln.

Sobald Sie über ein Zertifikat verfügen, verwenden Sie das folgende PowerShell-Skript, um Ihre Anwendung zu registrieren und einen Dienstprinzipal zu erstellen. Den Dienstprinzipal verwenden Sie auch, um sich bei Azure anzumelden. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | BESCHREIBUNG | Beispiel |
| ----------- | ----------- | ------- |
| \<PepVM\> | Der Name der privilegierten Endpunkt-VM in Ihrer Azure Stack-Instanz. | "AzS-ERCS01" |
| \<YourCertificateLocation\> | Der Speicherort Ihres X.509-Zertifikats im lokalen Zertifikatspeicher. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | Ein beschreibender Name für die neue App-Registrierung. | "Mein Verwaltungstool" |

1. Öffnet eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führt das folgende Skript aus:

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
    Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. Nachdem das Skript abgeschlossen ist, zeigt es die Anwendungsregistrierungsinformationen an, einschließlich der Anmeldeinformationen des Dienstprinzipals. Wie gezeigt, werden die `ClientID` und der `Thumbprint` verwendet, um sich unter der Identität des Dienstprinzipals anzumelden. Nach einer erfolgreichen Anmeldung wird die Identität des Dienstprinzipals für die nachfolgende Autorisierung und für den Zugriff auf von Azure Resource Manager verwaltete Ressourcen verwendet. 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Lassen Sie Ihre PowerShell-Konsolensitzung geöffnet, da Sie sie mit dem `ApplicationIdentifier`-Wert im nächsten Abschnitt verwenden werden.

### <a name="update-a-service-principals-certificate-credential"></a>Aktualisieren der Zertifikatanmeldeinformationen eines Dienstprinzipals

Nachdem Sie nun einen Dienstprinzipal erstellt haben, erfahren Sie in diesem Abschnitt Folgendes:

1. Erstellen eines neuen selbstsignierten X.509-Zertifikats für Testzwecke.
2. Aktualisieren der Anmeldeinformationen des Dienstprinzipals durch Aktualisieren der Eigenschaft **Thumbprint** (Fingerabdruck), sodass sie dem neuen Zertifikat entspricht.

Aktualisieren Sie die Zertifikatanmeldeinformationen mithilfe der PowerShell, indem Sie die folgenden Platzhalter durch Ihre eigenen Werte ersetzen:

| Platzhalter | BESCHREIBUNG | Beispiel |
| ----------- | ----------- | ------- |
| \<PepVM\> | Der Name der privilegierten Endpunkt-VM in Ihrer Azure Stack-Instanz. | "AzS-ERCS01" |
| \<YourAppName\> | Ein beschreibender Name für die neue App-Registrierung. | "Mein Verwaltungstool" |
| \<YourCertificateLocation\> | Der Speicherort Ihres X.509-Zertifikats im lokalen Zertifikatspeicher. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | Der Bezeichner, der der Anwendungsregistrierung zugewiesen ist. | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. Führen Sie unter Verwendung Ihrer Windows PowerShell-Sitzung mit erhöhten Rechten die folgenden Cmdlets aus:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Nachdem das Skript abgeschlossen ist, zeigt es die aktualisierten Anwendungsregistrierungsinformationen an, einschließlich des Fingerabdruckwerts für das neue selbstsignierte Zertifikat.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Erstellen eines Dienstprinzipals, der Anmeldeinformationen mit einem geheimen Clientschlüssel verwendet

> [!IMPORTANT]
> Die Verwendung eines geheimen Clientschlüssels ist weniger sicher als Anmeldeinformationen mit einem X.509-Zertifikat. Nicht nur der Authentifizierungsmechanismus ist weniger sicher, sondern es ist in der Regel auch erforderlich, das Geheimnis in den Quellcode der Client-App einzubetten. Daher werden Sie dringend aufgefordert, für Produktionsanwendungen Zertifikatanmeldeinformationen zu verwenden.

Nun erstellen Sie eine weitere App-Registrierung, aber diesmal geben Sie Anmeldeinformationen mit einem geheimen Clientschlüssel an. Im Gegensatz zu Zertifikatanmeldeinformationen ist das Verzeichnis in der Lage, Anmeldeinformationen mit einem geheimen Clientschlüssel zu generieren. Somit verwenden Sie also, anstatt den geheimen Clientschlüssel anzugeben, den Schalter `-GenerateClientSecret`, um seine Generierung anzufordern. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | BESCHREIBUNG | Beispiel |
| ----------- | ----------- | ------- |
| \<PepVM\> | Der Name der privilegierten Endpunkt-VM in Ihrer Azure Stack-Instanz. | "AzS-ERCS01" |
| \<YourAppName\> | Ein beschreibender Name für die neue App-Registrierung. | "Mein Verwaltungstool" |

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
     Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. Nachdem das Skript abgeschlossen ist, zeigt es die Anwendungsregistrierungsinformationen an, einschließlich der Anmeldeinformationen des Dienstprinzipals. Wie gezeigt, werden die `ClientID` und der generierte `ClientSecret` verwendet, um sich unter der Identität des Dienstprinzipals anzumelden. Nach einer erfolgreichen Anmeldung wird die Identität des Dienstprinzipals für die nachfolgende Autorisierung und für den Zugriff auf von Azure Resource Manager verwaltete Ressourcen verwendet.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Lassen Sie Ihre PowerShell-Konsolensitzung geöffnet, da Sie sie mit dem `ApplicationIdentifier`-Wert im nächsten Abschnitt verwenden werden.

### <a name="update-a-service-principals-client-secret"></a>Aktualisieren des geheimen Clientschlüssels eines Dienstprinzipals

Aktualisieren Sie die Anmeldeinformationen mit einem geheimen Clientschlüssel mithilfe der PowerShell unter Verwendung des Parameters **ResetClientSecret**, der dem geheimen Clientschlüssel sofort ändert. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | BESCHREIBUNG | Beispiel |
| ----------- | ----------- | ------- |
| \<PepVM\> | Der Name der privilegierten Endpunkt-VM in Ihrer Azure Stack-Instanz. | "AzS-ERCS01" |
| \<AppIdentifier\> | Der Bezeichner, der der Anwendungsregistrierung zugewiesen ist. | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. Führen Sie unter Verwendung Ihrer Windows PowerShell-Sitzung mit erhöhten Rechten die folgenden Cmdlets aus:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Nachdem das Skript abgeschlossen ist, zeigt es die aktualisierten Anwendungsregistrierungsinformationen an, einschließlich des neu generierten geheimen Clientschlüssels.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Entfernen eines Dienstprinzipals

Jetzt sehen Sie, wie Sie eine App-Registrierung mithilfe der PowerShell aus Ihrem Verzeichnis entfernen/löschen sowie dessen zugeordnetes Dienstprinzipalobjekt. 

Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | BESCHREIBUNG | Beispiel |
| ----------- | ----------- | ------- |
| \<PepVM\> | Der Name der privilegierten Endpunkt-VM in Ihrer Azure Stack-Instanz. | "AzS-ERCS01" |
| \<AppIdentifier\> | Der Bezeichner, der der Anwendungsregistrierung zugewiesen ist. | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

Beim Aufrufen des Cmdlets „Remove-GraphApplication“ am privilegierten Endpunkt wird keine Ausgabe zurückgegeben, aber Ihnen wird während der Ausführung des Cmdlets eine wörtliche Bestätigungsausgabe in der Konsole angezeigt:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Der Zugriff von Benutzern und Anwendungen auf Azure-Ressourcen wird mittels rollenbasierter Zugriffssteuerung autorisiert. Um einer Anwendung den Zugriff auf Ressourcen in Ihrem Abonnement unter Verwendung ihres Dienstprinzipals zu gestatten, müssen Sie den Dienstprinzipal einer *Rolle* für eine bestimmte *Ressource* *zuweisen*. Entscheiden Sie zuerst, welche Rolle die geeigneten *Berechtigungen* für die Anwendung darstellt. Informationen zu den verfügbaren Rollen finden Sie unter [Integrierte Rollen für Azure-Ressourcen](/azure/role-based-access-control/built-in-roles).

Der Typ der Ressource, die Sie auswählen, bestimmt auch den *Zugriffsbereich* für den Dienstprinzipal. Sie können den Zugriffsbereich auf Ebene des Abonnements, der Ressourcengruppe oder der Ressource festlegen. Berechtigungen werden von niedrigeren Ebenen mit geringerem Umfang geerbt. Wenn z.B. der Leserolle für eine Ressourcengruppe eine Anwendung hinzugefügt wird, kann diese Rolle die Ressourcengruppe und alle darin enthaltenen Ressourcen lesen.

1. Melden Sie sich beim entsprechenden Portal an, basierend auf dem Verzeichnis, das Sie während der Azure Stack-Installation angegeben haben (z. B. für Azure AD im Azure-Portal oder für AD FS im Azure Stack-Benutzerportal). In diesem Beispiel zeigen wir einen beim Azure Stack-Benutzerportal angemeldeten Benutzer.

   > [!NOTE]
   > Um Rollenzuweisungen für eine bestimmte Ressource hinzuzufügen, muss Ihr Benutzerkonto einer Rolle angehören, die die `Microsoft.Authorization/roleAssignments/write`-Berechtigung deklariert. Beispielsweise einer der integrierten Rollen [Besitzer](/azure/role-based-access-control/built-in-roles#owner)oder [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator).  
2. Navigieren Sie zu der Ressource, auf die Sie den Zugriff durch den Dienstprinzipal zulassen möchten. In diesem Beispiel weisen Sie den Dienstprinzipal einer Rolle auf Abonnementebene zu, indem Sie zuerst **Abonnements** und dann ein bestimmtes Abonnement auswählen. Sie können stattdessen auch eine Ressourcengruppe oder eine bestimmte Ressource wie einen virtuellen Computer auswählen. 

     ![Auswählen des Abonnements für die Zuweisung](./media/azure-stack-create-service-principal/select-subscription.png)

3. Wählen Sie die Seite **Zugriffssteuerung (IAM)** aus, die für alle Ressourcen universell ist, die rollenbasierte Zugriffssteuerung unterstützen.
4. Wählen Sie **+ Hinzufügen** aus.
5. Wählen Sie unter **Rolle** die Rolle aus, die Sie der Anwendung zuweisen möchten.
6. Suchen Sie unter **Auswählen** nach Ihrer Anwendung, indem Sie einen vollständigen oder teilweisen Anwendungsnamen verwenden. Während der Registrierung wird der Anwendungsname als *Azurestack -\<NameIhrerApp\>-\<ClientID\>* generiert. Wenn Sie beispielsweise den Anwendungsnamen *App2* verwendet haben, und die ClientID *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* während der Erstellung zugewiesen wurde, wäre der vollständige Name *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Sie können entweder nach der exakten Zeichenfolge suchen oder nach einem Teil, wie z. B. *Azurestack* oder *Azurestack-App2*.
7. Sobald Sie die App gefunden haben, wählen Sie sie aus, und sie wird unter **Ausgewählte Mitglieder** angezeigt.
8. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen. 

     [ ![Zuweisen einer Rolle](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Wenn Sie fertig sind, wird die Anwendung in der Liste der für den aktuellen Bereich zugewiesenen Prinzipale für die angegebene Rolle angezeigt.

     [ ![Zugewiesene Rolle](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Nachdem Sie nun einen Dienstprinzipal erstellt und eine Rolle zugewiesen haben, können Sie diese Elemente in Ihrer Anwendung nutzen, um auf Azure Stack-Ressourcen zuzugreifen.  

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Benutzern für AD FS](azure-stack-add-users-adfs.md)  
[Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md)  
[Azure Active Directory-Dokumentation](https://docs.microsoft.com/azure/active-directory)  
[Active Directory-Verbunddienste (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
