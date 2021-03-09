---
title: Anbieten eines auf Red Hat basierenden virtuellen Computers für Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Red Hat-Linux-Betriebssystem enthält.
author: sethmanheim
ms.topic: article
ms.date: 2/18/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 2/18/2021
ms.openlocfilehash: 4dc1280b0120c5c64dff6a273ce13ae97dbe980c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840302"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Anbieten eines auf Red Hat basierenden virtuellen Computers für Azure Stack Hub

In diesem Artikel wird beschrieben, wie Sie einen auf Red Hat Enterprise Linux basierenden virtuellen Computer (VM) für die Verwendung in Azure Stack Hub vorbereiten. 

## <a name="offer-a-red-hat-based-vm"></a>Anbieten einer auf Red Hat basierenden VM

Es gibt zwei Möglichkeiten, wie Sie eine auf Red Hat basierende VM in Azure Stack Hub anbieten können:

- Sie können den Computer über den Azure Stack Hub Marketplace anbieten.
    - Red Hat Enterprise Linux-Images sind ein privates Angebot auf Azure Stack Hub. Um dieses Angebot auf der Registerkarte **Marketplace-Verwaltung** verfügbar zu machen, müssen Sie [eine Umfrage durchführen](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u).
    - Weitere Informationen finden Sie unter [Azure Stack Hub Marketplace: Übersicht](azure-stack-marketplace.md).
- Sie können Ihrem Azure Stack Hub Ihr eigenes benutzerdefiniertes Image hinzufügen und das Image dann im Marketplace anbieten. 
    1. Sie müssen über Red Hat-Cloudzugriff verfügen.
    2. Anweisungen zum Vorbereiten des Images für Azure und Azure Stack Hub finden Sie unter [Vorbereiten eines auf Red Hat basierenden virtuellen Computers für Azure](/azure/virtual-machines/linux/redhat-create-upload-vhd).
    3. Anweisungen zum Anbieten Ihres benutzerdefinierten Images im Azure Stack Hub Marketplace finden Sie unter [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Hypervisoren, die zum Ausführen von Red Hat Enterprise Linux zertifiziert sind, finden Sie auf der [Red Hat-Website](https://access.redhat.com/certified-hypervisors).