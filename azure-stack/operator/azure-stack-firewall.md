---
title: Azure Stack Hub-Firewallintegration für integrierte Azure Stack Hub-Systeme | Microsoft-Dokumentation
description: Enthält Informationen zur Azure Stack Hub-Firewallintegration für integrierte Azure Stack Hub-Systeme.
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
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 5b480b92bced0af040a75f97cc8094a17de90ffe
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818297"
---
# <a name="azure-stack-hub-firewall-integration"></a>Azure Stack Hub-Firewallintegration
Es empfiehlt sich, Azure Stack Hub mit einem Firewallgerät zu schützen. Firewalls können bei der Verteidigung vor Dingen wie DDoS-Angriffen (Denial of Service), bei der Angriffserkennung sowie der Inhaltsuntersuchung helfen. Sie können allerdings auch zu einem Durchsatzengpass für Azure-Speicherdienste wie Blobs, Tabellen und Warteschlangen werden.

 Bei Verwendung eines getrennten Bereitstellungsmodus muss der AD FS-Endpunkt veröffentlicht werden. Weitere Informationen finden Sie im Artikel zur [Rechenzentrumsintegration mithilfe von Identitäten](azure-stack-integrate-identity.md).

Für die Endpunkte „Azure Resource Manager (Administrator)“, „Portal (Administrator)“ und „Schlüsseltresor (Administrator)“ ist nicht zwingend eine externe Veröffentlichung erforderlich. Als Dienstanbieter könnten Sie beispielsweise die Angriffsfläche verkleinern, indem Sie Azure Stack Hub nur innerhalb Ihres Netzwerks (und nicht über das Internet) verwalten.

Bei einem Unternehmen kann das Unternehmensnetzwerk als externes Netzwerk fungieren. In diesem Szenario müssen Sie Endpunkte veröffentlichen, um Azure Stack Hub über das Unternehmensnetzwerk bedienen zu können.

### <a name="network-address-translation"></a>Netzwerkadressenübersetzung
Die Netzwerkadressenübersetzung (Network Address Translation, NAT) stellt die empfohlene Methode dar, um virtuellen Bereitstellungscomputern (Deployment Virtual Machine, DVM) bei der Bereitstellung sowie ERCS-VMs (Emergency Recovery Console) oder privilegierten Endpunkten (PEP) bei der Registrierung und Problembehandlung Zugriff auf externe Ressourcen und das Internet zu ermöglichen.

Die NAT kann auch alternativ zu öffentlichen IP-Adressen im externen Netzwerk oder öffentlichen VIPs verwendet werden. Hiervon wird aber abgeraten, da diese die Benutzerfreundlichkeit des Mandanten verringert und die Komplexität erhöht. Eine Option wäre eine 1:1-NAT, die immer noch eine öffentliche IP pro Benutzer-IP im Pool erfordert. Eine andere Option ist eine n:1-NAT, die eine NAT-Regel pro Benutzer-VIP für alle Ports erfordert, die ein Benutzer verwenden könnte.

Die Verwendung der NAT für öffentliche VIPs hat folgende Nachteile:
- Durch die NAT erhöht sich der Verwaltungsaufwand für Firewallregeln, da Benutzer ihre eigenen Endpunkte und ihre eigenen Veröffentlichungsregeln im SDN-Stapel (Software Defined Networking) steuern. Benutzer müssen sich zur Veröffentlichung ihrer VIPs sowie zur Aktualisierung der Portliste an den Azure Stack Hub-Operator wenden.
- Die Verwendung der NAT beeinträchtigt zwar die Benutzerfreundlichkeit, gibt dem Betreiber jedoch die volle Kontrolle über Veröffentlichungsanforderungen.
- In Hybrid Cloud-Szenarien mit Azure wird die Einrichtung eines VPN-Tunnels zu einem Endpunkt mit NAT nicht unterstützt.

### <a name="ssl-interception"></a>Abfangen von SSL
Zurzeit wird empfohlen, das Abfangen von SSL (z. B. Entschlüsselungsabladung) für den gesamten Azure Stack Hub-Datenverkehr zu deaktivieren. Wenn diese Funktion in zukünftigen Updates unterstützt wird, werden Anleitungen zum Aktivieren von SSL-Abfangen für Azure Stack Hub bereitgestellt.

## <a name="edge-firewall-scenario"></a>Szenario mit Edgefirewall
Azure Stack Hub wird in einer Edgebereitstellung direkt hinter dem Edgerouter oder der Firewall bereitgestellt. In diesen Szenarien kann die Firewall dem Border-Gerät übergeordnet sein (Szenario 1) und sowohl Aktiv/Aktiv- als auch Aktiv/Passiv-Firewallkonfigurationen unterstützen oder als Border-Gerät fungieren (Szenario 2) und nur eine Aktiv/Aktiv-Firewallkonfiguration unterstützen, wobei ECMP (Equal-Cost Multi-Path) mit BGP oder statischem Routing für Failover erforderlich ist.

Öffentliche routingfähige IP-Adressen werden für den öffentlichen VIP-Pool aus dem externen Netzwerk zur Bereitstellungszeit angegeben. In einem Edgeszenario wird aus Sicherheitsgründen davon abgeraten, öffentliche routingfähige IP-Adressen in anderen Netzwerken zu verwenden. Dadurch erhalten Benutzer eine vollständig selbstgesteuerte Cloudumgebung – genau wie bei einer öffentlichen Cloud (z.B. Azure).  

![Beispiel für eine Azure Stack Hub-Edgefirewall](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Firewallszenario mit Unternehmensintranet oder -umkreisnetzwerk
Bei einer Bereitstellung im Unternehmensintranet oder -umkreisnetzwerk wird Azure Stack Hub in einer Firewall mit mehreren Zonen oder zwischen der Edgefirewall und der internen Unternehmensnetzwerkfirewall bereitgestellt. Dann wird wie im Folgenden beschrieben der Datenverkehr zwischen dem sicheren Umkreisnetzwerk (oder der DMZ) und unsicheren Zonen verteilt:

- **Sichere Zone**: Dies ist das interne Netzwerk, das interne oder routingfähige Unternehmens-IP-Adressen verwendet. Das sichere Netzwerk kann unterteilt werden, über ausgehenden Internetzugriff über die NAT in der Firewall verfügen und in der Regel von einem beliebige Ort aus in Ihrem Rechenzentrum über das interne Netzwerk aufgerufen werden. Alle Azure Stack Hub-Netzwerke sollten sich in der sicheren Zone befinden, der öffentliche VIP-Pool im externen Netzwerk ausgenommen.
- **Umkreiszone**: Das Umkreisnetzwerk wird dort eingesetzt, wo in der Regel externe oder Internet-Apps, z. B. Webserver, bereitgestellt werden. Es wird normalerweise durch eine Firewall überwacht, um Angriffe wie DDoS-Angriffe und Eindringversuche (Hacken) zu verhindern, während gleichzeitig angegebener eingehender Datenverkehr aus dem Internet zugelassen wird. In der DMZ-Zone sollte sich nur der öffentliche VIP-Pool des externen Netzwerks von Azure Stack Hub befinden.
- **Unsichere Zone**: Dies ist das externe Netzwerk, das Internet. Es wird davon **abgeraten**, Azure Stack Hub in der unsicheren Zone bereitzustellen.

![Beispiel für eine Azure Stack Hub-Bereitstellung in einem Umkreisnetzwerk](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Weitere Informationen
Erfahren Sie mehr über [die von Azure Stack Hub-Endpunkten verwendeten Ports und Protokolle](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Nächste Schritte
[PKI-Anforderungen für Azure Stack Hub](azure-stack-pki-certs.md)

