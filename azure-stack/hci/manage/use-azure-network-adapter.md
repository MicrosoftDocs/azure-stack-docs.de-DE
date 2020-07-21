---
title: Verwenden des Azure-Netzwerkadapters, um einen Server mit einem Azure Virtual Network zu verbinden
description: Dieser Artikel enthält Anforderungen und Schritte zur Verwendung des Azure-Netzwerkadapters, um einen Server mit einem Azure Virtual Network zu verbinden.
ms.topic: article
author: thomasmaurer
ms.author: thmaure
ms.date: 07/14/2020
ms.openlocfilehash: 1bec00f972cd0cfb1b56aeae831dd1ba2914ab33
ms.sourcegitcommit: 2be3dd5419b0d003a9598a42541ebb1d251aea3d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2020
ms.locfileid: "86393629"
---
# <a name="use-azure-network-adapter-to-connect-a-server-to-an-azure-virtual-network"></a>Verwenden des Azure-Netzwerkadapters, um einen Server mit einem Azure Virtual Network zu verbinden

>Gilt für: Windows Server 2019, Windows Server 2016, Windows Server 2012 R2

Viele Workloads, die lokal und in Multi-Cloud-Umgebungen ausgeführt werden, erfordern Verbindungen zu virtuellen Computern (VMs), die in Microsoft Azure ausgeführt werden. Sie haben mehrere Möglichkeiten, einen Server mit einem Azure Virtual Network zu verbinden, beispielsweise Site-to-Site-VPN, Azure Express Route und Point-to-Site-VPN.

Windows Admin Center und der Azure-Netzwerkadapter bieten die Möglichkeit, den Server mit nur einem Mausklick über eine [Point-to-Site-VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)-Verbindung mit Ihrem virtuellen Netzwerk zu verbinden. Der Prozess automatisiert die Konfiguration des virtuellen Netzwerkgateways und des lokalen VPN-Clients.

## <a name="when-to-use-azure-network-adapter"></a>Wann ein Azure-Netzwerkadapter verwendet werden sollte
Point-to-Site-VPN-Verbindungen für Azure-Netzwerkadapter sind nützlich, wenn Sie von einem Remotestandort (beispielsweise einer Zweigniederlassung einem Geschäft oder einem anderen Standort) eine Verbindung mit Ihrem virtuellen Netzwerk herstellen möchten. Sie können einen Azure-Netzwerkadapter auch anstelle eines Site-to-Site-VPNs verwenden, wenn Sie wenige Server eine Verbindung zu einem virtuellen Netzwerk benötigen. Für Azure-Netzwerkadapterverbindungen ist kein VPN-Gerät und auch keine öffentliche IP-Adresse erforderlich.

### <a name="requirements"></a>Requirements (Anforderungen)
Die Verwendung eines Azure-Netzwerkadapters für die Verbindung mit einem virtuellen Netzwerk erfordert Folgendes:
- Ein Azure-Konto mit mindestens einem aktiven Abonnement.
- Ein vorhandenes virtuelles Netzwerk.
- Internetzugriff für die Zielserver, die Sie mit dem virtuellen Azure-Netzwerk verbinden möchten.
- Eine Windows Admin Center-Verbindung zu Azure.
  Weitere Informationen finden Sie unter [Konfigurieren der Azure-Integration](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- Die neueste Version von Windows Admin Center.
  Weitere Informationen finden Sie unter [Windows Admin Center](https://www.microsoft.com/windows-server/windows-admin-center).

> [!NOTE]
> Es ist nicht erforderlich, Windows Admin Center auf dem Server zu installieren, den Sie mit Azure verbinden möchten. In einem Szenario mit einem einzigen Server können Sie dies jedoch tun.

## <a name="add-an-azure-network-adapter-to-a-server"></a>Hinzufügen eines Azure-Netzwerkadapters zu einem Server
Um einen Azure-Netzwerkadapter zu konfigurieren, gehen Sie zu dessen Netzwerkerweiterung im Windows Admin Center.

Im Windows Admin Center:
1. Navigieren Sie zu dem Server, der die VMs hostet, die Sie zum Azure-Netzwerkadapter hinzufügen möchten.
1. Wählen Sie unter **Tools** die Option **Netzwerke** aus.
1. Wählen Sie **Azure-Netzwerkadapter hinzufügen** aus.
1. Geben Sie im Bereich **Azure-Netzwerkadapter hinzufügen** die folgenden erforderlichen Informationen ein, und wählen Sie dann **Erstellen** aus:
    - **Abonnement**
    - **Location**
    - **Virtual Network**
    - **Gatewaysubnetz** (wenn nicht vorhanden)
    - **Gateway-SKU** (wenn nicht vorhanden)
    - **Clientadressraum**

        Der Clientadresspool ist ein Bereich privater IP-Adressen, die Sie angeben. Den Clients, die eine Verbindung über ein P2S-VPN herstellen, wird dynamisch eine IP-Adresse aus diesem Bereich zugewiesen. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem virtuellen Netzwerk überschneiden, mit dem Sie Verbindungen herstellen möchten. Wir empfehlen die Verwendung von IP-Adressen, die in den für private Netzwerke vorgesehenen Bereichen liegen (10.x.x.x, 192.168.x.x oder 172.16.0.0 bis 172.31.255.255).

    - **Authentifizierungszertifikat**

        Azure verwendet Zertifikate zum Authentifizieren von Clients verwendet, die eine Verbindung mit einem virtuellen Netzwerk über eine Point-to-Site-VPN-Verbindung herstellen. Die Informationen des öffentlichen Schlüssels des Stammzertifikats werden in Azure hochgeladen. Das Stammzertifikat wird dann von Azure als „vertrauenswürdig“ für eine Point-to-Site-Verbindung mit dem virtuellen Netzwerk betrachtet. Das Clientzertifikat muss über das vertrauenswürdige Stammzertifikat generiert und auf dem Clientserver installiert werden. Mit dem Clientzertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem virtuellen Netzwerk initiiert.
    
        Weitere Informationen finden Sie unter [Konfigurieren einer Point-to-Site-VPN-Verbindung mit einem VNet unter Verwendung der nativen Azure-Zertifikatauthentifizierung über das Azure-Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) im Abschnitt „Konfigurieren eines Authentifizierungstyps“.

    :::image type="content" source="media/azure-network-adapter/add-azure-network-adapter.png" alt-text="Der Bereich „Azure-Netzwerkadapter hinzufügen“ im Windows Admin Center.":::

> [!NOTE]
> Netzwerkappliances wie VPN Gateway und Application Gateway, die innerhalb eines virtuellen Netzwerks ausgeführt werden, sind mit zusätzlichen Kosten verbunden. Weitere Informationen finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network/).

Wenn es kein vorhandenes Azure Virtual Network-Gateway gibt, wird eines von Windows Admin Center für Sie erstellt. Dieser Setupvorgang kann bis zu 25 Minuten dauern. Nachdem der Azure-Netzwerkadapter erstellt wurde, können Sie direkt von Ihrem Server aus auf VMs im virtuellen Netzwerk zugreifen.

Wenn Sie die Konnektivität nicht mehr benötigen, wählen Sie unter **Netzwerke** den Azure-Netzwerkadapter aus, den Sie trennen möchten, wählen Sie im oberen Menü **Trennen** und dann im Popupfenster mit der **„VPN trennen“-Bestätigung** die Option **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Virtual Network finden Sie auch unter:

- [Azure Virtual Network – häufig gestellte Fragen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
