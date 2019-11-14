---
title: Übersicht über Azure Stack Hub | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht darüber, worum es sich bei Azure Stack Hub handelt und wie Sie damit Azure-Dienste in Ihrem Rechenzentrum ausführen können.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: 9461b7c306eac0c2a1467378a49d4e91e234a629
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595421"
---
# <a name="azure-stack-hub-overview"></a>Übersicht über Azure Stack Hub

Azure Stack Hub ist eine Erweiterung von Azure, die Ihnen die Möglichkeit bietet, Apps in einer lokalen Umgebung auszuführen und Azure-Dienste in Ihrem Rechenzentrum bereitzustellen. Mit einer einheitlichen Cloudplattform können Organisationen Entscheidungen in Bezug auf ihre Technologie ohne Bedenken auf Grundlage geschäftlicher Anforderungen treffen, anstatt geschäftliche Entscheidungen von Technologieeinschränkungen abhängig zu machen.

## <a name="why-use-azure-stack-hub-"></a>Gründe für die Verwendung von Azure Stack Hub

Azure bietet Entwicklern eine umfassende Plattform für die Erstellung moderner Apps. Bei einigen cloudbasierten Apps erweisen sich die Wartezeit, zeitweilige Konnektivitätsprobleme und gesetzliche Bestimmungen jedoch als Hindernisse. Azure und Azure Stack Hub ermöglichen gänzlich neue Hybrid Cloud-Anwendungsfälle für Kundenanwendungen und interne Branchenanwendungen:

- **Edgelösungen und nicht vernetzte Lösungen:** Erfüllen Sie die Anforderungen im Hinblick auf die Wartezeit und Konnektivität, indem Sie Daten lokal in Azure Stack Hub verarbeiten und dann zur weiteren Analyse in Azure aggregieren – mit einer gemeinsamen App-Logik in beiden Umgebungen. Sie können Azure Stack Hub sogar ohne Internetverbindung bereitstellen, d. h. ohne Konnektivität mit Azure. Anwendungsbeispiele hierfür sind Fabrikhallen, Kreuzfahrtschiffe und Bergbauminen.

- **Cloud-Apps, die verschiedene Bestimmungen erfüllen:** Sie können Apps in Azure entwickeln und bereitstellen. Dank vollständiger Flexibilität können sie dieselben Anwendungen mithilfe von Azure Stack Hub in Ihrer lokalen Umgebung bereitstellen, um gesetzliche oder richtlinienbasierte Anforderungen zu erfüllen. Hierzu sind keine Codeänderungen erforderlich. App-Beispiele hierfür sind globale Überwachung, Finanzberichte, Devisenhandel, Onlinespiele und Spesenabrechnungen.

- **Lokales Cloud-App-Modell:** Nutzen Sie Azure-Dienste, Container, serverlose Architekturen und Microservicearchitekturen, um vorhandene Apps zu aktualisieren oder zu erweitern oder um neue Apps zu erstellen. Nutzen Sie konsistente DevOps-Prozesse in Azure in der Cloud und lokal in Azure Stack Hub, um die Modernisierung zentraler, unternehmenskritischer Apps zu beschleunigen.

Folgende Features von Azure Stack Hub ermöglichen diese Verwendungsszenarien:

- Integrierte Bereitstellungsfunktion zur schnellen Inbetriebnahme von speziell entwickelten integrierten Azure Stack Hub-Systemen von unseren vertrauenswürdigen Hardwarepartnern. Nach der Bereitstellung kann Azure Stack Hub mühelos in das Rechenzentrum integriert und mit dem System Center Operations Manager Management Pack oder der Nagios-Erweiterung überwacht werden.

- Flexible Identitätsverwaltung mit Azure Active Directory (Azure AD) für hybride Azure- und Azure Stack Hub-Umgebungen und Nutzung von Active Directory-Verbunddienste (Active Directory Federation Services, AD FS) für nicht verbundene Bereitstellungen.

- Mit Azure konsistente Umgebung für die App-Entwicklung, um die Produktivität von Entwicklern zu maximieren und die Verwendung gängiger DevOps-Verfahren in Hybridumgebungen zu ermöglichen.

- Bereitstellung von Azure-Diensten aus der lokalen Umgebung mit der Computingleistung der Hybrid Cloud. Führen Sie gemeinsame Betriebspraktiken in Azure und Azure Stack Hub ein, um Azure IaaS-Dienste (Infrastructure-as-a-Service) und Azure PaaS-Dienste (Platform-as-a-Service) mit den gleichen Verwaltungsfunktionen und -tools wie in Azure bereitzustellen und auszuführen. Microsoft stellt kontinuierlich Azure-Innovationen für Azure Stack Hub bereit, darunter neue Azure-Dienste, Updates für vorhandene Dienste sowie zusätzliche Azure Marketplace-Apps und -Images.

## <a name="azure-stack-hub-architecture"></a>Architektur von Azure Stack Hub

Integrierte Azure Stack Hub-Systeme bestehen aus Racks mit 4 bis 16 Servern von vertrauenswürdigen Hardwarepartnern und werden direkt an Ihr Rechenzentrum geliefert. Ein Lösungsanbieter stellt das integrierte System nach der Lieferung gemeinsam mit Ihnen bereit und stellt sicher, dass die Azure Stack Hub-Lösung Ihre geschäftlichen Anforderungen erfüllt. Bereiten Sie das Rechenzentrum vor, indem Sie sicherstellen, dass die gesamte erforderliche Stromversorgung und Kühlung, die Grenzkonnektivität und andere erforderliche Komponenten für die Rechenzentrumsintegration vorhanden sind.

> Weitere Informationen zur Rechenzentrumsintegration von Azure Stack Hub finden Sie unter [Azure Stack Hub-Rechenzentrumsintegration](azure-stack-customer-journey.md).

Azure Stack Hub basiert auf Hardware nach Industriestandard und wird mit den gleichen Tools verwaltet, die Sie bereits für die Verwaltung von Azure-Abonnements verwenden. Daher können Sie unabhängig davon, ob Sie mit Azure verbunden sind oder nicht, konsistente DevOps-Prozesse anwenden.

Für Remotestandorte, bei zeitweiligen Konnektivitätsproblemen oder für den Betrieb ohne Internetverbindung ermöglicht die Azure Stack Hub-Architektur Ihnen die Bereitstellung von Azure-Diensten im Edgebereich. Sie können Hybridlösungen erstellen, die Daten lokal in Azure Stack Hub verarbeiten und dann zur weiteren Verarbeitung und Analyse in Azure aggregieren. Und da Azure Stack Hub lokal installiert ist, haben Sie die Flexibilität, Cloud-Apps zur Erfüllung von spezifischen gesetzlichen Bestimmungen oder Richtlinienanforderungen lokal bereitzustellen, ohne auch nur eine einzige Zeile Code zu ändern.

## <a name="deployment-options"></a>Bereitstellungsoptionen

### <a name="production-or-evaluation-environments"></a>Produktions- oder Evaluierungsumgebungen

Azure Stack Hub wird zur Erfüllung Ihrer Anforderungen mit zwei Bereitstellungsoptionen angeboten: integrierte Azure Stack Hub-Systeme für die Produktion und Azure Stack Hub Development Kit (ASDK) für die Evaluierung von Azure Stack Hub:

- **Integrierte Azure Stack Hub-Systeme:** Integrierte Azure Stack Hub-Systeme werden im Rahmen einer Partnerschaft zwischen Microsoft und Hardwarepartnern angeboten. Dadurch entsteht eine Lösung mit cloudbasierten Innovationen und komfortabler Computeverwaltung. Als integriertes Hardware- und Softwaresystem bietet Ihnen Azure Stack Hub nicht nur genau die Flexibilität und Kontrolle, die Sie benötigen, sondern auch die Möglichkeit zur Nutzung innovativer Cloudfeatures. Integrierte Azure Stack Hub-Systeme haben eine Größe von 4 bis 16 Knoten, und der Support wird vom Hardwarepartner und von Microsoft gemeinsam bereitgestellt. Mit integrierten Azure Stack Hub-Systemen ermöglichen Sie neue Szenarien und stellen neue Lösungen für Ihre Produktionsworkloads bereit.

- **Azure Stack Hub Development Kit (ASDK):** Das [ASDK](../asdk/asdk-what-is.md) ist eine kostenlose Einzelknotenbereitstellung von Azure Stack Hub, mit der Sie Azure Stack Hub evaluieren und kennenlernen können. Sie können das ASDK auch für Entwicklungsarbeiten zum Erstellen von Apps mit APIs und Tools verwenden, die mit Azure konsistent sind. Das ASDK ist jedoch nicht zur Verwendung als Produktionsumgebung vorgesehen und weist im Vergleich zu den vollständigen integrierten Systemen für die Produktionsbereitstellung die folgenden Einschränkungen auf:

    - Das ASDK kann nur einem einzelnen Azure AD- oder AD FS-Identitätsanbieter zugeordnet werden.
    - Da Azure Stack Hub-Komponenten auf einem einzelnen Hostcomputer bereitgestellt werden, sind für Mandantenressourcen die verfügbaren physischen Ressourcen begrenzt. Diese Konfiguration ist nicht für die Skalierung oder die Beurteilung der Leistung vorgesehen.
    - Netzwerkszenarien sind aufgrund der Bereitstellungsanforderungen für Einzelhosts und NICs eingeschränkt.

### <a name="connection-models"></a>Verbindungsmodelle

Sie können Azure Stack Hub entweder mit dem Internet (und mit Azure) **verbunden** oder **ohne Verbindung** bereitstellen. Diese Auswahl bestimmt, welche Optionen für Ihren Identitätsspeicher (Azure AD oder AD FS) und Ihr Abrechnungsmodell (nutzungs- oder kapazitätsbasierte Abrechnung) verfügbar sind.

> Weitere Informationen finden Sie in den Überlegungen zu [verbundenen](azure-stack-connected-deployment.md) und [nicht verbundenen](azure-stack-disconnected-deployment.md) Bereitstellungsmodellen.

### <a name="identity-provider"></a>Identitätsanbieter 

Azure Stack Hub verwendet entweder Azure AD oder Active AD FS für die Bereitstellung von Identitäten. Azure AD ist der cloudbasierte, mehrinstanzenfähige Identitätsanbieter von Microsoft. Für die meisten Hybridszenarien mit Bereitstellungen mit Internetverbindung wird Azure AD als Identitätsspeicher verwendet.

Für getrennte Bereitstellungen von Azure Stack Hub müssen Sie AD FS verwenden. Azure Stack Hub-Ressourcenanbieter und andere Apps funktionieren mit AD FS oder Azure AD auf ähnliche Weise. Azure Stack Hub umfasst eine eigene Active Directory-Instanz und eine Active Directory Graph-API.

> [!IMPORTANT]
> Sie können den Identitätsanbieter nach der Bereitstellung nicht mehr ändern. Sie müssen Azure Stack Hub erneut bereitstellen, um einen anderen Identitätsanbieter zu verwenden. Weitere Informationen und Überlegungen zur Azure Stack Hub-Identität finden Sie unter [Übersicht über die Identität für Azure Stack Hub](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-hub-managed"></a>Wie wird Azure Stack Hub verwaltet?

Sie können Azure Stack Hub über das Administratorportal, das Benutzerportal oder mit [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1) verwalten. Alle Azure Stack Hub-Portale basieren jeweils auf separaten Azure Resource Manager-Instanzen. Ein **Azure Stack Hub-Betreiber** verwendet das Administratorportal, um Azure Stack Hub zu verwalten und Aufgaben wie das Erstellen von Mandantenangeboten, Sicherstellen der Integrität und Überwachen des Status des integrierten Systems auszuführen. Das Benutzerportal bietet eine Self-Service-Benutzeroberfläche für die Nutzung von Cloudressourcen wie virtuelle Computer, Speicherkonten und Web-Apps.

> Weitere Informationen zum Verwalten von Azure Stack Hub über das Administratorportal finden Sie unter [Schnellstart: Verwenden des Azure Stack Hub-Verwaltungsportals](azure-stack-manage-portals.md).

Als Azure Stack Hub-Betreiber können Sie eine Vielzahl von Diensten und Apps bereitstellen, z. B. [VMs](azure-stack-tutorial-tenant-vm.md), [Web-Apps](azure-stack-app-service-overview.md) sowie hochverfügbare [SQL Server](azure-stack-tutorial-sql.md)-Datenbanken und [MySQL](azure-stack-tutorial-mysql.md)-Serverdatenbanken. Sie können auch [Azure Resource Manager-Schnellstartvorlagen für Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) zum Bereitstellen von SharePoint, Exchange usw. verwenden.

Das Administratorportal ermöglicht Ihnen das [Konfigurieren von Azure Stack Hub zur Bereitstellung von Diensten](service-plan-offer-subscription-overview.md) für Mandanten (mit Plänen, Kontingenten, Angeboten und Abonnements). Mandantenbenutzer können mehrere Angebote abonnieren. Angebote können einen oder mehrere Pläne enthalten und die Pläne wiederum einen oder mehrere Dienste. Betreiber können auch die Kapazität verwalten und auf Warnungen reagieren.

Wenn Azure Stack Hub konfiguriert ist, nutzt ein **Azure Stack Hub-Benutzer** Dienste, die der Betreiber anbietet. Benutzer können Dienste, die sie abonniert haben – z. B. Web-Apps, Speicher und virtuelle Computer – bereitstellen, überwachen und verwalten.

> Weitere Informationen zum Verwalten von Azure Stack Hub finden Sie unter [Grundlagen zur Verwaltung von Azure Stack Hub](azure-stack-manage-basics.md). Dort erfahren Sie u. a., welche Konten wo verwendet werden müssen, welche typischen Aufgaben zum Zuständigkeitsbereich des Betreibers zählen, welche Informationen Sie Ihren Benutzern mitteilen müssen und wo Sie Hilfe erhalten.

## <a name="resource-providers"></a>Ressourcenanbieter

Ressourcenanbieter sind Webdienste, die die Grundlage für alle IaaS- und PaaS-Dienste von Azure Stack Hub bilden. Azure Resource Manager nutzt verschiedene Ressourcenanbieter, um den Zugriff auf Dienste zu ermöglichen. Mit diesen Ressourcenanbietern können Sie die zugehörigen Ressourcen konfigurieren und steuern. Dienstadministratoren können auch neue benutzerdefinierte Ressourcenanbieter hinzufügen.

### <a name="foundational-resource-providers"></a>Grundlegende Ressourcenanbieter

Es gibt drei grundlegende IaaS-Ressourcenanbieter (Infrastructure-as-a-Service):

- **Compute**: Mit dem Computeressourcenanbieter können Azure Stack Hub-Mandanten eigene VMs erstellen. Der Computeressourcenanbieter bietet die Möglichkeit, VMs und VM-Erweiterungen zu erstellen. Der Erweiterungsdienst für virtuelle Computer unterstützt die Bereitstellung von IaaS-Funktionen für virtuelle Windows- und Linux-Computer. Beispielsweise können Sie den Computeressourcenanbieter zum Bereitstellen einer Linux-VM verwenden und während der Bereitstellung Bash-Skripts ausführen, um die VM zu konfigurieren.
- **Netzwerkressourcenanbieter:** Der Netzwerkressourcenanbieter bietet eine Reihe von Features für Software-Defined Networking (SDN) und Netzwerkfunktionsvirtualisierung (Network Function Virtualization, NFV) für die private Cloud. Mit dem Netzwerkressourcenanbieter können Sie Ressourcen wie Software-Lastenausgleichsmodule, öffentliche IP-Adressen, Netzwerksicherheitsgruppen und virtuelle Netzwerke erstellen.
- **Speicherressourcenanbieter:** Der Speicherressourcenanbieter umfasst vier mit Azure konsistente Speicherdienste: [Blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [Warteschlange](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [Tabelle](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage) und [Key Vault](https://docs.microsoft.com/azure/key-vault/)-Kontoverwaltung mit Funktionen zur Verwaltung und Überwachung von Geheimnissen (z. B. Kennwörter und Zertifikate). Der Speicherressourcenanbieter bietet außerdem einen Speichercloud-Verwaltungsdienst, um die Dienstanbieterverwaltung von Speicherdiensten zu vereinfachen, die mit Azure konsistent sind. Azure Storage bietet die Flexibilität zum Speichern und Abrufen großer Mengen von unstrukturierten Daten, z. B. Dokumenten und Mediendateien mit Azure-Blobs, und von strukturierten NoSQL-basierten Daten mit Azure-Tabellen.

### <a name="optional-resource-providers"></a>Optionale Ressourcenanbieter

Es gibt drei optionale PaaS-Ressourcenanbieter (Platform-as-a-Service), die Sie bereitstellen und mit Azure Stack Hub verwenden können:

- **App Service**: [Azure App Service in Azure Stack Hub](azure-stack-app-service-overview.md) ist ein PaaS-Angebot von Microsoft Azure, das für Azure Stack verfügbar ist. Mit diesem Dienst können Ihre internen oder externen Kunden Web-, API- und Azure Functions-Apps für beliebige Plattformen oder Geräte erstellen.
- **SQL Server:** Verwenden Sie den [SQL Server-Ressourcenanbieter](azure-stack-sql-resource-provider.md), um SQL-Datenbanken als Dienst in Azure Stack Hub anzubieten. Nachdem Sie den Ressourcenanbieter installiert und mit mindestens einer SQL Server-Instanz verbunden haben, können Sie und Ihre Benutzer Datenbanken für cloudnative Apps, Websites mit SQL-Nutzung und andere Workloads mit SQL erstellen.
- **MySQL Server:** Verwenden Sie den [MySQL-Server-Ressourcenanbieter](azure-stack-mysql-resource-provider-deploy.md), um MySQL-Datenbanken als Azure Stack Hub-Dienst verfügbar zu machen. Der MySQL-Ressourcenanbieter wird als Dienst auf einem virtuellen Windows Server 2016 Server Core-Computer ausgeführt.

## <a name="providing-high-availability"></a>Bereitstellen von Hochverfügbarkeit

Zur Erreichung von Hochverfügbarkeit für ein Produktionssystem mit mehreren VMs in Azure werden die VMs in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) angeordnet, um sie auf mehrere Fehlerdomänen und Updatedomänen zu verteilen. Im kleineren Rahmen von Azure Stack Hub ist eine Fehlerdomäne in einer Verfügbarkeitsgruppe als einzelner Knoten in der Skalierungseinheit definiert.  

Die Infrastruktur von Azure Stack Hub verfügt zwar bereits über Resilienz gegenüber Ausfällen, aber bei einem Hardwarefehler kommt es bei der zugrunde liegenden Technologie (Failoverclustering) für VMs auf einem betroffenen physischen Server trotzdem noch zu Ausfallzeiten. Azure Stack Hub unterstützt die Verwendung einer Verfügbarkeitsgruppe mit maximal drei Fehlerdomänen, um Konsistenz mit Azure zu erzielen.

- **Fehlerdomänen**: In einer Verfügbarkeitsgruppe angeordnete VMs werden physisch voneinander isoliert, indem sie so gleichmäßig wie möglich auf mehrere Fehlerdomänen (Azure Stack Hub-Knoten) verteilt werden. Bei einem Hardwarefehler werden VMs aus der fehlerhaften Fehlerdomäne in anderen Fehlerdomänen neu gestartet. Sie werden getrennt von den anderen VMs in separaten Fehlerdomänen, aber nach Möglichkeit in derselben Verfügbarkeitsgruppe gespeichert. Nachdem die Hardware wieder in den Onlinezustand versetzt wurde, wird für die VMs ein neuer Ausgleichsvorgang durchgeführt, um die Hochverfügbarkeit sicherzustellen.

- **Updatedomänen**: Updatedomänen sind ein anderes Azure-Konzept, mit dem für Hochverfügbarkeit in Verfügbarkeitsgruppen gesorgt wird. Eine Updatedomäne ist eine logische Gruppe von zugrunde liegender Hardware, die zur gleichen Zeit gewartet werden kann. VMs in derselben Updatedomäne werden während einer geplanten Wartung gemeinsam neu gestartet. Wenn Mandanten VMs in einer Verfügbarkeitsgruppe erstellen, werden die VMs von der Azure-Plattform automatisch auf diese Updatedomänen verteilt. In Azure Stack Hub wird für VMs eine Livemigration über die anderen Onlinehosts im Cluster durchgeführt, bevor der zugrunde liegende Host aktualisiert wird. Da es während eines Hostupdates nicht zu Mandantenausfallzeiten kommt, ist das Updatedomänenfeature in Azure Stack Hub nur für die Vorlagenkompatibilität mit Azure vorhanden. Virtuelle Computer in einer Verfügbarkeitsgruppe zeigen im Portal **0** als Nummer der Updatedomäne an.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Mit der rollenbasierten Zugriffssteuerung (Role Based Access Control, RBAC) können Sie autorisierten Benutzern, Gruppen und Diensten Systemzugriff gewähren, indem Sie ihnen Rollen auf Abonnement-, Ressourcengruppen- oder Ressourcenebene zuweisen. Jede Rolle definiert die Zugriffsebene, über die ein Benutzer, eine Gruppe oder ein Dienst für Microsoft Azure Stack Hub-Ressourcen verfügt.

Die rollenbasierte Zugriffssteuerung von Azure Stack Hub umfasst drei grundlegende Rollen, die für alle Ressourcentypen gelten: Besitzer, Mitwirkender und Leser. Besitzer verfügen über vollständigen Zugriff auf alle Ressourcen, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren. Mitwirkende können alle Arten von Azure-Ressourcen erstellen und verwalten, aber keinen anderen Personen Zugriff gewähren. Leser können nur vorhandene Ressourcen anzeigen. Die verbleibenden RBAC-Rollen ermöglichen die Verwaltung von bestimmten Azure-Ressourcen. Beispielsweise ermöglicht die Rolle „Mitwirkender für virtuelle Computer“ die Erstellung und Verwaltung von virtuellen Computern, nicht jedoch des virtuellen Netzwerks oder des Subnetzes, mit dem der virtuelle Computer eine Verbindung herstellt.

> Weitere Informationen finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md).

## <a name="reporting-usage-data"></a>Melden von Nutzungsdaten

Azure Stack Hub erfasst und aggregiert Nutzungsdaten für sämtliche Ressourcenanbieter und übermittelt sie zur Verarbeitung durch Azure Commerce an Azure. Die in Azure Stack Hub gesammelten Nutzungsdaten können über eine REST-API angezeigt werden. Es gibt eine mit Azure übereinstimmende Mandanten-API sowie APIs für Anbieter und delegierte Anbieter, mit denen die Nutzungsdaten für alle Mandantenabonnements abgerufen werden können. Diese Daten können in ein externes Tool oder einen Dienst integriert oder für die Abrechnung oder verbrauchsbasierte Kostenzuteilung verwendet werden. Sobald die Nutzungsdaten von Azure Commerce verarbeitet wurden, können sie im Azure-Abrechnungsportal angezeigt werden.

> Erfahren Sie mehr über das [Melden von Azure Stack Hub-Nutzungsdaten an Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Nächste Schritte

[Vergleich zwischen Azure Stack Hub und einer globalen Azure-Umgebung](compare-azure-azure-stack.md)

[Verwaltungsgrundlagen](azure-stack-manage-basics.md)

[Schnellstart: Verwenden des Azure Stack Hub-Verwaltungsportals](azure-stack-manage-portals.md)
