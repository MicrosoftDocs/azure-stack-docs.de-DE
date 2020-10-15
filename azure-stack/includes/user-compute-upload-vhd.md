---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 28fe06ca182b2df8104c2d8b23ce9f5c27448064
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936027"
---
Sie können Ihre VHD im Portal hochladen oder mithilfe des Containers, den Sie im Portal erstellt haben. Verwenden Sie dazu AzCopy.

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>Portal zum Generieren der SAS-URL und Hochladen der VHD

1. Melden Sie sich beim Azure Stack Hub-Benutzerportal an.

2. Wählen Sie unter **Speicherkonten** ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues.

3. Wählen Sie auf dem Blatt „Speicherkonto“ für Ihr Speicherkonto **Blobs** aus. Wählen Sie Container, um einen neuen Container zu erstellen.

4. Geben Sie den Namen Ihres Containers ein, und wählen Sie dann **Blob (anonymer Lesezugriff nur für Blobs)** aus.

5. Wenn Sie AzCopy zum Hochladen Ihres Images statt des Portals verwenden möchten, erstellen Sie einen SAS-Token. Wählen Sie im Speicherkonto **Shared Access Signature** und dann **SAS und Verbindungszeichenfolge generieren** aus. Kopieren und notieren Sie die **SAS-URL des Blob-Diensts**. Sie nutzen diese URL bei Verwendung von AzCopy zum Hochladen der VHD.

6. Wählen Sie Ihren Container und dann **Hochladen** aus. Laden Sie Ihre VHD hoch.

### <a name="azcopy-vhd"></a>AzCopy für VHD

Verwenden Sie Azure Storage-Explorer oder AzCopy, um die Wahrscheinlichkeit zu verringern, dass Ihre VHD beim Hochladen beschädigt wird, und das Hochladen zu beschleunigen. In den folgenden Schritten wird AzCopy auf einem Computer mit Windows 10 ausgeführt. AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren.

1. Wenn Sie AzCopy nicht installiert haben, installieren Sie das Tool. Anweisungen zum Herunterladen und zum Einstieg finden Sie im Artikel [Erste Schritte mit AzCopy](/azure/storage/common/storage-use-azcopy-v10). Notieren Sie sich, wo Sie die Binärdatei speichern. Sie können [AzCopy Ihrem Pfad hinzufügen](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/), um es an der PowerShell-Befehlszeile zu verwenden.

2. Öffnen Sie PowerShell, um AzCopy auf der Shell zu verwenden.

3. Laden Sie mit AzCopy Ihre VHD in Ihren Container im Speicherkonto hoch.

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> Laden Sie Ihre VHD mit einer Syntax hoch, die dem Hochladen einer einzelnen Datei in ein virtuelles Verzeichnis ähnelt. Fügen Sie `--blob-type=PageBlob` hinzu, um sicherzustellen, dass die VHD als **Seitenblob** hochgeladen wird, anstatt standardmäßig als **Block**.

Weitere Informationen zum Einsatz von AzCopy und anderen Speichertools finden Sie unter [Verwenden der Datenübertragungstools im Azure Stack Hub-Speicher](../user/azure-stack-storage-transfer.md).