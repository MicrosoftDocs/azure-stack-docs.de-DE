---
title: Peering virtueller Netzwerke in Azure Stack Hub
description: Erfahren Sie, wie Sie Peering virtueller Netzwerke verwenden, um virtuelle Netzwerke in Azure Stack Hub miteinander zu verbinden.
author: sethmanheim
ms.author: sethm
ms.date: 11/11/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 16d7701161b8ec8c16aa3caecf5d5e291d6c0e99
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548717"
---
# <a name="virtual-network-peering"></a>Peering virtueller Netzwerke

Peering virtueller Netzwerke ermöglicht es Ihnen, virtuelle Netzwerke in einer Azure Stack Hub-Umgebung nahtlos miteinander zu verbinden. Die virtuellen Netzwerke werden für Verbindungszwecke als einzelnes Element angezeigt. Für den Datenverkehr zwischen virtuellen Computern wird die zugrunde liegende SDN-Infrastruktur verwendet. Analog zum Datenverkehr zwischen virtuellen Computern im selben Netzwerk wird der Datenverkehr nur über das private Azure Stack Hub-Netzwerk geleitet.

Azure Stack Hub unterstützt kein globales Peering, da das Konzept der „Regionen“ nicht gilt.

Die Verwendung von Peering virtueller Netzwerke bietet folgende Vorteile:

- Niedrige Latenz, Verbindung mit hoher Bandbreite zwischen Ressourcen in unterschiedlichen virtuellen Netzwerken
- Die Möglichkeit, dass Ressourcen in einem virtuellen Netzwerk mit Ressourcen in einem anderen virtuellen Netzwerk kommunizieren können
- Die Möglichkeit, Daten zwischen virtuellen Netzwerken über verschiedene Abonnements und Azure Active Directory-Mandanten hinweg zu übertragen
- Keine Ausfallzeiten für Ressourcen in beiden virtuellen Netzwerken beim Erstellen des Peerings oder nachdem das Peering erstellt wurde

Netzwerkdatenverkehr zwischen virtuellen Netzwerken, die mittels Peering verknüpft sind, ist privat. Datenverkehr zwischen virtuellen Netzwerken verbleibt auf der Infrastrukturebene. Die Kommunikation zwischen virtuellen Netzwerken kommt ohne öffentliches Internet, Gateways oder Verschlüsselung aus.

## <a name="connectivity"></a>Konnektivität

Für virtuelle Netzwerke mit Peering kann für Ressourcen in einem der virtuellen Netzwerke eine direkte Verbindung mit den Ressourcen im virtuellen Peernetzwerk hergestellt werden.

Die Netzwerklatenz zwischen virtuellen Computern in per Peering verknüpften virtuellen Netzwerken in der gleichen Region entspricht der Netzwerklatenz in einem einzelnen virtuellen Netzwerk. Der Netzwerkdurchsatz basiert auf der Bandbreite, die für den virtuellen Computer proportional zu seiner Größe zulässig ist. Im Peering bestehen keine weiteren Bandbreiteneinschränkungen.

Der Datenverkehr zwischen virtuellen Computern in mittels Peering verknüpften virtuellen Netzwerken wird nicht über ein Gateway oder das öffentliche Internet, sondern direkt über die SDN-Ebene (Schicht) geleitet.

Sie können Netzwerksicherheitsgruppen in beiden virtuellen Netzwerken anwenden, um den Zugriff auf andere virtuelle Netzwerke oder Subnetze zu blockieren. Beim Konfigurieren des VNET-Peerings können Sie die Regeln für Netzwerksicherheitsgruppen zwischen den virtuellen Netzwerken öffnen oder schließen. Wenn Sie sich für das Öffnen der vollständigen Konnektivität zwischen den mittels Peering verknüpften virtuellen Netzwerken entscheiden, können Sie Netzwerksicherheitsgruppen verwenden, um den spezifischen Zugriff jeweils zu blockieren oder zu verweigern. „Vollständige Konnektivität“ ist die Standardoption. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Sicherheitsgruppen](/azure/virtual-network/security-overview).

## <a name="service-chaining"></a>Dienstverkettung

Eine Dienstverkettung ermöglicht es, Datenverkehr über benutzerdefinierte Routingtabellen aus einem virtuellen Netzwerk an ein virtuelles Gerät oder Gateway in einem Netzwerk mit Peering zu leiten.

Um die Dienstverkettung zu aktivieren, konfigurieren Sie benutzerdefinierte Routen, die auf virtuelle Computer in mittels Peering verknüpften virtuellen Netzwerken als IP-Adresse für den *nächsten Hop* verweisen.

Sie können auch *Hub-and-Spoke*-Netzwerke bereitstellen, in denen das virtuelle Hubnetzwerk Infrastrukturkomponenten wie etwa ein virtuelles Netzwerkgerät oder ein VPN-Gateway hostet. Alle virtuellen Spoke-Netzwerke können dann mittels Peering mit dem virtuellen Hubnetzwerk verknüpft werden. Der Datenverkehr durchläuft virtuelle Netzwerkgeräte oder VPN-Gateways im virtuellen Hubnetzwerk.

Mit Peering virtueller Netzwerke kann der nächste Hop einer benutzerdefinierten Route die IP-Adresse eines virtuellen Computers im mittels Peering verknüpften virtuellen Netzwerk sein. Für weitere Informationen zu benutzerdefinierten Routen können Sie sich die [Übersicht zu benutzerdefinierten Routen](/azure/virtual-network/virtual-networks-udr-overview#user-defined) ansehen. Informationen zum Erstellen einer Hub-and-Spoke-Netzwerktopologie finden Sie unter [Hub-and-Spoke-Netzwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways und lokale Konnektivität

Jedes virtuelle Netzwerk, einschließlich eines mittels Peering verknüpften virtuellen Netzwerks, kann ein eigenes Gateway besitzen. Ein virtuelles Netzwerk kann sein Gateway verwenden, um eine Verbindung mit einem lokalen Netzwerk herzustellen. Weitere Informationen finden Sie in der [VPN Gateway-Dokumentation](/azure/vpn-gateway/).

Sie können auch das Gateway im mittels Peering verknüpften virtuellen Netzwerk als Transitpunkt für ein lokales Netzwerk konfigurieren. In diesem Fall kann das virtuelle Netzwerk, das ein Remotegateway verwendet, kein eigenes Gateway besitzen. Ein virtuelles Netzwerk hat nur ein Gateway. Bei diesem Gateway handelt es sich um ein lokales Gateway oder ein Remotegateway im mittels Peering verknüpften virtuellen Netzwerk, wie in der folgenden Abbildung zu sehen ist:

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="VPN-Gatewaytopologie":::

Beachten Sie, dass im VPN-Gateway ein **Connection**-Objekt erstellt werden muss, bevor die **UseRemoteGateways**-Optionen im Peering aktiviert werden.

## <a name="virtual-network-peering-configuration"></a>Konfigurieren des Peerings virtueller Netzwerke

**Zugriff auf virtuelles Netzwerk zulassen:** Durch die Aktivierung der Kommunikation zwischen virtuellen Netzwerken können Ressourcen, die mit einem der beiden virtuellen Netzwerke verbunden sind, mit derselben Bandbreite und Latenz kommunizieren, als ob sie mit demselben virtuellen Netzwerk verbunden wären. Die gesamte Kommunikation zwischen Ressourcen in den beiden virtuellen Netzwerken wird über die interne SDN-Ebene geleitet.  

Ein Grund, Netzwerkzugriff nicht zu aktivieren, könnte ein Szenario sein, in dem Sie ein virtuelles Netzwerk mittels Peering mit einem anderen virtuellen Netzwerk verknüpft haben, jedoch die Option haben möchten, den Datenverkehr zwischen den beiden virtuellen Netzwerken gelegentlich zu deaktivieren. Möglicherweise finden Sie Aktivieren/Deaktivieren bequemer als das Löschen und Neuerstellen von Peerings. Wenn diese Einstellung deaktiviert ist, wird kein Datenverkehr zwischen den mittels Peering verknüpften virtuellen Netzwerken weitergeleitet.

**Weitergeleiteten Datenverkehr zulassen**: Aktivieren Sie dieses Kontrollkästchen, um zuzulassen, dass der von einem virtuellen Netzwerkgerät *weitergeleitete* Datenverkehr (der nicht von dem virtuellen Netzwerk generiert wird) mittels Peering über dieses virtuelle Netzwerk erfolgt. Sehen Sie sich beispielsweise die drei virtuellen Netzwerke mit dem Namen „Spoke1“, „Spoke2“ und „Hub“ an. Die einzelnen Spokes und der Hub des virtuellen Netzwerks sind mittels Peering miteinander verknüpft, jedoch nicht die Spokes des virtuellen Netzwerks untereinander. Im Hub des virtuellen Netzwerks wird eine virtuelle Netzwerkappliance bereitgestellt, während bei den einzelnen Spokes des virtuellen Netzwerks benutzerdefinierte Routen angewendet werden, die Datenverkehr zwischen den Subnetzen über die virtuelle Netzwerkappliance weiterleiten. Wenn dieses Kontrollkästchen für das Peering zwischen den einzelnen Spokes und dem Hub des virtuellen Netzwerks nicht aktiviert ist, fließt kein Datenverkehr zwischen den Spokes des virtuellen Netzwerks, da der Hub den Datenverkehr zwischen den virtuellen Netzwerken nicht weiterleitet. Durch die Aktivierung dieser Funktion wird zwar die Weiterleitung des Datenverkehrs mittels Peering ermöglicht, es werden jedoch keine benutzerdefinierten Routen oder virtuellen Netzwerkgeräte erstellt. Benutzerdefinierte Routen und virtuelle Netzwerkgeräte werden separat erstellt. Weitere Informationen hierzu finden Sie unter [Benutzerdefinierte Routen](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Sie müssen diese Einstellung nicht überprüfen, wenn der Datenverkehr zwischen virtuellen Netzwerken über ein VPN-Gateway weitergeleitet wird.

**Gatewaytransit zulassen**: Aktivieren Sie dieses Kontrollkästchen, wenn ein Gateway für virtuelle Netzwerke mit diesem virtuellen Netzwerk verbunden ist und Sie zulassen möchten, dass Datenverkehr von mittels Peering verknüpften virtuellen Netzwerken über das Gateway geleitet werden soll. Dieses virtuelle Netzwerk kann z.B. über ein Gateway für virtuelle Netzwerke mit einem lokalen Netzwerk verbunden werden. Wird dieses Kontrollkästchen aktiviert, kann Datenverkehr aus dem mittels Peering verknüpften virtuellen Netzwerk über das mit diesem virtuellen Netzwerk verbundene Gateway an das lokale Netzwerk weitergeleitet werden. Wenn Sie dieses Kontrollkästchen aktivieren, darf für das mittels Peering verknüpfte virtuelle Netzwerk kein Gateway konfiguriert sein. Wenn Sie das Peering vom anderen virtuellen Netzwerk mit diesem virtuellen Netzwerk einrichten, muss für das mittels Peering verknüpfte virtuelle Netzwerk das Kontrollkästchen **Remotegateways verwenden** aktiviert sein. Wenn Sie dieses Kontrollkästchen deaktiviert belassen (die Standardeinstellung), wird der Datenverkehr trotzdem von dem mittels Peering verknüpften Netzwerk an dieses virtuelle Netzwerk weitergeleitet, kann jedoch nicht über ein mit diesem virtuellen Netzwerk verbundenes Gateway für virtuelle Netzwerke erfolgen.

**Remotegateways verwenden**: Aktivieren Sie dieses Kontrollkästchen, wenn Sie zulassen möchten, dass Datenverkehr von diesen virtuellen Netzwerken über ein Gateway für virtuelle Netzwerke geleitet werden soll, das mit dem mittels Peering verknüpften virtuellen Netzwerk verbunden ist. Beispielsweise ist an das virtuelle Netzwerk, mit dem Sie mittels Peering eine Verbindung herstellen, ein VPN-Gateway verbunden, dass Kommunikation mit einem lokalen Netzwerk ermöglicht. Wird dieses Kontrollkästchen aktiviert, kann Datenverkehr aus diesem virtuellen Netzwerk über das verbundene VPN-Gateway an dieses mittels Peering verknüpfte virtuelle Netzwerk weitergeleitet werden. Wenn Sie dieses Kontrollkästchen aktivieren, muss ein Gateway für virtuelle Netzwerke mit dem mittels Peering verknüpften virtuellen Netzwerk verbunden sein. Zudem muss für das Netzwerk das Kontrollkästchen **Gatewaytransit zulassen** aktiviert sein. Wenn Sie dieses Kontrollkästchen deaktiviert belassen (die Standardeinstellung), kann der Datenverkehr trotzdem von dem mittels Peering verknüpften Netzwerk an dieses virtuelle Netzwerk weitergeleitet werden, kann jedoch nicht über ein mit diesem virtuellen Netzwerk verbundenes Gateway für virtuelle Netzwerke erfolgen.

Wenn bereits ein Gateway in Ihrem virtuellen Netzwerk konfiguriert ist, können keine Remotegateways verwendet werden.

### <a name="permissions"></a>Berechtigungen

Stellen Sie sicher, dass den Konten, wenn Sie Peerings mit VNETs in unterschiedlichen Abonnements und Azure AD-Mandanten erstellen, die Rolle **Mitwirkender** zugewiesen ist. Außerdem gibt es keine Benutzeroberflächenfunktionalität für ein Peering zwischen unterschiedlichen Azure AD-Mandanten. Sie können Azure CLI und PowerShell verwenden, um die Peerings zu erstellen.

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>Peering virtueller Netzwerke – häufig gestellte Fragen

### <a name="what-is-virtual-network-peering"></a>Was ist Peering virtueller Netzwerke?

Peering virtueller Netzwerke ermöglicht Ihnen, virtuelle Netzwerke miteinander zu verbinden. Über eine VNET-Peeringverbindung zwischen virtuellen Netzwerken können Sie Datenverkehr zwischen diesen privat über IPv4-Adressen weiterleiten. Virtuelle Computer in den mittels Peering verknüpften VNETs können miteinander kommunizieren, als ob sie sich im gleichen Netzwerks befinden. VNET-Peeringverbindungen können auch über mehrere Abonnements hinweg erstellt werden.

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>Unterstützt Azure Stack Hub globales VNET-Peering?

Azure Stack Hub unterstützt kein globales Peering, da das Konzept der „Regionen“ nicht gilt.

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>Ab welchem Azure Stack Hub-Update ist Peering virtueller Netzwerke verfügbar?

Peering virtueller Netzwerke ist in Azure Stack Hub ab dem Update 2008 verfügbar.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>Kann ich mein virtuelles Netzwerk in Azure Stack Hub über Peering mit einem virtuellen Netzwerk in Azure verbinden?

Nein, Peering zwischen Azure und Azure Stack Hub wird zurzeit nicht unterstützt.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>Kann ich mein virtuelles Netzwerk in Azure Stack Hub1 über Peering mit einem virtuellen Netzwerk in Azure Stack Hub2 verbinden?

Nein, Peering kann nur zwischen virtuellen Netzwerken in einem Azure Stack Hub-System erstellt werden. Weitere Informationen dazu, wie zwei virtuelle Netzwerke aus unterschiedlichen Stempeln verbunden werden können, finden Sie unter [Einrichten einer VNET-zu-VNET-Verbindung in Azure Stack Hub per Fortinet FortiGate NVA](azure-stack-network-howto-vnet-to-vnet-stacks.md).

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kann ich Peering aktivieren, wenn meine virtuellen Netzwerke zu Abonnements in verschiedenen Azure Active Directory-Mandanten gehören?

Ja. Es ist möglich, VNET-Peering einzurichten, wenn Ihre Abonnements zu verschiedenen Azure Active Directory-Mandanten gehören. Dies kann mittels PowerShell oder CLI erfolgen. Das Portal wird noch nicht unterstützt.

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>Kann ich mein virtuelles Netzwerk über Peering mit einem virtuellen Netzwerk in einem anderen Abonnement verbinden?

Ja. Sie können virtuelle Netzwerke über Abonnements hinweg über Peering miteinander verbinden.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Gibt es Bandbreiteneinschränkungen für Peeringverbindungen?

Nein. Für Peering virtueller Netzwerke gibt es keine Bandbreiteneinschränkungen. Die Bandbreite wird nur durch die VM oder die Computeressource beschränkt.

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>Meine Verbindung für Peering virtueller Netzwerke hat den Status *Initiiert*. Warum kann ich keine Verbindung herstellen?

Wenn Ihre Peeringverbindung den Status **Initiiert** hat, bedeutet dies, dass Sie nur einen Link erstellt haben. Damit eine Verbindung erfolgreich hergestellt werden kann, muss ein bidirektionaler Link erstellt werden. Soll beispielsweise VNET A über Peering mit VNET B verbunden werden, müssen ein Link von VNET A zu VNET B und ein Link von VNET B zu VNET A erstellt werden. Nachdem beide Links erstellt sind, änder sich der Status in **Verbunden**.

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>Meine Verbindung für Peering virtueller Netzwerke hat den Status *Getrennt*. Warum kann ich keine Peeringverbindung herstellen?

Wenn Ihre Verbindung für Peering virtueller Netzwerke den Status **Getrennt** hat, bedeutet dies, dass einer der erstellen Links gelöscht wurde. Um die Peeringverbindung erneut herzustellen, löschen Sie den Link, und erstellen Sie ihn dann neu.

### <a name="is-virtual-network-peering-traffic-encrypted"></a>Wird Datenverkehr für Peering virtueller Netzwerke verschlüsselt?

Nein. Datenverkehr zwischen Ressourcen in per Peering verknüpften virtuellen Netzwerken ist privat und isoliert. Er verbleibt vollständig auf der SDN-Ebene (Schicht) des Azure Stack Hub-Systems.

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>Wenn ich VNET A über Peering mit VNET B und VNET B über Peering mit VNET C verbinde, besteht dann die Möglichkeit, VNET A und VNET C über Peering zu miteinander verbinden?

Nein. Transitives Peering wird nicht unterstützt. Sie müssen VNET A und VNET B über Peering verbinden.

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
- [Übersicht über benutzerdefinierte Routen](/azure/virtual-network/virtual-networks-udr-overview#user-defined)
- [Hub-Spoke-Netzwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
