---
title: Bereitstellen eines GPU-fähigen (grafikprozessorfähigen) IoT-Moduls auf Azure Stack Hub
description: Vorgehensweise zum Bereitstellen eines GPU-fähigen (grafikprozessorfähigen) IoT-Moduls auf Azure Stack Hub
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/13/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: b8aef224c5694cd6b8408538473dce3b86996c4d
ms.sourcegitcommit: 8187658b1d45dceed727aca3ae1b9b57aca04392
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94632835"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>Bereitstellen eines GPU-fähigen IoT-Moduls in Azure Stack Hub

Mit einem GPU-fähigen Azure Stack Hub können Sie prozessorintensive Module auf Linux-Geräten bereitstellen, die auf dem IoT Edge-Gerät ausgeführt werden. GPU-optimierte VM-Größen sind für spezielle VMs mit einzelner NVIDIA-GPU oder mit mehreren NVIDIA-GPUs verfügbar. In diesem Artikel erfahren Sie, wie Sie GPU-optimierte VMs verwenden, um rechenintensive, grafikintensive und Visualisierungsworkloads auszuführen.

Bevor Sie beginnen, benötigen Sie ein Azure Active Directory-Abonnement (Azure AD) mit Zugriff auf globales Azure und Azure Stack Hub, eine Azure Container Registry-Instanz (ACR) und einen IoT-Hub.

In diesem Artikel führen Sie folgende Schritte aus:
  - Installieren Sie eine GPU-fähige Linux-VM, und installieren Sie die richtigen Treiber.
  - Installieren Sie Docker, und aktivieren Sie die GPU in der Runtime.
  - Verbinden Sie Ihr IoT-Gerät mit Ihrem IoT-Hub, und installieren Sie das Modell aus dem IoT-Marketplace: `Getting started with GPUs`.
  - Verwenden Sie auf einem lokalen Computer Azure IoT-Explorer, um Ihr Gerät zu installieren und zu überwachen.
  - Verwenden Sie optional die Azure IoT-Erweiterung in Visual Studio Code, um Ihr Gerät zu installieren und zu überwachen.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen die folgenden Ressourcen in Ihrer Azure Stack Hub-Instanz, in Ihrer globalen Azure-Umgebung und auf Ihrem lokalen Entwicklungscomputer verfügbar haben.

### <a name="azure-stack-hub-and-azure"></a>Azure Stack Hub und Azure

  - Ein Abonnement als Benutzer, wobei im Abonnement Azure Active Directory (Azure AD) in einem integrierten Azure Stack Hub-System mit einer NVIDA-GPU verwendet wird. Die folgenden Chips funktionieren mit einem IoT-Hub:
    - NCv3
    - NCas_v4

    Weitere Informationen zu GPUs in Azure Stack Hub finden Sie unter [Grafikprozessor (GPU)-VM in Azure Stack Hub](gpu-vms-about.md).
  - Ein globales Azure-Abonnement. Wenn Sie kein globales Azure-Abonnement haben, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
- Eine [Azure Container Registry-Instanz (ACR)](/). Notieren Sie sich den Server, den Benutzernamen und das Kennwort für die ACR-Anmeldung.
-   Ein [IoT-Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) in globalem Azure mit dem Tarif „Free“ oder „Standard“.

### <a name="a-development-machine"></a>Ein Entwicklungscomputer

-   Sie können je nach Ihren Präferenzen bei der Entwicklung Ihren eigenen Computer oder einen virtuellen Computer verwenden. Auf Ihrem Entwicklungscomputer muss geschachtelte Virtualisierung unterstützt werden. Diese Funktionalität ist für das Ausführen von Docker erforderlich. Docker ist die Container-Engine, die in diesem Artikel verwendet wird.

  - Auf Ihrem Entwicklungscomputer sind die folgenden Ressourcen erforderlich:
      - [Python 3.x](https://www.python.org/downloads/)
      - [Pip](https://pypi.org/project/pip/) zum Installieren von Python-Paketen. Dieses Programm wird mit ihrer Python-Installation installiert. Wenn Sie pip installiert haben, sollten Sie ein Upgrade auf die neueste Version durchführen. Ein Upgrade können Sie direkt aus pip durchführen. Geben Sie Folgendes ein: `pip install --upgrade pip`.
      - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)
      - [Git-Client](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) für Visual Studio Code
      - [Python Extension Pack](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack) für Visual Studio Code

## <a name="register-an-iot-edge-device"></a>Registrieren eines IoT Edge-Geräts

Verwenden Sie ein eigenständiges Gerät, um Ihr IoT Edge-Gerät zu hosten. Die Verwendung eines eigenständigen Geräts bietet eine Trennung zwischen dem Entwicklungscomputer und dem IoT Edge-Gerät, was einem Bereitstellungsszenario genauer entspricht. 

Erstellen Sie ein IoT Edge-Gerät in Azure mit einer Linux-VM:

1.  [Erstellen Sie eine Linux-Server-VM der N-Serie](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal) in Azure Stack Hub. Wenn Sie Komponenten für Ihren Server installieren, kommunizieren Sie über SSH mit dem Server. Weitere Informationen finden Sie unter [Verwenden eines öffentlichen SSH-Schlüssels](/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).

2.  [Erstellen und registrieren Sie ein IoT Edge-Gerät](https://docs.microsoft.com/azure/iot-edge/how-to-register-device).

## <a name="prepare-a-gpu-enabled-vm"></a>Vorbereiten einer GPU-fähigen VM

1. Installieren Sie die NVIDA-GPU-Treiber auf Ihrem Linux-Server der N-Serie. Führen Sie dazu die Schritte aus, die im Artikel [Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup) aufgeführt sind.

    > [!NOTE]  
    > Sie verwenden die bash-Befehlszeile, um Ihre Software zu installieren. Notieren Sie sich die Befehle, da Sie später dieselben Befehle verwenden, um die Treiber in dem Container zu installieren, der in Docker auf Ihrer GPU-fähigen VM ausgeführt wird.

2.  Installieren Sie die neueste IoT Edge-Runtime auf Ihrem Linux-Server der N-Serie in Azure Stack Hub. Anleitungen hierzu finden Sie unter [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](/azure/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version).

## <a name="install-docker"></a>Installieren von Docker

Installieren Sie Docker auf Ihrer GPU-fähigen VM. Sie führen das Modul aus dem IoT Edge-Marketplace in einem Container auf der VM aus.

Sie müssen Docker 19.02 oder höher installieren. Die Docker-Runtime unterstützt jetzt die NVIDIA-GPUs. Weitere Informationen zu GPUs in Docker finden Sie in den Docker-Dokumenten im Artikel [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/#gpu).

### <a name="install-docker"></a>Installieren von Docker

Docker-Container können überall ausgeführt werden, lokal im Rechenzentrum des Kunden, bei einem externen Dienstanbieter oder in der Cloud auf Azure. Docker-Imagecontainer können nativ unter Linux und Windows ausgeführt werden. Allerdings können Windows-Images nur auf Windows-Hosts ausgeführt werden, während Linux-Images sowohl auf Linux-Hosts als auch auf Windows-Hosts ausgeführt werden können (bis dato mithilfe einer Hyper-V-Linux-VM), wobei Host einen Server oder eine VM bedeutet. Weitere Informationen finden Sie unter [Was ist Docker?](https://docs.microsoft.com/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

1. Stellen Sie über Ihren SSH-Client eine Verbindung mit Ihrem Linux-Server der N-Serie her.

1.  Aktualisieren Sie den apt-Index und die apt-Listen.

    ```bash  
    sudo apt-get update
    ```

1.  Rufen Sie die neuen Versionen von Paketen ab, die auf der VM vorhanden sind.

    ```bash  
    sudo apt-get upgrade
    ```

2.  Installieren Sie die Abhängigkeiten, die erforderlich sind, um das apt-Repository von Docker hinzuzufügen.

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  Fügen Sie den GPG-Schlüssel von Docker hinzu.

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  Fügen Sie das apt-Repository von Docker hinzu.

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  Aktualisieren Sie den apt-Index und die apt-Listen, und installieren Sie Docker Community Edition.

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  Überprüfen Sie die Installation, indem Sie Docker-Version überprüfen.

    ```bash  
    docker -v
    ```

7. Machen Sie die verfügbaren GPU-Ressourcen in Docker verfügbar.

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>Abrufen des Moduls aus dem Marketplace

Kehren Sie zum Azure-Portal zurück, und fügen Sie das Modul Ihrem IoT Edge-Gerät hinzu, indem Sie den IoT-Marketplace verwenden. Wählen Sie im Menü die Option **Marketplace-Modul** aus. Suchen Sie nach `Getting started with GPUs`, und befolgen Sie die Anweisungen, um das Modul hinzuzufügen.

Anweisungen finden Sie unter [Auswählen des Geräts und Hinzufügen von Modulen](/azure/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules).

## <a name="enable-monitoring"></a>Aktivieren der Überwachung

1. Laden Sie [Azure IoT-Explorer-](/azure/iot-pnp/howto-use-iot-explorer) herunter, und verbinden Sie die Anwendung mit Ihrem IoT-Hub.

2. Wählen Sie Ihr IoT-Gerät aus, und navigieren Sie über das Navigationsmenü zu „Telemetrie“.

3. Wählen Sie **Starten** aus, um mit der Überwachung der Ausgabe des IoT Edge-Geräts zu beginnen.

![Eine gültige Installation](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>Überwachen des Moduls (optional)  

1. Führen Sie in der Befehlspalette von VS Code den Befehl **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub auswählen) aus.

2. Wählen Sie das Abonnement und den IoT-Hub aus, die das zu konfigurierende IoT Edge-Gerät enthalten. Wählen Sie in diesem Fall das Abonnement aus, das zur Bereitstellung des Azure Stack Edge-Geräts verwendet wird, und dann das für Ihr Azure Stack Edge-Gerät erstellte IoT Edge-Gerät. Dies erfolgt, wenn Sie Compute über das Azure-Portal in den vorherigen Schritten konfigurieren.

3. Klappen Sie im Visual Studio Code-Explorer den Abschnitt „Azure IoT Hub“ auf. Unter **Geräte** sollte das IoT Edge-Gerät angezeigt werden, das Ihrem Azure Stack Edge-Gerät entspricht. 

    1. Wählen Sie dieses Gerät aus, klicken Sie mit der rechten Maustaste, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus.

    2. Wechseln Sie zu **Geräte > Module**. Hier sollte angezeigt werden, dass Ihr **GPU-Modul** ausgeführt wird.

    3. Im VS Code-Terminal sollten die IoT-Hub-Ereignisse auch als Überwachungsausgabe für Ihr Azure Stack Edge-Gerät angezeigt werden.

    ![Ist eine gültige Installation](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    Wie Sie sehen, ist die Zeit, die für die Ausführung derselben Gruppe von Vorgängen (5.000 Iterationen von Formtransformationen) von der GPU benötigt wird, wesentlich kürzer als bei der CPU.

## <a name="next-steps"></a>Nächste Schritte

  - Weitere Informationen finden Sie unter [Grafikprozessor (GPU)-VM in Azure Stack Hub](gpu-vms-about.md).

  - Weitere Informationen über Azure Stack Hub, Data Box Edge und die Intelligent Edge finden Sie unter [Die Zukunft des Computing: intelligente Cloud und Intelligent Edge](https://azure.microsoft.com/overview/future-of-cloud).

  - Weitere Informationen über Hybrid Cloud-Anwendungen finden Sie unter [Hybrid Cloud-Lösungen](https://docs.microsoft.com/hybrid/app-solutions/).