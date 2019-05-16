---
title: Unterschiede und Aspekte für virtuelle Computer in Azure Stack | Microsoft-Dokumentation
description: Es werden die Unterschiede und Aspekte beim Verwenden von virtuellen Computern in Azure Stack beschrieben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/19/2018
ms.openlocfilehash: a399d8e3df3212f5d778e54bbc8e2c1a992f8df5
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985784"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Überlegungen zur Verwendung von virtuellen Computern in Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Virtuelle Azure Stack-Computer bieten bedarfsgesteuerte, skalierbare Computingressourcen. Machen Sie sich vor der Bereitstellung virtueller Computer (VMs) mit den Unterschieden zwischen den Features virtueller Computer in Azure Stack und Microsoft Azure vertraut. In diesem Artikel werden diese Unterschiede beschrieben und wichtige Überlegungen zur Planung von Bereitstellungen virtueller Computer erörtert. Informationen zu allgemeinen Unterschieden zwischen Azure Stack und Azure finden Sie im Artikel [Key considerations](azure-stack-considerations.md) (Wichtige Aspekte).

## <a name="cheat-sheet-virtual-machine-differences"></a>Cheatsheet: Unterschiede bei virtuellen Computern

| Feature | Azure (global) | Azure Stack |
| --- | --- | --- |
| VM-Images | Der Azure Marketplace enthält Images, die Sie zum Erstellen eines virtuellen Computers verwenden können. Auf der Seite [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) können Sie die Liste mit den Images anzeigen, die im Azure Marketplace verfügbar sind. | Im Azure Stack-Marketplace sind standardmäßig keine Images verfügbar. Der Azure Stack-Cloudadministrator sollte Images veröffentlichen oder auf den Azure Stack-Marketplace herunterladen, bevor sie von Benutzern verwendet werden können. |
| Größen virtueller Computer | Azure unterstützt eine Vielzahl von Größen für virtuelle Computer. Informationen zu den verfügbaren Größen und Optionen finden Sie in den Themen [Größen für virtuelle Windows-Computer in Azure (Windows)](/azure/virtual-machines/virtual-machines-windows-sizes) und [Größen für virtuelle Linux-Computer in Azure (Linux)](/azure/virtual-machines/linux/sizes). | Azure Stack unterstützt eine Teilmenge der VM-Größen, die in Azure verfügbar sind. Die Liste mit den unterstützten Größen finden Sie in diesem Artikel im Abschnitt [Größen virtueller Computer](#virtual-machine-sizes). |
| Kontingente für virtuelle Computer | [Kontingentgrenzen](/azure/azure-subscription-service-limits#service-specific-limits) werden von Microsoft festgelegt. | Der Azure Stack-Cloudadministrator muss Kontingente zuweisen, bevor den Benutzern die virtuellen Computer angeboten werden. |
| VM-Erweiterungen |Azure unterstützt eine Vielzahl von Erweiterungen für virtuelle Computer. Weitere Informationen zu den verfügbaren Erweiterungen finden Sie im Artikel [Erweiterungen und Features für virtuelle Computer für Windows](/azure/virtual-machines/windows/extensions-features).| Azure Stack unterstützt eine Teilmenge der Erweiterungen, die in Azure verfügbar sind, und jede Erweiterung hat eine bestimmte Version. Der Azure Stack-Cloudadministrator kann jeweils wählen, welche Erweiterungen für seine Benutzer verfügbar gemacht werden sollen. Die Liste mit den unterstützten Erweiterungen finden Sie in diesem Artikel im Abschnitt [VM-Erweiterungen](#virtual-machine-extensions). |
| Netzwerk für virtuelle Computer | Öffentliche IP-Adressen, die dem virtuellen Mandantencomputer zugewiesen sind, sind über das Internet zugänglich.<br><br><br>Azure Virtual Machines verfügen über einen feststehenden DNS-Namen. | Öffentliche IP-Adressen, die einem virtuellen Mandantencomputer zugewiesen sind, sind nur in der Azure Stack Development Kit-Umgebung zugänglich. Ein Benutzer muss per [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) Zugriff auf das Azure Stack Development Kit haben, um eine Verbindung mit einem virtuellen Computer herstellen zu können, der in Azure Stack erstellt wird.<br><br>Virtuelle Computer, die in einer bestimmten Azure Stack-Instanz erstellt werden, verfügen über einen DNS-Namen basierend auf dem Wert, der vom Cloudadministrator konfiguriert wird. |
| VM-Speicher | Unterstützt [verwaltete Datenträger](/azure/virtual-machines/windows/managed-disks-overview). | Verwaltete Datenträger werden in Azure Stack ab Version 1808 unterstützt. |
| Leistung von VM-Datenträgern | Abhängig von Datenträgertyp und -größe. | Abhängig von der VM-Größe des virtuellen Computers, an den die Datenträger angefügt wurden. Lesen Sie den Artikel [In Azure Stack unterstützte VM-Größen](azure-stack-vm-sizes.md).
| API-Versionen | Azure verfügt für alle VM-Features immer über die aktuellen API-Versionen. | Azure Stack unterstützt bestimmte Azure-Dienste und bestimmte API-Versionen für diese Dienste. Die Liste mit den unterstützten API-Versionen finden Sie in diesem Artikel im Abschnitt [API-Versionen](#api-versions). |
| Azure-Instanzmetadatendienst | Der Azure-Instanzmetadatendienst stellt Informationen zum Ausführen von Instanzen virtueller Computer bereit, die zum Verwalten und Konfigurieren Ihrer virtuellen Computer verwendet werden können.  | Der Instanzmetadatendienst wird für Azure Stack nicht unterstützt. |
| VM-Verfügbarkeitsgruppen|Mehrere Fehlerdomänen (zwei oder drei pro Region)<br>Mehrere Updatedomänen|Mehrere Fehlerdomänen (zwei oder drei pro Region)<br>Mehrere Updatedomänen (bis zu 20)|
| VM-Skalierungsgruppen|Unterstützung der Autoskalierung|Keine Unterstützung der Autoskalierung<br>Fügen Sie einer Skalierungsgruppe mit dem Portal, Resource Manager-Vorlagen oder PowerShell weitere Instanzen hinzu. |
| Diagnose des virtuellen Computers | Linux-VM-Diagnose | Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren.

## <a name="virtual-machine-sizes"></a>Größen virtueller Computer

Azure Stack erzwingt Ressourcengrenzwerte, um einen übermäßigen Ressourcenverbrauch (auf dem lokalen Server und auf Dienstebene) zu vermeiden. Durch diese Grenzwerte wird ein besseres Benutzererlebnis im Zusammenhang mit Mandanten sichergestellt, da die Auswirkungen des Ressourcenverbrauchs durch andere Mandanten verringert werden.

- Für ausgehenden Netzwerkdatenverkehr des virtuellen Computers gelten Bandbreitenobergrenzen. Die Obergrenzen in Azure Stack sind mit denen in Azure identisch.
- Für Speicherressourcen implementiert Azure Stack Speicher-IOPS-Grenzwerte, um den allgemeinen übermäßigen Ressourcenverbrauch durch Mandanten für Speicherzugriff zu vermeiden.
- Für VM-Datenträger sind Datenträger-IOPS (Input/Output Operations Per Second, Ein-/Ausgabevorgänge pro Sekunde) in Azure Stack eine Funktion der VM-Größe, nicht des Datenträgertyps. Dies bedeutet, dass der IOPS-Grenzwert für einen einzelnen zusätzlichen Datenträger für einen virtuellen Computer der Standard_Fs-Reihe 2.300 IOPS beträgt – unabhängig davon, ob Sie einen Datenträger des Typs SSD oder HDD auswählen.

Die folgende Tabelle enthält die in Azure Stack unterstützten virtuellen Computer sowie ihre Konfiguration:

| Type           | Größe          | Bereich der unterstützten Größen |
| ---------------| ------------- | ------------------------ |
|Allgemeiner Zweck |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Allgemeiner Zweck |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Allgemeiner Zweck |D-Serie       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Allgemeiner Zweck |Dv2-Serie     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Allgemeiner Zweck |DS-Serie      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Allgemeiner Zweck |DSv2-Serie    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Arbeitsspeicheroptimiert|D-Serie       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Arbeitsspeicheroptimiert|DS-Serie      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Arbeitsspeicheroptimiert|Dv2-Serie     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Arbeitsspeicheroptimiert|DSv2-Serie -  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Die VM-Größen und entsprechenden Ressourcenmengen sind bei Azure Stack und Azure konsistent. Dies umfasst die Arbeitsspeichermenge, die Anzahl von Kernen und die Anzahl bzw. Größe von Datenträgern für Daten, die erstellt werden können. Die Leistung von VMs mit der gleichen Größe richtet sich aber nach den zugrunde liegenden Eigenheiten der jeweiligen Azure Stack-Umgebung.

## <a name="virtual-machine-extensions"></a>VM-Erweiterungen

Azure Stack umfasst einige wenige Erweiterungen. Updates und zusätzliche Erweiterungen sind per Marketplace-Syndikation verfügbar.

Verwenden Sie das folgende PowerShell-Skript, um die Liste mit den VM-Erweiterungen abzurufen, die in Ihrer Azure Stack-Umgebung verfügbar sind:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Sie eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.

## <a name="api-versions"></a>API-Versionen

VM-Features in Azure Stack unterstützen die folgenden API-Versionen:

![VM-Ressourcentypen](media/azure-stack-vm-considerations/vm-resoource-types.png)

Sie können das folgende PowerShell-Skript verwenden, um die API-Versionen für die VM-Features abzurufen, die in Ihrer Azure Stack-Umgebung verfügbar sind:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

Die Liste mit den unterstützten Ressourcentypen und API-Versionen kann variieren, wenn der Cloudbetreiber Ihre Azure Stack-Umgebung auf eine neuere Version aktualisiert.

## <a name="windows-activation"></a>Aktivierung von Windows

Für die Nutzung von Windows-Produkten gelten die Produktnutzungsrechte und Microsoft-Lizenzbedingungen. Bei Azure Stack werden virtuelle Windows Server-Computer (VMs) über die [automatische VM-Aktivierung](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) aktiviert.

- Beim Azure Stack-Host wird Windows durch AVMA-Schlüssel für Windows Server 2016 aktiviert. Alle VMs, auf denen Windows Server 2012 R2 oder höher ausgeführt wird, werden automatisch aktiviert.
- VMs, auf denen Windows Server 2012 oder eine frühere Version ausgeführt wird, werden nicht automatisch aktiviert und müssen über die [MAK-Aktivierung](https://technet.microsoft.com/library/ff793438.aspx) aktiviert werden. Zur Verwendung der MAK-Aktivierung müssen Sie Ihren eigenen Product Key angeben.

Bei Microsoft Azure werden Windows-VMs über die KMS-Aktivierung aktiviert. Wenn bei der Migration einer VM von Azure Stack nach Azure Probleme mit der Aktivierung auftreten sollten, lesen Sie [Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Weitere Informationen finden Sie im Blogbeitrag des Azure-Supportteams [Behandlung von Windows-Aktivierungsfehlern bei Azure-VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/).

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines virtuellen Windows-Computers mithilfe von PowerShell in Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
