---
title: Verwalten eines Dienstprinzipals für Azure Stack | Microsoft-Dokumentation
description: Beschreibt das Verwalten eines neuen Dienstprinzipals, der mit der rollenbasierten Zugriffssteuerung in Azure Resource Manager zum Verwalten des Zugriffs auf Ressourcen verwendet werden kann.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 8d6548ada50a25350f622d7bc7460005f4abc401
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308838"
---
# <a name="provide-applications-access-to-azure-stack"></a>Bereitstellen des Anwendungszugriffs auf Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Wenn eine Anwendung Zugriff benötigt, um Ressourcen über Azure Resource Manager in Azure Stack bereitzustellen oder zu konfigurieren, erstellen Sie einen Dienstprinzipal. Dies ist ein Objekt mit Anmeldeinformationen für Ihre Anwendung. Sie können für diesen Dienstprinzipal dann nur die erforderlichen Berechtigungen delegieren.  

Beispiel: Sie verfügen über ein Tool für die Konfigurationsverwaltung, das Azure Resource Manager zum Inventarisieren von Azure-Ressourcen einsetzt. In diesem Szenario können Sie einen Dienstprinzipal erstellen, dem Dienstprinzipal die Rolle „Leser“ gewähren und das Tool für die Konfigurationsverwaltung auf den schreibgeschützten Zugriff beschränken. 

Der Ansatz mit Dienstprinzipalen ist dem Ausführen der App mit Ihren Anmeldeinformationen aus folgenden Gründen vorzuziehen:

 - Sie können dem Dienstprinzipal Berechtigungen zuweisen, die sich von Ihren eigenen Kontoberechtigungen unterscheiden. In der Regel sind diese Berechtigungen genau auf die Aufgaben der App beschränkt.
 - Sie müssen keine Anmeldeinformationen für die App ändern, wenn sich Ihre Zuständigkeiten ändern.
 - Sie können ein Zertifikat verwenden, um die Authentifizierung beim Ausführen eines unbeaufsichtigten Skripts zu automatisieren.  

## <a name="getting-started"></a>Erste Schritte

Je nachdem, wie Sie Azure Stack bereitgestellt haben, beginnen Sie mit dem Erstellen eines Dienstprinzipals. Dieses Dokument beschreibt das Erstellen eines Dienstprinzipals für:

- Azure Active Directory (Azure AD): Azure AD ist ein mehrinstanzenfähiger cloudbasierter Verzeichnis- und Identitätsverwaltungsdienst. Sie können Azure AD mit einem verbundenen Azure Stack verwenden.
- Active Directory-Verbunddienste (AD FS). AD FS verfügt über Funktionen für den vereinfachten, geschützten Identitätsverbund und die einmalige Webanmeldung (SSO). Sie können AD FS sowohl mit verbundenen als auch mit getrennten Azure Stack-Instanzen verwenden.

Nachdem Sie den Dienstprinzipal erstellt haben, können Sie einige Schritte ausführen, die für AD FS und Azure Active Directory gleich sind, um für die Rolle Berechtigungen zu delegieren.

## <a name="manage-service-principal-for-azure-ad"></a>Verwalten des Dienstprinzipals für Azure AD

Wenn Sie Azure Stack mit Azure Active Directory (Azure AD) als Identitätsverwaltungsdienst bereitgestellt haben, können Sie Dienstprinzipale genauso wie für Azure erstellen. In diesem Abschnitt erfahren Sie, wie die Schritte über das Portal ausgeführt werden. Vergewissern Sie sich vor Beginn, dass Sie über die [erforderlichen Azure AD-Berechtigungen]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) verfügen.

### <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

In diesem Abschnitt erstellen Sie eine Anwendung (Dienstprinzipal) in Azure AD, die Ihre Anwendung repräsentiert.

1. Melden Sie sich über das [Azure-Portal](https://portal.azure.com) bei Ihrem Azure-Konto an.
2. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Registrierung einer neuen Anwendung** aus.
3. Geben Sie einen Namen und eine URL für die Anwendung an. Wählen Sie als Typ für die zu erstellende Anwendung entweder **Web-App/API** oder **Nativ** aus. Wählen Sie nach dem Festlegen der Werte **Erstellen** aus.

Sie haben einen Dienstprinzipal für Ihre Anwendung erstellt.

### <a name="get-credentials"></a>Abrufen von Anmeldeinformationen

Beim programmgesteuerten Anmelden verwenden Sie die ID für Ihre Anwendung und einen Authentifizierungsschlüssel für eine Web-App/API. Führen Sie die folgenden Schritte aus, um diese Werte abzurufen:

1. Wählen Sie in Active Directory unter **App Registrierungen** Ihre Anwendung aus.

2. Kopieren Sie die **Anwendungs-ID**, und speichern Sie sie in Ihrem Anwendungscode. Die Anwendungen im Abschnitt „Beispielanwendungen“ verweisen auf diesen Wert als Client-ID.

     ![Client-ID](./media/azure-stack-create-service-principal/image12.png)
3. Klicken Sie zum Generieren eines Authentifizierungsschlüssels für eine Web-App/API auf **Einstellungen** > **Schlüssel**. 

4. Geben Sie eine Beschreibung des Schlüssels und eine Dauer für den Schlüssel ein. Wählen Sie dann die Option **Schließen**.

Nach dem Speichern des Schlüssels wird der Wert des Schlüssels angezeigt. Kopieren Sie diesen Wert in Editor oder an einen anderen temporären Speicherort, damit Sie den Schlüssel später abrufen können. Sie geben den Schlüsselwert zusammen mit der Anwendungs-ID ein, um die Anmeldung als Anwendung durchzuführen. Speichern Sie die Schlüsselwert an einem Ort, von dem Ihre Anwendung ihn abrufen kann.

![Gespeicherter Schlüssel](./media/azure-stack-create-service-principal/image15.png)

Nach Abschluss des Vorgangs können Sie Ihrer Anwendung eine Rolle zuweisen.

## <a name="manage-service-principal-for-ad-fs"></a>Verwalten eines Dienstprinzipals für AD FS

Wenn Sie Azure Stack mit Azure Directory-Verbunddiensten (AD FS) als Identitäsverwaltungsdienst bereitgestellt haben, verwenden Sie PowerShell, um einen Dienstprinzipal zu erstellen, eine Rolle für den Zugriff zuzuweisen und sich mit dieser Identität anzumelden.

Sie können eine von zwei Methoden verwenden, um Ihren Dienstprinzipal mit AD FS zu erstellen. Ihre Möglichkeiten:
 - [Erstellen eines Dienstprinzipals mithilfe eines Zertifikats](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Erstellen eines Dienstprinzipals mithilfe eines geheimen Clientschlüssels](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Aufgaben für die Verwaltung von AD FS-Dienstprinzipalen.

| Type | Aktion |
| --- | --- |
| AD FS-Zertifikat | [Erstellen](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| AD FS-Zertifikat | [Aktualisieren](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| AD FS-Zertifikat | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Geheimer AD FS-Clientschlüssel | [Erstellen](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Geheimer AD FS-Clientschlüssel | [Aktualisieren](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Geheimer AD FS-Clientschlüssel | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Erstellen eines Dienstprinzipals mithilfe eines Zertifikats

Wenn Sie einen Dienstprinzipal erstellen, während Sie AD FS für die Identität verwenden, können Sie ein Zertifikat verwenden.

#### <a name="certificate"></a>Zertifikat

Ein Zertifikat ist erforderlich.

**Zertifikatanforderungen**

 - Der Kryptografiedienstanbieter (Cryptographic Service Provider, CSP) muss ein Legacyschlüsselanbieter sein.
 - Das Zertifikatformat muss eine PFX-Datei sein, da sowohl der öffentliche als auch der private Schlüssel benötigt wird. Windows-Server verwenden PFX-Dateien, die die Datei für den öffentlichen Schlüssel (SSL-Zertifikatdatei) und die zugehörige Datei für den privaten Schlüssel enthalten.
 - Zertifikate müssen für die Produktion von einer internen oder einer öffentlichen Zertifizierungsstelle ausgestellt werden. Wenn Sie eine öffentliche Zertifizierungsstelle verwenden, muss die Zertifizierungsstelle im Rahmen des Microsoft Trusted Root Authority Program in das Basisbetriebssystem-Image aufgenommen werden. Sie finden die vollständige Liste unter [Microsoft Trusted Root Certificate Program: Participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Ihre Azure Stack-Infrastruktur muss über Netzwerkzugriff auf den im Zertifikat veröffentlichten Speicherort der Zertifikatsperrliste (Certificate Revocation List, CRL) der Zertifizierungsstelle verfügen. Bei dieser CRL muss es sich um einen HTTP-Endpunkt handeln.

#### <a name="parameters"></a>Parameter

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

|Parameter|BESCHREIBUNG|Beispiel|
|---------|---------|---------|
|NAME|Name des SPN-Kontos|MyAPP|
|ClientCertificates|Array von Zertifikatobjekten|X.509-Zertifikat|
|ClientRedirectUris<br>(Optional)|Umleitungs-URI der Anwendung|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Erstellen eines Dienstprinzipals mithilfe von Azure PowerShell

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Zu Überprüfungszwecken kann mit dem folgenden Beispiel ein selbstsigniertes Zertifikat erstellt werden:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Nach Abschluss der Automatisierung zeigt es die für die Verwendung des SPN erforderlichen Details an. Es wird empfohlen, die Ausgabe zur späteren Verwendung zu speichern.

   Beispiel: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Aktualisieren des Zertifikats für den Dienstprinzipal für AD FS

Wenn Sie Azure Stack mit AD FS bereitgestellt haben, können Sie PowerShell verwenden, um das Geheimnis für einen Dienstprinzipal zu aktualisieren.

Das Skript wird über den privilegierten Endpunkt auf einem virtuellen ERCS-Computer ausgeführt.

#### <a name="parameters"></a>Parameter

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

|Parameter|BESCHREIBUNG|Beispiel|
|---------|---------|---------|
|NAME|Name des SPN-Kontos|MyAPP|
|ApplicationIdentifier|Eindeutiger Bezeichner|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Array von Zertifikatobjekten|X.509-Zertifikat|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Beispiel für das Aktualisieren eines Dienstprinzipals für AD FS

In dem Beispiel wird ein selbstsigniertes Zertifikat erstellt. Wenn Sie die Cmdlets in einer Produktionsbereitstellung ausführen, rufen Sie mit [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) das Zertifikatobjekt für das Zertifikat ab, das Sie verwenden möchten.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Nach Abschluss der Automatisierung zeigt sie den aktualisierten Fingerabdruckwert an, der für die SPN-Authentifizierung erforderlich ist.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Erstellen eines Dienstprinzipals mithilfe eines geheimen Clientschlüssels

Wenn Sie einen Dienstprinzipal erstellen, während Sie AD FS für die Identität verwenden, können Sie ein Zertifikat verwenden. Sie verwenden den privilegierten Endpunkt zum Ausführen der Cmdlets.

Diese Skripts werden von dem privilegierten Endpunkt aus auf einem virtuellen ERCS-Computer ausgeführt. Weitere Informationen zum privilegierten Endpunkt finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

#### <a name="parameters"></a>Parameter

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

| Parameter | BESCHREIBUNG | Beispiel |
|----------------------|--------------------------|---------|
| NAME | Name des SPN-Kontos | MyAPP |
| GenerateClientSecret | Erstellen eines Geheimnisses |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Verwenden Sie den ERCS-PrivilegedEndpoint, um den Dienstprinzipal zu erstellen.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Nach Ausführung der Cmdlets zeigt die Shell die für die Verwendung des SPN erforderlichen Details an. Stellen Sie sicher, dass Sie den geheimen Clientschlüssel speichern.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Aktualisieren des geheimen Clientschlüssels für einen Dienstprinzipal für AD FS

Ein neuer geheimer Clientschlüssel wird automatisch von dem PowerShell-Cmdlet generiert.

Das Skript wird über den privilegierten Endpunkt auf einem virtuellen ERCS-Computer ausgeführt.

##### <a name="parameters"></a>Parameter

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

| Parameter | BESCHREIBUNG | Beispiel |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Eindeutiger Bezeichner. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Ändert den geheimen Clientschlüssel mit einem Rolloverzeitraum von 2880 Minuten, während dessen das alte Geheimnis noch gültig ist. |  |
| ResetClientSecret | Sofortiges Ändern des geheimen Clientschlüssels |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Beispiel für das Aktualisieren eines geheimen Clientschlüssels für AD FS

Im Beispiel wird der Parameter **ResetClientSecret** verwendet, der das Clientgeheimnis sofort ändert.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden Cmdlets aus:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Nach Abschluss der Automatisierung zeigt sie das neu generierte Geheimnis an, das für die SPN-Authentifizierung erforderlich ist. Stellen Sie sicher, dass Sie den neuen geheimen Clientschlüssel speichern.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Entfernen eines Dienstprinzipals für AD FS

Wenn Sie Azure Stack mit AD FS bereitgestellt haben, können Sie PowerShell verwenden, um einen Dienstprinzipal zu löschen.

Das Skript wird über den privilegierten Endpunkt auf einem virtuellen ERCS-Computer ausgeführt.

#### <a name="parameters"></a>Parameter

Die folgenden Informationen sind als Eingabe für die Automatisierungsparameter erforderlich:

|Parameter|BESCHREIBUNG|Beispiel|
|---------|---------|---------|
| Parameter | BESCHREIBUNG | Beispiel |
| ApplicationIdentifier | Eindeutiger Bezeichner | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Zum Anzeigen einer Liste alle vorhandenen Dienstprinzipale und ihrer Anwendungs-IDs kann der Befehl „get-graphapplication“ verwendet werden.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Beispiel für das Entfernen des Dienstprinzipals für AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Um auf Ressourcen in Ihrem Abonnement zuzugreifen, müssen Sie die Anwendung einer Rolle zuweisen. Entscheiden Sie, welche Rolle die geeigneten Berechtigungen für die Anwendung darstellt. Informationen zu verfügbaren Rollen finden Sie unter [RBAC: Integrierte Rollen]((/azure/role-based-access-control/built-in-roles).

Sie können den Umfang auf Abonnement-, Ressourcengruppen- oder Ressourcenebene festlegen. Berechtigungen werden von niedrigeren Ebenen mit geringerem Umfang geerbt. Wenn z.B. der Leserolle für eine Ressourcengruppe eine Anwendung hinzugefügt wird, kann diese Rolle die Ressourcengruppe und alle darin enthaltenen Ressourcen lesen.

1. Navigieren Sie im Azure Stack-Portal zur Bereichsebene, der Sie die Anwendung zuweisen möchten. Um z.B. einer Gruppe im Abonnementkontext eine Rolle zuzuweisen, wählen Sie **Abonnements** aus. Sie können stattdessen auch eine Ressourcengruppe oder Ressource auswählen.

2. Wählen Sie das entsprechende Abonnement (Ressourcengruppe oder Ressource) aus, dem die Anwendung zugewiesen werden soll.

     ![Auswählen des Abonnements für die Zuweisung](./media/azure-stack-create-service-principal/image16.png)

3. Wählen Sie **Access Control (IAM)** aus.

     ![Auswählen des Zugriffs](./media/azure-stack-create-service-principal/image17.png)

4. Wählen Sie **Rollenzuweisung hinzufügen** aus.

5. Wählen Sie die Rolle aus, die Sie der Anwendung zuweisen möchten.

6. Suchen Sie nach Ihrer Anwendung, und wählen Sie sie aus.

7. Wählen Sie **OK** aus, um das Zuweisen der Rolle abzuschließen. Ihre Anwendung wird in der Liste der Benutzer angezeigt, die einer Rolle für diesen Kontext zugewiesen sind.

Nachdem Sie nun einen Dienstprinzipal erstellt und eine Rolle zugewiesen haben, können Sie diese Elemente in Ihrer Anwendung nutzen, um auf Azure Stack-Ressourcen zuzugreifen.  

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Benutzern für AD FS](azure-stack-add-users-adfs.md)  
[Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md)  
[Azure Active Directory-Dokumentation](https://docs.microsoft.com/azure/active-directory)  
[Active Directory-Verbunddienste (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
