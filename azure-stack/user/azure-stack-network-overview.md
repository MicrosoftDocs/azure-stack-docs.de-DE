---
title: Einführung in Azure Stack Hub-Netzwerke
description: Informationen zu Azure Stack Hub-Netzwerken
author: mattbriggs
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 2cb8ede31a91ba05102c55591a4942f512bce0c8
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674471"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Einführung in Azure Stack Hub-Netzwerke

Azure Stack Hub bietet verschiedene Arten von Netzwerkfunktionen, die einzeln oder zusammen verwendet werden können:

- **Konnektivität zwischen Azure Stack Hub-Ressourcen**  
    Stellen Sie in einem sicheren und privaten virtuellen Netzwerk in der Cloud eine Verbindung zwischen Azure-Ressourcen her.
- **Internetkonnektivität**  
    Kommunizieren Sie mit und von Azure Stack Hub-Ressourcen über das Internet.
- **Lokale Konnektivität**  
    Stellen Sie entweder unter Verwendung eines virtuellen privaten Netzwerks (VPN) über das Internet oder über eine dedizierte Verbindung mit Azure Stack Hub eine Verbindung zwischen einem lokalen Netzwerk und Azure Stack Hub-Ressourcen her. 
    > [!IMPORTANT]
    > Sie müssen eine VPN- oder öffentliche IP-Verbindung erstellen, um auf lokale Ressourcen zugreifen zu können.
- **Lastenausgleich und Datenverkehrsrichtung**  
    Führen Sie einen Lastenausgleich für Datenverkehr durch, der für Server am gleichen Standort bestimmt ist, und leiten Sie Datenverkehr an Server an anderen Standorten weiter.
- **Security**  
    Filtern Sie den Netzwerkdatenverkehr zwischen Netzwerksubnetzen oder einzelnen virtuellen Computern (VMs).
- **Routing**  
    Verwenden Sie Standardroutingfunktionen, oder steuern Sie das gesamte Routing zwischen Azure Stack Hub und lokalen Ressourcen.
- **Verwaltbarkeit**  
    Überwachen und verwalten Sie Ihre Azure Stack Hub-Netzwerkressourcen.
- **Bereitstellungs- und Konfigurationstools**  
    Verwenden Sie ein webbasiertes Portal oder plattformübergreifende Befehlszeilentools zum Bereitstellen und Konfigurieren von Netzwerkressourcen.


## <a name="next-steps"></a>Nächste Schritte

* [Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)