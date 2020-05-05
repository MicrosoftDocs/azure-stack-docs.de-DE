---
title: Rotieren von Geheimnissen und Zertifikaten in App Service in Azure Stack Hub
description: Hier erfahren Sie, wie Sie Geheimnisse und Zertifikate rotieren, die von Azure App Service in Azure Stack Hub verwendet werden.
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: 39431c7e217410f7bae78fe114595ec6c90399b5
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150243"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Rotieren von Geheimnissen und Zertifikaten in App Service in Azure Stack Hub

Diese Anweisungen gelten nur für Azure App Service in Azure Stack Hub. Das Rotieren von Geheimnissen in Azure App Service in Azure Stack Hub ist nicht Teil des zentralisierten Rotationsverfahrens von Geheimnissen für Azure Stack Hub. Bediener können die Gültigkeit von Geheimnissen innerhalb des Systems, das Datum der letzten Aktualisierung und die verbleibende Zeit bis zum Ablauf der Geheimnisse überwachen.

> [!Important]
> Die Bediener erhalten keine Warnungen zum Ablauf von Geheimnissen im Azure Stack Hub-Dashboard, da Azure App Service in Azure Stack Hub nicht in den Azure Stack Hub-Warnungsdienst integriert ist. Die Bediener müssen die Geheimnisse regelmäßig mit den Verwaltungsfunktionen für Azure App Service in Azure Stack Hub im Azure Stack Hub-Administratorportal überprüfen.

Dieses Dokument enthält die Vorgehensweise zum Rotieren der folgenden Geheimnisse:

* Verschlüsselungsschlüssel, die in Azure App Service in Azure Stack Hub verwendet werden
* Anmeldeinformationen für Datenbankverbindungen, die von Azure App Service in Azure Stack Hub für die Interaktion mit den Hosting- und Messungsdatenbanken verwendet werden
* Zertifikate, die von Azure App Service in Azure Stack Hub zum Sichern von Endpunkten und zur Rotation des Identitätsanwendungszertifikats in Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) verwendet werden
* Systemanmeldeinformationen für Rollen in Azure App Service in Azure Stack Hub

## <a name="rotate-encryption-keys"></a>Rotieren von Verschlüsselungsschlüsseln

Führen Sie zum Rotieren der Verschlüsselungsschlüssel, die in Azure App Service in Azure Stack Hub verwendet werden, die folgenden Schritte aus:

1. Navigieren Sie zur App Service-Verwaltungsoberfläche im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Verschlüsselungsschlüssel“ auf die Schaltfläche **Rotieren**.

1. Wählen Sie **OK** aus, um die Rotation zu starten.

1. Die Verschlüsselungsschlüssel werden rotiert, und alle Rolleninstanzen werden aktualisiert. Bediener können den Status mithilfe der Schaltfläche **Status** überprüfen.

## <a name="rotate-connection-strings"></a>Rotieren von Verbindungszeichenfolgen

Führen Sie zum Aktualisieren der Anmeldeinformationen für die Datenbankverbindungszeichenfolge für die App Service-Hosting- und Messungsdatenbanken die folgenden Schritte aus:

1. Navigieren Sie zur App Service-Verwaltungsoberfläche im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Verbindungszeichenfolgen“ auf die Schaltfläche **Rotieren**.

1. Geben Sie den **SQL SA-Benutzernamen** und das **Kennwort** ein, und wählen Sie **OK** aus, um die Rotation zu starten.

1. Die Anmeldeinformationen werden in allen Azure App Service-Rolleninstanzen rotiert. Bediener können den Status mithilfe der Schaltfläche **Status** überprüfen.

## <a name="rotate-certificates"></a>Rotieren von Zertifikaten

Führen Sie zum Rotieren der Zertifikate, die in Azure App Service in Azure Stack Hub verwendet werden, die folgenden Schritte aus:

1. Navigieren Sie zur App Service-Verwaltungsoberfläche im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Zertifikate“ auf die Schaltfläche **Rotieren**.

1. Geben Sie die **Zertifikatsdatei** und das zugehörige **Kennwort** für die zu rotierenden Zertifikate an, und wählen Sie **OK** aus.

1. Die Zertifikate werden in allen Rolleninstanzen für Azure App Service in Azure Stack Hub wie erforderlich rotiert. Bediener können den Status mithilfe der Schaltfläche **Status** überprüfen.

Wenn das Identitätsanwendungszertifikat rotiert wird, muss die entsprechende App in Azure AD oder AD FS ebenfalls mit dem neuen Zertifikat aktualisiert werden.

> [!IMPORTANT]
> Wenn die Identitätsanwendung nicht mit dem neuen Zertifikat aktualisiert wird, kommt es im Benutzerportal für Azure Functions zu Unterbrechungen bzw. Fehlern. Benutzer können dann nicht mehr die Kudu-Entwicklertools verwenden, und Administratoren können in App Service keine Skalierungsgruppen auf Workerebene verwalten.

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>Rotieren der Anmeldeinformationen für die Azure AD-Identitätsanwendung

Die Identitätsanwendung wird vom Operator vor der Bereitstellung von Azure App Service in Azure Stack Hub erstellt. Wenn die Anwendungs-ID unbekannt ist, führen Sie die folgenden Schritte aus, um sie zu ermitteln:

1. Navigieren Sie zum **Azure Stack Hub-Administratorportal**.

1. Navigieren Sie zu **Abonnements**, und wählen Sie **Standardabonnement des Anbieters** aus.

1. Wählen Sie **Zugriffssteuerung (IAM)** und dann die Anwendung **App Service** aus.

1. Notieren Sie sich die **App-ID**. Dieser Wert ist die Anwendungs-ID der Identitätsanwendung, die in Azure AD aktualisiert werden muss.

Führen Sie die folgenden Schritte aus, um das Zertifikat für die Anwendung in Azure AD zu rotieren:

1. Wechseln Sie zum **Azure-Portal**, und melden Sie sich mit den Anmeldeinformationen des globalen Administrators an, der zum Bereitstellen von Azure Stack Hub verwendet wurde.

1. Wechseln Sie zu **Azure Active Directory**, und navigieren Sie zu **App-Registrierungen**.

1. Suchen Sie nach der **Anwendungs-ID**, und geben Sie dann die Identitätsanwendungs-ID an.

1. Wählen Sie die Anwendung aus, und navigieren Sie dann zu **Zertifikate und Geheimnisse**.

1. Wählen Sie **Zertifikat hochladen** aus, und laden Sie das neue Zertifikat für die Identitätsanwendung mit einem der folgenden Dateitypen hoch: „.cer“, „.pem“ oder „.crt“.

1. Vergewissern Sie sich, dass der **Fingerabdruck** mit dem Fingerabdruck übereinstimmt, der in der App Service-Verwaltungsoberfläche im Azure Stack Hub-Administratorportal aufgeführt ist.

1. Löschen Sie das alte Zertifikat.

### <a name="rotate-certificate-for-ad-fs-identity-application"></a>Rotieren des Zertifikats für die AD FS-Identitätsanwendung

Die Identitätsanwendung wird vom Operator vor der Bereitstellung von Azure App Service in Azure Stack Hub erstellt. Wenn die Objekt-ID der Anwendung unbekannt ist, führen Sie die folgenden Schritte aus, um sie zu ermitteln:

1. Navigieren Sie zum **Azure Stack Hub-Administratorportal**.

1. Navigieren Sie zu **Abonnements**, und wählen Sie **Standardabonnement des Anbieters** aus.

1. Wählen Sie **Zugriffssteuerung (IAM)** und dann die Anwendung **AzureStack-AppService<guid>** aus.

1. Notieren Sie sich die **Objekt-ID**. Dieser Wert ist die ID des Dienstprinzipals, der in AD FS aktualisiert werden muss.

Um das Zertifikat für die Anwendung in AD FS zu rotieren, benötigen Sie Zugriff auf den privilegierten Endpunkt (PEP). Anschließend aktualisieren Sie die Zertifikatanmeldeinformationen mithilfe von PowerShell, indem Sie die folgenden Platzhalter durch Ihre eigenen Werte ersetzen:

| Platzhalter | BESCHREIBUNG | Beispiel |
| ----------- | ----------- | ------- |
| `<PepVM>` | Der Name der privilegierten Endpunkt-VM in Ihrer Azure Stack Hub-Instanz. | "AzS-ERCS01" |
| `<CertificateFileLocation>` | Der Speicherort des X509-Zertifikats auf dem Datenträger. | "d:\certs\sso.cer" |
| `<ApplicationObjectId>` | Der Bezeichner, der der Identitätsanwendung zugewiesen ist. | "S-1-5-21-401916501-2345862468-1451220656-1451" |

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten, und führen Sie das folgende Skript aus:

    ```powershell
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint
    $Creds = Get-Credential

    # Create a new Certificate object from the identity application certificate exported as .cer file
    $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<CertificateFileLocation>")

    # Create a new PSSession to the PrivelegedEndpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with the App Service identity application
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<ApplicationObjectId>" -ClientCertificates $using:Cert}
    $Session | Remove-PSSession

    # Output the updated service principal details
    $SpObject

    ```

1. Nachdem das Skript ausgeführt wurde, zeigt es die aktualisierten App-Registrierungsinformationen an, einschließlich des Fingerabdruckwerts für das Zertifikat.

    ```shell
    ApplicationIdentifier : S-1-5-21-401916501-2345862468-1451220656-1451
    ClientId              : 
    Thumbprint            : FDAA679BF9EDDD0CBB581F978457A37BFD73CA3B
    ApplicationName       : Azurestack-AppService-d93601c2-1ec0-4cac-8d1c-8ccde63ef308
    ClientSecret          : 
    PSComputerName        : AzS-ERCS01
    RunspaceId            : cb471c79-a0d3-40ec-90ba-89087d104510
    ```

## <a name="rotate-system-credentials"></a>Rotieren von Systemanmeldeinformationen

Führen Sie zum Rotieren der Systemanmeldeinformationen, die in Azure App Service in Azure Stack Hub verwendet werden, die folgenden Schritte aus:

1. Navigieren Sie zur App Service-Verwaltungsoberfläche im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Systemanmeldeinformationen“ auf die Schaltfläche **Rotieren**.

1. Wählen Sie den **Bereich** für die Systemanmeldeinformationen aus, die Sie rotieren. Bediener können angeben, ob die Systemanmeldeinformationen für alle oder nur für einzelne Rollen rotiert werden sollen.

1. Geben Sie neue Werte für **Benutzername des lokalen Administrators** und **Kennwort** an. Bestätigen Sie das **Kennwort**, und wählen Sie **OK** aus.

1. Die Anmeldeinformationen werden in den entsprechenden Rolleninstanzen für Azure App Service in Azure Stack Hub wie erforderlich rotiert. Bediener können den Status mithilfe der Schaltfläche **Status** überprüfen.
