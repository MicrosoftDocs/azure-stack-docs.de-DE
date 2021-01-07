---
title: Problembehandlung bei der Bereitstellung von Kubernetes in Azure Stack Hub
description: Erfahren Sie, wie Sie Probleme bei der Bereitstellung von Kubernetes in Azure Stack Hub beheben können.
author: mattbriggs
ms.topic: article
ms.author: mabrigg
ms.date: 12/16/2020
ms.reviewer: waltero
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: d15b6d3cbd4cac10c3df0d8c215a869c1fa398b7
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873876"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack-hub"></a>Problembehandlung bei der Bereitstellung von Kubernetes in Azure Stack Hub

> [!NOTE]  
> Verwenden Sie die Kubernetes-Erweiterung für Azure Stack Hub aus dem Marketplace nur, um Cluster als Proof of Concept bereitzustellen. Verwenden Sie für unterstützte Kubernetes-Cluster in Azure Stack Hub [die AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md).

In diesem Artikel wird erläutert, wie Sie Probleme mit Ihrem Kubernetes-Cluster beheben. Um mit der Problembehandlung zu beginnen, überprüfen Sie zunächst die für die Bereitstellung erforderlichen Elemente. Sie müssen möglicherweise die Bereitstellungsprotokolle von Azure Stack Hub oder den Linux-VMs erfassen, die Kubernetes hosten. Wenden Sie sich an Ihren Azure Stack Hub-Administrator, um Protokolle von einem Verwaltungsendpunkt abzurufen.

## <a name="overview-of-kubernetes-deployment"></a>Übersicht über die Kubernetes-Bereitstellung

Gehen Sie noch einmal den Bereitstellungsprozess für Azure Stack Hub-Kubernetes-Cluster durch, bevor Sie mit der Problembehandlung für Ihren Cluster beginnen. Bei der Bereitstellung wird eine Azure Resource Manager-Lösungsvorlage verwendet, um die VMs zu erstellen und die AKS-Engine (Azure Kubernetes Service) für Ihren Cluster zu installieren.

### <a name="kubernetes-deployment-workflow"></a>Workflow der Kubernetes-Bereitstellung

Die folgende Abbildung zeigt den allgemeinen Ablauf der Bereitstellung des Clusters.

![Vorgang der Bereitstellung von Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Bereitstellungsschritte

1. Erfassen Sie die Eingabeparameter aus dem Marketplace-Element.

    Geben Sie die Werte ein, die Sie für die Einrichtung des Kubernetes-Clusters benötigen, einschließlich:
    -  **Benutzername**: Hierbei handelt es sich um den Benutzernamen für die virtuellen Linux-Computer (VMs), die Teil des Kubernetes-Clusters und des DVM (Deployment Virtual Machine, virtueller Computer für die Bereitstellung) sind.
    -  **Öffentlicher SSH-Schlüssel**: Der Schlüssel, der für die Autorisierung bei allen Linux-Computern verwendet wird, die als Teil des Kubernetes-Clusters und der DVM erstellt werden.
    -  **Dienstprinzipal**: Die ID, die vom Kubernetes Azure-Cloudanbieter verwendet wird. Die als Anwendungs-ID identifizierte Client-ID, wenn Sie Ihren Dienstprinzipal erstellt haben. 
    -  **Geheimer Clientschlüssel**: Der Schlüssel, den Sie beim Erstellen Ihres Dienstprinzipals erstellt haben.

2. Erstellen Sie die Bereitstellungs-VM und die benutzerdefinierte Skripterweiterung.
    -  Erstellen Sie die Linux-Bereitstellungs-VM mit dem Marketplace-Linux-Image **Ubuntu Server 16.04-LTS**.
    -  Laden Sie die benutzerdefinierte Skripterweiterung aus Marketplace herunter, und führen Sie sie aus. Das Skript ist das **Benutzerdefinierte Skript für Linux 2.0**.
    -  Führen Sie das benutzerdefinierte DVM-Skript aus. Das Skript führt folgende Aufgaben aus:
        1. Ruft den Katalogendpunkt aus dem Azure Resource Manager-Metadatenendpunkt ab.
        2. Ruft die Active Directory-Ressourcen-ID aus dem Azure Resource Manager-Metadatenendpunkt ab.
        3. Lädt das API-Modell für die AKS-Engine.
        4. Stellt die AKS-Engine im Kubernetes-Cluster bereit und speichert das Azure Stack Hub-Cloudprofil in `/etc/kubernetes/azurestackcloud.json`.
3. Erstellen Sie die Master-VMs.

4. Laden Sie die benutzerdefinierten Skripterweiterungen herunter, und führen Sie sie aus.

5. Führen Sie das Masterskript aus.

    Das Skript führt folgende Aufgaben aus:
    - Installiert etcd, Docker und Kubernetes-Ressourcen wie Kubelet. etcd ist ein verteilter Schlüsselwertspeicher, der eine Möglichkeit zum Speichern von Daten in einem Cluster aus Computern bereitstellt. Docker unterstützt reine Virtualisierungen auf Betriebssystemebene, die als Container bezeichnet werden. Kubelet ist der Knoten-Agent, der auf jedem Kubernetes-Knoten ausgeführt wird.
    - Richtet den **etcd**-Dienst ein.
    - Richtet den **Kubelet**-Dienst ein.
    - Startet Kubelet. Diese Aufgabe umfasst die folgenden Schritte:
        1. Starten des API-Diensts.
        2. Starten des Controllerdiensts.
        3. Starten des Schedulerdiensts.
6. Führen Sie die Agent-VMs aus.

7. Laden Sie die benutzerdefinierte Skripterweiterung herunter, und führen Sie sie aus.

7. Führen Sie das Agent-Skript aus. Das benutzerdefinierte Agent-Skript führt folgende Aufgaben aus:
    - Installiert **etcd**.
    - Richtet den **Kubelet**-Dienst ein.
    - Tritt dem Kubernetes-Cluster bei.

## <a name="steps-to-troubleshoot-kubernetes"></a>Schritte zum Behandeln von Problemen mit Kubernetes

Sie können auf den VMs, die Ihren Kubernetes-Cluster unterstützen, Bereitstellungsprotokolle sammeln und diese überprüfen. Wenden Sie sich an den Azure Stack Hub-Administrator, um die erforderliche Azure Stack Hub-Version zu überprüfen und mit Ihrer Bereitstellung zusammenhängende Protokolle aus Azure Stack Hub abzurufen.

1. Überprüfen Sie den von der ARM-Bereitstellung zurückgegebenen Fehlercode im Bereich **Bereitstellungen** der Ressourcengruppe, in der Sie den Cluster bereitgestellt haben. Die Beschreibungen der Fehlercodes finden Sie im GitHub-Repository der AKS-Engine im Artikel [Problembehandlung](https://github.com/msazurestackworkloads/azurestack-gallery/blob/master/kubernetes/docs/troubleshooting.md). Wenn Sie das Problem nicht mithilfe der Fehlerbeschreibung beheben können, fahren Sie mit diesen Schritten fort.
2. Überprüfen Sie den [Bereitstellungsstatus](#review-deployment-status), und rufen Sie die Protokolle vom Masterknoten in Ihrem Kubernetes-Cluster ab.
3. Stellen Sie sicher, dass Sie die neueste Version von Azure Stack Hub verwenden. Wenn Sie sich nicht sicher sind, welche Version Sie verwenden sind, wenden Sie sich an den Azure Stack Hub-Administrator.
4. Überprüfen Sie Ihre VM-Erstellungsdateien. Es können die folgenden Probleme auftreten:  
    - Der öffentliche Schlüssel ist möglicherweise ungültig. Überprüfen Sie den Schlüssel, den Sie erstellt haben.  
    - Die Erstellung des virtuellen Computers kann einen internen Fehler oder einen Erstellungsfehler ausgelöst haben. Fehler können durch eine Reihe von Faktoren verursacht werden, einschließlich Kapazitätseinschränkungen für Ihr Azure Stack Hub-Abonnement.
    - Stellen Sie sicher, dass der vollqualifizierte Domänenname (FQDN) für den virtuellen Computer mit einem doppelten Präfix beginnt.
5.  Wenn der virtuelle Computer **OK** ist, untersuchen Sie die DVM. Wenn für die DVM eine Fehlermeldung vorliegt, sind die folgenden Ursachen möglich:
    - Der öffentliche Schlüssel ist möglicherweise ungültig. Überprüfen Sie den Schlüssel, den Sie erstellt haben.  
    - Wenden Sie sich an den Azure Stack Hub-Administrator, um die Protokolle für Azure Stack Hub mit den privilegierten Endpunkten abzurufen. Weitere Informationen finden Sie unter [Azure Stack Hub-Diagnosetools](../operator/azure-stack-get-azurestacklog.md).
5. Wenn Sie Fragen zu Ihrer Bereitstellung haben, können Sie diese im [Azure Stack Hub-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen bzw. ermitteln, ob jemand die Frage dort bereits beantwortet hat. 


## <a name="review-deployment-status"></a>Überprüfen des Bereitstellungsstatus

Sie können den Bereitstellungsstatus überprüfen, wenn Sie Ihren Kubernetes-Cluster bereitstellen, um Probleme zu überprüfen.

1. Öffnen Sie das Azure Stack Hub-Portal `https://portal.local.azurestack.external`.
2. Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann den Namen der Ressourcengruppe aus, die beim Bereitstellen des Kubernetes-Clusters verwendet wurde.
3. Wählen Sie **Bereitstellungen** und dann den **Bereitstellungsnamen** aus.

    ![Problembehandlung für Kubernetes: Auswählen der Bereitstellung](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Sehen Sie sich das Problembehandlungsfenster an. Jede bereitgestellte Ressource stellt die folgenden Informationen zur Verfügung.
    
    | Eigenschaft | BESCHREIBUNG |
    | ----     | ----        |
    | Resource | Der Name der Ressource. |
    | type | Der Ressourcenanbieter und der Typ der Ressource. |
    | Status | Der Status des Elements. |
    | TimeStamp | Der UTC-Zeitstempel. |
    | Vorgangsdetails | Die Details des Vorgangs, z.B. der Ressourcenanbieter für den Vorgang, der Ressourcenendpunkt und der Name der Ressource. |

    Jedes Element weist ein grünes oder rotes Statussymbol auf.

## <a name="review-deployment-logs"></a>Überprüfen der Bereitstellungsprotokolle

Falls die im Azure Stack Hub-Portal verfügbaren Informationen nicht ausreichen, um einen Bereitstellungsfehler zu beheben, besteht der nächste Schritt darin, die Clusterprotokolldateien zu überprüfen. Um die Bereitstellungsprotokolle manuell abzurufen, müssen Sie in der Regel eine Verbindung mit einer der Master-VMs des Clusters herstellen. Eine einfachere alternative Methode ist das Herunterladen und Ausführen des folgenden [Bash-Skripts](https://aka.ms/AzsK8sLogCollectorScript), das vom Azure Stack Hub-Team bereitgestellt wurde. Das Skript stellt eine Verbindung mit der Bereitstellungs-VM (Deployment VM, DVM) und den VMs im Cluster her, sammelt relevante System- und Clusterprotokolldateien und lädt diese wieder auf Ihre Arbeitsstation herunter.

### <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Bash-Eingabeaufforderung auf dem Computer, den Sie zur Verwaltung von Azure Stack Hub verwenden. Auf einem Windows-Computer können Sie [Git für Windows](https://git-scm.com/downloads) installieren, um eine Bash-Eingabeaufforderung zu erhalten. Suchen Sie nach der Installation im Startmenü nach _Git Bash_.

### <a name="retrieving-the-logs"></a>Abrufen der Protokolle

Gehen Sie wie folgt vor, um die Clusterprotokolldateien zu sammeln und herunterzuladen:

1. Öffnen Sie eine Bash-Eingabeaufforderung. Öffnen Sie auf einem Windows-Computer _Git Bash_, oder führen Sie den folgenden Befehl aus: `C:\Program Files\Git\git-bash.exe`

2. Laden Sie das Protokollsammlerskript herunter, indem Sie an der Bash-Eingabeaufforderung den folgenden Befehl ausführen:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Suchen Sie nach den erforderlichen Informationen für das Skript, und führen Sie das Skript aus:

    | Parameter           | BESCHREIBUNG                                                                                                      | Beispiel                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | Die öffentliche IP-Adresse oder der vollqualifizierte Domänenname (FQDN) der DVM. Der Name des virtuellen Computers beginnt mit `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Informationen zur Verwendung des Befehls anzeigen. | |
    | -i, --identity-file | Pfad zu der Datei mit dem privaten RSA-Schlüssel, die beim Erstellen des Kubernetes-Clusters an das Marketplace-Element übergeben wird. Diese Datei ist erforderlich, um eine Remoteverbindung mit den Kubernetes-Knoten herzustellen. | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | Die öffentliche IP-Adresse oder der vollqualifizierte Domänenname (FQDN) eines Kubernetes-Masterknotens. Der Name des virtuellen Computers beginnt mit `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | Der Benutzername, der beim Erstellen des Kubernetes-Clusters an das Marketplace-Element übergeben wird. Diese Datei ist erforderlich, um eine Remoteverbindung mit den Kubernetes-Knoten herzustellen. | azureuser (Standardwert) |


   Wenn Sie Ihre Parameterwerte hinzufügen, könnte der Befehl wie folgt aussehen:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Nach ein paar Minuten gibt das Skript die gesammelten Protokolle in ein Verzeichnis namens `KubernetesLogs_{{time-stamp}}` aus. Dort finden Sie ein Verzeichnis für jede VM, die dem Cluster angehört.

    Das Protokollsammlerskript sucht in den Protokolldateien auch nach Fehlern. Wenn es ein bekanntes Problem findet, fügt es der Ausgabe Schritte zur Problembehandlung hinzu. Stellen Sie sicher, dass Sie die aktuelle Version des Skripts ausführen, um die Wahrscheinlichkeit zu erhöhen, dass bekannte Probleme gefunden werden.

> [!NOTE]  
> In diesem GitHub-[Repository](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) finden Sie weitere Details zum Protokollsammlerskript.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von Kubernetes in Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)

[Hinzufügen eines Kubernetes-Clusters zu Marketplace (für Azure Stack Hub-Operator)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
