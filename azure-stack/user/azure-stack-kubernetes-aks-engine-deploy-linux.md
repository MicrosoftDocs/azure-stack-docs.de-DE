---
title: Installieren der AKS-Engine unter Linux in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Linux-Computer in Ihrer Azure Stack-Instanz zum Hosten der AKS-Engine verwenden, um einen Kubernetes-Cluster bereitzustellen und zu verwalten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 3979dfdf6a229f1d81b6c5cc017d4d3f75a62e1a
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595354"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack"></a>Installieren der AKS-Engine unter Linux in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Erfahren Sie, wie Sie einen Linux-Computer in Ihrer Azure Stack-Instanz zum Hosten der AKS-Engine verwenden, um einen Kubernetes-Cluster bereitzustellen und zu verwalten. In diesem Artikel wird beschrieben, wie Sie die Client-VM für die Verwaltung Ihres Clusters für verbundene und nicht verbundene Azure Stack-Instanzen vorbereiten, die Installation überprüfen und die Client-VM auf dem ASDK einrichten.

## <a name="prepare-the-client-vm"></a>Vorbereiten der Client-VM

Die AKS-Engine ist ein Befehlszeilentool, das zum Bereitstellen und Verwalten Ihres Kubernetes-Clusters verwendet wird. Sie können die Engine auf einem Computer in Ihrer Azure Stack-Instanz ausführen. Auf diesem Computer führen Sie die AKS-Engine zum Bereitstellen der IaaS-Ressourcen und Software aus, die für die Ausführung des Clusters erforderlich sind. Anschließend können Sie den Computer, auf dem die Engine ausgeführt wird, zum Ausführen von Verwaltungsaufgaben auf Ihrem Cluster verwenden.

Wenn Sie Ihren Clientcomputer auswählen, sollten Sie Folgendes beachten:

1. Ob der Clientcomputer im Notfall wiederherstellbar sein soll.
2. Wie Sie eine Verbindung mit dem Clientcomputer herstellen werden, und wie der Computer mit Ihrem Cluster interagieren wird.

## <a name="install-in-a-connected-environment"></a>Installieren in einer verbundenen Umgebung

Sie können die Client-VM installieren, um Ihren Kubernetes-Cluster in einer mit dem Internet verbundenen Azure Stack-Instanz zu verwalten.

1. Erstellen Sie eine Linux-VM in ihrer Azure Stack-Instanz. Anweisungen finden Sie unter [Schnellstart: Erstellen Sie einen virtuellen Linux-Server mit dem Azure Stack-Portal](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Stellen Sie eine Verbindung mit Ihrer VM her.
3. Suchen Sie die Version der AKS-Engine in der Tabelle [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (Unterstützte Kubernetes-Versionen). Die AKS-Basis-Engine muss in Ihrem Azure Stack-Marketplace verfügbar sein. Beim Ausführen des Befehls müssen Sie die Version `--version v0.43.0` angeben. Wenn Sie die Version nicht angeben, installiert der Befehl die neueste Version, die möglicherweise ein VHD-Image erfordert, das in Ihrem Marketplace nicht verfügbar ist.
4. Führen Sie den folgenden Befehl aus:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.43.0
    ```

    > [!Note]  
    > Wenn bei der Installation ein Fehler auftritt, können Sie die Schritte in der [getrennten Umgebung](#install-in-a-disconnected-environment) ausprobieren, oder [probieren Sie GoFish aus](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), einen alternativen Paket-Manager.

## <a name="install-in-a-disconnected-environment"></a>Installieren in einer getrennten Umgebung

Sie können die Client-VM installieren, um Ihren Kubernetes-Cluster in einer Azure Stack-Instanz zu verwalten, die vom Internet getrennt ist.

1.  Wechseln Sie von einem Computer mit Internetzugriff zu GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Laden Sie ein Archiv (*.tar.gz) für einen Linux-Computer herunter, z. B. `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Erstellen Sie ein Speicherkonto in ihrer Azure Stack-Instanz, um die Archivdatei (*.tar.gz) mit der AKS-Engine-Binärdatei hochzuladen. Anweisungen zur Verwendung von Azure Storage-Explorer finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Erstellen Sie eine Linux-VM in ihrer Azure Stack-Instanz. Anweisungen finden Sie unter [Schnellstart: Erstellen Sie einen virtuellen Linux-Server mit dem Azure Stack-Portal](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Laden Sie die Datei von der Blob-URL des Azure Stack-Speicherkontos, wo Sie die Archivdatei (*.tar.gz) hochgeladen haben, auf Ihre Verwaltungs-VM herunter. Extrahieren Sie das Archiv in das `/usr/local/bin`-Verzeichnis.

4. Stellen Sie eine Verbindung mit ihrer VM her.

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
> [Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)