---
title: Verschieben einer VM aus Azure in Azure Stack Hub
description: Erfahren Sie, wie Sie eine VM aus Azure in Azure Stack Hub verschieben.
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: c8c68a64f7a05e03c70e138cb4d8c95da7417ec9
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519416"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>Verschieben einer VM aus Azure in Azure Stack Hub

Sie können eine virtuelle Festplatte (Virtual Hard Drive, VHD) von einem in Azure erstellten virtuellen Computer (Virtual Machine, VM) in Ihre Azure Stack Hub-Instanz hochladen.

## <a name="prepare-and-download-your-vhd-from-azure"></a>Vorbereiten und Herunterladen der VHD aus Azure

Informieren Sie sich im Abschnitt, der sich speziell auf Ihre Anforderungen bei der Vorbereitung Ihrer VHD bezieht.

#### <a name="windows---specialized"></a>[Windows: Spezialisiert](#tab/win-spec)

- Führen Sie die Schritte im Artikel [Erstellen einer Windows-VM von einem speziellen Datenträger mithilfe von PowerShell](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) aus.
- Stellen Sie sicher, dass der VM-Agent (MSI) verfügbar ist, um VM-Erweiterungen bereitzustellen.  
  Weitere Informationen und Anweisungen finden Sie unter [Übersicht über den Agent für virtuelle Azure-Computer](/azure/virtual-machines/extensions/agent-windows). Stellen Sie sicher, dass die Erweiterung auf der VM installiert ist, bevor Sie sie verschieben. Wenn der VM-Agent auf der virtuellen Festplatte nicht vorhanden ist, tritt bei der Erweiterungsbereitstellung ein Fehler auf. Die Festlegung des Betriebssystemprofils oder von `$vm.OSProfile.AllowExtensionOperations = $true` ist nicht bei der Bereitstellung erforderlich.

#### <a name="windows---generalized"></a>[Windows: Generalisiert](#tab/win-gen)

::: moniker range="<=azs-1910"
- Befolgen Sie die Anweisungen unter [Herunterladen einer Windows-VHD von Azure](/azure/virtual-machines/windows/download-vhd), um die VHD ordnungsgemäß zu generalisieren und herunterzuladen, bevor Sie sie in Azure Stack Hub verschieben.
- Stellen Sie die VM über PowerShell in Azure bereit. Bereiten Sie sie ohne das Flag `-ProvisionVMAgent` vor.
- Entfernen Sie alle VM-Erweiterungen mithilfe des Cmdlets **Remove-AzureRmVMExtension** vom virtuellen Computer, bevor Sie den virtuellen Computer in Azure generalisieren. Sie können feststellen, welche VM-Erweiterungen installiert sind, indem Sie zu `Windows (C:) > WindowsAzure > Logs > Plugins` navigieren.

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

Befolgen Sie die Anweisungen unter [Herunterladen einer Windows-VHD von Azure](/azure/virtual-machines/windows/download-vhd), um die VHD ordnungsgemäß zu generalisieren und herunterzuladen, bevor Sie sie in Azure Stack Hub verschieben.
::: moniker-end

#### <a name="linux---specialized"></a>[Linux: Spezialisiert](#tab/lin-spec)

- Bevor Sie Ihre Linux-VM herunterladen, befolgen Sie die Anweisungen im Abschnitt „Vorbereiten der VM“ des Artikels [Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm).
- Führen Sie die Schritte im Artikel [Herunterladen einer Linux-VHD von Azure](/azure//virtual-machines/windows/download-vhd) aus, um die VHD vorzubereiten und herunterzuladen.
- Stellen Sie für eine spezialisierte VHD sicher, dass Sie die „attach“-Semantik mithilfe von `-CreateOption Attach`verwenden. Ein Beispiel finden Sie im Artikel [Erstellen eines virtuellen Computers mit einem vorhandenen verwalteten Betriebssystemdatenträger mit PowerShell (Windows)](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks).

#### <a name="linux---generalized"></a>[Linux: Generalisiert](#tab/lin-gen)

1. Beenden des **waagent**-Diensts:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Die Linux-Agent-Versionen für Azure, die mit Azure Stack Hub funktionieren, [sind hier dokumentiert](../operator/azure-stack-linux.md#azure-linux-agent). Stellen Sie sicher, dass das Image, für das Sie sysprep ausgeführt haben, über eine Version des Azure Linux-Agents verfügt, die mit Azure Stack Hub kompatibel ist.

2. Beenden Sie den virtuellen Computer, oder heben Sie seine Zuordnung auf.

3. Laden Sie die VHD herunter.

   1. Um die VHD-Datei herunterzuladen, generieren Sie eine SAS-URL (Shared Access Signature). Wenn die URL generiert wird, wird der URL eine Ablaufzeit zugewiesen.

   1. Wählen Sie im Menü auf dem Blatt des virtuellen Computers die Option **Datenträger** aus.

   1. Wählen Sie den Betriebssystem-Datenträger für den virtuellen Computer und anschließend **Datenträgerexport** aus.

   1. Legen Sie die Ablaufzeit der URL auf 36000 fest.

   1. Wählen Sie **URL generieren** aus.

   1. Generieren Sie die URL.

   1. Wählen Sie unter der generierten URL die Option **VHD-Datei herunterladen** aus.

   1. Gegebenenfalls muss im Browser die Option **Speichern** ausgewählt werden, um den Download zu starten. Der Standardname für die VHD-Datei lautet **abcd**.

   1. Sie können diese VHD jetzt in Azure Stack Hub verschieben.

> [!IMPORTANT]  
> Im Artikel [Beispielskript zum Hochladen einer virtuellen Festplatte nach Azure und Erstellen eines neuen virtuellen Computers](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) finden Sie ein Skript, mit dem Sie die VHD in ein Azure Stack Hub-Benutzerspeicherkonto hochladen und einen virtuellen Computer erstellen können. Stellen Sie sicher, dass Sie `$urlOfUploadedImageVhd` als Azure Stack Hub-Speicherkonto und Container-URL bereitstellen. Stellen Sie bei einer generalisierten VHD sicher, dass Sie `-CreateOption FromImage` auf den Wert `FromImage` festlegen.

---

## <a name="verify-your-vhd"></a>Überprüfen der VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Hochladen in ein Speicherkonto

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Es gibt zwei Arten benutzerdefinierter Images: **Spezialisiert** und **Generalisiert**.

### <a name="specialized"></a>[Spezialisiert](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[Generalisiert](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>Nächste Schritte

[Verschieben einer VM in Azure Stack Hub: Übersicht](vm-move-overview.md)
