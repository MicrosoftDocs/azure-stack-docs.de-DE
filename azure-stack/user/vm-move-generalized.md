---
title: Verschieben einer generalisierten VM aus der lokalen Umgebung in Azure Stack Hub
description: Erfahren Sie mehr zum Verschieben einer generalisierten VM aus der lokalen Umgebung in Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 105beaa0805fe0aea1aacfce8bd22f3bd01714b1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872873"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>Verschieben einer generalisierten VM aus der lokalen Umgebung in Azure Stack Hub

Sie können ein Image eines virtuellen Computers (VM) aus Ihrer lokalen Umgebung hinzufügen. Sie können Ihr Image als virtuelle Festplatte (VHD) erstellen und in ein Speicherkonto in Ihrer Azure Stack Hub-Instanz hochladen. Anschließend können Sie eine VM anhand der VHD erstellen.

Bei einem generalisierten Datenträgerimage handelt es sich um ein Image, das mit **Sysprep** vorbereitet wurde, um eindeutige Informationen (z. B. Benutzerkonten) zu entfernen und so die Wiederverwendung für mehrere VMs zu ermöglichen. Generalisierte VHDs eignen sich gut für die Erstellung von Images, die der Betreiber der Azure Stack Hub-Cloud als Marketplace-Elemente verwenden möchte.

## <a name="how-to-move-an-image"></a>Verschieben eines Images

Informieren Sie sich im Abschnitt, der sich speziell auf Ihre Anforderungen bei der Vorbereitung Ihrer VHD bezieht.

#### <a name="windows-vm"></a>[Windows-VM](#tab/port-win)

Führen Sie die Schritte in [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) aus, um Ihre VHD vor dem Hochladen ordnungsgemäß zu generalisieren. Sie müssen eine VHD für Azure Stack Hub verwenden.

#### <a name="linux-vm"></a>[Linux-VM](#tab/port-linux)

Befolgen Sie die entsprechenden Anweisungen zum Generalisieren der VHD für Ihr Linux-Betriebssystem:

- [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES oder openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>Überprüfen der VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>Hochladen in ein Speicherkonto

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>Erstellen des Images in Azure Stack Hub

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>Nächste Schritte

[Verschieben einer VM in Azure Stack Hub: Übersicht](vm-move-overview.md)
