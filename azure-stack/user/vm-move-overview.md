---
title: Verschieben einer VM in Azure Stack Hub
description: Erfahren Sie mehr zu den Möglichkeiten, wie Sie eine VM in Azure Stack Hub verschieben können.
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: cfcb5b494374fedaa5bdced131a8a42203aa5eb0
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246570"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>Verschieben einer VM in Azure Stack Hub: Übersicht

Sie können virtuelle Computer (VM) aus Ihrer Umgebung in Azure Stack Hub verschieben. Es gibt einige Einschränkungen, die Sie beim Planen des Verschieben Ihrer Workloads erwarten müssen. In diesem Artikel sind die Anforderungen an virtuelle Festplatten (Virtual Hard Disks, VHDs) in Azure Stack Hub aufgeführt. Azure Stack Hub erfordert eine VHD der 1. Generation. Ihre VM muss generalisiert oder spezialisiert sein. Verwenden Sie generalisierte VMs als Basis-Image für in Azure Stack Hub erstellte VMs. Ein spezialisierte VM enthält Benutzerkonten. Überprüfen Sie zum Migrieren, Vorbereiten und Herunterladen der VHD, ob sie die Anforderungen erfüllt. Laden Sie das Image in ein Speicherkonto in Azure Stack Hub hoch, und erstellen Sie dann die VM in Ihrer Cloud. Wenn eine komplexere Migrationsaufgabe vorliegt, finden Sie eine vollständige Erörterung im Whitepaper zur *Migration zu Azure Stack Hub*.

Es gibt zwei Arten von benutzerdefinierten Images: **generalisierte** und **spezialisierte**.

- **Generalisiertes Image**  
  Bei einem generalisierten Datenträgerimage handelt es sich um ein Image, das mit **Sysprep** vorbereitet wurde, um eindeutige Informationen (z. B. Benutzerkonten) zu entfernen und so die Wiederverwendung für mehrere VMs zu ermöglichen. Generalisierte VHDs eignen sich gut für die Erstellung von Images, die der Betreiber der Azure Stack Hub-Cloud als Marketplace-Elemente verwenden möchte. Images, die über das Administratorportal oder Administratorendpunkte angeboten werden, sind **Plattformimages**.

- **Spezialisiertes Image**  
  Ein spezielles Datenträgerimage ist eine Kopie einer virtuellen Festplatte (VHD) einer bereits vorhandenen VM, die die Benutzerkonten, Anwendungen und andere Statusdaten Ihrer ursprünglichen VM enthält. Dabei handelt es sich normalerweise um das Format, in dem VMs zu Azure Stack Hub migriert werden. Spezialisierte VHDs eignen sich gut für die Migration virtueller Computer aus einer lokalen Umgebung zu Azure Stack Hub.

Wenn Sie ein Image in Azure Stack Hub verschieben, berücksichtigen Sie, wie das Image verwendet werden soll.

- **Persönliche Workload**  
    Möglicherweise haben Sie einen Computer in Ihrer lokalen oder der globalen Azure-Umgebung, den Sie für die Entwicklung oder bestimmte Aufgaben einsetzen und mit Azure Stack Hub gerne in einer privaten Cloud hosten würden. Sie möchten die Daten und Benutzerkonten auf dem Computer beibehalten. Sie sollten diesen Computer als spezialisiertes Image in Azure Stack Hub verschieben.

- **Goldenes Image**  
    Möglicherweise wünschen Sie sich eine einheitliche VM-Konfiguration und das gemeinsame Nutzen einer bestimmten Gruppe von Anwendungen innerhalb Ihrer Arbeitsgruppe. Sie müssen das Image nicht für Benutzer außerhalb Ihrer Azure Stack Hub-Domäne (Verzeichnismandant) freigeben. In diesem Fall sollten Sie das Image generalisieren, indem Sie Daten und Benutzerkonten entfernen. Sie können dieses Image anschließend für andere Benutzer in Ihrem Mandanten freigeben.

- **Azure Stack Hub Marketplace: Angebot**  
    Ihr Cloudbetreiber kann ein generalisiertes Image als Grundlage für ein Marketplace-Angebot verwenden. Sobald Sie Ihr Image vorbereitet haben, kann Ihr Cloudbetreiber das benutzerdefinierte Image verwenden, um ein Marketplace-Angebot für Ihre Azure Stack Hub-Instanz zu erstellen. Benutzer können anhand des Images ihre eigene VM erstellen, wie sie es bei jedem anderen Angebot in Marketplace tun würden. Sie müssen mit Ihrem Cloudbetreiber zusammenarbeiten, um dieses Angebot zu erstellen.

## <a name="verify-vhd-requirements"></a>Überprüfen der VHD-Anforderungen

> [!IMPORTANT]  
> Beim Vorbereiten Ihrer VHD müssen Sie die folgenden Voraussetzungen erfüllen, da Sie sie sonst nicht in Azure Stack Hub einsetzen können.
> Berücksichtigen Sie vor dem Hochladen des Images unbedingt Folgendes:
> - Azure Stack Hub unterstützt nur Images von VMs der 1. Generation.
> - Die VHD hat einen Typ mit fester Größe. Azure Stack Hub unterstützt keine dynamischen VHDs.
> - Die VHD hat eine minimale virtuelle Größe von mindestens 20 MB.
> - Die VHD ist ausgerichtet, was heißt, dass die virtuelle Größe ein Vielfaches von 1 MB sein muss.
> - VHD-Bloblänge = virtuelle Größe + VHD-Fußzeilenlänge (512). Die Eigenschaften der VHD werden in einer kleinen Fußzeile am Ende des Blobs beschrieben. 

Schritte zum Reparieren der VHD finden Sie unter [Überprüfen der VHD](vm-move-from-azure.md#verify-your-vhd).

## <a name="methods-of-moving-a-vm"></a>Methoden zum Verschieben einer VM

In den folgenden Szenarien können Sie Ihre VM manuell in Azure Stack Hub verschieben:

| Szenario | Instructions |
| --- | --- |
| Aus der globalen Azure-Umgebung in Azure Stack Hub | Bereiten Sie Ihre VHD in der globalen Azure-Umgebung vor, und laden Sie sie dann in Azure Stack Hub hoch. Weitere Informationen finden Sie unter [Verschieben einer VM aus Azure in Azure Stack Hub](vm-move-from-azure.md). |
| Lokal generalisiert in Azure Stack Hub | Bereiten Sie Ihre VHD vor, generalisieren Sie eine VHD lokal in Hyper-V, und laden Sie sie dann in Azure Stack Hub hoch. Weitere Informationen finden Sie unter [Verschieben einer generalisierten VM aus einer lokalen Umgebung in Azure Stack Hub](vm-move-generalized.md). |
| Lokal spezialisiert in Azure Stack Hub | Bereiten Sie Ihre spezialisierte lokal in Hyper-V vor, und laden Sie sie dann in Azure Stack Hub hoch. Weitere Informationen finden Sie unter [Verschieben einer spezialisierten VM aus einer lokalen Umgebung in Azure Stack Hub](vm-move-specialized.md). |

## <a name="migrate-to-azure-stack-hub"></a>Migrieren zu Azure Stack Hub

Details, Prüflisten und bewährte Methoden für die Planung und umfassende Migration Ihrer Workloads zu Azure Stack Hub finden Sie in der globalen Azure-Umgebung in einem von den AzureCAT-Experten verfassten Leitfaden. Der Leitfaden konzentriert sich auf die Migration bestehender Anwendungen, die entweder auf physischen Servern oder bestehenden Virtualisierungsplattformen laufen. Durch die Verschiebung dieser Workloads in die Azure Stack Hub-IaaS-Umgebung profitieren Teams von reibungsloseren Vorgängen, Self-Service-Bereitstellungen, standardisierten Hardwarekonfigurationen und Azure-Konsistenz.

[Holen Sie sich das Whitepaper.](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/)

Anleitungen zur Migration finden Sie auch im Cloud Adoption Framework. Weitere Informationen finden Sie unter [Planen Ihrer Azure Stack Hub-Migration](/azure/cloud-adoption-framework/scenarios/azure-stack/plan). 

## <a name="next-steps"></a>Nächste Schritte

[Einführung in Azure Stack Hub-VMs](azure-stack-compute-overview.md)

[Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub](../operator/azure-stack-add-vm-image.md)