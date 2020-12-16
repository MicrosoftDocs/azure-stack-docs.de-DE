---
title: 'Azure Stack Hub: Bekannte Probleme bei Azure Stack Hub Ruggedized'
description: Enthält Informationen zu bekannten Problemen in Releases von Azure Stack Hub Ruggedized.
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
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: 4b75024a1ff747d39f4f2194d0d86c3882475dee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941140"
---
# <a name="known-issues-in-azure-stack-hub-ruggedized"></a>Bekannte Probleme in Azure Stack Hub Ruggedized 

In diesem Artikel sind die bekannten Probleme aufgelistet, die es in Azure Stack Hub Ruggedized gibt. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

## <a name="update"></a>Aktualisieren

Informationen zu bekannten Problemen beim Aktualisieren von Azure Stack Hub finden Sie unter [Problembehandlung von Azure Stack Hub-Updates](../operator/azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Fehler bei der Installation des Pakets „Microsoft.AzureStack.Compute.Installer“ auf der Zertifizierungsstellen-VM durch das Update

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Während des Updates richtet ein Prozess eine Sperre für neue Inhalte ein, die auf die Zertifizierungsstellen-VM kopiert werden müssen. Die Sperre wird aufgehoben, wenn das Update fehlschlägt.
- Abhilfe: Fahren Sie mit dem Update fort.
- Häufigkeit: Selten

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Die DenyAllOutbound-Regel kann nicht erstellt werden.

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der VM-Erstellung kann in einer NSG keine explizite Regel vom Typ **DenyAllOutbound** für das Internet erstellt werden, da dadurch die erforderliche Kommunikation für den Abschluss der VM-Bereitstellung verhindert wird. Außerdem werden die beiden wichtigen IP-Adressen verweigert, die für die Bereitstellung von VMs erforderlich sind: DHCP-IP: 169.254.169.254 und DNS-IP: 168.63.129.16

- Abhilfe: Lassen Sie bei der VM-Erstellung ausgehenden Datenverkehr für das Internet zu, und ändern Sie die NSG nach Abschluss der VM-Erstellung, um den erforderlichen Datenverkehr zu blockieren.
- Häufigkeit: Allgemein

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

#### <a name="documentation"></a>Dokumentation

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Dokumentationslinks auf der Übersichtsseite des virtuellen Netzwerkgateways verlinken mit Azure-spezifischer Dokumentation anstelle von Azure Stack Hub. Verwenden Sie die folgenden Links für die Azure Stack Hub-Dokumentation:

  - [Gateway-SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Hoch verfügbare Verbindungen](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurieren von BGP in Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-Verbindungen](../operator/azure-stack-connect-expressroute.md)
  - [Angeben von benutzerdefinierten IPsec/IKE-Richtlinien](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer leitet Datenverkehr in bestimmten Szenarien an eine Back-End-VM weiter

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen. 
- Ursache: Beim Aktivieren der **Sitzungsaffinität** in einem Lastenausgleich verwendet der 2-Tupel-Hash die PA-IP-Adresse (physische IP-Adresse) anstelle der privaten IP-Adressen, die den VMs zugewiesen sind. In Szenarien, in denen Datenverkehr, der an den Lastenausgleich weitergeleitet wird, über ein VPN eingeht oder alle virtuellen Clientcomputer (Quell-IPs) sich im gleichen Knoten befinden und die Sitzungsaffinität aktiviert ist, wird der gesamte Datenverkehr an eine Back-End-VM geleitet.
- Häufigkeit: Allgemein
