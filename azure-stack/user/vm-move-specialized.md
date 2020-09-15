---
title: Verschieben einer spezialisierten VM aus der lokalen Umgebung in Azure Stack Hub
description: Erfahren Sie mehr zum Verschieben einer spezialisierten VM aus der lokalen Umgebung in Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 383e96889f742b05a5f4b25e91bab48e1fd4c075
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609915"
---
# <a name="move-a-specialized-vm-from-on-premises-to-azure-stack-hub"></a>Verschieben einer spezialisierten VM aus der lokalen Umgebung in Azure Stack Hub

Sie können ein Image eines virtuellen Computers (VM) aus Ihrer lokalen Umgebung hinzufügen. Sie können Ihr Image als virtuelle Festplatte (VHD) erstellen und in ein Speicherkonto in Ihrer Azure Stack Hub-Instanz hochladen. Anschließend können Sie eine VM anhand der VHD erstellen.

Ein spezielles Datenträgerimage ist eine Kopie einer virtuellen Festplatte (VHD) einer bereits vorhandenen VM, die die Benutzerkonten, Anwendungen und andere Statusdaten Ihrer ursprünglichen VM enthält. Dabei handelt es sich normalerweise um das Format, in dem VMs zu Azure Stack Hub migriert werden. Spezialisierte VHDs eignen sich gut für die Migration virtueller Computer aus einer lokalen Umgebung zu Azure Stack Hub.

## <a name="how-to-move-an-image"></a>Verschieben eines Images

Informieren Sie sich im Abschnitt, der sich speziell auf Ihre Anforderungen bei der Vorbereitung Ihrer VHD bezieht.

#### <a name="windows-vm"></a>[Windows-VM](#tab/port-win)

- Führen Sie die Schritte in [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) aus, um die VHD ordnungsgemäß vorzubereiten. Sie müssen eine VHD für Azure Stack Hub verwenden.
   > [!NOTE]  
   > Generalisieren Sie den virtuellen Computer **nicht** mithilfe von Sysprep.
- Entfernen Sie alle auf dem virtuellen Computer installierten Gastvirtualisierungstools und Agents (z.B. VMware-Tools).
- Stellen Sie sicher, dass der virtuelle Computer so konfiguriert ist, dass die IP-Adressen und DNS-Einstellungen über DHCP abgerufen werden. Dadurch wird sichergestellt, dass der Server beim Starten eine IP-Adresse innerhalb des virtuellen Netzwerks bezieht.
- Stellen Sie sicher, dass RDP/SSH aktiviert ist und die Firewall Kommunikation zulässt.
- Stellen Sie sicher, dass der VM-Agent (`.msi`) verfügbar ist, um VM-Erweiterungen bereitzustellen. Anleitungen finden Sie unter [Übersicht über den Agent für virtuelle Azure-Computer](/azure/virtual-machines/extensions/agent-windows). Wenn der VM-Agent auf der virtuellen Festplatte nicht vorhanden ist, tritt bei der Erweiterungsbereitstellung ein Fehler auf. Die Festlegung des Betriebssystemprofils oder von `$vm.OSProfile.AllowExtensionOperations = $true` ist nicht bei der Bereitstellung erforderlich.

#### <a name="linux-vm"></a>[Linux-VM](#tab/port-linux)

#### <a name="generalize-the-vhd"></a>Generalisieren der VHD

Befolgen Sie die entsprechenden Anweisungen zum Vorbereiten der VHD für Ihr Linux-Betriebssystem:

- [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES oder openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> Führen Sie den letzten Schritt nicht aus: (`sudo waagent -force -deprovision`), da die VHD hierdurch generalisiert wird.

#### <a name="identify-the-version-of-the-linux-agent"></a>Bestimmen der Version des Linux-Agents

Um festzustellen, welche Version des Linux-Agents im Quellimage des virtuellen Computers installiert ist, führen Sie die folgenden Befehle aus. Die Versionsnummer, die den Bereitstellungscode beschreibt, lautet `WALinuxAgent-`, nicht `Goal state agent`:

   ```bash  
   waagent -version
   ```
    
   Beispiel:
    
   ```bash  
   waagent -version
   WALinuxAgent-2.2.45 running on centos 7.7.1908
   Python: 2.7.5
   Goal state agent: 2.2.46
   ```

#### <a name="linux-agent-224-and-earlier-disable-the-linux-agent-provisioning"></a>Bis Linux-Agent 2.2.4: Deaktivieren der Bereitstellung des Linux-Agents 

Um die Bereitstellung einer Linux-Agents mit einer Version bis 2.2.4 zu deaktivieren, legen Sie die folgenden Parameter in **/etc/waagent.conf** fest: `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`.

#### <a name="linux-agent-2245-and-later-disable-the-linux-agent-provisioning"></a>Ab Linux-Agent 2.2.45: Deaktivieren der Bereitstellung des Linux-Agents

Zum Deaktivieren der Bereitstellung mit Linux Agent 2.2.45 und höher nehmen Sie die folgenden Änderungen an den Konfigurationsoptionen vor:

`Provisioning.Enabled` und `Provisioning.UseCloudInit` werden jetzt ignoriert.

In dieser Version gibt es derzeit keine `Provisioning.Agent`-Option zum vollständigen Deaktivieren der Bereitstellung. Sie können aber die Bereitstellungsmarkerdatei hinzufügen, und mit den folgenden Einstellungen wird die Bereitstellung ignoriert:

1. Fügen Sie in **/etc/waagent.conf** die Konfigurationsoption `Provisioning.Agent=Auto` hinzu.
2. Um sicherzustellen, dass die walinuxagent-Bereitstellung deaktiviert ist, führen Sie sie `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned` aus.
3. Deaktivieren Sie die „cloud-init“-Installation, indem Sie den folgenden Befehl ausführen:

   ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Melden Sie sich ab.

#### <a name="run-an-extension"></a>Ausführen einer Erweiterung

1. Legen Sie den folgenden Parameter in **/etc/waagent.conf** fest:

   - `Provisioning.Enabled=n`
   - `Provisioning.UseCloudInit=n`

2. Um sicherzustellen, dass die walinuxagent-Bereitstellung deaktiviert ist, führen Sie sie `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned` aus.

3. Wenn Sie „cloud-init“ in Ihrem Image verwenden, deaktivieren Sie es:

    ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Führen Sie eine Abmeldung durch.

---

## <a name="verify-your-vhd"></a>Überprüfen der VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Hochladen in ein Speicherkonto

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-disk-in-azure-stack-hub"></a>Erstellen des Datenträgers in Azure Stack Hub

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

## <a name="next-steps"></a>Nächste Schritte

[Verschieben einer VM in Azure Stack Hub: Übersicht](vm-move-overview.md)
