---
title: Für Azure Stack Hub unterstützte Gastbetriebssysteme
titleSuffix: Azure Stack
description: Erfahren Sie, welche Gastbetriebssysteme mit Azure Stack Hub verwendet werden können.
author: sethmanheim
ms.topic: article
ms.date: 02/01/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 336a4d27adf5f0ab52fffc6b624ce84f7fd0b53c
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245907"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Für Azure Stack Hub unterstützte Gastbetriebssysteme

## <a name="windows"></a>Windows

Azure Stack Hub unterstützt die Windows-Gastbetriebssysteme aus der folgenden Tabelle:

| Betriebssystem | BESCHREIBUNG | Verfügbar im Azure Stack Hub-Marketplace |
| --- | --- | --- |
| Windows Server, Version 1709 | 64 Bit | Core mit Containern |
| Windows Server 2019 | 64 Bit |  Datacenter, Datacenter Core, Datacenter mit Containern |
| Windows Server 2016 | 64 Bit |  Datacenter, Datacenter Core, Datacenter mit Containern |
| Windows Server 2012 R2 | 64 Bit |  Datacenter |
| Windows Server 2012 | 64 Bit |  Datacenter |
| Windows Server 2008 R2 SP1 | 64 Bit |  Datacenter |
| Windows Server 2008 SP2 | 64 Bit |  Verwendung eigener Images (Bring Your Own Image, BYOI) |
| Windows 10 *(siehe Hinweis 1)* | 64 Bit, Pro und Enterprise | Verwendung eigener Images (Bring Your Own Image, BYOI) |

> [!NOTE]
> Wenn Sie Windows 10-Clientbetriebssysteme für Azure Stack Hub bereitstellen möchten, müssen Sie über eine [benutzerspezifische Windows-Lizenzierung](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) verfügen oder den Kauf über einen [QMTH (Qualified Multitenant Hoster)](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx) abwickeln.

Marketplace-Images stehen für eine Lizenzierung mit nutzungsbasierter Abrechnung oder für eine BYOL-Lizenzierung (EA/SPLA) zur Verfügung. Für eine einzige Azure Stack Hub-Instanz können jedoch nicht beide Lizenzierungstypen verwendet werden. Während der Bereitstellung fügt Azure Stack Hub eine geeignete Version des Gast-Agents in das Image ein.

Rechenzentrumseditionen stehen im Azure Stack Hub-Marketplace zum Download zur Verfügung. Kunden können ihre eigenen Serverimages mit anderen Editionen verwenden. Windows-Clientimages stehen im Azure Stack Hub-Marketplace nicht zur Verfügung.

## <a name="linux"></a>Linux

Die im Azure Stack Hub-Marketplace verfügbaren Linux-Distributionen enthalten den erforderlichen Windows Azure-Linux-Agent (WALA). Wenn Sie in Azure Stack Ihr eigenes Image verwenden möchten, beachten Sie die Richtlinien unter [Hinzufügen von Linux-Images zu Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Benutzerdefinierte Images müssen mit der neuesten öffentlichen WALA-Version (in Azure Stack Hub Build 1903 und höher oder mit dem 1901/1902-Hotfix) oder mit Version 2.2.20 erstellt werden. Versionen vor 2.2.20 und zwischen 2.2.21 und 2.2.2.34 (einschließlich) funktionieren in Azure Stack Hub möglicherweise nicht ordnungsgemäß. Unter Azure Stack Hub-Release 1910 und höher funktionieren alle Versionen von Azure WALA-Agentversionen mit Azure Stack Hub.
>
> [cloud-init](https://cloud-init.io/) wird nur ab Azure Stack Hub 1910 unterstützt.

| Distribution | BESCHREIBUNG | Herausgeber | Azure Stack Hub Marketplace |
| --- | --- | --- | --- |
| CentOS-basiert 8.0 | 64 Bit | Rogue Wave | Ja |
| CentOS-basiert 7.8 | 64 Bit | Rogue Wave | Ja |
| CentOS-basiert 7.7 LVM | 64 Bit | Rogue Wave | Ja |
| CentOS-basiert 7.7 | 64 Bit | Rogue Wave | Ja |
| CentOS-basiert 7.6 | 64 Bit | Rogue Wave | Ja |
| 7\.5 (CentOS-basiert) | 64 Bit | Rogue Wave | Ja |
| CentOS-basiert 7.5 LVM | 64 Bit | Rogue Wave | Ja |
| 7.4 (CentOS-basiert) | 64 Bit | Rogue Wave | Ja |
| 7\.3 (CentOS-basiert) | 64 Bit | Rogue Wave | Ja |
| 6\.9 (CentOS-basiert) | 64 Bit | Rogue Wave | Ja |
| 6\.10 (CentOS-basiert) | 64 Bit | Rogue Wave | Ja |
| ClearLinux | 64 Bit | ClearLinux.org | Ja |
| CoreOS Linux (stabil) |  64 Bit | CoreOS | Ja |
| Debian 8 „Jessie“ | 64 Bit | credativ |  Ja |
| Debian 9 „Stretch“ | 64 Bit | credativ | Ja |
| Oracle Linux | 64 Bit | Oracle | Ja |
| Red Hat Enterprise Linux 7.1 (und höher) | 64 Bit | Red Hat | Verwendung eigener Images (Bring Your Own Image, BYOI) |
| SLES 11SP4 | 64 Bit | SUSE | Ja |
| SLES 12SP3 | 64 Bit | SUSE | Ja |
| Ubuntu 14.04-LTS | 64 Bit | Canonical | Ja |
| Ubuntu 16.04-LTS | 64 Bit | Canonical | Ja |
| Ubuntu 18.04-LTS | 64 Bit | Canonical | Ja |
| Ubuntu Server 20.04 LTS | 64 Bit | Canonical | Ja |

Informationen zur Unterstützung von Red Hat Enterprise Linux finden Sie unter [Red Hat and Azure Stack Hub: Frequently Asked Questions](https://access.redhat.com/articles/3413531) (Red Hat und Azure Stack: häufig gestellte Fragen).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure Stack Hub-Marketplace finden Sie in den folgenden Artikeln:

- [Herunterladen von Marketplace-Elementen](azure-stack-download-azure-marketplace-item.md)  
- [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
