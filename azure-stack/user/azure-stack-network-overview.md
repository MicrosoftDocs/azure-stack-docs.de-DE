---
title: Einführung in Azure Stack-Netzwerke | Microsoft-Dokumentation
description: Informationen zu Azure Stack-Netzwerken
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7eb57c38de01c3ad7de34b96b4c6baf7a4c14ad8
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782616"
---
# <a name="introduction-to-azure-stack-networking"></a>Einführung in Azure Stack-Netzwerke

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack bietet eine Vielzahl von Netzwerkfunktionen, die einzeln oder zusammen verwendet werden können:

- **Konnektivität zwischen Azure Stack-Ressourcen**  
    Stellen Sie in einem sicheren, privaten virtuellen Netzwerk in der Cloud eine Verbindung zwischen Azure-Ressourcen her.
- **Internetkonnektivität**  
    Kommunizieren Sie mit und von Azure Stack-Ressourcen über das Internet.
- **Lokale Konnektivität**  
    Stellen Sie eine Verbindung zwischen einem lokalen Netzwerk und Azure Stack-Ressourcen her – entweder unter Verwendung eines virtuellen privaten Netzwerks (VPN) über das Internet oder über eine dedizierte Verbindung mit Azure Stack.
- **Lastenausgleich und Datenverkehrsrichtung**  
    Führen Sie einen Lastenausgleich für Datenverkehr durch, der für Server am gleichen Standort bestimmt ist, und leiten Sie Datenverkehr an Server an anderen Standorten weiter.
- **Sicherheit**  
    Filtern Sie den Netzwerkdatenverkehr zwischen Netzwerksubnetzen oder einzelnen virtuellen Computern (virtual machines, VMs).
- **Routing**  
    Verwenden Sie Standardroutingfunktionen, oder steuern Sie das gesamte Routing zwischen Azure Stack und lokalen Ressourcen.
- **Verwaltbarkeit**  
    Überwachen und verwalten Sie Ihre Azure Stack-Netzwerkressourcen.
- **Bereitstellungs- und Konfigurationstools**  
    Verwenden Sie ein webbasiertes Portal oder plattformübergreifende Befehlszeilentools zum Bereitstellen und Konfigurieren von Netzwerkressourcen.


## <a name="next-steps"></a>Nächste Schritte

* [Überlegungen zu Azure Stack-Netzwerken](azure-stack-network-differences.md)