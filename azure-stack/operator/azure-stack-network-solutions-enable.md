---
title: Anbieten einer Netzwerklösung in Azure Stack mit Fortinet FortiGate | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Netzwerklösung in Azure Stack mit Fortinet FortiGate aktivieren.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 27012c491054043f45004d76787538091864577c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962712"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>Anbieten einer Netzwerklösung in Azure Stack mit Fortinet FortiGate

Sie können Ihrem Azure Stack-Marketplace „FortiGate Next-Generation Firewall“ hinzufügen. Mit FortiGate können Ihre Benutzer Netzwerklösungen erstellen, z. B. ein VPN (Virtual Private Network) für Azure Stack und VNET-Peering. Mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) wird der Fluss des Netzwerkdatenverkehrs aus einem Umkreisnetzwerk in andere Netzwerke oder Subnetze gesteuert. 

Weitere Informationen zu FortiGate im Azure Marketplace finden Sie unter [FortiGate Next-Generation Firewall – Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-marketplace-items"></a>Herunterladen der erforderlichen Elemente aus dem Azure Stack-Marketplace

1.  Öffnen Sie das Azure Stack-Administratorportal.

2.  Wählen Sie **Marketplace management** (Marketplace-Verwaltung) und dann **Add from Azure** (Aus Azure hinzufügen) aus.

3. Geben Sie im Suchfeld `Forti` ein, doppelklicken Sie, und wählen Sie **Herunterladen** aus, um die aktuellen verfügbaren Versionen der folgenden Elemente zu erhalten: 
    - Fortinet FortiGate-VM For Azure BYOL
    - FortiGate NGFW – Single VM Deployment (BYOL)

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Warten Sie, bis für Ihre Marketplace-Elemente der Status **Heruntergeladen** angezeigt wird. Es kann einige Minuten dauern, bis die Elemente heruntergeladen wurden.

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Nächste Schritte

[Einrichten eines VPN-Gateways für Azure Stack per FortiGate NVA](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Verbinden von zwei VNETs per Peering](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Einrichten einer VNET-zu-VNET-Verbindung per Fortinet FortiGate NVA](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
