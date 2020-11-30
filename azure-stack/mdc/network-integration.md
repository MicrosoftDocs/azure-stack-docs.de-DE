---
title: Netzwerkintegration von Modular Data Center
description: Erfahren Sie mehr über die Azure Stack-Netzwerkintegration für Azure Modular Data Center.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: e2bd1f42cd60aabc3baa153525b2a7aeb03ac30b
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924332"
---
# <a name="network-integration"></a>Netzwerkintegration

Dieser Artikel behandelt die Azure Stack-Netzwerkintegration für Azure Modular Data Center.

## <a name="border-connectivity-uplink"></a>Grenzkonnektivität (Uplink)

Die Planung der Netzwerkintegration ist eine wichtige Voraussetzung, um erfolgreich integrierte Azure Stack-Systeme bereitstellen, betreiben und verwalten zu können. Bei der Planung der Konnektivität über Border-Geräte entscheiden Sie zuerst, ob Sie dynamisches Routing mit Border Gateway Protocol (BGP) oder statisches Routing verwenden möchten. Für dynamisches Routing ist es erforderlich, dass Sie eine autonome 16-Bit-Systemnummer (ASN) zuweisen (öffentlich oder privat). Statisches Routing verwendet eine statische Standardroute, die den Border-Geräten zugewiesen ist.

Für Edgeswitches sind Layer 3-Uplinks mit für physische Schnittstellen konfigurierte Point-to-Point-IP-Adressen (Netzwerke der Größe /30) erforderlich. Layer 2-Uplinks mit Edgeswitches für Azure Stack-Vorgänge werden nicht unterstützt.

### <a name="bgp-routing"></a>BGP-Routing

Durch ein dynamisches Routingprotokoll wie BGP wird die Erkennung von Netzwerkänderungen durch Ihr System sichergestellt und die Verwaltung vereinfacht. Zur Verbesserung der Sicherheit können Sie ein Kennwort für das BGP-Peering zwischen Edge und Grenze festlegen.

Wie im folgenden Diagramm dargestellt, wird die Ankündigung des privaten IP-Bereichs für den TOR-Switch (Top-of-Rack) durch eine Präfixliste blockiert. Die Präfixliste lehnt die Ankündigung des privaten Netzwerks ab und wird als Routenzuordnung für die Verbindung zwischen TOR und Edge angewandt.

Software Load Balancer (SLB) in der Azure Stack-Lösung stellt mittels Peering eine Verbindung mit TOR-Geräten her, damit die VIP-Adressen dynamisch angekündigt werden können.

Um sicherzustellen, dass Benutzerdatenverkehr nach einem Fehler umgehend und transparent wiederhergestellt wird, ermöglicht die zwischen den TOR-Geräten konfigurierte virtuelle Private Cloud- oder MLAG-Vernetzung (Multi-Chassis Link Aggregation) die Verwendung von MLAG für die Hosts sowie des HSRP- oder VRRP-Protokolls, das Redundanz für die IP-Netzwerke bietet.

### <a name="static-routing"></a>Statisches Routing

Statisches Routing erfordert zusätzliche Konfiguration für die Border-Geräte. Vor jeder Änderung sind mehr manuelle Intervention und Verwaltung sowie eine gründliche Analyse erforderlich. Bei Problemen, die durch Konfigurationsfehler verursacht wurden, kann der Rollback je nach den vorgenommenen Änderungen relativ zeitintensiv sein. Diese Routingmethode wird nicht empfohlen, jedoch unterstützt.

Zum Integrieren von Azure Stack in Ihre Netzwerkumgebung mithilfe von statischem Routing müssen alle vier physischen Verknüpfungen zwischen dem Grenz- und dem Edgegerät verbunden sein. Hohe Verfügbarkeit kann aufgrund der Funktionsweise von statischem Routing nicht garantiert werden.

Das Border-Gerät muss für Datenverkehr an Netzwerke innerhalb von Azure Stack mit statischen Routen konfiguriert sein, die auf jede der vier festgelegten Point-2-Point-IP-Adressen zwischen Edge und Grenze verweisen. Für den Betrieb ist aber nur das externe oder öffentliche VIP-Netzwerk erforderlich. Statische Routen zum BMC-Netzwerk und den externen Netzwerken sind für die erste Bereitstellung erforderlich. Operatoren können statische Routen auch an der Grenze beibehalten, um auf Verwaltungsressourcen im BMC- und im Infrastrukturnetzwerk zuzugreifen. Das Hinzufügen von statischen Routen zu Switchinfrastruktur- und Switchverwaltungsnetzwerken ist optional.

Die Tor-Geräte sind mit einer statischen Standardroute vorkonfiguriert, die sämtlichen Datenverkehr an die Border-Geräte sendet. Die einzige Datenverkehrsausnahme von der Standardregel ist der private Bereich. Dieser wird mithilfe einer Zugriffssteuerungsliste für die Verbindung zwischen dem TOR- und Border-Gerät blockiert.

Statisches Routing wird nur auf die Uplinks zwischen den Edge- und Grenzswitches angewandt. Im Rack kommt nach wie vor dynamisches BGP-Routing zum Einsatz, da es ein unverzichtbares Hilfsmittel für SLB und andere Komponenten darstellt. Es kann daher nicht deaktiviert oder entfernt werden.

\* Das BMC-Netzwerk ist nach der Bereitstellung optional.

\*\* Das Switchinfrastrukturnetzwerk ist optional, da das gesamte Netzwerk in das Switchverwaltungsnetzwerk eingeschlossen werden kann.

\*\*\* Das Switchverwaltungsnetzwerk ist erforderlich und kann separat aus dem Switchinfrastrukturnetzwerk hinzugefügt werden.

### <a name="transparent-proxy"></a>Transparenter Proxy

Wenn Ihr Rechenzentrum verlangt, dass für den gesamten Datenverkehr ein Proxy verwendet wird, müssen Sie einen transparenten Proxy konfigurieren. Dieser muss den gesamten Datenverkehr gemäß den Richtlinien verarbeiten. Sie müssen den Datenverkehr zwischen den Zonen in Ihrem Netzwerk trennen.

Die Azure Stack-Lösung unterstützt keine normalen Webproxys.

Ein transparenter Proxy (auch abfangender, inline oder erzwungener Proxy genannt) fängt die normale Kommunikation auf der Netzwerkschicht ab, ohne dass eine spezielle Clientkonfiguration erforderlich ist. Clients müssen nicht wissen, dass der Proxy vorhanden ist.

Das Abfangen von SSL-Datenverkehr wird nicht unterstützt und kann beim Zugriff auf Endpunkte zu Dienstfehlern führen. Das maximal unterstützte Zeitlimit für die Kommunikation mit Endpunkten, die für die Identität erforderlich sind, ist 60 Sekunden mit drei Wiederholungsversuchen.

## <a name="dns"></a>DNS

In diesem Abschnitt wird die DNS-Konfiguration (Domain Name System) behandelt.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurieren der bedingten DNS-Weiterleitung

Dieser Leitfaden gilt nur für eine AD FS-Bereitstellung (Active Directory-Verbunddienste).

Um die Namensauflösung mit Ihrer vorhandenen DNS-Infrastruktur zu ermöglichen, konfigurieren Sie eine bedingte Weiterleitung.

Zum Hinzufügen einer bedingten Weiterleitung müssen Sie den privilegierten Endpunkt verwenden.

Verwenden Sie für diesen Vorgang einen Computer in Ihrem Datencenternetzwerk, der mit dem privilegierten Endpunkt in Azure Stack kommunizieren kann.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten („Als Administrator ausführen“). Stellen Sie eine Verbindung mit der IP-Adresse des privilegierten Endpunkts her. Verwenden Sie die Anmeldeinformationen für die CloudAdmin-Authentifizierung.

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```

1. Nachdem Sie eine Verbindung mit dem privilegierten Endpunkt hergestellt haben, führen Sie folgenden PowerShell-Befehl aus. Ersetzen Sie die bereitgestellten Beispielwerte durch Ihren Domänennamen und die IP-Adressen des DNS-Servers, den Sie verwenden möchten.

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolve-azure-stack-dns-names-from-outside-azure-stack"></a>Auflösen von Azure Stack-DNS-Namen von außerhalb von Azure Stack

Die autoritativen Server enthalten die Informationen zu externen DNS-Zonen und zu allen vom Benutzer erstellten Zonen. Integrieren Sie diese Server, um die Zonendelegierung oder die bedingte Weiterleitung für das Auflösen von Azure Stack-DNS-Namen von außerhalb von Azure Stack zu aktivieren.

### <a name="get-dns-server-external-endpoint-information"></a>Erhalten der externen Endpunktinformationen des DNS-Servers

Sie benötigen folgende Informationen, um Ihre Azure Stack-Bereitstellung in Ihre DNS-Infrastruktur zu integrieren:

- Vollqualifizierte Domänennamen (FQDNs) des DNS-Servers
- IP-Adressen des DNS-Servers

Die vollqualifizierten Domänennamen für die Azure Stack-DNS-Server weisen folgendes Format auf:

- *\<NAMINGPREFIX\>-ns01.\<REGION\>.\<EXTERNALDOMAINNAME\>*
- *\<NAMINGPREFIX\>-ns02.\<REGION\>.\<EXTERNALDOMAINNAME\>*

Wenn Sie die Beispielwerte verwenden, sind die vollqualifizierten Domänennamen für die DNS-Server Folgende:

- *azs-ns01.east.cloud.fabrikam.com*
- *azs-ns02.east.cloud.fabrikam.com*

Diese Informationen sind im Verwaltungsportal verfügbar, werden aber auch am Ende aller Azure Stack-Bereitstellungen in einer Datei namens *AzureStackStampInformation.json* festgehalten. Diese Datei befindet sich im Ordner *C:\\CloudDeployment\\logs* des virtuellen Computers der Bereitstellung. Wenn Sie nicht sicher sind, welche Werte für Ihre Azure Stack-Bereitstellung verwendet werden, können Sie diese Werte hier abrufen.

Wenn der virtuelle Computer für die Bereitstellung nicht mehr verfügbar ist oder nicht darauf zugegriffen werden kann, können Sie die Werte abrufen, indem Sie eine Verbindung mit dem privilegierten Endpunkt herstellen und das PowerShell-Cmdlet *Get-AzureStackStampInformation* ausführen. Weitere Informationen finden Sie unter Privilegierter Endpunkt.

### <a name="set-up-conditional-forwarding-to-azure-stack"></a>Einrichten der bedingten Weiterleitung an Azure Stack

Der einfachste und sicherste Weg, um Azure Stack in Ihre DNS-Infrastruktur zu integrieren, ist die bedingte Weiterleitung der Zone von dem Server aus, der die übergeordnete Zone hostet. Dieser Ansatz wird empfohlen, wenn Sie die direkte Kontrolle über die DNS-Server besitzen, die die übergeordnete Zone für Ihren externen DNS-Namespace von Azure Stack hosten.

Wenn Sie nicht mit der bedingten Weiterleitung mit DNS vertraut sind, finden Sie weitere Informationen im TechNet-Artikel „Zuweisen einer bedingten Weiterleitung für einen Domänennamen“ oder in der spezifischen Dokumentation für Ihre DNS-Lösung.

In Szenarios, in denen Sie angegeben haben, dass Ihre externe Azure Stack-DNS-Zone wie die untergeordnete Domäne des Namens Ihrer Unternehmensdomäne aussehen soll, kann die bedingte Weiterleitung nicht verwendet werden. Die DNS-Delegierung muss konfiguriert werden.

Beispiel:

- DNS-Domänenname des Unternehmens: *contoso.com*
- Externer Azure Stack-DNS-Domänenname: *azurestack.contoso.com*

### <a name="edit-dns-forwarder-ips"></a>Bearbeiten von IP-Adressen der DNS-Weiterleitung

IP-Adressen der DNS-Weiterleitung werden während der Bereitstellung von Azure Stack festgelegt. Wenn die IP-Adressen der Weiterleitung aus irgendeinem Grund aktualisiert werden müssen, können Sie die Werte bearbeiten, indem Sie eine Verbindung mit dem privilegierten Endpunkt herstellen und die PowerShell-Cmdlets *Get-AzSDnsForwarder* und *Set-AzSDnsForwarder [[-IPAddress] \<IPAddress[]\>]* ausführen. Weitere Informationen finden Sie unter Privilegierter Endpunkt.

### <a name="delegate-the-external-dns-zone-to-azure-stack"></a>Delegieren der externen DNS-Zone an Azure Stack

Damit DNS-Namen von außerhalb einer Azure Stack-Bereitstellung auflösbar sind, müssen Sie eine DNS-Delegierung einrichten.

Jede Registrierungsstelle hat seine eigenen DNS-Verwaltungstools, um die Namenservereinträge für eine Domäne zu ändern. Bearbeiten Sie auf der DNS-Verwaltungsseite der Registrierungsstelle die NS-Einträge, und ersetzen Sie die NS-Einträge für die Zone durch die in Azure Stack erstellten Einträge.

Die meisten DNS-Registrierungsstellen erfordern, dass Sie mindestens zwei DNS-Server angeben, um die Delegierung abzuschließen.

### <a name="firewall"></a>Firewall

Azure Stack richtet für die eigenen Infrastrukturrollen virtuelle IP-Adressen (VIPs) ein. Diese VIPs stammen aus dem öffentlichen IP-Adresspool. Jede VIP wird durch eine Zugriffssteuerungsliste (Access Control List, ACL) auf der softwaredefinierten Netzwerkebene geschützt. Für zusätzlichen Schutz werden außerdem übergreifende ACLs für die physischen Switches (TORs und BMC) verwendet. Für jeden Endpunkt in der externen DNS-Zone, die zum Zeitpunkt der Bereitstellung angegeben wurde, wird ein DNS-Eintrag erstellt. Dem Benutzerportal wird z. B. der DNS-Hosteintrag „portal. *\<region\>.\<fqdn\>* “ zugewiesen.

Das folgende Architekturdiagramm zeigt die verschiedenen Netzwerkebenen und ACLs.

![Architekturdiagramm, das die verschiedenen Netzwerkebenen und ACLs zeigt.](media/network-deployment/network-architecture.png)

### <a name="ports-and-urls"></a>Ports und URLs

Damit Sie Azure Stack-Dienste, wie Portale, Azure Resource Manager und DNS, für externe Netzwerke zur Verfügung stellen können, müssen Sie für diese Endpunkte den eingehenden Datenverkehr für bestimmte URLs, Ports und Protokolle zulassen.

In einer Bereitstellung mit einem Uplink zwischen einem transparenten Proxy und einem herkömmlichen Proxyserver bzw. wenn eine Firewall die Lösung schützt, müssen sowohl für die eingehende als auch die ausgehende Kommunikation bestimmte Ports und URLs zugelassen werden. Beispiele umfassen Ports und URLs für Identität, den Azure Stack Hub Marketplace, Patch und Update, Registrierung und Nutzungsdaten.

### <a name="outbound-communication"></a>Ausgehende Kommunikation

Azure Stack unterstützt nur transparente Proxyserver. In einer Bereitstellung mit einem Uplink zwischen einem transparenten Proxy und einem herkömmlichen Proxyserver müssen für die ausgehende Kommunikation bei einer Bereitstellung im verbundenen Modus die Ports und URLs in der folgenden Tabelle zugelassen werden.

Das Abfangen von SSL-Datenverkehr wird nicht unterstützt und kann beim Zugriff auf Endpunkte zu Dienstfehlern führen. Das maximal unterstützte Zeitlimit für die Kommunikation mit Endpunkten, die für die Identität erforderlich sind, ist 60 Sekunden.

>[!NOTE]
>Azure Stack bietet keine Unterstützung für die Verwendung von Azure ExpressRoute zur Kommunikation mit den in der folgenden Tabelle aufgeführten Azure-Diensten, da ExpressRoute den Datenverkehr möglicherweise nicht an alle Endpunkte weiterleiten kann.


|Zweck|Ziel-URL|Protocol|Ports|Quellnetzwerk|
|---------|---------|---------|---------|---------|
|Identity|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure Deutschland**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Öffentliche VIP - /27<br>Öffentliches Infrastrukturnetzwerk|
|Azure Stack Hub Marketplace-Syndikation|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|Öffentliche VIP - /27|
|Patch und Update|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Öffentliche VIP - /27|
|Registrierung|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|Öffentliche VIP - /27|
|Verwendung|**Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|Öffentliche VIP - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Öffentliche VIP - /27<br>Öffentliches Infrastrukturnetzwerk|
|NTP|IP des für die Bereitstellung bereitgestellten NTP-Servers|UDP|123|Öffentliche VIP - /27|
|DNS|IP des für die Bereitstellung bereitgestellten DNS-Servers|TCP<br>UDP|53|Öffentliche VIP - /27|
|CRL|URL unter CRL-Verteilungspunkten auf Ihrem Zertifikat|HTTP|80|Öffentliche VIP - /27|
|LDAP|Active Directory-Gesamtstruktur, bereitgestellt für die Azure Graph-Integration|TCP<br>UDP|389|Öffentliche VIP - /27|
|LDAP SSL|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|636|Öffentliche VIP - /27|
|LDAP GC|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|3268|Öffentliche VIP - /27|
|LDAP GC SSL|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|3269|Öffentliche VIP - /27|
|AD FS|AD FS-Metadatenendpunkt, bereitgestellt für die AD FS-Integration|TCP|443|Öffentliche VIP - /27|
|Sammlungsdienst für Diagnoseprotokolle|Von Azure Blob Storage bereitgestellte Shared Access Signature-URL (SAS)|HTTPS|443|Öffentliche VIP - /27|
|     |     |     |     |     |
                                                                                                                                                                
### <a name="inbound-communication"></a>Eingehende Kommunikation

Eine Reihe von Infrastruktur-VIPs wird für die Veröffentlichung von Azure Stack-Endpunkten in externen Netzwerken benötigt. Die Tabelle Endpunkt (VIP) enthält jeweils den Endpunkt, den erforderlichen Port und das Protokoll. Informationen zu Endpunkten, für die zusätzliche Ressourcenanbieter (etwa der SQL-Ressourcenanbieter) erforderlich sind, finden Sie in der spezifischen Bereitstellungsdokumentation des jeweiligen Ressourcenanbieters.

Interne Infrastruktur-VIPs sind nicht aufgeführt, da sie zum Veröffentlichen von Azure Stack nicht benötigt werden. Benutzer-VIPs sind dynamisch und werden von den Benutzern selbst definiert. Der Azure Stack-Betreiber hat darauf keinen Einfluss.

>[!NOTE]
>
>IKEv2-VPN ist eine standardbasierte IPsec-VPN-Lösung, für die UDP-Port 500 und 4500 sowie TCP-Port 50 verwendet werden. Da diese Ports für Firewalls nicht immer geöffnet sind, kann eine IKEv2-VPN-Verbindung die Proxys und Firewalls ggf. nicht immer durchlaufen.

|Endpunkt (VIP)|A-Eintrag des DNS-Hosts|Protocol|Ports|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure-Portal (Administrator)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Administratorhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (Administrator)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure-Portal (Benutzer)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (Benutzer)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Zertifikatsperrliste|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP und UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Key Vault (Benutzer)|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Key Vault (Administrator)|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Queue Storage |&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Azure Table Storage |&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Azure Blob Storage |&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-Ressourcenanbieter|sqladapter.dbadapter. *&lt;Region>.&lt;FQDN>*|HTTPS|44300-44304|
|MySQL-Ressourcenanbieter|mysqladapter.dbadapter. *&lt;Region>.&lt;FQDN>*|HTTPS|44300-44304|
|Azure App Service|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Azure VPN Gateway|     |     |[Weitere Informationen finden Sie unter Häufig gestellte Fragen zum VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

