---
title: Übersicht über Modular Data Center |
description: Das Modular Data Center ist ein portables, schnell bereitstellbares Rechenzentrum, das für die Unterstützung umfangreicher Einsätze an temporären und festen Gefechtsständen geeignet ist.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eaa84fb673863935dc4778f34a6fce592590a7a8
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383403"
---
# <a name="modular-data-center-overview"></a>Übersicht über das Modular Data Center 

Das Modular Data Center (MDC) basiert auf Azure Stack Hub. Das MDC ist ein portables, schnell bereitstellbares Rechenzentrum, das für die Unterstützung umfangreicher Einsätze an temporären und festen Gefechtsständen geeignet ist.

Azure Stack Hub ist eine horizontal und vertikal skalierbare Lösung, die mehrinstanzenfähige, native Hybrid Cloud-Funktionen für IaaS- und PaaS-Dienste in Umgebungen am Edge bereitstellt und eine Vielzahl von modularen Szenarios für temporäre und feste Gefechtsstände sowie Expeditionsstreitkräfte unterstützt. Azure Stack Hub ist eine integrierte Hardware- und Softwareappliance, die für eine Vielzahl von Aufgabengebiete kommerziell erhältlich ist. Sie unterstützt die Skalierung über Knotenskalierungseinheiten und wird mit Erweiterungen wie Konfigurationen für universelle GPUs (Graphics Processing Unit) und erweiterbarem externem Speicher angeboten.

## <a name="how-you-can-use-the-mdc"></a>Verwendungsmöglichkeiten des MDC

Azure Stack bietet vier Kernprinzipien, die mit den Funktionen von Azure für modulare Edge-Szenarien konsistent sind und diese erweitern. 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Entwickeln und Bereitstellen von Apps mit einem gemeinsamen DevOps-Modell einschließlich API-Symmetrie mit Azure

Die Konsistenz zwischen Azure und Azure Stack bedeutet, dass Lösungen einmal entwickelt, für eine Vielzahl von Anwendungsfällen bereitgestellt und mit gemeinsam verwendeten Tools wie Azure Key Vault für die Schlüsselverwaltung und Azure Monitor für die Ressourcenüberwachung und -verwaltung gesichert und verwaltet werden. Azure Stack ist mit lokalen Daten, Apps und Tools für DevOps und sicheren Betrieb, z. B. die Schlüsselverwaltung, interoperabel.

### <a name="deliver-azure-services-on-premises"></a>Lokales Bereitstellen von Azure-Diensten

Azure Stack kann in Umgebungen mit beeinträchtigten (umkämpften, überlasteten oder verweigerten) Kommunikationsmöglichkeiten sowie in robusten Kommunikationsumgebungen ausgeführt werden und ist nicht von der Konnektivität mit Azure abhängig, um Einsatzanwendungen auszuführen und den lokalen Betrieb zu ermöglichen. 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Verwenden der integrierten Hardware- und Softwarebereitstellung

Der modulare Edge erfordert eine Reihe von Funktionen, die nicht nur grundlegende Compute- und Speicherdienste, sondern auch erweiterte Funktionen für maschinelles Lernen, KI und Analysen bereitstellen. Die Möglichkeit, vom modularen Edge aus eine Verbindung mit Ihrer sicheren Cloud herzustellen, wenn die Situation dies zulässt, oder umgekehrt in widrigen Umständen unabhängig von einer solchen Verbindung agieren zu können, ist wichtig, um den Zugriff auf Daten zu ermöglichen, die für die Entscheidungsfindung benötigt werden.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Sicherheit und Verfügbarkeit des Rechenzentrums dank Hybrid Cloud-Sicherheitsvorgängen

Aufgrund der cloudnativen Konzeption von Azure Stack wird die betriebliche Komplexität herkömmlicher Virtualisierungsumgebungen vermieden, indem Administratoren den Zeitpunkt für Patches selbst auswählen können und der gesamte Vorgang über die Installation orchestriert wird. Das Azure Stack-Verwaltungsfabric ist nativ in das System integriert.

Über eine Microsoft-Remoteverwaltung oder ein von Microsoft verwaltetes Angebot auf dem Gebiet der Azure Stack-Geräte erhalten Benutzer den entsprechenden Zugriff über RBAC, um Aktionen über das Verwaltungsportal, den privilegierten Verwaltungsendpunkt oder die Befehlszeilenschnittstelle auszuführen. Dadurch können alle Patches und andere Verwaltungs- und Überwachungsaktivitäten von Microsoft ausgeführt werden. Azure Stack kann über das sichere Verwaltungsportal und Befehle der Befehlszeilenschnittstelle (Command-Line Interface, CLI) im Feld erweitert und aktualisiert werden, mit denen Sicherheitsupdates und aktualisierte IaaS- und PaaS-Features angewendet werden können, um ihn ggf. über lokale oder Remotenetzwerke auf den gleichen Stand wie die kommerzielle Version zu bringen. 

## <a name="benefits-of-using-the-mdc"></a>Vorteile des MDC

Das MDC unterstützt eine konsistente Umgebung mit Azure in Umgebungen mit eingeschränkten Kommunikationsmöglichkeiten:
 - Statische, modulare und schnell bereitstellbare Rechenzentren mit Azure Cloud Services, um große Analyse-Apps in Modular Operations Centers (TOC) zu unterstützen.
 - Die modularen Edge-Angebote von Azure gewährleisten die Konsistenz zwischen Cloud und Edge dank eines einheitlichen Ansatzes für die Unterstützung von IaaS-Primitiven wie virtuellen Computern, Speicher und virtuellen Netzwerken.
 - Azure Active Directory und rollenbasierte Zugriffssteuerung
 - Einheitliche Verwaltungsoberflächen
 - API-Symmetrie und Unterstützung für DevOps-Tools von Microsoft, Dritt- und Open-Source-Anbietern
 - Verwaltung und Überwachung über Azure Log Analytics und Azure Security Center
 - Nutzen Sie die Agilität des Cloud Computing für Ihre lokale Umgebung und den Edge über eine Hybrid Cloud-Infrastruktur.<br>Ihre Möglichkeiten:
     - Wiederverwenden von Code und konsistentes Ausführen von cloudnativen Apps in Azure und Ihren lokalen Umgebungen.
     - Ausführen herkömmlicher virtualisierter Workloads mit optionalen Verbindungen mit Azure-Diensten
     - Übertragen von Daten in die Cloud oder Speichern der Daten in Ihrem unabhängigen Rechenzentrum zur Einhaltung von Richtlinien
     - Ausführen von hardwarebeschleunigten Workloads für maschinelles Lernen bzw. containerbasierten oder virtualisierten Workloads mit Intelligent Edge

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Kapazitätsplanung für Azure Stack Hub](../operator/azure-stack-capacity-planning-overview.md)
