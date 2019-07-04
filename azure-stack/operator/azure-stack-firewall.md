---
title: Azure Stack-Firewallplanung für integrierte Azure Stack-Systeme | Microsoft-Dokumentation
description: Informationen zu Überlegungen der Azure Stack-Firewall für mit Azure verbundene Bereitstellungen von Azure Stack-Systemen mit mehreren Knoten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: abca6560e8644b201483001258542121fefb6b08
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131521"
---
# <a name="azure-stack-firewall-integration"></a>Azure Stack-Firewallintegration
Es empfiehlt sich, Azure Stack mit einem Firewallgerät zu schützen. Firewalls können bei der Verteidigung vor Dingen wie DDoS-Angriffen (Denial of Service), bei der Angriffserkennung sowie der Inhaltsuntersuchung helfen. Sie können allerdings auch zu einem Durchsatzengpass für Azure-Speicherdienste wie Blobs, Tabellen und Warteschlangen werden.

 Bei Verwendung eines getrennten Bereitstellungsmodus muss der AD FS-Endpunkt veröffentlicht werden. Weitere Informationen finden Sie im Artikel zur [Rechenzentrumsintegration mithilfe von Identitäten](azure-stack-integrate-identity.md).

Für die Endpunkte „Azure Resource Manager (Administrator)“, „Portal (Administrator)“ und „Schlüsseltresor (Administrator)“ ist nicht zwingend eine externe Veröffentlichung erforderlich. Als Dienstanbieter könnten Sie beispielsweise die Angriffsfläche verkleinern, indem Sie Azure Stack nur innerhalb Ihres Netzwerks (und nicht über das Internet) verwalten.

Bei einem Unternehmen kann das Unternehmensnetzwerk als externes Netzwerk fungieren. In diesem Szenario müssen Sie Endpunkte veröffentlichen, um Azure Stack über das Unternehmensnetzwerk bedienen zu können.

### <a name="network-address-translation"></a>Netzwerkadressenübersetzung
Die Netzwerkadressenübersetzung (Network Address Translation, NAT) stellt die empfohlene Methode dar, um virtuellen Bereitstellungscomputern (Deployment Virtual Machine, DVM) bei der Bereitstellung sowie ERCS-VMs (Emergency Recovery Console) oder privilegierten Endpunkten (PEP) bei der Registrierung und Problembehandlung Zugriff auf externe Ressourcen und das Internet zu ermöglichen.

Die NAT kann auch alternativ zu öffentlichen IP-Adressen im externen Netzwerk oder öffentlichen VIPs verwendet werden. Hiervon wird jedoch abgeraten, da diese die Benutzerfreundlichkeit des Mandanten verringert und die Komplexität erhöht. Eine Option wäre eine 1:1-NAT, die immer noch eine öffentliche IP pro Benutzer-IP im Pool erfordert. Eine andere Option ist eine Viele-zu-Eins-NAT (*:1), die eine NAT-Regel pro Benutzer-VIP für alle Ports erfordert, die ein Benutzer verwenden könnte.

Die Verwendung der NAT für öffentliche VIPs hat folgende Nachteile:
- Durch die NAT erhöht sich der Verwaltungsaufwand für Firewallregeln, da Benutzer ihre eigenen Endpunkte und ihre eigenen Veröffentlichungsregeln im SDN-Stapel (Software Defined Networking) steuern. Benutzer müssen sich zur Veröffentlichung ihrer VIPs sowie zur Aktualisierung der Portliste an den Azure Stack-Betreiber wenden.
- Die Verwendung der NAT beeinträchtigt zwar die Benutzerfreundlichkeit, gibt dem Betreiber jedoch die volle Kontrolle über Veröffentlichungsanforderungen.
- In Hybrid Cloud-Szenarien mit Azure wird die Einrichtung eines VPN-Tunnels zu einem Endpunkt mit NAT nicht unterstützt.

### <a name="ssl-decryption"></a>SSL-Entschlüsselung
Derzeit wird empfohlen, die SSL-Entschlüsselung bei allem Azure Stack-Datenverkehr zu deaktivieren. Wenn sie in zukünftigen Updates unterstützt wird, werden Anleitungen zum Aktivieren der SSL-Entschlüsselung für Azure Stack bereitgestellt.

## <a name="edge-firewall-scenario"></a>Szenario mit Edgefirewall
Azure Stack wird in einer Edgebereitstellung direkt hinter dem Edgerouter oder der Firewall bereitgestellt. In diesen Szenarien kann die Firewall dem Border-Gerät übergeordnet sein (Szenario 1) und sowohl Aktiv/Aktiv- als auch Aktiv/Passiv-Firewallkonfigurationen unterstützen oder als Border-Gerät fungieren (Szenario 2) und nur eine Aktiv/Aktiv-Firewallkonfiguration unterstützen, wobei ECMP (Equal Cost Multi Path) mit BGP oder statischem Routing für Failover erforderlich ist.

Öffentliche routingfähige IP-Adressen werden für den öffentlichen VIP-Pool aus dem externen Netzwerk zur Bereitstellungszeit angegeben. In einem Edgeszenario wird aus Sicherheitsgründen davon abgeraten, öffentliche routingfähige IP-Adressen in anderen Netzwerken zu verwenden. Dadurch erhalten Benutzer eine vollständig selbstgesteuerte Cloudumgebung – genau wie bei einer öffentlichen Cloud (z.B. Azure).  

![Beispiel für eine Azure Stack-Edgefirewall](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Firewallszenario mit Unternehmensintranet oder -umkreisnetzwerk
Bei einer Bereitstellung im Unternehmensintranet oder -umkreisnetzwerk wird Azure Stack in einer Firewall mit mehreren Zonen oder zwischen der Edgefirewall und der internen Unternehmensnetzwerkfirewall bereitgestellt. Dann wird wie im Folgenden beschrieben der Datenverkehr zwischen dem sicheren Umkreisnetzwerk (oder der DMZ) und unsicheren Zonen verteilt:

- **Sichere Zone**: Dies ist das interne Netzwerk, das interne oder routingfähige Unternehmens-IP-Adressen verwendet. Das sichere Netzwerk kann unterteilt werden, über ausgehenden Internetzugriff über die NAT in der Firewall verfügen und in der Regel von einem beliebige Ort aus in Ihrem Rechenzentrum über das interne Netzwerk aufgerufen werden. Alle Azure Stack-Netzwerke sollten sich in der sicheren Zone befinden, ausgenommen dem öffentlichen VIP-Pool im externen Netzwerk.
- **Umkreiszone**: Das Umkreisnetzwerk wird dort eingesetzt, wo in der Regel externe oder Internetanwendungen wie Webserver bereitgestellt werden. Es wird normalerweise durch eine Firewall überwacht, um Angriffe wie DDoS-Angriffe und Eindringversuche (Hacken) zu verhindern, lässt jedoch gleichzeitig angegebenen eingehenden Datenverkehr aus dem Internet zu. In der DMZ-Zone sollte sich nur der öffentliche VIP-Pool des externen Netzwerks von Azure Stack befinden.
- **Unsichere Zone**: Dies ist das externe Netzwerk, das Internet. Es wird davon **abgeraten**, Azure Stack in der unsicheren Zone bereitzustellen.

![Beispiel für eine Azure Stack-Bereitstellung in einem Umkreisnetzwerk](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Weitere Informationen
Erfahren Sie mehr über [die von Azure Stack-Endpunkten verwendeten Ports und Protokolle](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Nächste Schritte
[PKI-Anforderungen für Azure Stack](azure-stack-pki-certs.md)

