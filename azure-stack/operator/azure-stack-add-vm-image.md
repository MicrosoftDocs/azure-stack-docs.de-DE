---
title: Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure Stack ein benutzerdefiniertes VM-Image hinzufügen oder es entfernen.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 9dc5039a2c8b74b14da59573758a4cf8d1a3657a
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282656"
---
# <a name="add-a-custom-vm-to-azure-stack"></a>Hinzufügen eines benutzerdefinierten virtuellen Computers zu Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In Azure Stack können Sie in Marketplace ein benutzerdefiniertes VM-Image (virtueller Computer) hinzufügen und Benutzern zur Verfügung zu stellen. Images werden hinzugefügt, indem Azure Resource Manager-Vorlagen für Azure Stack verwendet werden. Sie können VM-Images der Azure Marketplace-Benutzeroberfläche auch als Marketplace-Element hinzufügen, indem Sie das Administratorportal oder Windows PowerShell verwenden. Verwenden Sie entweder ein Image aus dem globalen Azure Marketplace oder Ihr eigenes benutzerdefiniertes VM-Image.

## <a name="generalize-the-vm-image"></a>Generalisieren des VM-Images

### <a name="windows"></a>Windows

Erstellen Sie eine benutzerdefinierte generalisierte VHD. Wenn die VHD nicht von Azure stammt, führen Sie die Schritte in [Hochladen einer generalisierten VHD und Verwendung dieser zum Erstellen neuer VMs in Azure](/azure/virtual-machines/windows/upload-generalized-managed) durch, zu erstellen, um **Sysprep** für Ihre VHD korrekt auszuführen und sie zu generalisieren.

Wenn die VHD von Azure stammt, befolgen Sie die Anweisungen in [Generalisieren des virtuellen Quellcomputers mithilfe von Sysprep](/azure/virtual-machines/windows/upload-generalized-managed#generalize-the-source-vm-by-using-sysprep), bevor Sie sie zu Azure Stack portieren.

### <a name="linux"></a>Linux

Wenn die VHD nicht von Azure stammt, befolgen Sie die entsprechenden Anweisungen zum Generalisieren der VHD:

- [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES oder openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Wenn die VHD von Azure stammt, befolgen Sie diese Anweisungen zum Generalisieren der VHD:

1. Beenden des **waagent**-Diensts:

   ```bash
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
   ```

2. Fahren Sie den virtuellen Computer herunter, und laden Sie die VHD herunter. Wenn Sie Ihre VHD aus Azure einbinden, können Sie den Datenträgerexport verwenden, wie in [Herunterladen einer Windows-VHD von Azure](/azure/virtual-machines/windows/download-vhd) gezeigt.

### <a name="common-steps-for-both-windows-and-linux"></a>Allgemeine Schritte für Windows als auch Linux

Berücksichtigen Sie vor dem Hochladen des Images unbedingt folgende Faktoren:

- Azure Stack unterstützt nur das Generieren eines (1) virtuellen Computers im VHD-Format für Datenträger mit fester Größe. Bei diesem festen Format wird der logische Datenträger in der Datei linear strukturiert, sodass das Datenträger-Offset *X* beim Blob-Offset *X* gespeichert wird. Die Eigenschaften der VHD werden in einer kleinen Fußzeile am Ende des Blobs beschrieben. Ob es sich bei Ihrem Datenträger um einen Datenträger mit fester Größe handelt, können Sie mithilfe des PowerShell-Cmdlets **Get-VHD** ermitteln.

- Azure Stack unterstützt keine dynamischen VHDs. Die Anpassung der Größe eines dynamischen Datenträgers, der an einen virtuellen Computer angefügt ist, führt zu einem Fehler. Löschen Sie in diesem Fall den virtuellen Computer, ohne jedoch seinen Datenträger (ein VHD-Blob in einem Speicherkonto) zu löschen. Konvertieren Sie anschließend die virtuelle Festplatte von einem dynamischen Datenträger in einen Datenträger mit fester Größe, und erstellen Sie die VM neu.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Hinzufügen eines VM-Images als Azure Stack-Betreiber über das Portal

1. Auf Images muss über einen Blobspeicher-URI verwiesen werden können. Bereiten Sie ein Image mit Windows- oder Linux-Betriebssystem im VHD-Format (nicht VHDX) vor, und laden Sie es anschließend in ein Speicherkonto in Azure oder Azure Stack hoch.

   - Wenn sich die VHD in Azure befindet, können Sie bei der Ausführung auf einer verbundenen Azure Stack-Instanz ein Tool wie [Azcopy](/azure/storage/common/storage-use-azcopy) verwenden, um die VHD direkt zwischen einem Azure-Konto und Ihrem Azure Stack-Speicherkonto zu übertragen.

   - Wenn sich die VHD in Azure befindet, müssen Sie sie bei einer nicht verbundenen Azure Stack-Instanz auf einen Computer herunterladen, der über eine Verbindung mit Azure und Azure Stack verfügt. Anschließend kopieren Sie die VHD von Azure auf diesen Computer, bevor Sie sie mithilfe eines der gängigen [Speicherdaten-Übertragungstools](../user/azure-stack-storage-transfer.md), die in Azure und Azure Stack verwendet werden können, auf Azure Stack übertragen.

2. Sie können [diesem Beispiel ](/powershell/module/azurerm.compute/add-azurermvhd?view=azurermps-6.13.0) folgen, um eine VHD in ein Speicherkonto im Azure Stack-Administratorportal hochzuladen.

   - Es ist effizienter, ein Image in den Azure Stack-Blobspeicher als in Azure Blob Storage hochzuladen, da die Übertragung des Images per Push in das Azure Stack-Image-Repository schneller geht.

   - Ersetzen Sie beim Hochladen des [Windows-VM-Images](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) den Schritt **Anmelden bei Azure** durch den Schritt [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md).  

3. Notieren Sie sich den URI des Blobspeichers, in den Sie das Image hochladen. Der Blobspeicher-URI hat folgendes Format: *&lt;Speicherkonto&gt;/&lt;Blobcontainer&gt;/&lt;Ziel-VHD-Name&gt;* .vhd.

4. Damit auf das Blob anonym zugegriffen werden kann, navigieren Sie zum Speicherkonto-Blobcontainer, in den die VM-Image-VHD hochgeladen wurde. Wählen Sie **Blob** und anschließend **Zugriffsrichtlinie**. Sie können optional auch eine Shared Access Signature für den Container generieren und in den Blob-URI aufnehmen. Mit diesem Schritt wird sichergestellt, dass das Blob für die Nutzung verfügbar ist. Kann auf das Blob nicht anonym zugegriffen werden, wird das VM-Image mit dem Status „Fehler“ erstellt.

   ![Navigieren zu Speicherkontoblobs](./media/azure-stack-add-vm-image/image1.png)

   ![Festlegen des öffentlichen Blobzugriffs](./media/azure-stack-add-vm-image/image2.png)

5. Melden Sie sich bei Azure Stack als Bediener an. Wählen Sie im Menü unter **Compute** > **Hinzufügen** die Optionen **Alle Dienste** > **Images**.

6. Geben Sie unter **Image erstellen** den Namen, das Abonnement, die Ressourcengruppe, den Ort, den Betriebssystemdatenträger, den Betriebssystemtyp, den Speicherblob-URI, den Kontotyp und die Hostzwischenspeicherung an. Klicken Sie anschließend auf **Erstellen**, um mit der Erstellung des VM-Images zu beginnen.

   ![Starten der Imageerstellung](./media/azure-stack-add-vm-image/image4.png)

   Nach Erstellung des Images ändert sich der VM-Imagestatus in **Erfolgreich**.

7. Wenn Sie ein Image hinzufügen, ist es nur für Azure Resource Manger-basierte Vorlagen und PowerShell-Bereitstellungen verfügbar. Wenn Sie ein Image für Ihre Benutzer als Marketplace-Element bereitstellen möchten, können Sie es mit den Schritten im Artikel [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md) veröffentlichen. Notieren Sie sich unbedingt die Werte für **Herausgeber**, **Angebot**, **SKU** und **Version**. Sie benötigen diese, wenn Sie die Resource Manager-Vorlage und „Manifest.json“ in Ihrer benutzerdefinierten AZPKG-Datei bearbeiten.

## <a name="remove-the-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Entfernen des VM-Images als Azure Stack-Betreiber über das Portal

1. Öffnen Sie das Azure Stack-[Administratorportal](https://adminportal.local.azurestack.external).

2. Wenn dem VM-Image ein Marketplace-Element zugeordnet ist, wählen Sie **Marketplace-Verwaltung** und dann das zu löschende VM-Marketplace-Element aus.

3. Wenn dem VM-Image kein Marketplace-Element zugeordnet ist, navigieren Sie zu **Alle Dienste > Compute > VM-Images** , und wählen Sie dann die Auslassungspunkte ( **…** ) neben dem VM-Image aus.

4. Klicken Sie auf **Löschen**.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Hinzufügen eines VM-Images als Azure Stack-Betreiber über PowerShell

1. [Installieren Sie PowerShell für Azure Stack](azure-stack-powershell-install.md).  

2. Melden Sie sich an Azure Stack als Bediener an. Eine entsprechende Anleitung finden Sie unter [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md).

3. Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   Mit dem **Add-AzsPlatformimage**-Cmdlet werden Werte angegeben, die von den Azure Resource Manager-Vorlagen zum Verweisen auf das VM-Image verwendet werden. Hierzu gehören folgende Werte:
   - **publisher**  
     Beispiel: `Canonical`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem **Herausgebernamen**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **offer**  
     Beispiel: `UbuntuServer`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem **Angebotsnamen**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **sku**  
     Beispiel: `14.04.3-LTS`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem **SKU-Namen**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **version**  
     Beispiel: `1.0.0`  
     Die von Benutzern bei der Bereitstellung des VM-Images verwendete Version des VM-Images. Diese Version wird im Format *\#.\#.\#* angegeben. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **osType**  
     Beispiel: `Linux`  
     Als Betriebssystemtyp (**osType**) des Images muss **Windows** oder **Linux** angegeben werden.  
   - **OSUri**  
     Beispiel: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Sie können einen Blobspeicher-URI für ein `osDisk`-Element angeben.  

     Weitere Informationen finden Sie in der PowerShell-Referenz für die Cmdlets [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) und [New-DataDiskObject](/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="remove-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Entfernen eines VM-Images als Azure Stack-Betreiber über PowerShell

Wenn Sie das hochgeladene VM-Image nicht mehr benötigen, können Sie es im Marketplace mit dem folgenden Cmdlet löschen:

1. [Installieren Sie PowerShell für Azure Stack](azure-stack-powershell-install.md).

2. Melden Sie sich an Azure Stack als Bediener an.

3. Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   Mit dem **Remove-AzsPlatformImage**-Cmdlet werden Werte angegeben, die von den Azure Resource Manager-Vorlagen zum Verweisen auf das VM-Image verwendet werden. Hierzu gehören folgende Werte:
   - **publisher**  
     Beispiel: `Canonical`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem **Herausgebernamen**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **offer**  
     Beispiel: `UbuntuServer`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem **Angebotsnamen**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **sku**  
     Beispiel: `14.04.3-LTS`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem **SKU-Namen**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **version**  
     Beispiel: `1.0.0`  
     Die von Benutzern bei der Bereitstellung des VM-Images verwendete Version des VM-Images. Diese Version wird im Format *\#.\#.\#* angegeben. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  

     Weitere Informationen zum Cmdlet **Remove-AzsPlatformImage** finden Sie in der [Dokumentation zum Azure Stack-Bedienermodul](/powershell/module/) für Microsoft PowerShell.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack-Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
- [Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)
