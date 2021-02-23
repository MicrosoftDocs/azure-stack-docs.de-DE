---
title: Verwenden der Bereitstellungsaktion von Azure-Web-Apps mit Azure Stack Hub
description: Verwenden Sie die Bereitstellungsaktion von Azure-Web-Apps, um einen Continuous Integration-/Continuous Deployment-Workflow (CI/CD) auf Azure Stack Hub zu erstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 2/16/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 2/16/2021
ms.openlocfilehash: fec9acb7a3e156a8646aef88c53e681eed9e53da
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655275"
---
# <a name="use-the-azure-web-app-deploy-action-with-azure-stack-hub"></a>Verwenden der Bereitstellungsaktion von Azure-Web-Apps mit Azure Stack Hub

Sie können GitHub Actions einrichten, um eine Web-App auf Ihrer Azure Stack Hub-Instanz bereitzustellen. Dies ermöglicht es Ihnen, Continuous Integration und Continuous Deployment für Ihre App einzurichten. Dieser Artikel hilft Ihnen bei der Einrichtung der automatisierten Bereitstellung mithilfe von GitHub Actions und Azure Stack Hub. Erstellen Sie eine Web-App, und verwenden Sie das Veröffentlichungsprofil, um die Web-App zum Hosten Ihrer App zu erstellen.

GitHub Actions sind Workflows, die aus Aktionen bestehen, die die Automatisierung direkt in Ihrem Coderepository ermöglichen. Sie können die Workflows mit Ereignissen in Ihrem GitHub-Entwicklungsprozess auslösen. Sie können gängige DevOps-Automatisierungsaufgaben festlegen, wie z. B. Tests, Bereitstellung und Continuous Integration.

Dieser Beispielworkflow beinhaltet:
- Anweisungen zum Erstellen und Überprüfen Ihres Dienstprinzipals (SPN)
- Anweisungen zum Erstellen des Veröffentlichungsprofils Ihrer Web-App
- Hinzufügen eines laufzeitspezifischen Workflows
- Hinzufügen eines übereinstimmenden Workflows mit der Web-App-Bereitstellung
## <a name="get-service-principal"></a>Abrufen eines Dienstprinzipals

Ein SPN stellt rollenbasierte Anmeldeinformationen zur Verfügung, sodass Prozesse außerhalb von Azure Verbindungen mit Ressourcen herstellen und mit diesen interagieren können. Sie benötigen für die Verwendung mit Ihren GitHub Actions-Instanzen einen SPN mit Zugriff für Mitwirkende und den in diesen Anweisungen angegebenen Attributen.

Als Benutzer von Azure Stack Hub verfügen Sie nicht über die Berechtigung zum Erstellen des SPN. Sie müssen diesen Prinzipal von Ihrem Cloudbetreiber anfordern. Die Anweisungen werden hier bereitgestellt, um den SPN zu erstellen, wenn Sie ein Cloudbetreiber sind. Wenn Sie Entwickler sind, können Sie den von einem Cloudbetreiber bereitgestellten SPN überprüfen.

Der Cloudbetreiber muss den SPN mithilfe der Azure CLI erstellen.

Die folgenden Codeausschnitte wurden für einen Windows-Computer unter Verwendung der PowerShell-Eingabeaufforderung mit Azure CLI geschrieben. Wenn Sie die CLI auf einem Linux-Computer mit der Bash verwenden, entfernen Sie entweder die Zeilenerweiterung, oder ersetzen Sie sie durch einen `\`.

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

    Wenn Sie über keine Cloudbetreiberberechtigungen verfügen, können Sie sich auch mit dem von Ihrem Cloudbetreiber bereitgestellten SPN anmelden. Sie benötigen die Client-ID, das Geheimnis und Ihre Mandanten-ID. Mit diesen Werten können Sie die folgenden Azure CLI-Befehle verwenden, um das JSON-Objekt zu erstellen, das Sie Ihrem GitHub-Repository als Geheimnis hinzufügen können.

    ```azurecli  
    az login --service-principal -u "<client-id>" -p "<secret>" --tenant "<tenant-ID>" --allow-no-subscriptions
    az account show --sdk-auth
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

## <a name="create-the-web-app-publish-profile"></a>Erstellen des Veröffentlichungsprofils der Web-App

### <a name="open-the-create-web-app-blade"></a>Öffnen Sie das Blatt „Web-App erstellen“.

1. Melden Sie sich beim Azure Stack Hub-Portal an.
1. Wählen Sie **Ressource erstellen** > **Web + Mobil** > **Web-Apps** aus.
    ![Erstellen einer Web-App in Azure Stack Hub](\media\ci-cd-github-action-webapp\create-web-app.png)
### <a name="to-create-your-web-app"></a>So erstellen Sie Ihre Web-App

1. Wählen Sie Ihr **Abonnement** aus.
1. Erstellen Sie eine **Ressourcengruppe**, oder wählen Sie eine Ressourcengruppe aus.
1. Geben Sie den **Namen** Ihrer App ein. Der Name der App wird in der URL für Ihre App angezeigt, zum Beispiel `yourappname.appservice.<region>.<FQDN>`.
1. Wählen Sie den **Runtimestapel** für Ihre App aus Die Runtime muss mit dem Workflow identisch sein, den Sie für das Veröffentlichungsprofil verwenden.
1. Wählen Sie das **Betriebssystem** aus, das Ihre Runtime und Ihre App hosten soll.
1. Wählen Sie die **Region** für Ihre Azure Stack Hub-Instanz aus, oder geben Sie sie ein.
1. Wählen Sie den Plan basierend auf Ihrer Azure Stack Hub-Instanz, der Region und des Betriebssystems der App aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Überprüfen Sie Ihre Web-App. Klicken Sie auf **Erstellen**.
    ![Überprüfen der Web-App in Azure Stack Hub](\media\ci-cd-github-action-webapp\review-azure-stack-hub-web-app.png)
1. Wählen Sie **Zu Ressource wechseln** aus.
    ![Abrufen des Veröffentlichungsprofils in Azure Stack Hub](\media\ci-cd-github-action-webapp\get-azure-stack-hub-web-app-publish-profile.png)
1. Klicken Sie auf **Veröffentlichungsprofil abrufen**. Ihr Veröffentlichungsprofil wird heruntergeladen und hat den Namen `<yourappname>.PublishSettings`. Die Datei enthält eine XML-Datei mit den Zielwerten Ihrer Web-App.
1. Speichern Sie Ihr Veröffentlichungsprofil, damit Sie darauf zugreifen können, wenn Sie die Geheimnisse für Ihr Repository erstellen.

## <a name="add-your-secrets-to-the-repository"></a>Hinzufügen der Geheimnisse zum Repository

Sie können GitHub-Geheimnisse verwenden, um vertrauliche Informationen für die Verwendung in Ihren Aktionen zu verschlüsseln. Sie erstellen ein Geheimnis, das Ihren SPN erhält, und ein weiteres Geheimnis für das Veröffentlichungsprofil Ihrer Web-App. So kann die Aktion sich bei Ihrer Azure Stack Hub-Instanz anmelden und Ihre App auf dem Web-App-Ziel erstellen.

1. Öffnen oder erstellen Sie ein GitHub-Repository. Wenn Sie Anweisungen zum Erstellen eines Repositorys in GitHub benötigen, finden Sie [Anweisungen in der GitHub-Dokumentation](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Klicken Sie auf **Einstellungen**.
1. Wählen Sie **Secrets** (Geheimnisse) aus.
1. Wählen Sie **New repository secret** (Neues Repositorygeheimnis) aus.
    ![Hinzufügen Ihres GitHub Actions-Geheimnisses](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Geben Sie Ihrem Geheimnis den Namen `AZURE_CREDENTIALS`.
1. Fügen Sie das JSON-Objekt ein, das Ihren SPN darstellt.
1. Klicken Sie auf **Add secret** (Geheimnis hinzufügen).
1. Wählen Sie **New repository secret** (Neues Repositorygeheimnis) aus.
1. Geben Sie Ihrem Geheimnis den Namen `AZURE_WEBAPP_PUBLISH_PROFILE`.
1. Öffnen Sie Ihre `<yourappname>.PublishSettings` in einem Text-Editor, und kopieren Sie den XML-Code in das Repositorygeheimnis.
1. Klicken Sie auf **Add secret** (Geheimnis hinzufügen).

## <a name="add-a-runtime-workflow"></a>Hinzufügen eines Repositoryworkflows

1. Wählen Sie eine Vorlage aus der Tabelle für Ihre Web-App-Runtime aus.

    | Laufzeit | Vorlage |
    | --- | --- |
    | DotNet | [dotnet.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/asp.net-core-webapp-on-azure.yml) |
    | NodeJS | [node.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/node.js-webapp-on-azure.yml) |
    | Java | [java_jar.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-jar-webapp-on-azure.yml) |
    | Java | [java_war.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-war-webapp-on-azure.yml) |
    | Python | [python.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/python-webapp-on-azure.yml) |
    | PHP | [php.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/php-webapp-on-azure.yml) |
    | Docker | [docker.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/docker-webapp-container-on-azure.yml) |

2. Platzieren Sie die Vorlage für das Workflowverzeichnis der GitHub-Aktion in Ihrem Projektrepository: `.github/workflows/<runtime.yml>` Ihr Workflowverzeichnis enthält zwei Workflows.

## <a name="add-the-web-app-deploy-action"></a>Hinzufügen der Bereitstellungsaktion für die Web-App

Erstellen Sie einen zweiten Workflow mithilfe der YAML-Datei in diesem Abschnitt. In diesem Beispiel stellen Sie eine Python-Web-App bereit. Sie müssen eine Setupaktion basierend auf Ihren Workflow auswählen. Sie finden nach den zum Erstellen der Aktion verwendeten Schritte die Verweise auf die Einrichtung von Aktionen für verschiedene Runtimes in der Tabelle, [Setupaktionen für verschiedene Runtimes](#setup-actions-for-different-runtimes).

### <a name="example-github-action-workflow"></a>Beispiel für einen GitHub-Aktionsworkflow

1. Öffnen Sie Ihr GitHub-Repository. Wenn Ihre Ressourcen für die Web-App-Anwendung noch nicht hinzugefügt haben, tun Sie dies jetzt. In diesem Beispiel wird das Beispiel [Python Flask Hello World](https://github.com/Azure-Samples/python-docs-hello-world) verwendet, und die Python-Dateien `.gitignore`, `app.py` und `requirements.txt` wurden hinzugefügt.

    ![Beispiel des Repositorys mit Python Flask über Azure Stack Hub](\media\ci-cd-github-action-webapp\example-of-repo.png)
1. Wählen Sie **Actions** aus.
1. Klicken Sie auf **New workflow** (Neuer Workflow).
    - Wenn dies Ihr erster Workflow ist, wählen Sie **set up a workflow yourself** (selbst einen Workflow einrichten) unter **Choose a workflow template** (Workflowvorlage auswählen) aus.
    - Wenn Sie über vorhandene Workflows verfügen, wählen Sie **New workflow** > **Set up a workflow yourself** (Neuer Workflow > Selbst einen Workflow einrichten) aus.

4. Benennen Sie die Datei im Pfad `workflow.yml`.
5. Kopieren Sie die yml-Datei des Workflows, und fügen Sie sie ein.
    ```yaml  
    # .github/workflows/worfklow.yml
    on: push

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout Github Action' 
          uses: actions/checkout@master
    
        - name: Setup Python 3.6
          uses: actions/setup-node@v1
          with:
            python-version: '3.6'
        - name: 'create a virtual environment and install dependencies'
          run: |
            python3 -m venv .venv
            source .venv/bin/activate
            pip install -r requirements.txt
    
        - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with:
            app-name: <YOURAPPNAME>
            publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
    ```

6. Im „workflow.yaml“-Update `<YOURAPPNAME>` mit Ihrem App-Namen.
7. Klicken Sie auf **Start commit** (Commit starten).
8. Fügen Sie den Titel des Commits und optionale Details hinzu, und wählen Sie dann **Commit new file** (Neue Datei committen) aus.


### <a name="setup-actions-for-different-runtimes"></a>Setupaktionen für verschiedene Runtimes

Verwenden Sie zum Erstellen von App-Code in einer bestimmten sprachbasierten Umgebung die folgenden Setupaktionen:

|  Laufzeit | Setupaktionen |
|------------|---------|
| DotNet     | [Setup DotNet](https://github.com/actions/setup-dotnet) |
| NodeJS     | [Setup Node](https://github.com/actions/setup-node) |
| Java     | [Setup Java  ](https://github.com/actions/setup-java) |
| Python     | [Setup Python ](https://github.com/actions/setup-python) |
| Docker | [docker-login ](https://github.com/Azure/docker-login) |

Sobald die Anmeldeaktion abgeschlossen ist, können die nächsten Aktionen im Workflow Aufgaben wie das Erstellen, Kennzeichnen und Pushen von Containern durchführen. Weitere Informationen finden Sie in der Dokumentation zur [Azure-Web-App-Aktion](https://github.com/marketplace/actions/azure-webapp).
## <a name="trigger-your-deployment"></a>Auslösen der Bereitstellung

Überprüfen Sie nach der Ausführung, ob die Aktion erfolgreich ausgeführt wurde.

1. Öffnen Sie Ihr GitHub-Repository. Sie können den Workflow durch Pushübertragung in das Repository auslösen.
1. Wählen Sie **Actions** aus.
1. Wählen Sie den Namen des Commits unter **All workflows** (Alle Workflows) aus. Beide Workflows haben ihren Status protokolliert.
    ![Statusüberprüfung für Ihre GitHub-Aktion](\media\ci-cd-github-action-webapp\workflow-success.png)
1. Wählen Sie den Namen des Auftrags für die Bereitstellung `.github/workflows/workflow.yml` aus.
1. Klappen Sie die Abschnitte auf, um die Rückgabewerte für Ihre Workflowaktionen zu überprüfen. Suchen Sie nach der URL für Ihre bereitgestellte Web-App.
    ![Suchen nach Ihrer Web-App-URL für Azure Stack Hub](\media\ci-cd-github-action-webapp\review-cli-log-and-get-url.png)
1. Öffnen Sie einen Webbrowser, und laden Sie die URL.
## <a name="next-steps"></a>Nächste Schritte

- Weitere Aktionen finden Sie im [GitHub Marketplace](https://github.com/marketplace).
- Erfahren Sie mehr über [Häufige Bereitstellungen für Azure Stack Hub](azure-stack-dev-start-deploy-app.md)  
- Informieren Sie sich über [Verwenden von Azure Resource Manager-Vorlagen in Azure Stack Hub](azure-stack-arm-templates.md)  
- Arbeiten Sie das DevOps-Muster für hybride Clouds unter [DevOps-Muster](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline) durch.
