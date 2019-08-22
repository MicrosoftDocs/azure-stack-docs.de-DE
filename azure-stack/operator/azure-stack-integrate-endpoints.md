---
title: Integration des Azure Stack-Datencenters – Veröffentlichen von Endpunkten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Stack-Endpunkte in Ihrem Datencenter veröffentlichen.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/05/2019
ms.openlocfilehash: 6ffd13982a4acf90896b152adcee360e34c02b79
ms.sourcegitcommit: 8de4c18b25bd1047fc270812a795f24e8f1e9244
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68865885"
---
# <a name="azure-stack-datacenter-integration---publish-azure-stack-services"></a>Integration des Azure Stack-Datencenters – Veröffentlichen der Azure Stack-Dienste

Azure Stack richtet für die eigenen Infrastrukturrollen virtuelle IP-Adressen (VIPs) ein. Diese VIPs stammen aus dem öffentlichen IP-Adresspool. Jede VIP wird durch eine Zugriffssteuerungsliste (Access Control List, ACL) auf der softwaredefinierten Netzwerkebene geschützt. Für zusätzlichen Schutz werden außerdem übergreifende ACLs für die physischen Switches (TORs und BMC) verwendet. Für jeden Endpunkt in der externen DNS-Zone, die zum Zeitpunkt der Bereitstellung angegeben wurde, wird ein DNS-Eintrag erstellt. Dem Benutzerportal wird z. B. der DNS-Hosteintrag „portal. *&lt;region>.&lt;fqdn>* “ zugewiesen.

Das folgende Architekturdiagramm zeigt die verschiedenen Netzwerkebenen und ACLs:

![Strukturbild](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

### <a name="ports-and-urls"></a>Ports und URLs
Damit Sie Azure Stack-Dienste (wie Portale, Azure Resource Manager, DNS, usw.) für externe Netzwerke zur Verfügung stellen können, müssen Sie für diese Endpunkte den eingehenden Datenverkehr für bestimmte URLs, Ports und Protokolle zulassen.
 
In einer Bereitstellung mit einem Uplink zwischen einem transparenten Proxy und einem herkömmlichen Proxyserver bzw. wenn eine Firewall die Lösung schützt, müssen sowohl für die [eingehende](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) als auch die [ausgehende](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) Kommunikation bestimmte Ports und URLs zugelassen werden. Dazu gehören Ports und URLs für Identität, der Marketplace, Patch und Update, Registrierung und Nutzungsdaten.

## <a name="ports-and-protocols-inbound"></a>Ports und Protokolle (eingehend)

Eine Reihe von Infrastruktur-VIPs wird für die Veröffentlichung von Azure Stack-Endpunkten in externen Netzwerken benötigt. Die Tabelle *Endpunkt (VIP)* enthält jeweils den Endpunkt, den erforderlichen Port und das Protokoll. Informationen zu Endpunkten, für die zusätzliche Ressourcenanbieter (etwa der SQL-Ressourcenanbieter) erforderlich sind, finden Sie in der Bereitstellungsdokumentation des jeweiligen Ressourcenanbieters.

Interne Infrastruktur-VIPs sind nicht aufgeführt, da sie zum Veröffentlichen von Azure Stack nicht benötigt werden.

> [!Note]  
> Benutzer-VIPs sind dynamisch und werden von den Benutzern selbst definiert. Der Azure Stack-Betreiber hat darauf keinen Einfluss.

> [!Note]  
> IKEv2-VPN. IKEv2-VPN ist eine standardbasierte IPsec-VPN-Lösung, die UDP-Port 500 und 4500 und IP-Protokollnummer 50 verwendet. Firewalls öffnen nicht immer diese Ports, daher kann IKEv2-VPN unter Umständen Proxys und Firewalls nicht überwinden.

> [!Note]  
> Ab dem Update 1811 müssen Ports im Bereich von 12495–30015 nicht mehr offen sein, weil der [Erweiterungshost](azure-stack-extension-host-prepare.md) hinzugefügt wurde.

|Endpunkt (VIP)|A-Eintrag des DNS-Hosts|Protocol|Ports|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (Administrator)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Administratorhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (Administrator)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (Benutzer)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (Benutzer)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Zertifikatsperrliste|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP und UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (Benutzer)|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (Administrator)|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Speicherwarteschlange|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Speichertabelle|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Speicherblob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-Ressourcenanbieter|sqladapter.dbadapter. *&lt;Region>.&lt;FQDN>*|HTTPS|44300-44304|
|MySQL-Ressourcenanbieter|mysqladapter.dbadapter. *&lt;Region>.&lt;FQDN>*|HTTPS|44300-44304|
|App Service|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN-Gateways|     |     |Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Ports und URLs (ausgehend)

Azure Stack unterstützt nur transparente Proxyserver. In einer Bereitstellung mit einem Uplink zwischen einem transparenten Proxy und einem herkömmlichen Proxyserver müssen für die ausgehende Kommunikation die Ports und URLs in der folgenden Tabelle zugelassen werden.

> [!Note]  
> Azure Stack unterstützt nicht die Verwendung von Express Route, um die in der folgenden Tabelle aufgeführten Azure-Dienste zu erreichen.

|Zweck|Ziel-URL|Protocol|Ports|Quellnetzwerk|
|---------|---------|---------|---------|---------|
|Identity|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>|HTTP<br>HTTPS|80<br>443|Öffentliche VIP - /27<br>Öffentliches Infrastrukturnetzwerk|
|Marketplace-Syndikation|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn/|HTTPS|443|Öffentliche VIP - /27|
|Patches und Updates|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Öffentliche VIP - /27|
|Registrierung|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/|HTTPS|443|Öffentliche VIP - /27|
|Verwendung|**Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|Öffentliche VIP - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Öffentliche VIP - /27<br>Öffentliches Infrastrukturnetzwerk|
|NTP|(IP des für die Bereitstellung bereitgestellten NTP-Servers)|UDP|123|Öffentliche VIP - /27|
|DNS|(IP des für die Bereitstellung bereitgestellten DNS-Servers)|TCP<br>UDP|53|Öffentliche VIP - /27|
|CRL|(URL unter CRL-Verteilungspunkten auf Ihrem Zertifikat)|HTTP|80|Öffentliche VIP - /27|
|LDAP|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP<br>UDP|389|Öffentliche VIP - /27|
|LDAP SSL|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|636|Öffentliche VIP - /27|
|LDAP GC|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|3268|Öffentliche VIP - /27|
|LDAP GC SSL|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|3269|Öffentliche VIP - /27|
|AD FS|AD FS-Metadatenendpunkt, bereitgestellt für die AD FS-Integration|TCP|443|Öffentliche VIP - /27|
|Sammlungsdienst für Diagnoseprotokolle|Von Azure Storage bereitgestellte Blob SAS URL|HTTPS|443|Öffentliche VIP - /27|
|     |     |     |     |     |

Für ausgehende URLs erfolgt ein Lastenausgleich mithilfe von Azure Traffic Manager, um bestmögliche Konnektivität basierend auf dem geografischen Standort zu bieten. Durch URLs mit Lastenausgleich kann Microsoft Back-End-Endpunkte ohne Auswirkungen auf Kunden aktualisieren und ändern. Microsoft gibt die Liste der IP-Adressen für die URLs mit Lastenausgleich nicht frei. Sie sollten ein Gerät verwenden, das ein Filtern nach URL und nicht nach IP-Adresse unterstützt.

DNS in ausgehender Richtung ist immer erforderlich. Nur die Quelle, über die das externe DNS abgefragt wird, und die gewählte Identitätsintegration variieren. Wenn dies ein verbundenes Szenario ist, benötigt die im BMC-Netzwerk angeordnete DVM diesen ausgehenden Zugriff. Nach der Bereitstellung wird der DNS-Dienst aber auf eine interne Komponente verschoben, von der Abfragen über eine öffentliche VIP gesendet werden. Zu diesem Zeitpunkt kann der ausgehende DNS-Zugriff über das BMC-Netzwerk entfernt werden, aber der Zugriff über die öffentliche VIP auf diesen DNS-Server muss erhalten bleiben, da für die Authentifizierung ansonsten ein Fehler auftritt.

## <a name="next-steps"></a>Nächste Schritte

[PKI-Anforderungen für Azure Stack](azure-stack-pki-certs.md)
