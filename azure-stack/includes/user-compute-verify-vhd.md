---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 4c0e9fd823b7b932ec11c61e24ce650df1cf9cc9
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936118"
---
Vor dem Hochladen der VHD müssen Sie prüfen, ob die VHD die Anforderungen erfüllt. VHDs, die die Anforderungen nicht erfüllen, können nicht in Azure Stack Hub geladen werden.

1. Sie verwenden die PowerShell-Module in Hyper-V. Beim Aktivieren von Hyper-V wird Unterstützung für PowerShell-Module installiert. Sie können prüfen, ob Sie über das Modul verfügen, indem Sie PowerShell an einer Eingabeaufforderung mit erhöhten Rechten öffnen und das folgende Cmdlet ausführen:

    ```powershell  
    Get-Command -Module hyper-v
    ```

    Wenn Ihnen die Hyper-V-Befehle nicht zur Verfügung stehen, finden Sie weitere Informationen unter [Arbeiten mit Hyper-V und Windows PowerShell](/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell). 

2. Ermitteln Sie den Pfad zu Ihrer VHD auf Ihrem Computer. Führen Sie das folgende Cmdlet aus:

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    Das Cmdlet gibt das VHD-Objekt zurück und zeigt die Attribute an, wie z. B.:
    
    ```powershell  
    ComputerName            : YOURMACHINENAME
    Path                    : <path-to-your-VHD>
    VhdFormat               : VHD
    VhdType                 : Fixed
    FileSize                : 68719477248
    Size                    : 68719476736
    MinimumSize             : 32212254720
    LogicalSectorSize       : 512
    PhysicalSectorSize      : 512
    BlockSize               : 0
    ParentPath              :
    DiskIdentifier          : 3C084D21-652A-4C0E-B2D1-63A8E8E64C0C
    FragmentationPercentage : 0
    Alignment               : 1
    Attached                : False
    DiskNumber              :
    IsPMEMCompatible        : False
    AddressAbstractionType  : None
    Number                  :
    ```

3. Prüfen Sie beim VHD-Objekt, ob es die Anforderungen für Azure Stack Hub erfüllt.
    - [VHD hat einen Typ mit fester Größe.](#vhd-is-of-fixed-type)
    - [VHD hat eine minimale virtuelle Größe von mindestens 20 MB.](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [VHD ist ausgerichtet.](#vhd-is-aligned)
    - [VHD-Bloblänge = virtuelle Größe + VHD-Fußzeilenlänge (512).](#vhd-blob-length) 
    
    Darüber hinaus unterstützt Azure Stack Hub nur Images von [VMs der 1. Generation](#generation-one-vms).

4. Wenn Ihre VHD nicht mit Azure Stack Hub kompatibel ist, müssen Sie zum Quellimage und Hyper-V zurückkehren, eine den Anforderungen entsprechende VHD erstellen und diese dann hochladen. Um mögliche Beschädigungen beim Hochladen zu minimieren, verwenden Sie AzCopy.

### <a name="how-to-fix-your-vhd"></a>Festlegen der festen Größe der VHD

Die folgenden Anforderungen müssen für die Kompatibilität Ihrer VHD mit Azure Stack Hub erfüllt sein.

#### <a name="vhd-is-of-fixed-type"></a>VHD hat einen Typ mit fester Größe
**Ermitteln Sie Folgendes**: Verwenden Sie das Cmdlet `get-vhd` zum Abrufen des VHD-Objekts.  
**Problemlösung:** Sie können eine VHDX-Datei in das VHD-Format und einen dynamisch erweiterbaren Datenträger in einen Datenträger mit fester Größe konvertieren, die Generation eines virtuellen Computers kann jedoch nicht geändert werden.
Verwenden Sie [Hyper-V-Manager oder PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk), um den Datenträger zu konvertieren.

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>VHD hat eine minimale virtuelle Größe von mindestens 20 MB
**Ermitteln Sie Folgendes**: Verwenden Sie das Cmdlet `get-vhd` zum Abrufen des VHD-Objekts.  
**Problemlösung:** Verwenden Sie [Hyper-V-Manager oder PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk), um die Größe des Datenträgers zu ändern. 

### <a name="vhd-is-aligned"></a>VHD ist ausgerichtet
**Ermitteln Sie Folgendes**: Verwenden Sie das Cmdlet `get-vhd` zum Abrufen des VHD-Objekts.  
**Problemlösung:** Die virtuelle Größe muss ein Vielfaches von 1 MB sein. 

Datenträger benötigen eine virtuelle Größe, die auf 1 MiB ausgerichtet ist. Wenn die Größe Ihrer VHD ein Bruchteil von 1 MiB ist, muss die Größe des Datenträgers in ein Vielfaches von 1 MiB geändert werden. Bei Datenträgern mit einem MiB-Bruchteil treten Fehler auf, wenn Images auf der Grundlage der hochgeladenen VHD erstellt werden. Verwenden Sie zur Überprüfung der Größe das PowerShell-Cmdlet Get-VHD, und sehen Sie sich den Wert von „Size“ an. Der Wert in Azure muss ein Vielfaches von 1 MiB sein. Sehen Sie sich außerdem den Wert von „FileSize“ an. Dieser Wert muss der Summe aus dem Wert von „Size“ plus 512 Bytes für die VHD-Fußzeile entsprechen.

Verwenden Sie [Hyper-V-Manager oder PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk), um die Größe des Datenträgers zu ändern. 


### <a name="vhd-blob-length"></a>VHD-Bloblänge
**Ermitteln Sie Folgendes**: Verwenden Sie das Cmdlet `get-vhd` zum Anzeigen von `Size`   
**Problemlösung:** VHD-Bloblänge = virtuelle Größe + VHD-Fußzeilenlänge (512). Die Eigenschaften der VHD werden in einer kleinen Fußzeile am Ende des Blobs beschrieben. `Size` muss in Azure ein Vielfaches von 1 MiB und `FileSize` sein, das `Size` + 512 Bytes für die VHD-Fußzeile entspricht.

Verwenden Sie [Hyper-V-Manager oder PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk), um die Größe des Datenträgers zu ändern. 

### <a name="generation-one-vms"></a>VMs der 1. Generation
**Ermitteln Sie Folgendes**: Bestätigen Sie mit dem Cmdlet `Get-VM | Format-Table Name, Generation`, ob es sich bei Ihrem virtuellen Computer um die 1. Generation handelt.  
**Problemlösung:** Sie müssen Ihre VM in Ihrem Hypervisor (Hyper-V) neu erstellen.