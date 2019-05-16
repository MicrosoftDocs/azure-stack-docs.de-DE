---
title: Hinzufügen von Linux-Images zu Azure Stack
description: Hier erfahren Sie, wie Sie Linux-Images zu Azure Stack hinzufügen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 2f7f0c55f02fd99a419619d878be8300d7326303
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309648"
---
# <a name="add-linux-images-to-azure-stack"></a>Hinzufügen von Linux-Images zu Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können virtuelle Linux-Computer in Azure Stack bereitstellen, indem Sie ein Linux-basiertes Image im Azure Stack-Marketplace hinzufügen. Am einfachsten wird ein Linux-Image über die Marketplace-Verwaltung zu Azure Stack hinzugefügt. Diese Images wurden vorbereitet und auf Kompatibilität mit Azure Stack getestet.

## <a name="marketplace-management"></a>Marketplace-Verwaltung

Die Verfahren zum Herunterladen von Linux-Images aus dem Azure Marketplace werden im Artikel [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md) beschrieben. Wählen Sie die Linux-Images aus, die Sie Benutzern in Azure Stack zur Verfügung stellen möchten. 

Beachten Sie, dass diese Images regelmäßig aktualisiert werden. Überprüfen Sie daher immer wieder die Marketplace-Verwaltung, um auf dem neuesten Stand zu bleiben.

## <a name="prepare-your-own-image"></a>Vorbereiten eines eigenen Image

Laden Sie nach Möglichkeit die Images herunter, die über die Marketplace-Verwaltung verfügbar sind, da diese für Azure Stack konzipiert und getestet wurden.

### <a name="azure-linux-agent"></a>Azure Linux-Agent
Der Azure Linux-Agent (in der Regel `WALinuxAgent` oder `walinuxagent`) ist erforderlich, aber nicht alle Versionen des Agents können mit Azure Stack verwendet werden. Versionen zwischen 2.2.20 und 2.2.35 werden in Azure Stack nicht unterstützt. Um die neuesten Agent-Versionen über 2.2.35 zu verwenden, installieren Sie den Hotfix 1901 bzw. 1902, oder aktualisieren Sie Azure Stack auf die Version 1903 (oder höher). Beachten Sie, dass [cloud-init](https://cloud-init.io/) derzeit nicht für Azure Stack unterstützt wird.

| Azure Stack-Build | Azure Linux-Agent-Build |
| ------------- | ------------- |
| 1.1901.0.99 oder früher | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 oder höher |
| 1.1902.2.73  | 2.2.35 oder höher |
| 1.1903.0.35  | 2.2.35 oder höher |
| Nicht unterstützt | 2.2.21-2.2.34 |

Sie können Ihr eigenes Linux-Image gemäß den folgenden Anweisungen vorbereiten:

* [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES &amp; openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Hinzufügen Ihres Images zum Marketplace

Gehen Sie wie unter [Verfügbarmachen eines VM-Images in Azure Stack](azure-stack-add-vm-image.md) beschrieben vor. Stellen Sie sicher, dass der `OSType`-Parameter auf `Linux` festgelegt ist.

Nachdem Sie das Image zum Marketplace hinzugefügt haben, wird ein Marketplace-Element erstellt, und Benutzer können einen virtuellen Linux-Computer bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Der Azure Stack-Marketplace – Übersicht](azure-stack-marketplace.md)
