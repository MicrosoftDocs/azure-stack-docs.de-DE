---
title: Vorbereiten eines auf Red Hat basierenden virtuellen Computers für Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Red Hat-Linux-Betriebssystem enthält.
author: sethmanheim
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: 8d6a94214d45fcf9d008f93d1ce726d7af536d77
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890237"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Vorbereiten eines auf Red Hat basierenden virtuellen Computers für Azure Stack Hub

In diesem Artikel erfahren Sie, wie Sie einen auf Red Hat Enterprise Linux (RHEL) basierenden virtuellen Computer für die Verwendung in Azure Stack Hub vorbereiten. In diesem Artikel werden die RHEL-Versionen 7.1 und höher behandelt. Darüber hinaus werden in diesem Artikel die Hypervisoren Hyper-V, KVM und VMware für die Vorbereitung vorgestellt.

Informationen zur Unterstützung von Red Hat Enterprise Linux finden Sie unter [Red Hat and Azure Stack Hub: Frequently Asked Questions](https://access.redhat.com/articles/3413531) (Red Hat und Azure Stack: häufig gestellte Fragen).

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Vorbereiten eines auf Red Hat basierenden virtuellen Computers über den Hyper-V-Manager

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits eine ISO-Datei von der Red Hat-Website besitzen und das RHEL-Image auf einer virtuellen Festplatte (VHD) installiert haben. Weitere Informationen zum Installieren eines Betriebssystemimages mit dem Hyper-V-Manager finden Sie unter [Installieren von Hyper-V und Erstellen eines virtuellen Computers](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Installationshinweise für RHEL

* Das VHDX-Format wird von Azure Stack Hub nicht unterstützt. Azure unterstützt nur feste virtuelle Festplatten. Sie können Hyper-V Manager verwenden, um den Datenträger in das VHD-Format zu konvertieren, oder Sie können das convert-vhd-Cmdlet verwenden. Wählen Sie bei Verwendung von VirtualBox die Option **Feste Größe** und nicht die standardmäßig dynamisch zugeordnete Option, wenn Sie den Datenträger erstellen.
* Azure Stack Hub unterstützt nur virtuelle Computer der 1. Generation. Sie können virtuelle Computer der 1. Generation vom VHDX- in das VHD-Dateiformat und von einem dynamisch erweiterbaren Datenträger in einen Datenträger mit fester Größe konvertieren. Die Generation eines virtuellen Computers kann nicht geändert werden. Weitere Informationen finden Sie unter [Sollte ich einen virtuellen Computer der 1. oder der 2. Generation in Hyper-V erstellen?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Die maximal zulässige Größe für die virtuelle Festplatte beträgt 1.023 GB.
* Beim Installieren des Linux-Betriebssystems wird empfohlen, anstelle von LVM (Logical Volume Manager) – bei vielen Installationen oftmals voreingestellt – die Standardpartitionen zu verwenden. So werden LVM-Namenskonflikte mit geklonten virtuellen Computern vermieden. Dies gilt insbesondere dann, wenn Sie einen Betriebssystemdatenträger zur Problembehandlung an einen anderen identischen virtuellen Computer anfügen müssen.
* Kernel-Unterstützung für die Bereitstellung von UDF-Dateisystemen (Universal Disk Format) ist erforderlich. Beim ersten Starten übergibt das Medium im UDF-Format, das an den Gast angefügt ist, die Bereitstellungskonfiguration an den virtuellen Linux-Computer. Der Azure Linux-Agent muss das UDF-Dateisystem einbinden, um die Konfiguration zu lesen und den virtuellen Computer bereitzustellen.
* Konfigurieren Sie auf dem Betriebssystemdatenträger keine Swap-Partition. Der Linux-Agent kann so konfiguriert werden, dass auf dem temporären Ressourcendatenträger eine Auslagerungsdatei erstellt wird. Weitere Informationen hierzu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Stellen Sie beim Konvertieren von einem RAW-Datenträger in VHD sicher, dass die Größe des RAW-Datenträgers vor der Konvertierung ein Vielfaches von 1 MB beträgt. Einzelheiten erfahren Sie im folgenden Schritt.
* Azure Stack Hub unterstützt cloud-init. [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) ist ein weit verbreiteter Ansatz zum Anpassen einer Linux-VM beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs aufgerufen wird, müssen Sie keine zusätzlichen Schritte oder Agents auf Ihre Konfiguration anwenden. Anweisungen zum Hinzufügen von cloud-init zu Ihrem Image finden Sie unter [Vorbereiten eines vorhandenen Linux-Azure-VM-Images für die Verwendung mit cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/cloudinit-prepare-custom-image).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>Vorbereiten eines virtuellen RHEL 7-Computers über den Hyper-V-Manager

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

1. Wählen Sie **Verbinden** aus, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ggf. den folgenden Text hinzu:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```bash
    sudo systemctl enable network
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Mit dieser Änderung wird sichergestellt, dass alle Konsolennachrichten an den ersten seriellen Port gesendet werden. Dies kann den Azure-Support bei der Behebung von Fehlern unterstützen. Außerdem werden bei dieser Konfiguration die neuen RHEL 7-Benennungskonventionen für Netzwerkkarten deaktiviert.

   Weder grafische Startvorgänge noch Startvorgänge im stillen Modus sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Dieser Parameter reduziert den verfügbaren Arbeitsspeicher des virtuellen Computers um mindestens 128 MB. Dies kann bei kleineren virtuellen Computern Probleme verursachen. Es wird empfohlen, die folgenden Parameter zu entfernen:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Nachdem Sie die Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. [Optional nach Release 1910] Entfernen und Deinstallieren von cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und so konfiguriert ist, dass er beim Booten hochfährt. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

    ```sh
    ClientAliveInterval 180
    ```

1. Wenn Sie eine benutzerdefinierte VHD für Azure Stack Hub erstellen, denken Sie daran, dass die WALinuxAgent-Versionen zwischen 2.2.20 und 2.2.35 (beide exklusiv) in Azure Stack Hub-Umgebungen vor dem Release 1910 nicht funktionieren. Sie können die Versionen 2.2.20/2.2.35 zum Vorbereiten des Images verwenden. Um zum Vorbereiten Ihres benutzerdefinierten Images Versionen ab 2.2.35 zu verwenden, aktualisieren Sie Ihre Azure Stack Hub-Version auf Release 1903 oder höher, oder installieren Sie den 1901/1902-Hotfix.
    
    [Vor Release 1910] Befolgen Sie diese Anweisungen, um einen kompatiblen WALinuxAgent herunterzuladen:
    
    1. Laden Sie die Setuptools herunter:
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
    
    1. Laden Sie die Version 2.2.20 des Agents von unserem GitHub herunter, und entpacken Sie sie.

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    1. Installieren Sie „setup.py“:

        ```bash
        sudo python setup.py install
        ```

    1. Starten Sie „waagent“ neu:
    
        ```bash
        sudo systemctl restart waagent
        ```

    1. Testen Sie, ob die Agent-Version mit der heruntergeladenen Version übereinstimmt. In diesem Beispiel sollte es Version 2.2.20 sein.

        ```bash
        waagent -version
        ```

    [Nach Release 1910] Befolgen Sie diese Anweisungen, um einen kompatiblen WALinuxAgent herunterzuladen:
    
    1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
    

1. Erstellen Sie auf dem Betriebssystemdatenträger keinen Auslagerungsbereich.

    Der Azure Linux-Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft wird. Der lokale Ressourcendatenträger ist ein temporärer Datenträger und kann geleert werden, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents im vorherigen Schritt die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Wenn Sie die Registrierung  des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

    ```bash
    sudo subscription-manager unregister
    ```

1. Wenn Sie ein System verwenden, das mit einer Zertifizierungsstelle für Unternehmen bereitgestellt wurde, stuft der virtuelle RHEL-Computer das Azure Stack Hub-Stammzertifikat als nicht vertrauenswürdig ein. Verwenden Sie zum Platzieren den vertrauenswürdigen Stammspeicher. Weitere Informationen finden Sie unter [Hinzufügen vertrauenswürdiger Stammzertifikate zum Server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Führen Sie die folgenden Befehle aus, um die Bereitstellung des virtuellen Computers aufzuheben und ihn für die Bereitstellung in Azure vorzubereiten:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Wählen Sie im Hyper-V-Manager **Aktion** > **Herunterfahren** aus.

1. Konvertieren Sie die VHD in eine VHD mit fester Größe mit der Hyper-V-Manager-Funktion „Datenträger bearbeiten“ oder dem PowerShell-Befehl „Convert-VHD“. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Vorbereiten eines auf Red Hat basierenden virtuellen Computers über KVM

1. Laden Sie das KVM-Image von RHEL 7 von der Red Hat-Website herunter. Bei diesem Verfahren wird RHEL 7 als Beispiel verwendet.

1. Legen Sie ein Stammkennwort fest.

    Generieren Sie ein verschlüsseltes Kennwort, und kopieren Sie die Ausgabe des Befehls:

    ```bash
    openssl passwd -1 changeme
    ```

   Legen Sie ein Stammkennwort mit Guestfish fest:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Ändern Sie im zweiten Feld des Stammbenutzers "!!" in das verschlüsselte Kennwort.

1. Erstellen Sie einen virtuellen Computer in KVM über das qcow2-Image. Legen Sie den Datenträgertyp auf **qcow2** und als Gerätemodell der virtuellen Netzwerkschnittstelle **virtio** fest. Starten Sie anschließend den virtuellen Computer, und melden Sie sich als Stammbenutzer an.

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```bash
    sudo systemctl enable network
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`, um diese Konfiguration durchzuführen. Beispiel:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Mit diesem Befehl wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Außerdem werden mit dem Befehl die neuen RHEL 7-Benennungskonventionen für Netzwerkkarten deaktiviert.

   Weder grafische Startvorgänge noch Startvorgänge im stillen Modus sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Dieser Parameter reduziert den verfügbaren Arbeitsspeicher des virtuellen Computers um mindestens 128 MB. Dies kann bei kleineren virtuellen Computern Probleme verursachen. Es wird empfohlen, die folgenden Parameter zu entfernen:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Nachdem Sie die Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Fügen Sie „initramfs“ Hyper-V-Module hinzu:

    Bearbeiten Sie `/etc/dracut.conf` , und fügen Sie Inhalt hinzu:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Erstellen Sie „initramfs“ neu:

    ```bash
    dracut -f -v
    ```

1. [Optional nach Release 1910] Beenden und Deinstallieren von cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:

    ```bash
    systemctl enable sshd
    ```

    Ändern Sie die Datei „/etc/ssh/sshd_config“, sodass sie die folgenden Zeilen enthält:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Wenn Sie eine benutzerdefinierte VHD für Azure Stack Hub erstellen, denken Sie daran, dass die WALinuxAgent-Versionen zwischen 2.2.20 und 2.2.35 (beide exklusiv) in Azure Stack Hub-Umgebungen vor dem Release 1910 nicht funktionieren. Sie können die Versionen 2.2.20/2.2.35 zum Vorbereiten des Images verwenden. Um zum Vorbereiten Ihres benutzerdefinierten Images Versionen ab 2.2.35 zu verwenden, aktualisieren Sie Ihre Azure Stack Hub-Version auf Release 1903 oder höher, oder installieren Sie den 1901/1902-Hotfix.

    [Vor Release 1910] Befolgen Sie diese Anweisungen, um einen kompatiblen WALinuxAgent herunterzuladen:

    1. Laden Sie die Setuptools herunter:
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Laden Sie die Version 2.2.20 des Agents von unserem GitHub herunter, und entpacken Sie sie.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Installieren Sie „setup.py“:
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Starten Sie „waagent“ neu:
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Testen Sie, ob die Agent-Version mit der heruntergeladenen Version übereinstimmt. In diesem Beispiel sollte es Version 2.2.20 sein.
        
        ```bash
        waagent -version
        ```
        
    [Nach Release 1910] Befolgen Sie diese Anweisungen, um einen kompatiblen WALinuxAgent herunterzuladen:
    
    1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

        1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

            ```bash
            sudo yum install WALinuxAgent
            sudo systemctl enable waagent.service
            ```

1. Erstellen Sie auf dem Betriebssystemdatenträger keinen Auslagerungsbereich.

    Der Azure Linux-Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft wird. Der lokale Ressourcendatenträger ist ein temporärer Datenträger und kann geleert werden, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents im vorherigen Schritt die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

    ```bash
    subscription-manager unregister
    ```

1. Wenn Sie ein System verwenden, das mit einer Zertifizierungsstelle für Unternehmen bereitgestellt wurde, stuft der virtuelle RHEL-Computer das Azure Stack Hub-Stammzertifikat als nicht vertrauenswürdig ein. Verwenden Sie zum Platzieren den vertrauenswürdigen Stammspeicher. Weitere Informationen finden Sie unter [Hinzufügen vertrauenswürdiger Stammzertifikate zum Server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Führen Sie die folgenden Befehle aus, um die Bereitstellung des virtuellen Computers aufzuheben und ihn für die Bereitstellung in Azure vorzubereiten:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Fahren Sie den virtuellen Computer in KVM herunter.

1. Konvertieren Sie das qcow2-Image in das VHD-Format.

    > [!NOTE]
    > Es gibt einen bekannten Fehler in den qemu-img-Versionen ab Version 2.2.1, der zu einer nicht ordnungsgemäßen Formatierung der virtuellen Festplatte führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder eine niedrigere Version verwenden oder auf mindestens 2.6 aktualisieren. Referenz: https://bugs.launchpad.net/qemu/+bug/1490611.

    Konvertieren Sie das Bild zuerst in das raw-Format:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Bei QEMU-Version **2.6 oder höher** müssen Sie die Option `force_size` einschließen.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Vorbereiten eines auf Red Hat basierenden virtuellen Computers über VMware

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen virtuellen RHEL-Computer in VMware installiert haben. Weitere Informationen zum Installieren eines Betriebssystems in VMware finden Sie im [VMware-Installationshandbuch für Gastbetriebssysteme](https://aka.ms/aa6z600).

* Beim Installieren des Linux-Betriebssystems wird empfohlen, anstelle von LVM – bei vielen Installationen oftmals voreingestellt – die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, insbesondere dann, wenn ein Betriebssystemdatenträger zur Fehlerbehebung an einen anderen virtuellen Computer angefügt wird. LVM oder RAID können bei Bedarf auf Datenträgern verwendet werden.
* Konfigurieren Sie auf dem Betriebssystemdatenträger keine Swap-Partition. Sie können den Linux-Agent zur Erstellung einer Auslagerungsdatei auf dem temporären Ressourcendatenträger konfigurieren. Weitere Informationen zu dieser Konfiguration finden Sie in den folgenden Schritten.
* Wählen Sie beim Erstellen der virtuellen Festplatte **Virtuellen Datenträger als einzelne Datei speichern**aus.

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>Vorbereiten eines virtuellen RHEL 7-Computers über VMware

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```bash
    sudo chkconfig network on
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Durch diese Konfiguration wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Außerdem werden die neuen RHEL 7-Benennungskonventionen für Netzwerkkarten deaktiviert. Es wird empfohlen, die folgenden Parameter zu entfernen:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Weder grafische Startvorgänge noch Startvorgänge im stillen Modus sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Dieser Parameter reduziert den verfügbaren Arbeitsspeicher des virtuellen Computers um mindestens 128 MB. Dies kann bei kleineren virtuellen Computern Probleme verursachen.

1. Nachdem Sie die Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Fügen Sie „initramfs“ Hyper-V-Module hinzu.

    Bearbeiten Sie `/etc/dracut.conf`und fügen Sie Inhalt hinzu:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Erstellen Sie „initramfs“ neu:

    ```bash
    dracut -f -v
    ```

1. [Optional nach Release 1910] Beenden und Deinstallieren von cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist normalerweise die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

    ```sh
    ClientAliveInterval 180
    ```

1. Wenn Sie eine benutzerdefinierte VHD für Azure Stack Hub erstellen, denken Sie daran, dass die WALinuxAgent-Versionen zwischen 2.2.20 und 2.2.35 (beide exklusiv) in Azure Stack Hub-Umgebungen vor dem Release 1910 nicht funktionieren. Sie können die Versionen 2.2.20/2.2.35 zum Vorbereiten des Images verwenden. Um zum Vorbereiten Ihres benutzerdefinierten Images Versionen ab 2.2.35 zu verwenden, aktualisieren Sie Ihre Azure Stack Hub-Version auf Release 1903 oder höher, oder installieren Sie den 1901/1902-Hotfix.

    [Vor Release 1910] Befolgen Sie diese Anweisungen, um einen kompatiblen WALinuxAgent herunterzuladen:

    1. Laden Sie die Setuptools herunter:
    
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Laden Sie die Version 2.2.20 des Agents von unserem GitHub herunter, und entpacken Sie sie.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Installieren Sie „setup.py“:
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Starten Sie „waagent“ neu:
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Testen Sie, ob die Agent-Version mit der heruntergeladenen Version übereinstimmt. In diesem Beispiel sollte es Version 2.2.20 sein.
        
        ```bash
        waagent -version
        ```
        
    [Nach Release 1910] Befolgen Sie diese Anweisungen, um einen kompatiblen WALinuxAgent herunterzuladen:
    
    1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:
        
        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
        
1. Erstellen Sie auf dem Betriebssystemdatenträger keinen Auslagerungsbereich.

    Der Azure Linux-Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft wird. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents im vorherigen Schritt die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Wenn Sie die Registrierung  des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

    ```bash
    sudo subscription-manager unregister
    ```

1. Wenn Sie ein System verwenden, das mit einer Zertifizierungsstelle für Unternehmen bereitgestellt wurde, stuft der virtuelle RHEL-Computer das Azure Stack Hub-Stammzertifikat als nicht vertrauenswürdig ein. Verwenden Sie zum Platzieren den vertrauenswürdigen Stammspeicher. Weitere Informationen finden Sie unter [Hinzufügen vertrauenswürdiger Stammzertifikate zum Server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Führen Sie die folgenden Befehle aus, um die Bereitstellung des virtuellen Computers aufzuheben und ihn für die Bereitstellung in Azure vorzubereiten:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Fahren Sie den virtuellen Computer herunter, und konvertieren Sie die VMDK-Datei in das VHD-Format.

    > [!NOTE]
    > Es gibt einen bekannten Fehler in den qemu-img-Versionen ab Version 2.2.1, der zu einer nicht ordnungsgemäßen Formatierung der virtuellen Festplatte führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder eine niedrigere Version verwenden oder auf mindestens 2.6 aktualisieren. Referenz: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Konvertieren Sie das Bild zuerst in das raw-Format:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Bei QEMU-Version **2.6 oder höher** müssen Sie die Option `force_size` einschließen.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Automatisches Vorbereiten eines auf Red Hat basierenden virtuellen Computers aus einer ISO-Datei mit einer Kickstart-Datei

1. Erstellen Sie eine Kickstart-Datei mit dem folgenden Inhalt, und speichern Sie die Datei. Das Beenden und Deinstallieren von cloud-init ist optional (cloud-init wird in Azure Stack Hub nach Release 1910 unterstützt). Installieren Sie den Agent erst nach Release 1910 aus dem RedHat-Repository. Verwenden Sie vor Release 1910 das Azure-Repository wie im vorherigen Abschnitt beschrieben. Weitere Informationen zur Kickstart-Installation finden Sie im [Kickstart-Installationshandbuch](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Legen Sie die Kickstart-Datei an einem Speicherort ab, an dem das Installationssystem darauf zugreifen kann.

1. Erstellen Sie in Hyper-V Manager einen neuen virtuellen Computer. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Virtuelle Festplatte später zuordnen**, und schließen Sie den Assistenten für neue virtuelle Computer ab.

1. Öffnen Sie die VM-Einstellungen:

    a. Ordnen Sie dem virtuellen Computer eine neue virtuelle Festplatte zu. Wählen Sie **VHD-Format** und **Feste Größe** aus.

    b. Ordnen Sie die ISO-Installationsdatei dem DVD-Laufwerk zu.

    c. Legen Sie fest, dass BIOS von der CD starten soll.

1. Starten Sie den virtuellen Computer. Wenn die Installationsanweisungen angezeigt werden, drücken Sie die **TAB-TASTE** , um die Startoptionen zu konfigurieren.

1. Geben Sie am Ende der Startoptionen `inst.ks=<the location of the kickstart file>` ein und drücken Sie die **EINGABETASTE**.

1. Warten Sie, bis die Installation abgeschlossen ist. Wenn sie abgeschlossen ist, wird der virtuelle Computer automatisch heruntergefahren. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Der Hyper-V-Treiber konnte dem anfänglichen RAM-Datenträger nicht hinzugefügt werden, wenn ein anderer Hypervisor als ein Hyper-V-Hypervisor verwendet wurde.

In einigen Fällen enthalten Linux-Installationsprogramme unter Umständen keine Treiber für Hyper-V auf dem anfänglichen RAM-Datenträger („initrd“ oder „initramfs“), sofern von Linux nicht die Ausführung in einer Hyper-V-Umgebung erkannt wird.

Wenn ein anderes Virtualisierungssystem (z. B. Oracle VM VirtualBox, Xen Project usw.) zur Vorbereitung des Linux-Images verwendet wird, müssen Sie unter Umständen „initrd“ neu erstellen, um sicherzustellen, dass mindestens die Kernelmodule „hv_vmbus“ und „hv_storvsc“ auf dem anfänglichen RAM-Datenträger verfügbar sind. Dies ist zumindest auf Systemen, die auf der Red Hat-Upstream-Verteilung basieren, ein bekanntes Problem.

Um dieses Problem zu beheben, müssen Sie „initramfs“ Hyper-V-Module hinzufügen und das Archiv neu erstellen:

Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie den folgenden Inhalt hinzu:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Erstellen Sie „initramfs“ neu:

```bash
dracut -f -v
```

Weitere Informationen finden Sie unter [Erneutes Erstellen von „initramfs“](https://access.redhat.com/solutions/1958) (in englischer Sprache).

## <a name="next-steps"></a>Nächste Schritte

Sie können jetzt mit Ihrer Red Hat Enterprise Linux-VHD neue virtuelle Azure-Computer in Azure Stack Hub erstellen. Ist dies das erste Mal, dass Sie die VHD-Datei in Azure Stack Hub hochladen, finden Sie weitere Informationen unter [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).

Weitere Informationen zu Hypervisoren, die zum Ausführen von Red Hat Enterprise Linux zertifiziert sind, finden Sie auf der [Red Hat-Website](https://access.redhat.com/certified-hypervisors).
