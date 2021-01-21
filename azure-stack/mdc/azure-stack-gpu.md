---
title: GPU-VMs in Azure Stack | Microsoft-Dokumentation
description: Referenz zu GPU-Computing in Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: patricka
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 1fb2542680f0fa6d7c020db77ce5e59663d87f9c
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255604"
---
# <a name="gpu-vms-on-azure-stack"></a>GPU-VMs in Azure Stack 

*Anwendungsbereich: Integrierte Azure Stack-Systeme* 

In diesem Thema wird das Verwalten von GPU-VMs in Azure Stack Hub beschrieben.


## <a name="partitioned-gpu-vm-size"></a>Partitionierte GPU-VM-Größe 

Die VMs der NVv4-Serie verfügen über [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25)-GPUs. Mit der NVv4-Serie führt Azure Stack Hub VMs mit partiellen GPUs ein. Diese Größe kann für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops verwendet werden. Von virtuellen Computern der NVv4-Serie wird derzeit nur das Windows-Gastbetriebssystem unterstützt. 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>Patch- und Update- sowie FRU-Verhalten von VMs 

Vorgänge wie Patchen und Aktualisieren (Patch und Update, PnU) und der Austausch von Hardware (Field Replaceable Unit, FRU) für Azure Stack Hub führen bei GPU-VMs zu einer Downtime. In der folgenden Tabelle finden Sie eine Beschreibung des VM-Zustands während dieser Aktivitäten und die manuelle Aktion, mit der der Benutzer die VMs nach diesen Vorgängen wieder verfügbar machen kann. 

| Vorgang | PnU: Express-Update | PnU: Vollständiges Update, OEM-Update | FRU | 
| --- | --- | --- | --- | 
| VM-Zustand  | Während des Updates und danach nicht verfügbar ohne einen manuellen Startvorgang. | Während des Updates nicht verfügbar. Nach dem Update durch Ausführen der manuellen Benutzeraktion verfügbar. | Während des Updates nicht verfügbar. Nach dem Update durch Ausführen der manuellen Benutzeraktion verfügbar.| 
| Manuelle Benutzeraktion | Wenn die VM während des Updates verfügbar gemacht werden muss und GPU-Partitionen verfügbar sind, können Sie die VM über das Portal neu starten, indem Sie auf die Schaltfläche „Neu starten“ klicken. Die VM muss nach dem Update mithilfe der Schaltfläche „Neu starten“ im Portal neu gestartet werden. | Die VM kann während des Updates nicht verfügbar gemacht werden. Nach dem Update müssen Sie die VM beenden und ihre Zuordnung aufheben, indem Sie auf die Schaltfläche „Beenden“ klicken und sie anschließend mithilfe der Schaltfläche „Starten“ wieder starten. | Die VM kann während des Updates nicht verfügbar gemacht werden. Nach dem Update müssen Sie die VM beenden und ihre Zuordnung aufheben, indem Sie auf die Schaltfläche „Beenden“ klicken und sie anschließend mithilfe der Schaltfläche „Starten“ wieder starten.| 

## <a name="guest-driver-installation"></a>Installation des Gasttreibers 

In [diesem Dokument](/azure/virtual-machines/windows/n-series-amd-driver-setup) werden das Einrichten des AMD-Gasttreibers auf einer VM mit aktivierter NVv4-GPU-P sowie die Schritte zum Überprüfen der Treiberinstallation beschrieben. 

## <a name="next-steps"></a>Nächste Schritte 

[Features von Azure Stack-VMs](azure-stack-vm-considerations.md)