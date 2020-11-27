---
title: Übersicht zu Modular Datacenter |
description: Azure Modular Data Center ist ein portables, schnell bereitstellbares Rechenzentrum, das für die Unterstützung umfangreicher Einsätze an temporären und festen Gefechtsständen geeignet ist.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eabafd31d25a59cd43daa1a0779afed0a80f8354
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924315"
---
# <a name="modular-datacenter-overview"></a>Übersicht zu Modular Datacenter

Azure Modular Data Center (MDC) basiert auf Azure Stack Hub. MDC ist ein portables, schnell bereitstellbares Rechenzentrum, das für die Unterstützung umfangreicher Einsätze an temporären und festen Gefechtsständen geeignet ist.

Azure Stack Hub ist eine horizontal und vertikal skalierbare Lösung, die mehrinstanzfähige, native Hybrid Cloud-Funktionen für IaaS- (Infrastructure-as-a-Service) und Paas-Dienste (Platform-as-a-Service) für Umgebungen im Edgebereich bereitstellt. Azure Stack Hub unterstützt eine Vielzahl modularer Szenarien für temporäre und feste Gefechtsstände und Expeditionskorps.

Azure Stack Hub ist eine integrierte Hardware- und Softwareappliance, die kommerziell in verschiedenen Kapazitäten verfügbar ist, deren Skalierung auf Knoten-Skalierungseinheiten basiert. Azure Stack Hub ist mit Erweiterungen verfügbar, zu denen Konfigurationen mit Unterstützung für allgemeine GPUs (Graphics Processing Units) und erweiterbarer externer Speicher gehören.

## <a name="use-mdc"></a>Verwenden von MDC

Azure Stack bietet vier Kernprinzipien, die mit den Funktionen von Azure für modulare Edge-Szenarien konsistent sind und diese erweitern.

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Entwickeln und Bereitstellen von Apps mit einem gemeinsamen DevOps-Modell einschließlich API-Symmetrie mit Azure

Die Konsistenz zwischen Azure und Azure Stack bedeutet, dass Lösungen einmal entwickelt, für eine Vielzahl von Anwendungsfällen bereitgestellt und mit gemeinsam verwendeten Tools gesichert und verwaltet werden. Als Tools dienen beispielsweise Azure Key Vault für die Schlüsselverwaltung und Azure Monitor für die Ressourcenüberwachung und -verwaltung. Azure Stack ist mit lokalen Daten, Apps und Tools für DevOps und sicheren Betrieb, z. B. die Schlüsselverwaltung, interoperabel.

### <a name="deliver-azure-services-on-premises"></a>Lokales Bereitstellen von Azure-Diensten

Azure Stack kann in benachteiligten (umkämpften, überfüllten oder verweigerten) Kommunikationsumgebungen sowie in stabilen Kommunikationsumgebungen ausgeführt werden. Azure Stack ist nicht von Konnektivität mit Azure abhängig, um missionsbezogene Apps auszuführen und lokale Operationen zu unterstützen.

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Verwenden der integrierten Bereitstellungsoberfläche für Hardware und Software

Der modulare Edge erfordert eine Reihe von Funktionen, die grundlegende Compute- und Speicherdienste ebenso wie erweiterte Funktionen für maschinelles Lernen, KI und Analysen bereitstellen. Die Möglichkeit, vom modularen Edge aus eine Verbindung mit Ihrer sicheren Cloud herzustellen, wenn die Situation dies zulässt, oder umgekehrt in widrigen Umständen unabhängig von einer solchen Verbindung agieren zu können, ist wichtig, um den Zugriff auf Daten zu ermöglichen, die für die Entscheidungsfindung benötigt werden.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Sicherheit und Verfügbarkeit des Rechenzentrums dank Hybrid Cloud-Sicherheitsvorgängen

Dank der cloudnativen Auslegung von Azure Stack entfallen die Komplexitäten im Betrieb, die herkömmlichen Virtualisierungsumgebungen eigen sind. Administratoren können entscheiden, wann die gesamte Operation durch den Installationsvorgang und die nativ in das System integrierte Azure Stack-Verwaltungsfabric gepatcht und orchestriert werden soll.

Über eine Microsoft-Remoteverwaltung oder ein von Microsoft verwaltetes Angebot auf dem Gebiet der Azure Stack-Geräte erhalten Benutzer den entsprechenden Zugriff über die rollenbasierte Zugriffssteuerung (RBAC), um Aktionen über das Verwaltungsportal, den privilegierten Verwaltungsendpunkt oder die Befehlszeilenschnittstelle (CLI) auszuführen. Diese Fähigkeit ermöglicht es, dass alle Patches und andere Verwaltungs- und Überwachungsaktivitäten von Microsoft ausgeführt werden können. Azure Stack kann über das sichere Verwaltungsportal und CLI-Befehle im Feld erweitert und aktualisiert werden. Auf beiden Wegen werden Sicherheitsupdates und Updates von IaaS- und PaaS-Features angewendet, um über lokale oder Remotenetzwerke den gleichen Stand wie die kommerzielle Version zu erreichen.

## <a name="benefits-of-using-mdc"></a>Vorteile der Verwendung von MDC

Das MDC unterstützt eine konsistente Umgebung mit Azure in Umgebungen mit eingeschränkten Kommunikationsmöglichkeiten:

 - Statische, modulare und schnell bereitstellbare Rechenzentren mit Azure Cloud Services, um große Analyse-Apps in modularen Operationszentren zu unterstützen.
 - Die modularen Edge-Angebote von Azure gewährleisten die Konsistenz zwischen Cloud und Edge dank eines einheitlichen Ansatzes für die Unterstützung von IaaS-Primitiven wie virtuellen Computern, Speicher und virtuellen Netzwerken.
 - Azure Active Directory- und RBAC-Unterstützung
 - Einheitliche Verwaltungsoberflächen
 - API-Symmetrie und Unterstützung für DevOps-Tools von Microsoft, Dritt- und Open-Source-Anbietern
 - Verwaltung und Überwachung über Azure Log Analytics und Azure Security Center
 - Nutzen Sie die Agilität von Cloud Computing für Ihre lokale Umgebung und den Edge, indem Sie eine hybride Cloud aktivieren.<br>Ihre Möglichkeiten:
     - Wiederverwenden von Code und konsistentes Ausführen von cloudnativen Apps in Azure und Ihren lokalen Umgebungen.
     - Ausführen herkömmlicher virtualisierter Workloads mit optionalen Verbindungen mit Azure-Diensten
     - Übertragen von Daten in die Cloud oder Speichern der Daten in Ihrem unabhängigen Rechenzentrum zur Einhaltung von Richtlinien
     - Ausführen von hardwarebeschleunigten Workloads für maschinelles Lernen bzw. containerbasierten oder virtualisierten Workloads mit Intelligent Edge

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Kapazitätsplanung für Azure Stack Hub](../operator/azure-stack-capacity-planning-overview.md)
