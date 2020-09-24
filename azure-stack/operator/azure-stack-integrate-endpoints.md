---
title: Veröffentlichen von Azure Stack Hub-Diensten in Ihrem Rechenzentrum
description: Hier erfahren Sie, wie Sie Azure Stack Hub-Dienste in Ihrem Rechenzentrum veröffentlichen.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/22/2020
ms.author: justinha
ms.reviewer: wamota
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: b1b3a267185d9486bdf023f1af7c8e2348e49351
ms.sourcegitcommit: 9a3397f703ff9dd7d539372bd8e5fdbe6d6a0725
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "91019621"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>Veröffentlichen von Azure Stack Hub-Diensten in Ihrem Rechenzentrum

Azure Stack Hub richtet für die eigenen Infrastrukturrollen virtuelle IP-Adressen (VIPs) ein. Diese VIPs stammen aus dem öffentlichen IP-Adresspool. Jede VIP wird durch eine Zugriffssteuerungsliste (Access Control List, ACL) auf der softwaredefinierten Netzwerkebene geschützt. Für zusätzlichen Schutz werden außerdem übergreifende ACLs für die physischen Switches (TORs und BMC) verwendet. Für jeden Endpunkt in der externen DNS-Zone, die zum Zeitpunkt der Bereitstellung angegeben wurde, wird ein DNS-Eintrag erstellt. Dem Benutzerportal wird z. B. der DNS-Hosteintrag „portal. *&lt;region>.&lt;fqdn>* “ zugewiesen.

Das folgende Architekturdiagramm zeigt die verschiedenen Netzwerkebenen und ACLs:

![Diagramm mit verschiedenen Netzwerkebenen und ACLs](media/azure-stack-integrate-endpoints/integrate-endpoints-01.svg)

## <a name="ports-and-urls"></a>Ports und URLs

Damit Sie Azure Stack Hub-Dienste (wie Portale, Azure Resource Manager, DNS, usw.) für externe Netzwerke zur Verfügung stellen können, müssen Sie für diese Endpunkte den eingehenden Datenverkehr für bestimmte URLs, Ports und Protokolle zulassen.

In einer Bereitstellung mit einem Uplink zwischen einem transparenten Proxy und einem herkömmlichen Proxyserver bzw. wenn eine Firewall die Lösung schützt, müssen sowohl für die [eingehende](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) als auch die [ausgehende](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) Kommunikation bestimmte Ports und URLs zugelassen werden. Dazu gehören Ports und URLs für Identität, der Marketplace, Patch und Update, Registrierung und Nutzungsdaten.

Das Abfangen von SSL-Datenverkehr wird [nicht unterstützt](azure-stack-firewall.md#ssl-interception) und kann beim Zugriff auf Endpunkte zu Dienstfehlern führen. 

## <a name="ports-and-protocols-inbound"></a>Ports und Protokolle (eingehend)

Eine Reihe von Infrastruktur-VIPs wird für die Veröffentlichung von Azure Stack Hub-Endpunkten in externen Netzwerken benötigt. Die Tabelle *Endpunkt (VIP)* enthält jeweils den Endpunkt, den erforderlichen Port und das Protokoll. Informationen zu Endpunkten, für die zusätzliche Ressourcenanbieter (etwa der SQL-Ressourcenanbieter) erforderlich sind, finden Sie in der Bereitstellungsdokumentation des jeweiligen Ressourcenanbieters.

Interne Infrastruktur-VIPs sind nicht aufgeführt, da sie zum Veröffentlichen von Azure Stack Hub nicht benötigt werden. Benutzer-VIPs sind dynamisch und werden von den Benutzern selbst definiert. Der Azure Stack Hub-Operator hat darauf keinen Einfluss.

> [!Note]  
> IKEv2-VPN ist eine standardbasierte IPsec-VPN-Lösung, für die UDP-Port 500 und 4500 sowie TCP-Port 50 verwendet werden. Da diese Ports für Firewalls nicht immer geöffnet sind, kann eine IKEv2-VPN-Verbindung die Proxys und Firewalls ggf. nicht immer durchlaufen.

Wenn der [Erweiterungshost](azure-stack-extension-host-prepare.md) hinzugefügt wird, sind Ports im Bereich 12495 - 30015 nicht erforderlich.

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
|VPN-Gateways|     |     |Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Ports und URLs (ausgehend)

Azure Stack Hub unterstützt nur transparente Proxyserver. In einer Bereitstellung mit einem Uplink zwischen einem transparenten Proxy und einem herkömmlichen Proxyserver müssen für die ausgehende Kommunikation die Ports und URLs in der folgenden Tabelle zugelassen werden.

Das Abfangen von SSL-Datenverkehr wird [nicht unterstützt](azure-stack-firewall.md#ssl-interception) und kann beim Zugriff auf Endpunkte zu Dienstfehlern führen. Das maximal unterstützte Zeitlimit für die Kommunikation mit Endpunkten, die für die Identität erforderlich sind, ist 60 Sekunden.

> [!Note]  
> Azure Stack Hub bietet keine Unterstützung für die Verwendung von ExpressRoute zur Kommunikation mit den in der folgenden Tabelle aufgeführten Azure-Diensten, weil ExpressRoute den Datenverkehr möglicherweise nicht an alle Endpunkte weiterleiten kann.

|Zweck|Ziel-URL|Protocol|Ports|Quellnetzwerk|
|---------|---------|---------|---------|---------|
|Identity|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure Deutschland**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Öffentliche VIP - /27<br>Öffentliches Infrastrukturnetzwerk|
|Marketplace-Syndikation|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|Öffentliche VIP - /27|
|Patches und Updates|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Öffentliche VIP - /27|
|Registrierung|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|Öffentliche VIP - /27|
|Verwendung|**Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|Öffentliche VIP - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br><br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Öffentliche VIP - /27<br>Öffentliches Infrastrukturnetzwerk|
|NTP|(IP des für die Bereitstellung bereitgestellten NTP-Servers)|UDP|123|Öffentliche VIP - /27|
|DNS|(IP des für die Bereitstellung bereitgestellten DNS-Servers)|TCP<br>UDP|53|Öffentliche VIP - /27|
|SYSLOG|(IP des für die Bereitstellung bereitgestellten SYSLOG-Servers)|TCP<br>UDP|6514<br>514|Öffentliche VIP - /27|
|CRL|(URL unter CRL-Verteilungspunkten auf Ihrem Zertifikat)<br>http://crl.microsoft.com/pki/crl/products<br>http://mscrl.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pki/certs<br>http://www.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>|HTTP|80|Öffentliche VIP - /27|
|LDAP|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP<br>UDP|389|Öffentliche VIP - /27|
|LDAP SSL|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|636|Öffentliche VIP - /27|
|LDAP GC|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|3268|Öffentliche VIP - /27|
|LDAP GC SSL|Active Directory-Gesamtstruktur, bereitgestellt für die Graph-Integration|TCP|3269|Öffentliche VIP - /27|
|AD FS|AD FS-Metadatenendpunkt, bereitgestellt für die AD FS-Integration|TCP|443|Öffentliche VIP - /27|
| Erfassen von Diagnoseprotokollen | https://azsdiagprdlocalwestus02.blob.core.windows.net<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com | HTTPS | 443 | Öffentliche VIP - /27 |
|     |     |     |     |     |

Für ausgehende URLs wird mithilfe von Azure Traffic Manager ein Lastausgleich vorgenommen, um bestmögliche Konnektivität basierend auf dem geografischen Standort zu bieten. Durch URLs mit Lastenausgleich kann Microsoft Back-End-Endpunkte ohne Auswirkungen auf Kunden aktualisieren und ändern. Microsoft gibt die Liste der IP-Adressen für die URLs mit Lastenausgleich nicht frei. Verwenden Sie ein Gerät, das ein Filtern nach URL und nicht nach IP-Adresse unterstützt.

Ausgehender DNS-Datenverkehr ist immer erforderlich. Nur die Quelle für die externe DNS-Abfrage und die gewählte Identitätsintegration können variieren. Während der Bereitstellung eines verbundenen Szenarios benötigt der DVM im BMC-Netzwerk Zugriff auf den ausgehenden Datenverkehr. Nach der Bereitstellung wird der DNS-Dienst jedoch in eine interne Komponente verschoben, die Abfragen über eine öffentliche virtuelle IP-Adresse sendet. Zu diesem Zeitpunkt kann der ausgehende DNS-Zugriff über das BMC-Netzwerk entfernt werden, aber der Zugriff über die öffentliche virtuelle IP-Adresse auf diesen DNS-Server muss erhalten bleiben, weil es bei der Authentifizierung ansonsten zu einem Fehler kommt.

## <a name="next-steps"></a>Nächste Schritte

[PKI-Anforderungen für Azure Stack Hub](azure-stack-pki-certs.md)
