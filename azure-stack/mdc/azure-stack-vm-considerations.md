---
title: Features von Azure Stack-VMs | Microsoft-Dokumentation
description: Enthält Informationen zu den unterschiedlichen Features und Überlegungen bei der Verwendung von VMs in einem MDC (Modular Data Center).
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
ms.date: 12/20/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: b8a5d2a0f08ac36b4f4ebc20e0dc3c9eea67218a
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935099"
---
# <a name="azure-stack-vm-features"></a>Features von Azure Stack-VMs

Über virtuelle Azure Stack-Computer (VMs) werden bedarfsgesteuerte, skalierbare Computingressourcen bereitgestellt. Vor dem Bereitstellen von virtuellen Computern sollten Sie sich mit den Unterschieden zwischen den VM-Features von Azure Stack und Microsoft Azure vertraut machen. In diesem Artikel werden diese Unterschiede beschrieben und wichtige Überlegungen zur Planung von VM-Bereitstellungen erörtert. Informationen zu allgemeinen Unterschieden zwischen Azure Stack und Azure finden Sie im Artikel [Key considerations](../user/azure-stack-considerations.md) (Wichtige Aspekte).

## <a name="vm-differences"></a>VM-Unterschiede

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
| VM-Images | Azure Marketplace enthält Images, die Sie zum Erstellen eines virtuellen Computers verwenden können. Auf der Seite [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) können Sie die Liste mit den Images anzeigen, die im Azure Marketplace verfügbar sind. | Im Azure Stack-Marketplace sind standardmäßig keine Images verfügbar. Der Azure Stack-Cloudadministrator muss Images veröffentlichen oder auf den Azure Stack-Marketplace herunterladen, bevor sie von Benutzern verwendet werden können. |
| Größen virtueller Computer | Azure unterstützt eine Vielzahl von Größen für VMs. Informationen zu den verfügbaren Größen und Optionen finden Sie in den Themen [Größen für virtuelle Windows-Computer in Azure](/azure/virtual-machines/sizes) und [Größen für virtuelle Linux-Computer in Azure](/azure/virtual-machines/linux/sizes). | Azure Stack unterstützt eine Teilmenge der VM-Größen, die in Azure verfügbar sind. Die Liste mit den unterstützten Größen finden Sie in diesem Artikel im Abschnitt [VM-Größen](#vm-sizes). |
| Kontingente für virtuelle Computer | [Kontingentgrenzen](/azure/azure-subscription-service-limits#service-specific-limits) werden von Microsoft festgelegt. | Der Azure Stack-Cloudadministrator muss Kontingente zuweisen, bevor Benutzern VMs angeboten werden. |
| VM-Erweiterungen |Azure unterstützt viele verschiedene VM-Erweiterungen. Weitere Informationen zu den verfügbaren Erweiterungen finden Sie im Artikel zu den [Erweiterungen und Features für VMs](/azure/virtual-machines/windows/extensions-features).| Azure Stack unterstützt eine Teilmenge der Erweiterungen, die in Azure verfügbar sind, und jede Erweiterung hat eine bestimmte Version. Der Azure Stack-Cloudadministrator kann jeweils wählen, welche Erweiterungen für seine Benutzer verfügbar gemacht werden sollen. Die Liste mit den unterstützten Erweiterungen finden Sie in diesem Artikel im Abschnitt [VM-Erweiterungen](#vm-extensions). |
| Netzwerk für virtuelle Computer | Öffentliche IP-Adressen, die einer Mandanten-VM zugewiesen sind, sind über das Internet zugänglich.<br><br><br>Azure-VMs verfügen über einen festen DNS-Namen. | Öffentliche IP-Adressen, die einer Mandanten-VM zugewiesen sind, sind nur in der Azure Stack Development Kit-Umgebung zugänglich. Ein Benutzer muss per [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) Zugriff auf das Azure Stack Development Kit haben, um eine Verbindung mit einer VM herstellen zu können, die in Azure Stack erstellt wird.<br><br>VMs, die in einer bestimmten Azure Stack-Instanz erstellt werden, verfügen über einen DNS-Namen basierend auf dem Wert, der vom Cloudadministrator konfiguriert wird. |
| VM-Speicher | Unterstützt [verwaltete Datenträger](/azure/virtual-machines/windows/managed-disks-overview). | Verwaltete Datenträger werden in Azure Stack ab Version 1808 unterstützt. |
| Leistung von VM-Datenträgern | Abhängig von Datenträgertyp und -größe. | Richtet sich nach der VM-Größe des virtuellen Computers, an den die Datenträger angefügt sind. Weitere Informationen finden Sie im Artikel [In Azure Stack unterstützte VM-Größen](azure-stack-vm-sizes.md).
| API-Versionen | Azure verfügt für alle VM-Features immer über die aktuellen API-Versionen. | Azure Stack unterstützt bestimmte Azure-Dienste und bestimmte API-Versionen für diese Dienste. Die Liste mit den unterstützten API-Versionen finden Sie in diesem Artikel im Abschnitt [API-Versionen](#api-versions). |
| Azure-Instanzmetadatendienst | Instance Metadata Service stellt Informationen zum Ausführen von VM-Instanzen bereit, die zum Verwalten und Einrichten Ihres virtuellen Computers verwendet werden können.  | Der Azure-Instanzmetadatendienst wird für Azure Stack nicht unterstützt. |
| VM-Verfügbarkeitsgruppen|Mehrere Fehlerdomänen (zwei oder drei pro Region)<br>Mehrere Updatedomänen|Mehrere Fehlerdomänen (zwei oder drei pro Region)<br>Einzelne Updatedomäne mit Livemigration zum Schutz von Workloads während des Updates. 20 Updatedomänen werden aus Gründen der Vorlagenkompatibilität unterstützt.<br>Der virtuelle Computer und die Verfügbarkeitsgruppe müssen sich am gleichen Standort und in der gleichen Ressourcengruppe befinden.|
| VM-Skalierungsgruppen|Die automatische Skalierung wird unterstützt.|Die automatische Skalierung wird nicht unterstützt.<br><br>Fügen Sie einer Skalierungsgruppe mit dem Portal, Resource Manager-Vorlagen oder PowerShell weitere Instanzen hinzu. |
| Cloudzeuge | Wählen Sie die Endpunkte aus den in Azure Stack verfügbaren Speicherkontoeigenschaften aus. | [Cloudzeuge](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) ist ein Quorumzeugentyp für Failovercluster, der Microsoft Azure zum Bereitstellen einer Stimme für das Clusterquorum verwendet.<br>Die Endpunkte in Global Azure sehen im Vergleich zu Azure Stack möglicherweise wie folgt aus:<br>Für Global Azure:<br>`https://mywitness.blob.core.windows.net/`<br>Für Azure Stack:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnose des virtuellen Computers | Die Linux-VM-Diagnose wird unterstützt. | Die Linux-VM-Diagnose wird in Azure Stack nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren. |

## <a name="vm-sizes"></a>VM-Größen

Azure Stack erzwingt Ressourcengrenzwerte, um einen übermäßigen Ressourcenverbrauch (auf dem lokalen Server und auf Dienstebene) zu vermeiden. Durch diese Grenzwerte wird eine optimale Nutzung durch Mandanten ermöglicht, da die Auswirkungen des Ressourcenverbrauchs durch andere Mandanten verringert werden.

- Für ausgehenden Netzwerkdatenverkehr des virtuellen Computers gelten Bandbreitenobergrenzen. Die Obergrenzen in Azure Stack sind mit denen in Azure identisch.
- Für Speicherressourcen implementiert Azure Stack Speicher-IOPS-Grenzwerte (Input/Output Operations Per Second), um den allgemeinen übermäßigen Ressourcenverbrauch für die Speichernutzung durch Mandanten zu vermeiden.
- Für VM-Datenträger ist Datenträger-IOPS unter Azure Stack keine Funktion des Datenträgertyps, sondern der VM-Größe. Dies bedeutet, dass der IOPS-Grenzwert für einen zweiten Datenträger für einen virtuellen Computer der Standard_Fs-Reihe 2300 IOPS beträgt (unabhängig davon, ob Sie einen Datenträger des Typs SSD oder HDD auswählen).

Die folgende Tabelle enthält die in Azure Stack unterstützten virtuellen Computer sowie ihre Konfiguration:

| type            | Size          | Bereich der unterstützten Größen |
| ----------------| ------------- | ------------------------ |
|Allgemeiner Zweck  |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Allgemeiner Zweck  |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Allgemeiner Zweck  |Av2-Serie     |[A1_v2–A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Allgemeiner Zweck  |D-Serie       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Allgemeiner Zweck  |Dv2-Serie     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Allgemeiner Zweck  |DS-Serie      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Allgemeiner Zweck  |DSv2-Serie    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Arbeitsspeicheroptimiert |D-Serie       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Arbeitsspeicheroptimiert |DS-Serie      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Arbeitsspeicheroptimiert |Dv2-Serie     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Arbeitsspeicheroptimiert |DSv2-Serie    |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Computeoptimiert|F-Serie       |[F1–F16](azure-stack-vm-sizes.md#f-series)    |
|Computeoptimiert|Fs-Serie      |[F1s–F16s](azure-stack-vm-sizes.md#fs-series)    |
|Computeoptimiert|Fsv2-Serie    |[F2s_v2–F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |
|GPU-fähig      |NVv4-Serie    |[NVv4](azure-stack-vm-sizes.md#nvv4-series) |

Die VM-Größen und entsprechenden Ressourcenmengen sind für Azure Stack und Azure konsistent. Diese Konsistenz umfasst die Arbeitsspeichermenge, die Anzahl von Kernen und die Anzahl bzw. Größe von Datenträgern für Daten, die erstellt werden können. Die Leistung von VMs mit der gleichen Größe richtet sich aber nach den zugrunde liegenden Eigenheiten der jeweiligen Azure Stack-Umgebung.

## <a name="vm-extensions"></a>VM-Erweiterungen

Azure Stack umfasst einige wenige Erweiterungen. Updates und zusätzliche Erweiterungen sind per Marketplace-Syndikation verfügbar.

Verwenden Sie das folgende PowerShell-Skript, um die Liste mit den VM-Erweiterungen abzurufen, die in Ihrer Azure Stack-Umgebung verfügbar sind:

### <a name="az-modules"></a>[Az-Module](#tab/az1)

```powershell
Get-AzVmImagePublisher -Location local | `
  Get-AzVMExtensionImageType | `
  Get-AzVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

---

Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Sie eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.

## <a name="api-versions"></a>API-Versionen

VM-Features in Azure Stack unterstützen die folgenden API-Versionen:

„2017-12-01“, „2017-03-30“, „2016-03-30“ und „2015-06-15“.

Sie können das folgende PowerShell-Skript verwenden, um die API-Versionen für die VM-Features abzurufen, die in Ihrer Azure Stack-Umgebung verfügbar sind:

### <a name="az-modules"></a>[Az-Module](#tab/az)

```powershell
Get-AzResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

---

Die Liste mit den unterstützten Ressourcentypen und API-Versionen kann variieren, wenn der Cloudbetreiber Ihre Azure Stack-Umgebung auf eine neuere Version aktualisiert.

## <a name="windows-activation"></a>Aktivierung von Windows

Für die Nutzung von Windows-Produkten gelten die Produktnutzungsrechte und Microsoft-Lizenzbedingungen. Bei Azure Stack werden Windows Server-VMs über die [automatische VM-Aktivierung](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) aktiviert.

- Beim Azure Stack-Host wird Windows durch AVMA-Schlüssel für Windows Server 2016 aktiviert. Alle VMs, auf denen Windows Server 2012 R2 oder höher ausgeführt wird, werden automatisch aktiviert.
- VMs, auf denen Windows Server 2012 oder eine frühere Version ausgeführt wird, werden nicht automatisch aktiviert und müssen über die [MAK-Aktivierung](https://technet.microsoft.com/library/ff793438.aspx) aktiviert werden. Zur Verwendung der MAK-Aktivierung müssen Sie Ihren eigenen Product Key angeben.

Bei Microsoft Azure werden Windows-VMs über die KMS-Aktivierung aktiviert. Falls bei der Migration einer VM von Azure Stack nach Azure Probleme mit der Aktivierung auftreten sollten, helfen Ihnen die Informationen unter [Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems) weiter. Weitere Informationen finden Sie im Blogbeitrag [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) (Behandlung von Windows-Aktivierungsfehlern bei Azure-VMs) des Azure-Supportteams.

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erstellen eines virtuellen Windows Server-Computers in Azure Stack mit PowerShell](../user/azure-stack-quick-create-vm-windows-powershell.md)
