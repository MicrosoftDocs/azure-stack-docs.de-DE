---
title: Einführung in Azure Stack-Netzwerke | Microsoft-Dokumentation
description: Informationen zu Azure Stack-Netzwerken
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e47079b100487331434a201a05bbcae7645de4f1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824160"
---
# <a name="introduction-to-azure-stack-networking"></a>Einführung in Azure Stack-Netzwerke

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack bietet verschiedene Arten von Netzwerkfunktionen, die einzeln oder zusammen verwendet werden können:

- **Konnektivität zwischen Azure Stack-Ressourcen**  
    Stellen Sie in einem sicheren und privaten virtuellen Netzwerk in der Cloud eine Verbindung zwischen Azure-Ressourcen her.
- **Internetkonnektivität**  
    Kommunizieren Sie mit und von Azure Stack-Ressourcen über das Internet.
- **Lokale Konnektivität**  
    Stellen Sie entweder unter Verwendung eines virtuellen privaten Netzwerks (VPN) über das Internet oder über eine dedizierte Verbindung mit Azure Stack eine Verbindung zwischen einem lokalen Netzwerk und Azure Stack-Ressourcen her.
- **Lastenausgleich und Datenverkehrsrichtung**  
    Führen Sie einen Lastenausgleich für Datenverkehr durch, der für Server am gleichen Standort bestimmt ist, und leiten Sie Datenverkehr an Server an anderen Standorten weiter.
- **Sicherheit**  
    Filtern Sie den Netzwerkdatenverkehr zwischen Netzwerksubnetzen oder einzelnen virtuellen Computern (VMs).
- **Routing**  
    Verwenden Sie Standardroutingfunktionen, oder steuern Sie das gesamte Routing zwischen Azure Stack und lokalen Ressourcen.
- **Verwaltbarkeit**  
    Überwachen und verwalten Sie Ihre Azure Stack-Netzwerkressourcen.
- **Bereitstellungs- und Konfigurationstools**  
    Verwenden Sie ein webbasiertes Portal oder plattformübergreifende Befehlszeilentools zum Bereitstellen und Konfigurieren von Netzwerkressourcen.


## <a name="next-steps"></a>Nächste Schritte

* [Überlegungen zu Azure Stack-Netzwerken](azure-stack-network-differences.md)