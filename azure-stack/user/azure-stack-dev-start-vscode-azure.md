---
title: Herstellen einer Verbindung mit Azure Stack per Azure-Kontoerweiterung in Visual Studio Code | Microsoft-Dokumentation
description: Stellen Sie als Entwickler eine Verbindung mit Azure Stack her, indem Sie die Azure-Kontoerweiterung in Visual Studio Code verwenden.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 06/25/2019
ms.openlocfilehash: 9f45e94f26e577f1a47f60b7df24758d7bc88767
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603059"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Herstellen einer Verbindung mit Azure Stack über die Azure-Kontoerweiterung in Visual Studio Code

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie eine Verbindung mit Azure Stack herstellen, indem Sie die Azure-Kontoerweiterung verwenden. Sie müssen Ihre VS Code-Einstellungen (Visual Studio Code) aktualisieren.

VS Code ist ein einfacher Editor zum Erstellen und Debuggen von Web- und Cloudanwendungen. Er wird für ASP.NET Core, Python, NodeJS, Go und andere Entwicklungsarbeit verwendet. Mit der Azure-Kontoerweiterung können Sie eine einzelne Azure-Anmeldung mit Abonnementfilterung für zusätzliche Azure-Erweiterungen verwenden. Die Erweiterung macht die Azure Cloud Shell im VS Code-integrierten Terminal verfügbar. Mit der Erweiterung können Sie eine Verbindung mit Ihrem Azure Stack-Abonnement herstellen, indem Sie sowohl Azure Active Directory (Azure AD) als auch Active Directory-Verbunddienste (AD FS) für Ihren Identitäts-Manager verwenden. Auf diese Weise können Sie sich an Azure Stack anmelden, Ihr Abonnement auswählen und eine neue Befehlszeile in einer Cloud Shell-Instanz öffnen. 

> [!Note]  
> Sie können die Schritte in diesem Artikel für eine AD FS-Umgebung (Active Directory-Verbunddienste) nutzen. Verwenden Sie Ihre AD FS-Anmeldeinformationen und -Endpunkte.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Voraussetzungen für die Azure-Kontoerweiterung

1. Build 1904 der Azure Stack-Umgebung oder höher
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure-Kontoerweiterung](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack-Abonnement](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>Schritte zum Herstellen einer Verbindung mit Azure Stack

1. Führen Sie das **Identity**-Skript aus den Azure Stack-Tools in GitHub aus.

    - Bevor Sie das Skript ausführen, müssen Sie PowerShell installiert und für Ihre Umgebung konfiguriert haben. Weitere Anleitungen finden Sie unter [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md).

    - Anleitungen zum **Identity**-Skript sowie das Skript selbst finden Sie unter [AzureStack-Tools/Identity](https://github.com/Azure/AzureStack-Tools/tree/master/Identity).

2. Öffnen Sie Visual Studio Code.

3. Wählen Sie links in der Ecke die Option **Erweiterungen**.

3. Geben Sie im Suchfeld `Azure Account`ein.

4. Wählen Sie **Azure-Konto** und dann **Installieren**.

      ![Azure Stack Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

5. Starten Sie VS Code neu, um die Erweiterung zu laden.

6. Rufen Sie die Metadaten ab, um eine Verbindung mit Azure Resource Manager in Ihrer Azure Stack-Instanz herzustellen. 
    
    Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Sie Azure-Ressourcen bereitstellen, verwalten und überwachen können.
    - Die Resource Manager-URL für das Azure Stack Development Kit (ASDK) lautet: `https://management.local.azurestack.external/`. 
    - Die Resource Manager-URL für ein integriertes System lautet: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.
    - Fügen Sie Ihrer URL für den Zugriff auf die Metadaten den folgenden Text hinzu: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Die URL zum Abrufen der Metadaten für Ihren Azure Resource Manager-Endpunkt kann beispielsweise wie folgt aussehen: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`.

    Notieren Sie sich den zurückgegebenen JSON-Code. Sie benötigen die Werte für die Eigenschaften `loginEndpoint` und `audiences`.

7. Drücken Sie **STRG+UMSCHALT+P**, und wählen Sie **Preferences: Open User Settings (JSON)** (Einstellungen: Benutzereinstellungen öffnen (JSON)).

8. Aktualisieren Sie im Code-Editor den folgenden JSON-Codeausschnitt mit den Werten für Ihre Umgebung, und fügen Sie den Codeausschnitt dann in den Block mit den Einstellungen ein.

    - Werte:

        | Parameter | BESCHREIBUNG |
        | --- | --- |
        | `tenant-ID` | Der Wert Ihrer [Mandanten-ID](../operator/azure-stack-identity-overview.md) für Azure Stack. |
        | `activeDirectoryEndpointUrl` | Dies ist die URL aus der loginEndpoint-Eigenschaft. |
        | `activeDirectoryResourceId` | Dies ist die URL aus der audiences-Eigenschaft.
        | `resourceManagerEndpointUrl` | Dies ist die Stamm-URL für die Azure Resource Manager-Instanz für Azure Stack. | 

    - JSON-Codeausschnitt:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

9. Speichern Sie die Benutzereinstellungen, und drücken Sie erneut **STRG+UMSCHALT+P**. Wählen Sie **Azure: Sign in to Azure Cloud** (Azure: An Azure-Cloud anmelden). Die neue Option **AzurePPE** wird in der Liste mit den Zielen angezeigt.

10. Wählen Sie **AzurePPE** aus. Die Authentifizierungsseite wird in Ihrem Browser geladen. Melden Sie sich an Ihrem Endpunkt an.

11. Um das erfolgreiche Anmelden an Ihrem Azure Stack-Abonnement zu testen, drücken Sie **STRG+UMSCHALT+P** und wählen **Azure: Select Subscription** (Azure: Abonnement auswählen). Sie können dann prüfen, ob das gewünschte Abonnement verfügbar ist.

## <a name="commands"></a>Befehle

| Azure: Anmelden | Melden Sie sich bei Ihrem Azure-Abonnement an. |
| --- | --- |
| Azure: Anmelden mit Gerätecode | Melden Sie sich an Ihrem Azure-Abonnement mit einem Gerätecode an. Gehen Sie so in Fällen vor, in denen der Befehl „Anmelden“ nicht funktioniert. |
| Azure: Anmelden an der Azure-Cloud | Melden Sie sich an Ihrem Azure-Abonnement in einer der Sovereign Clouds an. |
| Azure: Abmelde- | Melden Sie sich von Ihrem Azure-Abonnement ab. |
| Azure: Auswählen von Abonnements | Wählen Sie die Gruppe mit den Abonnements aus, die Sie verwenden möchten. Die Erweiterung zeigt nur Ressourcen in den gefilterten Abonnements an. |
| Azure: Erstellen eines Kontos | Falls Sie nicht über ein Azure-Konto verfügen, können Sie sich noch heute [registrieren](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) und 200 USD Guthaben erhalten. |
| Azure: Öffnen von Bash in Cloud Shell | Öffnen Sie ein neues Terminal zur Ausführung von Bash in Cloud Shell. |
| Azure: Öffnen von PowerShell in Cloud Shell | Öffnen Sie ein neues Terminal zur Ausführung von PowerShell in Cloud Shell. |
| Azure: Hochladen in Cloud Shell | Laden Sie eine Datei in Ihr Cloud Shell-Speicherkonto hoch. |

## <a name="next-steps"></a>Nächste Schritte

[Einrichten einer Entwicklungsumgebung in Azure Stack](azure-stack-dev-start.md)
