---
title: Hybridrelaymuster in Azure und Azure Stack Hub
description: Verwenden Sie das Hybridrelaymuster in Azure und Azure Stack Hub, um eine Verbindung mit von Firewalls geschützten Edgeressourcen herzustellen.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: abb4f2b779afb7450c04b98d1d6b02d824446b09
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891007"
---
# <a name="hybrid-relay-pattern"></a>Hybridrelaymuster

Hier erfahren Sie, wie Sie mithilfe des Hybridrelaymusters und von Azure Service Bus-Relays eine Verbindung mit Edgeressourcen oder -geräten herstellen, die von Firewalls geschützt werden.

## <a name="context-and-problem"></a>Kontext und Problem

Edge-Geräte befinden sich oft hinter einer Unternehmensfirewall oder einem NAT-Gerät. Obwohl sie sicher sind, können sie möglicherweise nicht mit der öffentlichen Cloud oder Edgegeräten in anderen Unternehmensnetzwerken kommunizieren. Es kann notwendig sein, bestimmte Ports und Funktionen Benutzern in der öffentlichen Cloud auf sichere Weise verfügbar zu machen.

## <a name="solution"></a>Lösung

Das Hybridrelaymuster verwendet Azure Service Bus-Relays, um einen WebSockets-Tunnel zwischen zwei Endpunkten einzurichten, die nicht direkt miteinander kommunizieren können. Geräte, die nicht lokal sind, aber eine Verbindung mit einem lokalen Endpunkt herstellen müssen, werden mit einem Endpunkt in der öffentlichen Cloud verbunden. Dieser Endpunkt leitet den Datenverkehr auf vordefinierten Routen über einen sicheren Kanal um. Ein Endpunkt innerhalb der lokalen Umgebung empfängt den Datenverkehr und leitet ihn an das gewünschte Ziel weiter.

![Hybridrelaymuster: Lösungsarchitektur](media/pattern-hybrid-relay/solution-architecture.png)

So funktioniert das Hybridrelaymuster:

1. Ein Gerät stellt an einem vordefinierten Port eine Verbindung mit einem virtuellen Computer (VM) in Azure her.
2. Datenverkehr wird an das Service Bus-Relay in Azure weitergeleitet.
3. Die VM in Azure Stack Hub, die bereits eine langlebige Verbindung mit dem Service Bus-Relay aufgebaut hat, empfängt den Datenverkehr und leitet ihn an das Ziel weiter.
4. Der lokale Dienst oder Endpunkt verarbeitet die Anforderung.

## <a name="components"></a>Komponenten

Diese Lösung verwendet die folgenden Komponenten:

| Ebene | Komponente | BESCHREIBUNG |
|----------|-----------|-------------|
| Azure | Azure VM | Eine Azure-VM bietet einen öffentlich zugänglichen Endpunkt für die lokale Ressource. |
| | Azure Service Bus Relay | Ein [Azure Service Bus-Relay](/azure/service-bus-relay/) stellt die Infrastruktur zum Verwalten des Tunnels und der Verbindung zwischen der Azure-VM und Azure Stack Hub-VM bereit.|
| Azure Stack Hub | Compute | Eine Azure Stack Hub-VM stellt die Serverseite des Hybridrelaytunnels bereit. |
| | Storage | Der in Azure Stack Hub bereitgestellte AKS-Engine-Cluster bietet eine skalierbare, resiliente Engine für die Ausführung des Gesichtserkennungs-API-Containers.|

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

Beachten Sie die folgenden Punkte bei der Entscheidung, wie diese Lösung implementiert werden soll:

### <a name="scalability"></a>Skalierbarkeit

Dieses Muster erlaubt auf dem Client und Server nur 1:1-Portzuordnungen. Wenn beispielsweise Port 80 für einen Dienst auf dem Azure-Endpunkt getunnelt ist, kann er nicht für einen anderen Dienst verwendet werden. Portzuordnungen müssen entsprechend geplant werden. Das Service Bus-Relay und VMs müssen zur Bewältigung des Datenverkehrs entsprechend skaliert werden.

### <a name="availability"></a>Verfügbarkeit

Diese Tunnel und Verbindungen sind nicht redundant. Um Hochverfügbarkeit sicherzustellen, sollten Sie Code zur Prüfung auf Fehler implementieren. Eine weitere Möglichkeit besteht darin, einen Pool von mit dem Service Bus-Relay verbundenen VMs hinter einem Lastenausgleich vorzuhalten.

### <a name="manageability"></a>Verwaltbarkeit

Diese Lösung kann eine Vielzahl von Geräten und Standorten umfassen und damit recht unhandlich werden. Mit den IoT-Diensten von Azure können neue Standorte und Geräte automatisch online geschaltet und auf dem neuesten Stand gehalten werden.

### <a name="security"></a>Sicherheit

Dieses hier gezeigte Muster ermöglicht im Edge-Bereich den ungehinderten Zugriff auf einen Port auf einem internen Gerät. Erwägen Sie, dem Dienst auf dem internen Gerät oder vor dem Hybridrelayendpunkt einen Authentifizierungsmechanismus hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel behandelten Themen:

- Dieses Muster verwendet Azure Service Bus-Relays. Weitere Informationen finden Sie in der [Dokumentation zu Azure Service Bus Relay](/azure/service-bus-relay/).
- Unter [Überlegungen zum Entwurf von Hybrid-Apps](overview-app-design-considerations.md) erfahren Sie mehr zu bewährten Methoden und erhalten Antworten auf weitere Fragen.
- Im Artikel zur [Azure Stack-Familie mit Produkten und Lösungen](/azure-stack) erfahren Sie mehr über das gesamte Portfolio von Produkten und Lösungen.

Wenn Sie bereit sind, das Lösungsbeispiel zu testen, fahren Sie mit dem [Bereitstellungsleitfaden für eine Hybridrelaylösung](https://aka.ms/hybridrelaydeployment) fort. In diesem Bereitstellungsleitfaden finden Sie detaillierte Anweisungen zum Bereitstellen und Testen der zugehörigen Komponenten.