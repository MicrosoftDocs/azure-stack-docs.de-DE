---
title: Grafikprozessor (GPU)-VM in Azure Stack Hub
description: Referenz für GPU-Computing in Azure Stack Hub.
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: reference
ms.date: 07/07/2020
ms.reviewer: kivenkat
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: ec859cfc977c5596f44bf349c765c68873ea7430
ms.sourcegitcommit: 17ef9f9119f5fea9782adeefb9a430e6a3a650e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170352"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Grafikprozessor (GPU)-VM in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel erfahren Sie, welche Grafikprozessormodelle (Graphics Processing Unit, GPU) im Azure Stack Hub-System mit mehreren Knoten unterstützt werden. Außerdem finden Sie hier Anweisungen zum Installieren der mit den GPUs verwendeten Treiber. Die GPU-Unterstützung in Azure Stack Hub ermöglicht Lösungen wie künstliche Intelligenz, Training, Rückschluss und Datenvisualisierung. Der AMD Radeon Instinct Mi25 kann zur Unterstützung von grafikintensiven Anwendungen wie Autodesk AutoCAD verwendet werden.

Während des Public Preview-Zeitraums stehen drei GPU-Modelle zur Auswahl: NVIDIA V100, NVIDIA T4 und AMD Mi25. Diese physischen GPUs entsprechen den folgenden Azure-VM-Typen der N-Serie:
- [NCv3](https://docs.microsoft.com/azure/virtual-machines/ncv3-series)
- [NVv4 (AMD Mi25)](https://docs.microsoft.com/azure/virtual-machines/nvv4-series)
- NCas_T4_v3

> [!IMPORTANT]  
> Die GPU-Unterstützung in Azure Stack Hub ist zurzeit als öffentliche Vorschauversion verfügbar. Wenn Sie am Vorschauprogramm teilnehmen möchten, füllen Sie das Formular unter [aka.ms/azurestackhubgpupreview](https://aka.ms/azurestackhubgpupreview) aus.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ncv3"></a>NCv3

NCv3-Serien-VMs werden mit NVIDIA Tesla V100-GPUs betrieben. Kunden können diese neuen GPUs für herkömmliche HPC-Workloads wie Modellierung von Lagerstätten, DNA-Sequenzierung, Proteinanalysen, Monte Carlo-Simulationen und Ähnliches nutzen. 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="nvv4"></a>NVv4

Die VMs der NVv4-Serie verfügen über [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25)-GPUs. Mit der NVv4-Serie führt Azure Stack Hub VMs mit partiellen GPUs ein. Diese Größe kann für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops verwendet werden. Von virtuellen Computern der NVv4-Serie wird derzeit nur das Windows-Gastbetriebssystem unterstützt. 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="ncas_t4_v3"></a>NCas_T4_v3

Die neue NVIDIA T4-VM-Größe ermöglicht die Ausführung einfacher Machine Learning-, Rückschluss- und Visualisierungsworkloads in Azure Stack Hub. Diese VM-Größe kann zurzeit nicht über das Portal bereitgestellt werden. Stattdessen müssen Sie PowerShell oder die CLI verwenden.


| Size | vCPU | Memory: GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |4 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |4 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |4 |448 | 4 | 64 | 32 | 8 | 


## <a name="patch-and-update-fru-behavior-of-vms"></a>Patch- und Update- sowie FRU-Verhalten von VMs 

Vorgänge wie Patchen und Aktualisieren (Patch und Update, PnU) und der Austausch von Hardware (Field Replaceable Unit, FRU) für Azure Stack Hub führen bei GPU-VMs zu einer Downtime. In der folgenden Tabelle finden Sie eine Beschreibung des VM-Zustands während dieser Aktivitäten und die manuelle Aktion, mit der der Benutzer die VMs nach diesen Vorgängen wieder verfügbar machen kann. 

| Vorgang | PnU: Express-Update | PnU: Vollständiges Update, OEM-Update | FRU | 
| --- | --- | --- | --- | 
| VM-Zustand  | Während des Updates und danach nicht verfügbar ohne einen manuellen Startvorgang. | Während des Updates nicht verfügbar. Nach dem Update durch Ausführen der manuellen Benutzeraktion verfügbar. | Während des Updates nicht verfügbar. Nach dem Update durch Ausführen der manuellen Benutzeraktion verfügbar.| 
| Manuelle Benutzeraktion | Wenn die VM während des Updates verfügbar gemacht werden muss und GPU-Partitionen verfügbar sind, können Sie die VM über das Portal neu starten, indem Sie auf die Schaltfläche **Neu starten** klicken. Starten Sie die VM nach dem Update mithilfe der Schaltfläche **Neu starten** im Portal neu. | Die VM kann während des Updates nicht verfügbar gemacht werden. Nach dem Update müssen Sie die VM beenden und ihre Zuordnung aufheben, indem Sie auf die Schaltfläche **Beenden** klicken, und sie anschließend mithilfe der Schaltfläche „Starten“ wieder starten. | Die VM kann während des Updates nicht verfügbar gemacht werden. Nach dem Update müssen Sie die VM beenden und ihre Zuordnung aufheben, indem Sie auf die Schaltfläche **Beenden** klicken, und sie anschließend mithilfe der Schaltfläche **Starten** wieder starten.| 

## <a name="guest-driver-installation"></a>Installation des Gasttreibers 

### <a name="amd-mi25"></a>AMD Mi25
Im Artikel [Installieren von AMD-GPU-Treibern für virtuelle Computer der N-Serie unter Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup) finden Sie Anweisungen zum Installieren des Treibers für den AMD Radeon Instinct Mi25 auf der GPU-fähigen NVv4-VM sowie zum Überprüfen der Treiberinstallation. Diese Erweiterung funktioniert nur im verbundenen Modus.

### <a name="nvidia"></a>NVIDIA

NVIDIA-Treiber sind erforderlich, um CUDA- oder GRID-Workloads auf der VM ausführen zu können. Stellen Sie sicher, dass Sie über die entsprechenden GRID-Lizenzen und einen eingerichteten Lizenzserver verfügen, bevor Sie die GRID-Treiber mithilfe der Erweiterung auf der VM installieren. Informationen zum Einrichten des Lizenzservers finden Sie [hier](https://docs.nvidia.com/grid/ls/latest/grid-license-server-user-guide/index.html). CUDA-Treiber erfordern keinen Lizenzserver.

NVIDIA-CUDA- und -GRID-Treiber müssen manuell auf der VM installiert werden. Die Tesla-CUDA-Treiber können von der NVIDIA-[Downloadwebsite](https://www.nvidia.com/Download/index.aspx) heruntergeladen werden. GRID-Treiber können über den NVIDIA Application Hub heruntergeladen werden, sofern Sie über die erforderlichen Lizenzen verfügen.

## <a name="next-steps"></a>Nächste Schritte 

[Features von Azure Stack-VMs](azure-stack-vm-considerations.md) 
