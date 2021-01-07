---
title: Behandlung bekannter Probleme mit virtuellen Computern in Azure Stack Hub
description: Erfahren Sie, wie Sie bekannte Probleme mit virtuellen Computern in Azure Stack Hub behandeln.
author: mattbriggs
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/09/2020
ms.openlocfilehash: d752b31ac5076255ae4368904145a50d5a3c09ff
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867161"
---
# <a name="known-issues-vms-on-azure-stack-hub"></a>Bekannte Probleme: VMs in Azure Stack Hub

In diesem Artikel finden Sie Informationen zur Problembehandlung bei bekannten Problemen mit Azure Stack Hub-Computeressourcenanbietern bei der Arbeit mit virtuellen Computern (VMs) und Skalierungsgruppen.

## <a name="portal-doesnt-show-correct-vm-name"></a>Im Portal wird nicht der richtige VM-Name angezeigt
- **Geltungsbereich**  
    Dieses Problem betrifft alle Releases.  
- **Ursache**  
    Wenn die Details eines virtuellen Computers auf dem Übersichtsblatt angezeigt werden, wird der Computername als (nicht verfügbar) angezeigt. Diese Anzeige ist für VMs, die aus speziellen Datenträgern bzw. Datenträgermomentaufnahmen erstellt werden, so beabsichtigt.  
- **Problembehandlung**  
    Wählen Sie im Portal **Einstellungen** > **Eigenschaften** aus.
- **Vorkommen**  
    Allgemein  

## <a name="nvv4-vm-size-on-portal"></a>NVv4-VM-Größe im Portal
- **Geltungsbereich**  
    Dieses Problem tritt bei Azure Stack Hub, Release 2002 und höher, auf.  
- **Ursache**  
    Bei der Erstellung des virtuellen Computers wird die VM-Größe angezeigt: NV4as_v4. Bei Kunden mit der erforderlichen Hardware für die AMD MI25-basierte Azure Stack Hub-GPU-Vorschau ist die VM-Bereitstellung erfolgreich. Bei allen anderen Kunden tritt bei der VM-Bereitstellung mit dieser VM-Größe ein Fehler auf.  
- **Problembehandlung**  
    Keine.  
- **Vorkommen**  
    Allgemein  

## <a name="vm-boot-diagnostics"></a>VM-Startdiagnose
- **Geltungsbereich**  
    Dieses Problem gilt für alle unterstützten Versionen.  
- **Ursache**  
    Wenn Sie einen neuen virtuellen Computer erstellen, wird unter Umständen der folgende Fehler angezeigt: Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“. Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.  
- **Problembehandlung**  
    Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- **Vorkommen**  
    Allgemein  

## <a name="vm-diagnostics-storage-account-not-found"></a>VM-Diagnosespeicherkonto nicht gefunden
- **Geltungsbereich**  
    Dieses Problem gilt für alle unterstützten Versionen.  
- **Ursache**  
    Wenn Sie versuchen, einen virtuellen Computer mit dem Status „Beendet (Zuordnung aufgehoben)“ zu starten, wird unter Umständen der folgende Fehler angezeigt: Das Speicherkonto „diagnosticstorageaccount“ für die VM-Diagnose wurde nicht gefunden. Vergewissern Sie sich, dass das Speicherkonto nicht gelöscht wurde. Der Fehler tritt auf, wenn Sie versuchen, einen virtuellen Computer mit aktivierter Startdiagnose zu starten, aber das Speicherkonto für die Startdiagnose, auf das verwiesen wird, gelöscht wurde.  
- **Problembehandlung**  
    Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.  
- **Häufigkeit** Häufig  

## <a name="consumed-compute-quota"></a>Verbrauchtes Computekontingent
- **Geltungsbereich**  
    Dieses Problem gilt für alle unterstützten Versionen.  
- **Ursache**   
    Wenn Sie einen neuen virtuellen Computer erstellen, wird unter Umständen eine Fehlermeldung wie die folgende angezeigt: Dieses Abonnement hat die Kapazität für „Regionale vCPUs gesamt“ an diesem Standort erreicht. 50 „Regionale vCPUs gesamt“ sind verfügbar, und alle werden im Abonnement verwendet. Dies deutet darauf hin, dass das Kontingent für die Gesamtanzahl verfügbarer Kerne erreicht wurde.  
- **Problembehandlung**  
    Erkundigen Sie sich bei Ihrem Betreiber nach einem Add-On-Plan mit zusätzlichem Kontingent. Das Kontingent des aktuellen Plans kann nicht geändert werden, oder das höhere Kontingent wird nicht berücksichtigt.
- **Vorkommen**  
    Selten  

## <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

-  **Geltungsbereich**  
    Dieses Problem gilt für alle unterstützten Versionen.  
- **Ursache**  
    Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack Hub-Umgebungen mit vier Knoten. Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack Hub-Umgebung mit vier Knoten der Fehler „FabricVmPlacementErrorUnsupportedFaultDomainSize“ auf.  
- **Problembehandlung**  
    Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack Hub-Bereitstellung mit vier Knoten aber immer noch nicht verfügbar.  
- **Vorkommen**  
    Selten  

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den [Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md).
