---
title: Allgemeine Überlegungen zur Integration von Rechenzentren für integrierte Azure Stack-Systeme | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Integration von Rechenzentren mit Azure Stack-Systemen mit mehreren Knoten jetzt planen und vorbereiten können.
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
ms.date: 03/14/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 53289e46a2f29a03535958b76743db107f6ecf64
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617779"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Überlegungen zur Integration von Rechenzentren für integrierte Azure Stack-Systeme
Wenn Sie an einem mit Azure Stack integrierten System interessiert sind, müssen Sie die wichtigsten Aspekte bei der Planung der Bereitstellung verstehen und wissen, wie das System in Ihr Rechenzentrum passt. Dieser Artikel bietet einen allgemeinen Überblick über diese Aspekte, damit Sie wichtige Infrastrukturentscheidungen für Ihr Azure Stack-System mit mehreren Knoten treffen können. Ein Verständnis dieser Aspekte hilft Ihnen bei der Zusammenarbeit mit Ihrem OEM-Hardwareanbieter, sobald dieser Azure Stack in Ihrem Rechenzentrum bereitstellt.  

> [!NOTE]
> Azure Stack-Systeme mit mehreren Knoten können nur bei autorisierten Hardwareherstellern bezogen werden. 

Um Azure Stack bereitzustellen, müssen Sie Ihrem Lösungsanbieter vor dem Beginn der Bereitstellung Planungsinformationen bereitstellen, damit der Prozess schnell und reibungslos abläuft. Die benötigten Informationen reichen von Netzwerk-, Sicherheits- und Identitätsinformationen bis hin zu vielen wichtigen Entscheidungen, die möglicherweise Wissen aus vielen verschiedenen Bereichen und von verschiedenen Entscheidungsträgern erfordern. Daher müssen Sie möglicherweise Mitarbeiter aus mehreren Teams in Ihrem Unternehmen hinzuziehen, um sicherzustellen, dass Sie über alle erforderlichen Informationen verfügen, bevor die Bereitstellung beginnt. Es ist möglicherweise hilfreich, sich mit Ihrem Hardwarehersteller zu besprechen, während Sie diese Informationen sammeln, da er Ratschläge geben kann, die Ihnen bei Ihren Entscheidungen helfen.

Während Sie die erforderlichen Informationen recherchieren und sammeln, müssen Sie möglicherweise vor der Bereitstellung einige Konfigurationsänderungen an Ihrer Netzwerkumgebung vornehmen. Dazu gehören die Reservierung von IP-Adressräumen für die Azure Stack-Lösung sowie die Konfiguration Ihrer Router, Switches und Firewalls zur Vorbereitung der Konnektivität mit den Switches der neuen Azure Stack-Lösung. Sichern Sie sich bei Ihrer Planung die Unterstützung der Fachbereichsexperten.

## <a name="capacity-planning-considerations"></a>Aspekte der Kapazitätsplanung
Beim Auswerten einer Azure Stack-Lösung für den Kauf müssen Entscheidungen in Bezug auf die Hardwarekonfiguration getroffen werden, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack-Lösung haben. Hierzu gehört die übliche Auswahl von CPU, Arbeitsspeicherdichte, Speicherkonfiguration und Gesamtumfang der Lösung (z.B. Anzahl von Servern). Im Gegensatz zu einer herkömmlichen Virtualisierungslösung gilt die einfache Arithmetik dieser Komponenten zur Ermittlung der nutzbaren Kapazität nicht. Der erste Grund ist, dass Azure Stack so aufgebaut ist, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Der zweite Grund ist, dass ein Teil der Lösungskapazität zur Unterstützung der Resilienz reserviert ist. Es geht um die Aktualisierung der Lösungssoftware auf eine Weise, bei der die Beeinträchtigung von Mandantenworkloads verringert wird. 

Mit dem Tool [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) können Sie auf zwei Arten fundierte Entscheidungen zur Planung der Kapazität treffen: entweder per Auswahl eines Hardwareangebots und einer passenden Kombination von Ressourcen, oder per Definition der von Azure Stack auszuführenden Workload, um die verfügbaren Hardware-SKUs anzuzeigen, die dies unterstützen. Schließlich dient das Tool auch als Hilfe beim Treffen von Entscheidungen in Bezug auf die Azure Stack-Planung und -Konfiguration. 

Es soll aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen.  Microsoft gewährt für die mit dem Tool bereitgestellten Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.



## <a name="management-considerations"></a>Überlegungen zur Verwaltung
Azure Stack ist ein geschlossenes System, bei dem die Infrastruktur aus Berechtigungs- und Netzwerksicht abgeriegelt ist. Dabei werden Netzwerk-Zugriffssteuerungslisten (ACLs) angewendet, um sämtlichen nicht autorisierten eingehenden Datenverkehr und sämtliche unnötige Kommunikation zwischen Infrastrukturkomponenten zu blockieren. Dadurch soll nicht autorisierten Benutzern der Zugriff auf das System verwehrt werden.

Für die tägliche Verwaltung und den Betrieb besteht kein uneingeschränkter Administratorzugriff auf die Infrastruktur. Azure Stack-Operatoren müssen das System über das Administratorportal oder mithilfe von Azure Resource Manager (über PowerShell oder die REST-API) verwalten. Es gibt keinen Zugriff auf das System durch andere Verwaltungstools wie Hyper-V-Manager oder Failovercluster-Manager. Es kann keine Software von Drittanbietern (z. B. Agenten) innerhalb der Komponenten der Azure Stack-Infrastruktur installiert werden, um den Schutz des Systems zu unterstützen. Die Interoperabilität mit externer Verwaltungs- und Sicherheitssoftware erfolgt über PowerShell oder die REST-API.

Wenn für die Behandlung von Problemen, die nicht durch Schritte zur Warnungsvermittlung behoben werden, eine höhere Zugriffsebene erforderlich ist, müssen Sie mit dem Microsoft-Support zusammenarbeiten. Über den Support gibt es eine Methode zur vorübergehenden Bereitstellung von uneingeschränktem Administratorzugriff für das System, um komplexere Operationen durchführen zu können. 

## <a name="identity-considerations"></a>Überlegungen zur Identität

### <a name="choose-identity-provider"></a>Auswählen des Identitätsanbieters
Sie müssen bedenken, welchen Identitätsanbieter Sie für die Bereitstellung von Azure Stack nutzen möchten, Azure AD oder AD FS. Sie können die Identitätsanbieter nach der Bereitstellung nicht wechseln, ohne das System komplett neu bereitstellen zu müssen. Wenn Sie Azure AD-Kontos nicht besitzen und ein Konto verwenden, die Sie von Ihrem Cloud-Service-Anbieter bereitgestellt und Sie verwenden möchten, wechseln Sie Anbieter, und verwenden einen anderen Azure AD-Konto, die an diesem Punkt müssen, wenden Sie sich an Ihre Lösungsanbieter, um die f-Lösung erneut bereitstellen oder Sie Ihre Kosten.

Ihre Wahl des Identitätsanbieters hat keinen Einfluss auf virtuelle Computer des Mandanten, auf das Identitätssystem sowie auf von ihnen verwendete Konten oder darauf, ob sie einer Active Directory-Domäne usw. beitreten können. Das ist was anderes.

Im Artikel [Verbindungsmodell von in Azure Stack integrierten Systemen](./azure-stack-connection-models.md) erfahren Sie mehr über die Auswahl eines Identitätsanbieters.

### <a name="ad-fs-and-graph-integration"></a>Integration von AD FS und Graph
Wenn Sie die Bereitstellung von Azure Stack mithilfe von AD FS als Identitätsanbieter gewählt haben, müssen Sie die AD FS-Instanz für Azure Stack über eine Verbundvertrauensstellung mit einer vorhandenen AD FS-Instanz integrieren. Dies gestattet Identitäten in einer vorhandenen Active Directory-Gesamtstruktur die Authentifizierung mit Ressourcen in Azure Stack.

Sie können auch den Graph-Dienst in Azure Stack mit dem vorhandenen Active Directory integrieren. Dadurch können Sie die rollenbasierte Zugriffssteuerung (RBAC) in Azure Stack verwalten. Wenn der Zugriff auf eine Ressource delegiert wird, sucht die Graph-Komponente das Benutzerkonto mithilfe des LDAP-Protokolls in der vorhandenen Active Directory-Gesamtstruktur.

Das folgende Diagramm zeigt den integrierten Datenverkehrsfluss von AD FS und Graph.
![Diagramm mit Datenverkehrsfluss für AD FS und Graph](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Lizenzierungsmodell
Sie müssen entscheiden, welches Lizenzierungsmodell Sie verwenden möchten. Die verfügbaren Optionen hängen davon ab, ob Sie Azure Stack mit einer Internetverbindung bereitstellen:
- Für eine [verbundene Bereitstellung](azure-stack-connected-deployment.md) können Sie entweder die nutzungs- oder die kapazitätsbasierte Lizenzierung auswählen. Die nutzungsbasierte Lizenzierung erfordert eine Verbindung mit Azure, um die Nutzungsdaten zu melden, die dann über Azure Commerce abgerechnet werden. 
- Bei einer [Bereitstellung ohne Internetverbindung](azure-stack-disconnected-deployment.md) wird nur die kapazitätsbasierte Lizenzierung unterstützt. 

Weitere Informationen zu den Lizenzierungsmodellen finden Sie unter [Microsoft Azure Stack – Verpackung und Preise](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Entscheidungen zur Benennung

Sie müssen entsprechende Überlegungen zur Planung Ihres Azure Stack-Namespace anstellen, insbesondere zum Regionsnamen und zum externen Domänennamen. Der externe vollqualifizierte Domänenname (FQDN) der Azure Stack-Bereitstellung für öffentliche Endpunkte ist eine Kombination aus den folgenden beiden Namen: &lt;*Region*&gt;.&lt;*FQDN*&gt;, z.B. *east.cloud.fabrikam.com*. In diesem Beispiel wären die Azure Stack-Portale unter den folgenden URLs verfügbar:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Der Name der Region für Ihre Azure Stack-Bereitstellung muss eindeutig sein. Er wird in den Portaladressen angezeigt. 

In der folgenden Tabelle sind diese Entscheidungen zur Domänenbenennung zusammengefasst.

| NAME | BESCHREIBUNG | 
| -------- | ------------- | 
|Regionsname | Der Name Ihres ersten Azure Stack-Region. Dieser Name wird als FQDN-Komponente für die öffentlichen virtuellen IP-Adressen (VIPs) verwendet, die von Azure Stack verwaltet werden. Der Regionsname gibt in der Regel eine physische Standort-ID an, z. B. den Standort des Rechenzentrums.<br><br>Die Regionsname darf nur aus Buchstaben und Ziffern zwischen 0 und 9 bestehen. Sonderzeichen wie „-“ oder „#“ usw. sind unzulässig.| 
| Externer Domänenname | Der Name der DNS-Zone (Domain Name System) für Endpunkte mit externen VIPs. Wird im FQDN für diese öffentlichen VIPs verwendet. | 
| Privater (interner) Domänenname | Der Name der Domäne (und internen DNS-Zone), die in Azure Stack zur Infrastrukturverwaltung erstellt wurde. 
| | |

## <a name="certificate-requirements"></a>Zertifikatanforderungen

Für die Bereitstellung müssen Sie SSL-Zertifikate (Secure Sockets Layer) für öffentliche Endpunkte anbieten. Auf allgemeiner Ebene haben Zertifikate die folgenden Anforderungen:

- Sie können ein einzelnes Platzhalterzertifikat oder eine Reihe von dedizierten Zertifikaten verwenden und dann Platzhalter nur für Endpunkte wie Speicher und Key Vault einsetzen.
- Zertifikate können von einer öffentlichen vertrauenswürdigen Zertifizierungsstelle oder einer vom Kunden verwalteten Zertifizierungsstelle ausgestellt werden.

Weitere Informationen darüber, welche PKI-Zertifikate (Public Key-Infrastruktur) für die Bereitstellung von Azure Stack erforderlich sind und wie Sie diese erhalten, finden Sie unter [Anforderungen für PKI-Zertifikate für Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Die Informationen zu PKI-Zertifikaten in diesem Artikel dienen nur als allgemeine Leitlinien. Bevor Sie PKI-Zertifikate für Azure Stack erwerben, wenden Sie sich an Ihren OEM-Hardwarepartner. Er stellt Ihnen ausführliche Anleitungen und Anforderungen für Zertifikate bereit.


## <a name="time-synchronization"></a>Zeitsynchronisierung
Sie müssen einen bestimmten Zeitserver auswählen, mit dessen Hilfe Azure Stack synchronisiert wird.  Die Uhrzeitsynchronisierung ist für Azure Stack und seine Infrastrukturrollen von entscheidender Bedeutung, da sie zur Generierung von Kerberos-Tickets dient, mit denen sich interne Dienste gegenseitig authentifizieren.

Sie müssen eine IP-Adresse für den Zeitsynchronisierungsserver angeben. Wenngleich die meisten Komponenten in der Infrastruktur eine URL auflösen können, können einige nur IP-Adressen unterstützen. Wenn Sie die Bereitstellungsoption „Getrennt“ verwenden, müssen Sie einen Zeitserver in Ihrem Unternehmensnetzwerk angeben, von dem Sie sicher sind, dass er über das Infrastrukturnetzwerk in Azure Stack erreichbar ist.

## <a name="connect-azure-stack-to-azure"></a>Herstellen einer Verbindung von Azure Stack zu Azure

Für Hybrid Cloud-Szenarien müssen Sie planen, wie Sie Azure Stack mit Azure verbinden möchten. Es gibt zwei unterstützte Methoden, um virtuelle Netzwerke in Azure Stack mit virtuellen Netzwerken in Azure zu verbinden: 
- **Standort zu Standort**: Eine VPN-Verbindung (virtuelles privates Netzwerk) über IPsec (IKE v1 und IKE v2). Diese Art von Verbindung erfordert ein VPN-Gerät oder einen Routing- und RAS-Dienst (RRAS). Weitere Informationen zu VPN-Gateways in Azure finden Sie unter [Informationen zu VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Die Kommunikation über diesen Tunnel ist verschlüsselt und sicher. Die Bandbreite ist jedoch durch den maximalen Durchsatz des Tunnels (100-200 MBit/s) beschränkt.
- **Ausgehende NAT**: Standardmäßig verfügen alle virtuellen Computer in Azure Stack mittels ausgehender NAT über Verbindungen mit externen Netzwerken. Jedem in Azure Stack erstellten virtuellen Netzwerk wird eine öffentliche IP-Adresse zugewiesen. Ganz gleich, ob dem virtuellen Computer eine öffentliche IP-Adresse direkt zugewiesen wird oder er sich hinter einem Load Balancer mit einer öffentlichen IP-Adresse befindet, verfügt er mittels der ausgehenden NAT über den ausgehenden Zugriff, indem die virtuelle IP-Adresse des virtuellen Netzwerks verwendet wird. Dies funktioniert nur für die Kommunikation, die vom virtuellen Computer eingeleitet wird und die für externe Netzwerke (Internet oder Intranet) bestimmt ist. Dies kann nicht für die Kommunikation mit dem virtuellen Computer von außerhalb verwendet werden.

### <a name="hybrid-connectivity-options"></a>Optionen der Hybridkonnektivität

Für die Hybridkonnektivität ist es wichtig, die Art und den Ort der anzubietenden Bereitstellung zu berücksichtigen. Sie müssen prüfen, ob Sie den Netzwerkdatenverkehr pro Mandant isolieren müssen. Zudem müssen Sie überlegen, ob Sie eine Bereitstellung über Intranet oder Internet verwenden.

- **Azure Stack für einzelnen Mandanten**: Eine Azure Stack-Bereitstellung, die zumindest aus Netzwerksicht so aussieht, als ob es sich um einen Mandanten handelt. Es können viele Mandantenabonnements vorliegen, aber wie bei jedem Intranetdienst verläuft sämtlicher Datenverkehr über dieselben Netzwerke. Der Netzwerkdatenverkehr für ein Abonnement wird über dieselbe Netzwerkverbindung wie für ein anderes Abonnement übertragen und muss nicht über einen verschlüsselten Tunnel isoliert werden.

- **Azure Stack für mehrere Mandanten**: Eine Azure Stack-Bereitstellung, bei der der an Netzwerke, die außerhalb von Azure Stack verlaufen, gebundene Datenverkehr der einzelnen Mandantenabonnements vom Netzwerkdatenverkehr anderer Mandanten isoliert werden muss.
 
- **Intranetbereitstellung**: Eine Azure Stack-Bereitstellung, die auf einem Unternehmensintranet basiert und sich in der Regel in einem privaten IP-Adressraum und hinter einer oder mehreren Firewalls befindet. Die öffentlichen IP-Adressen sind nicht wirklich öffentlich, da sie nicht direkt über das öffentliche Internet weitergeleitet werden können.

- **Internetbereitstellung**: Eine Azure Stack-Bereitstellung, die mit dem öffentlichen Internet verbunden ist und über das Internet zugängliche öffentliche IP-Adressen für den öffentlichen VIP-Bereich verwendet. Die Bereitstellung kann sich weiterhin hinter einer Firewall befinden, aber der öffentliche VIP-Adressbereich ist direkt über das öffentliche Internet und Azure erreichbar.
 
In der folgenden Tabelle sind die Szenarien zur Hybridkonnektivität mit den jeweiligen Vor- und Nachteilen sowie mit Anwendungsfällen zusammengefasst.

| Szenario | Konnektivitätsmethode | Vorteile | Nachteile | Geeignet für |
| -- | -- | --| -- | --|
| Azure Stack für einzelnen Mandanten, Intranetbereitstellung | Ausgehende NAT | Höhere Bandbreite für schnellere Übertragungen Einfach zu implementieren, keine Gateways erforderlich | Datenverkehr nicht verschlüsselt, keine Isolierung oder Verschlüsselung außerhalb des Stapels | Unternehmensbereitstellungen, bei denen alle Mandanten gleichermaßen vertrauenswürdig sind<br><br>Unternehmen, die über eine Azure ExpressRoute-Verbindung zu Azure verfügen |
| Azure Stack für mehrere Mandanten, Intranetbereitstellung | Standort-zu-Standort-VPN-Verbindung | Datenverkehr aus dem VNet des Mandanten zum Ziel ist sicher | Bandbreite durch Standort-zu-Standort-VPN-Tunnel begrenzt<br><br>Gateway im virtuellen Netzwerk und ein VPN-Gerät im Zielnetzwerk erforderlich | Unternehmensbereitstellungen, in denen gewisser Mandantendatenverkehr vor anderen Mandanten geschützt werden muss |
| Azure Stack für einzelnen Mandanten, Internetbereitstellung | Ausgehende NAT | Höhere Bandbreite für schnellere Übertragungen | Datenverkehr nicht verschlüsselt, keine Isolierung oder Verschlüsselung außerhalb des Stapels | Hostingszenarien, in denen Mandanten ihre eigene Azure Stack-Bereitstellung und eine dedizierte Verbindung mit der Azure Stack-Umgebung erhalten Beispiel: ExpressRoute und MPLS (Multiprotocol Label Switching)
| Azure Stack für mehrere Mandanten, Internetbereitstellung | Standort-zu-Standort-VPN-Verbindung | Datenverkehr aus dem VNet des Mandanten zum Ziel ist sicher | Bandbreite durch Standort-zu-Standort-VPN-Tunnel begrenzt<br><br>Gateway im virtuellen Netzwerk und ein VPN-Gerät im Zielnetzwerk erforderlich | Hostingszenarien, in denen der Anbieter eine Cloud für mehrere Mandanten ohne gegenseitiges Vertrauen bereitstellen möchte und der Datenverkehr verschlüsselt werden muss.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Verwenden von ExpressRoute

Sie können Azure Stack über [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) für Szenarien mit Intranetbereitstellungen für einzelne und mehrere Mandanten mit Azure verbinden. Sie benötigen eine bereitgestellte ExpressRoute-Verbindung über einen [Konnektivitätsanbieter](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Das folgende Diagramm zeigt ExpressRoute für ein Szenario mit einzelnem Mandanten (dabei stellt die Verbindung des Kunden die ExpressRoute-Verbindung dar).

![Diagramm mit ExpressRoute-Szenario mit einzelnem Mandanten](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Das folgende Diagramm zeigt ExpressRoute für ein Szenario mit mehreren Mandanten.

![Diagramm mit ExpressRoute-Szenario mit mehreren Mandanten](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externe Überwachung
Um eine Gesamtübersicht über alle Warnungen Ihrer Azure Stack-Bereitstellung sowie den zugehörigen Geräten zu erhalten und Warnungen zur Ticketerstellung in vorhandene Workflows zum IT-Service-Management zu integrieren, können Sie [Azure Stack mit Überwachungslösungen externer Rechenzentren integrieren](azure-stack-integrate-monitor.md).

Der in der Azure Stack-Lösung enthaltene Hardwarelebenszyklushost ist ein Computer, der sich außerhalb von Azure Stack befindet, und vom OEM-Anbieter bereitgestellte Verwaltungstools für Hardware ausführt. Sie können diese Tools oder andere Lösungen verwenden, die direkt mit vorhandenen Überwachungslösungen in Ihr Rechenzentrum integriert werden.

In der folgenden Tabelle ist die Liste der derzeit verfügbaren Optionen zusammengefasst.

| Bereich | Externe Überwachungslösung |
| -- | -- |
| Azure Stack-Software | [Azure Stack Management Pack für Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios-Plug-In](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-basierte API-Aufrufe | 
| Physische Server (BMCs über IPMI) | OEM-Hardware – Operations Manager-Lieferantenverwaltungspaket<br>Vom OEM-Hardwareanbieter bereitgestellte Lösung<br>Plug-Ins des Hardwareanbieters Nagios.<br>Vom OEM-Partner unterstützte Überwachungslösung (enthalten) | 
| Netzwerkgeräte (SNMP) | Operations Manager-Netzwerkgeräteermittlung<br>Vom OEM-Hardwareanbieter bereitgestellte Lösung<br>Nagios Switch-Plug-In |
| Systemüberwachung für Mandantenabonnements | [System Center Management Pack für Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Beachten Sie folgende Punkte:
- Die von Ihnen verwendete Lösung darf keine Agenten verwenden. Sie können keine Agenten von Drittanbietern in Azure Stack-Komponenten installieren. 
- Wenn Sie System Center Operations Manager verwenden möchten, erfordert dies Operations Manager 2012 R2 oder Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Backup und Notfallwiederherstellung

Die Planung der Sicherung und Notfallwiederherstellung umfasst das Planen für die zugrunde liegende Azure Stack-Infrastruktur, die virtuelle IaaS-Computer und PaaS-Dienste hostet, sowie für Mandantenanwendungen und -daten. Sie müssen diese separat planen.

### <a name="protect-infrastructure-components"></a>Schützen von Infrastrukturkomponenten

Sie können Infrastrukturkomponenten von Azure Stack in einer von Ihnen angegebenen SMB-Freigabe [sichern](azure-stack-backup-back-up-azure-stack.md):

- Sie benötigen eine externe SMB-Dateifreigabe auf einem vorhandenen Windows-basierten Dateiserver oder auf einem Gerät eines Drittanbieters.
- Sie sollten dieselbe Freigabe für die Sicherung von Netzwerkswitches und des Hardwarelebenszyklushosts verwenden. Ihr OEM-Hardwareanbieter unterstützt Sie dabei, eine Anleitung zum Sichern und Wiederherstellen dieser Komponenten bereitzustellen, da sich diese außerhalb von Azure Stack befinden. Sie sind dafür zuständig, die Sicherungsworkflows auf Basis der Empfehlungen des OEM-Anbieters auszuführen.

Wenn ein schwerwiegender Datenverlust auftritt, können Sie die Sicherung der Infrastruktur dazu verwenden, um für die Bereitstellungsdaten (z. B. Bereitstellungseingaben und -IDs, Dienstkonten, Stammzertifikat der Zertifizierungsstelle, Verbundressourcen (in Bereitstellungen ohne Verbindung zum Internet), Pläne, Angebote, Abonnements, Kontingente, RBAC-Richtlinien- und -Rollenzuweisungen sowie geheime Key Vault-Schlüssel) ein erneutes Seeding durchzuführen.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Schützen von Mandantenanwendungen auf virtuellen IaaS-Computern

Azure Stack sichert keine Mandantenanwendungen und -daten. Sie müssen die Sicherung und Notfallwiederherstellung auf einem Ziel außerhalb von Azure Stack planen. Der Mandantenschutz ist eine vom Mandanten gesteuerte Aktivität. Für virtuelle IaaS-Computer können Mandanten In-Guest-Technologien verwenden, um Dateiordner, Anwendungsdaten und den Systemstatus zu schützen. Als Unternehmen oder Dienstanbieter möchten Sie jedoch möglicherweise eine Sicherungs- und Wiederherstellungslösung anbieten, die sich in demselben Rechenzentrum oder extern in einer Cloud befindet.

Zur Sicherung von virtuellen IaaS-Computern mit Linux oder Windows müssen Sie Sicherungsprodukte mit Zugriff auf das Gastbetriebssystem verwenden, um den Datei-, Ordner- und Systemstatus sowie Anwendungsdaten zu schützen. Sie können Azure Backup, System Center Data Center Protection Manager oder unterstützte Produkte von Drittanbietern verwenden.

Sie können Azure Site Recovery oder unterstützte Produkte von Drittanbietern verwenden, um Daten für den Notfall an einen sekundären Standort zu replizieren und Anwendungsfailover zu orchestrieren. Darüber hinaus können Anwendungen, die die native Replikation unterstützen (z.B. Microsoft SQL Server), Daten an einen anderen Speicherort replizieren, an dem die Anwendung ausgeführt wird.

## <a name="learn-more"></a>Weitere Informationen

- Informationen zu Anwendungsfällen, zum Erwerb, zu Partnern und zu OEM-Hardwareanbietern finden Sie auf der Produktseite von [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Informationen zur Roadmap und geografischen Verfügbarkeit für integrierte Azure Stack-Systeme finden Sie im Whitepaper: [Azure Stack: Eine Erweiterung von Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Nächste Schritte
[Verbindungsmodelle für die Azure Stack-Bereitstellung](azure-stack-connection-models.md)
