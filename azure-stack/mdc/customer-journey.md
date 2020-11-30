---
title: Customer Journey vom Einkauf bis nach der Bereitstellung von Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, was Sie bei einer erfolgreichen lokalen Bereitstellung eines Azure Modular Data Center (MDC) erwarten können – von der Planung bis zu den Aktionen nach der Bereitstellung.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: 2d65f96cb7e64b22949150a246326d620ea66f56
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924366"
---
# <a name="modular-datacenter-integration-overview"></a>Übersicht über die Modular Data Center-Integration

Dieser Artikel beschreibt den End-to-End-Prozess für die Azure Modular Data Center-Integration (MDC) – von der Anschaffung bis zu den Schritten nach der Bereitstellung. Die Integration ist ein gemeinschaftliches Projekt zwischen dem Kunden und Microsoft. In den folgenden Abschnitten werden die einzelnen Phasen der Projektzeitachse und die genauen Schritte der Projektbeteiligten behandelt.

## <a name="introduction"></a>Einführung

In der folgenden Tabelle ist dargestellt, was während der verschiedenen Phasen der Bereitstellung zu erwarten ist.

| Teilnehmer |Bestellprozess |Vor der Bereitstellung |Integration, Validierung, Transport |Lokale Bereitstellung |Nach der Bereitstellung |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  | Signal für die Lieferung an einen Standort in den USA.    |Bereitstellen der erforderlichen Tools und der Dokumentation zum Sammeln der Anforderungen an das Rechenzentrum. |Validierung der Konfigurationsartefakte und Überprüfung der Validierungsergebnisse.<br>Überprüfung der Hardwarelieferung.    |Rack und Stack.<br>Netzwerkintegration.<br>Azure Stack Hub-Bereitstellung.<br>Übergabe an den Kunden.    |Registrierung und Azure Stack Hub Marketplace-Syndikation.|
|Kunde   |Signal für Kauf.   |Ausfüllen der Netzwerkdetails im Bereitstellungsarbeitsblatt.<br>Sammeln der Zertifikate.<br>Besorgen von Microsoft Azure Active Directory (Azure AD)-Konten.<br>Ausführen aller bereitgestellten Validierungstools.   |Sicherstellen, dass der Standort alle Anforderungen an Netzwerk, Stromversorgung und Kühlkomponenten erfüllt.   |Vorbereitung mit Konfigurationsartefakten für die Bereitstellung.<br>Sicherstellen der Verfügbarkeit des Netzwerktechnikers beim Kunden.   |     |


## <a name="order-process"></a>Bestellprozess

Ihre Organisation gibt in Zusammenarbeit mit Microsoft eine Bestellung für eine festgelegte Anzahl von Systemen auf. Nachdem Sie die Bestellung aufgegeben haben, liefert Microsoft das MDC an Ihren Standort in den USA. Microsoft sorgt dafür, dass alle Anforderungen an eine sichere Lieferkette erfüllt sind.

## <a name="hardware-delivery"></a>Hardwarelieferung

Microsoft arbeitet mit Ihnen zusammen, um sicherzustellen, dass alle erforderlichen Hardwareanforderungen innerhalb der festgelegten Zeit am Standort in den USA eintreffen.

Es ist *äußerst wichtig*, dass alle erforderlichen Daten gesperrt und verfügbar sind, *bevor der Microsoft-Techniker für die Bereitstellung der Lösung vor Ort eintrifft*.

- Im Bereitstellungsarbeitsblatt wurden alle Daten ausgefüllt.
- Alle Zertifikate müssen überprüft und einsatzbereit sein.
- Der Regionsname muss festgelegt werden.
- Alle Parameter für die Netzwerkintegration wurden abgeschlossen.

>[!Tip]
>Wenn eine dieser Informationen geändert wurde, stellen Sie in Zusammenarbeit mit Ihrer internen Organisation sicher, dass diese Informationen vor der Ankunft des Bereitstellungstechnikers vor Ort aktualisiert werden. Durch die Aktualisierung Ihrer Informationen werden Verzögerungen bei der Bereitstellung vermieden.

## <a name="predeployment"></a>Vor der Bereitstellung

Sie entscheiden, wie Sie Azure Stack Hub in Ihr Rechenzentrum integrieren. Microsoft hat ein [Bereitstellungsarbeitsblatt](../operator/azure-stack-deployment-worksheet.md) veröffentlicht, das Sie durch die Erfassung aller für die erfolgreiche Integration in Ihr Rechenzentrum erforderlichen Informationen führt. Außerdem sind zum Zeitpunkt der Bereitstellung bestimmte Zertifikate erforderlich. Um Sie bei der Beschaffung dieser Zertifikate zu unterstützen, stellt Ihnen Microsoft den [Azure Stack Hub Readiness Checker](../operator/azure-stack-validation-report.md) zur Verfügung. Dieses Tool hilft Ihnen beim Erstellen von Zertifikatsignieranforderungen (Certificate Signing Requests, CSRs), die Sie Ihrer internen Zertifizierungsstelle bereitstellen können.

>[!Important]
>Alle Voraussetzungen werden überprüft, um Verzögerungen bei der Bereitstellung zu vermeiden. Die Überprüfung der Voraussetzungen kann einige Zeit in Anspruch nehmen und die Koordination und Datensammlung aus verschiedenen Abteilungen innerhalb Ihres Unternehmens erfordern.

Sie wählen Folgendes aus:

- **Azure Stack Hub-Verbindungsmodell und Identitätsanbieter.** Sie können Azure Stack Hub entweder [mit einer Verbindung mit dem Internet (und mit Azure)](../operator/azure-stack-connected-deployment.md) oder [unverbunden](../operator/azure-stack-disconnected-deployment.md) bereitstellen. Um optimal von Azure Stack Hub, einschließlich Hybridszenarien, zu profitieren, empfiehlt es sich, die Bereitstellung mit einer Verbindung mit Azure durchzuführen. Die Wahl von Active Directory-Verbunddienste (AD FS) oder Azure AD ist eine Entscheidung, die Sie bei der Bereitstellung einmalig treffen müssen. *Sie können Ihren Identitätsanbieter später nicht mehr ändern, ohne das gesamte System neu bereitzustellen.*
- **Netzwerkintegration.** Die [Netzwerkintegration](../operator/azure-stack-network.md) ist äußerst wichtig für Bereitstellung, Betrieb und Verwaltung von Azure Stack Hub-Systemen. Es müssen verschiedene Aspekte berücksichtigt werden, um sicherzustellen, dass die Azure Stack Hub-Lösung resilient ist und über eine hoch verfügbare physische Infrastruktur verfügt, um die zugehörigen Vorgänge zu unterstützen.
- **Firewallintegration.** Wir empfehlen Ihnen, Azure Stack Hub [mit einer Firewall](../operator/azure-stack-firewall.md) zu schützen. Firewalls können bei der Verhinderung von DDoS-Angriffen, bei Angriffserkennung und Inhaltsuntersuchung helfen. Beachten Sie, dass Firewalls zu einem Durchsatzengpass für Azure-Speicherdienste werden können.
- **Zertifikatanforderungen.** Es ist sehr wichtig, dass [alle erforderlichen Zertifikate](../operator/azure-stack-pki-certs.md) zur Verfügung stehen, bevor ein Vor-Ort-Techniker in Ihrem Datencenter zur Bereitstellung eintrifft.

Nachdem alle erforderlichen Informationen im Bereitstellungsarbeitsblatt erfasst wurden, überprüft Microsoft, ob alle Validierungstools ausgeführt wurden, und unterstützt Sie bei allen weiteren Fragen.

## <a name="onsite-deployment"></a>Lokale Bereitstellung

Die Bereitstellung von Azure Stack Hub wird durch einen Bereitstellungstechniker von Microsoft vor Ort gestartet. Dabei wird außerdem vorausgesetzt, dass während der Bereitstellung vor Ort auch ein Netzwerktechniker Ihrer Organisation verfügbar ist.

Anhand der folgenden Überprüfungen wird beschrieben, was Sie vom Vor-Ort-Techniker während des Bereitstellungsverfahrens erwarten sollten:

- Auspacken und Inventarisieren der Hardware.
- Anschließen der Stromversorgung und Einschalten der Lösung.
- Überprüfen der Integrität der physischen Hardware.
- Überprüfen der gesamten Verkabelung und Grenzkonnektivität, um sicherzustellen, dass die Lösung ordnungsgemäß zusammengesetzt wird und Ihre Anforderungen erfüllt.
- Konfigurieren des Lösungs-HLH (Hardware Lifecycle Host, Hardwarelebenszyklus-Host).
- Integrieren des Rechenzentrum-Netzwerks.
- Überprüfen, ob alle Einstellungen der physischen Hardware korrekt sind
- Sicherstellen, dass die Firmware für alle Komponenten die neueste durch die Lösung genehmigte Version aufweist.
- Starten der Bereitstellung.

## <a name="post-deployment"></a>Nach der Bereitstellung

Der Bereitstellungstechniker von Microsoft muss mehrere Schritte ausführen, bevor die Lösung an den Kunden übergeben wird. In dieser Phase ist die Validierung wichtig, um sicherzustellen, dass das System korrekt bereitgestellt wird und einwandfrei funktioniert.

Aktionen, die vom Microsoft-Bereitstellungstechniker durchgeführt werden müssen:

- Aktivieren von Ressourcenanbietern, die Mehrwert schaffen.
- Ausführen von [test-azurestack](../operator/azure-stack-diagnostic-test.md)
- [Registrieren](../operator/azure-stack-registration-role.md) bei Azure.
- Sicherstellen der [Azure Stack Hub Marketplace-Syndikation](../operator/azure-stack-marketplace.md).
- Sichern der Konfigurationsdateien für Switch und HLH.
- Vorbereiten einer Kundenzusammenfassung zur Bereitstellung
- [Überprüfen auf Updates](../operator/azure-stack-updates.md), um sicherzustellen, dass die Lösungssoftware auf die neueste Version aktualisiert wurde

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Modular Data Center (MDC)-Bereitstellung](deployment-overview.md)

