---
title: Problembehandlung bei der Bereitstellung von Kubernetes in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme bei der Bereitstellung von Kubernetes in Azure Stack behandeln können.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 04/02/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 2d4176ceaf1651539a248928faf2034376a8b97a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310986"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Problembehandlung bei der Bereitstellung von Kubernetes in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!Note]  
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Das Szenario mit nicht verbundenem Azure Stack wird von der Preview zurzeit nicht unterstützt.

Der folgende Artikel beschäftigt sich mit der Problembehandlung Ihres Kubernetes-Clusters. Sie können die Bereitstellungswarnung untersuchen und den Status Ihrer Bereitstellung anhand der für die Bereitstellung erforderlichen Elemente überprüfen. Sie müssen möglicherweise die Bereitstellungsprotokolle von Azure Stack oder den Linux-VMs erfassen, die Kubernetes hosten. Darüber hinaus müssen Sie möglicherweise mit Ihrem Azure Stack-Administrator zusammenarbeiten, um Protokolle von einem Verwaltungsendpunkt abzurufen.

## <a name="overview-of-kubernetes-deployment"></a>Übersicht über die Kubernetes-Bereitstellung

Bevor Sie sich mit der Problembehandlung Ihres Clusters beginnen, möchten Sie vielleicht den Bereitstellungsprozess des Azure Stack Kubernetes-Clusters überprüfen. Die Bereitstellung verwendet eine Azure Resource Manager-Lösungsvorlage, um die virtuellen Computer zu erstellen, und installiert die ACS-Engine für Ihren Cluster.

### <a name="kubernetes-deployment-workflow"></a>Workflow der Kubernetes-Bereitstellung

Die folgende Abbildung zeigt den allgemeinen Ablauf der Bereitstellung des Clusters.

![Vorgang der Bereitstellung von Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Bereitstellungsschritte

1. Erfassen Sie die Eingabeparameter aus dem Marketplace-Element.

    Geben Sie die Werte ein, die Sie für die Einrichtung des Kubernetes-Clusters benötigen, einschließlich:
    -  **Benutzername**: Hierbei handelt es sich um den Benutzernamen für die virtuellen Linux-Computer, die Teil des Kubernetes-Clusters und des DVM (Deployment Virtual Machine, virtueller Computer für die Bereitstellung) sind.
    -  **Öffentlicher SSH-Schlüssel**: Der Schlüssel, der für die Autorisierung bei allen Linux-Computern verwendet wird, die als Teil des Kubernetes-Clusters und der DVM erstellt werden.
    -  **Dienstprinzipal**: Die ID, die vom Kubernetes Azure-Cloudanbieter verwendet wird. Die als Anwendungs-ID identifizierte Client-ID, wenn Sie Ihren Dienstprinzipal erstellt haben. 
    -  **Geheimer Clientschlüssel**: Der Schlüssel, den Sie beim Erstellen Ihres Dienstprinzipals erstellt haben.

2. Erstellen Sie die Bereitstellungs-VM und die benutzerdefinierte Skripterweiterung.
    -  Erstellen Sie die Linux-Bereitstellungs-VM mit dem Marketplace-Linux-Image **Ubuntu Server 16.04-LTS**.
    -  Laden Sie die benutzerdefinierte Skripterweiterung aus Marketplace herunter, und führen Sie sie aus. Das Skript ist das **Benutzerdefinierte Skript für Linux 2.0**.
    -  Führen Sie das benutzerdefinierte DVM-Skript aus. Das Skript führt folgende Aufgaben aus:
        1. Ruft den Katalogendpunkt aus dem Azure Resource Manager-Metadatenendpunkt ab.
        2. Ruft die Active Directory-Ressourcen-ID aus dem Azure Resource Manager-Metadatenendpunkt ab.
        3. Lädt das API-Modell für die ACS-Engine.
        4. Stellt die ACS-Engine für den Kubernetes-Cluster bereit und speichert das Azure Stack-Cloudprofil in `/etc/kubernetes/azurestackcloud.json`.
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

## <a name="steps-for-troubleshooting"></a>Schritte zur Problembehandlung

Sie können Protokolle auf den virtuellen Computern erfassen, die Ihren Kubernetes-Cluster unterstützen. Sie können auch das Bereitstellungsprotokoll überprüfen. Möglicherweise müssen Sie sich auch an Ihren Azure Stack-Administrator wenden, um die von Ihnen verwendete Version von Azure Stack zu überprüfen und Protokolle von Azure Stack im Zusammenhang mit Ihrer Bereitstellung abzurufen.

1. Überprüfen Sie den [Bereitstellungsstatus](#review-deployment-status), und rufen Sie die Protokolle vom Masterknoten in Ihrem Kubernetes-Cluster ab.
2. Stellen Sie sicher, dass Sie die neueste Version von Azure Stack verwenden. Wenn Sie sich nicht sicher sind, welche Version Sie verwenden sind, wenden Sie sich an den Azure Stack-Administrator.
3.  Überprüfen Sie Ihre VM-Erstellungsdateien. Es können die folgenden Probleme auftreten:  
    - Der öffentliche Schlüssel ist möglicherweise ungültig. Überprüfen Sie den Schlüssel, den Sie erstellt haben.  
    - Die Erstellung des virtuellen Computers kann einen internen Fehler oder einen Erstellungsfehler ausgelöst haben. Fehler können durch eine Reihe von Faktoren verursacht werden, einschließlich Kapazitätseinschränkungen für Ihr Azure Stack-Abonnement.
    - Stellen Sie sicher, dass der vollqualifizierte Domänenname (FQDN) für den virtuellen Computer mit einem doppelten Präfix beginnt.
4.  Wenn der virtuelle Computer **OK** ist, untersuchen Sie die DVM. Wenn für die DVM eine Fehlermeldung vorliegt, sind die folgenden Ursachen möglich:

    - Der öffentliche Schlüssel ist möglicherweise ungültig. Überprüfen Sie den Schlüssel, den Sie erstellt haben.  
    - Sie müssen sich an Ihren Azure Stack-Administrator wenden, um die Protokolle für Azure Stack mit den privilegierten Endpunkten abzurufen. Weitere Informationen finden Sie unter [Azure Stack-Diagnosetools](../operator/azure-stack-diagnostics.md).
5. Wenn Sie Fragen zu Ihrer Bereitstellung haben, können Sie diese im [Azure Stack-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen bzw. ermitteln, ob jemand die Frage dort bereits beantwortet hat. 

## <a name="review-deployment-status"></a>Überprüfen des Bereitstellungsstatus

Sie können den Bereitstellungsstatus überprüfen, wenn Sie Ihren Kubernetes-Cluster bereitstellen, um Probleme zu überprüfen.

1. Öffnen Sie das [Azure Stack-Portal](https://portal.local.azurestack.external).
2. Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann den Namen der Ressourcengruppe aus, die beim Bereitstellen des Kubernetes-Clusters verwendet wurde.
3. Wählen Sie **Bereitstellungen** und dann den **Bereitstellungsnamen** aus.

    ![Problembehandlung](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Sehen Sie sich das Problembehandlungsfenster an. Jede bereitgestellte Ressource stellt die folgenden Informationen zur Verfügung.
    
    | Eigenschaft | BESCHREIBUNG |
    | ----     | ----        |
    | Resource | Der Name der Ressource. |
    | Type | Der Ressourcenanbieter und der Typ der Ressource. |
    | Status | Der Status des Elements. |
    | TimeStamp | Der UTC-Zeitstempel. |
    | Vorgangsdetails | Die Details des Vorgangs, z.B. der Ressourcenanbieter für den Vorgang, der Ressourcenendpunkt und der Name der Ressource. |

    Jedes Element weist ein grünes oder rotes Statussymbol auf.

## <a name="review-deployment-logs"></a>Überprüfen der Bereitstellungsprotokolle

Falls die im Azure Stack-Portal verfügbaren Informationen nicht ausreichen, um einen Bereitstellungsfehler zu beheben, besteht der nächste Schritt darin, die Clusterprotokolldateien zu überprüfen. Um die Bereitstellungsprotokolle manuell abzurufen, müssen Sie in der Regel eine Verbindung mit einer der Master-VMs des Clusters herstellen. Eine einfachere alternative Methode ist das Herunterladen und Ausführen des folgenden [Bash-Skripts](https://aka.ms/AzsK8sLogCollectorScript), das vom Azure Stack-Team bereitgestellt wurde. Das Skript stellt eine Verbindung mit der Bereitstellungs-VM (Deployment VM, DVM) und den VMs im Cluster her, sammelt relevante System- und Clusterprotokolldateien und lädt diese wieder auf Ihre Arbeitsstation herunter.

### <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Bash-Eingabeaufforderung auf dem Computer, den Sie zur Verwaltung von Azure Stack verwenden. Auf einem Windows-Computer können Sie [Git für Windows](https://git-scm.com/downloads) installieren, um eine Bash-Eingabeaufforderung zu erhalten. Suchen Sie nach der Installation im Startmenü nach _Git Bash_.

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
    | -d, --vmd-host      | Die öffentliche IP-Adresse oder der vollqualifizierte Domänenname (FQDN) der DVM. Der Name der VM beginnt mit `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Informationen zur Verwendung des Befehls anzeigen. | |
    | -i, --identity-file | Die Datei mit dem privaten RSA-Schlüssel, die beim Erstellen des Kubernetes-Clusters an das Marketplace-Element übergeben wird. Diese Datei ist erforderlich, um eine Remoteverbindung mit den Kubernetes-Knoten herzustellen. | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | Die öffentliche IP-Adresse oder der vollqualifizierte Domänenname (FQDN) eines Kubernetes-Masterknotens. Der Name der VM beginnt mit `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | Der Benutzername, der beim Erstellen des Kubernetes-Clusters an das Marketplace-Element übergeben wird. Der Benutzername ist erforderlich, um eine Remoteverbindung mit den Kubernetes-Knoten herzustellen. | azureuser (Standardwert) |


   Wenn Sie Ihre Parameterwerte hinzufügen, könnte der Befehl wie folgt aussehen:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Nach ein paar Minuten gibt das Skript die gesammelten Protokolle in ein Verzeichnis namens `KubernetesLogs_{{time-stamp}}` aus. Dort finden Sie ein Verzeichnis für jede VM, die dem Cluster angehört.

    Das Protokollsammlerskript sucht in den Protokolldateien auch nach Fehlern. Wenn es ein bekanntes Problem findet, fügt es der Ausgabe Schritte zur Problembehandlung hinzu. Stellen Sie sicher, dass Sie die aktuelle Version des Skripts ausführen, um die Wahrscheinlichkeit zu erhöhen, dass bekannte Probleme gefunden werden.

> [!Note]  
> In diesem GitHub-[Repository](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) finden Sie weitere Details zum Protokollsammlerskript.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Hinzufügen eines Kubernetes-Clusters zum Marketplace (für Azure Stack-Bediener)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
