---
title: Zugreifen auf das Kubernetes-Dashboard in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie in Azure Stack auf das Kubernetes-Dashboard zugreifen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: ecd0d3c79edc2359cf82aa9c52fb9021d7fc7a6f
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308689"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Zugreifen auf das Kubernetes-Dashboard in Azure Stack 

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit* 
> [!Note]   
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Ein Szenario mit nicht verbundenem Azure Stack wird von der Vorschau zurzeit nicht unterstützt. Verwenden Sie das Marketplace-Element nur für Entwicklungs- und Testszenarien.

Kubernetes enthält ein Webdashboard, das Sie für einfache Verwaltungsvorgänge verwenden können. In diesem Dashboard können Sie den Integritätsstatus und grundlegende Metriken für Ihre Anwendungen anzeigen, Dienste erstellen und bereitstellen und vorhandene Anwendungen bearbeiten. In diesem Artikel wird veranschaulicht, wie Sie das Kubernetes-Dashboard in Azure Stack einrichten.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Voraussetzungen für Kubernetes-Dashboard

* Azure Stack-Kubernetes-Cluster

    Sie müssen in Azure Stack einen Kubernetes-Cluster bereitgestellt haben. Weitere Informationen finden Sie im Artikel zum Thema [Bereitstellen von Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* SSH-Client

    Sie benötigen einen SSH-Client, um eine sichere Verbindung mit Ihrem Masterknoten im Cluster herzustellen. Bei Verwendung von Windows können Sie [PuTTY](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) nutzen. Sie benötigen den privaten Schlüssel, der beim Bereitstellen Ihres Kubernetes-Clusters verwendet wurde.

* FTP (PSCP)

    Unter Umständen benötigen Sie auch einen FTP-Client mit SSH-Unterstützung und das SSH-Dateiübertragungsprotokoll, um die Zertifikate vom Masterknoten auf Ihren Azure Stack-Verwaltungscomputer zu übertragen. Sie können [FileZilla](https://filezilla-project.org/download.php?type=client) verwenden. Sie benötigen den privaten Schlüssel, der beim Bereitstellen Ihres Kubernetes-Clusters verwendet wurde.

## <a name="overview-of-steps-to-enable-dashboard"></a>Übersicht über die Schritte zum Aktivieren des Dashboards

1.  Exportieren Sie die Kubernetes-Zertifikate vom Masterknoten in den Cluster. 
2.  Importieren Sie die Zertifikate in Ihren Azure Stack-Verwaltungscomputer.
2.  Öffnen Sie das Kubernetes-Webdashboard. 

## <a name="export-certificate-from-the-master"></a>Exportieren des Zertifikats vom Master 

Sie können die URL für das Dashboard vom Masterknoten in Ihrem Cluster abrufen.

1. Rufen Sie die öffentliche IP-Adresse und den Benutzernamen für Ihren Clustermaster aus dem Azure Stack-Dashboard ab. Rufen Sie diese Informationen wie folgt ab:

    - Melden Sie sich am [Azure Stack-Portal](https://portal.local.azurestack.external/) an.
    - Wählen Sie **Alle Dienste** > **Alle Ressourcen**. Suchen Sie in Ihrer Clusterressourcengruppe nach dem Master. Der Master hat den Namen `k8s-master-<sequence-of-numbers>`. 

2. Öffnen Sie den Masterknoten im Portal. Kopieren Sie die **öffentliche IP-Adresse**. Klicken Sie auf **Verbinden**, um Ihren Benutzernamen im Feld **Mit lokalem VM-Konto anmelden** abzurufen. Dies ist der Benutzername, den Sie beim Erstellen Ihres Clusters festgelegt haben. Verwenden Sie die öffentliche IP-Adresse und nicht die private IP-Adresse, die auf dem Blatt „Verbinden“ angegeben ist.

3.  Öffnen Sie einen SSH-Client, um eine Verbindung mit dem Master herzustellen. Wenn Sie unter Windows arbeiten, können Sie [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) zum Herstellen der Verbindung verwenden. Sie verwenden die öffentliche IP-Adresse für den Masterknoten und den Benutzernamen und fügen den privaten Schlüssel hinzu, den Sie beim Erstellen des Clusters verwendet haben.

4.  Geben Sie nach der Verbindungsherstellung mit dem Terminal `kubectl` ein, um den Kubernetes-Befehlszeilenclient zu öffnen.

5. Führen Sie den folgenden Befehl aus:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Suchen Sie nach der URL für das Dashboard. Beispiel: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extrahieren Sie das selbstsignierte Zertifikat, und konvertieren Sie es in das PFX-Format. Führen Sie den folgenden Befehl aus:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Rufen Sie die Liste mit den Geheimnissen im Namespace **kube-system** ab. Führen Sie den folgenden Befehl aus:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Notieren Sie sich den Wert von „kubernetes-dashboard-token-\<XXXXX>“. 

8.  Rufen Sie das Token ab, und speichern Sie es. Aktualisieren Sie `kubernetes-dashboard-token-<####>` mit dem Geheimniswert aus dem vorherigen Schritt.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importieren des Zertifikats

1. Öffnen Sie FileZilla, und stellen Sie eine Verbindung mit dem Masterknoten her. Sie benötigen Folgendes:

    - Öffentliche IP-Adresse des Masterknotens
    - Benutzername
    - Privates Geheimnis
    - Verwenden Sie **SFTP (SSH File Transfer Protocol)** .

2. Kopieren Sie `/etc/kubernetes/certs/client.pfx` und `/etc/kubernetes/certs/ca.crt` auf Ihren Azure Stack-Verwaltungscomputer.

3. Notieren Sie sich die Dateispeicherorte. Aktualisieren Sie das Skript mit den Speicherorten, und öffnen Sie anschließend PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten. Führen Sie das aktualisierte Skript aus:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Öffnen des Kubernetes-Dashboards 

1. Deaktivieren Sie den Popupblocker in Ihrem Webbrowser.

2. Navigieren Sie in Ihrem Browser zu der URL, die Sie sich beim Ausführen des Befehls `kubectl cluster-info` notiert haben. Beispiel: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. Wählen Sie das Clientzertifikat aus.
4. Geben Sie das Token ein. 
5. Stellen Sie auf dem Masterknoten erneut eine Verbindung mit der Bash-Befehlszeile her, und gewähren Sie Berechtigungen für `kubernetes-dashboard`. Führen Sie den folgenden Befehl aus:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Mit dem Skript werden Cloudadministrator-Berechtigungen für `kubernetes-dashboard` gewährt. Weitere Informationen finden Sie unter [Zugreifen auf das Kubernetes-Webdashboard in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Sie können das Dashboard verwenden. Weitere Informationen zum Kubernetes-Dashboard finden Sie unter [Kubernetes Web UI Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) (Kubernetes-Dashboard mit Webbenutzeroberfläche). 

![Azure Stack-Kubernetes-Dashboard](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Nächste Schritte 

[Bereitstellen von Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Hinzufügen eines Kubernetes-Clusters zum Marketplace (für Azure Stack-Bediener)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
