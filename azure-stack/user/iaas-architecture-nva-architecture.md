---
title: Bereitstellen hochverfügbarer virtueller Netzwerkgeräte in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie hochverfügbare virtuelle Netzwerkgeräte in Azure Stack Hub bereitstellen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 5bf9434b14c7dfb4abdbe295339c02eb320323ae
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815492"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack-hub"></a>Bereitstellen hochverfügbarer virtueller Netzwerkgeräte in Azure Stack Hub

In diesem Artikel erfahren Sie, wie eine Reihe virtueller Netzwerkgeräte (Network Virtual Appliances, NVAs) für Hochverfügbarkeit in Azure Stack Hub bereitgestellt wird. Ein NVA wird normalerweise verwendet, um den Netzwerkdatenverkehrsfluss von einem Umkreisnetzwerk (auch als DMZ bekannt) zu anderen Netzwerken oder Subnetzen zu steuern. Der Artikel enthält Beispielarchitekturen für nur eingehenden, nur ausgehenden sowie ein- und ausgehenden Datenverkehr.

Im [Azure Stack Hub Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items) sind NVAs unterschiedlicher Anbieter verfügbar. Verwenden Sie eines davon, um eine optimale Leistung zu erzielen.

Diese Architektur besteht aus den folgenden Komponenten.

## <a name="networking-and-load-balancing"></a>Netzwerk und Lastenausgleich

-   **Virtuelles Netzwerk und Subnetze**. Jede Azure-VM wird in einem virtuellen Netzwerk bereitgestellt, das in Subnetze segmentiert werden kann. Erstellen Sie für jede Schicht ein separates Subnetz.

-   **Layer 7-Lastenausgleich.** Da Application Gateway noch nicht für Azure Stack Hub verfügbar ist, sind Alternativen in [Azure Stack Hub Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items) verfügbar, wie z. B.: [KEMP LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) oder [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Lastenausgleichsmodule**. Verwenden Sie [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) zum Verteilen von Netzwerkdatenverkehr von der Webschicht auf die Geschäftsschicht und von der Geschäftsschicht an SQL Server.

-   **Netzwerksicherheitsgruppen** (NSGs). Verwenden Sie NSGs, um den Netzwerkdatenverkehr im virtuellen Netzwerk zu beschränken. In der hier gezeigten dreischichtigen Architektur akzeptiert die Datenbankschicht beispielsweise keinen Datenverkehr vom Web-Front-End, sondern nur von der Geschäftsschicht und dem Verwaltungssubnetz.

-   **Benutzerdefinierte Routen**. Verwenden Sie [*benutzerdefinierten Routen*](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview/) zum Weiterleiten von Datenverkehr an den jeweiligen Lastenausgleich.

In diesem Artikel werden Grundkenntnisse zu Azure Stack Hub-Netzwerken vorausgesetzt.

## <a name="architecture-diagrams"></a>Architekturdiagramme

Ein NVA kann in vielen Architekturen in einem Umkreisnetzwerk bereitgestellt werden. Die folgende Abbildung veranschaulicht beispielsweise die Verwendung eines einzelnen NVA für eingehenden Datenverkehr.

![Screenshot einer automatisch generierten Beschreibung eines Posts in sozialen Medien](./media/iaas-architecture-nva-architecture/image1.png)

In dieser Architektur stellt das NVA eine sichere Netzwerkgrenze dar, indem der gesamte eingehende und ausgehende Netzwerkdatenverkehr überprüft und nur der Datenverkehr weitergeleitet wird, der die Netzwerksicherheitsregeln erfüllt. Aufgrund der Tatsache, dass der gesamte Netzwerkdatenverkehr über das NVA erfolgen muss, stellt das NVA einen Single Point of Failure im Netzwerk dar. Tritt beim NVA ein Fehler auf, steht kein anderer Pfad für den Netzwerkdatenverkehr bereit und keines der Back-End-Subnetze ist verfügbar.

Um Hochverfügbarkeit für ein NVA zu gewährleisten, stellen Sie mehrere NCAs in einer Verfügbarkeitsgruppe bereit.

In den folgenden Architekturen sind die für hochverfügbare NVAs erforderlichen Ressourcen und die Konfiguration angegeben:

| Lösung | Vorteile | Überlegungen |
| --- | --- | --- |
| Eingehender Datenverkehr mit Layer-7-NVAs | Alle NVA-Knoten sind aktiv. | Erfordert ein NVA, das Verbindungen beenden und SNAT verwenden kann.<br>Erfordert eine separate Gruppe von NVAs für Datenverkehr aus dem Unternehmensnetzwerk/Internet und aus Azure Stack Hub.<br>Kann nur für Datenverkehr verwendet werden, dessen Ursprung außerhalb von Azure Stack Hub liegt.  |
| Ausgehender Datenverkehr mit Layer-7-NVAs | Alle NVA-Knoten sind aktiv. | Erfordert ein NVA, das Verbindungen beenden kann und die Übersetzung der Quellnetzwerkadresse (SNAT) implementiert. |
| Ein-/ausgehender Datenverkehr mit Layer 7-NVAs | Alle Knoten sind aktiv.<br>Kann den aus Azure Stack Hub stammenden Datenverkehr abwickeln. | Erfordert ein NVA, das Verbindungen beenden und SNAT verwenden kann.<br>Erfordert eine separate Gruppe von NVAs für Datenverkehr aus dem Unternehmensnetzwerk/Internet und aus Azure Stack Hub. |

## <a name="ingress-with-layer-7-nvas"></a>Eingehender Datenverkehr mit Layer-7-NVAs

Die folgende Abbildung zeigt eine Hochverfügbarkeitsarchitektur, die ein Umkreisnetzwerk für eingehenden Datenverkehr hinter einem Lastenausgleich mit Internetzugriff implementiert. Diese Architektur bietet Konnektivität für Azure Stack Hub-Workloads für den Layer 7-Datenverkehr, z. B. HTTP oder HTTPS:

![Screenshot einer automatisch generierten Zuordnungsbeschreibung](./media/iaas-architecture-nva-architecture/image2.png)

Der Vorteil dieser Architektur ist, dass alle NVAs aktiv sind, und bei Ausfall eines NVA wird der Netzwerkdatenverkehr durch den Lastenausgleich an das andere NVA weitergeleitet. Beide NVAs leiten den Datenverkehr an den internen Lastenausgleich, sodass der Datenverkehr weiter fließt, solange ein NVA aktiv ist. Die NVAs sind erforderlich, um den für virtuelle Computer der Webebene vorgesehenen SSL-Datenverkehr zu beenden. Diese NVAs können nicht auf die Abwicklung des Datenverkehr aus dem Unternehmensnetzwerk ausgeweitet werden, da für diesen Datenverkehr eine andere dedizierte Gruppe von NVAs mit eigenen Netzwerkrouten erforderlich ist.

## <a name="egress-with-layer-7-nvas"></a>Ausgehender Datenverkehr mit Layer-7-NVAs

Die Architektur „Eingehender Datenverkehr mit Layer 7-NVAs“ kann so erweitert werden, dass sie ein ausgehendes Umkreisnetzwerk für Anforderungen umfasst, die aus der Azure Stack Hub-Workload stammen. Die folgende Architektur bietet Hochverfügbarkeit der NVAs im Umkreisnetzwerk für den Layer 7-Datenverkehr, z. B. HTTP oder HTTPS:

![Screenshot einer automatisch generierten Beschreibung für ein Mobiltelefon](./media/iaas-architecture-nva-architecture/image3.png)

In dieser Architektur wird der gesamte aus Azure Stack Hub stammende Datenverkehr an einen internen Lastenausgleich weitergeleitet. Der Lastenausgleich verteilt ausgehende Anforderungen auf eine Gruppe von NVAs. Diese NVAs leiten den Datenverkehr über ihre jeweiligen öffentlichen IP-Adressen an das Internet weiter.

## <a name="ingress-egress-with-layer-7--nvas"></a>Ein-/ausgehender Datenverkehr mit Layer 7-NVAs

Bei den beiden Architekturen für ein- und ausgehenden Datenverkehr gab es ein separates Umkreisnetzwerk für ein- und ausgehenden Datenverkehr. Die folgende Architektur veranschaulicht das Erstellen eines Umkreisnetzwerks, das sowohl für ein- als auch ausgehenden Layer 7-Datenverkehr, z. B. HTTP oder HTTPS, verwendet werden kann:

![Screenshot einer automatisch generierten Beschreibung eines Posts in sozialen Medien](./media/iaas-architecture-nva-architecture/image4.png)

In der Architektur „Ein-/ausgehender Datenverkehr mit Layer 7-NVAs“ verarbeiten die NVAs eingehende Anforderungen von einem Layer 7-Lastenausgleich. Die NVAs verarbeiten auch ausgehende Anforderungen von den Workload-VMs im Back-End-Pool des Lastenausgleichs. Da eingehender Datenverkehr mit einem Layer-7-Lastenausgleich und ausgehender Datenverkehr mit einem SLB (Azure Stack Hub Basic Load Balancer) weitergeleitet wird, sind die NVAs für die Aufrechterhaltung von Sitzungsaffinität zuständig. Das heißt, dass das der Layer 7-Lastenausgleich eine Zuordnung von ein- und ausgehenden Anforderungen verwaltet, damit es die richtige Antwort an den ursprünglichen Anforderer weiterleiten kann. Der interne Lastenausgleich hat jedoch keinen Zugriff auf die Zuordnungen des Layer 7-Lastenausgleichs und verwendet eine eigene Logik zum Senden von Antworten an die NVAs. Es ist möglich, dass der Lastenausgleich eine Antwort an ein NVA sendet, das die Anforderung nicht ursprünglich vom Layer 7-Lastenausgleich empfangen hat. In diesem Fall müssen die NVAs kommunizieren und die Antwort untereinander übertragen, damit das richtige NVA die Antwort an den Layer 7-Lastenausgleich weiterleiten kann.

> [!Note]  
> Sie können das Problem des asymmetrischen Routings auch beheben, indem Sie sicherstellen, dass die NVAs eine eingehende Übersetzung der Quellnetzwerkadresse (SNAT) ausführen. Dadurch wird die ursprüngliche Quell-IP des Anforderers durch eine der IP-Adressen des NVA ersetzt, der für den eingehenden Datenfluss verwendet wird. Auf diese Weise ist sichergestellt, dass Sie mehrere NVAs gleichzeitig verwenden und dabei die Routensymmetrie beibehalten können.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Stack Hub-VMs finden Sie unter [Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md).  
- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
