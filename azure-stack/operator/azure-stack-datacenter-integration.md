---
title: Überlegungen zur Planung der Rechenzentrumsintegration für in Azure Stack Hub integrierte Systeme
description: Erfahren Sie, wie Sie die Rechenzentrumsintegration für in Azure Stack Hub integrierte Systeme planen und vorbereiten.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2019
ms.openlocfilehash: c8ea0ea53574132d144ad7e205f78c5bebc41226
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295323"
---
# <a name="datacenter-integration-planning-considerations-for-azure-stack-hub-integrated-systems"></a>Überlegungen zur Planung der Rechenzentrumsintegration für in Azure Stack Hub integrierte Systeme

Wenn Sie an einem in Azure Stack Hub integrierten System interessiert sind, müssen Sie die wichtigsten Aspekte bei der Planung der Bereitstellung verstehen und wissen, wie das System in Ihr Rechenzentrum passt. Dieser Artikel bietet einen allgemeinen Überblick über diese Aspekte, damit Sie wichtige Infrastrukturentscheidungen für Ihre in Azure Stack Hub integrierten Systeme treffen können. Das Verständnis dieser Aspekte hilft Ihnen bei der Zusammenarbeit mit Ihrem OEM-Hardwareanbieter, wenn dieser Azure Stack Hub in Ihrem Rechenzentrum bereitstellt.  

> [!NOTE]  
> In Azure Stack Hub integrierte Systeme können nur bei autorisierten Hardwareanbietern bezogen werden.

Um Azure Stack Hub bereitzustellen, müssen Sie Ihrem Lösungsanbieter vor Beginn der Bereitstellung Planungsinformationen bereitstellen, damit der Prozess schnell und reibungslos abläuft. Die benötigten Informationen reichen von Netzwerk-, Sicherheits- und Identitätsinformationen bis hin zu vielen wichtigen Entscheidungen, die möglicherweise Wissen aus vielen verschiedenen Bereichen und von verschiedenen Entscheidungsträgern erfordern. Sie benötigen Mitarbeiter aus mehreren Teams Ihres Unternehmens, um sicherzustellen, dass Sie vor Beginn der Bereitstellung über alle erforderlichen Informationen verfügen. Es kann ratsam sein, dass Sie sich mit Ihrem Hardwareanbieter abstimmen, während Sie diese Informationen sammeln, da er Ihnen ggf. hilfreiche Ratschläge geben kann.

Während Sie die erforderlichen Informationen recherchieren und sammeln, müssen Sie möglicherweise vor der Bereitstellung einige Konfigurationsänderungen an Ihrer Netzwerkumgebung vornehmen. Zu diesen Änderungen gehören die Reservierung von IP-Adressräumen für die Azure Stack Hub-Lösung sowie die Konfiguration Ihrer Router, Switches und Firewalls zur Vorbereitung der Konnektivität mit den Switches der neuen Azure Stack Hub-Lösung. Sichern Sie sich bei Ihrer Planung die Unterstützung der Fachbereichsexperten.

## <a name="capacity-planning-considerations"></a>Aspekte der Kapazitätsplanung
Beim Evaluieren einer Azure Stack Hub-Lösung für den Kauf müssen Sie Entscheidungen in Bezug auf die Aspekte der Hardwarekonfiguration treffen, die eine direkte Auswirkung auf die Gesamtkapazität der Azure Stack Hub-Lösung haben. Hierzu gehört die übliche Auswahl von CPU, Arbeitsspeicherdichte, Speicherkonfiguration und Gesamtumfang der Lösung (z. B. Anzahl von Servern). Im Gegensatz zu einer herkömmlichen Virtualisierungslösung gilt die einfache Arithmetik dieser Komponenten zur Ermittlung der nutzbaren Kapazität nicht. Der erste Grund ist, dass Azure Stack Hub so aufgebaut ist, dass die Infrastruktur- bzw. Verwaltungskomponenten in der Lösung selbst gehostet werden. Der zweite Grund ist, dass ein Teil der Lösungskapazität zur Unterstützung der Resilienz reserviert ist. Es geht um die Aktualisierung der Lösungssoftware auf eine Weise, bei der die Beeinträchtigung von Mandantenworkloads verringert wird.

Mit der [Kalkulationstabelle des Azure Stack Hub-Kapazitätsplanungstools](https://aka.ms/azstackcapacityplanner) haben Sie zwei Optionen, um fundierte Entscheidungen zur Planung der Kapazität zu treffen. Die erste Option besteht darin, ein Hardwareangebot auszuwählen und zu versuchen, eine geeignete Kombination von Ressourcen zu ermitteln. Bei der zweiten Option wird die Workload definiert, die mit Azure Stack Hub ausgeführt werden soll, um die verfügbaren Hardware-SKUs anzuzeigen, die unterstützt werden können. Schließlich dient das Tool auch als Hilfe beim Treffen von Entscheidungen in Bezug auf die Azure Stack Hub-Planung und -Konfiguration.

Es soll aber nicht als Ersatz für Ihre eigenen Untersuchungen und Analysen dienen. Microsoft gewährt für die mit dem Tool bereitgestellten Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.

## <a name="management-considerations"></a>Überlegungen zur Verwaltung
Azure Stack Hub ist ein geschlossenes System, bei dem die Infrastruktur aus Berechtigungs- und Netzwerksicht abgeriegelt ist. Dabei werden Netzwerk-Zugriffssteuerungslisten (ACLs) angewendet, um sämtlichen nicht autorisierten eingehenden Datenverkehr und sämtliche unnötige Kommunikation zwischen Infrastrukturkomponenten zu blockieren. Hierdurch wird für nicht autorisierte Benutzer der Zugriff auf das System erschwert.

Für die tägliche Verwaltung und den Betrieb besteht kein uneingeschränkter Administratorzugriff auf die Infrastruktur. Azure Stack Hub-Operatoren müssen das System über das Administratorportal oder mithilfe von Azure Resource Manager (über PowerShell oder die REST-API) verwalten. Es besteht kein Zugriff auf das System durch andere Verwaltungstools wie Hyper-V-Manager oder Failovercluster-Manager. Um den Schutz des Systems zu gewährleisten, können keine Softwareprodukte von Drittanbietern (z. B. Agents) innerhalb der Komponenten der Azure Stack Hub-Infrastruktur installiert werden. Die Interoperabilität mit externer Verwaltungs- und Sicherheitssoftware erfolgt über PowerShell oder die REST-API.

Wenden Sie sich an den Microsoft-Support, falls für die Behandlung von Problemen, die nicht durch Schritte zur Warnungskorrektur behoben werden, eine höhere Zugriffsebene erforderlich ist. Über den Support ist eine Methode zur vorübergehenden Bereitstellung von uneingeschränktem Administratorzugriff für das System verfügbar, um erweiterte Vorgänge durchführen zu können.

## <a name="identity-considerations"></a>Überlegungen zur Identität

### <a name="choose-identity-provider"></a>Auswählen des Identitätsanbieters
Sie müssen sich überlegen, welchen Identitätsanbieter Sie für die Bereitstellung von Azure Stack Hub nutzen möchten: Azure AD oder AD FS. Sie können die Identitätsanbieter nach der Bereitstellung nicht wechseln, ohne das System komplett neu bereitstellen zu müssen. Wenn Sie nicht im Besitz des Azure AD-Kontos sind und ein von Ihrem Cloudlösungsanbieter bereitgestelltes Konto verwenden und dann den Anbieter wechseln und ein anderes Azure AD-Konto nutzen möchten, müssen Sie sich an Ihren Lösungsanbieter wenden, damit er die Lösung auf Ihre Kosten für Sie erneut bereitstellt.

Die Wahl des Identitätsanbieters hat keinen Einfluss auf virtuelle Mandantencomputer (VMs), auf das dafür verwendete Identitätssystem, die verwendeten Konten, die Möglichkeit zum Beitritt zu einer Active Directory-Domäne usw. Dies sind separate Dinge.

Im Artikel [Verbindungsmodell von in Azure Stack Hub integrierten Systemen](./azure-stack-connection-models.md) erfahren Sie mehr über die Auswahl eines Identitätsanbieters.

### <a name="ad-fs-and-graph-integration"></a>Integration von AD FS und Graph
Wenn Sie sich für eine Bereitstellung von Azure Stack Hub mit AD FS als Identitätsanbieter entschieden haben, müssen Sie die AD FS-Instanz für Azure Stack Hub über eine Verbundvertrauensstellung mit einer vorhandenen AD FS-Instanz integrieren. Diese Integration gestattet Identitäten in einer vorhandenen Active Directory-Gesamtstruktur die Authentifizierung mit Ressourcen in Azure Stack Hub.

Es ist außerdem möglich, den Graph-Dienst mit der vorhandenen Active Directory-Instanz in Azure Stack Hub zu integrieren. Dank dieser Integration können Sie die rollenbasierte Zugriffssteuerung (RBAC) in Azure Stack Hub verwalten. Wenn der Zugriff auf eine Ressource delegiert wird, sucht die Graph-Komponente das Benutzerkonto mithilfe des LDAP-Protokolls in der vorhandenen Active Directory-Gesamtstruktur.

Das folgende Diagramm zeigt den integrierten Datenverkehrsfluss von AD FS und Graph.
![Diagramm mit Datenverkehrsfluss für AD FS und Graph](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Lizenzierungsmodell
Sie müssen entscheiden, welches Lizenzierungsmodell Sie verwenden möchten. Die verfügbaren Optionen hängen davon ab, ob Sie Azure Stack Hub mit einer Internetverbindung bereitstellen:
- Für eine [verbundene Bereitstellung](azure-stack-connected-deployment.md) können Sie entweder die nutzungs- oder die kapazitätsbasierte Lizenzierung auswählen. Die nutzungsbasierte Lizenzierung erfordert eine Verbindung mit Azure, um die Nutzungsdaten zu melden, die dann über Azure Commerce abgerechnet werden. 
- Bei einer [Bereitstellung ohne Internetverbindung](azure-stack-disconnected-deployment.md) wird nur die kapazitätsbasierte Lizenzierung unterstützt. 

Weitere Informationen zu den Lizenzierungsmodellen finden Sie unter [Microsoft Azure Stack Hub – Pakete und Preise](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Entscheidungen zur Benennung

Sie müssen entsprechende Überlegungen zur Planung Ihres Azure Stack Hub-Namespace anstellen, insbesondere zum Regionsnamen und zum externen Domänennamen. Der externe vollqualifizierte Domänenname (FQDN) der Azure Stack Hub-Bereitstellung für öffentliche Endpunkte ist eine Kombination aus den folgenden beiden Namen: &lt;*Region*&gt;.&lt;*FQDN*&gt;, z.B. *east.cloud.fabrikam.com*. In diesem Beispiel wären die Azure Stack Hub-Portale unter den folgenden URLs verfügbar:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Der Name der Region für Ihre Azure Stack Hub-Bereitstellung muss eindeutig sein. Er wird in den Portaladressen angezeigt. 

In der folgenden Tabelle sind diese Entscheidungen zur Domänenbenennung zusammengefasst.

| Name | BESCHREIBUNG | 
| -------- | ------------- | 
|Regionsname | Der Name Ihrer ersten Azure Stack Hub-Region. Dieser Name wird als FQDN-Komponente für die öffentlichen virtuellen IP-Adressen (VIPs) verwendet, die von Azure Stack Hub verwaltet werden. Der Regionsname gibt in der Regel eine physische Standort-ID an, z. B. den Standort des Rechenzentrums.<br><br>Die Regionsname darf nur aus Buchstaben und Ziffern zwischen 0 und 9 bestehen. Es sind keine Sonderzeichen (z. B. `-`, `#` usw.) zulässig.| 
| Externer Domänenname | Der Name der DNS-Zone (Domain Name System) für Endpunkte mit externen VIPs. Wird im FQDN für diese öffentlichen VIPs verwendet. | 
| Privater (interner) Domänenname | Der Name der Domäne (und internen DNS-Zone), die in Azure Stack Hub zur Infrastrukturverwaltung erstellt wurde.
| | |

## <a name="certificate-requirements"></a>Zertifikatanforderungen

Für die Bereitstellung müssen Sie SSL-Zertifikate (Secure Sockets Layer) für öffentliche Endpunkte anbieten. Auf allgemeiner Ebene haben Zertifikate die folgenden Anforderungen:

- Sie können ein einzelnes Platzhalterzertifikat oder eine Reihe von dedizierten Zertifikaten verwenden und dann Platzhalter nur für Endpunkte wie Speicher und Key Vault einsetzen.
- Zertifikate können von einer öffentlichen vertrauenswürdigen Zertifizierungsstelle oder einer vom Kunden verwalteten Zertifizierungsstelle ausgestellt werden.

Weitere Informationen dazu, welche PKI-Zertifikate (Public Key-Infrastruktur) für die Bereitstellung von Azure Stack Hub erforderlich sind und wie Sie diese erhalten, finden Sie unter [Anforderungen für PKI-Zertifikate für Azure Stack Hub](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Die Informationen zu PKI-Zertifikaten in diesem Artikel dienen nur als allgemeine Leitlinien. Bevor Sie PKI-Zertifikate für Azure Stack Hub erwerben, wenden Sie sich an Ihren OEM-Hardwarepartner. Dort erhalten Sie ausführliche Anleitungen und Anforderungen für Zertifikate.


## <a name="time-synchronization"></a>Zeitsynchronisierung
Sie müssen einen bestimmten Zeitserver auswählen, der zum Synchronisieren von Azure Stack Hub verwendet wird. Die Zeitsynchronisierung ist für Azure Stack Hub und die zugehörigen Infrastrukturrollen von entscheidender Bedeutung, da sie zum Generieren von Kerberos-Tickets verwendet wird. Kerberos-Tickets werden genutzt, um interne Dienste untereinander zu authentifizieren.

Sie müssen eine IP-Adresse für den Zeitsynchronisierungsserver angeben. Die meisten Komponenten der Infrastruktur können zwar eine URL auflösen, aber einige unterstützen nur IP-Adressen. Wenn Sie die Bereitstellungsoption „Getrennt“ verwenden, müssen Sie einen Zeitserver in Ihrem Unternehmensnetzwerk angeben, von dem Sie sicher sind, dass er über das Infrastrukturnetzwerk in Azure Stack Hub erreichbar ist.

## <a name="connect-azure-stack-hub-to-azure"></a>Herstellen einer Verbindung zwischen Azure Stack Hub und Azure

Für Hybrid Cloud-Szenarien müssen Sie planen, wie Sie Azure Stack Hub mit Azure verbinden möchten. Es gibt zwei unterstützte Methoden, um virtuelle Netzwerke in Azure Stack Hub mit virtuellen Netzwerken in Azure zu verbinden:

- **Site-to-Site**: Eine VPN-Verbindung (virtuelles privates Netzwerk) über IPsec (IKE v1 und IKE v2). Diese Art von Verbindung erfordert ein VPN-Gerät oder einen Routing- und RAS-Dienst (RRAS). Weitere Informationen zu VPN-Gateways in Azure finden Sie unter [Informationen zu VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Die Kommunikation über diesen Tunnel ist verschlüsselt und sicher. Die Bandbreite ist jedoch durch den maximalen Durchsatz des Tunnels (100-200 MBit/s) beschränkt.

- **Ausgehende NAT**: Standardmäßig verfügen alle VMs in Azure Stack Hub mittels ausgehender NAT über Verbindungen mit externen Netzwerken. Jedem in Azure Stack Hub erstellten virtuellen Netzwerk wird eine öffentliche IP-Adresse zugewiesen. Unabhängig davon, ob der VM eine öffentliche IP-Adresse direkt zugewiesen wird oder sie sich hinter einem Load Balancer mit einer öffentlichen IP-Adresse befindet, verfügt sie per ausgehender NAT über ausgehenden Zugriff, indem die virtuelle IP-Adresse des virtuellen Netzwerks verwendet wird. Diese Methode funktioniert nur für die Kommunikation, die von der VM eingeleitet wird und die für externe Netzwerke (Internet oder Intranet) bestimmt ist. Dieser Ansatz kann nicht für die Kommunikation mit der VM von außerhalb verwendet werden.

### <a name="hybrid-connectivity-options"></a>Optionen der Hybridkonnektivität

Für die Hybridkonnektivität ist es wichtig, die Art und den Ort der anzubietenden Bereitstellung zu berücksichtigen. Sie müssen prüfen, ob Sie den Netzwerkdatenverkehr pro Mandant isolieren müssen. Zudem müssen Sie überlegen, ob Sie eine Bereitstellung über Intranet oder Internet verwenden.

- **Azure Stack Hub für einzelnen Mandanten**: Eine Azure Stack Hub-Bereitstellung, die zumindest aus Netzwerksicht so aussieht, als ob es sich um einen Mandanten handelt. Es können viele Mandantenabonnements vorliegen, aber wie bei jedem Intranetdienst verläuft sämtlicher Datenverkehr über dieselben Netzwerke. Der Netzwerkdatenverkehr für ein Abonnement wird über dieselbe Netzwerkverbindung wie für ein anderes Abonnement übertragen und muss nicht über einen verschlüsselten Tunnel isoliert werden.

- **Azure Stack Hub für mehrere Mandanten**: Eine Azure Stack Hub-Bereitstellung, bei der der Datenverkehr jedes Mandantenabonnements, das an Netzwerke außerhalb von Azure Stack Hub gebunden ist, vom Netzwerkverkehr anderer Mandanten isoliert werden muss.
 
- **Intranetbereitstellung**: Eine Azure Stack Hub-Bereitstellung, die auf einem Unternehmensintranet basiert und sich in der Regel in einem privaten IP-Adressraum und hinter mindestens einer Firewall befindet. Die öffentlichen IP-Adressen sind nicht wirklich öffentlich, da sie nicht direkt über das öffentliche Internet weitergeleitet werden können.

- **Internetbereitstellung**: Eine Azure Stack Hub-Bereitstellung, die mit dem öffentlichen Internet verbunden ist und über das Internet zugängliche öffentliche IP-Adressen für den öffentlichen VIP-Bereich verwendet. Die Bereitstellung kann sich weiterhin hinter einer Firewall befinden, aber der öffentliche VIP-Adressbereich ist direkt über das öffentliche Internet und Azure erreichbar.
 
In der folgenden Tabelle sind die Szenarien zur Hybridkonnektivität mit den jeweiligen Vor- und Nachteilen sowie mit Anwendungsfällen zusammengefasst.

| Szenario | Konnektivitätsmethode | Vorteile | Nachteile | Geeignet für |
| -- | -- | --| -- | --|
| Azure Stack Hub für einzelnen Mandanten, Intranetbereitstellung | Ausgehende NAT | Höhere Bandbreite für schnellere Übertragungen Einfach zu implementieren, keine Gateways erforderlich | Datenverkehr nicht verschlüsselt, keine Isolierung oder Verschlüsselung außerhalb des Stapels | Unternehmensbereitstellungen, bei denen alle Mandanten gleichermaßen vertrauenswürdig sind<br><br>Unternehmen, die über eine Azure ExpressRoute-Verbindung zu Azure verfügen |
| Azure Stack Hub für mehrere Mandanten, Intranetbereitstellung | Standort-zu-Standort-VPN-Verbindung | Datenverkehr aus dem VNet des Mandanten zum Ziel ist sicher | Bandbreite durch Standort-zu-Standort-VPN-Tunnel begrenzt<br><br>Gateway im virtuellen Netzwerk und ein VPN-Gerät im Zielnetzwerk erforderlich | Unternehmensbereitstellungen, in denen gewisser Mandantendatenverkehr vor anderen Mandanten geschützt werden muss |
| Azure Stack Hub für einzelnen Mandanten, Internetbereitstellung | Ausgehende NAT | Höhere Bandbreite für schnellere Übertragungen | Datenverkehr nicht verschlüsselt, keine Isolierung oder Verschlüsselung außerhalb des Stapels | Hostingszenarien, in denen Mandanten ihre eigene Azure Stack Hub-Bereitstellung und eine dedizierte Verbindung mit der Azure Stack-Umgebung erhalten. Beispiel: ExpressRoute und MPLS (Multiprotocol Label Switching)
| Azure Stack Hub für mehrere Mandanten, Internetbereitstellung | Standort-zu-Standort-VPN-Verbindung | Datenverkehr aus dem VNet des Mandanten zum Ziel ist sicher | Bandbreite durch Standort-zu-Standort-VPN-Tunnel begrenzt<br><br>Gateway im virtuellen Netzwerk und ein VPN-Gerät im Zielnetzwerk erforderlich | Hostingszenarien, in denen der Anbieter eine Cloud für mehrere Mandanten ohne gegenseitiges Vertrauen bereitstellen möchte und der Datenverkehr verschlüsselt werden muss.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Verwenden von ExpressRoute

Sie können Azure Stack Hub über [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) für Szenarien mit Intranetbereitstellungen für einzelne und mehrere Mandanten mit Azure verbinden. Sie benötigen eine bereitgestellte ExpressRoute-Verbindung über einen [Konnektivitätsanbieter](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Das folgende Diagramm zeigt ExpressRoute für ein Szenario mit einzelnem Mandanten (dabei stellt die Verbindung des Kunden die ExpressRoute-Verbindung dar).

![Diagramm mit ExpressRoute-Szenario mit einzelnem Mandanten](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Das folgende Diagramm zeigt ExpressRoute für ein Szenario mit mehreren Mandanten.

![Diagramm mit ExpressRoute-Szenario mit mehreren Mandanten](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externe Überwachung
Um eine Gesamtübersicht über alle Warnungen Ihrer Azure Stack Hub-Bereitstellung sowie der zugehörigen Geräte zu erhalten und Warnungen zur Ticketerstellung in vorhandene Workflows zur IT-Dienstverwaltung zu integrieren, können Sie [Azure Stack Hub mit Überwachungslösungen externer Rechenzentren integrieren](azure-stack-integrate-monitor.md).

Der in der Azure Stack Hub-Lösung enthaltene Hardwarelebenszyklushost ist ein Computer, der sich außerhalb von Azure Stack Hub befindet und vom OEM-Anbieter bereitgestellte Verwaltungstools für Hardware ausführt. Sie können diese Tools oder andere Lösungen verwenden, die direkt mit vorhandenen Überwachungslösungen in Ihr Rechenzentrum integriert werden.

In der folgenden Tabelle ist die Liste der derzeit verfügbaren Optionen zusammengefasst.

| Bereich | Externe Überwachungslösung |
| -- | -- |
| Azure Stack Hub-Software | [Azure Stack Hub Management Pack für Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios-Plug-In](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-basierte API-Aufrufe | 
| Physische Server (BMCs über IPMI) | OEM-Hardware – Operations Manager-Lieferantenverwaltungspaket<br>Vom OEM-Hardwareanbieter bereitgestellte Lösung<br>Plug-Ins des Hardwareanbieters Nagios.<br>Vom OEM-Partner unterstützte Überwachungslösung (enthalten) | 
| Netzwerkgeräte (SNMP) | Operations Manager-Netzwerkgeräteermittlung<br>Vom OEM-Hardwareanbieter bereitgestellte Lösung<br>Nagios Switch-Plug-In |
| Systemüberwachung für Mandantenabonnements | [System Center Management Pack für Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Beachten Sie folgende Punkte:
- Die von Ihnen verwendete Lösung darf keine Agenten verwenden. Sie können keine Agents von Drittanbietern in Azure Stack Hub-Komponenten installieren. 
- Wenn Sie System Center Operations Manager verwenden möchten, erfordert dies Operations Manager 2012 R2 oder Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Backup und Notfallwiederherstellung

Die Planung der Sicherung und Notfallwiederherstellung umfasst das Planen für die zugrunde liegende Azure Stack Hub-Infrastruktur, mit der IaaS-VMs und PaaS-Dienste gehostet werden, sowie für Mandanten-Apps und -daten. Planen Sie diese Dinge separat.

### <a name="protect-infrastructure-components"></a>Schützen von Infrastrukturkomponenten

Sie können Infrastrukturkomponenten von Azure Stack Hub in einer von Ihnen angegebenen SMB-Freigabe [sichern](azure-stack-backup-back-up-azure-stack.md):

- Sie benötigen eine externe SMB-Dateifreigabe auf einem vorhandenen Windows-basierten Dateiserver oder auf einem Gerät eines Drittanbieters.
- Verwenden Sie dieselbe Freigabe für die Sicherung von Netzwerkswitches und des Hardwarelebenszyklus-Hosts. Ihr OEM-Hardwareanbieter unterstützt Sie dabei, eine Anleitung zum Sichern und Wiederherstellen dieser Komponenten bereitzustellen, da sich diese außerhalb von Azure Stack Hub befinden. Sie sind dafür zuständig, die Sicherungsworkflows auf Basis der Empfehlungen des OEM-Anbieters auszuführen.

Bei einem katastrophalen Datenverlust können Sie die Infrastruktursicherung verwenden, um für die Bereitstellungsdaten das erneute Seeding durchzuführen, z. B.: 

- Bereitstellungseingaben und -IDs
- Dienstkonten
- Zertifizierungsstellen-Stammzertifikat
- Verbundressourcen (in Bereitstellungen ohne Verbindung)
- Pläne, Angebote, Abonnements und Kontingente
- RBAC-Richtlinien- und -Rollenzuweisungen
- Key Vault-Geheimnisse

### <a name="protect-tenant-apps-on-iaas-vms"></a>Schützen von Mandanten-Apps auf IaaS-VMs

In Azure Stack Hub werden Mandanten-Apps und -daten nicht gesichert. Sie müssen die Sicherung und Notfallwiederherstellung auf einem Ziel außerhalb von Azure Stack Hub planen. Der Mandantenschutz ist eine vom Mandanten gesteuerte Aktivität. Für IaaS-VMs können Mandanten In-Guest-Technologien verwenden, um Dateiordner, App-Daten und den Systemstatus zu schützen. Als Unternehmen oder Dienstanbieter möchten Sie jedoch möglicherweise eine Sicherungs- und Wiederherstellungslösung anbieten, die sich in demselben Rechenzentrum oder extern in einer Cloud befindet.

Zur Sicherung von IaaS-VMs mit Linux oder Windows müssen Sie Sicherungsprodukte mit Zugriff auf das Gastbetriebssystem verwenden, um den Datei-, Ordner- und Systemstatus sowie App-Daten zu schützen. Sie können Azure Backup, System Center Data Center Protection Manager oder unterstützte Produkte von Drittanbietern verwenden.

Sie können Azure Site Recovery oder unterstützte Produkte von Drittanbietern verwenden, um Daten im Notfall an einem sekundären Standort zu replizieren und Anwendungsfailover zu orchestrieren. Darüber hinaus können Apps, die die native Replikation unterstützen (z. B. Microsoft SQL Server), Daten an einem anderen Speicherort replizieren, an dem die App ausgeführt wird.

## <a name="learn-more"></a>Weitere Informationen

- Informationen zu Anwendungsfällen, zum Erwerb, zu Partnern und zu OEM-Hardwareanbietern finden Sie auf der Produktseite von [Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).
- Informationen zur Roadmap und geografischer Verfügbarkeit für in Azure Stack Hub integrierte Systeme finden Sie im Whitepaper: [Azure Stack Hub: Eine Erweiterung von Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Nächste Schritte
[Verbindungsmodelle für die Azure Stack Hub-Bereitstellung](azure-stack-connection-models.md)
