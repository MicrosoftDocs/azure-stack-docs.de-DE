---
title: Herstellen einer Verbindung mit Azure Stack Hub über die Azure-Kontoerweiterung in Visual Studio Code
description: Stellen Sie als Entwickler eine Verbindung mit Azure Stack Hub her, indem Sie die Azure-Kontoerweiterung in Visual Studio Code verwenden.
author: mattbriggs
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/21/2020
ms.openlocfilehash: bedb015cb799619933510745e5bc3292a722cf14
ms.sourcegitcommit: 9a3397f703ff9dd7d539372bd8e5fdbe6d6a0725
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "91019604"
---
# <a name="connect-to-azure-stack-hub-using-azure-account-extension-in-visual-studio-code"></a>Herstellen einer Verbindung mit Azure Stack Hub über die Azure-Kontoerweiterung in Visual Studio Code

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie eine Verbindung mit Azure Stack Hub herstellen, indem Sie die Azure-Kontoerweiterung verwenden. Sie müssen Ihre VS Code-Einstellungen (Visual Studio Code) aktualisieren.

VS Code ist ein einfacher Editor zum Erstellen und Debuggen von Web- und Cloudanwendungen. ASP.NET Core, Python, NodeJS, Go und andere Entwickler verwenden VS Code. Mit der Azure-Kontoerweiterung können Sie eine einzelne Azure-Anmeldung mit Abonnementfilterung für zusätzliche Azure-Erweiterungen verwenden. Die Erweiterung macht die Azure Cloud Shell im VS Code-integrierten Terminal verfügbar. Mit der Erweiterung können Sie eine Verbindung mit Ihrem Azure Stack Hub-Abonnement herstellen, indem Sie sowohl Azure Active Directory (Azure AD) als auch Active Directory-Verbunddienste (AD FS) für Ihren Identitäts-Manager verwenden. Sie können sich bei Azure Stack Hub anmelden, Ihr Abonnement auswählen und eine neue Befehlszeile in einer Cloud Shell-Instanz öffnen. 

> [!NOTE]  
> Sie können die Schritte in diesem Artikel für eine AD FS-Umgebung (Active Directory-Verbunddienste) nutzen. Verwenden Sie Ihre AD FS-Anmeldeinformationen und -Endpunkte.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Voraussetzungen für die Azure-Kontoerweiterung

1. Build 1904 der Azure Stack Hub-Umgebung oder höher
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure-Kontoerweiterung](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack Hub-Abonnement](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack-hub"></a>Schritte zum Herstellen einer Verbindung mit Azure Stack Hub

1. Führen Sie das **Identity**-Skript aus den Azure Stack Hub-Tools in GitHub aus.

    - Bevor Sie das Skript ausführen, müssen Sie PowerShell installiert und für Ihre Umgebung konfiguriert haben. Weitere Anleitungen finden Sie unter [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md).

    - Anleitungen zum **Identity**-Skript sowie das Skript selbst finden Sie unter [AzureStack-Tools/Identity](https://aka.ms/aa6z611).

    - Führen Sie in derselben Sitzung Folgendes aus:

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. Öffnen Sie Visual Studio Code.

3. Wählen Sie links in der Ecke die Option **Erweiterungen**.

4. Geben Sie im Suchfeld `Azure Account`ein.

5. Wählen Sie **Azure-Konto** und dann **Installieren**.

      ![Azure Stack Hub – Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

6. Starten Sie VS Code neu, um die Erweiterung zu laden.

7. Rufen Sie die Metadaten ab, um eine Verbindung mit Azure Resource Manager in Ihrer Azure Stack Hub-Instanz herzustellen. 
    
    Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Sie Azure-Ressourcen bereitstellen, verwalten und überwachen können.
    - Die Resource Manager-URL für das Azure Stack Development Kit (ASDK) lautet: `https://management.local.azurestack.external/`. 
    - Die Resource Manager-URL in integrierten Systemen lautet `https://management.region.<fqdn>/`, wobei `<fqdn>` Ihr vollqualifizierter Domänenname ist.
    - Fügen Sie Ihrer URL für den Zugriff auf die Metadaten den folgenden Text hinzu: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Die URL zum Abrufen der Metadaten für Ihren Azure Resource Manager-Endpunkt kann beispielsweise wie folgt aussehen: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`.

    Notieren Sie sich den zurückgegebenen JSON-Code. Sie benötigen die Werte für die Eigenschaften `loginEndpoint` und `audiences`.

8. Drücken Sie **STRG+UMSCHALT+P**, und wählen Sie **Preferences: Open User Settings (JSON)** (Einstellungen: Benutzereinstellungen öffnen (JSON)).

9. Aktualisieren Sie im Code-Editor den folgenden JSON-Codeausschnitt mit den Werten für Ihre Umgebung, und fügen Sie den Codeausschnitt dann in den Block mit den Einstellungen ein.

    - Werte:

        | Parameter | BESCHREIBUNG |
        | --- | --- |
        | `tenant-ID` | Der Wert Ihrer [Mandanten-ID](../operator/azure-stack-identity-overview.md) für Azure Stack Hub. |
        | `activeDirectoryEndpointUrl` | Dies ist die URL aus der loginEndpoint-Eigenschaft. |
        | `activeDirectoryResourceId` | Dies ist die URL aus der audiences-Eigenschaft.
        | `resourceManagerEndpointUrl` | Dies ist die Stamm-URL für die Azure Resource Manager-Instanz für Azure Stack Hub. |
        | `validateAuthority` | Sie können diesen Parameter weglassen, wenn Sie Azure AD als Identity Manager verwenden. Fügen Sie den Parameter mit dem Wert `false` hinzu, wenn Sie AD FS verwenden. |

    - JSON-Codeausschnitt:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
          "validateAuthority" : false, 
      },
      "azure.cloud": "AzurePPE"
      ```

10. Speichern Sie die Benutzereinstellungen, und drücken Sie erneut **STRG+UMSCHALT+P**. Wählen Sie **Azure: Sign in to Azure Cloud** (Azure: An Azure-Cloud anmelden). Die neue Option **AzurePPE** wird in der Liste mit den Zielen angezeigt.

11. Wählen Sie **AzurePPE** aus. Die Authentifizierungsseite wird in Ihrem Browser geladen. Melden Sie sich an Ihrem Endpunkt an.

12. Um das erfolgreiche Anmelden an Ihrem Azure Stack Hub-Abonnement zu testen, drücken Sie **STRG+UMSCHALT+P** und wählen **Azure: Select Subscription** (Azure: Abonnement auswählen). Sie können dann prüfen, ob das gewünschte Abonnement verfügbar ist.

## <a name="commands"></a>Befehle

| Azure: Anmelden | Melden Sie sich bei Ihrem Azure-Abonnement an. |
| --- | --- |
| Azure: Anmelden mit Gerätecode | Melden Sie sich an Ihrem Azure-Abonnement mit einem Gerätecode an. Verwenden Sie in Setups, in denen der Befehl „Anmelden“ nicht funktioniert, einen Gerätecode. |
| Azure: Anmelden an der Azure-Cloud | Melden Sie sich an Ihrem Azure-Abonnement in einer der Sovereign Clouds an. |
| Azure: Abmelde- | Melden Sie sich von Ihrem Azure-Abonnement ab. |
| Azure: Auswählen von Abonnements | Wählen Sie die Gruppe mit den Abonnements aus, die Sie verwenden möchten. Die Erweiterung zeigt nur Ressourcen in den gefilterten Abonnements an. |
| Azure: Erstellen eines Kontos | Falls Sie nicht über ein Azure-Konto verfügen, können Sie sich noch heute [](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account)registrieren\$ und 200 USD Guthaben erhalten. |
| Azure: Öffnen von Bash in Cloud Shell | Öffnen Sie ein neues Terminal zur Ausführung von Bash in Cloud Shell. |
| Azure: Öffnen von PowerShell in Cloud Shell | Öffnen Sie ein neues Terminal zur Ausführung von PowerShell in Cloud Shell. |
| Azure: Hochladen in Cloud Shell | Laden Sie eine Datei in Ihr Cloud Shell-Speicherkonto hoch. |

## <a name="next-steps"></a>Nächste Schritte

[Einrichten einer Entwicklungsumgebung in Azure Stack Hub](azure-stack-dev-start.md)
