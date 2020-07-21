---
title: Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub
description: Es wird beschrieben, wie Sie Azure Stack Hub ein benutzerdefiniertes VM-Image hinzufügen oder es entfernen.
author: sethmanheim
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 07/10/2020
ms.openlocfilehash: ebcb85ba88d90d43a1364b465c4f66e6851d09cb
ms.sourcegitcommit: 2be3dd5419b0d003a9598a42541ebb1d251aea3d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2020
ms.locfileid: "86390771"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub

Als Bediener in Azure Stack Hub können Sie Ihr benutzerdefiniertes VM-Image zum Marketplace hinzufügen und Benutzern zur Verfügung stellen. Sie können dem Azure Stack Hub-Marketplace VM-Images über das Administratorportal oder über Windows PowerShell hinzufügen. Verwenden Sie ein Image aus dem globalen Azure Marketplace als Grundlage für Ihr benutzerdefiniertes Image, oder erstellen Sie ein eigenes Image mit Hyper-V.

Ein Benutzer im Mandantenportal im Azure Stack Hub kann ein benutzerdefiniertes VM-Image auch hinzufügen, indem er der Anleitung in Schritt 1 folgt. Ein Benutzer kann sein eigenes benutzerdefiniertes Image als virtuelle Festplatte (VHD) erstellen und in ein Speicherkonto in Azure Stack Hub hochladen. Anschließend kann er einen virtuellen Computer aus der VHD erstellen.

Es gibt zwei Arten von benutzerdefinierten Images: **generalisierte** und **spezialisierte**.

- **Generalisiertes Image**

  Bei einem generalisierten Datenträgerimage handelt es sich um ein Image, das mit **Sysprep** vorbereitet wurde, um eindeutige Informationen (z. B. Benutzerkonten) zu entfernen und so die Wiederverwendung für mehrere VMs zu ermöglichen. Dies ist eine gute Wahl für Marketplace-Elemente.

- **Spezialisiertes Image**

  Ein spezielles Datenträgerimage ist eine Kopie einer virtuellen Festplatte (VHD) einer bereits vorhandenen VM, die die Benutzerkonten, Anwendungen und andere Statusdaten Ihrer ursprünglichen VM enthält. Dabei handelt es sich normalerweise um das Format, in dem VMs zu Azure Stack Hub migriert werden.

## <a name="step-1-create-the-custom-vm-image"></a>Schritt 1: Erstellen des benutzerdefinierten VM-Images

### <a name="windows---create-a-custom-generalized-vhd"></a>Windows: Erstellen einer benutzerdefinierten generalisierten VHD

#### <a name="vhd-is-from-outside-azure"></a>Wenn die VHD nicht von Azure stammt

Folgen Sie den Schritten in [Hochladen einer generalisierten VHD und Verwendung dieser zum Erstellen neuer VMs in Azure](/azure/virtual-machines/windows/upload-generalized-managed), um **Sysprep** für Ihre VHD korrekt auszuführen und sie zu generalisieren.

#### <a name="vhd-is-from-azure"></a>Wenn die VHD von Azure stammt

Stellen Sie vor der Generalisierung der VM Folgendes sicher:

Vor dem Azure Stack 1910-Release:

- Wenn Sie die VM in Azure bereitstellen, verwenden Sie PowerShell, und stellen Sie sie ohne das `-ProvisionVMAgent`-Flag bereit.
- Entfernen Sie alle VM-Erweiterungen mithilfe des Cmdlets **Remove-AzureRmVMExtension** vom virtuellen Computer, bevor Sie den virtuellen Computer in Azure generalisieren. Sie können feststellen, welche VM-Erweiterungen installiert sind, indem Sie zu `Windows (C:) > WindowsAzure > Logs > Plugins` navigieren.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Mit oder nach dem Azure Stack 1910-Release:

- Die vorherigen Schritte gelten nicht für VHDs, die von Azure in Azure Stack Hub-Release ab Release 1910 übertragen werden.

Befolgen Sie die Anweisungen in [diesem Artikel](/azure/virtual-machines/windows/download-vhd), um die VHD ordnungsgemäß zu generalisieren und herunterzuladen, bevor Sie sie zu Azure Stack Hub portieren.

### <a name="windows---specialized"></a>Windows: Spezialisiert

Führen Sie die [hier](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) beschriebenen Schritte aus, um die VHD ordnungsgemäß vorzubereiten.
Stellen Sie zum Bereitstellen von VM-Erweiterungen sicher, dass die MSI-Datei des VM-Agents [in diesem Artikel](/azure/virtual-machines/extensions/agent-windows#manual-installation) vor der VM-Bereitstellung auf der VM installiert wird. Wenn der VM-Agent auf der virtuellen Festplatte nicht vorhanden ist, tritt bei der Erweiterungsbereitstellung ein Fehler auf. Die Festlegung des Betriebssystemprofils oder von `$vm.OSProfile.AllowExtensionOperations = $true` ist nicht bei der Bereitstellung erforderlich.

### <a name="linux---generalized"></a>Linux: Generalisiert

#### <a name="vhd-from-outside-azure"></a>Wenn die VHD nicht von Azure stammt

Wenn die VHD nicht von Azure stammt, befolgen Sie die entsprechenden Anweisungen zum Generalisieren der VHD:

- [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
- [SLES oder openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

#### <a name="vhd-from-azure"></a>Wenn die VHD von Azure stammt

Wenn die VHD von Azure stammt, befolgen Sie diese Anweisungen zum Generalisieren und Herunterladen der VHD:

1. Beenden des **waagent**-Diensts:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Die Linux-Agent-Versionen für Azure, die mit Azure Stack Hub funktionieren, [sind hier dokumentiert](azure-stack-linux.md#azure-linux-agent). Stellen Sie sicher, dass das sysprepped-Image über eine Linux-Agent-Version für Azure verfügt, die mit Azure Stack Hub kompatibel ist.

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

   1. Sie können diese VHD jetzt in Azure Stack Hub portieren.

> [!IMPORTANT]  
> Im Artikel [Beispielskript zum Hochladen einer virtuellen Festplatte nach Azure und Erstellen eines neuen virtuellen Computers](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) finden Sie ein Skript, mit dem Sie die VHD in ein Azure Stack Hub-Benutzerspeicherkonto hochladen und einen virtuellen Computer erstellen können. Stellen Sie sicher, dass Sie `$urlOfUploadedImageVhd` als Azure Stack Hub-Speicherkonto und Container-URL bereitstellen. Stellen Sie bei einer generalisierten VHD sicher, dass Sie `-CreateOption FromImage` auf den Wert `FromImage` festlegen.

### <a name="linux---specialized"></a>Linux: Spezialisiert

Spezialisierte VHDs sollten nicht als Basis-VHD für ein Marketplace-Element verwendet werden. Verwenden Sie hierfür generalisierte VHDs. Spezialisierte VHDs eignen sich jedoch gut für die Migration virtueller Computer von einem lokalen Standort zu Azure Stack Hub.

#### <a name="vhd-from-outside-azure"></a>Wenn die VHD nicht von Azure stammt

Schritt 1: Befolgen Sie die entsprechenden Anweisungen, um die VHD für Azure vorzubereiten. Verwenden Sie diesen Artikel bis zum Schritt „Installieren des Linux-Agents“, und fahren Sie dann mit Schritt 2 fort, bevor Sie den Agent installieren:

- [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
- [SLES oder openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> Führen Sie den letzten Schritt nicht aus: (`sudo waagent -force -deprovision`), da die VHD hierdurch generalisiert wird.

Schritt 2: Wenn ein spezialisierte Linux-VHD von außerhalb von Azure nach Azure Stack Hub übertragen wird, gehen Sie wie folgt vor, um VM-Erweiterungen auszuführen und die Bereitstellung zu deaktivieren:

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

Um die Bereitstellung einer Linux-Agents mit einer Version niedriger als 2.2.4 zu deaktivieren, legen Sie die folgenden Parameter in **/etc/waagent.conf** fest: `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`.

In Szenarien, in denen Sie Erweiterungen ausführen möchten:

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

Zum Deaktivieren der Bereitstellung mit Linux Agent 2.2.45 und höher nehmen Sie die folgenden Änderungen an den Konfigurationsoptionen vor:

- `Provisioning.Enabled` und `Provisioning.UseCloudInit` werden jetzt ignoriert.

In dieser Version gibt es derzeit keine `Provisioning.Agent`-Option zum vollständigen Deaktivieren der Bereitstellung. Sie können aber die Bereitstellungsmarkerdatei hinzufügen, und mit den folgenden Einstellungen wird die Bereitstellung ignoriert:

1. Fügen Sie in **/etc/waagent.conf** die Konfigurationsoption `Provisioning.Agent=Auto` hinzu.
2. Um sicherzustellen, dass die walinuxagent-Bereitstellung deaktiviert ist, führen Sie sie `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned` aus.
3. Deaktivieren Sie die „cloud-init“-Installation, indem Sie den folgenden Befehl ausführen:

   ```bash
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Melden Sie sich ab.

#### <a name="vhd-from-azure"></a>Wenn die VHD von Azure stammt

Sie können [diese Anleitung](/azure/virtual-machines/linux/upload-vhd#requirements) zur Vorbereitung der VHD verwenden.

> [!IMPORTANT]  
> Sie können das folgende PowerShell-Beispiel verwenden, um die VHD in ein Azure Stack Hub-Benutzerspeicherkonto hochzuladen:

```powershell  
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'Orlando'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$storageaccounturl = 'https://resourcegrpabc.blob.orlando.azurestack.corp.microsoft.com/container'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vhdName = 'myUploadedVhd.vhd'

New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
  -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ($storageaccounturl + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath
```

Stellen Sie für eine spezialisierte VHD sicher, dass Sie die attach-Semantik mit `-CreateOption Attach` [wie in diesem Beispiel](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks) verwenden, um aus dieser VHD eine VM zu erstellen.

### <a name="considerations"></a>Überlegungen

Berücksichtigen Sie vor dem Hochladen des Images unbedingt folgende Faktoren:

- Azure Stack Hub unterstützt nur virtuelle Computer der 1. Generation im VHD-Format für Datenträger fester Größe. Bei diesem festen Format wird der logische Datenträger in der Datei linear strukturiert, sodass das Datenträger-Offset **X** beim Blob-Offset **X** gespeichert wird. Die Eigenschaften der VHD werden in einer kleinen Fußzeile am Ende des Blobs beschrieben. Ob es sich bei Ihrem Datenträger um einen Datenträger mit fester Größe handelt, können Sie mithilfe des PowerShell-Cmdlets **Get-VHD** ermitteln.

- Azure Stack Hub unterstützt keine dynamischen VHDs.

## <a name="step-2-upload-a-storage-account"></a>Schritt 2: Hochladen eines Speicherkontos

Laden Sie das VM-Image als Azure Stack Hub-Bediener auf ein Speicherkonto hoch.

1. [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md)  

2. Melden Sie sich an Azure Stack Hub als Bediener an. Eine entsprechende Anleitung finden Sie unter [Anmelden bei Azure Stack Hub als Bediener](azure-stack-powershell-configure-admin.md).

3. Auf Images muss über einen Blobspeicher-URI verwiesen werden. Bereiten Sie ein Image mit Windows- oder Linux-Betriebssystem im VHD-Format (nicht VHDX) vor, und laden Sie es anschließend in ein Speicherkonto in Azure Stack Hub hoch.

   - Wenn sich die VHD in Azure befindet, können Sie bei der Ausführung auf einer verbundenen Azure Stack Hub-Instanz ein Tool wie [Azcopy](/azure/storage/common/storage-use-azcopy) verwenden, um die VHD direkt zwischen einem Azure-Konto und Ihrem Azure Stack Hub-Speicherkonto zu übertragen.

   - Wenn sich die VHD in Azure befindet, müssen Sie sie bei einer nicht verbundenen Azure Stack Hub-Instanz auf einen Computer herunterladen, der über eine Verbindung mit Azure und Azure Stack Hub verfügt. Anschließend kopieren Sie die VHD von Azure auf diesen Computer, bevor Sie sie mithilfe eines der gängigen [Speicherdaten-Übertragungstools](../user/azure-stack-storage-transfer.md), die in Azure und Azure Stack Hub verwendet werden können, auf Azure Stack Hub übertragen.

     In diesem Beispiel wird als solches Tool der Befehl **Add-AzureRmVHD** verwendet, um eine VHD auf ein Speicherkonto im Azure Stack Hub-Administratorportal hochzuladen:

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. Notieren Sie sich den URI des Blobspeichers, in den Sie das Image hochladen. Der Blobspeicher-URI hat folgendes Format: **&lt;Speicherkonto&gt;/&lt;Blobcontainer&gt;/&lt;Ziel-VHD-Name&gt;*.vhd**.

5. Damit auf das Blob anonym zugegriffen werden kann, navigieren Sie zum Speicherkonto-Blobcontainer, in den die VM-Image-VHD hochgeladen wurde. Wählen Sie **Blob** und anschließend **Zugriffsrichtlinie**. Sie können optional auch eine Shared Access Signature (SAS) für den Container generieren und in den Blob-URI aufnehmen. Mit diesem Schritt wird sichergestellt, dass das Blob für die Nutzung verfügbar ist. Kann auf das Blob nicht anonym zugegriffen werden, wird das VM-Image mit dem Status „Fehler“ erstellt.

   ![Navigieren zu Speicherkontoblobs](./media/azure-stack-add-vm-image/tca1.png)

   ![Festlegen des öffentlichen Blobzugriffs](./media/azure-stack-add-vm-image/tca2.png)

   ![Festlegen des öffentlichen Blobzugriffs](./media/azure-stack-add-vm-image/tca3.png)

6. Sie können die vorhergehenden Schritte auch verwenden, um das Image in ein Speicherkonto auf dem Benutzerportal hochzuladen (indem Sie sich als der Benutzer anmelden) und direkt daraus eine VM zu erstellen. In diesem Fall handelt es sich um eine benutzerdefinierte VHD, die nicht über den Marketplace verfügbar ist. Sie brauchen auch Schritt 3 nicht zu befolgen.

## <a name="step-3-option-1-add-using-the-portal"></a>Schritt 3, Option 1: Hinzufügen über das Portal

Fügen Sie das VM-Image als Azure Stack Hub-Bediener über das Portal hinzu.

1. Melden Sie sich an Azure Stack Hub als Bediener an. Wählen Sie im Menü **Alle Dienste** > **Compute** und unter **VM-Images** > **Hinzufügen** aus.

   ![Benutzeroberfläche für das Querladen des benutzerdefinierten Images](./media/azure-stack-add-vm-image/tca4.png)

2. Geben Sie unter **Image erstellen** den **Herausgeber**, das **Angebot**, die **SKU**, die **Version** und den URI des Betriebssystem-Datenträgerblobs ein. Klicken Sie anschließend auf **Erstellen**, um mit der Erstellung des VM-Images zu beginnen.

   ![Benutzeroberfläche für das Querladen des benutzerdefinierten Images](./media/azure-stack-add-vm-image/tca5.png)

   Nach Erstellung des Images ändert sich der VM-Imagestatus in **Erfolgreich**.

3. Wenn Sie ein Image hinzufügen, ist es nur für Azure Resource Manger-basierte Vorlagen und PowerShell-Bereitstellungen verfügbar. Wenn Sie ein Image für Ihre Benutzer als Marketplace-Element bereitstellen möchten, können Sie es mit den Schritten im Artikel [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md) veröffentlichen. Notieren Sie sich unbedingt die Werte für **Herausgeber**, **Angebot**, **SKU** und **Version**. Sie benötigen diese, wenn Sie die Resource Manager-Vorlage und „Manifest.json“ in Ihrer benutzerdefinierten AZPKG-Datei bearbeiten.

## <a name="step-3-option-2-add-using-powershell"></a>Schritt 3, Option 2: Hinzufügen unter Verwendung von PowerShell

 Fügen Sie ein VM-Image als Azure Stack Hub-Bediener unter Verwendung von PowerShell hinzu.

1. [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md)  

2. Melden Sie sich an Azure Stack Hub als Bediener an. Eine entsprechende Anleitung finden Sie unter [Anmelden bei Azure Stack Hub als Bediener](azure-stack-powershell-configure-admin.md).

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
     Die von Benutzern bei der Bereitstellung des VM-Images verwendete Version des VM-Images. Diese Version wird im Format **\#.\#.\#** angegeben. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **osType**  
     Beispiel: `Linux`  
     Als Betriebssystemtyp (**osType**) des Images muss **Windows** oder **Linux** angegeben werden.  
   - **OSUri**  
     Beispiel: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Sie können einen Blobspeicher-URI für ein `osDisk`-Element angeben.  

     Weitere Informationen finden Sie in der PowerShell-Referenz für das Cmdlet [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage).

4. Wenn Sie ein Image hinzufügen, ist es nur für Azure Resource Manger-basierte Vorlagen und PowerShell-Bereitstellungen verfügbar. Wenn Sie ein Image für Ihre Benutzer als Marketplace-Element bereitstellen möchten, können Sie es mit den Schritten im Artikel [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md) veröffentlichen. Notieren Sie sich unbedingt die Werte für **Herausgeber**, **Angebot**, **SKU** und **Version**. Sie benötigen diese, wenn Sie die Resource Manager-Vorlage und „Manifest.json“ in Ihrer benutzerdefinierten AZPKG-Datei bearbeiten.

## <a name="remove-using-the-portal"></a>Entfernen über das Portal

Um das VM-Image als Azure Stack Hub-Bediener über das Azure Stack Hub-Portal zu entfernen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das Azure Stack Hub-[Administratorportal](https://adminportal.local.azurestack.external).

2. Wenn dem VM-Image ein Marketplace-Element zugeordnet ist, wählen Sie **Marketplace-Verwaltung** und dann das zu löschende VM-Marketplace-Element aus.

3. Wenn dem VM-Image kein Marketplace-Element zugeordnet ist, navigieren Sie zu **Alle Dienste > Compute > VM-Images** , und wählen Sie dann die Auslassungspunkte ( **…** ) neben dem VM-Image aus.

4. Klicken Sie auf **Löschen**.

## <a name="remove-using-powershell"></a>Entfernen unter Verwendung von PowerShell

Um das VM-Image als Azure Stack Hub-Bediener unter Verwendung von PowerShell zu entfernen, führen Sie die folgenden Schritte aus:

1. [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md)

2. Melden Sie sich an Azure Stack Hub als Bediener an.

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
     Die von Benutzern bei der Bereitstellung des VM-Images verwendete Version des VM-Images. Diese Version wird im Format **\#.\#.\#** angegeben. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  

     Weitere Informationen zum Cmdlet **Remove-AzsPlatformImage** finden Sie in der [Dokumentation zum Azure Stack Hub-Bedienermodul](/powershell/azure/azure-stack/overview) für Microsoft PowerShell.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack Hub-Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
- [Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)
