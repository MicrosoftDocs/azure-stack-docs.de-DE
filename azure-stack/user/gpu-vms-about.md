---
title: Grafikprozessor (GPU)-VM in Azure Stack Hub
description: Referenz für GPU-Computing in Azure Stack Hub.
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: reference
ms.date: 10/20/2020
ms.reviewer: kivenkat
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: 0379b435a1f8b71766540865f358ca5da71d63e6
ms.sourcegitcommit: 8187658b1d45dceed727aca3ae1b9b57aca04392
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630752"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Grafikprozessor (GPU)-VM in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel ist beschrieben, welche GPU-Modelle (Graphics Processing Unit, Grafikprozessor) in einem Azure Stack Hub-System mit mehreren Knoten unterstützt werden. Außerdem finden Sie hier Anweisungen zum Installieren der mit den GPUs verwendeten Treiber. Die GPU-Unterstützung in Azure Stack Hub ermöglicht Lösungen wie künstliche Intelligenz, Training, Rückschluss und Datenvisualisierung. Der AMD Radeon Instinct MI25 kann zur Unterstützung von grafikintensiven Anwendungen wie Autodesk AutoCAD verwendet werden.

Während des Public Preview-Zeitraums stehen drei GPU-Modelle zur Auswahl: NVIDIA V100, NVIDIA T4 und AMD MI25. Diese physischen GPUs entsprechen den folgenden Azure-VM-Typen der N-Serie:
- [NCv3](/azure/virtual-machines/ncv3-series)
- [NVv4 (AMD MI25)](/azure/virtual-machines/nvv4-series)
- [NCasT4_v3](/azure/virtual-machines/nct4-v3-series)

> [!IMPORTANT]  
> Die GPU-Unterstützung in Azure Stack Hub ist zurzeit als öffentliche Vorschauversion verfügbar. Wenn Sie am Vorschauprogramm teilnehmen möchten, füllen Sie das Formular unter [aka.ms/azurestackhubgpupreview](https://aka.ms/azurestackhubgpupreview) aus.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ncv3"></a>NCv3

NCv3-Serien-VMs werden mit NVIDIA Tesla V100-GPUs betrieben. Kunden können diese neuen GPUs für herkömmliche HPC-Workloads wie Modellierung von Lagerstätten, DNA-Sequenzierung, Proteinanalysen, Monte Carlo-Simulationen und Ähnliches nutzen. 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="nvv4"></a>NVv4

Die VMs der NVv4-Serie verfügen über [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-MI25)-GPUs. Mit der NVv4-Serie führt Azure Stack Hub VMs mit partiellen GPUs ein. Diese Größe kann für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops verwendet werden. Von virtuellen Computern der NVv4-Serie wird derzeit nur das Windows-Gastbetriebssystem unterstützt. 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="ncast4_v3"></a>NCasT4_v3

| Size | vCPU | Memory: GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |8 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |16 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |64 |448 | 4 | 64 | 32 | 8 |

## <a name="patch-and-update-fru-behavior-of-vms"></a>Patch- und Update- sowie FRU-Verhalten von VMs 

Vorgänge wie Patchen und Aktualisieren (Patch und Update, PnU) und der Austausch von Hardware (Field Replaceable Unit, FRU) für Azure Stack Hub führen bei GPU-VMs zu einer Downtime. In der folgenden Tabelle finden Sie eine Beschreibung des VM-Zustands während dieser Aktivitäten und die manuelle Aktion, mit der der Benutzer die VMs nach diesen Vorgängen wieder verfügbar machen kann. 

| Vorgang | PnU: Express-Update | PnU: Vollständiges Update, OEM-Update | FRU | 
| --- | --- | --- | --- | 
| VM-Zustand  | Während des Updates und danach nicht verfügbar ohne einen manuellen Startvorgang. | Während des Updates nicht verfügbar. Nach dem Update durch Ausführen der manuellen Benutzeraktion verfügbar. | Während des Updates nicht verfügbar. Nach dem Update durch Ausführen der manuellen Benutzeraktion verfügbar.| 
| Manuelle Benutzeraktion | Wenn die VM während des Updates verfügbar gemacht werden muss und GPU-Partitionen verfügbar sind, können Sie die VM über das Portal neu starten, indem Sie auf die Schaltfläche **Neu starten** klicken. Starten Sie die VM nach dem Update mithilfe der Schaltfläche **Neu starten** im Portal neu. | Die VM kann während des Updates nicht verfügbar gemacht werden. Nach dem Update müssen Sie die VM beenden und ihre Zuordnung aufheben, indem Sie auf die Schaltfläche **Beenden** klicken, und sie anschließend mithilfe der Schaltfläche „Starten“ wieder starten. | Die VM kann während des Updates nicht verfügbar gemacht werden. Nach dem Update müssen Sie die VM beenden und ihre Zuordnung aufheben, indem Sie auf die Schaltfläche **Beenden** klicken, und sie anschließend mithilfe der Schaltfläche **Starten** wieder starten.| 

## <a name="guest-driver-installation"></a>Installation des Gasttreibers

### <a name="amd-mi25"></a>AMD MI25

Im Artikel [Installieren von AMD-GPU-Treibern für virtuelle Computer der N-Serie unter Windows](/azure/virtual-machines/windows/n-series-amd-driver-setup) erfahren Sie, wie Sie den Treiber für AMD Radeon Instinct MI25 auf dem GPU-fähigen virtuellen NVv4-Computer installieren und die Treiberinstallation überprüfen. Diese Erweiterung funktioniert nur im verbundenen Modus.

### <a name="nvidia"></a>NVIDIA

NVIDIA-Treiber müssen auf dem virtuellen Computer installiert sein, damit für CUDA- oder GRID-Workloads die GPU verwendet werden kann.

#### <a name="use-case-graphicsvisualization"></a>Anwendungsfall: Grafik/Visualisierung

Dieses Szenario erfordert die Verwendung von GRID-Treibern. GRID-Treiber können über den NVIDIA Application Hub heruntergeladen werden, sofern Sie über die erforderlichen Lizenzen verfügen. Die GRID-Treiber erfordern außerdem einen GRID-Lizenzserver mit entsprechenden GRID-Lizenzen, bevor die GRID-Treiber auf der VM verwendet werden können. Informationen zum Einrichten des Lizenzservers finden Sie hier.

#### <a name="use-case-computecuda"></a>Anwendungsfall: Compute/CUDA

NVIDIA-CUDA- und -GRID-Treiber müssen manuell auf der VM installiert werden. Die Tesla-CUDA-Treiber können von der NVIDIA-[Downloadwebsite](https://www.nvidia.com/Download/index.aspx) heruntergeladen werden. CUDA-Treiber erfordern keinen Lizenzserver.

## <a name="next-steps"></a>Nächste Schritte

- [Features von Azure Stack-VMs](azure-stack-vm-considerations.md)  
- [Bereitstellen eines GPU-fähigen IoT-Moduls in Azure Stack Hub](gpu-deploy-sample-module.md)
