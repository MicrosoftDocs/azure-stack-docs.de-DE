---
title: Problembehandlung bei der AKS-Engine in Azure Stack | Microsoft-Dokumentation
description: In diesem Artikel werden Schritte zur Problembehandlung für die AKS-Engine in Azure Stack beschrieben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femilav
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: aed53295b7c1748abd8ab3bd2862043d7d69e4b8
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310341"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack"></a>Problembehandlung bei der AKS-Engine in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Beim Bereitstellen oder Arbeiten mit der AKS-Engine in Azure Stack tritt möglicherweise ein Problem auf. In diesem Artikel werden die Schritte zur Problembehandlung bei der Bereitstellung der AKS-Engine, zum Sammeln von Informationen über Ihre AKS-Engine, zum Erfassen von Kubernetes-Protokollen, zum Überprüfen der Fehlercodes für die benutzerdefinierte Skripterweiterung und die Anweisungen zum Öffnen eines GitHub-Problems für die AKS-Engine behandelt.

## <a name="troubleshoot-the-aks-engine-install"></a>Problembehandlung bei der Installation der AKS-Engine

### <a name="try-gofish"></a>Ausprobieren von GoFish

Wenn bei den vorherigen Installationsschritten Fehler auftreten, können Sie die AKS-Engine mit dem GoFish-Paket-Manager installieren. [GoFish](https://gofi.sh) ist nach eigener Darstellung ein plattformübergreifendes Homebrew.

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>Installieren der AKS-Engine mit GoFish unter Linux

Installieren Sie GoFish von der [Installationsseite](https://gofi.sh/#install).

1. Führen Sie an einer Bash-Eingabeaufforderung den folgenden Befehl aus:

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  Führen Sie den folgenden Befehl zum Installieren der AKS-Engine mit GoFish aus:

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>Installieren der AKS-Engine mit GoFish unter Windows

Installieren Sie GoFish von der [Installationsseite](https://gofi.sh/#install).

1. Führen Sie den folgenden Befehl an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten aus:

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  Führen Sie den folgenden Befehl in derselben Sitzung zum Installieren der AKS-Engine mit GoFish aus:

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>Checkliste für häufige Bereitstellungsprobleme

Wenn beim Bereitstellen eines Kubernetes-Clusters mit der AKS-Engine Fehler auftreten, können Sie Folgendes überprüfen:

1.  Verwenden Sie die korrekten Dienstprinzipal-Anmeldeinformationen (SPN)?
2.  Hat der SPN die Rolle „Mitwirkende“ für das Azure Stack-Abonnement?
3. Verfügen Sie über ein ausreichend großes Kontingent in Ihrem Azure Stack-Plan?
4.  Wird für die Azure Stack-Instanz ein Patch oder Upgrade angewendet?

Weitere Informationen finden Sie im Artikel [Troubleshooting](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md) (Problembehandlung) im **Azure/aks-engine**-GitHub-Repository.

## <a name="collect-aks-engine-logs"></a>Sammeln von AKS-Engine-Protokollen

Sie können auf Überprüfungsinformationen zugreifen, die von der AKS-Engine erstellt wurden. Die AKS-Engine meldet Status und Fehler, während die Anwendung ausgeführt wird. Sie können die Ausgabe entweder an eine Textdatei übergeben oder direkt von der Befehlszeilenkonsole kopieren. Eine Liste der von der AKS-Engine ausgelösten Fehlercodes finden Sie unter [Überprüfen der Fehlercodes für die benutzerdefinierte Skripterweiterung](#review-custom-script-extension-error-codes).

1.  Erfassen Sie Standardausgabe und Fehler aus Informationen, die im AKS-Engine-Befehlszeilentool angezeigt werden.

2. Rufen Sie Protokolle aus einer lokalen Datei ab. Sie können das Ausgabeverzeichnis mit dem Parameter **--output-directory** festlegen.

    So legen Sie den lokalen Pfad für die Protokolle fest:

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>Sammeln von Kubernetes-Protokollen

Zusätzlich zu den Protokollen der AKS-Engine generieren die Kubernetes-Komponenten Status- und Fehlermeldungen. Sie können diese Protokolle mit dem Bash-Skript [getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.0/diagnosis.zip) erfassen.

Mit diesem Skript wird der Prozess des Sammelns der folgenden Protokolle automatisiert: 

 - Microsoft Azure Linux Agent-Protokolle (waagent)
 - Protokolle der benutzerdefinierten Skripterweiterung
 - Ausführen von Kube-System-Containermetadaten
 - Ausführen von Kube-System-Containerprotokollen
 - Kubelet-Dienststatus und -Journal
 - Etcd-Dienststatus und -Journal
 - DVM-Protokolle des Katalogelements
 - Kube-System-Momentaufnahme

Ohne dieses Skript müssten Sie eine Verbindung mit jedem Knoten im Cluster herstellen und die Protokolle manuell herunterladen. Außerdem kann das Skript optional die gesammelten Protokolle in ein Speicherkonto hochladen, das Sie verwenden können, um die Protokolle für andere freizugeben.

Anforderungen:

 - Eine Linux-VM, Git Bash oder Bash in Windows.
 - [Azure CLI](azure-stack-version-profiles-azurecli2.md) muss auf dem Computer installiert sein, auf dem das Skript ausgeführt wird.
 - Eine bei einer Azure CLI-Sitzung mit Azure Stack angemeldete Dienstprinzipalidentität. Da das Skript für seine Aufgaben ARM-Ressourcen ermitteln und erstellen kann, müssen die Azure CLI und eine Dienstprinzipalidentität verwendet werden.
 - Benutzerkonto (Abonnement), in dem der Kubernetes-Cluster bereits in der Umgebung ausgewählt ist. 
1. Laden Sie die neueste Version der Skript-tar-Datei auf Ihre Client-VM herunter, einen Computer mit Zugriff auf Ihren Kubernetes-Cluster oder derselbe Computer, den Sie zum Bereitstellen Ihres Clusters mit der AKS-Engine verwendet haben.

    Führen Sie die folgenden Befehle aus:

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.1/diagnosis-v0.1.1.tar.gz
    tar xvf diagnosis-v0.1.1.tar.gz -C ./
    ```

2. Suchen Sie nach den Parametern, die das `getkuberneteslogs.sh`-Skript benötigt. Das Skript verwendet die folgenden Parameter:

    | Parameter | BESCHREIBUNG | Erforderlich | Beispiel |
    | --- | --- | --- | --- |
    | -h, --help | Informationen zur Verwendung des Befehls anzeigen. | no | 
    -u,--user | Der Benutzername des Administrators für die Cluster-VMs | Ja | azureuser<br>(Standardwert) |
    | -i, --identity-file | Der an den öffentlichen Schlüssel gebundene private RSA-Schlüssel, der zum Erstellen des Kubernetes-Clusters verwendet wurde (manchmal „id_rsa“ genannt)  | Ja | `./rsa.pem` (Putty)<br>`~/.ssh/id_rsa` (SSH) |
    |   -g, --resource-group    | Kubernetes-Clusterressourcengruppe | Ja | k8sresourcegroup |
    |   -n, --user-namespace               | Sammeln von Protokollen aus Containern in den angegebenen Namespaces (Kube-System-Protokolle werden immer erfasst) | no |   monitoring |
    |       --api-model                    | Speichert die Datei „apimodel.json“ in einem Azure Stack-Speicherkonto. Die Datei „apimodel.json“ wird in das Speicherkonto hochgeladen, wenn der Parameter „--upload-logs“ ebenfalls angegeben wird. | no | `./apimodel.json` |
    | --all-namespaces               | Sammeln von Protokollen aus Containern in allen Namespaces. „--user-namespace“ wird überschrieben | no | |
    | --upload-logs                  | Speichert abgerufene Protokolle in einem Azure Stack-Speicherkonto. Protokolle finden Sie in der KubernetesLogs-Ressourcengruppe. | no | |
    --disable-host-key-checking    | Legt die StrictHostKeyChecking-Option von SSH auf „no“ fest, während das Skript ausgeführt wird. Nur in einer sicheren Umgebung verwenden. | no | |

3. Führen Sie einen der folgenden Beispielbefehle mit ihren Informationen aus:

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>Überprüfen der Fehlercodes für die benutzerdefinierte Skripterweiterung

Sie können eine Liste der Fehlercodes einsehen, die von der benutzerdefinierten Skripterweiterung (Custom Script Extension, CSE) beim Ausführen Ihres Clusters erstellt wurden. Die CSE-Fehlercodes können nützlich sein, um die Ursache des Problems zu diagnostizieren. Die CSE für den Ubuntu-Server, der im Kubernetes-Cluster verwendet wird, unterstützt viele der AKS-Engine-Vorgänge. Weitere Informationen zu den CSE-Exitcodes finden Sie unter [cse_helpers. sh](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh).

### <a name="providing-kubernetes-logs-to-a-microsoft-support-engineer"></a>Bereitstellen von Kubernetes-Protokollen für einen Microsoft-Supporttechniker

Wenn Sie das Problem nach dem Erfassen und Durchsehen von Protokollen immer noch nicht beheben können, empfiehlt es sich, den Prozess zum Erstellen eines Supporttickets zu starten und die erfassten Protokolle durch Ausführen von `getkuberneteslogs.sh` mit dem Parametersatz `--upload-logs` zu übermitteln. 

Wenden Sie sich an Ihren Azure Stack-Operator. Der Operator erstellt den Supportfall anhand der Informationen aus Ihren Protokollen.

Während der Behandlung von supportbezogenen Problemen kann ein Microsoft-Supporttechniker darum bitten, dass Ihr Azure Stack-Bediener die Azure Stack-Systemprotokolle erfasst. Möglicherweise müssen Sie dem Bediener die Informationen zum Speicherkonto zur Verfügung stellen, unter dem Sie die Kubernetes-Protokolle hochgeladen haben. Führen Sie dazu `getkuberneteslogs.sh` aus.

Der Bediener führt u. U. das PowerShell-Cmdlet **Get-AzureStackLog** aus. Von diesem Befehl wird ein Parameter (`-InputSaSUri`) verwendet, der das Speicherkonto angibt, unter dem Sie die Kubernetes-Protokolle gespeichert haben.

Der Operator kann die von Ihnen erstellten Protokolle zusammen mit anderen vom Microsoft-Support benötigten Systemprotokollen kombinieren und diese für Microsoft verfügbar machen.

## <a name="open-github-issues"></a>Öffnen von GitHub-Problemen

Wenn Sie den Bereitstellungsfehler nicht beheben können, können Sie ein GitHub-Problem öffnen. 

1. Öffnen Sie ein [GitHub-Problem](https://github.com/Azure/aks-engine/issues/new) im AKS-Engine-Repository.
2. Fügen Sie einen Titel im folgenden Format hinzu: C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`.
3. Beziehen Sie folgende Informationen in das Problem ein:

    - Die zum Bereitstellen des Clusters verwendete Clusterkonfigurationsdatei `apimodel json`. Entfernen Sie alle Geheimnisse und Schlüssel, bevor Sie sie auf GitHub veröffentlichen.  
     - Die Ausgabe des folgenden **kubectl**-Befehls `get nodes`.  
     - Die Inhalte von `/var/log/azure/cluster-provision.log` und `/var/log/cloud-init-output.log`.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).
