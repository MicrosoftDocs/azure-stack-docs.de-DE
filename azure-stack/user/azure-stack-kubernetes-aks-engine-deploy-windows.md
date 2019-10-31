---
title: Bereitstellen der AKS-Engine in Windows in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Windows-Computer in Ihrer Azure Stack-Instanz zum Hosten der AKS-Engine verwenden, um einen Kubernetes-Cluster bereitzustellen und zu verwalten.
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
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: c646f8229b5360aed12c9cd8070a00bf38ca3e01
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167304"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack"></a>Installieren der AKS-Engine in Windows in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen Windows-Computer in Ihrer Azure Stack-Instanz zum Hosten der AKS-Engine verwenden, um einen Kubernetes-Cluster bereitzustellen und zu verwalten. In diesem Artikel wird beschrieben, wie Sie die Client-VM für die Verwaltung Ihres Clusters für verbundene und nicht verbundene Azure Stack-Instanzen vorbereiten, die Installation überprüfen und die Client-VM auf dem ASDK einrichten.

## <a name="prepare-the-client-vm"></a>Vorbereiten der Client-VM

Die AKS-Engine ist ein Befehlszeilentool, das zum Bereitstellen und Verwalten Ihres Kubernetes-Clusters verwendet wird. Sie können die Engine auf einem Computer in Ihrer Azure Stack-Instanz ausführen. Auf diesem Computer führen Sie die AKS-Engine zum Bereitstellen der IaaS-Ressourcen und Software aus, die für die Ausführung des Clusters erforderlich sind. Anschließend können Sie den Computer, auf dem die Engine ausgeführt wird, zum Ausführen von Verwaltungsaufgaben auf Ihrem Cluster verwenden.

Wenn Sie Ihren Clientcomputer auswählen, sollten Sie Folgendes beachten:

1. Ob der Clientcomputer im Notfall wiederherstellbar sein soll.
3. Wie Sie eine Verbindung mit dem Clientcomputer herstellen werden, und wie der Computer mit Ihrem Cluster interagieren wird.

## <a name="install-in-a-connected-environment"></a>Installieren in einer verbundenen Umgebung

Sie können die Client-VM installieren, um Ihren Kubernetes-Cluster in einer mit dem Internet verbundenen Azure Stack-Instanz zu verwalten.

1. Erstellen Sie unter Azure Stack eine Windows-VM. Anweisungen dazu finden Sie unter [Schnellstart: Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack-Portal](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).
2. Stellen Sie eine Verbindung mit Ihrer VM her.
3. [Installieren Sie Chocolatey mithilfe der PowerShell-Anweisungen](https://chocolatey.org/install#install-with-powershellexe). 

    Die Chocolatey-Website enthält folgende Informationen: Chocolatey ist ein Paket-Manager wie z. B. apt-get oder yum, aber für Windows. Er wurde als dezentralisiertes Framework für die schnelle Installation von Anwendungen und Tools konzipiert, die Sie benötigen. Er basiert auf der NuGet-Infrastruktur und verwendet derzeit schwerpunktmäßig PowerShell zur Bereitstellung von Paketen aus den Distributionen für Ihren Computer.
4. Suchen Sie die Version der AKS-Engine in der Tabelle [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (Unterstützte Kubernetes-Versionen). Die AKS-Basis-Engine muss in Ihrem Azure Stack-Marketplace verfügbar sein. Beim Ausführen des Befehls müssen Sie die Version `--version v0.41.2` angeben. Wenn Sie die Version nicht angeben, installiert der Befehl die neueste Version, die möglicherweise ein VHD-Image erfordert, das in Ihrem Marketplace nicht verfügbar ist.
5. Führen Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten aus, und beziehen Sie die Versionsnummer ein:

    ```PowerShell  
        choco install aks-engine --version 0.41.2 -y
    ```

> [!Note]  
> Wenn bei dieser Installationsmethode ein Fehler auftritt, können Sie die Schritte in der [getrennten Umgebung](#install-in-a-disconnected-environment) ausprobieren, oder [probieren Sie GoFish aus](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), einen alternativen Paket-Manager.

## <a name="install-in-a-disconnected-environment"></a>Installieren in einer getrennten Umgebung

Sie können die Client-VM installieren, um Ihren Kubernetes-Cluster in einer Azure Stack-Instanz zu verwalten, die vom Internet getrennt ist.

1.  Wechseln Sie von einem Computer mit Internetzugriff zu GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Laden Sie ein Archiv (*.tar.gz) für einen Windows-Computer herunter, z. B. `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Erstellen Sie ein Speicherkonto in ihrer Azure Stack-Instanz, um die Archivdatei (*.tar.gz) mit der AKS-Engine-Binärdatei hochzuladen. Anweisungen zur Verwendung von Azure Storage-Explorer finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Erstellen Sie unter Azure Stack eine Windows-VM. Anweisungen dazu finden Sie unter [Schnellstart: Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack-Portal](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).

4.  Laden Sie die Datei von der Blob-URL des Azure Stack-Speicherkontos, wo Sie die Archivdatei (*.tar.gz) hochgeladen haben, auf Ihre Verwaltungs-VM herunter. Extrahieren Sie das Archiv in ein Verzeichnis, auf das Sie über die Eingabeaufforderung zugreifen können.

5. Stellen Sie eine Verbindung mit Ihrer VM her.

6. [Installieren Sie Chocolatey mithilfe der PowerShell-Anweisungen](https://chocolatey.org/install#install-with-powershellexe). 

7.  Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus. Beziehen Sie die richtige Versionsnummer ein:

    ```PowerShell  
        choco install aks-engine --version v0.41.2 -y
    ```

## <a name="verify-the-installation"></a>Überprüfen der Installation

Nachdem die Client-VM eingerichtet ist, überprüfen Sie, ob Sie die AKS-Engine installiert haben.

1. Stellen Sie eine Verbindung mit Ihrer Client-VM her.
2. Führen Sie den folgenden Befehl aus:

    ```PowerShell  
    aks-engine version
    ```

Wenn Sie nicht überprüfen können, ob Sie die AKS-Engine auf Ihrer Client-VM installiert haben, finden Sie weitere Informationen unter [Problembehandlung bei der AKS-Engine unter Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>ASDK-Installation

Wenn Sie die Client-VM für die AKS-Engine auf dem ASDK auf einem außerhalb des ASDK befindlichen Computer ausführen, müssen Sie ein Zertifikat hinzufügen. Wenn Sie eine Windows-VM in der ASDK-Umgebung selbst verwenden, vertraut der Computer bereits dem ASDK-Zertifikat. Wenn sich der Clientcomputer außerhalb des ASDK befindet, müssen Sie das Zertifikat aus dem ASDK extrahieren und dem Windows-Computer hinzufügen.

Wenn Sie ein ASDK verwenden und Ihr Azure Resource Manager-Endpunkt ein selbstsigniertes Zertifikat verwendet, müssen Sie dieses Zertifikat explizit dem vertrauenswürdigen Zertifikatspeicher des Computers hinzufügen. Sie finden das ASDK-Stammzertifikat auf jedem virtuellen Computer, den Sie im ASDK bereitstellen.

1. Exportieren Sie das Zertifizierungsstellen-Stammzertifikat. Anweisungen finden Sie unter [Exportieren des Azure Stack-Zertifizierungsstellen-Stammzertifikats](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-ca-root-certificate).
2. Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack als vertrauenswürdig ein. Anweisungen finden Sie unter [Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack als vertrauenswürdig](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-ca-root-certificate).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
