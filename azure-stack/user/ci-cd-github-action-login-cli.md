---
title: Verwenden der Azure-Anmeldeaktion mit Azure CLI und PowerShell auf Azure Stack Hub
description: Verwenden Sie die Azure-Anmeldeaktion mit Azure CLI und PowerShell, um einen Continuous Integration-/Continuous Deployment-Workflow (CI/CD) auf Azure Stack Hub zu erstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 1421917f870d09d61f665a2cee6eb9b617ae75f3
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187349"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>Verwenden der Azure-Anmeldeaktion mit Azure CLI und PowerShell auf Azure Stack Hub

Sie können GitHub Actions einrichten, um sich bei ihrer Azure Stack Hub-Instanz anzumelden, PowerShell auszuführen und dann ein Azure CLI-Skript auszuführen. Dies kann als Grundlage für einen Continuous Integration-/Continuous Deployment-Workflow (CI/CD) für Ihre Lösung mit Azure Stack Hub dienen. Mit diesem Workflow können Sie die Erstellung, das Testen und die Bereitstellung Ihrer Lösung automatisieren, sodass Sie sich auf das Schreiben von Code konzentrieren können. Durch Hinzufügen einiger weiterer Aktionen könnten Sie diesen Workflow beispielsweise zusammen mit einer Azure Resource Manager-Vorlage verwenden, um eine VM bereitzustellen, ein Anwendungsrepository zu validieren und dann jedes Mal, wenn Sie einen Merge in einem bestimmten Branch in GitHub ausführen, eine Anwendung auf dieser VM bereitzustellen. Aber zunächst mal hilft Ihnen dieser Artikel, sich bei GitHub Actions und dem Azure Stack Hub zurecht zu finden.

GitHub Actions sind Workflows, die aus Aktionen bestehen, die Automatisierung direkt in Ihrem Coderepository aktivieren. Sie können die Workflows mit Ereignissen in Ihrem GitHub-Entwicklungsprozess auslösen. Sie können gängige DevOps-Automatisierungsaufgaben ausführen, wie z. B. Tests, Bereitstellung und Continuous Integration.

Zum Verwenden von GitHub Actions mit Azure Stack Hub müssen Sie einen Dienstprinzipal (SPN) mit spezifischen Anforderungen verwenden. In diesem Artikel erstellen Sie einen *selbstgehosteten Runner*. GitHub ermöglicht es Ihnen, in Ihren GitHub Actions jeden beliebigen Computer zu verwenden, der von GitHub erreicht werden kann. Sie können einen virtuellen Computer (VM) als Ihren Runner in Azure, in Azure Stack Hub oder anderswo erstellen.

Dieser Beispielworkflow beinhaltet:
- Anweisungen zum Erstellen und Prüfen Ihres SPN.
- Konfigurieren eines Windows 2016 Server Core-Computers als selbstgehosteten GitHub Actions-Runner für die Verwendung mit Azure Stack Hub.
- Einen Workflow, der Folgendes verwendet:
    - Die Azure-Anmeldeaktion
    - Die PowerShell-Skriptaktion

### <a name="azure-stack-hub-github-actions"></a>Azure Stack Hub GitHub Actions

Das folgende Diagramm zeigt die verschiedenen Umgebungen und ihre Beziehungen.

![Azure Stack Hub Github Action](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg) Teilweise Verwendung des selbstgehosteten Runners:

- Auf GitHub gehostete GitHub Actions
- Auf Azure gehosteter selbstgehosteter Runner
- Azure Stack Hub

Eine Einschränkung bei der Verwendung von GitHub Actions mit Azure Stack Hub besteht darin, dass für den Prozess die Verwendung eines mit dem Web verbundenen Azure Stack Hub erforderlich ist. Der Workflow wird in einem GitHub-Repository ausgelöst. Sie können wahlweise Azure Active Directory (Azure AD) oder Active Directory Federated Services (AD FS) als Identitätsanbieter verwenden.

Es liegt zwar außerhalb des Themas dieses Artikels, aber ihr selbstgehosteter Runner kann auch ein virtuelles privates Netzwerk zum Herstellen von Verbindungen mit Ihrem Azure Stack Hub hinter einer Firewall verwenden.

## <a name="get-service-principal"></a>Abrufen eines Dienstprinzipals

Ein SPN stellt rollenbasierte Anmeldeinformationen zur Verfügung, sodass Prozesse außerhalb von Azure Verbindungen mit Ressourcen herstellen und mit diesen interagieren können. Sie benötigen für die Verwendung mit Ihren GitHub Actions einen SPN mit Zugriff für Mitwirkende und den in diesen Anweisungen angegebenen Attributen.

Als Benutzer von Azure Stack Hub verfügen Sie nicht über die Berechtigung zum Erstellen des SPN. Sie müssen diesen Prinzipal von Ihrem Cloudbetreiber anfordern. Die Anweisungen werden hier bereitgestellt, damit Sie den SPN erstellen können, wenn Sie ein Cloudbetreiber sind. Alternativ können Sie den SPN validieren, wenn Sie ein Entwickler sind, der einen von einem Cloudbetreiber bereitgestellten SPN in seinem Workflow verwendet.

Der Cloudbetreiber muss den SPN mithilfe der Azure CLI erstellen.

Die folgenden Codeausschnitte wurden für einen Windows-Computer unter Verwendung der PowerShell-Eingabeaufforderung mit Azure CLI geschrieben. Wenn Sie CLI auf einem Linux-Computer mit der Bash verwenden, entfernen Sie entweder die Zeilenerweiterung, oder ersetzen Sie sie durch einen `\`.

1. Bereiten Sie die Werte der folgenden Parameter vor, die zum Erstellen des SPN verwendet werden:

    | Parameter | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    endpoint-resource-manager | "https://management.orlando.azurestack.corp.microsoft.com"  | Der Ressourcenverwaltungsendpunkt. |
    suffix-storage-endpoint | „orlando.azurestack.corp.microsoft.com“  | Das Endpunktsuffix für Speicherkonten. |
    suffix-keyvault-dns | „.vault.orlando.azurestack.corp.microsoft.com“  | Das DNS-Suffix des Key Vault-Diensts. |
    endpoint-active-directory-graph-resource-id | "https://graph.windows.net/"  | Active Directory-Ressourcen-ID |
    endpoint-sql-management | https://notsupported  | Der SQL Server-Verwaltungsendpunkt. Auf `https://notsupported` |
    Profil | 2019-03-01-hybrid | Das Profil, das für diese Cloud verwendet werden soll. |

2. Öffnen Sie das Befehlszeilentool, z B. Windows PowerShell oder Bash, und melden Sie sich an. Verwenden Sie den folgenden Befehl:

    ```azurecli  
    az login
    ```

3. Verwenden Sie den Befehl `register` für eine neue Umgebung oder den Befehl `update`, wenn Sie eine vorhandene Umgebung verwenden. Verwenden Sie den folgenden Befehl.

    ```azurecli  
    az cloud register `
        -n "AzureStackUser" `
        --endpoint-resource-manager "https://management.<local>.<FQDN>" `
        --suffix-storage-endpoint ".<local>.<FQDN>" `
        --suffix-keyvault-dns ".vault.<local>.<FQDN>" `
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" `
        --endpoint-sql-management https://notsupported  `
        --profile 2019-03-01-hybrid
    ```

4. Rufen Sie Ihre Abonnement-ID und die Ressourcengruppe ab, die Sie für den SPN verwenden möchten.

5. Erstellen Sie den SPN mit dem folgenden Befehl mit der Abonnement-ID und der Ressourcengruppe:

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

6. Überprüfen Sie das resultierende JSON-Objekt. Sie verwenden das JSON-Objekt, um Ihr Geheimnis in Ihrem GitHub-Repository zu erstellen, das Ihre Aktion enthält. Das JSON-Objekt sollte die folgenden Attribute aufweisen:

    ```json
    {
      "clientId": <Application ID for the SPN>,
      "clientSecret": <Client secret for the SPN>,
      "subscriptionId": <Subscription ID for the SPN>,
      "tenantId": <Tenant ID for the SPN>,
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com/",
      "resourceManagerEndpointUrl": "https://management.<FQDN>",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://notsupported",
      "galleryEndpointUrl": "https://providers.<FQDN>:30016/",
      "managementEndpointUrl": "https://management.<FQDN>"
    }
    ```

## <a name="add-service-principal-to-repository"></a>Hinzufügen eines Dienstprinzipals zum Repository

Sie können GitHub-Geheimnisse verwenden, um vertrauliche Informationen für die Verwendung in Ihren Aktionen zu verschlüsseln. Sie erstellen ein Geheimnis, das Ihren SPN aufnimmt, sodass die Aktion sich bei Ihrer Azure Stack Hub-Instanz anmelden kann.

> [!WARNING]  
> GitHub empfiehlt Ihnen, keine selbstgehosteten Runner mit öffentlichen Repositorys zu verwenden. Forks Ihres öffentlichen Repositorys können möglicherweise gefährlichen Code auf Ihrem selbstgehosteten Runnercomputer ausführen, indem sie einen Pull Request erstellen, der den Code in einem Workflow ausführt. Weitere Informationen finden Sie unter „[Informationen zu selbstgehosteten Runnern](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories)“.

1. Öffnen oder erstellen Sie ein GitHub-Repository. Wenn Sie Anweisungen zum Erstellen eines Repositorys in GitHub benötigen, finden Sie [Anweisungen in der GitHub-Dokumentation](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Richten Sie Ihr Repository als privat ein.
    1. Wählen Sie **Settings**(Einstellungen)  > **Change repository visibility** (Sichtbarkeit des Repositorys ändern) aus.
    1. Wählen Sie **Make private** (Als privat festlegen) aus.
    1. Geben Sie den Namen Ihres Repositorys ein.
    1. Wählen Sie **I understand, change the repository visibility** (Ich habe verstanden, Sichtbarkeit des Repositorys ändern).
1. Klicken Sie auf **Einstellungen**.
1. Wählen Sie **Secrets** (Geheimnisse) aus.
1. Wählen Sie **New repository secret** (Neues Repositorygeheimnis) aus.
    ![Hinzufügen Ihres GitHub Actions-Geheimnisses](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Geben Sie Ihrem Geheimnis den Namen `AZURE_CREDENTIALS`.
1. Fügen Sie das JSON-Objekt ein, das Ihren SPN darstellt.
1. Klicken Sie auf **Add secret** (Geheimnis hinzufügen).

## <a name="create-your-vm-and-install-prerequisites"></a>Erstellen Ihrer VM und Installieren der Voraussetzungen

1. Erstellen Sie Ihren selbstgehosteten Runner. 

    Mit diesen Anweisungen wird ein Runner als Windows-VM in Azure erstellt. Wenn Sie eine Verbindung zu Ihrem Azure Stack Hub herstellen müssen, der in einem Rechenzentrum gehostet wird, benötigen Sie möglicherweise eine VPN-Verbindung. Anweisungen zum Aktivieren der Verbindung finden Sie im Abschnitt [Install Azure Stack Hub Tools on your self-hosted runner](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner) (Installieren von Azure Stack-Hubtools auf Ihrem selbstgehosteten Runner); hierfür wird möglicherweise eine VPN-Verbindung benötigt.
    - Anweisungen zum Erstellen einer Windows VM in Azure finden Sie unter [Schnellstart: Erstellen eines virtuellen Windows-Computers im Azure-Portal](/azure/virtual-machines/windows/quick-create-portal). Wenn Sie diese Anweisungen befolgen, installieren Sie Windows Server 2016 Core.
    - Anweisungen zum Erstellen einer Windows-VM in Azure Stack Hub finden Sie unter [Schnellstart: Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack Hub-Portal](./azure-stack-quick-windows-portal.md) befolgen. Wenn Sie diese Anweisungen befolgen, installieren Sie Windows Server 2016 Core.
1. Verwenden Sie eine Remoteverbindung, um mithilfe der Server-IP-Adresse, des Benutzernamens und des Kennworts, die Sie beim Erstellen des Computers definiert haben, eine Verbindung mit Ihrem Windows 2016-Server herzustellen.
1. Installieren Sie Chocolatey. Chocolatey ist ein Paket-Manager für Windows, mit dem Sie Abhängigkeiten von der Befehlszeile aus installieren und verwalten können. Geben Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten Folgendes ein:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. Installieren Sie PowerShell Core. Geben Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten Folgendes ein:
    ```powershell  
    choco install powershell-core
    ```
1. Installieren Sie Azure CLI. Geben Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten Folgendes ein:
    ```powershell  
    choco install azure-cli
    ```
1. Installieren Sie Azure Stack Hub PowerShell. Geben Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten Folgendes ein:
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    Weitere Informationen zur Verwendung der Az-Module für Azure Stack Hub finden Sie unter [Installieren des PowerShell Az-Moduls für Azure Stack Hub](../operator/powershell-install-az-module.md).
7. Starten Sie den Computer neu. Geben Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten Folgendes ein:
    ```powershell  
    shutdown /r
    ```
8. Fügen Sie den Computer Ihrem GitHub-Repository als selbstgehosteten Runner hinzu. Anweisungen zum Hinzufügen eines selbstgehosteten Runners finden Sie in der GitHub-Dokumentation. Weitere Informationen finden Sie unter [Hinzufügen von selbstgehosteten Runnern](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners).

    ![Der Runner lauscht](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. Wenn Sie fertig sind, vergewissern Sie sich, dass der Dienst ausgeführt wird und nach Ihrem Dienst lauscht. Bestätigen Sie Ihre Überprüfung, indem Sie `/run.cmd` aus dem Verzeichnis des Runners ausführen.

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>Optional: Installieren von Azure Stack Hub-Tools auf einem selbstgehosteten Runner

Die Anweisungen in diesem Artikel erfordern keinen Zugriff auf die [Azure Stack Hub-Tools](../operator/azure-stack-powershell-download.md?tabs=az). Wenn Sie jedoch einen eigenen Workflow entwickeln, müssen Sie die Tools möglicherweise verwenden. Mithilfe der folgenden Anweisungen können Sie die Tools auf Ihrem selbstgehosteten Windows-Runner installieren. Weitere Anweisungen zum Installieren von Azure Stack Hub-Tools finden Sie unter [Herunterladen von Azure Stack Hub-Tools von GitHub](../operator/azure-stack-powershell-download.md?tabs=az). Bei diesen Anweisungen wird davon ausgegangen, dass Sie den Paket-Manager Chocolatey installiert haben.

1. Installieren Sie Git.
    ```powershell  
    choco install git
    ```

2. Geben Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten Folgendes ein:
    ```powershell
    # Change directory to the root directory.
    cd \
    
    # Download the tools archive.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
    invoke-webrequest `
      https://github.com/Azure/AzureStack-Tools/archive/az.zip `
      -OutFile az.zip
    
    # Expand the downloaded files.
    expand-archive az.zip `
      -DestinationPath . `
      -Force
    
    # Change to the tools directory.
    cd AzureStack-Tools-az
    ```

3. Wenn für Ihren Runner das Herstellen einer Verbindung mit ihrer Azure Stack Hub-Instanz erforderlich ist, können Sie PowerShell verwenden. Die Anweisungen dazu finden Sie im Artikel [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell](../operator/azure-stack-powershell-configure-admin.md?tabs=az1%2Caz2%2Caz3).

## <a name="create-a-self-hosted-runner"></a>Erstellen eines selbstgehosteten Runners

Sie können in der GitHub-Dokumentation einen selbstgehosteten Runner einrichten. Ein selbstgehosteter Runner kann auf jedem Computer ausgeführt werden, der eine Verbindung mit GitHub herstellen kann. Sie können sich für den Einsatz eines selbstgehosteten Runners entscheiden, wenn Sie in Ihrem Automatisierungsworkflow eine Automatisierungsaufgabe verwenden, für die umfangreiche Abhängigkeiten, spezielle Lizenzierungsanforderungen, wie etwa ein USB-Dongle für eine Softwarelizenz, oder andere computer- oder softwarespezifische Voraussetzungen erforderlich sind. Bei Ihrem Computer kann es sich um einen physischen Computer, eine VM oder einen Container handeln. Sie können den Runner in Ihrem Datacenter oder in der Cloud platzieren.

In diesem Artikel verwenden Sie eine in Azure gehostete Windows-VM, die mit für Azure Stack Hub spezifischen PowerShell-Anforderungen konfiguriert wird.

Anweisungen zum Einrichten, Konfigurieren und Verbinden Ihres selbstgehosteten Runners mit Ihrem Repository finden Sie in der GitHub-Dokumentation „[Informationen zu selbstgehosteten Runnern](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners)“.

![Selbstgehosteter Runner mit hergestellter Verbindung](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

Notieren Sie sich den Namen und die Tags Ihres selbstgehosteten Runners. Der Workflow in diesem Artikel ruft ihn mit dem-Tag `self-hosted` auf.

## <a name="add-the-workflow-to-your-repository"></a>Hinzufügen des Workflows zu Ihrem Repository

Erstellen Sie einen neuen Workflow mithilfe des yaml-Codes in diesem Abschnitt.

1. Öffnen Sie Ihr GitHub-Repository.
2. Wählen Sie **Actions** aus.
3. Erstellen Sie einen neuen Workflow.
    - Wenn dies Ihr erster Workflow ist, wählen Sie **set up a workflow yourself** (selbst einen Workflow einrichten) unter **Choose a workflow template** (Workflowvorlage auswählen) aus.
    - Wenn Sie über vorhandene Workflows verfügen, wählen Sie **New workflow** > **Set up a workflow yourself** (Neuer Workflow > Selbst einen Workflow einrichten) aus.

4. Benennen Sie die Datei im Pfad `workflow.yml`.
5. Kopieren Sie die yml-Datei des Workflows, und fügen Sie sie ein.
    ```yaml  
    on: [push]
    
    env:
      ACTIONS_ALLOW_UNSECURE_COMMANDS: 'true'
     
    jobs: 
      azurestack-test:
        runs-on: self-hosted
        steps:
    
          - name: Login to AzureStack with Az Powershell
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: true
          
          - name: Run Az PowerShell Script Against AzureStack
            uses: azure/powershell@v1
            with:
              azPSVersion: '3.1.0'
              inlineScript: |
                hostname
                Get-AzContext
                Get-AzResourceGroup
          
          - name: Login to AzureStack with CLI
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: false
          
          - name: Run Azure CLI Script Against AzureStack
            run: |
              hostname
              az group list --output table
    ```
6. Klicken Sie auf **Start commit** (Commit starten).
7. Fügen Sie den Titel des Commits und optionale Details hinzu, und wählen Sie dann **Commit new file** (Neue Datei committen) aus.

Überprüfen Sie nach der Ausführung, ob die Aktion erfolgreich ausgeführt wurde.

1. Öffnen Sie Ihr GitHub-Repository. Sie können den Workflow durch Pushübertragung in das Repository auslösen.
1. Wählen Sie **Actions** aus.
1. Wählen Sie den Namen des Commits unter **All workflows** (Alle Workflows) aus.

    ![Überprüfen der Zusammenfassung des Commits](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. Wählen Sie den Namen des Auftrags aus, **azurestack-test**.

    ![Überprüfen der Commitdetails](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. Klappen Sie die Abschnitte auf, um die Rückgabewerte für Ihre PowerShell- und CLI-Befehle zu überprüfen.

Anmerkungen zur Workflowdatei und der Aktion:

- Der Workflow enthält einen einzelnen Auftrag mit dem Namen `azurestack-test`.
- Der Workflow wird durch ein Pushereignis ausgelöst.
- Die Aktion verwendet einen selbstgehosteten Runner, der im Repository eingerichtet wurde, und wird über die Bezeichnung des Runners im Workflow mithilfe dieser Zeile aufgerufen: `runs on: self-hosted`.
- Der Workflow enthält drei Aktionen:
- Die erste Aktion ruft die Azure-Anmeldeaktion für die Anmeldung bei PowerShell auf. Mit GitHub Actions für Azure können Sie Workflows erstellen, die Sie in Ihrem Repository zum Erstellen, Testen, Verpacken, Veröffentlichen und Bereitstellen in Azure einrichten können. Diese Aktion verwendet Ihre Anmeldeinformationen für den Azure Stack-SPN, um eine Verbindung mit Ihrer Azure Stack Hub-Umgebung herzustellen und eine Sitzung einzurichten. Weitere Informationen zum Verwenden der Aktion in GitHub finden Sie unter [Azure Login Action](https://github.com/marketplace/actions/azure-login) (Azure-Anmeldeaktion).
- Die zweite Aktion verwendet Azure PowerShell. Die Aktion verwendet die Az PowerShell-Module und funktioniert sowohl in Government- als auch in Azure Stack Hub-Clouds. Überprüfen Sie nach dem Ausführen dieses Workflows, Ihren Auftrag, um zu überprüfen, ob das Skript die Ressourcengruppen in Ihrer Azure Stack Hub-Umgebung erfasst hat. Weitere Informationen finden Sie unter [Azure PowerShell Action](https://github.com/marketplace/actions/azure-powershell-action) (Azure PowerShell-Aktion).
- Die dritte Aktion verwendet Azure CLI zum Anmelden und Herstellen einer Verbindung mit Ihrem Azure Stack Hub, um Ressourcengruppen zu erfassen. Weitere Informationen finden Sie unter [Azure CLI Action](https://github.com/marketplace/actions/azure-cli-action) (Azure CLI-Aktion).
- Weitere Informationen zum Arbeiten mit GitHub-Aktionen und selbstgehosteten Runnern finden Sie in der Dokumentation zu [GitHub Actions](https://github.com/features/actions).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Aktionen finden Sie im [GitHub Marketplace](https://github.com/marketplace).
- Erfahren Sie mehr über [Häufige Bereitstellungen für Azure Stack Hub](azure-stack-dev-start-deploy-app.md)  
- Informieren Sie sich über [Verwenden von Azure Resource Manager-Vorlagen in Azure Stack Hub](azure-stack-arm-templates.md)  
- Arbeiten Sie das DevOps-Muster für hybride Clouds unter [DevOps-Muster](/hybrid/app-solutions/pattern-cicd-pipeline) durch.
