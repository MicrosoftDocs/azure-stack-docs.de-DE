---
title: Installieren der AKS-Engine unter Linux in Azure Stack Hub
description: Erfahren Sie, wie Sie einen Linux-Computer in Ihrer Azure Stack Hub-Instanz zum Hosten der AKS-Engine verwenden, um einen Kubernetes-Cluster bereitzustellen und zu verwalten.
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 49855cbe926dc29b20c3309e4fc3a084ee5a5e70
ms.sourcegitcommit: 719569bb9e3f9924494a9229b4f2d211ae3f4f74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90717962"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>Installieren der AKS-Engine unter Linux in Azure Stack Hub

Sie können einen Linux-Computer in Ihrer Azure Stack Hub-Instanz zum Hosten der AKS-Engine verwenden, um einen Kubernetes-Cluster bereitzustellen und zu verwalten. In diesem Artikel wird beschrieben, wie Sie die Client-VM für die Verwaltung Ihres Clusters für verbundene und nicht verbundene Azure Stack Hub-Instanzen vorbereiten, die Installation überprüfen und die Client-VM auf dem ASDK einrichten.

## <a name="prepare-the-client-vm"></a>Vorbereiten der Client-VM

Die AKS-Engine ist ein Befehlszeilentool, das zum Bereitstellen und Verwalten Ihres Kubernetes-Clusters verwendet wird. Sie können die Engine auf einem Computer in Ihrer Azure Stack Hub-Instanz ausführen. Auf diesem Computer führen Sie die AKS-Engine zum Bereitstellen der IaaS-Ressourcen und Software aus, die für die Ausführung des Clusters erforderlich sind. Anschließend können Sie den Computer, auf dem die Engine ausgeführt wird, zum Ausführen von Verwaltungsaufgaben auf Ihrem Cluster verwenden.

Wenn Sie Ihren Clientcomputer auswählen, sollten Sie Folgendes beachten:

1. Ob der Clientcomputer im Notfall wiederherstellbar sein soll.
2. Wie Sie eine Verbindung mit dem Clientcomputer herstellen werden, und wie der Computer mit Ihrem Cluster interagieren wird.

## <a name="install-in-a-connected-environment"></a>Installieren in einer verbundenen Umgebung

Sie können die Client-VM installieren, um Ihren Kubernetes-Cluster in einer mit dem Internet verbundenen Azure Stack Hub-Instanz zu verwalten.

1. Erstellen Sie eine Linux-VM in ihrer Azure Stack Hub-Instanz. Anweisungen dazu finden Sie unter [Schnellstart: Erstellen eines virtuellen Linux-Server mit dem Azure Stack Hub-Portal](./azure-stack-quick-linux-portal.md).
2. Stellen Sie eine Verbindung mit Ihrer VM her.
3. Suchen Sie die Version der AKS-Engine in der Tabelle [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) (Unterstützte Kubernetes-Versionen). Das AKS-Basisimage muss in Ihrem Azure Stack Hub-Marketplace verfügbar sein. Beim Ausführen des Befehls müssen Sie die Version `--version v0.55.4` angeben. Wenn Sie die Version nicht angeben, wird mit dem Befehl die neueste Version installiert. Diese erfordert unter Umständen ein VHD-Image, das in Ihrem Marketplace nicht verfügbar ist.
4. Führen Sie den folgenden Befehl aus:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version 0.55.4
    ```

    > [!NOTE]  
    > Wenn bei der Installation ein Fehler auftritt, können Sie die Schritte in der [getrennten Umgebung](#install-in-a-disconnected-environment) ausprobieren, oder [probieren Sie GoFish aus](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), einen alternativen Paket-Manager.

## <a name="install-in-a-disconnected-environment"></a>Installieren in einer getrennten Umgebung

Sie können die Client-VM installieren, um Ihren Kubernetes-Cluster in einer Azure Stack Hub-Instanz zu verwalten, die vom Internet getrennt ist.

1.  Wechseln Sie von einem Computer mit Internetzugriff zu GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Laden Sie ein Archiv (*.tar.gz) für einen Linux-Computer herunter, z. B. `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Erstellen Sie ein Speicherkonto in ihrer Azure Stack Hub-Instanz, um die Archivdatei (*.tar.gz) mit der AKS-Engine-Binärdatei hochzuladen. Anweisungen zur Verwendung von Azure Storage-Explorer finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](./azure-stack-storage-connect-se.md).

3. Erstellen Sie eine Linux-VM in ihrer Azure Stack Hub-Instanz. Anweisungen dazu finden Sie unter [Schnellstart: Erstellen eines virtuellen Linux-Server mit dem Azure Stack Hub-Portal](./azure-stack-quick-linux-portal.md).

3.  Laden Sie die Datei von der Blob-URL des Azure Stack Hub-Speicherkontos, wo Sie die Archivdatei (*.tar.gz) hochgeladen haben, auf Ihre Verwaltungs-VM herunter. Extrahieren Sie das Archiv in das `/usr/local/bin`-Verzeichnis.

4. Stellen Sie eine Verbindung mit Ihrer VM her.

5.  Führen Sie den folgenden Befehl aus:

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>Überprüfen der Installation

Nachdem die Client-VM eingerichtet ist, überprüfen Sie, ob Sie die AKS-Engine installiert haben.

1. Stellen Sie eine Verbindung mit Ihrer Client-VM her.
2. Führen Sie den folgenden Befehl aus:

   ```bash  
   aks-engine version
   ```

3. Wenn der Azure Resource Manager-Endpunkt ein selbstsigniertes Zertifikat verwendet, müssen Sie das Stammzertifikat explizit dem vertrauenswürdigen Zertifikatspeicher des Computers hinzufügen. Sie finden das Stammzertifikat auf der VM in diesem Verzeichnis: „/var/lib/waagent/certificates.pem“. Kopieren Sie die Zertifikatsdatei mit folgendem Befehl: 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

Wenn Sie nicht überprüfen können, ob Sie die AKS-Engine auf Ihrer Client-VM installiert haben, finden Sie weitere Informationen unter [Problembehandlung bei der AKS-Engine unter Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>ASDK-Installation

Wenn Sie die Client-VM für die AKS-Engine auf dem ASDK ausführen, müssen Sie ein Zertifikat hinzufügen.

Wenn Sie ein ASDK verwenden und Ihr Azure Resource Manager-Endpunkt ein selbstsigniertes Zertifikat verwendet, müssen Sie dieses Zertifikat explizit dem vertrauenswürdigen Zertifikatspeicher des Computers hinzufügen. Sie finden das ASDK-Stammzertifikat auf jedem virtuellen Computer, den Sie im ASDK bereitstellen. Auf einer Ubuntu-VM finden Sie es beispielsweise im Verzeichnis `/var/lib/waagent/Certificates.pem`. 

Kopieren Sie die Zertifikatsdatei mit folgendem Befehl:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
