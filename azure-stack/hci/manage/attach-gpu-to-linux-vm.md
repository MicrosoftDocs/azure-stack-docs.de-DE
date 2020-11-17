---
title: Verknüpfen einer GPU mit einer Linux-VM in Azure Stack HCI
description: Informationen über das Verwenden einer GPU mit KI-Workloads, die auf einer Ubuntu Linux-VM in Azure Stack HCI ausgeführt werden.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/01/2020
ms.openlocfilehash: 422f6984fad6218387673d2dc9292f0ae7cb1739
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493651"
---
# <a name="attaching-a-gpu-to-an-ubuntu-linux-vm-on-azure-stack-hci"></a>Verknüpfen einer GPU mit einer Ubuntu Linux-VM in Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Dieses Thema enthält schrittweise Anleitungen dazu, wie eine NVIDIA-GPU (Graphics Processing Unit) mit Azure Stack HCI über DDA-Technologie (Discrete Device Assignment) für eine Ubuntu-VM installiert und konfiguriert wird.
In diesem Dokument wird davon ausgegangen, dass Sie den Azure Stack HCI-Cluster bereitgestellt und VMs installiert haben.

## <a name="install-the-gpu-and-then-dismount-it-in-powershell"></a>Einbauen der GPU und dann Aufheben von deren Bereitstellung mit PowerShell

1. Bauen Sie die NVIDIA-GPUs physisch in die entsprechenden Server ein, wobei Sie die OEM-Anweisungen und BIOS-Empfehlungen beachten.
2. Schalten Sie jeden Server ein.
3. Melden Sie sich mit einem Konto an, das Administratorrechte für die Server hat, in die die NVIDIA GPUs eingebaut wurden.
4. Öffnen Sie **Geräte-Manager**, und navigieren Sie zum Abschnitt *Weitere Geräte*. Es sollte ein Gerät angezeigt werden, das als „3D-Videocontroller“ aufgelistet wird.
5. Klicken Sie mit der rechten Maustaste auf „3D-Videocontroller“, um die Seite **Eigenschaften** anzuzeigen. Klicken Sie auf **Details**. Wählen Sie in der Dropdownliste unter **Eigenschaften** die Option „Speicherortpfade“ aus.
6. Notieren Sie sich den Wert mit der Zeichenfolge „PCIRoot“ (ist im folgenden Screenshot hervorgehoben). Klicken Sie mit der rechten Maustaste auf **Wert**, und kopieren/speichern Sie den Wert.
    :::image type="content" source="media/attach-gpu-to-linux-vm/pciroot.png" alt-text="Screenshot mit Speicherortpfad":::
7. Öffnen Sie Windows PowerShell mit erhöhten Rechten, und führen Sie das `Dismount-VMHostAssignableDevice`-Cmdlet aus, um die Bereitstellung des GPU-Geräts für DDA in der VM aufzuheben. Ersetzen Sie den Wert von *LocationPath* durch den Wert für das Gerät, den Sie in Schritt 6 abgerufen haben.
    ```PowerShell
    Dismount-VMHostAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -force
    ```
8. Vergewissern Sie sich, dass das Gerät in **Geräte-Manager** unter „Systemgeräte“ mit „Bereitstellung aufgehoben“ aufgeführt ist.
    :::image type="content" source="media/attach-gpu-to-linux-vm/dismounted.png" alt-text="Screenshot für Gerät, dessen Bereitstellung aufgehoben ist":::

## <a name="create-and-configure-an-ubuntu-virtual-machine"></a>Erstellen und Konfigurieren einer Ubuntu-VM

1. Laden Sie [Ubuntu Desktop Release 18.04.02 ISO](http://old-releases.ubuntu.com/releases/18.04.2/) herunter.
2. Öffnen Sie **Hyper-V-Manager** für den Knoten des Systems, in dem die GPU eingebaut ist.
   > [!NOTE]
   > [DDA unterstützt kein Failover](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment). Dies ist eine DDA-Einschränkung für virtuelle Computer. Daher wird empfohlen, nicht **Failovercluster-Manager**, sondern **Hyper-V Manager** zu verwenden, um die VM auf dem Knoten bereitzustellen. Ein Verwenden von **Failovercluster-Manager** mit DDA schlägt mit einer Fehlermeldung fehl, die angibt, dass die VM ein Gerät hat, für das hohe Verfügbarkeit nicht unterstützt wird.
3. Erstellen Sie mit der Ubuntu-ISO, die Sie in Schritt 1 heruntergeladen haben, einen neuen virtuellen Computer mit dem **Assistent für neue virtuelle Computer** in **Hyper-V-Manager**, um eine Ubuntu Gen 1-VM mit 2 GB Arbeitsspeicher und einer zugeordneten Netzwerkkarte zu erstellen.
4. Verwenden Sie in PowerShell die folgenden Cmdlets, um der VM das GPU-Gerät zuzuweisen, dessen Bereitstellung aufgehoben ist. Ersetzen Sie dabei den Wert von *LocationPath* durch den Wert für Ihr Gerät.
    ```PowerShell
    # Confirm that there are no DDA devices assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu

    # Assign the GPU to the VM
    Add-VMAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -VMName Ubuntu

    # Confirm that the GPU is assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu
    ```

    Bei erfolgreicher Zuweisung der GPU zur VM wird die folgende Ausgabe angezeigt:  :::image type="content" source="media/attach-gpu-to-linux-vm/assign-gpu.png" alt-text="Screenshot für Zuweisen von GPU":::

    Konfigurieren Sie weitere Werte entsprechend der GPU-Dokumentation, die Sie [hier](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda) finden:

   ```PowerShell
    # Enable Write-Combining on the CPU
    Set-VM -GuestControlledCacheTypes $true -VMName VMName

    # Configure the 32 bit MMIO space
    Set-VM -LowMemoryMappedIoSpace 3Gb -VMName VMName

    # Configure greater than 32 bit MMIO space
    Set-VM -HighMemoryMappedIoSpace 33280Mb -VMName VMName
   ```

   > [!NOTE]
   > Der Wert „33280Mb“ sollte für die meisten GPUs ausreichen, sollte jedoch durch einen Wert ersetzt werden, der größer ist als der GPU-Speicher.

5. Stellen Sie aus Hyper-V-Manager eine Verbindung mit der VM her, und starten Sie die Installation von Ubuntu. Wählen Sie die Standardeinstellungen aus, mit denen Ubuntu auf der VM installiert werden soll.

6. Verwenden Sie nach Abschluss der Installation **Hyper-V-Manager**, um die VM herunterzufahren, und konfigurieren Sie die **Automatische Stoppaktion** für die VM, um das Gastbetriebssystem herunterzufahren (siehe folgenden Screenshot):  :::image type="content" source="media/attach-gpu-to-linux-vm/guest-shutdown.png" alt-text="Screenshot für Herunterfahren des Gastbetriebssystems":::

7. Melden Sie sich bei Ubuntu an, und öffnen Sie das Terminal, um SSH zu installieren:

   ```shell
    $ sudo apt install openssh-server
   ```

8. Ermitteln Sie mit dem **ifconfig**-Befehl die TCP/IP-Adresse der Ubuntu-Installation, und kopieren Sie die IP-Adresse für die **eth0**-Schnittstelle.

9. Verwenden Sie einen SSH-Client wie OpenSSH (standardmäßige Installation von „ssh.exe“ mit Windows 10) oder [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/), um zur weiteren Konfiguration eine Verbindung mit dem virtuellen Ubuntu-Computer herzustellen.

10. Geben nach der Anmeldung über den SSH-Client den Befehl **Ispci** ein, und überprüfen Sie, ob die NVIDIA-GPU als „3D-Controller“ aufgelistet wird.

    > [!IMPORTANT]
    > Wird die NVIDIA-GPU nicht als „3D-Controller“ aufgelistet, führen Sie keine weiteren Schritte aus. Vergewissern Sie sich, dass die obigen Schritte ausgeführt wurden, bevor Sie den Vorgang fortsetzen.

11. Suchen Sie in der VM nach **Software & Updates**, und öffnen Sie diese Anwendung. Navigieren Sie zu **Additional Drivers**, und wählen Sie dann den neuesten der aufgelisteten NVIDIA-GPU-Treiber aus. Schließen Sie die Treiberinstallation ab, indem Sie auf die Schaltfläche **Apply Changes** klicken.
    :::image type="content" source="media/attach-gpu-to-linux-vm/driver-install.png" alt-text="Screenshot der Treiberinstallation":::

12. Starten Sie die Ubuntu-VM neu, nachdem die Treiberinstallation abgeschlossen wurde. Nachdem die VM gestartet wurde, stellen Sie über den SSH-Client eine Verbindung her, und geben Sie den Befehl **nvidia-smi** ein, um zu überprüfen, ob die Installation des NVIDIA-GPU-Treibers erfolgreich abgeschlossen wurde. Die Ausgabe sollte in etwa wie im folgenden Screenshot aussehen: :::image type="content" source="media/attach-gpu-to-linux-vm/nvidia-smi.png" alt-text="Der Screenshot zeigt die Ausgabe des Befehls „nvidia-smi“.":::

13. Verwenden Sie den SSH-Client, um das Repository einzurichten und die Docker CE-Engine zu installieren:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    ```
    Fügen Sie den offiziellen GPG-Schlüssel von Docker hinzu:

    ```shell
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

    Vergewissern Sie sich, dass Sie nun über den Schlüssel mit dem Fingerabdruck 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 verfügen. Suchen Sie dazu nach den letzten acht Zeichen des Fingerabdrucks:

    ```shell
    $ sudo apt-key fingerprint 0EBFCD88
    ```

    Die zugehörige Ausgabe sollte etwa wie folgt aussehen:

    ```shell
    pub   rsa4096 2017-02-22 [SCEA]
    9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```

    Richten Sie das stabile Repository für die Ubuntu AMD64-Architektur ein:

    ```shell
    $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```

    Aktualisieren Sie die Pakete, und installieren Sie Docker CE:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

    Überprüfen Sie die Installation von Docker CE:

    ```shell
    $ sudo docker run hello-world
    ```

## <a name="configure-azure-iot-edge"></a>Konfigurieren von Azure IoT Edge

Als Vorbereitung auf diese Konfiguration sollten Sie die häufig gestellten Fragen (F. A.Q.) lesen, die im GitHub-Repository [NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) enthalten sind. Dort wird erläutert, warum Docker anstelle von Moby installiert werden muss. Führen Sie danach die folgenden Schritten aus.

### <a name="install-nvidia-docker"></a>Installieren von NVIDIA Docker

1. Fügen Sie über den SSH-Client Paketrepositorys hinzu:

    ```shell
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list

    sudo apt-get update
    ```

2. Installieren Sie „nvidia-docker2“, und laden Sie die Konfiguration des Docker-Daemons neu:

    ```shell
    sudo apt-get install -y nvidia-docker2
    sudo pkill -SIGHUP dockerd
    ```

3. Starten Sie die VM neu:

    ```shell
    sudo /sbin/shutdown -r now
    ```

4. Überprüfen Sie nach dem Neustart, ob NVIDIA Docker erfolgreich installiert wurde:

    ```shell
    sudo docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
    ```

    Für eine erfolgreiche Installation sieht die Ausgabe wie im folgenden Screenshot aus:  :::image type="content" source="media/attach-gpu-to-linux-vm/docker.png" alt-text="Screenshot für erfolgreiche Docker-Installation":::

5. Führen Sie die folgenden Anweisungen aus, um Azure IoT Edge zu installieren, wobei die Runtime-Installation übersprungen wird:

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list

    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo apt-get update

    sudo apt-get install iotedge
    ```

    > [!NOTE]
    > Überprüfen Sie nach der Installation von Azure IoT Edge, ob die Datei „config.yaml“ auf der Ubuntu-VM als „/etc/iotedge/config.yaml“ vorhanden ist.

6. Erstellen Sie eine IoT Edge Geräteidentität im Azure-Portal, indem Sie [dieser Anleitung](/azure/iot-edge/how-to-register-device#register-in-the-azure-portal) folgen. Kopieren Sie danach die Geräteverbindungszeichenfolge für das neu erstellte IoT Edge-Gerät.

7. Aktualisieren Sie über den SSH-Client die Geräteverbindungszeichenfolge in der Datei „config.yaml“ auf der Ubuntu-VM:

    ```shell
    sudo nano /etc/iotedge/config.yaml
    ```

    Suchen Sie nach den Bereitstellungskonfigurationen (provisioning configurations) in der Datei, und heben Sie die Auskommentierung des Abschnitts „Manual provisioning configuration“ auf. Aktualisieren Sie den Wert von „device_connection_string“ mit der Verbindungszeichenfolge Ihres IoT Edge-Geräts. Stellen Sie sicher, dass alle anderen Bereitstellungsabschnitte auskommentiert sind. Stellen Sie sicher, dass der Zeile „provisioning:“ kein Leerzeichen vorangestellt ist und dass geschachtelte Elemente jeweils um zwei Leerzeichen eingerückt sind:

    :::image type="content" source="media/attach-gpu-to-linux-vm/manual-provisioning.png" alt-text="Screenshot für manuelle Bereitstellungskonfiguration":::

    Um den Inhalt der Zwischenablage in Nano einzufügen, drücken Sie UMSCHALT, und klicken Sie mit der rechten Maustaste, oder drücken Sie UMSCHALT + EINFG. Speichern und schließen Sie die Datei (STRG + X, Y, EINGABETASTE).

8. Starten Sie mit dem SSH-Client den IoT Edge-Daemon neu:

    ```shell
    sudo systemctl restart iotedge
    ```

    Überprüfen Sie die Installation, und überprüfen Sie den Status des IoT Edge-Daemons:

    ```shell
    systemctl status iotedge

    journalctl -u iotedge --no-pager --no-full
    ```

9. Erstellen Sie über den SSH-Client die folgende Verzeichnisstruktur auf der Ubuntu-VM:

    ```shell
    cd /var
    sudo mkdir deepstream
    mkdir ./deepstream/custom_configs
    cd /var/deepstream
    sudo mkdir custom_streams
    sudo chmod -R 777 /var/deepstream
    cd ./custom_streams
    ```

10. Stellen Sie sicher, dass „/var/deepstream/custom_streams“ Ihr Arbeitsverzeichnis ist, und laden Sie die [Datei mit den Demovideos](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) herunter, indem Sie den folgenden Befehl im SSH-Client ausführen:

    ```shell
    wget -O cars-streams.tar.gz --no-check-certificate https://onedrive.live.com/download?cid=0C0A4A69A0CDCB4C&resid=0C0A4A69A0CDCB4C%21588371&authkey=AAavgrxG95v9gu0
    ```

    Dekomprimieren Sie die Videodateien:

    ```shell
    tar -xzvf cars-streams.tar.gz
    ```

    Der Inhalt des Verzeichnisses „/var/deepstream/custom_streams“ sollte dem folgenden Screenshot ähneln:

    :::image type="content" source="media/attach-gpu-to-linux-vm/custom-streams.png" alt-text="Screenshot der benutzerdefinierten Streams":::

11. Erstellen Sie eine neue Datei namens „test5_config_file_src_infer_azure_iotedge_edited. txt“ im Verzeichnis „/var/deepstream/custom_configs“. Öffnen Sie die Datei mit einem Text-Editor, fügen Sie den folgenden Code ein, und speichern und schließen Sie dann die Datei.

    ```shell
    # Copyright (c) 2018 NVIDIA Corporation.  All rights reserved.
    #
    # NVIDIA Corporation and its licensors retain all intellectual property
    # and proprietary rights in and to this software, related documentation
    # and any modifications thereto.  Any use, reproduction, disclosure or
    # distribution of this software and related documentation without an express
    # license agreement from NVIDIA Corporation is strictly prohibited.

    [application]
    enable-perf-measurement=1
    perf-measurement-interval-sec=5
    #gie-kitti-output-dir=streamscl

    [tiled-display]
    enable=1
    rows=2
    columns=2
    width=1280
    height=720
    gpu-id=0
    #(0): nvbuf-mem-default - Default memory allocated, specific to particular platform
    #(1): nvbuf-mem-cuda-pinned - Allocate Pinned/Host cuda memory, applicable for Tesla
    #(2): nvbuf-mem-cuda-device - Allocate Device cuda memory, applicable for Tesla
    #(3): nvbuf-mem-cuda-unified - Allocate Unified cuda memory, applicable for Tesla
    #(4): nvbuf-mem-surface-array - Allocate Surface Array memory, applicable for Jetson
    nvbuf-memory-type=0

    [source0]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0

    [source1]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0

    [sink0]
    enable=0

    [sink3]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=RTSPStreaming
    type=4
    #1=h264 2=h265
    codec=1
    sync=0
    bitrate=4000000
    # set below properties in case of RTSPStreaming
    rtsp-port=8554
    udp-port=5400

    [sink1]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=UDPSink 5=nvoverlaysink 6=MsgConvBroker
    type=6
    msg-conv-config=../configs/dstest5_msgconv_sample_config.txt
    #(0): PAYLOAD_DEEPSTREAM - Deepstream schema payload
    #(1): PAYLOAD_DEEPSTREAM_MINIMAL - Deepstream schema payload minimal
    #(256): PAYLOAD_RESERVED - Reserved type
    #(257): PAYLOAD_CUSTOM   - Custom schema payload
    msg-conv-payload-type=1
    msg-broker-proto-lib=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_azure_edge_proto.so
    topic=mytopic
    #Optional:
    #msg-broker-config=../../../../libs/azure_protocol_adaptor/module_client/cfg_azure.txt

    [sink2]
    enable=0
    type=3
    #1=mp4 2=mkv
    container=1
    #1=h264 2=h265 3=mpeg4
    ## only SW mpeg4 is supported right now.
    codec=3
    sync=1
    bitrate=2000000
    output-file=out.mp4
    source-id=0

    [osd]
    enable=1
    gpu-id=0
    border-width=1
    text-size=15
    text-color=1;1;1;1;
    text-bg-color=0.3;0.3;0.3;1
    font=Arial
    show-clock=0
    clock-x-offset=800
    clock-y-offset=820
    clock-text-size=12
    clock-color=1;0;0;0
    nvbuf-memory-type=0

    [streammux]
    gpu-id=0
    ##Boolean property to inform muxer that sources are live
    live-source=0
    batch-size=4
    ##time out in usec, to wait after the first buffer is available
    ##to push the batch even if the complete batch is not formed
    batched-push-timeout=40000
    ## Set muxer output width and height
    width=1920
    height=1080
    ##Enable to maintain aspect ratio wrt source, and allow black borders, works
    ##along with width, height properties
    enable-padding=0
    nvbuf-memory-type=0

    [primary-gie]
    enable=1
    gpu-id=0
    batch-size=4
    ## 0=FP32, 1=INT8, 2=FP16 mode
    bbox-border-color0=1;0;0;1
    bbox-border-color1=0;1;1;1
    bbox-border-color2=0;1;1;1
    bbox-border-color3=0;1;0;1
    nvbuf-memory-type=0
    interval=0
    gie-unique-id=1
    model-engine-file=../../../../../samples/models/Primary_Detector/resnet10.caffemodel_b4_int8.engine
    labelfile-path=../../../../../samples/models/Primary_Detector/labels.txt
    config-file=../../../../../samples/configs/deepstream-app/config_infer_primary.txt
    #infer-raw-output-dir=../../../../../samples/primary_detector_raw_output/

    [tracker]
    enable=1
    tracker-width=600
    tracker-height=300
    ll-lib-file=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_mot_klt.so
    #ll-config-file required for DCF/IOU only
    #ll-config-file=tracker_config.yml
    #ll-config-file=iou_config.txt
    gpu-id=0
    #enable-batch-process applicable to DCF only
    enable-batch-process=0

    [tests]
    file-loop=1
    ```

12. Navigieren Sie zum Azure-Portal. Wählen Sie **IoT Hub Bereitgestellt** aus, klicken Sie auf **Automatische Geräteverwaltung**, und klicken Sie dann auf **IoT Edge**:

    :::image type="content" source="media/attach-gpu-to-linux-vm/iot-edge.png" alt-text="Screenshot für „Automatische Geräteverwaltung“":::

13. Wählen Sie im rechten Bereich die Geräteidentität aus, deren Geräteverbindungszeichenfolge oben verwendet wurde. Klicken Sie auf „Module festlegen“:

    :::image type="content" source="media/attach-gpu-to-linux-vm/set-modules.png" alt-text="Screenshot für „Module festlegen“":::

14. Klicken Sie unter „IoT Edge-Module“ auf „IoT Edge-Modul“:

    :::image type="content" source="media/attach-gpu-to-linux-vm/marketplace-module.png" alt-text="Screenshot für „IoT Edge-Modul hinzufügen“":::

15. Wählen Sie im Bereich **IoT Edge-Modul hinzufügen** die Registerkarte **Moduleinstellungen** aus, und geben Sie die folgenden Werte ein, oder wählen Sie diese aus:

    - **Name des IoT Edge-Moduls**: NVIDIADeepStreamSDK

    - **Image-URI**: marketplace.azurecr.io/nvidia/deepstream-iot2

    - **Neustartrichtlinie**: Immer

    - **Gewünschter Status**: Laufen

    - **Image Pull Policy**: *leer*
    
    Wählen Sie **Hinzufügen**.

    :::image type="content" source="media/attach-gpu-to-linux-vm/deepstream-module-settings.png" alt-text="Screenshot für „DeepStream SDK“":::

16. Stellen Sie sicher, dass das Modul „NvidiaDeepStreamSDK“ unter „IoT Edge-Module“ aufgelistet wird:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-modules.png" alt-text="Screenshot für „IoT Edge-Module“":::

17. Klicken Sie auf das Modul „NVIDIADeepStreamSDK“, und wählen Sie „Optionen für Containererstellung“ aus. Die Standardkonfiguration sieht wie folgt aus:

    :::image type="content" source="media/attach-gpu-to-linux-vm/container-create-options.png" alt-text="Screenshot für „Optionen für Containererstellung“":::

    Ersetzen Sie die obige Konfiguration durch die folgende Konfiguration:

    ```shell
    {
      "ExposedPorts": {
        "8554/tcp": {}
      },
      "Entrypoint": [
        "/usr/bin/deepstream-test5-app",
        "-c",
        "test5_config_file_src_infer_azure_iotedge_edited.txt",
        "-p",
        "1",
        "-m",
        "1"
      ],
      "HostConfig": {
        "runtime": "nvidia",
        "Binds": [
          "/var/deepstream/custom_configs:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/",
          "/var/deepstream/custom_streams:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_streams/"
        ],
        "PortBindings": {
          "8554/tcp": [
            {
              "HostPort": "8554"
            }
          ]
        }
      },
      "WorkingDir": "/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/"
    }
    ```

18. Klicken Sie auf **Überprüfen und erstellen**, und klicken Sie auf der nächsten Seite auf **Erstellen**. Sie sollten nun die drei nachstehend aufgeführten Module für Ihr IoT Edge-Gerät im Azure-Portal sehen:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-hub-connections.png" alt-text="Screenshot für „Module“ und „IoT Edge-Hubverbindungen“":::

19. Stellen Sie über den SSH-Client eine Verbindung mit der Ubuntu-VM her, und überprüfen Sie, ob die richtigen Module ausgeführt werden:

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-sudo.png" alt-text="Der Screenshot zeigt die Ausgabe der iotedge-Liste.":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-nvidia-smi.png" alt-text="Screenshot für „nvidia-smi“":::

    > [!NOTE]
    > Es dauert einige Minuten, bis der „NvidiaDeepstream“-Container heruntergeladen ist. Sie können den Download mit dem Befehl „journalctl -u iotedge --no-pager --no-full“ überprüfen, um sich die iotedge-Daemon-Protokolle anzusehen.

20. Vergewissern Sie sich, dass der „NvidiaDeepstream“-Container funktionsbereit ist. Die Befehlsausgabe in den nachfolgenden Screenshots kennzeichnet eine erfolgreiche Ausführung.

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify1.png" alt-text="Der Screenshot der Ausgabe, die zeigt, dass der NvdiaDeepStreem-Container betriebsbereit ist.":::

    ```shell
    sudo iotedge logs -f NVIDIADeepStreamSDK
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify2.png" alt-text="Der Screenshot zeigt die Ausgabe für den NVIDIADeepStreamSDK-Befehl „iotedge logs -f“.":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify3.png" alt-text="Der Screenshot zeigt die zusätzliche Ausgabe für den Befehl „nvidia-smi“.":::

21. Bestätigen Sie die TCP/IP-Adresse für Ihre Ubuntu-VM mit dem Befehl **ifconfig**, und suchen Sie nach der TCP/IP-Adresse neben der **eth0**-Schnittstelle.

22. Installieren Sie den VLC media player auf Ihrer Arbeitsstation. Klicken Sie im VLC media player auf **Medien > Netzwerkstream öffnen**, und geben Sie die Adresse im folgenden Format ein:

    rtsp://ipaddress:8554/ds-test

    Dabei ist „ipaddress“ die TCP/IP-Adresse Ihrer VM.

    :::image type="content" source="media/attach-gpu-to-linux-vm/vlc-player.png" alt-text="Screenshot für VLC media player":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu GPUs und DDA finden Sie auch hier:

- [Planen der Bereitstellung von Geräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment)
- [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)
