---
title: Features von Azure Stack Hub-VMs
description: Enthält Informationen zu den unterschiedlichen Features und Aspekten bei der Verwendung von VMs in Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/3/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: f93ce26acd7474def8495e6e0df28bd3b8669848
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "80614435"
---
# <a name="azure-stack-hub-vm-features"></a>Features von Azure Stack Hub-VMs

Über virtuelle Azure Stack Hub-Computer (VMs) werden bedarfsgesteuerte, skalierbare Computingressourcen bereitgestellt. Vor dem Bereitstellen von virtuellen Computern sollten Sie sich mit den Unterschieden zwischen den VM-Features von Azure Stack Hub und Microsoft Azure vertraut machen. In diesem Artikel werden diese Unterschiede beschrieben und wichtige Überlegungen zur Planung von VM-Bereitstellungen erörtert. Informationen zu allgemeinen Unterschieden zwischen Azure Stack Hub und Azure finden Sie im Artikel [Wichtige Aspekte](azure-stack-considerations.md).

## <a name="vm-differences"></a>VM-Unterschiede

| Funktion | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
| VM-Images | Azure Marketplace enthält Images, die Sie zum Erstellen eines virtuellen Computers verwenden können. Auf der Seite [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) können Sie die Liste mit den Images anzeigen, die im Azure Marketplace verfügbar sind. | Im Azure Stack Hub-Marketplace sind standardmäßig keine Images verfügbar. Der Azure Stack Hub-Cloudadministrator muss Images veröffentlichen oder auf den Azure Stack Hub-Marketplace herunterladen, bevor sie von Benutzern verwendet werden können. |
| VHD-Generation | Virtuelle Computer der Generation 2 unterstützen wichtige Features, die bei virtuellen Computern der Generation 1 nicht unterstützt werden. Zu diesen Features gehören mehr Speicher, Intel Software Guard Extensions (Intel SGX) und virtualisierter persistenter Speicher (vPMEM). Virtuelle Computer der Generation 2, die lokal ausgeführt werden, weisen auch einige Features auf, die in Azure noch nicht unterstützt werden. Weitere Informationen finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).  | Azure Stack Hub unterstützt nur virtuelle Computer der 1. Generation. Sie können virtuelle Computer der 1. Generation vom VHDX- in das VHD-Dateiformat und von einem dynamisch erweiterbaren Datenträger in einen Datenträger mit fester Größe konvertieren. Die Generation eines virtuellen Computers kann nicht geändert werden. Weitere Informationen finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2). |
| Größen virtueller Computer | Azure unterstützt eine Vielzahl von Größen für VMs. Informationen zu den verfügbaren Größen und Optionen finden Sie in den Themen [Größen für virtuelle Windows-Computer in Azure](/azure/virtual-machines/virtual-machines-windows-sizes) und [Größen für virtuelle Linux-Computer in Azure](/azure/virtual-machines/linux/sizes). | Azure Stack Hub unterstützt eine Teilmenge der VM-Größen, die in Azure verfügbar sind. Die Liste mit den unterstützten Größen finden Sie in diesem Artikel im Abschnitt [VM-Größen](#vm-sizes). |
| Kontingente für virtuelle Computer | [Kontingentgrenzen](/azure/azure-resource-manager/management/azure-subscription-service-limits#managing-limits) werden von Microsoft festgelegt. | Der Azure Stack Hub-Cloudadministrator muss Kontingente zuweisen, bevor Benutzern VMs angeboten werden. |
| VM-Erweiterungen |Azure unterstützt viele verschiedene VM-Erweiterungen. Weitere Informationen zu den verfügbaren Erweiterungen finden Sie im Artikel zu den [Erweiterungen und Features für VMs](/azure/virtual-machines/windows/extensions-features).| Azure Stack Hub unterstützt eine Teilmenge der Erweiterungen, die in Azure verfügbar sind, und jede Erweiterung hat eine bestimmte Version. Der Azure Stack Hub-Cloudadministrator kann jeweils wählen, welche Erweiterungen für seine Benutzer verfügbar gemacht werden sollen. Die Liste mit den unterstützten Erweiterungen finden Sie in diesem Artikel im Abschnitt [VM-Erweiterungen](#vm-extensions). |
| Netzwerk für virtuelle Computer | Öffentliche IP-Adressen, die einer Mandanten-VM zugewiesen sind, sind über das Internet zugänglich.<br><br><br>Azure-VMs verfügen über einen festen DNS-Namen. | Öffentliche IP-Adressen, die einer Mandanten-VM zugewiesen sind, sind nur in der Azure Stack Development Kit-Umgebung zugänglich. Ein Benutzer muss per [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) Zugriff auf das Azure Stack Development Kit haben, um eine Verbindung mit einer VM herstellen zu können, die in Azure Stack Hub erstellt wird.<br><br>VMs, die in einer bestimmten Azure Stack Hub-Instanz erstellt werden, verfügen über einen DNS-Namen basierend auf dem Wert, der vom Cloudadministrator konfiguriert wird. |
| VM-Speicher | Unterstützt [verwaltete Datenträger](/azure/virtual-machines/windows/managed-disks-overview). | Verwaltete Datenträger werden in Azure Stack Hub ab Version 1808 unterstützt. |
| Leistung von VM-Datenträgern | Abhängig von Datenträgertyp und -größe. | Richtet sich nach der VM-Größe des virtuellen Computers, an den die Datenträger angefügt sind. Weitere Informationen finden Sie im Artikel [In Azure Stack Hub unterstützte VM-Größen](azure-stack-vm-sizes.md).
| API-Versionen | Azure verfügt für alle VM-Features immer über die aktuellen API-Versionen. | Azure Stack Hub unterstützt bestimmte Azure-Dienste und bestimmte API-Versionen für diese Dienste. Die Liste mit den unterstützten API-Versionen finden Sie in diesem Artikel im Abschnitt [API-Versionen](#api-versions). |
| Azure-Instanzmetadatendienst | Instance Metadata Service stellt Informationen zum Ausführen von VM-Instanzen bereit, die zum Verwalten und Einrichten Ihres virtuellen Computers verwendet werden können.  | Der Azure Instance Metadata Service wird für Azure Stack Hub nicht unterstützt. |
| VM-Verfügbarkeitsgruppen|Mehrere Fehlerdomänen (zwei oder drei pro Region)<br>Mehrere Updatedomänen|Mehrere Fehlerdomänen (zwei oder drei pro Region)<br>Einzelne Updatedomäne mit Livemigration zum Schutz von Workloads während des Updates. 20 Updatedomänen werden aus Gründen der Vorlagenkompatibilität unterstützt.<br>Der virtuelle Computer und die Verfügbarkeitsgruppe müssen sich am gleichen Standort und in der gleichen Ressourcengruppe befinden.|
| VM-Skalierungsgruppen|Die automatische Skalierung wird unterstützt.|Die automatische Skalierung wird nicht unterstützt.<br><br>Fügen Sie einer Skalierungsgruppe mit dem Portal, Resource Manager-Vorlagen oder PowerShell weitere Instanzen hinzu. |
| Cloudzeuge | Wählen Sie die Endpunkte aus den in Azure Stack Hub verfügbaren Speicherkontoeigenschaften aus. | [Cloudzeuge](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) ist ein Quorumzeugentyp für Failovercluster, der Microsoft Azure zum Bereitstellen einer Stimme für das Clusterquorum verwendet.<br>Die Endpunkte in Global Azure sehen im Vergleich zu Azure Stack Hub möglicherweise wie folgt aus:<br>Für Global Azure:<br>`https://mywitness.blob.core.windows.net/`<br>Für Azure Stack Hub:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnose des virtuellen Computers | Die Linux-VM-Diagnose wird unterstützt. | Die Linux-VM-Diagnose wird in Azure Stack Hub nicht unterstützt. Wenn Sie eine Linux-VM mit aktivierter VM-Diagnose bereitstellen, schlägt die Bereitstellung fehl. Die Bereitstellung schlägt auch fehl, wenn Sie die grundlegenden Linux-VM-Metriken über die Diagnoseeinstellungen aktivieren. |

## <a name="vm-sizes"></a>VM-Größen

Azure Stack Hub erzwingt Ressourcengrenzwerte, um einen übermäßigen Ressourcenverbrauch (auf dem lokalen Server und auf Dienstebene) zu vermeiden. Durch diese Grenzwerte wird eine optimale Nutzung durch Mandanten ermöglicht, da die Auswirkungen des Ressourcenverbrauchs durch andere Mandanten verringert werden.

- Für ausgehenden Netzwerkdatenverkehr des virtuellen Computers gelten Bandbreitenobergrenzen. Die Obergrenzen in Azure Stack Hub sind mit denen in Azure identisch.
- Für Speicherressourcen implementiert Azure Stack Hub Speicher-IOPS-Grenzwerte (Input/Output Operations Per Second), um den allgemeinen übermäßigen Ressourcenverbrauch für die Speichernutzung durch Mandanten zu vermeiden.
- Für VM-Datenträger ist Datenträger-IOPS unter Azure Stack Hub keine Funktion des Datenträgertyps, sondern der VM-Größe. Dies bedeutet, dass der IOPS-Grenzwert für einen zweiten Datenträger für einen virtuellen Computer der Standard_Fs-Reihe 2300 IOPS beträgt (unabhängig davon, ob Sie einen Datenträger des Typs SSD oder HDD auswählen).

Die folgende Tabelle enthält die in Azure Stack Hub unterstützten virtuellen Computer sowie ihre Konfiguration:

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

Die VM-Größen und entsprechenden Ressourcenmengen sind für Azure Stack Hub und Azure konsistent. Diese Konsistenz umfasst die Arbeitsspeichermenge, die Anzahl von Kernen und die Anzahl bzw. Größe von Datenträgern für Daten, die erstellt werden können. Die Leistung von VMs mit der gleichen Größe richtet sich aber nach den zugrunde liegenden Eigenheiten der jeweiligen Azure Stack Hub-Umgebung.

## <a name="vm-extensions"></a>VM-Erweiterungen

Azure Stack Hub umfasst einige wenige Erweiterungen. Updates und zusätzliche Erweiterungen sind per Marketplace-Syndikation verfügbar.

Verwenden Sie das folgende PowerShell-Skript, um die Liste mit den VM-Erweiterungen abzurufen, die in Ihrer Azure Stack Hub-Umgebung verfügbar sind:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Wenn die Bereitstellung einer Erweiterung für eine VM-Bereitstellung zu lange dauert, sollten Sie eine Zeitüberschreitung der Bereitstellung zulassen und nicht versuchen, den Vorgang zum Aufheben der Zuordnung oder Löschen der VMs zu beenden.

## <a name="api-versions"></a>API-Versionen

VM-Features in Azure Stack Hub unterstützen die folgenden API-Versionen:

„2017-12-01“, „2017-03-30“, „2016-03-30“ und „2015-06-15“.

Sie können das folgende PowerShell-Skript verwenden, um die API-Versionen für die VM-Features abzurufen, die in Ihrer Azure Stack Hub-Umgebung verfügbar sind:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

Die Liste mit den unterstützten Ressourcentypen und API-Versionen kann variieren, wenn der Cloudbetreiber Ihre Azure Stack Hub-Umgebung auf eine neuere Version aktualisiert.

## <a name="windows-activation"></a>Aktivierung von Windows

Für die Nutzung von Windows-Produkten gelten die Produktnutzungsrechte und Microsoft-Lizenzbedingungen. Bei Azure Stack Hub werden Windows Server-VMs über die [automatische VM-Aktivierung](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) aktiviert.

- Beim Azure Stack Hub-Host wird Windows durch AVMA-Schlüssel für Windows Server 2016 aktiviert. Alle VMs, auf denen Windows Server 2012 R2 oder höher ausgeführt wird, werden automatisch aktiviert.
- VMs, auf denen Windows Server 2012 oder eine frühere Version ausgeführt wird, werden nicht automatisch aktiviert und müssen über die [MAK-Aktivierung](https://technet.microsoft.com/library/ff793438.aspx) aktiviert werden. Zur Verwendung der MAK-Aktivierung müssen Sie Ihren eigenen Product Key angeben.

Bei Microsoft Azure werden Windows-VMs über die KMS-Aktivierung aktiviert. Falls bei der Migration einer VM von Azure Stack Hub nach Azure Probleme mit der Aktivierung auftreten sollten, helfen Ihnen die Informationen unter [Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems) weiter. Weitere Informationen finden Sie im Blogbeitrag [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) (Behandlung von Windows-Aktivierungsfehlern bei Azure-VMs) des Azure-Supportteams.

## <a name="high-availability"></a>Hochverfügbarkeit

Ihre VM kann aufgrund geplanter Wartungsarbeiten, die vom Azure Stack Hub-Bediener geplant sind, neu gestartet werden. Zur Erreichung von Hochverfügbarkeit für ein Produktionssystem mit mehreren VMs in Azure werden die VMs in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) angeordnet, um sie auf mehrere Fehlerdomänen und Updatedomänen zu verteilen. Im kleineren Rahmen von Azure Stack Hub ist eine Fehlerdomäne in einer Verfügbarkeitsgruppe als einzelner Knoten in der Skalierungseinheit definiert.  

Die Infrastruktur von Azure Stack Hub verfügt zwar bereits über Resilienz gegenüber Ausfällen, aber bei einem Hardwarefehler kommt es bei der zugrunde liegenden Technologie (Failoverclustering) für VMs auf einem betroffenen physischen Server trotzdem noch zu Ausfallzeiten. Azure Stack Hub unterstützt die Verwendung einer Verfügbarkeitsgruppe mit maximal drei Fehlerdomänen, um Konsistenz mit Azure zu erzielen.

|                   |             |
|-------------------|-------------|
| **Fehlerdomänen** | In einer Verfügbarkeitsgruppe angeordnete VMs werden physisch voneinander isoliert, indem sie so gleichmäßig wie möglich auf mehrere Fehlerdomänen (Azure Stack Hub-Knoten) verteilt werden. Bei einem Hardwarefehler werden VMs aus der fehlerhaften Fehlerdomäne in anderen Fehlerdomänen neu gestartet. Sie werden getrennt von den anderen VMs in separaten Fehlerdomänen, aber nach Möglichkeit in derselben Verfügbarkeitsgruppe gespeichert. Nachdem die Hardware wieder in den Onlinezustand versetzt wurde, wird für die VMs ein neuer Ausgleichsvorgang durchgeführt, um die Hochverfügbarkeit sicherzustellen. |
| **Updatedomänen**| Updatedomänen sind eine andere Option von Azure, mit der für Hochverfügbarkeit in Verfügbarkeitsgruppen gesorgt wird. Eine Updatedomäne ist eine logische Gruppe von zugrunde liegender Hardware, die zur gleichen Zeit gewartet werden kann. VMs in derselben Updatedomäne werden während einer geplanten Wartung gemeinsam neu gestartet. Wenn Mandanten VMs in einer Verfügbarkeitsgruppe erstellen, werden die VMs von der Azure-Plattform automatisch auf diese Updatedomänen verteilt. <br>In Azure Stack Hub wird für VMs eine Livemigration über die anderen Onlinehosts im Cluster durchgeführt, bevor der zugrunde liegende Host aktualisiert wird. Da es während eines Hostupdates nicht zu Mandantenausfallzeiten kommt, ist das Updatedomänenfeature in Azure Stack Hub nur für die Vorlagenkompatibilität mit Azure vorhanden. Virtuelle Computer in einer Verfügbarkeitsgruppe zeigen im Portal „0“ als Nummer der Updatedomäne an. |

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erstellen eines virtuellen Windows Server-Computers in Azure Stack Hub mit PowerShell](azure-stack-quick-create-vm-windows-powershell.md)
