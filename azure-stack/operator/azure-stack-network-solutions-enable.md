---
title: Anbieten einer Netzwerklösung in Azure Stack Hub mit Fortinet FortiGate | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Netzwerklösung in Azure Stack Hub mit Fortinet FortiGate aktivieren.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 356c6825da9ef60737371c90700b79a8a8a201ea
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810783"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Anbieten einer Netzwerklösung in Azure Stack Hub mit Fortinet FortiGate

Sie können Ihrem Azure Stack Hub-Marketplace „FortiGate Next-Generation Firewall“ hinzufügen. Mit FortiGate können Ihre Benutzer Netzwerklösungen erstellen, z. B. ein VPN (Virtual Private Network) für Azure Stack Hub und VNET-Peering. Mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) wird der Fluss des Netzwerkdatenverkehrs aus einem Umkreisnetzwerk in andere Netzwerke oder Subnetze gesteuert. 

Weitere Informationen zu FortiGate im Azure Marketplace finden Sie unter [FortiGate Next-Generation Firewall – Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Herunterladen der erforderlichen Elemente aus dem Azure Stack Hub-Marketplace

1.  Öffnen Sie das Azure Stack Hub-Administratorportal.

2.  Wählen Sie **Marketplace management** (Marketplace-Verwaltung) und dann **Add from Azure** (Aus Azure hinzufügen) aus.

3. Geben Sie im Suchfeld `Forti` ein, doppelklicken Sie, und wählen Sie **Herunterladen** aus, um die aktuellen verfügbaren Versionen der folgenden Elemente zu erhalten: 
    - Fortinet FortiGate-VM For Azure BYOL
    - FortiGate NGFW – Single VM Deployment (BYOL)

    ![Azure Stack Hub FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Warten Sie, bis für Ihre Marketplace-Elemente der Status **Heruntergeladen** angezeigt wird. Es kann einige Minuten dauern, bis die Elemente heruntergeladen wurden.

    ![Azure Stack Hub FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Nächste Schritte

[Einrichten eines VPN-Gateways für Azure Stack Hub per FortiGate NVA](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Verbinden von zwei VNETs per Peering](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Einrichten einer VNET-zu-VNET-Verbindung per Fortinet FortiGate NVA](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
