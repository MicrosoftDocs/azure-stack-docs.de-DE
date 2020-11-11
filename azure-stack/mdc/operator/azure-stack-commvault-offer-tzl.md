---
title: Anbieten eines Commvault-Marketplace-Elements in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen von Commvault im Azure Stack-Marketplace
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 6ece6792602d90fd43bc682d7547061180ea9289
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330211"
---
# <a name="offer-commvault-marketplace-item-in-azure-stack"></a>Anbieten eines Commvault-Marketplace-Elements in Azure Stack

*Anwendungsbereich: Modular Data Center, Azure Stack Hub Ruggedized*

Commvault unterstützt die Sicherung und Wiederherstellung der folgenden Ressourcentypen in Azure Stack Hub:

- Sicherung auf VM-Ebene
  - IaaS-VM
  - Nicht verwaltete Datenträger
  - Verwaltete Datenträger
  - Weitere Informationen finden Sie unter [Backing Up Virtual Machines](https://documentation.commvault.com/commvault/v11/article?p=86503.htm) (Sichern von virtuellen Computern).

- Sichern von Speicherkonten
  - Blob
  - Weitere Informationen finden Sie unter [Azure Blob Storage Overview](https://documentation.commvault.com/commvault/v11/article?p=30063.htm) (Azure Blob Storage: Übersicht).

- Agent-basierte Sicherung
  - Gastbetriebssystem: Windows und Linux
  - Anwendung: SQL, MySQL
  - Weitere Informationen finden Sie unter [Backup Agents](https://documentation.commvault.com/commvault/v11/article?p=14333.htm) (Sicherungs-Agents).

Sie können Commvault auf einem externen Computer bereitstellen und Ressourcen in Azure Stack Hub remote schützen. Darüber hinaus kann Commvault als virtuelles Gerät in Azure Stack Hub bereitgestellt werden. Einen vollständigen Leitfaden von Commvault finden Sie auf der Dokumentationswebsite zu [Azure Stack Hub](https://documentation.commvault.com/commvault/v11/article?p=86486.htm). Als Referenz hat Commvault außerdem eine [vollständige Liste der Funktionen für Microsoft Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm) veröffentlicht.

## <a name="deploy-from-azure-stack-hub-marketplace"></a>Bereitstellen über den Azure Stack Hub-Marketplace

Commvault veröffentlicht ein BYOL-Image (Bring Your Own License, Verwendung Ihrer eigenen Lizenz) im Azure Marketplace und aktiviert das Image für die Syndikation in Azure Stack Hub. Die mindestens erforderliche Version zum Sichern von VMs in Azure Stack ist SP16. Wenn Sie beabsichtigen, ein virtuelles Gerät zu verwenden, stellen Sie sicher, dass Sie dieses auf SP16 (das aktuelle [langfristig unterstützte Release](https://documentation.commvault.com/commvault/v11/article?p=2617.htm)) oder SP17 (das neueste allgemein verfügbare Release) aktualisieren.

| Cloud        | Version | Verfügbar für Syndikation | Nächste Aktualisierung |
|--------------|---------|---------------------------|-------------|
| Azure, öffentlich | SP13    | Ja                       | TBD         |
| Azure Gov    | SP13    | TBD                       | TBD         |

## <a name="download-from-azure-marketplace"></a>Herunterladen aus dem Azure Marketplace

Azure Stack Hub-Operators können Elemente für verbundene und getrennte Umgebungen in den lokalen Azure Stack-Marketplace herunterladen. In einer verbundenen Umgebung kann der Operator die Liste der verfügbaren Elemente durchsuchen, die aus Azure hinzugefügt werden können:

![Hinzufügen aus Azure](media/azure-stack-commvault-offer-tzl/add-from-azure.png)

## <a name="upload-and-publish-manually"></a>Manuelles Hochladen und Veröffentlichen

In getrennten Umgebungen muss das Element aus Azure heruntergeladen und dann manuell in Azure Stack Hub hochgeladen werden. Weitere Informationen finden Sie im [vollständigen Leitfaden für verbundene und getrennte Umgebungen](../../operator/azure-stack-download-azure-marketplace-item.md).

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

- Externes Bereitstellen in Azure Stack Hub
- Bereitstellen als virtuelles Gerät in Azure Stack Hub
- Datenträgerbibliothek im Vergleich zu Cloudbibliothek
- Überlegungen zur Sichtverbindung im Netzwerk
- Isolation der Abonnementebene

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Schützen von IaaS-VMs finden Sie unter „Schützen von VMs in Azure Stack Hub“.
