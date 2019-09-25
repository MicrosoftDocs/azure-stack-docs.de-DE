---
title: Hinzufügen eines VM-Images zu Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure Stack ein VM-Image hinzufügen oder es entfernen.
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
ms.date: 09/17/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: fef815ec23655638bbe4df1bcdccae42aeee13e2
ms.sourcegitcommit: 9f4c6e96f60b4c229316e7a4ab6e0e5ef0a9a232
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71061166"
---
# <a name="add-a-vm-image-to-azure-stack"></a>Hinzufügen eines VM-Images zu Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In Azure Stack können Sie in Marketplace ein VM-Image (virtueller Computer) hinzufügen und Benutzern zur Verfügung zu stellen. Images werden hinzugefügt, indem Azure Resource Manager-Vorlagen für Azure Stack verwendet werden. Sie können VM-Images der Azure Marketplace-Benutzeroberfläche auch als Marketplace-Element hinzufügen, indem Sie das Administratorportal oder Windows PowerShell verwenden. Verwenden Sie entweder ein Image aus dem globalen Azure Marketplace oder Ihr eigenes benutzerdefiniertes VM-Image.

## <a name="add-a-vm-image-through-the-portal"></a>Hinzufügen eines VM-Images über das Portal

> [!NOTE]  
> Mit dieser Methode muss das Marketplace-Element separat erstellt werden.

Auf Images muss über einen Blobspeicher-URI verwiesen werden können. Bereiten Sie ein Image mit Windows- oder Linux-Betriebssystem im VHD-Format (nicht VHDX) vor, und laden Sie es anschließend in ein Speicherkonto in Azure oder Azure Stack hoch. Falls Ihr Image bereits in den Blobspeicher in Azure oder Azure Stack hochgeladen wurde, können Sie Schritt 1 überspringen.

1. [Laden Sie für Resource Manager-Bereitstellungen ein Windows-VM-Image in Azure hoch](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), oder gehen Sie für ein Linux-Image wie unter [Add Linux images to Azure Stack](azure-stack-linux.md) (Hinzufügen von Linux-Images zu Azure Stack) beschrieben vor. Berücksichtigen Sie vor dem Hochladen des Images unbedingt folgende Faktoren:

   - Azure Stack unterstützt nur virtuelle Computer der 1. Generation im VHD-Format für Datenträger fester Größe. Bei diesem Format wird der logische Datenträger in der Datei linear strukturiert, sodass das Datenträger-Offset X beim Blob-Offset X gespeichert wird. Die Eigenschaften der VHD werden in einer kleinen Fußzeile am Ende des Blobs beschrieben. Ob es sich bei Ihrem Datenträger um einen Datenträger mit fester Größe handelt, können Sie mithilfe des PowerShell-Befehls [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) ermitteln.  

     > [!IMPORTANT]  
     >  Azure Stack unterstützt keine dynamischen VHDs. Die Anpassung der Größe eines dynamischen Datenträgers, der an einen virtuellen Computer angefügt ist, führt zu einem Fehler. Löschen Sie in diesem Fall den virtuellen Computer, ohne jedoch seinen Datenträger (ein VHD-Blob in einem Speicherkonto) zu löschen. Konvertieren Sie anschließend die virtuelle Festplatte von einem dynamischen Datenträger in einen Datenträger mit fester Größe, und erstellen Sie die VM neu.

   - Es ist effizienter, ein Image in den Azure Stack-Blobspeicher als in Azure Blob Storage hochzuladen, da die Übertragung des Images per Push in das Azure Stack-Image-Repository schneller geht.

   - Ersetzen Sie beim Hochladen des [Windows-VM-Images](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) den Schritt **Anmelden bei Azure** durch den Schritt [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md).  

   - Notieren Sie sich den URI des Blobspeichers, in den Sie das Image hochladen. Der Blobspeicher-URI hat folgendes Format: *&lt;Speicherkonto&gt;/&lt;Blobcontainer&gt;/&lt;Ziel-VHD-Name&gt;* .vhd.

   - Damit auf das Blob anonym zugegriffen werden kann, navigieren Sie zum Speicherkonto-Blobcontainer, in den die VM-Image-VHD hochgeladen wurde. Wählen Sie **Blob** und anschließend **Zugriffsrichtlinie**. Sie können optional auch eine Shared Access Signature für den Container generieren und in den Blob-URI aufnehmen. Mit diesem Schritt wird sichergestellt, dass das Blob für die Nutzung verfügbar ist. Kann auf das Blob nicht anonym zugegriffen werden, wird das VM-Image mit dem Status „Fehler“ erstellt.

     ![Navigieren zu Speicherkontoblobs](./media/azure-stack-add-vm-image/image1.png)

     ![Festlegen des öffentlichen Blobzugriffs](./media/azure-stack-add-vm-image/image2.png)

2. Melden Sie sich bei Azure Stack als Bediener an. Wählen Sie im Menü unter **Compute** > **Hinzufügen** die Optionen **Alle Dienste** > **Images**.

3. Geben Sie unter **Image erstellen** den Namen, das Abonnement, die Ressourcengruppe, den Ort, den Betriebssystemdatenträger, den Betriebssystemtyp, den Speicherblob-URI, den Kontotyp und die Hostzwischenspeicherung an. Klicken Sie anschließend auf **Erstellen**, um mit der Erstellung des VM-Images zu beginnen.

   ![Starten der Imageerstellung](./media/azure-stack-add-vm-image/image4.png)

   Nach Erstellung des Images ändert sich der VM-Imagestatus in **Erfolgreich**.

4. Damit über die Benutzeroberfläche leichter auf das VM-Image zugegriffen werden kann, empfiehlt sich die [Erstellung eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Entfernen eines VM-Images über das Portal

1. Öffnen Sie das Administratorportal unter [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

2. Wählen Sie **Marketplace-Verwaltung** und dann die VM, die Sie löschen möchten.

3. Klicken Sie auf **Löschen**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Hinzufügen eines VM-Images zum Marketplace mit PowerShell

> [!Note]  
> Wenn Sie ein Image hinzufügen, ist es nur für Azure Resource Manger-basierte Vorlagen und PowerShell-Bereitstellungen verfügbar. Stellen Sie ein Image für Ihre Benutzer als Marketplace-Element zur Verfügung, indem Sie es mit den Schritten in diesem Artikel veröffentlichen: [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)

1. [Installieren Sie PowerShell für Azure Stack](azure-stack-powershell-install.md).  

2. Melden Sie sich an Azure Stack als Bediener an. Eine entsprechende Anleitung finden Sie unter [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md).

3. Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   Mit dem **Add-AzsPlatformimage**-Cmdlet werden Werte angegeben, die von den Azure Resource Manager-Vorlagen zum Verweisen auf das VM-Image verwendet werden. Hierzu gehören folgende Werte:
   - **publisher**  
     Beispiel: `Canonical`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem Herausgebernamen. Beispiel: **Microsoft**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **offer**  
     Beispiel: `UbuntuServer`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem Angebotsnamen. Beispiel: **WindowsServer**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **sku**  
     Beispiel: `14.04.3-LTS`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem SKU-Namen. Beispiel: **Datacenter2016**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **version**  
     Beispiel: `1.0.0`  
     Die von Benutzern bei der Bereitstellung des VM-Images verwendete Version des VM-Images. Diese Version wird im Format *\#.\#.\#* angegeben. Beispiel: **1.0.0**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **osType**  
     Beispiel: `Linux`  
     Als Betriebssystemtyp des Images muss **Windows** oder **Linux** angegeben werden.  
   - **OSUri**  
     Beispiel: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Sie können einen Blobspeicher-URI für ein `osDisk`-Element angeben.  

     Weitere Informationen finden Sie in der PowerShell-Referenz für das Cmdlet [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) und das Cmdlet [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Hinzufügen eines benutzerdefinierten VM-Images zum Marketplace mit PowerShell
 
1. [Installieren Sie PowerShell für Azure Stack](azure-stack-powershell-install.md).

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. Verwenden Sie bei Nutzung der **Active Directory-Verbunddienste (AD FS)** das folgende Cmdlet:

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external
    ```

3. Melden Sie sich an Azure Stack als Bediener an. Eine entsprechende Anleitung finden Sie unter [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers](azure-stack-powershell-configure-admin.md).

4. Erstellen Sie ein Speicherkonto in der globalen Azure- oder Azure Stack-Instanz, um Ihr benutzerdefiniertes VM-Image zu speichern. Anweisungen dazu finden Sie unter [Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs über das Azure-Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Bereiten Sie ein Windows- oder Linux-Betriebssystemimage im VHD-Format (nicht VHDX) vor, laden Sie das Image in Ihr Speicherkonto hoch, und rufen Sie den URI ab, über den das VM-Image per PowerShell abgerufen werden kann.  

   ```powershell
   Add-AzureRmAccount 
   -EnvironmentName "AzureStackAdmin" 
   -TenantId $TenantID
   ```
  
   >[!Note]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie lediglich Konten wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit Add-AzureRmAccount anmelden: `Remove-AzureRmAccount-Scope Process`

6. (Optional) Sie können als Teil des VM-Images ein Array mit Datenträgern für Daten hochladen. Erstellen Sie Ihre Datenträger für Daten mit dem New-DataDiskObject-Cmdlet. Öffnen Sie PowerShell über eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Weitere Informationen zum Cmdlet „Add-AzsPlatformimage“ und Cmdlet „New-DataDiskObject“ finden Sie in der [Dokumentation zum Azure Stack-Bedienermodul](https://docs.microsoft.com/powershell/module/) für Microsoft PowerShell.

## <a name="remove-a-vm-image-by-using-powershell"></a>Entfernen eines VM-Images mithilfe von PowerShell

Wenn Sie das hochgeladene VM-Image nicht mehr benötigen, können Sie es im Marketplace mit dem folgenden Cmdlet löschen:

1. [Installieren Sie PowerShell für Azure Stack](azure-stack-powershell-install.md).

2. Melden Sie sich an Azure Stack als Bediener an.

3. Öffnen Sie PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   Mit dem **Remove-AzsPlatformImage**-Cmdlet werden Werte angegeben, die von den Azure Resource Manager-Vorlagen zum Verweisen auf das VM-Image verwendet werden. Hierzu gehören folgende Werte:
   - **publisher**  
     Beispiel: `Canonical`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem Herausgebernamen. Beispiel: **Microsoft**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **offer**  
     Beispiel: `UbuntuServer`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem Angebotsnamen. Beispiel: **WindowsServer**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **sku**  
     Beispiel: `14.04.3-LTS`  
     Das von Benutzern bei der Bereitstellung des VM-Images verwendete Segment mit dem SKU-Namen. Beispiel: **Datacenter2016**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
   - **version**  
     Beispiel: `1.0.0`  
     Die von Benutzern bei der Bereitstellung des VM-Images verwendete Version des VM-Images. Diese Version wird im Format *\#.\#.\#* angegeben. Beispiel: **1.0.0**. Dieses Feld darf kein Leerzeichen und kein anderes Sonderzeichen enthalten.  
    
     Weitere Informationen zum Cmdlet „Remove-AzsPlatformImage“ finden Sie in der [Dokumentation zum Azure Stack-Bedienermodul](https://docs.microsoft.com/powershell/module/) für Microsoft PowerShell.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)
