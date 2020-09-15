---
title: Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub
description: Es wird beschrieben, wie Sie Azure Stack Hub ein benutzerdefiniertes VM-Image hinzufügen oder es entfernen.
author: sethmanheim
ms.topic: how-to
ms.date: 9/8/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 26e93e019c0be7b5ef9d5f29b509407011083acd
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89598495"
---
# <a name="add-and-remove-a-custom-vm-image-to-azure-stack-hub"></a>Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub und dessen Entfernen

Als Bediener in Azure Stack Hub können Sie Ihr benutzerdefiniertes VM-Image zum Marketplace hinzufügen und Benutzern zur Verfügung stellen. Sie können dem Azure Stack Hub-Marketplace VM-Images über das Administratorportal oder über Windows PowerShell hinzufügen. Verwenden Sie ein Image aus dem globalen Microsoft Azure Marketplace als Grundlage für Ihr benutzerdefiniertes Image, oder erstellen Sie mit Hyper-V ein eigenes Image.

## <a name="add-an-image"></a>Hinzufügen eines Image

Anweisungen zum Hinzufügen generalisierter und spezialisierter Images finden Sie im Abschnitt **Compute** des Benutzerhandbuchs. Sie sollten ein generalisiertes Image erstellen, ehe Sie Ihren Benutzern ein Image anbieten. Weitere Informationen finden Sie unter [Verschieben einer VM in Azure Stack Hub: Übersicht](/azure-stack/user/vm-move-overview). Wenn Sie für Ihre Mandanten verfügbare Images erstellen, verwenden Sie nicht die Endpunkte des Benutzerportals oder Mandantenverzeichnisses, sondern das Azure Stack Hub-Verwaltungsportal oder die Administratorendpunkte.

Sie haben zwei Möglichkeiten, einem Benutzer ein Image zur Verfügung zu stellen:

- **Anbieten eines Images, das nur über Azure Resource Manager zugänglich ist**  
  Wenn Sie das Image im Azure Stack Hub-Verwaltungsportal zu **Compute** > **Images** hinzufügen, können alle Ihre Mandanten darauf zugreifen. Ihre Benutzer müssen jedoch eine Azure Resource Manager-Vorlage nutzen, um Zugriff darauf zu haben. Es wird nicht in Ihrem Azure Stack Hub Marketplace angezeigt.

- **Anbieten eines Images über Azure Stack Hub Marketplace**  
    Sobald Sie Ihr Image über das Azure Stack Hub-Verwaltungsportal hinzugefügt haben, können Sie ein Marketplace-Angebot erstellen. Anweisungen finden Sie unter [Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack Hub Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).


## <a name="add-a-platform-image"></a>Hinzufügen eines Plattformimages

Um Azure Stack Hub ein Plattformimage hinzuzufügen, müssen Sie das Azure Stack Hub-Verwaltungsportal oder den Azure Stack Hub-Endpunkt mit PowerShell verwenden. Sie müssen eine generalisierte VHD erstellt haben. Anweisungen finden Sie unter [Verschieben einer VM in Azure Stack Hub: Übersicht](/azure-stack/user/vm-move-overview).

### <a name="portal"></a>[Portal](#tab/image-add-portal)

Fügen Sie das VM-Image als Azure Stack Hub-Bediener über das Portal hinzu.

1. Melden Sie sich an Azure Stack Hub als Bediener an. Wählen Sie im Menü **Alle Dienste** > **Compute** > **Images** > **Hinzufügen** aus.

   ![Hinzufügen eines VM-Images](./media/azure-stack-add-vm-image/tca4.png)

2. Geben Sie unter **Image erstellen** den **Herausgeber**, das **Angebot**, die **SKU**, die **Version** und den URI des Betriebssystem-Datenträgerblobs ein. Klicken Sie anschließend auf **Erstellen**, um mit der Erstellung des VM-Images zu beginnen.

   ![Benutzeroberfläche für das Querladen des benutzerdefinierten Images](./media/azure-stack-add-vm-image/tca5.png)

   Nach Erstellung des Images ändert sich der VM-Imagestatus in **Erfolgreich**.

3. Wenn Sie ein Image hinzufügen, ist es nur für Azure Resource Manger-basierte Vorlagen und PowerShell-Bereitstellungen verfügbar. Wenn Sie ein Image für Ihre Benutzer als Marketplace-Element bereitstellen möchten, können Sie es mit den Schritten im Artikel [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md) veröffentlichen. Notieren Sie sich unbedingt die Werte für **Herausgeber**, **Angebot**, **SKU** und **Version**. Sie benötigen diese, wenn Sie die Resource Manager-Vorlage und „Manifest.json“ in Ihrer benutzerdefinierten AZPKG-Datei bearbeiten.

### <a name="powershell"></a>[PowerShell](#tab/image-add-ps)

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

---

## <a name="remove-a-platform-image"></a>Entfernen eines Plattformimages

Sie können ein Plattformimage mithilfe des Portals oder von PowerShell entfernen.

### <a name="portal"></a>[Portal](#tab/image-rem-portal)

Um das VM-Image als Azure Stack Hub-Bediener über das Azure Stack Hub-Portal zu entfernen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das Azure Stack Hub-[Administratorportal](https://portal.azure.com/signin/index).

2. Wenn dem VM-Image ein Marketplace-Element zugeordnet ist, wählen Sie **Marketplace-Verwaltung** und dann das zu löschende VM-Marketplace-Element aus.

3. Wenn dem VM-Image kein Marketplace-Element zugeordnet ist, navigieren Sie zu **Alle Dienste > Compute > VM-Images** , und wählen Sie dann die Auslassungspunkte ( **…** ) neben dem VM-Image aus.

4. Klicken Sie auf **Löschen**.

### <a name="powershell"></a>[PowerShell](#tab/image-rem-ps)

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
---
## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack Hub-Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
- [Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)
